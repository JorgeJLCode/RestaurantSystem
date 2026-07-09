# PRD — Restaurant Reservation System

## 1. Product Overview

Restaurant Reservation System is a full-stack web application that allows customers to check table availability and create restaurant reservations online.

The application also provides an admin area where restaurant staff can manage reservations, tables, and reservation statuses.

This project is designed as a professional portfolio MVP focused on clean backend architecture, REST API development, business validation, and React integration.

---

## 2. Main Goal

Build a realistic restaurant reservation platform where:

- Customers can create reservations without needing an account.
- Admin users can manage reservations securely.
- The backend validates real business rules.
- The frontend provides a simple and professional user experience.

---

## 3. Target Users

### Customer

A person who wants to book a table at the restaurant.

The customer can:

- Check availability.
- Create a reservation.
- Cancel a reservation.
- See reservation confirmation.

### Admin

A restaurant employee who manages reservations.

The admin can:

- Log in securely.
- View all reservations.
- Confirm reservations.
- Cancel reservations.
- Manage restaurant tables.

---

## 4. MVP Features

### Customer Features

- View restaurant landing page.
- Search availability by:
  - date
  - time
  - number of guests

- Create a reservation.
- Receive a reservation confirmation.
- Cancel a reservation.

### Admin Features

- Admin login.
- View all reservations.
- Filter reservations by status.
- Confirm pending reservations.
- Cancel reservations.
- View restaurant tables.
- Create and edit tables.

---

## 5. Reservation Statuses

A reservation can have the following statuses:

- PENDING
- CONFIRMED
- CANCELLED
- COMPLETED

For the MVP, the most important statuses are:

- PENDING
- CONFIRMED
- CANCELLED

---

## 6. Business Rules

The system must enforce the following rules:

- A reservation cannot be created for a past date.
- A reservation cannot be created outside restaurant opening hours.
- A reservation must have at least 1 guest.
- A reservation must not exceed the maximum supported table capacity.
- A table cannot be double-booked for the same date and time slot.
- Cancelled reservations should not count as active reservations.
- Only admins can confirm or cancel reservations from the admin panel.

---

## 7. Basic Validation Rules

### Customer Reservation Form

Required fields:

- Customer name
- Customer email
- Customer phone
- Reservation date
- Reservation time
- Number of guests

Validation:

- Email must have valid format.
- Phone cannot be empty.
- Date cannot be in the past.
- Number of guests must be greater than 0.
- Number of guests must fit an available table.

---

## 8. MVP Out of Scope

The following features are intentionally excluded from the MVP:

- Online payments
- Email notifications
- Password recovery
- Customer accounts
- Advanced calendar management
- Table map visualization
- Employee management
- Menu management
- AI features
- Microservices
- Docker deployment

These features may be considered future improvements.

---

## 9. Success Criteria

The MVP will be considered complete when:

- Customers can create valid reservations.
- The backend prevents invalid reservations.
- Admin can log in securely.
- Admin can manage reservation statuses.
- The API is tested with Postman.
- React frontend works first with mock data.
- React frontend is later integrated with the backend API.
- The project has a professional README and clear documentation.

---

## 10. Portfolio Value

This project demonstrates:

- Spring Boot REST API development.
- Database design with relational entities.
- Business rule validation.
- Role-based security.
- JWT authentication for admin access.
- React and TypeScript frontend development.
- Frontend-backend integration.
- Professional project structure.
- Realistic full-stack workflow.
