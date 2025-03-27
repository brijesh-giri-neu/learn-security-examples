# Repudiation

The example demonstrates a vulnerability that can lead to repudiation by malicious users attempting to access the services provided by a server.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Run the server __insecure.ts__.

3. Pretend to be a malicous user and interact with the services by sending requests from the browser.

4. Do you think your actions can be repudiated?

## For you to do

1. Briefly explain the vulnerability.
   The insecure implementation doesn’t log any of the incoming requests or responses. This is a problem because without logging, there's no way to keep track of what users are doing, troubleshoot problems, or identify when something suspicious is happening. If something breaks or an attacker does something shady, there’s no trail to follow. This lack of visibility makes it hard to debug, audit, or respond to issues in a timely and effective way.

2. Briefly explain why the vulnerability is addressed in __secure.ts__.
   The secure version fixes the issue by adding a logging middleware that records key information about every request. It captures details like the request method, the route being accessed, the user’s IP address, and the time the request was made. On top of that, specific actions like sending or fetching messages are also logged. All this info is written to a server.log file, which gives developers and admins the ability to track activity, identify problems, and spot unusual behavior. That said, writing logs directly to a file can become a performance bottleneck, especially if the app gets a lot of traffic. If logging is done synchronously or takes too long, it could even cause delays or lead to a DoS situation. A better approach would be to handle logging asynchronously or offload it to an external service to keep things smooth.


3. Which design pattern is used in the secure version to address the vulnerability? Briefly explain how it works?
   The Middleware Pattern is used to implement logging in the secure version. In Express, middleware functions run in sequence for each request. The logging middleware is placed early in the request cycle so it can capture and log request details before they reach the actual route handlers. This keeps the logging logic separate from the core functionality of each endpoint, making the code cleaner and easier to maintain. It also makes sure logging happens consistently across all routes without having to repeat code.