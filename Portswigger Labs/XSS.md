[xss-notes (1).md](https://github.com/user-attachments/files/32389531/xss-notes.1.md)
# Cross-Site Scripting (XSS) Notes

Notes on XSS types and hands-on DOM XSS lab walkthroughs (PortSwigger).

---

## 1. XSS Types

### Reflected XSS
The payload travels to the **server**, gets embedded into the HTML response by server-side code, and the browser executes it only because the server echoed it back **unsanitized**.

### DOM XSS
The payload **never meaningfully touches server-side rendering logic**. It's processed entirely by **client-side JavaScript** that:
1. Reads data from the page/browser environment — URL, hash, referrer, `window.name`, etc.
2. Writes that data into the DOM using an **unsafe sink** — `innerHTML`, `document.write`, `eval`, etc.

---

## 2. General Testing Approach

- Look for an input field and think about: **what's the possible payload that bypasses existing filters, and how does it get rendered on the page?**
- Always check what happens to user input once it's reflected/rendered — is there sanitization, or not?

---

## 3. PortSwigger DOM XSS Labs

### Lab 1 — `innerHTML` in Search Functionality
- The search feature uses `innerHTML` to render results/query on the page.
- Approach: break out of the existing tag context, then inject a payload.
- **Payload used:**
  ```html
  /><img src=x onerror=alert("hi") />
  ```
- **Takeaway:** Always check for `innerHTML` usage in externally loaded JavaScript files — it's a common unsafe sink.

### Lab 2 — Reflected Input Without Sanitization
- If user input is reflected back into the same page, check whether it's sanitized.
- If not, break out of context directly:
  ```html
  "><script>alert("trigger")</script>
  ```
- **Takeaway:** Reflection ≠ vulnerability by itself — always verify whether encoding/sanitization is actually applied before the payload lands in the DOM.

### Lab 3 — `javascript:` URI Context
- Context: executing JavaScript through an HTML attribute or a URL (e.g. a link/redirect field) rather than directly in the DOM.
- **Payload used:**
  ```
  javascript:alert(document.cookie)
  ```
- **Takeaway:** When a sink accepts a URL (`href`, `src`, redirect params, etc.), the `javascript:` pseudo-protocol can be used to execute script directly from that context.

### Lab 4 — Delivering the Exploit URL via Nested `iframe`
- The lab's goal is to deliver a malicious URL that triggers the exploit when visited (client-side exploit delivery, not just proving alert()).
- **Payload used:**
  ```html
  <iframe src="https://0ae800fd0328b51881cfb195006000e5.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
  ```
- **Takeaway:** An `iframe` can load the vulnerable page and, on `onload`, append a secondary payload to its `src` (e.g. via the URL fragment) — useful when the vulnerable sink reads from `location.hash` and needs the final exploit delivered as one shareable URL.

### Lab 5 — Bypassing Angle-Bracket Encoding
- Observed that `<` and `>` were being HTML-encoded, so any payload using `<script>` or `<img>` tags would fail.
- Solution: break out of an existing HTML attribute using only quotes and an event handler — no angle brackets needed.
- **Payload used:**
  ```
  " onmouseover="alert(1)
  ```
- **Takeaway:** When angle brackets are encoded/filtered but the input still lands inside an existing HTML tag's attribute, closing the attribute with `"` and adding a new event handler (`onmouseover`, `onfocus`, etc.) can still achieve execution without needing `<` or `>` at all.
