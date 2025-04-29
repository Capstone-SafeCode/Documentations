# `Technical - Level 3` // SafeCode Documentation

## 📚 Table of Contents

1. [Introduction](#introduction)
2. [Our CWEs](#our-cews)
3. [Get code from GitHub](#get-code-from-github)
4. [Configure CI/CD](#configure-cicd)
5. [Report in PDF](#report-in-pdf)
6. [Install SafeCode locally](#install-safecode-locally)

## Introduction
Here is the documentation allocated to you according to your licence level. It will complement the Non-technical documentation, which is public.<br>
To get the documentation for the last level, increase the level of your licence.

## Our CWEs
- `CWE-20: Improper Input Validation`<br>
The product receives input or data, but it does not validate or incorrectly validates that the input has the properties that are required to process the data safely and correctly.

- `CWE-22: Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal')`<br>
The product uses external input to construct a pathname that is intended to identify a file or directory that is located underneath a restricted parent directory, but the product does not properly neutralize special elements within the pathname that can cause the pathname to resolve to a location that is outside of the restricted directory.

- `CWE-74: Improper Neutralization of Special Elements in Output Used by a Downstream Component ('Injection')`<br>
The product constructs all or part of a command, data structure, or record using externally-influenced input from an upstream component, but it does not neutralize or incorrectly neutralizes special elements that could modify how it is parsed or interpreted when it is sent to a downstream component.

- `CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')`<br>
The product does not neutralize or incorrectly neutralizes user-controllable input before it is placed in output that is used as a web page that is served to other users.

- `CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')`<br>
The product constructs all or part of an SQL command using externally-influenced input from an upstream component, but it does not neutralize or incorrectly neutralizes special elements that could modify the intended SQL command when it is sent to a downstream component. Without sufficient removal or quoting of SQL syntax in user-controllable inputs, the generated SQL query can cause those inputs to be interpreted as SQL instead of ordinary user data.

- `CWE-94: Improper Control of Generation of Code ('Code Injection')`<br>
The product constructs all or part of a code segment using externally-influenced input from an upstream component, but it does not neutralize or incorrectly neutralizes special elements that could modify the syntax or behavior of the intended code segment.

- `CWE-200: Exposure of Sensitive Information to an Unauthorized Actor`<br>
The product exposes sensitive information to an actor that is not explicitly authorized to have access to that information.

- `CWE-201: Insertion of Sensitive Information Into Sent Data`<br>
The code transmits data to another actor, but a portion of the data includes sensitive information that should not be accessible to that actor.

- `CWE-285: Improper Authorization`<br>
The product does not perform or incorrectly performs an authorization check when an actor attempts to access a resource or perform an action.

- `CWE-287: Improper Authentication`<br>
When an actor claims to have a given identity, the product does not prove or insufficiently proves that the claim is correct.

- `CWE-352: Cross-Site Request Forgery (CSRF)`<br>
The web application does not, or cannot, sufficiently verify whether a request was intentionally provided by the user who sent the request, which could have originated from an unauthorized actor.

- `CWE-384: Session Fixation`<br>
Authenticating a user, or otherwise establishing a new user session, without invalidating any existing session identifier gives an attacker the opportunity to steal authenticated sessions.

- `CWE-613: Insufficient Session Expiration`<br>
According to WASC, "Insufficient Session Expiration is when a web site permits an attacker to reuse old session credentials or session IDs for authorization."

- `CWE-639: Authorization Bypass Through User-Controlled Key`<br>
The system's authorization functionality does not prevent one user from gaining access to another user's data or record by modifying the key value identifying the data.

- `CWE-798: Use of Hard-coded Credentials`<br>
The product contains hard-coded credentials, such as a password or cryptographic key.

- `CWE-917: Improper Neutralization of Special Elements used in an Expression Language Statement ('Expression Language Injection')`<br>
The product constructs all or part of an expression language (EL) statement in a framework such as a Java Server Page (JSP) using externally-influenced input from an upstream component, but it does not neutralize or incorrectly neutralizes special elements that could modify the intended EL statement before it is executed.

## Get code from GitHub

## Configure CI/CD
### What does it do ?
- The user adds a SafeCode step to their GitHub Actions pipeline.
- For each push or pull request, the workflow prepares the code, compresses it into a `.zip` file, and sends it to the SafeCode API using an authentication token.
- SafeCode analyzes the code and returns a `pass/fail` status based on the percentage of vulnerabilities in it.
- If the result is `fail`, the workflow can block the merge or deployment.
### How to configure it ?
To integrate SafeCode into a GitHub Actions pipeline, the user must follow these steps:

1. **Generate an API token**  
   Go to the SafeCode dashboard and generate a personal API token under the **CI/CD Integration** section. This token will be used to authenticate API requests.

2. **Add the token as a secret in your repository**  
   In GitHub, go to your repository → **Settings** → **Secrets and variables** → **Actions** → Add a new secret:
   - Name: `SAFECODE_TOKEN`
   - Value: *(your generated token)*

3. **Add a SafeCode step to your GitHub Actions workflow**  
   Below is an example workflow snippet:

   ```yaml
   name: SafeCode CI Check

   on: [push, pull_request]

   jobs:
     safecode:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v3

         - name: Zip source code
           run: zip -r safecode.zip .

         - name: Send code to SafeCode
           run: |
             curl -X POST https://safecode.io/api/analyse-ci \
               -H "Authorization: Bearer ${{ secrets.SAFECODE_TOKEN }}" \
               -F "file=@safecode.zip" \
               -o safecode-result.json

         - name: Check result
           run: |
             STATUS=$(jq -r .status safecode-result.json)
             if [ "$STATUS" = "fail" ]; then
               echo "❌ SafeCode check failed. Blocking pipeline."
               exit 1
             else
               echo "✅ SafeCode check passed."
             fi

## Report in PDF
Creating certifications for solutions or software is important to prove their reliability and security. The PDF reports generated by SafeCode can respond to this need.
### How to get one ?
1. **Go to the folder of the project for which you want the report**
2. **Click on `Get a report` in the top right-hand corner**
3. **Select the parameters you want in your report**
4. **Confirm to start generation and download**

## Install SafeCode locally
### What does it do?
The Enterprise version of SafeCode allows clients to self-host the entire platform within their own infrastructure.<br>
It runs fully offline via Docker, and provides the same functionalities as the SaaS version but without requiring any internet access, after installation and licensing.

The system includes:
- A local web frontend (Nuxt-based + Dockerized)
- A backend engine for static code analysis (Dockerized)
- A MongoDB database to store reports and data
- A license file that controls activation and expiration

### How to install it?
1. **Download the deployment package**

Contact us to receive the installation bundle:
- `docker-compose.yml`
- Docker images
- A license file: `safecode.lic` (locked)

2. **Place the license file**
   
Put the `safecode.lic` file in the same directory as your `docker-compose.yml`.
```
/safecode-enterprise/
  ├── docker-compose.yml
  ├── safecode.lic
```
> This file is mandatory to start SafeCode. It contains your company’s license key and expiration date.

3. **Start the application**

Use Docker Compose to run the platform:
`docker-compose up -d`

This command will:
- Start the local MongoDB database.
- Launch the SafeCode backend engine.
- Serve the SafeCode frontend.

### After installation
Once running:
1. Open your browser and go to http://localhost:3000
2. Create your user account (GitHub is unavailable in this mode)
3. Use SafeCode just like the cloud version

### Notes
- Licenses are time-limited. You will need to request a new license when it expires.
- The frontend is fully customizable (option). You have to redeploy it internally with your own style or modifications.
- All data is stored locally (MongoDB volume). Ensure you have enough storage (recommended: 2GB+).

### Requirements
- Docker installed on your server or machine.
- Disk space for analysis logs and reports.
- A valid `safecode.lic` license file.

For support or updates, contact us at: enterprise@safecode.io
