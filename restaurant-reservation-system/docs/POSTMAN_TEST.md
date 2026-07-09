# Postman Tests — Restaurant Reservation System

## 1. Testing Goal

The backend API must be fully tested with Postman before starting the React frontend integration.

The goal is to validate:

- Public reservation flow
- Admin authentication
- Protected admin endpoints
- Business rule validations
- Error responses
- Reservation status changes

---

## 2. Test Environment

Base URL:

```text
http://localhost:8080/api
```

Admin test user:

```json
{
  "email": "admin@restaurant.com",
  "password": "password123"
}
```

---

## 3. Public Endpoints Tests

## 3.1 Check Availability

Endpoint:

```text
GET /availability?date=2026-07-20&time=20:00&partySize=4
```

### Success Case

Expected status:

```text
200 OK
```

Expected result:

```json
{
  "available": true,
  "availableTables": 1,
  "message": "Tables are available for the selected time."
}
```

### Error Cases

| Case                          | Expected Status |
| ----------------------------- | --------------- |
| Date is in the past           | 400 Bad Request |
| Party size is 0               | 400 Bad Request |
| Party size is negative        | 400 Bad Request |
| Time is outside opening hours | 400 Bad Request |
| No table available            | 400 Bad Request |

---

## 3.2 Create Reservation

Endpoint:

```text
POST /reservations
```

### Valid Request

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

Expected status:

```text
201 Created
```

Expected result:

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

### Error Cases

| Case                              | Expected Status |
| --------------------------------- | --------------- |
| Missing customer name             | 400 Bad Request |
| Invalid email format              | 400 Bad Request |
| Missing phone                     | 400 Bad Request |
| Date is in the past               | 400 Bad Request |
| Party size is 0                   | 400 Bad Request |
| No available table                | 400 Bad Request |
| Party size exceeds table capacity | 400 Bad Request |

---

## 3.3 Cancel Reservation by Code

Endpoint:

```text
PATCH /reservations/{reservationCode}/cancel
```

Example:

```text
PATCH /reservations/RSV-20260720-ABCD/cancel
```

### Success Case

Expected status:

```text
200 OK
```

Expected result:

```json
{
  "reservationCode": "RSV-20260720-ABCD",
  "status": "CANCELLED",
  "message": "Reservation cancelled successfully."
}
```

### Error Cases

| Case                            | Expected Status |
| ------------------------------- | --------------- |
| Reservation code does not exist | 404 Not Found   |
| Reservation already cancelled   | 400 Bad Request |
| Reservation date is in the past | 400 Bad Request |

---

## 4. Authentication Tests

## 4.1 Admin Login

Endpoint:

```text
POST /auth/login
```

### Valid Request

```json
{
  "email": "admin@restaurant.com",
  "password": "password123"
}
```

Expected status:

```text
200 OK
```

Expected result:

```json
{
  "token": "jwt-token",
  "role": "ADMIN"
}
```

### Error Cases

| Case              | Expected Status  |
| ----------------- | ---------------- |
| Wrong email       | 401 Unauthorized |
| Wrong password    | 401 Unauthorized |
| Empty credentials | 400 Bad Request  |

---

## 5. Admin Reservation Tests

All admin requests must include:

```text
Authorization: Bearer jwt-token
```

---

## 5.1 Get All Reservations

Endpoint:

```text
GET /admin/reservations
```

Expected status:

```text
200 OK
```

### Error Cases

| Case          | Expected Status  |
| ------------- | ---------------- |
| Missing token | 401 Unauthorized |
| Invalid token | 401 Unauthorized |

---

## 5.2 Filter Reservations by Status

Endpoint:

```text
GET /admin/reservations?status=PENDING
```

Expected status:

```text
200 OK
```

Expected result:

Only reservations with status `PENDING` should be returned.

---

## 5.3 Confirm Reservation

Endpoint:

```text
PATCH /admin/reservations/{id}/confirm
```

Expected status:

```text
200 OK
```

Expected result:

```json
{
  "id": 1,
  "status": "CONFIRMED",
  "message": "Reservation confirmed successfully."
}
```

### Error Cases

| Case                          | Expected Status |
| ----------------------------- | --------------- |
| Reservation does not exist    | 404 Not Found   |
| Reservation already confirmed | 400 Bad Request |
| Reservation cancelled         | 400 Bad Request |

---

## 5.4 Cancel Reservation as Admin

Endpoint:

```text
PATCH /admin/reservations/{id}/cancel
```

Expected status:

```text
200 OK
```

Expected result:

```json
{
  "id": 1,
  "status": "CANCELLED",
  "message": "Reservation cancelled successfully."
}
```

### Error Cases

| Case                          | Expected Status |
| ----------------------------- | --------------- |
| Reservation does not exist    | 404 Not Found   |
| Reservation already cancelled | 400 Bad Request |

---

## 6. Admin Table Tests

All table management endpoints require admin authentication.

---

## 6.1 Get Tables

Endpoint:

```text
GET /admin/tables
```

Expected status:

```text
200 OK
```

---

## 6.2 Create Table

Endpoint:

```text
POST /admin/tables
```

Valid request:

```json
{
  "tableNumber": 5,
  "capacity": 4,
  "active": true
}
```

Expected status:

```text
201 Created
```

### Error Cases

| Case                        | Expected Status  |
| --------------------------- | ---------------- |
| Capacity is 0               | 400 Bad Request  |
| Capacity is negative        | 400 Bad Request  |
| Table number already exists | 409 Conflict     |
| Missing token               | 401 Unauthorized |

---

## 6.3 Update Table

Endpoint:

```text
PUT /admin/tables/{id}
```

Valid request:

```json
{
  "tableNumber": 5,
  "capacity": 6,
  "active": true
}
```

Expected status:

```text
200 OK
```

### Error Cases

| Case                        | Expected Status |
| --------------------------- | --------------- |
| Table does not exist        | 404 Not Found   |
| Capacity is invalid         | 400 Bad Request |
| Table number already exists | 409 Conflict    |

---

## 7. Business Rule Tests

The following business rules must be tested manually in Postman:

- A reservation cannot be created in the past.
- A reservation cannot be created outside opening hours.
- A reservation cannot have 0 guests.
- A reservation cannot exceed table capacity.
- A table cannot be double-booked for the same date and time.
- Cancelled reservations must not block availability.
- Admin endpoints must reject requests without JWT.
- Only pending reservations can be confirmed.

---

## 8. MVP Completion Criteria

The backend will be considered ready for frontend development when:

- All public endpoints work correctly.
- Admin login returns a valid JWT.
- Protected endpoints reject unauthenticated requests.
- Admin can manage reservations.
- Admin can manage tables.
- Business validations return clear errors.
- Postman collection is organized and saved.
