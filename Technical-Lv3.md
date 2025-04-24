# `Technical - Level 3` // SafeCode Documentation

# Introduction
Here is the documentation allocated to you according to your licence level. It will complement the Non-technical documentation, which is public.<br>
To get the documentation for the last level, increase the level of your licence.

# Our CWEs
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

# Get code from GitHub

# Configure CI/CD

# Report in PDF
Creating certifications for solutions or software is important to prove their reliability and security. The PDF reports generated by SafeCode can respond to this need.
### How to get one ?
1. **Go to the folder of the project for which you want the report**
2. **Click on `Get a report` in the top right-hand corner**
3. **Select the parameters you want in your report**
4. **Confirm to start generation and download**

# Deploy local SafeCode
