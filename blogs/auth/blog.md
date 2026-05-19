# Authentication and Authorization

Authentication and authorization are related, but they solve different problems.

* **Authentication** = proving *who you are*
* **Authorization** = deciding *what you are allowed to do*

  Example:

* Logging into an app with Google → Authentication
* Accessing only admin routes → Authorization

---

# 1. Stateful Authentication

In stateful authentication, the server stores the user’s login state.

### How it works

1. User logs in with email/password
2. Server verifies credentials
3. Server creates a session and stores it in a database or memory store
4. A session ID is sent to the browser using cookies
5. On every request:

   * browser automatically sends the cookie
   * server checks the session in the database
   * if valid, the user is authenticated

### Flow

```text
User Login → Server Creates Session → Session Stored in DB
                ↓
          Session ID sent in Cookie
                ↓
Browser sends Cookie on every request
                ↓
Server checks Session DB

```

### Advantages

* Easy to revoke sessions
* More control over user sessions
* Good security for traditional web apps

### Disadvantages

* Requires server-side storage
* Harder to scale in distributed systems
* Every request usually needs a database/session lookup

### Common Uses

* Traditional server-rendered apps
* Banking systems
* Enterprise applications

---

# 2. Stateless Authentication

In stateless authentication, the server does **not** store session data.

Instead, the client stores a token such as a JWT (JSON Web Token).

### How it works

1. User logs in
2. Server verifies credentials
3. Server creates a signed token (JWT)
4. Token is stored in:

   * cookies
   * localStorage
   * memory
5. On every request:

   * client sends the token
   * server verifies the token signature
   * no database lookup is required for authentication

### JWT Structure

A JWT usually contains:

* Header
* Payload (user info, roles, expiration)
* Signature

Example:

```text
xxxxx.yyyyy.zzzzz
```

### Flow

```text
User Login → Server Creates JWT
                ↓
Client Stores Token
                ↓
Client sends Token on every request
                ↓
Server verifies Signature
```

### Advantages

* Highly scalable
* No session storage needed
* Works well for APIs and microservices

### Disadvantages

* Harder to revoke tokens before expiration
* Token leakage can be dangerous
* Large payloads increase request size

### Important Security Note

JWTs are only safe if:

* signed with a strong secret/private key
* sent over HTTPS
* stored securely

Avoid storing sensitive data inside JWT payloads because payloads can be decoded easily.

### Common Uses

* REST APIs
* Mobile apps
* Microservices
* Modern frontend frameworks

---

# 3. API Key Authentication

API key authentication is commonly used for server-to-server communication.

### How it works

1. A resource server generates an API key
2. Client includes the API key in requests
3. Server validates the key
4. If valid, access is granted

Example:

```http
Authorization: ApiKey abc123xyz
```

### Key Idea

Whoever owns the API key is treated as the authorized client.

### Advantages

* Very simple to implement
* Fast
* Good for internal services

### Disadvantages

* If the API key leaks, anyone can use it
* Usually identifies the application, not the actual user
* Hard to manage fine-grained permissions

### Best Practices

* Rotate keys regularly
* Set expiration dates
* Limit permissions/scopes
* Never expose secret API keys in frontend code

### Common Uses

* Third-party APIs
* Payment gateways
* Internal backend services

Examples:

* Stripe
* OpenAI
* GitHub

---

# 4. OAuth (Open Authorization)

OAuth allows users to log into one application using another trusted platform.

This is commonly called:

* **SSO (Single Sign-On)**
* “Login with Google/GitHub/etc.”

OAuth is mainly about **delegated access** — allowing one application to access limited user data from another platform without sharing passwords.

---

## How OAuth Works

Suppose you log into a Todo app using Google.

### Step-by-step

1. User clicks **Login with Google**
2. Todo app redirects user to Google
3. User signs in to Google
4. Google asks:

   * “Do you allow this app to access your profile/email?”
5. User grants permission
6. Google sends an authorization token/code
7. Todo app exchanges it for an access token
8. User is now logged into the Todo app

### Flow

```text
User → Todo App → Google Login
                     ↓
            User Grants Permission
                     ↓
           Google returns Token
                     ↓
           Todo App authenticates User
```

### Advantages

* Users do not need separate passwords for every app
* More secure than sharing credentials
* Easier onboarding and login experience

### Disadvantages

* More complex to implement
* Dependency on third-party providers
* Misconfigured OAuth flows can create security risks

### Common OAuth Providers

* Google
* GitHub
* Microsoft
* Clerk
* Auth0

---

![](https://raw.githubusercontent.com/MdNihal05/files/refs/heads/main/blogs/auth/image.png)

# Authorization

Authentication verifies identity.

Authorization determines permissions.

Example:

```text
User logged in successfully → Authentication
User can delete posts only if admin → Authorization
```

### Common Authorization Models

## Role-Based Access Control (RBAC)

Users are assigned roles.

Example:

```text
Admin → Full access
Editor → Edit content
User → Read-only access
```

## Permission-Based Authorization

Users receive specific permissions instead of broad roles.

Example:

```text
can_create_post
can_delete_comment
can_manage_users
```

## Attribute-Based Access Control (ABAC)

Access depends on attributes like:

* user department
* location
* time
* subscription plan

Used in enterprise systems.

---

# Choosing the Right Authentication Method

| Method          | Best For                | Stateful? | Scalable? |
| --------------- | ----------------------- | --------- | --------- |
| Session Auth    | Traditional web apps    | Yes       | Medium    |
| JWT/Auth Tokens | APIs & mobile apps      | No        | High      |
| API Keys        | Service-to-service APIs | No        | High      |
| OAuth           | Social login & SSO      | Mixed     | High      |

---

# Practical Industry Pattern

Modern applications often combine multiple methods:

```text
OAuth for login
↓
Server creates JWT/session
↓
Authorization middleware checks permissions
```

Example:

* Login with Google using OAuth
* Receive JWT
* Backend checks:

  * Is user authenticated?
  * Is user an admin?
  * Can user access this resource?


