# File Inclusion Leading to Configuration Disclosure

## Context

Configuration and resource files are loaded dynamically on the app based on the requirements of the request. 
This feature is used for:

- be able to support several environments
- to load feature-specific configuration
- manage localisation or theming
- Server-side file paths are built by user-driven input

---

## Issue Description

There is insufficient limitation of which files can be loaded by backend. 
Despite the functionality being to render a limited number of configuration files, the resolved file path is ultimately determined by user-controlled input.
Therefore, it is possible to access unintended files on the server, making the sensitive configuration data to be leaking. This represents file inclusion vulnerability for unauthorized file access.

---

## Impact

This flaw enables the adversary to:
- read internal configuration files.
- find application secrets, credentials etc.
- understand the conditions for the environment.
- internal application structure.

According to these exposed files, this may cause:
- credential compromise.
- further lateral movement.
- chaining and other vulnerabilities.
The effect is huge even without code execution.

---

## Reproduction Steps (Simplified)

1. Authenticate as a standard user.
2. Specify a function that loads files according to request parameters.
3. Intercept the request and change the file identifier.
4. File the request and input a crafted value pointing to files not intended.
5. Notice the server returns configuration data with far-flung scope.

The issue is non-related to file upload or running code.

---

## Root Cause Analysis

The vulnerability is due to:
- dynamic file path construction using user input
- inadequate validation of resolved file paths
- the assumption of directory boundaries

The application does not follow rigorous mapping between allowed identifiers and the true physical locations of the files.

---

## Recommendation

To remedy this issue:
- do not build file paths from user input
- apply stringent allowlists of configuration files for ease of access
- verify resolved paths after normalization
- save sensitive configuration in contexts not able to access the web

Access file system is a security sensitive operation.

---

## Notes

This result is a configuration disclosure issue, not a full file inclusion exploit in the conventional sense. The main risk is in information exposure and its vulnerability chaining possibility.
