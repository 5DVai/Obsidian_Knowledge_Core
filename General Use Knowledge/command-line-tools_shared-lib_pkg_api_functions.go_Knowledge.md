# API Interaction Utilities for Command Management
**Source:** command-line-tools\shared-lib\pkg\api\functions.go  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `functions.go` file provides a set of utility functions designed to interact with a remote API for managing command schedules and usage. These functions encapsulate HTTP requests to perform operations such as booting an agent, pinging an agent, retrieving command schedules, and fetching command usage by ID or keywords. The file demonstrates a pattern for making authenticated HTTP requests using bearer tokens, which is a common requirement in enterprise-grade applications that interact with RESTful APIs.

The functions are valuable for their reusable nature in any system that requires similar API interactions. They abstract the complexity of HTTP request handling, error management, and JSON serialization/deserialization, providing a clean interface for developers to integrate with the API. This makes them a potential candidate for inclusion in a software engineering knowledge base as a reference for building robust API client utilities.

## Key Concepts & Principles
- **HTTP Client Abstraction:** Encapsulation of HTTP request logic for reuse.
- **Bearer Token Authentication:** Secure API access using bearer tokens.
- **JSON Serialization/Deserialization:** Handling JSON data for API communication.
- **Error Handling:** Robust error management for network operations.
- **RESTful API Interaction:** Standardized approach to interacting with RESTful services.

## Detailed Technical Analysis

### HTTP Client and Request Handling
Each function in the file creates an HTTP client and constructs a request to a specific endpoint. The use of `http.NewRequest` allows for flexible request configuration, including setting headers for authentication.

### Bearer Token Authentication
The functions demonstrate the use of bearer tokens for authentication by adding an `Authorization` header to each request. This is a common pattern for securing API endpoints and is crucial for maintaining secure communication between clients and servers.

### JSON Handling
The functions utilize `json.Marshal` and `json.Unmarshal` for converting Go structs to JSON and vice versa. This is essential for interacting with APIs that use JSON as the data interchange format.

### Error Management
Error handling is consistently applied across all functions, ensuring that network errors, JSON parsing errors, and server response errors are captured and returned to the caller. This approach enhances the robustness of the API client.

## Enterprise Q&A Bank

1. **Q:** How can these functions be adapted for a different API?
   **A:** Modify the `apiBaseUri` and endpoint paths, and ensure the data models match the new API's requirements.

2. **Q:** What is the purpose of the `Authorization` header?
   **A:** It is used to pass the bearer token for authenticating requests to the API.

3. **Q:** How does the file handle JSON data?
   **A:** It uses `json.Marshal` to serialize data and `json.Unmarshal` to deserialize JSON responses.

4. **Q:** What happens if the API returns an error status code?
   **A:** The functions check the response status code and return an error if it is not `http.StatusOK`.

5. **Q:** Can these functions handle different HTTP methods?
   **A:** Yes, the `http.NewRequest` function allows specifying any HTTP method, such as GET, POST, PUT, etc.

## Actionable Takeaways
- Ensure the `apiBaseUri` is correctly configured for the target API.
- Always include error handling for network operations and JSON parsing.
- Use bearer tokens for secure API authentication.
- Validate response status codes to handle API errors gracefully.
- Reuse the HTTP client setup pattern for consistent API interactions.

---
**Raw Content:**
```go
package api

import (
	"bytes"
	"encoding/json"
	"errors"
	"io"
	"net/http"
	"strconv"

	"github.com/reconmap/shared-lib/pkg/models"
)

func AgentBoot(apiBaseUri string, accessToken string, systemInfo *SystemInfo) (*models.CommandSchedules, error) {
	var apiUrl string = apiBaseUri + "/agents/boot"
	marshalled, err := json.Marshal(systemInfo)

	client2 := &http.Client{}
	req, err := http.NewRequest("POST", apiUrl, bytes.NewBuffer(marshalled))
	if err != nil {
		return nil, err
	}
	req.Header.Add("Authorization", "Bearer "+accessToken)

	response, err := client2.Do(req)
	if err != nil {
		return nil, err
	}

	defer response.Body.Close()

	_, err = io.ReadAll(response.Body)
	if err != nil {
		return nil, err
	}

	return nil, nil
}

func AgentPing(apiBaseUri string, accessToken string) (*models.CommandSchedules, error) {
	var apiUrl string = apiBaseUri + "/agents/ping"

	client2 := &http.Client{}
	req, err := http.NewRequest("GET", apiUrl, nil)
	if err != nil {
		return nil, err
	}
	req.Header.Add("Authorization", "Bearer "+accessToken)

	response, err := client2.Do(req)
	if err != nil {
		return nil, err
	}

	defer response.Body.Close()

	_, err = io.ReadAll(response.Body)
	if err != nil {
		return nil, err
	}

	return nil, nil
}

func GetCommandsSchedules(apiBaseUri string, accessToken string) (*models.CommandSchedules, error) {
	var apiUrl string = apiBaseUri + "/commands/schedules"

	client2 := &http.Client{}
	req, err := http.NewRequest("GET", apiUrl, nil)
	if err != nil {
		return nil, err
	}
	req.Header.Add("Authorization", "Bearer "+accessToken)

	response, err := client2.Do(req)
	if err != nil {
		return nil, err
	}

	defer response.Body.Close()

	body, err := io.ReadAll(response.Body)
	if err != nil {
		return nil, err
	}

	var schedules *models.CommandSchedules = &models.CommandSchedules{}

	if err = json.Unmarshal(body, schedules); err != nil {
		return nil, err
	}

	return schedules, nil
}

func GetCommandUsageById(apiBaseUri string, id int) (*models.CommandUsage, error) {
	var apiUrl string = apiBaseUri + "/commands/usage/" + strconv.Itoa(id)

	client := &http.Client{}
	req, err := NewRmapRequest("GET", apiUrl, nil)
	if err != nil {
		return nil, err
	}
	req.Header.Add("Content-Type", "application/x-www-form-urlencoded")
	if err = AddBearerToken(req); err != nil {
		return nil, err
	}

	response, err := client.Do(req)
	if err != nil {
		return nil, err
	}

	defer response.Body.Close()
	body, err := io.ReadAll(response.Body)

	if response.StatusCode != http.StatusOK {
		return nil, errors.New("error from server: " + string(response.Status))
	}

	if err != nil {
		return nil, errors.New("unable to read response from server")
	}

	var command *models.CommandUsage = &models.CommandUsage{}

	if err = json.Unmarshal([]byte(body), command); err != nil {
		return command, err
	}

	return command, nil
}

func GetCommandsByKeywords(apiBaseUri string, keywords string) (*models.Commands, error) {
	var apiUrl string = apiBaseUri + "/commands?keywords=" + keywords

	client := &http.Client{}
	req, err := NewRmapRequest("GET", apiUrl, nil)
	if err != nil {
		return nil, err
	}
	req.Header.Add("Content-Type", "application/x-www-form-urlencoded")
	if err = AddBearerToken(req); err != nil {
		return nil, err
	}

	response, err := client.Do(req)
	if err != nil {
		return nil, err
	}

	defer response.Body.Close()
	body, err := io.ReadAll(response.Body)

	if response.StatusCode != http.StatusOK {
		return nil, errors.New("error from server: " + string(response.Status))
	}

	if err != nil {
		return nil, errors.New("unable to read response from server")
	}

	var commands *models.Commands = &models.Commands{}

	if err = json.Unmarshal(body, commands); err != nil {
		return commands, err
	}

	return commands, nil
}
```