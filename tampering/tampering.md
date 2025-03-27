# Tampering

This example demonstrates tampering through script injection.

## Steps to reproduce

1. Install all dependencies

    `npm install`

2. Start the **insecure.ts** server

    `npx ts-node insecure.ts`

3. In the browser, type a potentially malicious script in the name field of the form

    ```
        <script> document.body.innerHTML = "<a href='https://google.com'> Gotcha </a>"</script>
    ```

4. Do you see the potentially malicious hyperlink being injected into the form?

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**
   The insecure.ts file is at risk of Cross-Site Scripting (XSS) because it doesn’t properly handle user input. Specifically, the server saves whatever the user types into the session (req.session.user = req.body.name.trim();) without checking or cleaning it. Later on, that same data is directly inserted into the HTML response using res.send, which means that if someone enters JavaScript code or HTML tags, the server will blindly include them in the page. Since there's no input validation or filtering, attackers can easily inject scripts or harmful links, assuming the input will always be safe.
  
2. Briefly explain how a malicious attacker can exploit them.
    An attacker can abuse this setup in a few different ways:
   - Redirects to harmful websites: By injecting a link like <a href="http://malicious.site">Click here</a>, they could trick users into clicking on dangerous URLs.
   - Running scripts in the browser: Using something like <script>alert('Hacked!')</script>, the attacker could make the page execute JavaScript whenever someone visits it. This is a classic example of XSS.
   - Phishing: The attacker might even add a fake login form using HTML tags (like <form action="http://malicious.site">) to steal usernames and passwords by making it look like a real form on the site.
  
3. Briefly explain why **secure.ts** does not have the same vulnerabilties?
   The improved version in secure.ts fixes the XSS vulnerability by sanitizing user input before it’s ever stored or rendered on the page.
   - Input Sanitization using escapeHTML()
   The escapeHTML() function cleans the user’s input by converting special characters like <, >, ", ', and & into their safe HTML equivalents (&lt;, &gt;, etc.). This stops the browser from interpreting them as HTML or script — so any injected code gets shown as plain text instead of being executed.
   - Protection Against Stored XSS
   Because the input is sanitized before being stored in the session, even if someone tries to insert a script or a malicious tag, it won’t run. The browser just displays it as normal text, making the attack useless. This effectively blocks things like <a> tags, <form> elements, or <script> blocks from causing any harm.