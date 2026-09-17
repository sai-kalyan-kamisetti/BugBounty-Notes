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

---

*Notes compiled during ongoing web application security / XSS study (PortSwigger labs).*
