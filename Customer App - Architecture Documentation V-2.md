# Customer App - Architecture Documentation V-2

## Revision
- Version: 2
- Date: 2026-04-10
- Based on: `Customer App - Architecture Documentation V-1.md`

## Overview
The Customer App is built with a **Feature-First Pragmatic Clean Architecture** using **Flutter + Dart**. The architecture is designed to be:

- **Separation of Concerns**: Each layer has a single responsibility
- **Testable**: Business logic is isolated from UI and infrastructure
- **Maintainable**: Features are self-contained and easy to navigate
- **Scalable**: New functionality can be added with minimal impact
- **Resilient**: App behavior is predictable across state changes

### Core goals
- Keep the **Domain layer pure Dart** and framework independent
- Use **Riverpod** as the DI and state management backbone
- Organize code by **feature**, not by technical layer
- Use **MVVM** in the presentation layer
- Use **Either** for predictable error handling

- **Separation of Concerns**: Each layer has a single responsibility
- **Testable**: Business logic is isolated from UI and infrastructure
- **Maintainable**: Features are self-contained and easy to navigate
- **Scalable**: New functionality can be added with minimal impact
- **Resilient**: App behavior is predictable across state changes

### Core goals
- Keep the **Domain layer pure Dart** and framework independent
- Use **Riverpod** as the DI and state management backbone
- Organize code by **feature**, not by technical layer
- Use **MVVM** in the presentation layer
- Use **Either** for predictable error handling

---

## Technology Stack

### Core
- FVM: Flutter version management tool
- Flutter SDK: `3.38.5` (managed by FVM)
- Dart: `3.10.4`

### State Management & DI
- Riverpod: `2.6.1`
- Flutter Riverpod: `2.6.1`

### Routing
- GoRouter: `14.8.1`

### Networking
- Dio: `5.7.0`
- Pretty Dio Logger: `1.4.0`

### Functional / Error Handling
- Dartz: `0.10.1`

### Storage
- Flutter Secure Storage: `9.2.2`
- Hive: `2.2.3`
- hive_flutter: `1.1.0`

### Development Tools
- JSON Serializable: `6.9.4`
- Very Good Analysis: `6.0.0`

---

## Architecture Principles

### 1. Clean Architecture (Pragmatic)

The app is organized into three main layer categories with dependencies pointing inward.

```text
Presentation → Domain → Data
```

- `Presentation`: UI, state, navigation
- `Domain`: business rules, entities, use cases, repository interfaces
- `Data`: remote/local data sources, models, repository implementations

### 2. Feature-First Organization

Code is grouped by feature so each feature contains its own presentation, domain, and data implementation.

Example structure:

```text
lib/features/auth/
lib/features/home/
lib/features/navigation/
```

Benefits:
- Easier feature ownership
- Reduced merge conflicts
- Faster onboarding
- Better modularity

### 3. MVVM Pattern

Presentation uses MVVM with View and ViewModel co-located inside the feature folder.

- View: Flutter widgets, UI rendering
- ViewModel: state and business orchestration
- Model/Entity: domain data structures

### 4. SOLID Principles

- Single Responsibility: each class does one thing
- Open/Closed: extend without modifying existing code
- Liskov: substitute implementations safely
- Interface Segregation: small, focused contracts
- Dependency Inversion: high-level logic depends on abstractions

---

## Project Structure

```text
lib/
├── app/
│   ├── app.dart
│   ├── env_config.dart
│   └── router/
│       ├── app_router.dart
│       └── route_observer.dart
├── core/
│   ├── analytics/
│   ├── constants/
│   ├── errors/
│   ├── network/
│   ├── storage/
│   ├── theme/
│   └── utils/
├── features/
│   ├── auth/
│   ├── home/
│   ├── navigation/
│   └── splash/
├── shared/
│   ├── extensions/
│   └── widgets/
└── main.dart
```

### Core folder responsibilities
- `app/`: app entrypoint, environment config, router
- `core/`: shared behavior, constants, networking, storage, utilities
- `features/`: feature modules by domain area
- `shared/`: reusable widgets and app-wide extensions

---

## Layer Architecture

### Presentation Layer

Responsibility: UI rendering, state handling, navigation.

Components:
- Views: widgets that display UI
- ViewModels: state holders using `StateNotifier`
- State models: immutable state objects

### Domain Layer

Responsibility: business rules and policies.

Components:
- Entities: pure Dart business objects
- Use cases: business operations
- Repository interfaces: contracts for data sources
- Enums: domain constants

### Data Layer

Responsibility: remote/local data management.

Components:
- Models: JSON-serializable objects
- Data sources: remote and local providers
- Repository implementations: map data to domain contracts

---

## Data Flow

Example: user login flow.

1. User taps login button in View
2. ViewModel receives action and updates state
3. ViewModel calls Use Case
4. Use Case calls repository interface
5. Repository implementation calls remote data source
6. Remote data source uses Dio to call API
7. Response is converted into models/entities
8. Result propagates back to ViewModel and UI updates

