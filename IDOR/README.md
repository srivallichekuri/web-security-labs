# IDOR / Insecure Direct Object References

## Overview

Insecure Direct Object Reference (IDOR) is an access-control vulnerability
that occurs when an application uses a user-controllable identifier to access
an object without properly verifying whether the current user is authorized
to access that object.

IDOR is commonly associated with broken access control.

---

## Authentication vs Authorization

Authentication answers:

> Who are you?

Authorization answers:

> Are you allowed to access this resource?

A user may be successfully authenticated but still not be authorized to
access another user's object.

---

## Basic Example

Consider:

GET /invoice?id=1001

If the application returns invoice 1001 to the authenticated user, an attacker
may test whether changing the identifier affects the returned resource:

GET /invoice?id=1002

Changing the identifier alone does not prove that an IDOR exists.

The important question is:

> Does the server verify that the authenticated user is authorized to access
> invoice 1002?

---

## What I Learned

An object identifier does not have to appear directly in the URL.

Object references can occur in:

- Query parameters
- URL paths
- POST bodies
- JSON parameters
- Cookies
- HTTP headers

Therefore, testing should focus on identifying values that influence which
server-side object is accessed.

---

## Burp Suite Methodology

My general testing process:

1. Log in as a normal user.
2. Identify requests that access user-specific resources.
3. Send interesting requests to Burp Repeater.
4. Identify parameters that control object selection.
5. Modify the object reference.
6. Observe the server response.
7. Determine whether the server performs an authorization check.

---

## Important Observation

Changing an identifier is not automatically an IDOR.

For example:

GET /profile?id=2

becoming:

GET /profile?id=3

is only evidence of a potential vulnerability.

The vulnerability exists when the server returns object 3 without verifying
whether the current user is authorized to access it.

---

## Prevention

Applications should perform server-side authorization checks for every
protected object.

Common approaches include:

- Verify object ownership
- Perform authorization checks on every request
- Avoid relying on client-side access controls
- Use indirect references where appropriate
- Apply least-privilege principles

Changing an object identifier should never be sufficient to obtain another
user's protected resource.

---

## Key Takeaway

The central lesson from studying IDOR is:

> Authentication identifies the user; authorization determines what that user
> is allowed to access.

Security decisions must be enforced by the server rather than trusted to the
client.
