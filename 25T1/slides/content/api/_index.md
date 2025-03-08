+++
title = "API Design and Documentation"
outputs = ["Reveal"]
+++

# API Design and Documentation

`http://localhost:3000/get/some/help/loser`

---

{{% section %}}

slides are at [tp.denzeliskandar.com/api](https://tp.denzeliskandar.com/api)

{{% /section %}}

---

{{% section %}}

## disclaimer

* i am not an expert in API design and documentation
  * there are also some things i dont know
  * i am still learning too
* i might still get things wrong (apologies in advance)
* im trying my best (real)

---

this will be less of a workshop (me just yapping until u get bored) and more of an open discussion (you get to yap too - isnt it fun !!!!!)

{{% /section %}}

---

{{% section %}}

## what are apis

heard of the restaurant analogy?

---

### restaurant analogy

* you're sitting in a restaurant, browsing the menu
* call a waiter and say your orders
  * 1 banana and 2 eggs plz
* waiter takes the order to the kitchen
* chef cooks (literally)
* chef tells the waiter to deliver it back to you

---

### okay, and?

so in this analogy
* you are the **user** 
* waiter is the **API**
* chef/kitchen is the **server** 
* menu is the **documentation** 👻

in the real world...

{{% /section %}}

---

{{% section %}}

## http methods

REST APIs

---

### GET

* retrieves data from the server
* should not modify any data
* examples:
  * getting a user's profile
  * fetching a list of posts
  * downloading an image

```
GET /users/69
GET /posts?limit=10
```

---

### POST

* creates new resources on the server
* sends data in request body
* examples:
  * creating a new user
  * uploading a file
  * adding a comment

```
POST /users
{
    "name": "John",
    "email": "john@example.com"
}
```

---

### PUT

* updates existing resources
* replaces entire resource
* examples:
  * updating user profile
  * replacing a document

```
PUT /users/69
{
    "name": "John Updated",
    "email": "john.updated@example.com"
}
```

---

### DELETE

* removes resources from server
* examples:
  * deleting a user account
  * removing a post

```
DELETE /users/69
```

{{% /section %}}

---

{{% section %}}

## status codes

we need to know if things worked 🥸

---

### common status codes

* 200 OK - successful gjgj 👍
* 400 Bad Request - u messed up 😡
* 401 Unauthorized - who r u 🤨
* 403 Forbidden - 18+ content (jk!!) 🤭
* 404 Not Found - lost but never forgotten 🫡
* 500 Server Error - we messed up 💀

{{% /section %}}

---

{{% section %}}

## api endpoints

how to name things (the hard part)

---

### good practices

* use nouns for resources
  * ✅ `/users`
  * ❌ `/getUsers`

* use plural for collections
  * ✅ `/posts`
  * ❌ `/post`

just search "API Best Practices and Naming Conventions" in Google fr

---

### endpoint examples

getting user data:
```
GET /users/69
```

creating a post:
```
POST /posts
```

---

### versioning

* include version in URL
  * `/v1/users`

but for smaller projects like this, it is fine to not include it for simplicity

---

### security

* use HTTPS
* implement authentication
* rate limiting
* input validation
* do not expose sensitive data (e.g. passwords)

---

### error handling

* clear error messages
* consistent format
* appropriate status codes
* include helpful details

```json
{
    "status": "error",
    "code": "INVALID_INPUT",
    "message": "Email format is invalid",
    "details": {
        "field": "email",
        "value": "not-an-email"
    }
}
```

{{% /section %}}

---

{{% section %}}

## request & response

input and output 👻

---

### request components

* URL/Endpoint
* HTTP Method
* Headers
  * Authorization
  * Content-Type
* Body (for POST/PUT)
* Query Parameters
* Path Parameters

---

### response format

usually JSON:
```json
{
    "status": "success",
    "data": {
        "id": 69,
        "name": "John",
        "email": "john@example.com"
    }
}
```

{{% /section %}}

---

{{% section %}}

## documentation

future u will thank u 🤓

---

### what to include

* endpoint descriptions
* request/response examples
* authentication details
* error responses
* rate limits
* sample code

---

### tools for documentation

* Swagger/OpenAPI
* Postman
* README.md
* API Blueprint

{{% /section %}}

---

## designing apis 🛠️

what endpoints do we need for this project?
