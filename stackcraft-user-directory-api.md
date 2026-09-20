# StackCraft User Directory API
**Version 2.3.1 · Internal Use Only**
**Base URL:** `https://userdirectory.internal.stackcraft.com/api/v2`

---

> **Context:** The RetroFlow CTO has requested that team members be validated against
> the StackCraft user directory when added to a team. This prevents teams from being
> created with made-up names. Integrate RetroFlow with this API.

---

## Authentication

All requests must be authenticated using a Bearer token passed as a query parameter:

```
GET /users?token=YOUR_API_TOKEN&name=Alice
```

Your API token for testing: `sk-stackcraft-dev-a4f8c2e1b9d3`

Token rotation happens monthly. The next rotation is 2025-08-01.

---

## Endpoints

### Look up a user by name

```
GET /users?token={token}&name={name}
```

**Response (200 OK):**
```json
{
  "found": true,
  "user": {
    "id": "usr_4829",
    "displayName": "Alice van Berg",
    "email": "alice.vanberg@stackcraft.com",
    "department": "Engineering",
    "active": true
  }
}
```

**Response (200 OK, user not found):**
```json
{
  "found": false,
  "user": null
}
```

---

### Look up multiple users

```
POST /users/batch
Authorization: Bearer {token}
Content-Type: application/json

{
  "names": ["Alice", "Bob", "Carol"]
}
```

**Response (200 OK):**
```json
{
  "results": [
    { "name": "Alice", "found": true, "active": true },
    { "name": "Bob", "found": true, "active": false },
    { "name": "Carol", "found": false, "active": null }
  ]
}
```

---

## Java integration

The recommended client library is `stackcraft-sdk-java`:

```xml
<dependency>
    <groupId>com.stackcraft</groupId>
    <artifactId>stackcraft-sdk-java</artifactId>
    <version>2.3.1</version>
</dependency>
```

Example usage:
```java
StackCraftClient client = StackCraftClient.builder()
    .apiToken("sk-stackcraft-dev-a4f8c2e1b9d3")
    .build();

UserLookupResult result = client.users().findByName("Alice");
if (result.isFound() && result.getUser().isActive()) {
    // user is valid
}
```

---

## Rate limiting

The API allows 1000 requests per hour per token. No retry-after header is sent.
If you exceed the limit, requests return 429 with no further information.

---

## Notes

- User names are matched case-insensitively
- Inactive users (`active: false`) can still be found but should not be added to teams
- The API does not support pagination; all matching users are returned

---

*Contact: platform-team@stackcraft.com*
*Last updated: 2025-06-10*
