# Business App Architecture - Quick Reference

## Overview
Restaurant operations management platform using **Feature-First Clean Architecture** with **Riverpod** for state management. Supports multi-tenant operations, offline-first capabilities, and real-time updates.

## Technology Stack
- **FVM**: Flutter version management tool
- **Flutter**: 3.38.5 (managed by FVM)
- **Dart**: 3.10.4
- **State Management**: Riverpod 2.6.1
- **Routing**: GoRouter 14.8.1
- **Networking**: Dio 5.7.0
- **Error Handling**: Dartz 0.10.1
- **Local Storage**: Hive 2.2.3
- **Secure Storage**: Flutter Secure Storage 9.2.2
- **Code Generation**: JSON Serializable 6.9.4
- **Linting**: Very Good Analysis 6.0.0

## Key Features
- **Reservation Management**: Create, check-in, lifecycle tracking
- **Table Management**: Facility areas, status tracking, assignments
- **Order Management**: Dine-in, takeaway, delivery, pre-orders
- **Walk-in Handling**: On-demand customer processing
- **Business Switching**: Multi-business support per user
- **Analytics Dashboard**: Performance insights for owners
- **Real-time Notifications**: Operational alerts
- **Offline-First**: Local storage and sync queue

## Architecture Layers

### 1. Presentation Layer
- **Screens**: Flutter widgets for user interaction
- **ViewModels**: State management with Riverpod
- **Navigation**: GoRouter with typed routes
- **UI Customization**: Dynamic theming support

### 2. Domain Layer
- **Entities**: Business objects (Reservation, Table, Order, Business, User)
- **Use Cases**: Business logic orchestrators
- **Repository Interfaces**: Data operation contracts
- **Failure Types**: NetworkFailure, ServerFailure, CacheFailure, ValidationFailure

### 3. Data Layer
- **Models**: JSON serializable DTOs
- **Data Sources**: Remote (API) and local (Hive) providers
- **Repository Implementations**: Interface implementations
- **Mappers**: Entity ↔ Model conversions

### 4. Core Layer
- **Networking**: Dio client with authentication interceptors
- **Storage**: Secure storage and Hive helpers
- **Offline Queue**: Sync engine for offline operations
- **Notifications**: In-app and push notification handlers
- **Constants**: Multi-tenant configuration

## Project Structure
```
lib/
├── app/                    # App setup, router, business config
├── core/                   # Shared utilities, networking, storage, sync
├── features/               # Business feature modules
│   ├── auth/               # Authentication
│   ├── business_switch/    # Multi-business switching
│   ├── dashboard/          # Analytics dashboard
│   ├── reservations/       # Reservation management
│   ├── tables/             # Table management
│   ├── orders/             # Order processing
│   ├── staff/              # Staff management
│   ├── analytics/          # Business insights
│   └── profile/            # User profile
└── shared/                 # Reusable widgets, extensions
```

## Operational Workflows

### Reservation Lifecycle
```
PENDING → CONFIRMED → CHECKEDIN → COMPLETED
                     → CANCELLED
                     → NOSHOW
                     → EXPIRED
```

### Order Types & Lifecycle
- **Types**: Dine-In, Takeaway, Delivery, Pre-Order
- **Lifecycle**: Pending → Preparing → Ready → Served → Completed/Cancelled

### Table Status
- Available, Reserved, Occupied, Cleaning, Closed

## Multi-Tenant Architecture
- Business-scoped data isolation
- API requests include business identifiers
- Local storage partitioned by business
- Seamless business switching

## Offline Strategy
- **Local Storage**: Hive for reservations, tables, orders, businesses
- **Write Queue**: Queued actions during offline (create reservation, check-in, orders)
- **Sync Process**: Sequential processing on connectivity restore
- **Conflict Resolution**: Server priority with user notification

## Real-Time Updates
- **Events**: New orders, reservation check-ins, table updates, service requests
- **Implementation**: WebSocket or short-interval polling
- **Notifications**: In-app alerts and push notifications

## Security Architecture
- Authentication methods: email/phone + OTP, email/phone + password
- Token-based authentication with refresh tokens
- Secure storage for sensitive data
- Role-based access control (Business Owner, Staff)
- Tenant-level data isolation

## Development Commands
- Setup: `fvm flutter pub get`
- Run: `fvm flutter run`
- Test: `fvm flutter test`
- Build: `fvm flutter build apk` / `fvm flutter build ios`

## Best Practices
- Follow Clean Architecture layer boundaries
- Implement offline-first patterns
- Handle multi-tenant data properly
- Use Either for error handling
- Test offline and sync scenarios
- Document real-time behavior
- Ensure UI customization support