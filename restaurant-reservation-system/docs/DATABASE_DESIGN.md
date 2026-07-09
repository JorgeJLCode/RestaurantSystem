# Database Design — Restaurant Reservation System

## 1. Database Goal

The database must support a restaurant reservation workflow where customers can create reservations without an account and admins can manage reservations securely.

The model is intentionally simple for an MVP portfolio project, avoiding unnecessary complexity while keeping realistic business rules.

---

## 2. Main Tables

The MVP will use the following tables:

- `app_users`
- `restaurant_tables`
- `reservations`

Optional future table:

- `opening_hours`

---

## 3. Table: app_users

Stores admin users who can access the protected admin area.

Customers do not need an account in the MVP.

| Field      | Type     | Required | Description                  |
| ---------- | -------- | -------: | ---------------------------- |
| id         | Long     |      Yes | Primary key                  |
| username   | String   |      Yes | Admin username               |
| email      | String   |      Yes | Admin email                  |
| password   | String   |      Yes | Encrypted password           |
| role       | String   |      Yes | User role, for example ADMIN |
| created_at | DateTime |      Yes | Creation date                |

### Notes

- Passwords must be stored encrypted.
- Only admin users are stored here in the MVP.
- Customer data is stored directly inside the reservation.

---

## 4. Table: restaurant_tables

Stores the physical tables available in the restaurant.

| Field        | Type    | Required | Description                                     |
| ------------ | ------- | -------: | ----------------------------------------------- |
| id           | Long    |      Yes | Primary key                                     |
| table_number | Integer |      Yes | Visible table number                            |
| capacity     | Integer |      Yes | Maximum number of guests                        |
| active       | Boolean |      Yes | Whether the table is available for reservations |

### Business Rules

- Capacity must be greater than 0.
- Table number must be unique.
- Inactive tables cannot receive new reservations.

---

## 5. Table: reservations

Stores customer reservations.

| Field            | Type      | Required | Description                                     |
| ---------------- | --------- | -------: | ----------------------------------------------- |
| id               | Long      |      Yes | Primary key                                     |
| reservation_code | String    |      Yes | Public code used to identify/cancel reservation |
| customer_name    | String    |      Yes | Customer full name                              |
| customer_email   | String    |      Yes | Customer email                                  |
| customer_phone   | String    |      Yes | Customer phone                                  |
| reservation_date | LocalDate |      Yes | Reservation date                                |
| reservation_time | LocalTime |      Yes | Reservation time                                |
| party_size       | Integer   |      Yes | Number of guests                                |
| status           | String    |      Yes | Reservation status                              |
| table_id         | Long      |      Yes | Assigned restaurant table                       |
| created_at       | DateTime  |      Yes | Creation date                                   |
| updated_at       | DateTime  |       No | Last update date                                |

### Reservation Statuses

- PENDING
- CONFIRMED
- CANCELLED
- COMPLETED

### Business Rules

- Reservation date cannot be in the past.
- Reservation time must be inside restaurant opening hours.
- Party size must be greater than 0.
- Party size must fit the assigned table capacity.
- A table cannot have two active reservations at the same date and time.
- Cancelled reservations do not block table availability.

---

## 6. Relationships

### app_users

No required relationship with reservations in the MVP.

Admins manage reservations, but reservations are not owned by admins.

### restaurant_tables → reservations

One restaurant table can have many reservations.

A reservation belongs to one restaurant table.

Relationship:

```text
restaurant_tables 1 ─── * reservations
```

---

## 7. Why Customers Do Not Have Accounts

For the MVP, customers do not need login.

This keeps the project simpler and focuses the complexity on:

- reservation flow
- table availability
- admin security
- backend validation
- REST API integration

Customer information is stored directly inside each reservation.

---

## 8. Optional Future Table: opening_hours

This table can be added later if we want dynamic restaurant schedules.

| Field        | Type      | Required | Description                           |
| ------------ | --------- | -------: | ------------------------------------- |
| id           | Long      |      Yes | Primary key                           |
| day_of_week  | String    |      Yes | MONDAY, TUESDAY, etc.                 |
| opening_time | LocalTime |      Yes | Opening time                          |
| closing_time | LocalTime |      Yes | Closing time                          |
| active       | Boolean   |      Yes | Whether the restaurant opens that day |

For the MVP, opening hours can be handled as fixed business rules in the backend service.

---

## 9. MVP Decision

The MVP will start with only:

- `app_users`
- `restaurant_tables`
- `reservations`

Opening hours will be controlled in backend logic first.

This keeps the database clean and avoids unnecessary complexity during the first version.
