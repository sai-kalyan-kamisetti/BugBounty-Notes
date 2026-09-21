[Daily-Notes.md](https://github.com/user-attachments/files/32478152/Daily-Notes.md)
[PortSwigger-DOM-XSS-Labs.md](https://github.com/user-attachments/files/32419692/PortSwigger-DOM-XSS-Labs.md)
# PortSwigger DOM XSS Labs

1. Carefully inspect the page and the placeholder where you want to inject the script.
2. Also don't forget to look over the JavaScript files associated with the functions.
3. After understanding both in depth, move on to crafting a suitable payload and exploiting it.

### Lab 2 — DOM XSS in `document.write` sink using source `location.search`

- Clearly states that the XSS lies in the search functionality, so I inspected that.
- Found there is an input tag, so gracefully closing it and injecting a payload does the job.
- Payload used: `search_input"><script>alert(1)</script>`

### Lab 3 — `innerHTML` in Search Functionality

[](https://github.com/sai-kalyan-kamisetti/BugBounty-Notes/blob/main/Portswigger%20Labs/XSS.md#lab-1--innerhtml-in-search-functionality)

- The search feature uses `innerHTML` to render results/queries on the page.
- Approach: break out of the existing tag context, then inject a payload.
- **Payload used:**

    ```html
    /><img src=x onerror=alert("hi") />
    ```

- **Takeaway:** Always check for `innerHTML` usage in externally loaded JavaScript files — it's a common unsafe sink.

### Lab 4 — Reflected Input Without Sanitization

[](https://github.com/sai-kalyan-kamisetti/BugBounty-Notes/blob/main/Portswigger%20Labs/XSS.md#lab-2--reflected-input-without-sanitization)

- If user input is reflected back into the same page, check whether it's sanitized.
- If not, break out of context directly:

    ```html
    "><script>alert("trigger")</script>
    ```

- **Takeaway:** Reflection ≠ vulnerability by itself — always verify whether encoding/sanitization is actually applied before the payload lands in the DOM.

### Lab 5 — `javascript:` URI Context

[](https://github.com/sai-kalyan-kamisetti/BugBounty-Notes/blob/main/Portswigger%20Labs/XSS.md#lab-3--javascript-uri-context)

- Context: executing JavaScript through an HTML attribute or a URL (e.g. a link/redirect field) rather than directly in the DOM.
- **Payload used:**

    ```
    javascript:alert(document.cookie)
    ```

- **Takeaway:** When a sink accepts a URL (`href`, `src`, redirect params, etc.), the `javascript:` pseudo-protocol can be used to execute script directly from that context.

### Lab 6 — Delivering the Exploit URL via Nested `iframe`

[](https://github.com/sai-kalyan-kamisetti/BugBounty-Notes/blob/main/Portswigger%20Labs/XSS.md#lab-4--delivering-the-exploit-url-via-nested-iframe)

- The lab's goal is to deliver a malicious URL that triggers the exploit when visited (client-side exploit delivery, not just proving `alert()`).
- **Payload used:**

    ```html
    <iframe src="https://0ae800fd0328b51881cfb195006000e5.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
    ```

- **Takeaway:** An `iframe` can load the vulnerable page and, on `onload`, append a secondary payload to its `src` (e.g. via the URL fragment) — useful when the vulnerable sink reads from `location.hash` and needs the final exploit delivered as one shareable URL.

### Lab 7 — Bypassing Angle-Bracket Encoding

[](https://github.com/sai-kalyan-kamisetti/BugBounty-Notes/blob/main/Portswigger%20Labs/XSS.md#lab-5--bypassing-angle-bracket-encoding)

- Observed that `<` and `>` were being HTML-encoded, so any payload using `<script>` or `<img>` tags would fail.
- Solution: break out of an existing HTML attribute using only quotes and an event handler — no angle brackets needed.
- **Payload used:**

    ```
    " onmouseover="alert(1)
    ```

- **Takeaway:** When angle brackets are encoded/filtered but the input still lands inside an existing HTML tag's attribute, closing the attribute with `"` and adding a new event handler (`onmouseover`, `onfocus`, etc.) can still achieve execution without needing `<` or `>` at all.

### Lab 8 — Stored XSS into Anchor `href` Attribute with Double Quotes HTML-Encoded

- While checking the functionality of all the input fields — comments, name, email, and website — and inspecting them, I found that clicking a user's name redirects to the website given by that user (also mentioned in the lab description).
- Then switched on Burp and tried sending some payloads, starting with:
  - `<script>alert(1)</script>` — where the angle brackets are encoded.
  - `"onmouseover="alert(1)` — where the quotes are encoded.
- As noted by Vickie Li, the injection point is at `href`, and since the quotes and angle brackets are encoded, a suitable payload is `javascript:alert(1)`.
- The above payload triggers the alert function when the author's name in the comments is clicked.

### Lab 9 — Reflected XSS into a JavaScript String with Angle Brackets HTML-Encoded

- Inspecting the search bar showed me a script tag where `var searchterm = ...` is identified.
- The goal is clear: close that JavaScript string and insert the payload.
- To close a JavaScript string, we can use `'`, and to end the statement, use `;`.
- The following payload should be complete so that the remaining JavaScript runs without any error.
- The payload is: `'; alert(1); var foo='` (taken from *The Web Application Hacker's Handbook*).
- The HTML encoding is done by the `encodeURIComponent()` component, which doesn't encode the following characters: basic letters (`A-Z`, `a-z`), numbers (`0-9`), or a few special symbols (`-`, `_`, `.`, `!`, `~`, `*`, `'`, `(`, `)`). So the payload is crafted around these characters.

## Lab 11 (Practitioner 1) - DOM XSS in document.write sink using source location.search inside a select element

- The problem statement states that the vulnerability is in `storeId`.
- Initially it's not given in the URL, but it clearly appeared in Burp Suite, so using that, I appended it to the URL.
- The description tells us to "skip the tag," which refers to skipping HTML tags entirely. A payload suitable for this is:

    ```html
    /><script>alert(1)</script>
    ```

## Lab 12 (Practitioner 2) - DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded

- The problem statement says to exploit using an AngularJS expression.
- It also says the quotes and angle brackets are encoded, so an equivalent payload would be `javascript:alert(1)`.
- Now we need to convert this into a payload suitable for an AngularJS expression.
- Speaking about AngularJS — it's a JavaScript framework (currently discontinued), and expressions are represented within `{{ }}`.
- A suitable payload is:

    ```input
    {{constructor.constructor('alert(1)')()}}
    ```


