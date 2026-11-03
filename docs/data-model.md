# Data Model

Request and approval tracking app. Tables are created in dependency order.

## users

```sql
CREATE TABLE users (
  id          SERIAL PRIMARY KEY,
  full_name   VARCHAR(100) NOT NULL,
  email       VARCHAR(255) NOT NULL UNIQUE,
  role        VARCHAR(20)  NOT NULL
              CHECK (role IN ('employee', 'manager', 'admin')),
  created_at  TIMESTAMPTZ  NOT NULL DEFAULT now()
);
```

## categories

```sql
CREATE TABLE categories (
  id          SERIAL PRIMARY KEY,
  name        VARCHAR(100) NOT NULL UNIQUE,
  is_active   BOOLEAN      NOT NULL DEFAULT true,
  created_at  TIMESTAMPTZ  NOT NULL DEFAULT now()
);
```

## requests

```sql
CREATE TABLE requests (
  id           SERIAL PRIMARY KEY,
  title        VARCHAR(150) NOT NULL,
  description  TEXT,
  category_id  INTEGER NOT NULL REFERENCES categories(id),
  created_by   INTEGER NOT NULL REFERENCES users(id),
  status       VARCHAR(20)  NOT NULL DEFAULT 'pending'
               CHECK (status IN ('pending', 'approved', 'rejected')),
  created_at   TIMESTAMPTZ  NOT NULL DEFAULT now()
);
```

## Design notes

- `users` and `categories` must exist before `requests` (foreign keys).
- `categories.is_active`: categories are deactivated, not deleted (soft delete), so existing requests stay valid.
- Future (v2): `categories.parent_id` for hierarchical categories.
- Future: `approval_steps` and `request_logs` tables.