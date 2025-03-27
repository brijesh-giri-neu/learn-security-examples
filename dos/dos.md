# Denial-of-Service (DoS)

This example demonstrates DoS vulnerabilities and how they can be exploited.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Ignore if you have already done this once. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

    `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called __infodisclosure__. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

    `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

    ```
        http://localhost:3000/userinfo?id[$ne]=
    ```

4. Do you see the server crashing?

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts** that can lead to a DoS attack.
   The main vulnerability in insecure.ts that can lead to a Denial-of-Service (DoS) attack is the lack of request rate limiting. There are no controls in place to restrict how frequently a client can send requests to the /userinfo endpoint. Additionally, the application performs synchronous, potentially heavy database operations (User.findOne) without any input validation or error handling, which could be exploited to overload the server with expensive queries.

2. Briefly explain how a malicious attacker can exploit them.
   An attacker could flood the /userinfo route with a rapid stream of requests, overwhelming the server’s ability to respond in a timely manner. This could slow down or completely halt the service for legitimate users. Since the server attempts to process each incoming request by querying the database without performing any input validation or sanitization, this opens the door to resource exhaustion. Repeatedly sending invalid IDs or random data could introduce delays or errors in database operations, increasing the overall system strain.

3. Briefly explain the defensive techniques used in **secure.ts** to prevent the DoS vulnerability?
   The secure.ts implementation introduces safeguards by using the express-rate-limit middleware to control traffic. This setup restricts each client IP to a single request every five seconds, which significantly curtails the chance of automated flooding. If a client exceeds this limit, the server sends back a user-friendly error message, preventing excessive resource usage. By limiting request frequency and handling abuse gracefully, this version reduces the likelihood of server overload and helps maintain service availability.
   