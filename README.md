# Python API for Mobile Application

**2016–2017 · Python 2.7, CGI, JSON**

Production HTTP/JSON API developed as part of a larger web and mobile 
application ecosystem.

> **Source code:** Not publicly available. This repository documents 
> the architecture and selected implementation details of the 
> original system.

The API is still in operation and continues to serve the mobile 
application.

## What this API does

The API provides application functionality for:

* user registration and authentication
* token-based sessions
* user account management
* application data retrieval and updates
* user-specific results and rankings
* access-level and permission checks
* application version checking
* activity tracking
* JSON communication with the mobile client

## Architecture

The API uses a lightweight request-routing layer with application 
functionality separated into dedicated modules.

```text
Mobile Application
       │
       │ HTTP / JSON
       ▼
  Entry point (routing)
       │
       ├── request parsing
       ├── authentication
       ├── authorization
       ├── request routing
       │
       ▼
  application modules
       │
       ├── user operations
       ├── domain logic
       ├── data processing
       │
       ▼
    database
```

Application logic is split across dedicated modules (user operations, 
domain-specific functionality, shared utilities), keeping the routing 
layer thin and focused purely on dispatch.

## Authentication and authorization

Protected operations use a token supplied by the mobile application. 
A simplified illustration of the pattern used for protected requests:

```python
if user_id is None:
    result = api_error('User not recognized. Please log in again.')
elif not check_permissions(user_id, required_level):
    result = api_ok({'message': access_restriction_message})
else:
    result = execute_requested_operation(user_id, ...)
```

The API:

1. receives the request,
2. extracts the authentication token,
3. validates the token and client information,
4. identifies the user,
5. checks permissions where required,
6. executes the requested operation,
7. returns a JSON response.

Different operations require different access levels.

## Request handling

The API exposes separate operations for public and authenticated 
functionality — general information and static content on one side, 
user-specific actions (account data, submitting/editing entries, 
results) on the other. The routing layer keeps this dispatch logic 
isolated from the underlying business logic.

## Error handling and logging

Requests are wrapped in exception handling; server-side failures are 
logged with full detail internally, while clients only ever receive 
a generic error response — no internal error details are exposed:

```python
try:
    result = process_request(...)
except Exception as e:
    log_error(e)
    result = api_error('Server error.')
```

The API also records application activity and request-related 
information used for monitoring and operational purposes.

## Production context

This is not a tutorial project or a proof of concept. The API was 
developed for a production application and has remained in operation 
as part of the application's backend.

The original implementation dates from the Python 2.7 era and 
reflects the technical constraints of that environment. It is 
presented here to demonstrate the architecture, integration, and 
production engineering involved, rather than as a recommendation for 
building a new API with the same technology stack.

**Status:** Historical/ongoing production project · source code not 
publicly available · part of the [Custom application ecosystem](https://github.com/elast-net/2014-2018-sports-prediction-ecosystem)
