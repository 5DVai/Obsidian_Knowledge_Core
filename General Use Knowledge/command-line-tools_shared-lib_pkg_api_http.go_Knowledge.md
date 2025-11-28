# HTTP Utility Functions for Reconmap API
**Source:** command-line-tools\shared-lib\pkg\api\http.go  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `http.go` file provides a set of utility functions designed to facilitate HTTP requests within the Reconmap application. These functions are focused on creating HTTP requests with specific headers, managing session tokens, and ensuring secure communication with the Reconmap API. The file encapsulates logic for handling user-agent strings, bearer token authentication, and session token storage, making it a valuable component for any application requiring authenticated HTTP interactions.

The utility functions in this file are reusable and can be adapted for other applications that require similar HTTP request handling and token management. By abstracting these common tasks, the file promotes code reuse and simplifies the development of networked applications.

## Key Concepts & Principles
- **HTTP Request Abstraction:** Simplifies the creation of HTTP requests with custom headers.
- **Bearer Token Authentication:** Implements a standard method for securing API requests.
- **Session Token Management:** Provides mechanisms for reading, writing, and locating session tokens.
- **Cross-Platform User-Agent Handling:** Dynamically sets the user-agent string based on the operating system.

## Detailed Technical Analysis

### HTTP Request Creation
The functions `NewRmapRequestWithUserAgent` and `NewRmapRequest` streamline the process of creating HTTP requests by setting necessary headers, such as the user-agent. This abstraction reduces boilerplate code and ensures consistency across requests.

### Bearer Token Authentication
The `AddBearerToken` function adds an authorization header to HTTP requests using a bearer token. This is a common pattern for securing API endpoints and ensures that requests are authenticated.

### Session Token Management
- **Reading Tokens:** `ReadSessionToken` retrieves the session token from an environment variable or a configuration file, providing flexibility in token storage.
- **Saving Tokens:** `SaveSessionToken` writes the session token to a secure file, ensuring that sensitive information is stored safely.
- **Token Path Retrieval:** `GetSessionTokenPath` determines the file path for the session token, facilitating token management.

## Enterprise Q&A Bank

1. **Q:** How does the `NewRmapRequest` function enhance HTTP request creation?
   - **A:** It abstracts the creation of HTTP requests by automatically setting a user-agent string, reducing repetitive code.

2. **Q:** What is the purpose of the `AddBearerToken` function?
   - **A:** It adds a bearer token to the authorization header of an HTTP request, enabling secure API communication.

3. **Q:** How does `ReadSessionToken` determine where to find the session token?
   - **A:** It first checks an environment variable, then falls back to reading from a configuration file.

4. **Q:** Why is the user-agent string dynamically set in `NewRmapRequest`?
   - **A:** To provide context about the client's operating system, which can be useful for server-side analytics and compatibility checks.

5. **Q:** What security considerations are addressed by `SaveSessionToken`?
   - **A:** It writes the session token to a file with restricted permissions, minimizing the risk of unauthorized access.

## Actionable Takeaways
- Use `NewRmapRequest` and `NewRmapRequestWithUserAgent` to standardize HTTP request creation across applications.
- Implement `AddBearerToken` to secure API requests with bearer token authentication.
- Manage session tokens effectively using `ReadSessionToken`, `SaveSessionToken`, and `GetSessionTokenPath`.
- Ensure that sensitive data, such as session tokens, is stored securely with appropriate file permissions.
- Leverage dynamic user-agent strings to provide additional context in HTTP requests.

---