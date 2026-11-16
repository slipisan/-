# 🚗 car-booking-db

> Учебный проект: проектирование и реализация реляционной базы данных для системы бронирования автомобилей.

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-DDL%20%2F%20DML-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![DBeaver](https://img.shields.io/badge/DBeaver-client-382923?style=for-the-badge&logo=dbeaver&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)

---

## 📋 Содержание

- [О проекте](#-о-проекте)
- [Стек](#-стек)
- [Выбор типа БД](#-выбор-типа-бд)
- [Схема данных](#-схема-данных)
- [Структура репозитория](#-структура-репозитория)
- [Быстрый старт](#-быстрый-старт)
- [DDL: создание таблиц](#-ddl-создание-таблиц)
- [Тестовые данные](#-тестовые-данные)
- [Примеры запросов](#-примеры-запросов)
- [Резервное копирование](#-резервное-копирование)
- [Основные команды](#-основные-команды)
- [Результат](#-результат)
- [Вывод](#-вывод)

---

## 📖 О проекте

База данных для сервиса аренды и бронирования автомобилей. Реализует:

- учёт автопарка и категорий с тарифами;
- регистрацию клиентов с водительскими правами;
- оформление броней с расчётом стоимости;
- **запрет двойного бронирования** одного авто на пересекающиеся даты — на уровне СУБД;
- справочник статусов и учёт платежей.

---

## 🛠 Стек

| Инструмент | Версия / Назначение |
|------------|---------------------|
| **PostgreSQL** | 16.x — сервер БД |
| **pgAdmin / DBeaver** | клиент для администрирования |
| **psql** | консольная утилита |
| **ОС** | Windows 10/11, Linux, macOS |

---

## 🧭 Выбор типа БД

Предметная область «бронирование авто» — строго структурированные данные со связями «клиент → бронь → автомобиль» и жёсткими требованиями к целостности. Поэтому выбрана **реляционная модель**.

| Тип БД | Пример СУБД | Подходит? | Почему |
|--------|-------------|-----------|--------|
| **Реляционная** | PostgreSQL, MySQL | ✅ **Выбрана** | ACID, внешние ключи, ограничения целостности |
| Документная | MongoDB | ⚠️ Частично | Нет жёстких связей, риск «битых» ссылок |
| Ключ-значение | Redis | ❌ | Только как кэш сессий |
| Графовая | Neo4j | ❌ | Избыточна для простых связей |

**Почему именно PostgreSQL:** поддержка `daterange` и `EXCLUDE`-ограничений — позволяет на уровне БД запретить пересечение броней одного автомобиля.

---

## 🗂 Схема данных

```mermaid
erDiagram
    CAR_CATEGORIES ||--o{ CARS : "содержит"
    CARS ||--o{ BOOKINGS : "бронируется"
    CLIENTS ||--o{ BOOKINGS : "оформляет"
    BOOKING_STATUSES ||--o{ BOOKINGS : "определяет"
    BOOKINGS ||--o| PAYMENTS : "оплачивается"

    CARS {
        int id PK
        int category_id FK
        varchar reg_number
        numeric price_per_day
    }
    CLIENTS {
        int id PK
        varchar full_name
        varchar driver_license
        varchar phone
    }
    BOOKINGS {
        int id PK
        int car_id FK
        int client_id FK
        date start_date
        date end_date
        int status_id FK
        numeric total_price
    }
📁 Структура репозитория
car-booking-db/
├── README.md
├── sql/
│   ├── 01_create_database.sql
│   ├── 02_schema.sql
│   ├── 03_constraints.sql
│   ├── 04_seed.sql
│   └── 05_queries.sql
└── backup/
    └── car_booking.dump
