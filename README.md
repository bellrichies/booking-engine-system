````markdown
# Booking Engine System

A production-grade backend reservation engine for managing property availability, date-based bookings, and conflict-free reservations.

This project powers the core booking workflow for hospitality-style platforms such as apartment rentals, shortlet systems, hotel rooms, and workspace reservations.

## Features

- Property availability management
- Booking creation and lifecycle handling
- Date-range validation
- Double-booking prevention
- Transaction-safe booking operations
- Booking pricing and fee breakdown
- Availability rules and manual blocking
- Payment tracking
- Booking status history
- Admin booking controls
- Audit logging

---

## Use Cases

This system can be used for:

- apartment and shortlet booking platforms
- hotel room reservation engines
- villa booking systems
- workspace and meeting room reservations
- hospitality marketplaces

---

## Core Goals

The project is designed to ensure:

- accurate availability checks
- prevention of overlapping reservations
- safe concurrent booking handling
- traceable booking lifecycle transitions
- scalable booking domain design

---

## Tech Stack

- PHP 8.3+
- Custom MVC architecture
- MySQL 8+
- PDO
- Composer with PSR-4 autoloading
- Redis for caching and locks
- Docker / Docker Compose
- PHPMailer for notifications

---

## Project Structure

```text
booking-engine-system/
├── app/
│   ├── Config/
│   ├── Controllers/
│   ├── Core/
│   ├── DTOs/
│   ├── Middleware/
│   ├── Models/
│   ├── Repositories/
│   ├── Services/
│   ├── Policies/
│   ├── Jobs/
│   ├── Events/
│   └── Helpers/
├── bootstrap/
├── config/
├── database/
│   ├── migrations/
│   ├── seeds/
│   └── factories/
├── public/
├── routes/
├── storage/
├── tests/
├── docs/
├── .env.example
├── composer.json
├── docker-compose.yml
└── README.md
````

---

## Main Modules

### Auth

Handles user authentication and authorization.

### Properties

Manages property records and listing configuration.

### Property Units

Represents reservable inventory such as rooms, apartments, or desks.

### Availability

Controls recurring rules, blocked dates, maintenance windows, and pricing overrides.

### Bookings

Handles reservation creation, updates, lifecycle transitions, and conflict checking.

### Payments

Tracks payment status and integrates with payment workflows.

### Admin

Provides moderation tools, booking overrides, and reporting support.

### Audit

Logs sensitive business actions for traceability.

---

## Booking Lifecycle

A booking typically moves through the following statuses:

* `pending`
* `awaiting_payment`
* `confirmed`
* `checked_in`
* `checked_out`
* `completed`
* `cancelled`
* `expired`
* `rejected`

---

## Double-Booking Prevention

The system prevents overlapping reservations using interval-based conflict detection.

A booking conflicts when:

```text
existing.check_in < requested.check_out
AND
existing.check_out > requested.check_in
```

Blocking statuses include:

* `pending`
* `awaiting_payment`
* `confirmed`
* `checked_in`

The booking creation flow runs inside a database transaction to guarantee consistency.

---

## API Endpoints

### Auth

* `POST /api/v1/auth/register`
* `POST /api/v1/auth/login`
* `POST /api/v1/auth/logout`
* `GET /api/v1/auth/me`

### Properties

* `GET /api/v1/properties`
* `POST /api/v1/properties`
* `GET /api/v1/properties/{id}`
* `PUT /api/v1/properties/{id}`
* `PATCH /api/v1/properties/{id}/status`

### Units

* `GET /api/v1/properties/{propertyId}/units`
* `POST /api/v1/properties/{propertyId}/units`
* `GET /api/v1/units/{id}`
* `PUT /api/v1/units/{id}`

### Availability

* `GET /api/v1/units/{id}/availability`
* `POST /api/v1/units/{id}/availability/rules`
* `POST /api/v1/units/{id}/availability/exceptions`

### Booking

* `POST /api/v1/search/availability`
* `POST /api/v1/bookings/quote`
* `POST /api/v1/bookings`
* `GET /api/v1/bookings/{id}`
* `POST /api/v1/bookings/{id}/confirm`
* `POST /api/v1/bookings/{id}/cancel`
* `POST /api/v1/bookings/{id}/check-in`
* `POST /api/v1/bookings/{id}/check-out`

### Payments

* `POST /api/v1/bookings/{id}/payments/initiate`
* `POST /api/v1/payments/webhook`

### Admin

* `GET /api/v1/admin/bookings`
* `POST /api/v1/admin/units/{id}/block`

---

## Database Schema

Core tables:

* `users`
* `properties`
* `property_units`
* `availability_rules`
* `availability_exceptions`
* `bookings`
* `booking_guests`
* `booking_price_breakdowns`
* `payments`
* `booking_status_histories`
* `audit_logs`

---

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/yourusername/booking-engine-system.git
cd booking-engine-system
```

### 2. Install dependencies

```bash
composer install
```

### 3. Copy environment file

```bash
cp .env.example .env
```

### 4. Configure environment

Set the following in `.env`:

```env
APP_NAME=BookingEngineSystem
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=booking_engine
DB_USERNAME=root
DB_PASSWORD=

REDIS_HOST=127.0.0.1
REDIS_PORT=6379
```

### 5. Run database migrations

```bash
php cli migrate
```

### 6. Seed demo data

```bash
php cli db:seed
```

### 7. Start development server

```bash
php -S localhost:8000 -t public
```

---

## Example Booking Request

```json
{
  "property_unit_id": 7,
  "check_in_date": "2026-05-10",
  "check_out_date": "2026-05-14",
  "adults": 2,
  "children": 1,
  "guest_full_name": "John Doe",
  "guest_email": "john@example.com",
  "guest_phone": "+2348000000000",
  "special_requests": "Late check-in preferred"
}
```

---

## Example Conflict Response

```json
{
  "status": "error",
  "message": "Selected dates are not available.",
  "code": "BOOKING_CONFLICT"
}
```

---

## Testing

Run unit and feature tests:

```bash
vendor/bin/phpunit
```

Focus areas:

* overlap detection
* blocked-date validation
* pricing calculation
* state transitions
* concurrency handling

---

## Production Considerations

* enforce row-level locking during booking creation
* add Redis distributed locks for high concurrency
* expire unpaid bookings automatically
* verify payment webhooks with signatures
* log all admin overrides
* keep price snapshots on bookings
* use background jobs for notifications and cleanup

---

## Roadmap

* coupon support
* calendar sync
* multi-currency pricing
* channel integrations
* deposits and partial payments
* reporting dashboard
* analytics and occupancy insights

