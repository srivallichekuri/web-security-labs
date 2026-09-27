# Authentication

## Status

Currently learning — selected concepts and labs completed.

This section documents concepts and practical exercises I have completed
while studying web application authentication vulnerabilities.

---

## What is Authentication?

Authentication is the process of verifying the identity of a user or system.

Common authentication factors include:

- Something you know — password
- Something you have — security token or mobile device
- Something you are — biometric characteristic

---

## Topics Studied

- Username enumeration
- Authentication error messages
- Response-time differences
- Brute-force protection
- HTTP Basic Authentication
- Multi-factor authentication
- 2FA implementation weaknesses
- Burp Intruder attack types

---

## Username Enumeration

Applications may unintentionally reveal whether a username exists through:

- Different error messages
- Different HTTP status codes
- Different response lengths
- Different response times

### Key lesson

Authentication responses should avoid revealing whether a particular
username exists.

---

## Burp Intruder

I studied the following attack types:

| Attack type | Basic idea |
|---|---|
| Sniper | One payload position |
| Battering ram | Same payload in multiple positions |
| Pitchfork | Multiple payload sets used together |
| Cluster bomb | Multiple payload sets with every combination |

---

## HTTP Basic Authentication

HTTP Basic Authentication sends credentials through the Authorization header
using Base64 encoding.

Base64 is an encoding mechanism, not encryption.

Therefore HTTPS is essential when Basic Authentication is used.

---

## Multi-Factor Authentication

MFA introduces an additional authentication factor.

Potential implementation weaknesses can occur when:

- The second authentication step is not properly enforced
- The second factor is not correctly linked to the authenticated identity
- Authentication state can be manipulated
- Rate limiting is weak

---

## Current Status

This topic is still in progress.

I am continuing to study authentication vulnerabilities through practical
labs and HTTP request analysis.
