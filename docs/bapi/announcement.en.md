## AsterDex BAPI Overview

> **Base URLs**
> - Composite / Announcement: `https://www.asterdex.com`
> - Futures / Portfolio: `https://asterdex.com/bapi/futures`
>
> **Authentication**
> - `public` endpoints — no authentication required
> - `private` endpoints — require valid user session / auth headers

---

## Announcement Module

### Common Response Object — `AnnouncementResp`

| Field | Type | Description |
|-------|------|-------------|
| `id` | `Long` | Announcement ID |
| `category` | `String` | Category, see enum values below |
| `title` | `String` | Title |
| `subtitle` | `String` | Subtitle |
| `content` | `String` | Body content |
| `publishTime` | `Date` | Publish time (Unix timestamp in milliseconds) |
| `jumpLink` | `String` | Detail page URL |

**Category Enum**

| Value | Description |
|-------|-------------|
| `ACTIVITY` | Activity announcements |
| `NEW_LISTING` | New token listings |
| `DELISTING` | Delisting notices |
| `UPDATES` | Platform updates |

---

### Get Announcement

**`PUBLIC`** `GET /bapi/composite/v1/public/composite/ae/announcement/get`

Retrieve a single announcement by ID.

**Request Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | `Long` | Yes | Announcement ID |

**Request Example**

```bash
curl -X GET "https://www.asterdex.com/bapi/composite/v1/public/composite/ae/announcement/get?id=12345"
```

**Response Example**

```json
{
  "code": "000000",
  "message": null,
  "data": {
    "id": 12345,
    "category": "NEW_LISTING",
    "title": "AsterDex Will List XYZ Token",
    "subtitle": "XYZ/USDT Trading Pair Now Available",
    "content": "AsterDex is pleased to announce the listing of XYZ Token...",
    "publishTime": 1717545600000,
    "jumpLink": "https://www.asterdex.com/en/support/announcement/detail/12345"
  },
  "success": true
}
```

---

### Search Announcements

**`PUBLIC`** `POST /bapi/composite/v1/public/composite/ae/announcement/search`

Paginated search for announcements with optional category filtering.

**Request Body**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | `Integer` | Yes | Page number (starts from 1) |
| `size` | `Integer` | Yes | Number of items per page |
| `category` | `String` | No | Announcement category; returns all categories if omitted |

**Request Examples**

Fetch all categories, page 1, 10 items per page:

```bash
curl -X POST "https://www.asterdex.com/bapi/composite/v1/public/composite/ae/announcement/search" \
  -H "Content-Type: application/json" \
  -d '{
    "page": 1,
    "size": 10
  }'
```

Filter by category (`NEW_LISTING`):

```bash
curl -X POST "https://www.asterdex.com/bapi/composite/v1/public/composite/ae/announcement/search" \
  -H "Content-Type: application/json" \
  -d '{
    "page": 1,
    "size": 10,
    "category": "NEW_LISTING"
  }'
```

**Response** — `SearchResult<AnnouncementResp>`

| Field | Type | Description |
|-------|------|-------------|
| `total` | `Long` | Total number of records |
| `rows` | `List<AnnouncementResp>` | List of announcements |

**Response Example**

```json
{
  "code": "000000",
  "message": null,
  "data": {
    "total": 128,
    "rows": [
      {
        "id": 12345,
        "category": "NEW_LISTING",
        "title": "AsterDex Will List XYZ Token",
        "subtitle": "XYZ/USDT Trading Pair Now Available",
        "content": "AsterDex is pleased to announce the listing of XYZ Token...",
        "publishTime": 1717545600000,
        "jumpLink": "https://www.asterdex.com/en/support/announcement/detail/12345"
      }
    ]
  },
  "success": true
}
```
