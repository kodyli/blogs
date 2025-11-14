# Building Resilient Frontends: Clean Architecture in React

Structuring your frontend code effectively is crucial for building scalable Single Page Applications (SPAs). When application complexity increases, the boundaries between **presentation logic**, **state management**, and **core business rules** often blur. Clean Architecture offers a powerful solution by providing a clear map for separating these concerns in your React projects.

---

## The Core Problem: Tightly Coupled Components

In many standard React applications, components often become **monolithic** because they attempt to handle everything: managing local input, performing validation, calling an API service, and displaying status. This creates a component that is difficult to test, maintain, and reuse.

| Concern | Tightly Coupled Approach | Clean Architecture Approach |
| :--- | :--- | :--- |
| **Core Logic** | Mixed directly in component event handlers (e.g., calling the API). | Isolated in a dedicated **Use Case** class. |
| **Data Fetching** | Handled inside the component using `useEffect` or utility functions. | Abstracted to the **Data Layer** (Repository implementation). |
| **Reusability** | Low; the component is tied to a specific data operation. | High; the UI layer only requires basic props to function. |

---

## The Solution: Layers of Responsibility

Clean Architecture solves this by defining distinct layers. Our implementation uses three main layers:

### 1. The Core Layer: The Business Brain

This layer defines the **rules** of your application.

#### Example: The Use Case (`CreateOrderUseCase.js`)
~~~js
// src/core/use-cases/CreateOrderUseCase.js
import { Order } from '../entities/Order';
import { OrderAPISource } from '../../data/sources/OrderAPISource'; 

export class CreateOrderUseCase { 
  constructor(repository = new OrderAPISource()) { /* ... */ }

  async execute(orderData) {
    // 1. Create the Entity (Entity constructor handles basic data safety)
    const newOrder = new Order(orderData.item, orderData.quantity);
    
    // 2. Call the Data Layer
    const createdOrder = await this.repository.add(newOrder); 
    
    return createdOrder;
  }
}
~~~

### 2. The Data Layer: The Infrastructure

This layer contains **Repository Implementations** that manage external details, such as performing HTTP requests to a backend API.

#### Example: The Repository (`OrderAPISource.js`)
~~~js
// src/data/sources/OrderAPISource.js
import { Order } from '../../core/entities/Order';

export class OrderAPISource {
  
  async add(orderEntity) {
    console.log(`[API Call] Sending data to /api/orders:`, orderEntity);
    
    // Simulate Network Delay and API call
    await new Promise(resolve => setTimeout(resolve, 1000));
    
    // Simulate successful API call returning the created entity
    return new Order(orderEntity.item, orderEntity.quantity); 
  }
}
~~~

### 3. The UI Layer: The Interface Adapters

This is the presentation layer (your React code). We use a **Smart Form / Dumb Container** pattern here to connect the user interface to the business core.

---

## Pattern in Action: Smart Form / Dumb Container

This pattern clearly separates the concerns of **UI Presentation** from **Application Orchestration**.

### A. The Dumb Container (CreateOrderPage)

This component acts as the **Dependency Injector**.
~~~js
// src/ui/pages/CreateOrderPage.jsx
import React from 'react';
import { CreateOrderForm } from './CreateOrderForm';
import { CreateOrderUseCase } from '../../core/use-cases/CreateOrderUseCase'; 

const createOrderUseCase = new CreateOrderUseCase();
const DEFAULT_FORM_VALUES = { item: '', quantity: 1 };

export const CreateOrderPage = () => {
  
  // This function is the only connection to the business layer
  const handleCoreBusinessLogic = async (data) => {
    await createOrderUseCase.execute(data);
  };

  return (
    <div style={{ padding: '20px' }}>
      <CreateOrderForm
        defaultValues={DEFAULT_FORM_VALUES}
        onSubmit={handleCoreBusinessLogic}
      />
    </div>
  );
};
~~~

### B. The Smart Form (CreateOrderForm)

This component is **self-contained**. It manages all local state, inputs, and the submission lifecycle.
~~~js
// src/ui/pages/CreateOrderForm.jsx
import React, { useState } from 'react';

export const CreateOrderForm = ({ defaultValues, onSubmit }) => {
  
  // All Form State is managed internally
  const [formData, setFormData] = useState(defaultValues);
  const [status, setStatus] = useState('idle'); 
  const [error, setError] = useState(null);

  // Input Handler is local
  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };
  
  const handleFormSubmission = async (e) => {
    e.preventDefault();
    
    // Pre-submission setup
    setStatus('loading');
    setError(null);
    
    try {
      // Call the Use Case via the onSubmit prop
      await onSubmit(formData); 
      
      // Success handling
      setStatus('success');
      setFormData(defaultValues); // Reset form state
    } catch (err) {
      // Error handling (Catches errors from the Core or Data layers)
      setStatus('error');
      setError(err.message || 'Failed to create order.');
    }
  };

  const isDisabled = status === 'loading';

  return (
    <div>
      <h1>Create New Order</h1>
      <form onSubmit={handleFormSubmission}> 
        <div>
          <label htmlFor="item">Item Name:</label>
          <input
            id="item"
            name="item"
            type="text"
            value={formData.item}
            onChange={handleInputChange} 
            disabled={isDisabled}
            required 
          />
        </div>
        <div>
          <label htmlFor="quantity">Quantity:</label>
          <input
            id="quantity"
            name="quantity"
            type="number"
            value={formData.quantity}
            onChange={handleInputChange}
            disabled={isDisabled}
            min="1"
            required 
          />
        </div>
        <button type="submit" disabled={isDisabled}>
          {isDisabled ? 'Creating...' : 'Submit Order'}
        </button>
      </form>

      {/* Status and Error Display is LOCAL to the form */}
      {status === 'success' && <p style={{ color: 'green' }}>Order submitted successfully!</p>}
      {status === 'error' && <p style={{ color: 'red' }}>Error: {error}</p>}
    </div>
  );
};
~~~

