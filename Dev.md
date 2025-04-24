# `Dev` // SafeCode Documentation

# Introduction
The purpose of this documentation is to explain how the SafeCode project has been architected, how it has been created, how it works and how to add functionalities.

# Architecture

# Github
### - The user must first be connected to SafeCode via GitHub.

### - The user enters the GitHub URL of his repo (HTTPS, not SSH).

### - The frontend call an api with all the tokens to the backend.

### - The backend manages the tokens it needs.

### - The backend downloads the `.zip` via the Github API and places it in the `uplaods/` folder in the private area of the user.

### - The analysis is then run as a traditional `.zip` file by the frontend. 
