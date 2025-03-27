# Tampering

This example demonstrates information disclosure by injecting malicious query objects to a NoSQL database.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

    `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called __infodisclosure__. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

    `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

    ```
        http://localhost:3000/userinfo?username[$ne]=
    ```

4. Do you see user information being displayed despite the malicious request not having a valid username in the request?

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**
   The main vulnerability in insecure.ts is the lack of input validation and sanitization when handling user input for database queries. The application directly uses the username provided in the query parameters to perform a MongoDB query without checking or sanitizing it. This makes the application vulnerable to NoSQL injection attacks, where specially crafted inputs can manipulate the query logic. Additionally, if a user is found, the full user object is returned, which may include sensitive data (e.g., passwords), leading to information disclosure.

2. Briefly explain how a malicious attacker can exploit them.
   An attacker could exploit this by submitting a malicious query parameter, such as ?username[$ne]=null, which leverages MongoDB’s query operators to bypass the intended query logic. This could return unintended user records or even expose all users depending on how the query is constructed. If the server responds with the full user object (including fields like password), the attacker gains unauthorized access to sensitive data. This kind of attack could lead to further exploitation such as account compromise or credential stuffing in other systems.

3. Briefly explain the defensive techniques used in **secure.ts** to prevent the information disclosure vulnerability?
   The secure.ts version addresses the vulnerability by applying two key security measures. First, it validates the input type, ensuring that username is actually a string, which helps block malformed or unexpected values. Second, it sanitizes the input by stripping out non-alphanumeric characters, reducing the risk of NoSQL operators being injected. Additionally, the application includes error handling to avoid revealing internal errors to the user and avoids sending sensitive fields in responses, further preventing data leakage. These measures together significantly reduce the risk of information disclosure via NoSQL injection.

