# Содержание

- [Архитектурное описание системы](#архитектурное-описание-системы)
  - [1. Архитектурная диаграмма](#1-архитектурная-диаграмма)
    - [1.1. Сущности](#11-сущности)
    - [1.2. Сервисы](#12-сервисы)
  - [2. Подробная архитектура](#2-подробная-архитектура)
    - [2.1. Frontend](#21-frontend)
      - [Admin Dashboard](#admin-dashboard)
    - [2.2. Backend](#22-backend)
      - [API Gateway](#api-gateway)
      - [Product Service](#product-service)
      - [Marketplace Integration Service](#marketplace-integration-service)
      - [Price Calculation Service](#price-calculation-service)
      - [Parser Service](#parser-service)
      - [Account Management Service](#account-management-service)
    - [2.3. Дополнительные компоненты](#23-дополнительные-компоненты)
  - [3. Процесс работы](#3-процесс-работы)
  - [4. Технологическая диаграмма](#4-технологическая-диаграмма)
    - [4.1. Стек](#41-стек)
    - [4.2. Backend - CRM система](#42-backend---crm-система)
      - [API Endpoints](#api-endpoints)
    - [4.3. Frontend - CRM Dashboard](#43-frontend---crm-dashboard)
      - [Components](#components)

---

# Архитектурное описание системы

## 1. Архитектурная диаграмма

### 1.1. Сущности

- `Product`
- `Marketplace`
- `PriceFormula`
- `LegalEntity`
- `MarketplaceAccount`

### 1.2. Сервисы

- `Product Service`
- `Marketplace Integration Service`
- `Price Calculation Service`
- `Parser Service`
- `Account Management Service`

## 2. Подробная архитектура

### 2.1. Frontend

#### Admin Dashboard

- Управление товарами
- Управление юр. лицами и аккаунтами маркетплейсов
- Управление формулой цены

### 2.2. Backend

#### API Gateway

- Единая точка входа для всех запросов: маршрутизация, аутентификация и авторизация.

#### Product Service

- CRUD для товаров
- Получение списка артикулов с маркетплейсов для парсера

#### Marketplace Integration Service

- Интеграция с маркетплейсами
- Отправка обновленных данных на маркетплейсы

#### Price Calculation Service

- Расчет цен на основе данных и `PriceFormula`

#### Parser Service

- Парсинг данных с магазинов-поставщиков
- Возврат данных в `Product Service`

#### Account Management Service

- Управление юр. лицами
- Управление аккаунтами маркетплейсов

### 2.3. Дополнительные компоненты

- **Database**: Хранение данных о товарах, юридических лицах, аккаунтах маркетплейсов и т.д.
- **Message Broker**: Для асинхронной коммуникации между сервисами, особенно при массовом обновлении данных.
- **Scheduler**: Запуск парсеров по расписанию.
- **Logging & Monitoring System**: Отслеживание ошибок, статусов, а также мониторинг производительности системы.

## 3. Процесс работы
1. `Product Service` у каждого юр. лица собирает артикулы с маркетплейсов (и все отсальные данные нужные для CRUD), и сортирует их по поставщикам. __# TODO: расписать подробнее про систему [артикул_поставщика][наименование_поставщика]__
3. `Scheduler` запускает `Parser Service` для обновления данных.
4. `Parser Service` запрашивает список артикулов у `Product Service` и затем парсит данные у поставщиков.
5. Парсер возвращает обновленные данные в `Product Service`.
6. `Price Calculation Service` рассчитывает новые цены.
7. `Marketplace Integration Service` отправляет обновленные данные на соответствующие маркетплейсы с использованием данных из `Account Management Service`.

## 4. Технологическая диаграмма

### 4.1. Стек

- **Backend**: Python для легкости интеграции с парсингом и гибкости API (есть возможность написать backend на GoLang для производительности).
- **Frontend**: React.js или Vue.js для создания современного интерактивного интерфейса.
- **База данных**: PostgreSQL для надежного и масштабируемого хранения данных.

### 4.2. Backend - CRM система

#### API Endpoints

- `GET /products/<provider>`: Получение списка артикулов для определенного поставщика.
- `POST /products/update`: Прием обновленных данных от парсеров.

### 4.3. Frontend - CRM Dashboard

#### Components

- `Product List`: Отображает список товаров, их статус и последние обновления.
- `Price Formula Editor`: Интерфейс для настройки параметров формулы расчета.
- `Update Logs`: Секция для просмотра логов обновлений и ошибок.
- `Settings`: Настройки интеграции с маркетплейсами, учетные записи, API ключи и т.д.

---
