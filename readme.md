# E-Commerce Marketplace API

> Multi-vendor product marketplace with membership-based dynamic pricing —
> sellers list products, buyers get tiered discounts based on loyalty level.

[![Python](https://img.shields.io/badge/Python-3.11-blue)]()
[![Django](https://img.shields.io/badge/Django-5.x-green)]()
[![DRF](https://img.shields.io/badge/DRF-3.x-red)]()
[![PostgreSQL](https://img.shields.io/badge/DB-PostgreSQL-blue)]()
[![Docker](https://img.shields.io/badge/Docker-ready-blue)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-green)]()

---

## Problem

Marketplaces that treat all buyers equally leave revenue on the table.
Tiered discounts reward loyal customers — gold/silver/bronze members get
50/25/10% off, calculated live at cart level.

---

## What's Built

- **Tiered pricing** — gold/silver/bronze discounts (50/25/10%) via `get_total_price()`
- **Two-level catalog** — Category → SubCategory → Product with icon upload
- **Product detail** — multi-image gallery, video, article number, originality flag, avg rating
- **Cart with live totals** — auto-created on first access, quantity management, discount applied
- **Favorites** — personal wishlist per authenticated user
- **Reviews** — star rating (1–5) with author attribution
- **Filtering** — min/max price, article number, category, search, ordering
- **JWT + OAuth2** — login via GitHub or Google; token blacklisting on logout
- **Bilingual content** — EN/RU on 4 models via django-modeltranslation
- **Swagger docs** via drf-spectacular

---

## Endpoints

| Method | URL | Description |
|--------|-----|-------------|
| POST | `/register/` | Register |
| POST | `/login/` | JWT login |
| POST | `/logout/` | Blacklist token |
| GET/POST | `/products/` | Product list / create |
| GET | `/products/<pk>/` | Product detail |
| PUT/DELETE | `/products/<pk>/edit/` | Owner: update / delete |
| GET | `/categories/` | Category list |
| GET | `/categories/<pk>/` | Category with subcategories |
| GET | `/sub-categories/` | Subcategory list |
| GET | `/cart/` | Cart with totals |
| GET/POST | `/cart-items/` | Add to cart |
| PUT/DELETE | `/cart-items/<pk>/` | Update / remove |
| GET | `/favorites/` | Favorites |
| GET/POST | `/favorite-items/` | Add to favorites |
| DELETE | `/favorite-items/<pk>/` | Remove from favorites |
| GET/POST/PUT/DELETE | `/reviews/` | Reviews CRUD |
| GET | `/api/docs/` | Swagger UI |

**Filter example:**
```bash
GET /products/?min_price=500&max_price=3000&ordering=-product_price&search=shoes
```

---

## Tech Stack

| Category | Technology |
|----------|------------|
| Language | Python 3.11 |
| Framework | Django 5, Django REST Framework |
| Auth | SimpleJWT (blacklist) + django-allauth |
| OAuth2 | GitHub, Google |
| Database | PostgreSQL (prod) / SQLite (dev) |
| i18n | django-modeltranslation (EN/RU) |
| Docs | drf-spectacular / Swagger UI |
| Deploy | Docker Compose, Gunicorn, Nginx |

---

## Project Structure
```
drf_wildberries/
├── .gitignore
├── readme.md
└── drf_wildberries/
    ├── db.sqlite3
    ├── manage.py
    ├── reg.txt
    ├── media/
    │   └── product_image/
    ├── mysite/
    │   ├── __init__.py
    │   ├── asgi.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    └── wildberries/
        ├── __init__.py
        ├── admin.py
        ├── apps.py
        ├── filters.py
        ├── models.py
        ├── serializers.py
        ├── tests.py
        ├── translation.py
        ├── urls.py
        ├── views.py
        └── migrations/
            ├── __init__.py
            └── 0001_initial.py
```
---

## Key Decisions

- **Discount at model layer** — `CartItem.get_total_price()` reads
  `membership_status` and applies multiplier; logic centralized in one
  method, never duplicated across views or serializers
- **`get_or_create` for Cart** — auto-created on first access, no
  separate "create cart" endpoint needed, no orphaned records
- **Scoped querysets** — Cart, Favorite, and their items filtered by
  `request.user`; users can never read each other's data without
  object-level permission overhead
- **List / Detail / Create serializer split** — `ProductListSerializer`
  returns lightweight data for catalog; `ProductDetailSerializer` includes
  nested reviews and images; `ProductSerializer` handles write operations

---

## How to Run

**Local:**
```bash
git clone https://github.com/your-username/marketplace-api
cd marketplace-api
pip install -r requirements.txt
```

```bash
# Add SECRET_KEY, OAuth keys to .env
python manage.py makemigrations && python manage.py migrate
python manage.py runserver
```

**Docker:**
```bash
docker-compose up --build
```

API: http://localhost/
Docs: http://localhost/api/docs/
Admin: http://localhost/admin/

---
