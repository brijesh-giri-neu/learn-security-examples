# Spoofing

This example demonstrates spoofind through two ways -- Stealing cookies programmatically and cross site request forgery (CSRF).

## Steps to reproduce the vulnerability

1. Install dependencies

    `$ npx install`

2. Start the **insecure.ts** server

    `npx ts-node insecure.ts`

3. Start the malicious server **mal.ts**

    `npx ts-node mal.ts`

4. Open __http://localhost:8000__ in a browser, type a name and Submit.

5. Open the __Application__ tab in the Browser's inspect pane. Find the __Cookies__ under __Storage__. You should see a __connect.sid__ cookie being set.

6. Open the HTML file __mal-steal-cookie.html__ file in the same browser (different tab). Open inspect and view the console.

7. Click the link in the HTML file. Do you see the cookie being stolen in the console?

8. Open the HTML file __mal-csrf.html__ file in the same browser (different tab). What do you see if the user has not logged out of **insecure.ts**? What do you see if the user has logged out? 


## For you to answer

1. Briefly explain the spoofing vulnerability in **insecure.ts**.
The spoofing vulnerability in **insecure.ts** is due to misconfigured security settings related to cookies. We have the below two issues here:
- `httpOnly` set to `false`
  This allows JavaScript on any page, even malicious ones, to access the session cookie.
- `sameSite` is not set  
  Due to this, the browser sends cookies with cross-site requests, making it possible to carry out CSRF attacks.

2. Briefly explain different ways in which the vulnerability can be exploited.
Due to the above-discussed vulnerabilities, an attacker can spoof or impersonate a user by doing the following:
- Steal Cookies Programmatically via malicious sites:  
  When a user visits a malicious page like `steal-cookie.html`, served by `mal.ts`, a script on the page accesses `document.cookie` because the session cookie is not marked as `httpOnly`. This allows the attacker to retrieve and potentially exfiltrate the session ID, which can then be used to impersonate the user.

- Cross-Site Request Forgery (CSRF): 
  If the user is logged into `insecure.ts` and then opens `csrf.html` in another tab, a hidden form automatically submits a POST request to the `/sensitive` endpoint. Since the cookie is sent with the request due to the lack of a `sameSite` restriction, the server treats it as a valid authenticated action, allowing sensitive operations to be performed without the user’s knowledge or consent.

3. Briefly explain why **secure.ts** does not have the spoofing vulnerability in **insecure.ts**.
The secure version, **secure.ts**, fixes the spoofing vulnerabilities by securing the session cookie in the following ways:
- `httpOnly: true` : Prevents JavaScript from accessing the cookie, blocking cookie theft through malicious scripts.
- `sameSite: true` : Prevents cookies from being sent with cross-origin requests, blocking CSRF.

Because of these configurations:
- Malicious sites cannot read the session cookie.
- Malicious forms/scripts cannot trigger authenticated actions using the user’s session.

Thus, `secure.ts` mitigates both spoofing vectors present in `insecure.ts`.