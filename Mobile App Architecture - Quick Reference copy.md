# Mobile App Architecture - Quick Reference

## Overview
Flutter mobile app using **Feature-First Clean Architecture** with **Riverpod** for state management and dependency injection.

## Technology Stack
- **FVM**: Flutter version management tool
- **Flutter**: 3.38.5 (managed by FVM)
- **Dart**: 3.10.4
- **State Management**: flutter_riverpod ^2.4.9
- **Routing**: go_router ^13.2.2
- **Networking**: dio ^5.4.0
- **Error Handling**: dartz ^0.10.1
- **Storage**: flutter_secure_storage ^9.0.0, hive ^2.2.3
- **Code Generation**: build_runner ^2.4.8, riverpod_generator ^2.3.9, hive_generator ^2.0.1
- **Linting**: very_good_analysis ^6.0.0

## Architecture Layers

### 1. Presentation Layer
- **Views**: Flutter widgets (`ConsumerWidget`)
- **ViewModels**: State management (`StateNotifier`)
- **State**: Immutable state objects
- **Navigation**: GoRouter with typed routes

### 2. Domain Layer
- **Entities**: Pure Dart business objects (`Equatable`)
- **Use Cases**: Business logic orchestrators
- **Repository Interfaces**: Contracts for data operations
- **Enums**: Business constants

### 3. Data Layer
- **Models**: JSON serializable DTOs
- **Data Sources**: Remote (API) and local (cache) providers
- **Repository Implementations**: Interface implementations
- **Mappers**: Entity ↔ Model conversions

### 4. Core Layer
- **Networking**: Dio client with interceptors
- **Storage**: Secure storage and Hive helpers
- **Constants**: App-wide configuration
- **Utils**: Shared utilities and helpers

## Project Structure
```
lib/
├── app/                    # App setup, router, config
├── core/                   # Shared utilities and infrastructure
│   ├── constants/          # App-wide constants and strings
│   ├── errors/             # Failures and exceptions
│   ├── extensions/         # Dart extensions
│   ├── network/            # Dio client and API client
│   ├── providers/          # Core Riverpod providers
│   ├── responsive/         # Responsive sizing and widgets
│   ├── services/           # Shared services
│   ├── storage/            # Hive and secure storage helpers
│   ├── theme/              # App theme
│   ├── utils/              # Utilities (logger, JWT, etc.)
│   └── widgets/            # Reusable shared widgets
├── features/               # Feature modules
│   └── feature_name/
│       ├── data/           # Models, data sources, repositories
│       ├── domain/         # Entities, use cases, interfaces
│       └── presentation/   # Views, ViewModels, widgets
└── main.dart              # App entry point
```

## Key Patterns

### MVVM Pattern
```
View (Widget) ← observes → ViewModel (StateNotifier) ← uses → Repository Interface
```

### Dependency Injection
- Riverpod providers for all dependencies
- Interfaces in Domain, implementations in Data
- Constructor injection with provider overrides for testing

### Error Handling
- `Either<Failure, Success>` from Dartz
- Custom failure types extending base `Failure`
- Repository-level error mapping

### State Management
- `StateNotifierProvider` for complex state
- `Provider` for simple values and services
- `ref.watch()` for reactive UI updates
- `ref.read()` for one-time access

## Development Workflow

### Adding a Feature
1. Create `lib/features/feature_name/` folder
2. Define domain entities and repository interfaces
3. Implement data models and data sources
4. Create repository implementation with providers
5. Build ViewModel and screen View
6. Register routes in `app/router/app_router.dart`
7. Add tests and validate

### Coding Standards
- **Files**: `snake_case` with semantic suffixes
- **Classes**: `PascalCase` (Views, ViewModels, Entities, Models)
- **Variables**: `camelCase`
- **Constants**: `lowerCamelCase`
- **Providers**: `camelCase` + `Provider`

## Testing Strategy
- **Unit Tests**: Use cases, repositories, utilities
- **Widget Tests**: UI components and state changes
- **Integration Tests**: Complete user flows
- Mock dependencies using provider overrides

## Best Practices
- Keep business logic in use cases, not widgets
- Use immutable state objects
- Prefer composition over inheritance
- Write tests alongside code
- Keep features self-contained
- Use dependency injection everywhere

## Common Commands
- `fvm flutter pub get` - Install dependencies
- `fvm flutter test` - Run tests
- `fvm flutter analyze` - Code analysis
- `fvm flutter run` - Launch app
- `fvm flutter pub run build_runner build` - Generate code