---

## The Validation Strategy

By using this architecture, we minimize code duplication and establish a strong defense strategy:

| Layer | Validation Purpose | Mechanism |
| :--- | :--- | :--- |
| **UI Layer (Form)** | **User Experience & Structure.** Ensures input fields are filled correctly. | HTML `required` and `min` attributes (Browser Validation). |
| **Core Layer (Entity)** | **Data Safety.** Enforces minimum structural integrity before a business object can exist. | Checks within the Entity's constructor (e.g., `if (quantity <= 0)`). |
| **Backend Service** | **Final Business Rules & Security.** The ultimate authority for complex, sensitive, or transactional checks. | Final validation checks on the server side. |

By separating these concerns, your React application becomes a lightweight, organized interface that remains decoupled from the underlying business complexity, leading to code that is easier to test, debug, and evolve.

---
## Evaluating the Architecture: Pros and Cons

Choosing an architecture is always about trade-offs. Here is why this Clean Architecture implementation is a powerful choice, and why it might be overkill for smaller projects.

### Why This Design is Good (Pros)

| Benefit | Description |
| :--- | :--- |
| **High Testability** | The Use Case and Repository are plain JavaScript classes, meaning they can be unit-tested without needing to mock or mount React components. |
| **Decoupling/Reusability** | The **Smart Form** is reusable for any application that needs to collect `item` and `quantity`. The **Core Logic** is reusable by a mobile app or CLI client. |
| **Clear Scaling** | Any change to the database or API only affects the Data Layer (Repository), leaving the Core Logic and UI untouched. |
| **Strict Standards** | Enforces the distinction between application flow (Use Case) and presentation logic (Form), improving code quality over time. |

### Why This Design May Not Be Good (Cons)

| Drawback | Description |
| :--- | :--- |
| **Boilerplate** | A simple feature (creating an order) requires a minimum of four files (`Entity`, `Use Case`, `Repository`, `Form`) plus the container. This drastically increases setup time for small projects. |
| **Indirection** | Tracing the execution flow of a single button click requires navigating through multiple files (`Form` -> `Container` -> `Use Case` -> `Repository`), adding cognitive overhead. |
| **Client-Side Validation Delay** | By removing all complex validation from the Use Case, the user may only see complex business errors (e.g., "item is blacklisted") after the slow network request completes, impacting user experience. |
| **Over-engineering** | For small applications with simple CRUD (Create, Read, Update, Delete) operations, the complexity introduced by the extra layers often outweighs the maintenance benefits. |

---

## AI Prompt for Architectural Code Review

~~~md
**Role:** You are an experienced Senior Software Architect performing a code review. Your primary task is to assess whether the provided junior developer's code adheres to the defined Clean Architecture (CA) pattern, specifically the Smart Form / Dumb Container separation and layer responsibilities.

**Architecture Reference:**
1.  **Core Layer (Use Case/Entity):** Must contain business logic. Must be framework-agnostic (no React hooks, no DOM, no UI state management). Should not perform basic UI/structure validation (e.g., checking if an input field is empty).
2.  **Data Layer (Repository):** Must handle all external I/O (e.g., API calls, network logic).
3.  **UI Layer (React Components):**
    * **Dumb Container (Page):** Must instantiate the Use Case and pass the execution function (`onSubmit`) down as a prop. Should hold no complex state.
    * **Smart Form:** Must manage all local UI state (inputs, loading/error status, success/failure display). Must call the received `onSubmit` prop and handle its `try/catch` block for errors originating from the Core/Data layers. Must rely on HTML attributes (`required`, `min`) for basic validation.

**Task:** Review the following code snippet(s).

**Instructions:**
1.  **Identify the Role:** For each file/component, explicitly state its intended role (e.g., "Smart Form," "Core Use Case").
2.  **Compliance Score:** Give a rating (e.g., 1 to 5, where 5 is fully compliant) and briefly justify it.
3.  **Specific Violations:** List any specific lines or sections that violate the architectural rules (e.g., "Violation: Use Case imports React," or "Violation: Form component defines the API URL").
4.  **Refactoring Advice:** Provide concise, actionable advice on how to fix the violations to achieve full compliance with the CA pattern.

