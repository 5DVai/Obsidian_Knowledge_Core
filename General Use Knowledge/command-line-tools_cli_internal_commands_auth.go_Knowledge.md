# Authentication Command Implementation in CLI Tools
**Source:** command-line-tools\cli\internal\commands\auth.go  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `auth.go` file is a critical component of a command-line interface (CLI) tool designed to handle authentication processes using OAuth2 and OpenID Connect (OIDC) protocols. It provides functionality for logging in and out of a system, leveraging a Keycloak identity provider for authentication. This file encapsulates the logic for obtaining and verifying tokens, managing user sessions, and interacting with an API for authentication purposes. The implementation demonstrates a practical application of OAuth2 device flow, making it valuable for developers looking to implement similar authentication mechanisms in their applications.

## Key Concepts & Principles
- **OAuth2 Device Flow:** A user-friendly authentication flow for devices with limited input capabilities.
- **OpenID Connect (OIDC):** An identity layer on top of OAuth2 for verifying user identity.
- **Token Management:** Handling access and ID tokens for session management.
- **HTTP Client Usage:** Making authenticated requests to an API.
- **Error Handling:** Robust error checking and logging for authentication processes.

## Detailed Technical Analysis

### OAuth2 Device Flow Implementation
The `Login` function implements the OAuth2 device flow, which is particularly useful for devices that cannot easily display a web interface. The flow involves:
1. Requesting a device code and user code from the OAuth2 provider.
2. Prompting the user to visit a verification URL and enter the user code.
3. Exchanging the device code for an access token once the user has authenticated.

### Token Verification and Claims Extraction
After obtaining the access token, the code verifies the ID token using the OIDC provider's verifier. This step ensures the token's authenticity and extracts claims, such as the user's email, which are used for further processing.

### API Interaction and Session Management
The `Login` and `Logout` functions interact with an API to manage user sessions. The `Login` function sends a POST request to the API to log in the user, while the `Logout` function invalidates the session by removing the session token file and notifying the server.

### Error Handling and Logging
The implementation includes comprehensive error handling, logging errors using a shared logging library, and providing user feedback through terminal messages. This approach ensures that users are informed of any issues during the authentication process.

## Enterprise Q&A Bank

1. **Q:** How does the OAuth2 device flow enhance user experience on limited-input devices?
   **A:** It allows users to authenticate by entering a code on a separate device with a full browser, simplifying the process on devices with limited input capabilities.

2. **Q:** What is the role of the ID token in OpenID Connect?
   **A:** The ID token contains claims about the authentication event and the user, allowing the client to verify the user's identity.

3. **Q:** How does the implementation ensure secure token storage?
   **A:** Tokens are stored in a session file, which is removed upon logout to prevent unauthorized access.

4. **Q:** What are the potential error scenarios handled in the `Login` function?
   **A:** Errors include configuration read failures, provider initialization issues, token acquisition failures, and HTTP request errors.

5. **Q:** Why is it important to verify the ID token after obtaining it?
   **A:** Verification ensures the token's integrity and authenticity, preventing token forgery and unauthorized access.

## Actionable Takeaways
- Implement OAuth2 device flow for user-friendly authentication on devices with limited input.
- Use OpenID Connect to add an identity layer to OAuth2, enhancing security and user information retrieval.
- Ensure robust error handling and logging to improve user experience and facilitate troubleshooting.
- Securely manage session tokens and ensure they are invalidated upon logout.
- Regularly update and verify third-party libraries to maintain security and compatibility.

