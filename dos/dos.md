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

The insecure.ts file has multiple vulnerabilities that can lead to a Denial-of-Service (DoS) attack. The most significant issue is the lack of input validation, which makes the application vulnerable to NoSQL injection. The /userinfo route directly uses user-supplied input in a MongoDB query without sanitization, allowing an attacker to manipulate the query structure and potentially access unintended data. Additionally, the server does not have rate limiting, meaning an attacker can flood it with requests, overwhelming the database and causing service disruption. Furthermore, there are no safeguards against malformed input, which can result in unhandled exceptions, potentially crashing the server. In other words, If a malicious request is made, such as providing an invalid ID format, MongoDB may throw an error, crashing the server.


2. Briefly explain how a malicious attacker can exploit them.

A malicious attacker can exploit these vulnerabilities in several ways. By sending specially crafted NoSQL queries, such as id[$ne]= with a 24 character hex string, 12 byte Uint8Array, or an integer, an attacker could retrieve all users’ data or bypass authentication mechanisms. Additionally, by continuously sending a high volume of requests with expensive or malformed queries, the attacker can overload the database, slowing down performance or causing a crash. If the server does not properly handle database query errors, an attacker might intentionally send invalid data to trigger application failures, making the system unavailable to legitimate users.

3. Briefly explain the defensive techniques used in **secure.ts** to prevent the DoS vulnerability?

The secure.ts file implements several defensive techniques to mitigate the DoS vulnerabilities found in insecure.ts. First, it introduces rate limiting using the express-rate-limit middleware, which restricts each IP to one request every five seconds, preventing attackers from overwhelming the server with excessive queries. Additionally, the code now includes a try-catch block to handle errors gracefully, ensuring that unexpected database errors do not crash the application. While it still lacks proper input validation, implementing checks such as verifying whether id is a valid ObjectId before querying MongoDB would further improve security and prevent NoSQL injection attacks. In other worsds, Use libraries like mongoose.Types.ObjectId.isValid(id) to prevent invalid queries.