**Code Snippet to Review:**
[PASTE THE JUNIOR DEVELOPER'S CODE SNIPPET(S) HERE]
~~~

~~~md
**Role:** You are an experienced Senior Software Architect performing a mandatory code review for a feature implementation. Your primary task is to assess whether the entire codebase—across all layers—adheres to the defined Clean Architecture (CA) pattern and its responsibilities.

**Clean Architecture Ruleset (The Blueprint):**

| Layer | Component | Core Responsibility | Key Violation to Check For |
| :--- | :--- | :--- | :--- |
| **Core** | Use Case / Entity | Contains all application/business logic; framework-agnostic. | Importing any React hook, DOM element, or API utility. |
| **Data** | Repository/Source | Handles all external I/O (API, persistence simulation, `fetch`). | Defining business logic or importing React. |
| **UI (Container)**| Dumb Container (Page) | Instantiates Core logic and passes the execution function (orchestration). | Holding UI-specific state (loading, error, success status) or running input validation. |
| **UI (Form)** | Smart Form | Manages all local UI state (inputs, status display) and handles submission lifecycle. | Importing or instantiating the Use Case or Data Layer directly. |

---

**Task:** Review the provided code snippet(s) covering the Core, Data, and UI layers for the order creation feature.

**Instructions:**
1.  **Identify the Role:** For each file/component provided, explicitly state its intended architectural role (e.g., "Core Use Case," "Data Repository," "UI Smart Form").
2.  **Compliance Score:** Give a rating (1 to 5, where 5 is fully compliant) and briefly justify it based on the ruleset.
3.  **Specific Violations:** List any specific lines or sections that clearly violate the architectural rules (e.g., "Violation in Use Case: Contains a `useState` call," or "Violation in Smart Form: Defines the API URL.").
4.  **Refactoring Advice:** Provide concise, actionable advice on how to fix the violations to achieve full compliance across the architecture.

**Code Snippet(s) to Review:**
[PASTE THE JUNIOR DEVELOPER'S CODE SNIPPET(S) HERE]
~~~

**Role:** You are an experienced Senior Software Architect performing a mandatory, detailed code review. Your primary task is to assess whether the provided code adheres to the defined Clean Architecture (CA) pattern, ensuring strict separation of responsibilities and validation boundaries across all layers.

---

### Clean Architecture Ruleset (The Blueprint):

| Layer | Component | Primary Responsibility | State Management Rule | Key Violation to Check For |
| :--- | :--- | :--- | :--- | :--- |
| **Core** | Use Case / Entity | **Business Logic/Application Flow.** Must contain core rules. Framework-agnostic. | None. Must be pure JavaScript classes. | Importing any React hook, DOM element, or API utility. |
| **Data** | Repository/Source | **External I/O.** Handles network traffic (`fetch`, `axios`) and persistence logic. | None. Must be pure JavaScript classes. | Defining business logic (e.g., quantity check) or importing React. |
| **UI (Container)**| Dumb Container (Page) | **Orchestration.** Wires dependencies (instantiates Use Case) and defines the execution function. | **MUST NOT** manage any UI-specific state (loading, error, success status). | Managing status state (e.g., `const [loading, setLoading] = useState(false);`). |
| **UI (Form)** | Smart Form | **Presentation/View State.** Manages inputs, validation feedback, and submission lifecycle. | **MUST** manage all local UI status state (inputs, loading, error, success display). | Importing or instantiating Core or Data layers directly. |

---

### Intent and Rationale (Crucial Context for Review):

**1. Validation Strategy (Defense in Depth):**
* **Browser (HTML attributes):** Handles structural validity (`required`, `min="1"`) for immediate user feedback.
* **Entity Constructor:** Handles basic data safety immediately upon object creation (`if (quantity <= 0)`).
* **Backend (API Call):** Handles complex business rules (e.g., "item is blacklisted," "inventory check").
* ***The UI Layer (Form) MUST NOT attempt complex business validation logic in JavaScript; it must trust the Backend to throw an error if the data is invalid, and simply display that error.***

**2. Decoupling and Testing:**
* The **Core** and **Data** layers must be entirely reusable without a React dependency. If a core class imports `useState`, the architecture fails immediately.
* The **Smart Form** must be reusable and only communicates with the outside world via the `onSubmit` function prop.

**3. State Ownership:**
* The **Dumb Container**'s only job is to provide the logic (`onSubmit`). The **Smart Form** owns all the visual feedback (`loading`/`error` status) because those concerns belong to the presentation layer.

---

**Task:** Review the provided code snippet(s) covering the entire application flow.

**Instructions:**
1.  **Identify the Role:** For each file/component provided, explicitly state its intended architectural role ("Core Use Case," "UI Smart Form," etc.).
2.  **Compliance Score:** Give a rating (1 to 5, where 5 is fully compliant) and briefly justify it based on the ruleset and rationale.
3.  **Specific Violations:** List any specific lines or sections that clearly violate the architectural rules, referencing the layer and the rule broken.
4.  **Refactoring Advice:** Provide concise, actionable advice on how to fix the violations.

**Code Snippet(s) to Review:**
[PASTE THE JUNIOR DEVELOPER'S CODE SNIPPET(S) HERE]