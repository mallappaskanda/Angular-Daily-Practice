# Day 2 — Components & Dependency Injection

## 1. Standalone Component

A **standalone component** manages its template dependencies independently without requiring an `NgModule`.

Its dependencies are declared locally in the component's `imports`, making them **explicit, visible, and easier to understand and maintain**.

In the traditional `NgModule` approach, dependencies such as components, directives, and pipes were often declared or imported at the module level.

When an application became large, it could be difficult to trace where a dependency was declared, imported, or made available to a component.

Standalone components reduce this complexity by keeping the component's dependencies close to the component itself.

### Example

```ts
@Component({
  selector: 'app-alert-list',
  standalone: true,
  imports: [CommonModule, AlertTableComponent],
  templateUrl: './alert-list.html'
})
export class AlertList {}
```

Here, `AlertList` explicitly declares the dependencies it uses in its template.

### Easy way to remember

> **Standalone Component = Component + its template dependencies declared locally**

---

## 2. Component Responsibility

Every component should have a **clear and specific responsibility**.

It should primarily focus on the **UI and user interactions** related to that responsibility.

For example, `AlertList` is responsible for the Alert List screen.

### AlertList can:

- Display the list of alerts.
- Handle search, filter, sort, pagination, and selection.
- Pass data to child components using `input()`.
- Respond to child events using `output()`.
- Coordinate UI components on the Alert List screen.

### AlertList should not:

- Contain authentication/authorization logic.
- Directly handle HTTP/API communication.
- Contain database-related logic.
- Contain unrelated business logic.
- Create dependencies using `new`.
- Become responsible for functionality belonging to other features.

API communication and data-related operations should be delegated to services, while reusable UI functionality can be moved into child components.

### Example

Instead of:

```ts
export class AlertList {
  private alertService = new AlertService();
}
```

Prefer:

```ts
export class AlertList {
  private alertService = inject(AlertService);
}
```

The component focuses on the UI, while `AlertService` handles API/data-related responsibility.

### Easy way to remember

> **Component = UI + User Interaction + Coordination**

---

## 3. Dependency Injection

**Dependency Injection (DI)** is a design pattern where a class receives the dependencies it needs instead of creating those dependencies itself.

Angular provides a **DI system** that creates, resolves, and provides dependencies based on their provider configuration and injector scope.

If `AlertList` needs `AlertService`, instead of:

```ts
const alertService = new AlertService();
```

we use:

```ts
private alertService = inject(AlertService);
```

Here, `AlertList` is saying:

> "I need `AlertService`."

Angular's DI system provides the required instance.

### Without DI

```text
AlertList
    |
    └── new AlertService()
```

### With DI

```text
AlertList
    |
    | needs AlertService
    ↓
Angular DI
    |
    ↓
provides AlertService
```

### Benefits of DI

- Reduces tight coupling.
- Makes testing easier by allowing mocks/fakes.
- Centralizes dependency creation and management.
- Allows dependencies to have different scopes/lifetimes.
- Makes applications easier to maintain and extend.
- Lets a class focus on its own responsibility.

### Example

```ts
@Injectable({
  providedIn: 'root'
})
export class AlertService {
  getAlerts() {
    // API/data-related logic
  }
}
```

Then:

```ts
export class AlertList {
  private alertService = inject(AlertService);
}
```

### One sentence to remember

> **"I don't create what I need; I declare what I need, and Angular's DI system provides it."**

---

## 4. Feature Service

A service that belongs to a **specific feature** should be kept inside that feature.

This keeps all feature-related code together, making it easier to **understand, maintain, and modify**.

### Example

```text
features/
└── alerts/
    ├── pages/
    │   └── alert-list/
    ├── services/
    │   └── alert.service.ts
    └── models/
        └── alert.model.ts
```

`AlertService` belongs to the **Alerts feature**, so it stays inside the `alerts` folder.

### Why?

Keeping Alerts-related code together makes it easier to:

- Find related code.
- Understand the feature.
- Maintain the feature.
- Modify or extend the feature.
- Remove the feature when required.

### Easy rule

> **If a service belongs to one feature, keep it inside that feature.**

---

## 5. Core vs Feature

### Core

`Core` contains **application-wide functionality and services** used across the application or responsible for application-level concerns.

Examples:

```text
core/
├── services/
│   ├── auth.service.ts
│   └── logging.service.ts
├── guards/
└── interceptors/
```

Typical Core responsibilities:

- Authentication infrastructure
- Global logging
- Global error handling
- HTTP interceptors
- Application-wide services

### Feature

A feature contains functionality belonging to a **specific business feature**.

Example:

```text
features/
└── alerts/
    ├── pages/
    ├── components/
    ├── services/
    │   └── alert.service.ts
    └── models/
```

`AlertService` belongs to the Alerts feature because it handles Alert-related operations.

### Important distinction

Do not put something in `Core` simply because it is reusable.

The main question is:

> **Does this responsibility belong to the whole application or to a specific feature?**

For example, a reusable `AlertStatusBadgeComponent` may still belong inside the Alerts feature if it is specifically related to Alerts.

### Easy rule

> **Core = application-wide responsibility**  
> **Feature = feature-specific responsibility**

---

# Day 2 — Quick Revision

| Concept | Main Idea |
|---|---|
| **Standalone Component** | Declares template dependencies locally |
| **Component Responsibility** | Has a clear UI-focused responsibility |
| **Dependency Injection** | Class declares what it needs; Angular provides it |
| **Feature Service** | Feature-specific services stay inside that feature |
| **Core** | Application-wide functionality and services |
| **Feature** | Functionality related to a specific business feature |

## Day 2 Mental Model

```text
Application
│
├── Core
│   └── Application-wide responsibilities
│
└── Features
    └── Alerts
        ├── UI Components
        ├── Pages
        ├── Feature Services
        └── Models
```

### Final Takeaway

> **Keep responsibilities close to where they belong.**
>
> Components handle UI and user interaction.  
> Services handle reusable/data-related responsibilities.  
> Feature-specific services stay inside their feature.  
> Application-wide services and infrastructure belong in Core.  
> DI allows Angular to provide dependencies instead of classes creating them themselves.
