# 🌐 API Fundamentals

> A beginner-friendly guide to understanding APIs, HTTP methods, request-response flow, headers, query parameters, JWT authentication, and API testing.

---

# 📚 Table of Contents

- What is an API?
- Client-Server Architecture
- API Communication Flow
- Resources
- HTTP Methods
- URL Structure
- Query Parameters
- HTTP Headers
- JSON
- JWT Authentication
- GET Request
- POST Request
- Complete API Flow
- Request vs Response
- HTTP Status Codes
- API Testing Tools
- Interview Questions
- Quick Revision

---

# 📌 What is an API?

**API (Application Programming Interface)** is a set of rules that allows two software systems to communicate with each other.

Think of an API as a **messenger** between a client and a server.

```
Client  ----Request---->  API  ---->  Server
Client  <---Response---- API  <----  Server
```

---

# 🏗 Client-Server Architecture

## Client

A client is any application that requests data.

Examples:

- 🌍 Website
- 📱 Mobile App
- 🤖 AI Agent
- 💻 Desktop Application

---

## Server

The server contains:

- Business Logic
- Database
- Authentication
- APIs
- Data Processing

The client **never directly accesses the database.**

---

# 🔄 API Communication Flow

```
Instagram App
       |
       | Request
       ▼
      API
       |
       ▼
 Server + Database
       |
       ▲
   Response
```

### Flow

1. Client sends a request.
2. API receives the request.
3. API performs business logic.
4. Server accesses the database.
5. Data is returned.
6. API sends the response back to the client.

---

# 📦 Resource

A **Resource** is any object stored on the server.

Examples:

- Users
- Posts
- Products
- Orders
- Comments

Example endpoints:

```
/users
/posts
/products
/orders
```

---

# 🌍 HTTP Protocol

APIs communicate using the **HTTP (HyperText Transfer Protocol)**.

```
Client
   |
HTTP Request
   |
Server
   |
HTTP Response
   |
Client
```

---

# ⚡ HTTP Methods (CRUD)

| Method | Purpose | CRUD Operation |
|----------|---------|---------------|
| GET | Retrieve Data | Read |
| POST | Create Data | Create |
| PUT | Replace Entire Resource | Update |
| PATCH | Update Specific Fields | Update |
| DELETE | Remove Data | Delete |

---

# 📥 GET Request

Used to retrieve data.

Example

```http
GET /api/v1/posts
```

Response

```json
[
    {
        "id":1,
        "title":"Learning APIs"
    }
]
```

---

# 📤 POST Request

Used to create new data.

Request

```http
POST /api/v1/posts
```

Headers

```http
Content-Type: application/json
Authorization: Bearer <JWT Token>
```

Body

```json
{
    "title":"Learning APIs",
    "body":"APIs connect clients and servers."
}
```

Response

```json
{
    "message":"Post created successfully"
}
```

---

# 🔄 PUT Request

Used to replace an entire resource.

Example

```http
PUT /api/v1/posts/1
```

Body

```json
{
    "title":"Updated Title",
    "body":"Updated Body"
}
```

---

# ✏ PATCH Request

Updates only specific fields.

Example

```http
PATCH /api/v1/posts/1
```

Body

```json
{
    "title":"Updated Title"
}
```

---

# ❌ DELETE Request

Deletes a resource.

Example

```http
DELETE /api/v1/posts/1
```

Response

```json
{
    "message":"Post deleted successfully"
}
```

---

# 🌐 URL Structure

Example

```http
GET /api/v1/posts?authorId=123&sort=createdAt_desc&page=2&pageSize=10
```

Breakdown

```
/api/v1/posts
│
├── api      → API
├── v1       → Version 1
└── posts    → Resource
```

---

# 🔍 Query Parameters

Query parameters begin with **?**

Example

```http
?authorId=123
```

Filters posts by author.

---

Sorting

```http
sort=createdAt_desc
```

Sorts posts by creation date (descending).

---

Pagination

```http
page=2&pageSize=10
```

Returns

- Page Number = 2
- 10 records per page

---

# 📋 HTTP Headers

Headers contain **metadata** about the request.

