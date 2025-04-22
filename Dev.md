# `Dev` // SafeCode Documentation

# Introduction
The purpose of this documentation is to explain how the SafeCode project has been architected, how it has been created, how it works and how to add functionalities.

# Architecture

# Github
### The user enters the GitHub URL of a repo and whether it is public or private.

He is redirected to GitHub to authenticate himself, then we get an `access_token`

The frontend sends the link + public/private + token to the backend.

The backend downloads the `.zip` via the Github api and places it in the `uplaods/` folder in the private area of the user.

The analysis is then run as a traditional `.zip` file 
