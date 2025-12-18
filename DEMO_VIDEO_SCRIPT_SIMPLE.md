# Demo Video Script - What to Say

## Introduction

**What to Say:**

"Hello, I'm showing you the Samaritan Bayanihan API for my final project. This API works with both JSON and XML formats. It has five operations: GET, POST, PUT, and DELETE for the Benefits module."

---

## Part 1: Login

**What to Say:**

"First, I need to log in. I'll use a member account to get a token."

[Login in Postman]

**What to Say:**

"Good! I got the token. Now I'll copy it and put it in the Authorization header."

[Copy token, paste in Authorization]

**What to Say:**

"Done! The token is set. Now let's test the API."

---

## Part 2: GET - List Benefits (JSON)

**What to Say:**

"Let's get a list of benefits. First, I'll use JSON format."

[Send GET request]

**What to Say:**

"Great! The API sent back a JSON response with a list of benefits. You can see it has success, message, data, and page information."

---

## Part 3: GET - List Benefits (XML)

**What to Say:**

"Now let's do the same thing but ask for XML format."

[Send GET request with Accept: application/xml]

**What to Say:**

"Perfect! The same data came back in XML format. The API sees the Accept header and sends back XML."

---

## Part 4: POST - Create Benefit (JSON)

**What to Say:**

"Now let's create a new benefit. I'll use JSON format first."

[Send POST request]

**What to Say:**

"Good! The benefit was created. The response shows 201 Created and gives us the new benefit with an ID. I'll use this ID later to update it."

---

## Part 5: POST - Create Benefit (XML)

**What to Say:**

"Now let's create another benefit, but this time using XML format."

[Send POST request with XML]

**What to Say:**

"Excellent! The API read the XML request and created the benefit. The response is also in XML. This shows our API works with both JSON and XML."

---

## Part 6: PUT - Update Benefit (JSON)

**What to Say:**

"Now let's update the benefit I created earlier. I'll use the ID from the first POST."

[Send PUT request]

**What to Say:**

"Perfect! The benefit was updated. You can see the requested amount and reason changed."

---

## Part 7: PUT - Update Benefit (XML)

**What to Say:**

"Let's update another benefit using XML format."

[Send PUT request with XML]

**What to Say:**

"Great! The XML update worked. The API read the XML request and sent back an XML response."

---

## Part 8: Switch to Admin Account

**What to Say:**

"Now I need to use an admin account because only admins can delete benefits. Let me log in as admin."

[Login as admin]

**What to Say:**

"Good! I got the admin token. Now I'll copy it and update the Authorization header."

[Update token]

**What to Say:**

"Done! The admin token is set. Now let's test the DELETE operations."

---

## Part 9: DELETE - Delete Benefit (JSON)

**What to Say:**

"Now I'll delete a benefit. First, let's use JSON format."

[Send DELETE request]

**What to Say:**

"Excellent! The benefit was deleted. The response confirms it was successful."

---

## Part 10: DELETE - Delete Benefit (XML)

**What to Say:**

"Finally, let's test DELETE with XML format."

[Send DELETE request with XML]

**What to Say:**

"Perfect! The XML DELETE worked. The response is in XML format."

---

## Conclusion

**What to Say:**

"In this demo, I tested all five API operations: GET, POST, PUT, and DELETE. The API works with both JSON and XML formats. It checks the Accept header to know what format to send back. It uses tokens for security. Only admins can delete benefits. All the status codes are correct. This shows the API meets all the final project requirements for JSON and XML support."

