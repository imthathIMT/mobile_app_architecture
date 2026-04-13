# Business App - Developer Guide

## Purpose
This guide helps developers understand the Business Mobile Application architecture, set up the workspace, and follow consistent development practices for the restaurant operations management platform using Flutter with Clean Architecture and Riverpod.

## Overview
The Business Mobile Application is a restaurant operations management platform that enables businesses to manage reservations, tables, orders, and staff operations from mobile devices. It supports real-time operations, offline-first capabilities, and multi-tenant architecture.

## Getting Started

### Prerequisites
- FVM installed and configured for the project
- Flutter SDK `3.38.5` managed by FVM
- Dart `3.10.4`
- Optional: Android Studio / VS Code
- Git for source control

### Local Setup
1. Clone the repository to your machine.
2. Run `fvm flutter pub get` from the project root.
3. Launch the app with `fvm flutter run`.
4. Open the app in your IDE and confirm the project builds successfully.

### Environment Configuration
- Store runtime values in `lib/app/env_config.dart`
- Keep secrets out of source control
- Validate base API URLs, business identifiers, and environment keys before running
- Configure multi-tenant settings for business switching

## Technology Stack
- **Framework**: Flutter 3.38.5 (managed by FVM)
- **Language**: Dart 3.10.4
- **State Management**: Riverpod 2.6.1
- **Routing**: GoRouter 14.8.1
- **Networking**: Dio 5.7.0
- **Error Handling**: Dartz 0.10.1
- **Local Storage**: Hive 2.2.3
- **Secure Storage**: Flutter Secure Storage 9.2.2
- **Code Generation**: JSON Serializable 6.9.4
- **Linting**: Very Good Analysis 6.0.0

## Project Layout

### Folder Responsibilities
- `app/`: App entry point, router, environment setup, business switching logic
- `core/`: Shared constants, networking, storage, utilities, offline queue, sync engine
- `features/`: Feature modules (auth, business_switch, dashboard, reservations, tables, orders, staff, analytics, profile)
- `shared/`: Reusable widgets, extensions, notification handlers
- `main.dart`: App bootstrap with multi-tenant initialization

### Feature Structure
Each feature follows Clean Architecture:
- `domain/`: Entities, use cases, repository interfaces
- `data/`: Repository implementations, data sources, models
- `presentation/`: Screens, viewmodels, widgets

Example Business App features:
```text
lib/features/reservations/
├── domain/
│   ├── entities/
│   ├── usecases/
│   └── repositories/
├── data/
│   ├── models/
│   ├── datasources/
│   └── repositories/
└── presentation/
    ├── screens/
    ├── viewmodels/
    └── widgets/
```

## Development Workflow

### Feature Development
1. Create feature folder under `lib/features/`
2. Implement domain layer (entities, use cases, interfaces)
3. Implement data layer (repositories, data sources, models)
4. Implement presentation layer (screens, viewmodels, widgets)
5. Add routing in GoRouter configuration
6. Implement offline support if needed
7. Add unit and widget tests

### Offline-First Development
- Use Hive for local data storage
- Implement offline queue for write operations
- Handle sync conflicts with server priority
- Test offline scenarios thoroughly

### Real-Time Updates
- Implement WebSocket or polling for real-time data
- Handle notifications for operational events
- Update local storage upon receiving updates

### Multi-Tenant Considerations
- Include business ID in all API requests
- Isolate data per business in local storage
- Handle business switching in state management

## Coding Standards

### Naming Conventions
- Files: `snake_case`
- Classes: `PascalCase`
- Providers: `camelCaseProvider`
- Variables: `camelCase`
- Constants: `lowerCamelCase`
- Enums: `PascalCase` values

### File Suffixes
- `_screen.dart` for main screens
- `_viewmodel.dart` for state controllers
- `_model.dart` for data models
- `_entity.dart` for domain entities
- `_repository.dart` for repository classes
- `_datasource.dart` for data sources

## Testing Strategy

### Unit Testing
- Test use cases, repositories, utilities
- Use mock data sources for isolation
- Run with `fvm flutter test`

### Widget Testing
- Test UI components and interactions
- Use Riverpod test utilities
- Verify state changes and UI updates

### Integration Testing
- Test complete user flows (reservation to order completion)
- Test offline-online sync scenarios
- Test multi-business switching

## Deployment
- Use FVM for consistent Flutter version
- Configure build flavors for different environments
- Implement code push for updates if needed
- Test on multiple devices and network conditions

## Best Practices
- Follow Clean Architecture principles strictly
- Implement proper error handling with Either types
- Use immutable state with freezed
- Keep UI logic separate from business logic
- Document offline behavior and sync strategies
- Test real-time updates and notifications
- Ensure multi-tenant data isolation