---
**Raw Content:**  
```go
package commands

import (
	"bytes"
	"context"
	"encoding/json"
	"errors"
	"fmt"
	"io"
	"log"
	"net/http"
	"os"

	"github.com/reconmap/shared-lib/pkg/logging"

	"github.com/coreos/go-oidc"
	"github.com/reconmap/cli/internal/configuration"
	"github.com/reconmap/cli/internal/terminal"
	"github.com/reconmap/shared-lib/pkg/api"
	sharedconfig "github.com/reconmap/shared-lib/pkg/configuration"
	"golang.org/x/oauth2"
)

type IDTokenClaim struct {
	Email string `json:"email"`
}

func Login() error {
	logger := logging.GetLoggerInstance()

	config, err := sharedconfig.ReadConfig[configuration.Config](configuration.ConfigFileName)
	if err != nil {
		return err
	}

	provider, err := oidc.NewProvider(context.Background(), config.KeycloakConfig.BaseUri)
	if err != nil {
		return err
	}

	clientId := config.KeycloakConfig.ClientID
	oauthConfig := oauth2.Config{
		ClientID:    clientId,
		RedirectURL: "urn:ietf:wg:oauth:2.0:oob",
		Endpoint: oauth2.Endpoint{
			DeviceAuthURL: provider.Endpoint().AuthURL + "/device",
			AuthURL:       provider.Endpoint().AuthURL,
			TokenURL:      provider.Endpoint().TokenURL,
		},
		Scopes: []string{oidc.ScopeOpenID, "email"},
	}

	ctx := context.Background()

	deviceCode, err := oauthConfig.DeviceAuth(ctx)
	if err != nil {
		logger.Error(err)
		return err
	}
	fmt.Printf("Go to %v and enter code %v\n", deviceCode.VerificationURI, deviceCode.UserCode)
	fmt.Println()

	token, err := oauthConfig.DeviceAccessToken(ctx, deviceCode)
	if err != nil {
		panic(err)
	}

	err = api.SaveSessionToken(token.AccessToken)

	rawIDToken, ok := token.Extra("id_token").(string)
	if !ok {
		panic("id_token is missing")
	}

	verifier := provider.Verifier(&oidc.Config{ClientID: oauthConfig.ClientID})
	idToken, err := verifier.Verify(ctx, rawIDToken)
	if err != nil {
		panic(err)
	}

	idTokenClaim := IDTokenClaim{}
	if err := idToken.Claims(&idTokenClaim); err != nil {
		panic(err)
	}

	var apiUrl string = config.ReconmapApiConfig.BaseUri + "/users/login"

	formData := map[string]string{}
	jsonData, err := json.Marshal(formData)

	httpClient := &http.Client{}
	req, err := api.NewRmapRequest("POST", apiUrl, bytes.NewBuffer(jsonData))
	req.Header.Add("Content-Type", "application/json")
	api.AddBearerToken(req)
	response, err := httpClient.Do(req)
	if err != nil {
		return err
	}

	if response.StatusCode != http.StatusOK {

		if response.StatusCode == http.StatusForbidden || response.StatusCode == http.StatusUnauthorized {
			return errors.New("Invalid credentials")
		}

		if response.StatusCode == http.StatusMethodNotAllowed {
			return fmt.Errorf("Method POST not allowed for %s. Please make sure you are pointing to the API url and not the frontend one.", apiUrl)
		}

		return fmt.Errorf("Server returned code %d", response.StatusCode)
	}

	defer response.Body.Close()
	body, err := io.ReadAll(response.Body)

	if err != nil {
		return errors.New("Unable to read response from server")
	}

	var loginResponse api.LoginResponse

	if err = json.Unmarshal([]byte(body), &loginResponse); err != nil {
		return err
	}

	terminal.PrintGreenTick()
	fmt.Printf(" Successfully logged in as '%s'\n", idTokenClaim.Email)

	return err
}

func Logout() error {
	if _, err := api.ReadSessionToken(); err != nil {
		return errors.New("There is no active user session")
	}

	config, err := sharedconfig.ReadConfig[configuration.Config](configuration.ConfigFileName)
	if err != nil {
		return err
	}
	var apiUrl string = config.ReconmapApiConfig.BaseUri + "/users/logout"

	client := &http.Client{}
	req, err := api.NewRmapRequest("POST", apiUrl, nil)
	if err != nil {
		return err
	}

	if err = api.AddBearerToken(req); err != nil {
		return err
	}

	response, err := client.Do(req)
	if err != nil {
		return err
	}

	if response.StatusCode != http.StatusOK {
		return errors.New("Response error received from the server")
	}

	defer response.Body.Close()

	configPath, err := api.GetSessionTokenPath()
	if _, err := os.Stat(configPath); err == nil {
		err = os.Remove(configPath)
		if err != nil {
			log.Println("Unable to remove file")
		}
	} else if errors.Is(err, os.ErrNotExist) {
		log.Println("warning: Session file does not exist")
	}

	terminal.PrintGreenTick()
	fmt.Printf(" Successfully logged out from the server\n")

	return err
}
```