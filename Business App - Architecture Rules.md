# Business App - Architecture Rules

## Purpose
These rules define the architecture, coding, and delivery standards for the Business Mobile Application, a restaurant operations management platform using Flutter with Clean Architecture and Riverpod.

## Architecture Rules

### 1. Layer Boundaries
- Presentation layer may depend on Domain and Core
- Domain layer must not depend on Presentation or Data
- Data layer may depend on Domain and Core
- Core may be used by all layers
- Business-specific logic stays within feature boundaries

### 2. Feature-First Organization
- Always organize code by feature under `lib/features/`
- Each feature should contain `domain`, `data`, and `presentation`
- Shared behavior belongs in `core/` or `shared/`
- Business features: auth, business_switch, dashboard, reservations, tables, orders, staff, analytics, profile

### 3. Dependency Direction
- Dependencies must point inward toward the Domain layer
- Use repository interfaces in Domain
- Implement interfaces in Data
- Inject implementations through Riverpod providers
- Respect multi-tenant data isolation

### 4. Multi-Tenant Architecture
- Include business ID in all data operations
- Isolate local storage per business
- Handle business switching at the application level
- Ensure API requests include tenant identifiers

### 5. Offline-First Design
- Implement local storage for all operational data
- Use offline queue for write operations
- Handle sync conflicts with server priority
- Test offline scenarios in all features

## Coding Rules

### 1. Naming Conventions
- Files: `snake_case`
- Classes: `PascalCase`
- Providers: `camelCaseProvider`
- Variables: `camelCase`
- Constants: `lowerCamelCase`
- Enums: `PascalCase` values
- Business entities: Use descriptive names (e.g., `Reservation`, `TableAssignment`)

### 2. File Suffix Rules
- `_screen.dart` for main screens
- `_viewmodel.dart` for state controllers
- `_model.dart` for data models
- `_entity.dart` for domain entities
- `_repository.dart` for repository classes
- `_datasource.dart` for remote/local data sources
- `_arguments.dart` for route arguments

### 3. Class Structure
- Views should be `ConsumerWidget` or `ConsumerStatefulWidget`
- ViewModels should extend `StateNotifier<T>` or `AutoDisposeStateNotifier<T>`
- Domain entities should extend `Equatable`
- Models should provide `fromJson`/`toJson`
- Implement proper error handling with `Either<Failure, Success>`

### 4. Functions and Methods
- Use async/await for asynchronous operations
- Return `Either<Failure, T>` for operations that can fail
- Keep methods focused on single responsibilities
- Use descriptive names for business operations

### 5. State Management
- Use Riverpod providers for dependency injection
- Implement state notifiers for complex state
- Use `freezed` for immutable state objects
- Handle loading, success, and error states explicitly

## Business Logic Rules

### 1. Reservation Management
- Follow reservation lifecycle: PENDING → CONFIRMED → CHECKEDIN → COMPLETED/CANCELLED/NOSHOW/EXPIRED
- Support multiple check-in methods: QR scan, booking ID, customer search
- Handle real-time updates for reservation status

### 2. Table Management
- Maintain table status: Available, Reserved, Occupied, Cleaning, Closed
- Support facility area organization
- Handle table assignments and conflicts

### 3. Order Management
- Support order types: Dine-In, Takeaway, Delivery, Pre-Order
- Follow order lifecycle: Pending → Preparing → Ready → Served → Completed/Cancelled
- Allow table assignment post-creation

### 4. Walk-in Handling
- Create orders for walk-in customers
- Assign tables dynamically
- Integrate with normal order processing

### 5. Business Switching
- Implement seamless business switching
- Reload all data based on selected business
- Maintain user session across switches

### 6. Real-Time Updates
- Implement notifications for operational events
- Use WebSocket or polling for live data
- Update UI and local storage on events

### 7. Offline Strategy
- Store operational data in Hive
- Queue offline actions for sync
- Handle conflicts with server priority
- Provide offline indicators in UI

## Security Rules

### 1. Authentication
- Support login with email/phone + OTP
- Support login with email/phone + password
- Use token-based authentication
- Store tokens securely with Flutter Secure Storage
- Implement automatic token refresh

### 2. Authorization
- Enforce role-based access control
- Check permissions for business operations
- Validate user roles for feature access

### 3. Data Isolation
- Ensure tenant-level data separation
- Encrypt sensitive local data
- Clear data on logout/business switch

## Testing Rules

### 1. Unit Testing
- Test all use cases and repositories
- Mock external dependencies
- Cover error scenarios

### 2. Widget Testing
- Test UI components and state changes
- Verify business logic integration
- Test offline scenarios

### 3. Integration Testing
- Test complete operational workflows
- Verify multi-tenant isolation
- Test sync and conflict resolution

## Code Review Rules

### 1. Pull Request Requirements
- Include FVM in setup instructions
- Document offline behavior changes
- Test multi-business scenarios
- Verify real-time update handling
- Ensure proper error handling

### 2. Architecture Compliance
- Review layer boundary violations
- Check dependency injection usage
- Validate multi-tenant implementation
- Confirm offline-first patterns

## Deployment Rules

### 1. Build Process
- Use FVM for Flutter version consistency
- Configure environment-specific builds
- Include business configuration in builds

### 2. Release Checklist
- Test offline functionality
- Verify multi-tenant data isolation
- Check real-time notifications
- Validate on multiple devices/networks