---

## Authentication System

### Token system

The app uses a two-token approach:

- `CLIENT Token`: app-level token for public endpoints and startup calls
- `USER Token`: user-level token after successful login

### Storage

- Both tokens are stored in `FlutterSecureStorage`
- `CLIENT Token` is refreshed at startup or on `410`
- `USER Token` is refreshed on `401` when implemented

### Request policy

`Dio` interceptor ensures token priority:
- `USER Token` if available
- fallback to `CLIENT Token`

### OTP login flow

1. `POST /api/Identity/Account/Login` with phone/email to request OTP
2. Server returns OTP send status
3. `POST /api/Identity/Account/Login` with OTP to verify and sign in
4. Server returns `token`, `refreshToken`, and user data

---

## State Management

Riverpod is used for both dependency injection and state management.

### Provider usage

- `Provider`: singleton or simple value
- `StateNotifierProvider`: ViewModel state
- `ref.watch()`: rebuild when state changes
- `ref.read()`: one-time access
- `ref.listen()`: side effects

### ViewModel pattern

- Each screen has a `StateNotifier` or `StateNotifierProvider`
- ViewModels encapsulate state and actions
- Views observe ViewModel state via Riverpod

---

## Dependency Injection

Riverpod is the DI container.

Typical dependency graph:

1. `dioClientProvider`
2. `authRemoteDataSourceProvider`
3. `authRepositoryProvider`
4. `loginViewModelProvider`

All higher-level layers depend on abstractions, not concrete implementations.

---

## Error Handling

The app uses `Either<Failure, Success>` instead of exceptions.

### Failure types

- `NetworkFailure`
- `ServerFailure`
- `CacheFailure`
- `ValidationFailure`
- `UnknownFailure`

### Pattern

- Repository returns `Either<Failure, T>`
- ViewModel handles failure by updating UI state
- Use Cases validate and propagate business errors

---

## Network Layer

### Dio configuration

- Base URL and timeouts are configured in `lib/core/network/dio_client.dart`
- Auth interceptor injects bearer tokens
- Pretty logger enabled in debug mode only

### API response wrapper

Expected response shape:

```json
{
  "isError": false,
  "result": {},
  "error": {
    "detail": "...",
    "status": 400
  }
}
```

Repositories must validate `isError` and map the result cleanly.

---

## Storage Layer

### Flutter Secure Storage

Use for sensitive data only:
- `CLIENT Token`
- `USER Token`
- `Refresh Token`
- `User ID`
- Any secure credential

### Hive

Use Hive for:
- API cache
- User preferences
- Offline data
- Non-sensitive state

### Storage rules

- Secure secrets in `SecureStorage`
- Non-sensitive cached objects in Hive
- Do not store access tokens in plain local storage

---

## Feature Implementation Guide

### Steps to add a new feature

1. Create feature folder under `lib/features/`
2. Add `domain`, `data`, and `presentation` subfolders
3. Define entity and repository interface in Domain
4. Implement data models and data source in Data
5. Implement repository and providers in Data
6. Build ViewModel and screen View in Presentation
7. Register route in `app/router/app_router.dart`
8. Add tests for use cases, repositories, and widgets

### Example structure

```text
lib/features/profile/
├── data/
│   ├── datasources/
│   ├── models/
│   └── repositories/
├── domain/
│   ├── entities/
│   ├── interfaces/
│   └── usecases/
└── presentation/
    ├── profile_screen/
    └── widgets/
```

---

## Testing Strategy

### Unit tests
- Domain use cases
- Repository logic
- Helper utilities

### Widget tests
- Screen rendering
- provider state changes
- UI interactions

### Integration tests
- End-to-end flows such as login, profile update, and navigation

---

## Naming Conventions

### Files
- `snake_case`
- suffix with type: `_view.dart`, `_viewmodel.dart`, `_model.dart`, `_entity.dart`, `_repository.dart`

### Classes
- `PascalCase`
- view classes end in `View`
- viewmodels end in `ViewModel`
- models end in `Model`
- entities end in `Entity`

### Providers
- `camelCase`
- suffix with `Provider`

### Variables
- `camelCase`
- private variables start with `_`
- booleans start with `is`, `has`, `can`, `should`

### Constants
- `lowerCamelCase`
- use `static const`

---

## Best Practices

- Keep UI code thin and declarative
- Keep business logic inside use cases and repositories
- Use dependency injection for all external dependencies
- Avoid `setState` for feature logic; use Riverpod state
- Prefer immutable state objects
- Use `Either` for error handling, not exceptions
- Keep feature code self-contained

---

## Common Patterns

- `View` interacts with `ViewModel`
- `ViewModel` calls `UseCase`
- `UseCase` uses `Repository Interface`
- `Repository Implementation` uses `DataSource`
- `DataSource` uses `Dio` or local storage
