# Mobile App - Developer Guide

## Purpose
This guide helps developers understand the mobile app architecture, set up the workspace, and follow consistent development practices for Flutter applications using Clean Architecture with Riverpod.

## Getting Started

### Prerequisites
- FVM installed and configured for the project
- Flutter SDK `3.38.5` managed by FVM
- Dart `3.10.4`
- Optional: Android Studio / VS Code
- Git for source control

### Local setup
1. Clone the repository to your machine.
2. Run `fvm flutter pub get` from the project root.
3. Launch the app with `fvm flutter run`.
4. Open the app in your IDE and confirm the project builds successfully.

### Environment configuration
- Store runtime values in `lib/app/env_config.dart`
- Keep secrets out of source control
- Validate base API URLs and environment keys before running

## Project Layout

### Folder responsibilities
- `app/`: app entry point, router, environment setup
- `core/`: shared constants, networking, storage, utilities
- `features/`: feature modules
- `main.dart`: app bootstrap

### Feature structure
Each feature should contain:
- `domain/`
- `data/`
- `presentation/`

Example:
```text
lib/features/auth/
├── data/
├── domain/
└── presentation/
```

## Development Workflow

### Adding a new feature
1. Create the feature folder under `lib/features/`
2. Add domain entities and repository interfaces
3. Implement remote/local data sources and models
4. Create repository implementation and provider wiring
5. Build ViewModel and screen view
6. Register route in `app/router/app_router.dart`
7. Write tests
8. Validate on device/emulator

### Updating existing feature
- Keep changes inside the feature's folder
- Avoid cross-feature coupling unless necessary
- Update providers and route config only when adding new screens

### Routing
- Use `GoRouter`
- Add new routes in `app/router/app_router.dart`
- Keep route names consistent and easy to read

## Coding Standards

### File naming
- Use `snake_case`
- Add semantic suffixes: `_view.dart`, `_viewmodel.dart`, `_model.dart`, `_entity.dart`, `_repository.dart`

### Class naming
- Use `PascalCase`
- End UI widgets with `View`
- End state holders with `ViewModel`
- End entities with `Entity`
- End models with `Model`

### Provider naming
- Use `camelCase`
- End providers with `Provider`

### Constants
- Use `lowerCamelCase`
- Prefer `static const` inside classes

### State management
- Use Riverpod for state and DI
- Prefer `StateNotifierProvider` for ViewModel state
- Use `ref.watch()` in UI for reactive state
- Use `ref.read()` for one-time access
- Use `ref.listen()` for side effects

### Dependency injection
- Provide dependencies through Riverpod
- Avoid manual instantiation inside ViewModels
- Keep interfaces in the Domain layer
- Inject repository abstractions into Use Cases and ViewModels

## Testing Guide

### Unit tests
- Cover domain use cases
- Cover repository logic
- Mock data sources and dependencies

### Widget tests
- Test UI rendering and state changes
- Test interaction flows in individual screens

### Integration tests
- Test complete flows such as login, logout, and profile updates
- Use real app bootstrap and navigation

## Best Practices

- Keep business logic outside widgets
- Keep views declarative and lifecycle-safe
- Use immutable state objects for view state
- Prefer small, single-responsibility classes
- Write tests concurrently with code changes

## Release Checklist
- Confirm project builds successfully
- Run `fvm flutter test`
- Verify no lint issues from `very_good_analysis`
- Validate API endpoints and auth flows
- Confirm secure storage usage for tokens
- Review new route additions and feature folder structure

## Useful commands
- `fvm flutter pub get`
- `fvm flutter test`
- `fvm flutter analyze`
- `fvm flutter run`

## Collaboration Notes
- Use feature branches for new work
- Keep PRs focused and small
- Include a summary of changes and related stories
- Attach screenshots or test logs when relevant