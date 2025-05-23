﻿# `Non-technical` // SafeCode Documentation

## 📚 Table of Contents

1. [Introduction](#introduction)
2. [Vocabulary](#vocabulary)
3. [Current functionalities](#current-functionalities)
4. [Future functionalities](#future-functionalities)
5. [QuickStart](#quickstart)
6. [Pricing](#pricing)
7. [FAQ](#faq)

## Introduction
Safe code is a SaaS web application that detects vulnerabilities in source code sent by the user in `.zip` format. Weaknesses are displayed, along with advice on how to resolve them and the sources of the detections.

## Vocabulary
- `Vulnerability` → A weakness in a system or a code that can be exploited to compromise its security or functionality.
- `CWE (Common Weakness Enumeration)` → A standardized list of software and hardware weakness types used for identifying and categorizing vulnerabilities.
- `CVSS (Common Vulnerability Scoring System)` → A framework (tool) for rating the severity of security vulnerabilities (CWE) based on defined metrics.

## Current functionalities
- Secure connection
- GitHub connection
- Upload `.zip` file/Get from GitHub
- Automatic code analysis
- Analysis history (with few information)
- Clear, interactive reporting
- Python support

## Future functionalities
- To support C#
- A better interface for viewing vulnerabilities
- The application could be configured to suit your environment
- Add a CVSS score calculator based on the parameters entered
- To have his organisation on SafeCode

## QuickStart
1. **Go to the SafeCode website**
2. **Click on `Get Started`**
<img src="images/main-page.png" width="800"/>

3. **Create an account and log in**
<img src="images/create-account-page.png" width="800"/>
<img src="images/login-page.png" width="800"/>

4. **If you are not already in it, click on `Go to dashboard`**
5. **Click or drag and drop your `.zip` file**
<img src="images/put-zip-page.png" width="800"/>

6. **View your report**
<img src="images/review-page.png" width="800"/>

## Pricing
| Level 1                        | Level 2                       | Level 3                          |
|----------------------------------|----------------------------------|----------------------------------|
| <img src="images/Free.png" width="400"/> | <img src="images/Team.png" width="400"/> | <img src="images/Enterprise.png" width="400"/> |

### - Free
Downloadable from our website. The versions available will always be one or two versions behind the last one.<br> 
(Level 1 technical documentation becomes accessible).

### - Team
Downloadable from our website. The price will be the basic price and will increase according to the number of lines of code analysed during the month.<br>
(Level 2 technical documentation becomes accessible).

### - Enterprise
Please contact us to discuss your requirements and the possibility of installing SafeCode locally in your infrastructure.<br>
(Level 3 technical documentation becomes accessible).

> **💡Note:** Other optional services, like training, custom options and more, are also available (price on request)

## FAQ
### **- My zip doesn't work**
→ Check your connexion and you are logged well.<br>
→ Check that it is not empty or corrupted.<br>
→ Is it a real `.zip` and not with an hiden dot ?<br>

### **- The report doesn't detect anything**
→ Is your language supported ?<br>
→ Check your licence.<br>
→ Are you too good in code ?<br>

### **- Is it secure ?**
→ All the code we keep is encrypted, so we don't have access to it.<br>
→ We only keep the last analyse, never the others.<br>

## Sources we used
- [OWASP](https://owasp.org/Top10/fr/) (To see the list of 2021 CWEs)
- [Mitre](https://cwe.mitre.org) (To find out what is the vulneravilities of a CWE)
- [FIRST](https://www.first.org/cvss/) (For the CVSS score)