Example

```http
Host: example.com
Authorization: Bearer <JWT Token>
Accept: application/json
Content-Type: application/json
```

---

## Common Headers

### Authorization

```http
Authorization: Bearer eyJhbGc...
```

Used for authentication.

---

### Accept

```http
Accept: application/json
```

Requests the response in JSON format.

---

### Content-Type

```http
Content-Type: application/json
```

Specifies the format of the request body.

---

# 📄 JSON (JavaScript Object Notation)

Most APIs send and receive data in JSON format.

Example

```json
{
    "id":1,
    "title":"Learning APIs",
    "author":"Avidha"
}
```

Advantages

- Lightweight
- Human-readable
- Easy to parse
- Language independent

---

# 🔐 JWT (JSON Web Token)

JWT is commonly used for authentication.

Example

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

A JWT usually contains:

- User Information
- Permissions
- Expiration Time

---

## JWT Flow

```
User Login
      │
      ▼
Server verifies credentials
      │
      ▼
Creates JWT Token
      │
      ▼
Client stores token
      │
      ▼
Every future request

Authorization:
Bearer <JWT>
```

### Benefits

- Stateless
- Secure
- Compact
- Self-contained

---

# 🔄 Complete API Flow

```
User
  │
  │ GET /posts
  ▼

API

  │
Check JWT

  │
Business Logic

  │
Database

  │
Return Data

  ▼

Response
```

---

# 📬 Request vs Response

| Request | Response |
|----------|----------|
| Sent by Client | Sent by Server |
| Contains URL | Contains Data |
| Contains Headers | Contains Status Code |
| May contain Body | Returns JSON/XML |

---

# 📈 HTTP Status Codes

| Status Code | Meaning |
|-------------|----------|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 500 | Internal Server Error |

---

# 🛠 API Testing Tools

- Postman ⭐
- Thunder Client
- Insomnia
- curl
- Hoppscotch

---

# 💡 Interview Questions

## What is an API?

An API (Application Programming Interface) is a set of rules that enables different software applications to communicate and exchange data securely.

---

## Why do we use APIs?

- Frontend ↔ Backend Communication
- Third-party Integrations
- Secure Data Access
- Code Reusability
- Platform Independence

---

## Difference between PUT and PATCH?

| PUT | PATCH |
|------|--------|
| Updates entire resource | Updates specific fields |
| Sends complete object | Sends only modified fields |

---

## Why is JWT used?

JWT is used for secure authentication because it is:

- Stateless
- Compact
- Secure
- Easy to send in HTTP headers

---

## Difference between Authentication and Authorization?

| Authentication | Authorization |
|----------------|---------------|
| Verifies who you are | Verifies what you can access |
| Login | Permissions |

---

# 📝 Key Points

- API acts as a communication bridge between client and server.
- APIs communicate using the HTTP protocol.
- Resources represent data such as users, posts, and products.
- HTTP methods define CRUD operations.
- Query parameters are used for filtering, sorting, and pagination.
- Headers carry metadata such as authentication and content type.
- JSON is the standard data exchange format.
- JWT provides secure, stateless authentication.
- Postman is one of the most popular tools for testing APIs.

---

# 🚀 Quick Revision

✅ API = Communication bridge

✅ Client → API → Server

✅ Server contains business logic and database

✅ HTTP = Communication protocol

✅ GET = Read

✅ POST = Create

✅ PUT = Replace

✅ PATCH = Partial Update

✅ DELETE = Remove

✅ Headers = Metadata

✅ Query Parameters = Filter, Sort, Pagination

✅ JSON = Data Format

✅ JWT = Authentication Token

✅ Postman = API Testing Tool

---

# 🎯 Learning Outcome

After completing these notes, you should be able to:

- Explain what an API is.
- Understand client-server communication.
- Use HTTP methods correctly.
- Read and construct API URLs.
- Work with query parameters and headers.
- Understand JSON data exchange.
- Explain JWT authentication.
- Test APIs using Postman.
- Answer common API interview questions with confidence.

---

⭐ **Happy Learning! Keep Building APIs and Exploring Backend Development.**
