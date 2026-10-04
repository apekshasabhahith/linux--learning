# OverTheWire Natas — Levels 1–10

## About Natas

[Natas](https://overthewire.org/wargames/natas/) is a web-security wargame from OverTheWire.

Unlike Bandit, which mainly focuses on Linux commands, Natas focuses on **web application security**.

The levels teach how to inspect a website, understand HTTP requests, identify insecure application behavior, and recognize common web vulnerabilities.

> **Note:** Passwords and credentials are intentionally not included in these notes.

---

# Natas 1

## Concept
**Client-side restrictions and HTML source inspection**

### Goal

The page contains a restriction that prevents the normal right-click menu from being used.

The important question is:

> Is right-click protection actually preventing us from accessing the HTML?

### Observation

The browser must receive the webpage's HTML in order to display it.

Therefore, even if JavaScript disables right-click, the HTML source still exists in the browser.

### Method

Use:

```text
Ctrl + U
```

This opens the page source.

The required information can be found inside the HTML source.

### Why does this work?

Right-click blocking is implemented on the **client side** using JavaScript.

The server has already sent the HTML to the browser.

Therefore:

```text
Server
   ↓
HTML
   ↓
Browser
   ↓
JavaScript disables right-click
```

The JavaScript cannot make the already-delivered HTML disappear.

### Security Lesson

Client-side restrictions are not reliable security controls.

Examples include:

- Disabling right-click
- Hiding buttons with JavaScript
- Hiding information using CSS
- Client-side validation

If sensitive information is sent to the browser, a user can potentially inspect it.

### Key Takeaway

> **Never rely on client-side controls to protect sensitive information.**

---

# Natas 2

## Concept
**Information disclosure and exposed files**

### Goal

Find information that is not directly displayed on the webpage.

### Observation

Inspect the HTML source.

The page contains a reference to an image.

The important part is not the image itself.

The important clue is the **path to the image**.

For example, a webpage may contain something similar to:

```html
<img src="files/pixel.png">
```

This tells us that a directory named:

```text
files/
```

exists on the server.

### Reasoning

If the website exposes:

```text
/files/pixel.png
```

then the directory structure may also reveal other files.

This is an example of **information disclosure**.

### Why inspect source code?

The normal webpage might show only:

```text
Welcome
```

But the HTML source can reveal:

```text
images
directories
file names
comments
links
parameters
```

Therefore, source-code inspection is an important first step during web security testing.

### Security Lesson

Files that are not intended to be public should not be placed in publicly accessible directories.

Simply hiding a file from the webpage does not protect it.

### Key Takeaway

> **Always investigate paths and resources referenced by a webpage.**

---

# Natas 3

## Concept
**robots.txt and information disclosure**

### Goal

Find a location that the website does not want search-engine crawlers to access.

### Observation

Websites may contain a file called:

```text
robots.txt
```

It provides instructions to web crawlers.

Open:

```text
/robots.txt
```

The file contains rules such as:

```text
Disallow: /some-directory/
```

### Important Understanding

`robots.txt` is **not a security mechanism**.

It tells well-behaved crawlers:

> "Please do not crawl this path."

It does NOT tell the web server:

> "Block users from accessing this path."

Therefore, if a sensitive path appears inside `robots.txt`, it may actually reveal useful information to an attacker.

### Why is this a problem?

Suppose:

```text
robots.txt
```

contains:

```text
Disallow: /secret/
```

A person can simply request:

```text
/secret/
```

if the server does not otherwise restrict access.

### Security Lesson

Never use:

```text
robots.txt
```

to protect sensitive information.

Proper protection requires:

- Authentication
- Authorization
- Access controls
- Server-side restrictions

### Key Takeaway

> **Robots.txt controls crawler behavior, not user authorization.**

---

# Natas 4

## Concept
**HTTP Referer header and weak access control**

### Goal

Understand why the page cares about where the request came from.

### Observation

The webpage indicates that access depends on the **Referer** header.

The HTTP request can contain a header such as:

```http
Referer: http://example.com/
```

This tells the server which page the browser claims the user came from.

### Important Question

Can the server trust this value?

No.

The Referer header is supplied as part of the HTTP request and can be manipulated.

### Request Flow

Normally:

```text
Browser
   ↓
HTTP Request
   ↓
Referer: previous-page
   ↓
Server
```

The server checks the Referer.

If the application assumes:

```text
Correct Referer = trusted user
```

then the access control is weak.

### Security Problem

A request header controlled by the client should not be treated as proof of authentication or authorization.

### Security Lesson

Headers such as:

```text
Referer
User-Agent
Cookie
```

must be handled carefully because client-controlled request data can potentially be modified.

### Key Takeaway

> **Never use a client-controlled HTTP header as strong proof of authorization.**

---

# Natas 5

## Concept
**Cookies and client-controlled state**

### Goal

Understand how the website uses browser state to determine access.

### Observation

The application uses a cookie to store information related to the user's state.

HTTP cookies are sent by the browser with requests.

Example:

```http
Cookie: name=value
```

### Why is this important?

The browser stores the cookie and sends it back to the server.

Therefore:

```text
Server
   ↓
sets cookie
   ↓
Browser stores cookie
   ↓
Browser sends cookie
   ↓
Server
```

If an application stores an authorization decision directly in a client-controlled value, that value may be manipulated.

### Security Problem

A cookie should not automatically be trusted simply because it came back from the browser.

Sensitive authorization decisions must be validated securely on the server.

### Security Lesson

Client-controlled state should never be treated as unquestionable proof of identity or privilege.

Secure applications should use:

- Proper authentication
- Server-side session management
- Secure cookie attributes
- Server-side authorization checks

### Key Takeaway

> **The browser is controlled by the user, so client-side values cannot automatically be trusted.**

---

# Natas 6

## Concept
**Source-code disclosure and insecure configuration**

### Goal

Understand how the application obtains an important value from a configuration file.

### Observation

The webpage provides a clue that the application uses a secret/configuration value.

Inspecting the source code reveals how the value is loaded.

The important idea is to follow the program's logic:

```text
User input
     ↓
PHP code
     ↓
Configuration value
     ↓
Comparison
     ↓
Result
```

### Why inspect source code?

Source code can reveal:

- Variable names
- File locations
- Validation logic
- Authentication logic
- Configuration files
- Hidden application behavior

### Security Problem

If a configuration file containing secrets becomes publicly accessible, an attacker may obtain information that should remain server-side.

### Security Lesson

Sensitive configuration should be:

- Stored outside public web directories where possible
- Protected with proper file permissions
- Excluded from source repositories
- Managed using secure secret-management mechanisms

### Key Takeaway

> **Never expose configuration files containing secrets to users.**

---

# Natas 7

## Concept
**Local File Inclusion / Path Traversal**

### Goal

Understand how a URL parameter can influence which file the server reads.

### Observation

The page contains a parameter similar to:

```text
?page=...
```

The application uses this value to determine which page/file should be displayed.

For example:

```text
?page=home
```

The server might internally perform something similar to:

```text
include(page);
```

### Why is this dangerous?

The user controls the value of the parameter.

Therefore:

```text
User
 ↓
?page=value
 ↓
Server uses value as file path
```

If the application does not properly validate the value, the user may be able to make the server read files that were never intended to be accessible.

### Path Traversal

A common filesystem concept is:

```text
..
```

which means:

> Move to the parent directory.

For example:

```text
folder/subfolder/..
```

refers back to:

```text
folder/
```

### Security Problem

Applications should never blindly use user input as a filesystem path.

### Secure Approach

Applications should:

- Validate input
- Use allowlists
- Normalize paths
- Restrict accessible directories
- Avoid directly passing user input to file-inclusion functions

### Key Takeaway

> **User-controlled file paths can lead to unauthorized file access.**

---

# Natas 8

## Concept
**Encoding, decoding and source-code analysis**

### Goal

Understand how the application transforms a user-supplied value before comparing it with a secret.

### Observation

The source code shows multiple transformations.

The important task is to read the operations carefully and understand them in the correct order.

A transformation might conceptually look like:

```text
Original value
      ↓
Encoding
      ↓
Transformation
      ↓
Decoding
      ↓
Comparison
```

### Important Principle

Encoding is **not encryption**.

For example, Base64 is an encoding mechanism.

It is designed to represent data in another format, not to keep the data secret.

### Why source-code analysis matters

Instead of blindly guessing a value, we can reverse the operations performed by the application.

If the application performs:

```text
value
 → encode
 → transform
 → compare
```

we can analyze the transformation and determine what input would produce the expected result.

### Security Lesson

Do not use reversible encoding as a method of protecting secrets.

Examples:

```text
Base64
URL encoding
Hex encoding
```

These are not substitutes for encryption.

### Key Takeaway

> **Understand the application's transformation logic instead of treating encoded data as secure.**

---

# Natas 9

## Concept
**OS Command Injection**

### Goal

Understand how user input can become part of a server-side operating-system command.

### Observation

The webpage contains a search field.

The application processes the submitted value using a system command.

Conceptually, the application may do something similar to:

```text
system("command " + user_input);
```

This is dangerous because the user controls part of the command.

### The Problem

Suppose the application expects:

```text
apple
```

and constructs:

```text
command apple
```

The developer may think the input is only a search term.

But the shell interprets special characters as commands/operators.

Therefore:

```text
User input
     ↓
Application
     ↓
Shell command
     ↓
Operating system
```

If input is not safely handled, the user may influence what the operating system executes.

### Command Substitution

One important shell feature is command substitution:

```bash
$(command)
```

The shell executes the command inside `$()` and substitutes its output into the surrounding command.

This is why command injection can become much more powerful than simply changing a search term.

### Security Impact

Command injection can potentially allow an attacker to:

- Execute commands
- Read sensitive files
- Modify data
- Access environment information
- Compromise the server

### Secure Approach

The best solution is to avoid passing user input to a shell.

Prefer:

- Safe APIs
- Parameterized commands
- Strict allowlists
- Input validation
- Proper escaping when a shell is absolutely unavoidable

### Key Takeaway

> **Never directly concatenate untrusted user input into an operating-system command.**

---

# Natas 10

## Concept
**Command injection and blacklist filtering**

### Goal

Understand why simply blocking a few characters does not make command execution safe.

### Observation

The application attempts to filter certain characters from the input.

This looks like:

```text
User input
     ↓
Blacklist filter
     ↓
Shell command
     ↓
Operating system
```

### Why is blacklist filtering weak?

A blacklist says:

> "These particular characters are forbidden."

But the shell has many different syntax features.

Therefore, blocking a small list of characters does not guarantee that the input is safe.

### Important Security Principle

A blacklist tries to identify:

```text
BAD input
```

An allowlist instead defines:

```text
ONLY acceptable
