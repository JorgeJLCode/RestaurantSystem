# API Design — Restaurant Reservation System

## 1. API Goal

The REST API must allow customers to check table availability and create reservations, while allowing admins to manage reservations and tables from a protected area.

The API will be designed first and tested with Postman before connecting the React frontend.

---

## 2. Base URL

```text
/api
```

---

## 3. Authentication Endpoints

### POST /auth/login

Allows an admin user to log in.

#### Request Body

```json
{
  "email": "admin@restaurant.com",
  "password": "password123"
}
```

#### Success Response

```json
{
  "token": "jwt-token",
  "role": "ADMIN"
}
```

#### Possible Errors

- 401 Unauthorized — Invalid email or password

---

## 4. Public Reservation Endpoints

### GET /availability

Checks available tables for a specific date, time and party size.

#### Query Params

```text
date=2026-07-20
time=20:00
partySize=4
```

#### Example

```text
GET /api/availability?date=2026-07-20&time=20:00&partySize=4
```

#### Success Response

```json
{
  "available": true,
  "availableTables": 3,
  "message": "Tables are available for the selected time."
}
```

#### Possible Errors

- 400 Bad Request — Invalid date, time or party size
- 400 Bad Request — Reservation date cannot be in the past
- 400 Bad Request — Restaurant is closed at selected time

---

### POST /reservations

Creates a new reservation.

#### Request Body

```json
{
  "customerName": "John Smith",
  "customerEmail": "john@example.com",
  "customerPhone": "+34600000000",
  "reservationDate": "2026-07-20",
  "reservationTime": "20:00",
  "partySize": 4
}
```

#### Success Response

```json
{
  "reservationCode": "RSV-20260720-ABCD",
  "customerName": "John Smith",
  "reservationDate": "2026-07-20",
  "reservationTime": "20:00",
  "partySize": 4,
  "status": "PENDING",
  "message": "Reservation created successfully."
}
```

#### Possible Errors

- 400 Bad Request — Invalid input data
- 400 Bad Request — Reservation date cannot be in the past
- 400 Bad Request — No table available for selected date and time
- 400 Bad Request — Party size exceeds available table capacity

---

### PATCH /reservations/{reservationCode}/cancel

Cancels a reservation using its public reservation code.

#### Example

```text
PATCH /api/reservations/RSV-20260720-ABCD/cancel
```

#### Success Response

```json
{
  "reservationCode": "RSV-20260720-ABCD",
  "status": "CANCELLED",
  "message": "Reservation cancelled successfully."
}
```

#### Possible Errors

- 404 Not Found — Reservation not found
- 400 Bad Request — Reservation is already cancelled
- 400 Bad Request — Past reservations cannot be cancelled

---

## 5. Admin Reservation Endpoints

These endpoints require admin authentication.

### GET /admin/reservations

Returns all reservations.

#### Optional Query Params

```text
status=PENDING
date=2026-07-20
```

#### Success Response

```json
[
  {
    "id": 1,
    "reservationCode": "RSV-20260720-ABCD",
    "customerName": "John Smith",
    "customerEmail": "john@example.com",
    "customerPhone": "+34600000000",
    "reservationDate": "2026-07-20",
    "reservationTime": "20:00",
    "partySize": 4,
    "status": "PENDING",
    "tableNumber": 5
  }
]
```

#### Possible Errors

- 401 Unauthorized — Missing or invalid token
- 403 Forbidden — User is not admin

---

### PATCH /admin/reservations/{id}/confirm

Confirms a pending reservation.

#### Success Response

```json
{
  "id": 1,
  "status": "CONFIRMED",
  "message": "Reservation confirmed successfully."
}
```

#### Possible Errors

- 404 Not Found — Reservation not found
- 400 Bad Request — Only pending reservations can be confirmed

---

### PATCH /admin/reservations/{id}/cancel

Cancels a reservation from the admin panel.

#### Success Response

```json
{
  "id": 1,
  "status": "CANCELLED",
  "message": "Reservation cancelled successfully."
}
```

#### Possible Errors

- 404 Not Found — Reservation not found
- 400 Bad Request — Reservation is already cancelled

---

## 6. Admin Table Endpoints

These endpoints require admin authentication.

### GET /admin/tables

Returns all restaurant tables.

#### Success Response

```json
[
  {
    "id": 1,
    "tableNumber": 5,
    "capacity": 4,
    "active": true
  }
]
```

---

### POST /admin/tables

Creates a new restaurant table.

#### Request Body

```json
{
  "tableNumber": 5,
  "capacity": 4,
  "active": true
}
```

#### Success Response

```json
{
  "id": 1,
  "tableNumber": 5,
  "capacity": 4,
  "active": true
}
```

#### Possible Errors

- 400 Bad Request — Invalid table data
- 409 Conflict — Table number already exists

---

### PUT /admin/tables/{id}

Updates an existing restaurant table.

#### Request Body

```json
{
  "tableNumber": 5,
  "capacity": 4,
  "active": true
}
```

#### Possible Errors

- 404 Not Found — Table not found
- 400 Bad Request — Invalid table data
- 409 Conflict — Table number already exists

---

## 7. Standard Error Response

All API errors should follow this structure:

```json
{
  "status": 400,
  "error": "Bad Request",
  "message": "Reservation date cannot be in the past",
  "path": "/api/reservations"
}
```

---

## 8. Security Rules

Public endpoints:

- POST /auth/login
- GET /availability
- POST /reservations
- PATCH /reservations/{reservationCode}/cancel

Protected admin endpoints:

- GET /admin/reservations
- PATCH /admin/reservations/{id}/confirm
- PATCH /admin/reservations/{id}/cancel
- GET /admin/tables
- POST /admin/tables
- PUT /admin/tables/{id}

Only users with ADMIN role can access admin endpoints.

---

## 9. MVP API Decision

The API will keep the customer flow public and simple.

Admin features will be protected with JWT authentication.

This keeps the MVP realistic, secure and manageable for a junior full-stack portfolio project.
