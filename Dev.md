# `Dev` // SafeCode Documentation

## 📚 Table of Contents

1. [Introduction](#introduction)
2. [Technologies used](#technologies-used)
3. [User path](#user-path)
4. [Architecture](#architecture)
5. [Global logic](#1-global-logic)
6. [Server](#4-server)
7. [Parser](#5-parser)
8. [Analyser](#6-analyser)
9. [Docker Setup](#7-docker-setup)
10. [GitHub Integration](#github-integration)

## Introduction
This documentation is for current or future developers. The purpose of it is to explain how the SafeCode project has been architected, how it has been created, how it works and how to add functionalities.

## Technologies used
- Go (Classic + Gin)
- Python (for AST conversion)
- MongoDB (Atlas)
- Docker

## User path
<img src="images/SafeCodeUseCaseDiagram.png" width=800\>

## Architecture
This architecture is currently in monolithic.
## 1. Graphic
```mermaid
flowchart TD
    subgraph Docker Network
        Frontend(Frontend - React App)
        Backend(Backend - API Server)
        MongoDB[(MongoDB Database)]
        LicenseChecker([License Checker])
    end

    User --> Frontend
    Frontend --> Backend
    Backend --> MongoDB
    Backend --> LicenseChecker
```

## 2. Explanation
### 1. Global logic
First of all, the frontend sends an analysis request after making an upload request (or via GitHub).<br>
The backend will receive it, take the `.zip` of the project, decompress it and send the file pass of the folder containing the project to the parser.<br>
The parser will return a list that we will send to the analyser, which will go through the files one by one, storing them in a list (`*[]gin.H`) that it will save in our db (for your historical) and then send back to the frontend.<br>

**So, it's :**<br>
`Frontend request -> Zip file extraction -> Parser -> Analyser -> Saving in our database -> Sending back to frontend`

### 2. Ast
**Folder name:** `ast/`<br>
This folder is used to put all the code converters in AST. To convert a language we use the original language itself.<br>
The code will be called by the parser, the file name will be passed as an argument and the code will return the AST in the form of a `.json` file.

### 3. Json
**Folder name:** `doc/`<br>
This file contains all the rules that have been created and can be analysed.<br>
The model we use is :<br>
```
CWE-[number]/
   rule[1].json
   (...)
```
Example of `.json` :<br>
```json
{
  "schema": {
    "dangerous_functions": [
      [
        "int",
        ["value.func.id"]
      ]
    ],
    "safe_functions": [
      [
        "str.isdigit",
        ["value.func.attr"]
      ]
    ]
  },
  "ToFixIt": {
    "text": "Validate query parameters before using them. Use methods like .isdigit() or regex to ensure proper types."
  },
  "Kind": {
    "text": "Improper Input Validation (CWE-20)"
  },
  "Doc": {
    "what_is_an_issues": "User input is directly cast to an integer without validation. If the input is not a valid number, it can raise a runtime error, causing the app to crash or behave unpredictably.",
    "how_can_i_fix_it": "Always validate input using safe type checks before converting types. In this case, check if the input is a digit before using int().",
    "more_info": "https://cwe.mitre.org/data/definitions/20.html"
  }
}
```
The .json files contain ‘dangerous’ and ‘safe’ functions with their name and path in the AST.
If a dangerous function is detected and then the safe function (or just the safe), nothing will happen. But if there is only the dangerous function, then the vulnerability is recorded.

### 4. Server
**Folder name:** `src_server/`<br>
**Port:** `8080`<br>
**Endpoints:**<br>

The SafeCode backend runs on port 8080 using the Gin framework.<br>
It exposes several endpoints grouped into two categories: authentication and protected actions (requiring a valid token).

#### 🔑 Authentication Endpoints

| Method | Path                    | Description                                 |
|--------|--------------------------|---------------------------------------------|
| GET    | `/auth/github`            | Redirects the user to GitHub for OAuth login. |
| GET    | `/auth/github/callback`   | Handles the OAuth callback from GitHub and retrieves the access token. |
| POST   | `/auth/register`          | Registers a new user with email and password. |
| POST   | `/auth/login`             | Logs a user in and returns a JWT token. |
| POST   | `/logout`                 | Logs the user out by invalidating their token (handled client-side). |

#### 🔒 Protected Endpoints (require authentication)

| Method | Path                      | Description                                 |
|--------|----------------------------|---------------------------------------------|
| GET    | `/users`                   | Retrieves the list of all registered users. |
| GET    | `/me`                      | Retrieves the profile of the authenticated user. |
| POST   | `/upload`                  | Uploads a ZIP file for analysis. |
| POST   | `/analyse`                 | Starts the analysis process on an uploaded file. |
| GET    | `/analyse/history`         | Retrieves the analysis history of the user. |
| POST   | `/github/download`         | Downloads and extracts a GitHub repository based on a provided link. |

#### 🛡️ Security and Middleware
- All protected routes use a JWT authentication middleware (`AuthMiddleware`).
- CORS policy allows all origins and methods to facilitate communication with the frontend.

### 5. Parser
**Folder name:** `src_parser/`<br>

The parser is used to read all the files in a folder (`.zip` which has been extracted) and to note which language it is encoded in.<br>
Example its return :<br>
```
code/200uv1.py py
code/200v1.py py
code/201uv1.py py
code/201v1.py py
code/20uv1.py py
code/20v1.py py
code/22uv1.py py
code/22v1.py py
```

### 6. Analyser
**Folder name:** `src_analyser/`<br>
This folder contains all the logic for static code analysis.<br>
It is invoked by the backend when a user uploads a file or provides a GitHub repository.<br>

#### 🧠 General Workflow

1. The server sends a list of extracted file paths to the analyser.
2. The analyser:
   - Identifies the language of each file based on its extension
   - Selects the appropriate analysis function
   - Runs the analysis and appends the results to a final JSON array (then saved for historical purposes and sent to the frontend)

```go
// Core dispatch logic
analyzers := map[string]func(*[]gin.H, string){
  "py": analyseAskedPyFile,
  "cs": analyseAskedCsFile,
}
```
Each language-specific function uses its own analysis logic.<br>

#### 🧩 Language Detection & Dispatch
The function `AnalyseList([]string)` loops through the list of files, detects the language via file extension, and calls the corresponding analysis function.<br>
If the extension is unknown, the file is skipped and an error message is logged.<br>

#### 🐍 Example: Python Analysis
The function `analyseAskedPyFile()` builds an AST of the current file path and passes it to the main Python analyser function.<br>
This analyser launches submodules that follow the OWASP Top 10 2021, such as A01, A02, etc (Rest of the list in [Non-technical](Non-technical.md)).<br>

```go
rules.RunA01Analysis(resultJson, astRaw, filename)
rules.RunA02Analysis(...)
```
Each of these submodules runs a set of checks based on CWE IDs (Common Weakness Enumeration).<br>

#### 🔍 Rule Execution by CWE<br>
The function RunA01Analysis internally calls RunBeforeAnalysis, which:<br>

Receives the CWE ID ("22" for Path Traversal)<br>
Receives the detection method index ("1" for static AST pattern matching)<br>
Example:<br>
```go
RunBeforeAnalysis(resultJson, astRaw, filename, "22", "1")
RunBeforeAnalysis(resultJson, astRaw, filename, "200" "1")
RunBeforeAnalysis(resultJson, astRaw, filename, ..., ...)
```

This allows multiple detection strategies per CWE in the future ("1" = first way to detect a CWE, "2" = another way, etc.).<br>

#### 🧠 How to add a new language<br>
To add support for a new language (in this exemple `js`):<br>

Create a new function:<br>
```go
func analyseAskedJsFile(result *[]gin.H, filePath string)
```

Add the function to the analyzers dispatch map:<br>

```go
"js": analyseAskedJsFile,
```
Inside the analysis function:<br>
- Parse the file using the language’s AST or regex
- Call the relevant OWASP or custom rules
- Append results in the same format as others ([]gin.H)

#### 📦 Result Format<br>
The results of the analysis are formatted as described in the JSON schema section (see above in the doc).<br>
Each result includes:<br>
- CWE ID
- Rule ID
- File path
- Line number
- Its kind
- Explanation and solution suggestion

### 7. Docker Setup
**Docker Compose file:** `docker-compose.yml`  
**Dockerfile:** Root directory  

The SafeCode backend is containerized using Docker and orchestrated through a simple Docker Compose configuration. This allows for fast, reproducible deployment and development on any machine with Docker installed.

#### 🧱 Overview

The setup uses:
- A single container: `core-api` running the backend logic
- A bind mount to sync the code inside the container
- An .env file for environment variables
- The **Go 1.24.1 image** as the base

#### 📄 Docker Compose Summary

```yaml
services:
  api:
    container_name: core-api
    image: api:1.0
    build: .
    ports:
      - "8080:8080"
    volumes:
      - ./src_server:/app/src_server
    env_file:
      - .env
    restart: always
```
Port 8080 is exposed and mapped to the host for API access.

The source folder ./src_server is mounted inside the container at /app/src_server.

Environment variables are loaded from a local .env file.

#### ⚙️ Dockerfile Breakdown
```Dockerfile
FROM golang:1.24.1

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .
EXPOSE 8080

CMD ["go", "run", "./src_server/main.go"]
```
Base image: Official Go image (v1.24.1)

Dependencies: Downloads modules via go mod download

Code copy: Full project copied into /app

Startup command: Runs the server with go run ./src_server/main.go

#### 🚀 Running the Server
To build and start the backend container:

```bash
docker-compose up --build
```
Use the --build flag on first run or after code changes.

To stop the service:

```bash
docker-compose down
```

## Frontend

## GitHub Integration
### How our GitHub connection works
SafeCode allows users to log in using their GitHub accounts through the standard OAuth2 flow. This process is handled by two backend routes and initiated from the frontend.<br>
1. The frontend redirects the user to the `/auth/github` endpoint.
2. The backend constructs a GitHub OAuth2 URL and redirects the user to GitHub.
3. After login/authorization, GitHub redirects the user back to `/auth/github/callback` with a temporary code.
4. The backend exchanges this code for a GitHub access token.
5. The backend fetches the user's GitHub profile using the token.
6. The user is either created or updated in the database.
7. A JWT token is returned to the frontend for authentication.

#### 🧠 Technical Breakdown
##### 🔗 1. `GET /auth/github`

- Generates a GitHub OAuth2 URL using the config:
  ```go
  url := config.OAuth2Config.AuthCodeURL(config.OAuthStateString, oauth2.AccessTypeOffline)
  ```
Redirects the user to GitHub for authentication.

Includes a state parameter to protect against CSRF.

##### 🔁 2. GET /auth/github/callback
Validates the returned state to ensure it matches the one sent.

Exchanges the code for an access token via:
```go
token, err := OAuth2Config.Exchange(ctx, code)
```
Uses the token to query the GitHub API and retrieve user info:
```sql
GET https://api.github.com/user
```

Checks if the user already exists in MongoDB:
If not → creates the user

If yes → updates their GitHub token

Generates a JWT token using internal logic:

```go
jwtToken, err := generateJWT(user)
```
Returns:
- A success message
- The JWT token
- The GitHub user data

##### 🧑‍💻 Frontend Integration
On the frontend:
- You call GET /auth/github (can be triggered by a login button).
- The user is redirected to GitHub.
- After login, GitHub redirects to /auth/github/callback, which returns a JSON with a token.
- You can store the token (e.g., in localStorage) and use it for future authenticated requests.

##### 🛡️ Notes
Tokens are stored in the database (github_token) for optional future GitHub actions.

The JWT is required to access all protected endpoints in SafeCode.

This method does not require a password for GitHub users.

## 2. Create a GitHub OAuth Application

To allow users to log in via GitHub, you need to create an OAuth application on GitHub:

1. Go to [GitHub Developer Settings](https://github.com/settings/developers)
2. Click on **"New OAuth App"**
3. Fill in the fields:
   - **Application name**: `Safecode`
   - **Homepage URL**: `http://localhost:3000`
   - **Authorization callback URL**: `http://localhost:3000/auth/github/callback`
4. Click **"Register application"**
5. Copy the **Client ID** and **Client Secret**

## 3. Environment Configuration

Store your GitHub credentials in your `.env` file:

```env
GITHUB_CLIENT_ID=your_client_id
GITHUB_CLIENT_SECRET=your_client_secret
```
Make sure your Go application loads this .env file

3. **Create two routes**
   - 'auth/github' to redirect on Github Authentification page
   - 'auth/github/callback to come back on the application after Github Autorization
     
  After **callback**, the user is authenticated on the application and has a Github access Token.
  This Access Token is mendatory to use Github User's data, like repository or organisation.
  See the Github API documentation, to see al 
  
4. **GitHub Integration in This Project**
In this project, GitHub is used to allow the user to test a repository's code directly inside the app:

  - The user provides the HTTPS URL of a GitHub repository.
  - The app fetches the repository code and executes analysis or testing.
  - The GitHub Access Token is used to access private repositories if needed.

5. **Useful Links**
  - GitHub OAuth Apps Documentation
  - GitHub REST API Docs
  - godotenv (for loading .env in Go)

6. **Notes**
  - Don't forget to protect your .env file — never commit it to your repository.
  - You can store the access token in a session, database, or JWT, depending on your auth strategy.
  - Rate limiting applies to GitHub API requests. Use appropriate headers or tokens.
