# Mobile App - Architecture Rules

## Purpose
These rules define the architecture, coding, and delivery standards for Flutter mobile applications using Clean Architecture with Riverpod.

## Architecture Rules

### 1. Layer boundaries
- Presentation layer may depend on Domain and Core
- Domain layer must not depend on Presentation or Data
- Data layer may depend on Domain and Core
- Core may be used by all layers

### 2. Feature-first organization
- Always organize code by feature under `lib/features/`
- Each feature should contain `domain`, `data`, and `presentation`
- Shared behavior belongs in `core/`

### 3. Dependency direction
- Dependencies must point inward toward the Domain layer
- Use repository interfaces in Domain
- Implement interfaces in Data
- Inject implementations through Riverpod providers

## Coding Rules

### 1. Naming conventions
- Files: `snake_case`
- Classes: `PascalCase`
- Providers: `camelCase` + `Provider`
- Variables: `camelCase`
- Constants: `lowerCamelCase`
- Enums: name in `PascalCase`, values in `camelCase`

### 2. File suffix rules
- `_view.dart` for screens/widgets
- `_viewmodel.dart` for state controllers
- `_model.dart` for data models
- `_entity.dart` for domain entities
- `_repository.dart` for repository classes
- `_datasource.dart` for remote/local data sources
- `_arguments.dart` for route argument objects

### 3. Class structure
- Views should be `ConsumerWidget` or `ConsumerStatefulWidget`
- ViewModels should extend `StateNotifier<T>` or `AutoDisposeStateNotifier<T>`
- Domain entities should extend `Equatable`
- Models should provide `fromJson`/`toJson`

### 4. Functions and methods
- Use `camelCase`
- Public methods must be verbs
- Private methods start with `_`
- Use `on` prefix for callbacks: `onPressed`, `onChanged`

### 5. Constants
- Use `static const` fields inside classes
- Avoid `SCREAMING_SNAKE_CASE`
- Use semantic names such as `baseUrl`, `defaultPadding`

## State Management Rules

### 1. Riverpod usage
- Use Riverpod for both state and DI
- Avoid `setState` for business logic
- Use `StateNotifierProvider` for ViewModels
- Use `Provider` for repositories and helpers

### 2. Provider rules
- Keep providers in the same feature or core area
- Do not create global mutable state outside Riverpod
- Use `autoDispose` for screens that should release resources

## Authentication Rules

### 1. Token handling
- Store `USER Token` and `CLIENT Token` in `FlutterSecureStorage`
- Do not store tokens in plain local storage
- Use the interceptor token priority: `USER` then `CLIENT`

### 2. API requests
- Use `Dio` for all HTTP calls
- Add auth headers in one interceptor
- Log requests only in debug mode

## Data Rules

### 1. Repository interfaces
- Define interfaces in Domain layer
- Keep interface methods focused and limited
- Avoid large repository contracts

### 2. Models and mapping
- Keep Domain entities separate from Data models
- Implement `fromEntity` and `toEntity` where needed
- Use generated JSON serialization when possible

### 3. Local storage
- Use Hive for cache and settings
- Use Secure Storage only for secrets and tokens
- Keep storage access encapsulated in core storage helpers

## Error Handling Rules

- Use `Either<Failure, T>` for repository responses
- Do not throw raw exceptions across layers
- Map errors to domain-friendly failure types
- Display friendly messages at the UI layer only

## Testing Rules

- Add unit tests for use cases and repositories
- Add widget tests for each screen UI state
- Add integration tests for major user flows
- Use provider overrides inside tests for dependency substitution

## Review and Delivery Rules

- Use FVM to ensure a consistent Flutter toolchain
- Run `fvm flutter analyze` before merging
- Run `fvm flutter test` on feature changes
- Keep pull requests focused
- Document architectural decisions when introducing new patterns
- Share environment or API changes in PR description