# `Dev` // SafeCode Documentation

# Introduction
This documentation is for current or future developers. The purpose of it is to explain how the SafeCode project has been architected, how it has been created, how it works and how to add functionalities.

# Technologies used
- Go (Classic + Gin)
- Python (for AST conversion)
- MongoDB (Atlas)
- Docker

# User path
[UseCaseDiagram](images/SafeCodeUseCaseDiagram.png)

# Architecture
This architecture is currently in monolithic, here is the list of folders and their purpose:<br>


### 1. Ast
Folder name: `ast/`

### 2. Json
Folder name: `doc/`

### 3. Logic analyser
Folder name: `src_analyser/`

### 4. Logic parser
Folder name: `src_parser/`

### 5. Logic server
Folder name: `src_server/`

### 6. Docker
Folder name: `docker-compose.yml & Dockerfile`

# GitHub Integration Guide

This documentation explains how to integrate GitHub authentication and access into your Go project.

---

## 1. Create a GitHub OAuth Application

To allow users to log in via GitHub, you need to create an OAuth application on GitHub:

1. Go to [GitHub Developer Settings](https://github.com/settings/developers)
2. Click on **"New OAuth App"**
3. Fill in the fields:
   - **Application name**: `Safecode`
   - **Homepage URL**: `http://localhost:3000`
   - **Authorization callback URL**: `http://localhost:3000/auth/github/callback`
4. Click **"Register application"**
5. Copy the **Client ID** and **Client Secret**

---

## 2. Environment Configuration

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
