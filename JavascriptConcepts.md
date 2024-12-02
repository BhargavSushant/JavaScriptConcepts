# JavaScript Objects: A Developer's Daily Guide

- [JavaScript Objects: A Developer's Daily Guide](#javascript-objects-a-developers-daily-guide)
  - [Understanding Object Basics](#understanding-object-basics)
  - [Daily Tasks with Objects](#daily-tasks-with-objects)
    - [1. Checking for Properties](#1-checking-for-properties)
    - [2. Copying and Merging Objects](#2-copying-and-merging-objects)
    - [3. Transforming Objects](#3-transforming-objects)
    - [4. Working with Dynamic Keys](#4-working-with-dynamic-keys)
    - [5. Object Validation and Type Checking](#5-object-validation-and-type-checking)
- [Advanced JavaScript Concepts: A Complete Guide](#advanced-javascript-concepts-a-complete-guide)
  - [1. Object Composition Patterns](#1-object-composition-patterns)
  - [2. Proxy and Reflect APIs](#2-proxy-and-reflect-apis)
  - [3. Object Serialization Techniques](#3-object-serialization-techniques)
- [Understanding Object Composition: From Basics to Advanced](#understanding-object-composition-from-basics-to-advanced)
  - [The Problem with Inheritance](#the-problem-with-inheritance)
- [Advanced Object Composition Patterns](#advanced-object-composition-patterns)
  - [1. Dependency Injection with Composition](#1-dependency-injection-with-composition)
  - [2. Reactive Composition](#2-reactive-composition)
  - [3. Async Behavior Composition](#3-async-behavior-composition)
  - [4. Event-Driven Composition](#4-event-driven-composition)
- [Advanced Object Composition Patterns](#advanced-object-composition-patterns-1)
  - [1. Dependency Injection with Composition](#1-dependency-injection-with-composition-1)
  - [2. Reactive Composition](#2-reactive-composition-1)
  - [3. Async Behavior Composition](#3-async-behavior-composition-1)
  - [4. Event-Driven Composition](#4-event-driven-composition-1)

## Understanding Object Basics

Let's start with how objects work in JavaScript. Think of an object like a container that holds related information and functionality together. It's similar to a filing cabinet where each drawer (property) can hold different types of content.

```javascript
// Creating objects - there are multiple ways
// 1. Object literal notation (most common)
const user = {
  name: "John",
  age: 30,
  email: "john@example.com",
};

// 2. Constructor function
function User(name, age, email) {
  this.name = name;
  this.age = age;
  this.email = email;
}
const user2 = new User("Jane", 25, "jane@example.com");

// 3. Object.create()
const userTemplate = {
  printInfo() {
    console.log(`${this.name} is ${this.age} years old`);
  },
};
const user3 = Object.create(userTemplate);
user3.name = "Bob";
user3.age = 35;
```

## Daily Tasks with Objects

### 1. Checking for Properties

You'll often need to check if an object has certain properties. There are several ways to do this, each with its own use case:

```javascript
const user = {
  name: "John",
  age: 30,
  address: {
    street: "123 Main St",
    city: "Boston",
  },
};

// 1. Using hasOwnProperty (checks direct properties only)
if (user.hasOwnProperty("name")) {
  console.log("User has name property");
}

// 2. Using 'in' operator (checks prototype chain too)
if ("name" in user) {
  console.log("Name exists");
}

// 3. Optional chaining for nested properties (modern way)
const city = user?.address?.city; // Safe way to access nested props

// 4. Nullish coalescing for defaults
const country = user?.address?.country ?? "Unknown";
```

### 2. Copying and Merging Objects

One of the most common tasks is copying or merging objects. Be careful about deep vs shallow copies:

```javascript
// Shallow copy methods
// 1. Object spread (most common)
const userCopy = { ...user };

// 2. Object.assign()
const anotherCopy = Object.assign({}, user);

// Deep copy methods
// 1. JSON method (quick but has limitations with functions, dates, etc.)
const deepCopy = JSON.parse(JSON.stringify(user));

// 2. Custom deep clone (more robust)
function deepClone(obj) {
  if (obj === null || typeof obj !== "object") {
    return obj;
  }

  if (Array.isArray(obj)) {
    return obj.map((item) => deepClone(item));
  }

  return Object.fromEntries(
    Object.entries(obj).map(([key, value]) => [key, deepClone(value)])
  );
}

// Merging objects
const defaultSettings = { theme: "light", notifications: true };
const userSettings = { theme: "dark" };

// Merge with spread (shallow)
const finalSettings = { ...defaultSettings, ...userSettings };

// Real-world example: merging API response with defaults
function processUserData(apiResponse) {
  const defaults = {
    role: "user",
    preferences: {
      emailNotifications: true,
      language: "en",
    },
  };

  return {
    ...defaults,
    ...apiResponse,
    preferences: {
      ...defaults.preferences,
      ...apiResponse?.preferences,
    },
  };
}
```

### 3. Transforming Objects

You'll often need to transform objects from one format to another:

```javascript
const apiResponse = {
  user_id: 123,
  first_name: "John",
  last_name: "Doe",
  created_at: "2023-01-01",
};

// Converting snake_case to camelCase
function toCamelCase(obj) {
  return Object.fromEntries(
    Object.entries(obj).map(([key, value]) => [
      key.replace(/_([a-z])/g, (_, letter) => letter.toUpperCase()),
      value,
    ])
  );
}

// Transform for frontend use
function transformUserData(apiUser) {
  const { first_name, last_name, ...rest } = apiUser;
  return {
    ...toCamelCase(rest),
    fullName: `${first_name} ${last_name}`,
    displayName: first_name,
  };
}

// Filtering object properties
function filterSensitiveData(user) {
  const { password, ssn, ...safeData } = user;
  return safeData;
}
```

### 4. Working with Dynamic Keys

Sometimes you need to work with objects where you don't know the property names in advance:

```javascript
// Creating objects with dynamic keys
function createQueryParams(filters) {
  return Object.fromEntries(
    Object.entries(filters)
      .filter(([_, value]) => value != null)
      .map(([key, value]) => [
        `filter_${key}`,
        Array.isArray(value) ? value.join(",") : value,
      ])
  );
}

// Example usage
const filters = {
  status: "active",
  categories: ["books", "electronics"],
  price: null,
};

const queryParams = createQueryParams(filters);
// Results in:
// {
//     filter_status: 'active',
//     filter_categories: 'books,electronics'
// }

// Accessing dynamic keys safely
function getNestedValue(obj, path) {
  return path.split(".").reduce((current, key) => current && current[key], obj);
}

// Example usage
const data = {
  user: {
    profile: {
      settings: {
        theme: "dark",
      },
    },
  },
};

const theme = getNestedValue(data, "user.profile.settings.theme");
```

### 5. Object Validation and Type Checking

Validating object structure is crucial in real-world applications:

```javascript
// Simple validation function
function validateUser(user) {
  const required = ["name", "email", "age"];
  const errors = [];

  // Check required fields
  for (const field of required) {
    if (!(field in user)) {
      errors.push(`Missing required field: ${field}`);
    }
  }

  // Type checking
  if (typeof user.age !== "number") {
    errors.push("Age must be a number");
  }

  if (typeof user.email !== "string" || !user.email.includes("@")) {
    errors.push("Invalid email format");
  }

  return {
    isValid: errors.length === 0,
    errors,
  };
}

// Advanced validation with schema
const userSchema = {
  name: {
    type: "string",
    required: true,
    minLength: 2,
  },
  age: {
    type: "number",
    min: 0,
    max: 120,
  },
  email: {
    type: "string",
    required: true,
    pattern: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
  },
};

function validateAgainstSchema(data, schema) {
  const errors = [];

  for (const [field, rules] of Object.entries(schema)) {
    const value = data[field];

    // Required check
    if (rules.required && value == null) {
      errors.push(`${field} is required`);
      continue;
    }

    // Skip validation if value is not provided and not required
    if (value == null) continue;

    // Type check
    if (typeof value !== rules.type) {
      errors.push(`${field} must be a ${rules.type}`);
      continue;
    }

    // Additional validations
    if (rules.type === "string") {
      if (rules.minLength && value.length < rules.minLength) {
        errors.push(`${field} must be at least ${rules.minLength} characters`);
      }
      if (rules.pattern && !rules.pattern.test(value)) {
        errors.push(`${field} has invalid format`);
      }
    }

    if (rules.type === "number") {
      if (rules.min != null && value < rules.min) {
        errors.push(`${field} must be at least ${rules.min}`);
      }
      if (rules.max != null && value > rules.max) {
        errors.push(`${field} must be no more than ${rules.max}`);
      }
    }
  }

  return {
    isValid: errors.length === 0,
    errors,
  };
}
```

# Advanced JavaScript Concepts: A Complete Guide

## 1. Object Composition Patterns

Instead of inheritance with classes (the "extends" pattern), object composition focuses on combining smaller, focused objects to build more complex ones. Think of it like building with LEGO blocks rather than creating a family tree.

```javascript
// Instead of this inheritance approach:
class Animal {
  walk() {
    console.log("Walking...");
  }
}

class Dog extends Animal {
  bark() {
    console.log("Woof!");
  }
}

// We can use composition:
const walker = {
  walk() {
    console.log("Walking...");
  },
};

const barker = {
  bark() {
    console.log("Woof!");
  },
};

const swimmer = {
  swim() {
    console.log("Swimming...");
  },
};

// Now we can create objects by combining behaviors
function createDog(name) {
  return {
    name,
    // Combine the behaviors we want
    ...walker,
    ...barker,
  };
}

function createDuck(name) {
  return {
    name,
    // Different combination of behaviors
    ...walker,
    ...swimmer,
  };
}

// Real-world example: UI Components
const withLogging = {
  logAction(action) {
    console.log(`${this.componentName}: ${action}`);
  },
};

const withDataFetching = {
  async fetchData(url) {
    this.logAction("Fetching data...");
    const response = await fetch(url);
    return response.json();
  },
};

function createDataComponent(componentName) {
  return {
    componentName,
    ...withLogging,
    ...withDataFetching,
  };
}

const userList = createDataComponent("UserList");
userList.fetchData("/api/users"); // Logs: "UserList: Fetching data..."
```

## 2. Proxy and Reflect APIs

Proxies are like security guards for your objects - they can intercept and customize operations like reading properties, writing values, and more. The Reflect API provides methods for these operations.

```javascript
// Let's create a simple reactive object system
function makeReactive(obj) {
  const subscribers = new Map();

  return new Proxy(obj, {
    get(target, property, receiver) {
      // Track when properties are accessed
      if (!subscribers.has(property)) {
        subscribers.set(property, new Set());
      }

      // If we're in a computation, track the dependency
      if (currentComputation) {
        subscribers.get(property).add(currentComputation);
      }

      return Reflect.get(target, property, receiver);
    },

    set(target, property, value, receiver) {
      const result = Reflect.set(target, property, value, receiver);

      // Notify all subscribers when a property changes
      if (subscribers.has(property)) {
        subscribers.get(property).forEach((callback) => callback());
      }

      return result;
    },
  });
}

// Real-world example: Form validation
function createValidatedForm(initialData) {
  const validators = {
    email: (value) => value.includes("@"),
    phone: (value) => /^\d{10}$/.test(value),
    age: (value) => value >= 18,
  };

  return new Proxy(initialData, {
    set(target, property, value) {
      // Check if we have a validator for this property
      if (validators[property] && !validators[property](value)) {
        throw new Error(`Invalid ${property}: ${value}`);
      }

      return Reflect.set(target, property, value);
    },
  });
}

const form = createValidatedForm({});
form.email = "invalid"; // Throws error
form.email = "user@example.com"; // Works fine
```

## 3. Object Serialization Techniques

Object serialization is about converting objects into a format that can be stored or transmitted, then reconstructing them later. It's more complex than just using `JSON.stringify()`.

```javascript
// Advanced serialization with custom types
class CustomSerializer {
  constructor() {
    this.typeHandlers = new Map();

    // Register default handlers
    this.registerType(Date, {
      serialize: (date) => ({ _type: "Date", value: date.toISOString() }),
      deserialize: (data) => new Date(data.value),
    });

    this.registerType(RegExp, {
      serialize: (regex) => ({
        _type: "RegExp",
        source: regex.source,
        flags: regex.flags,
      }),
      deserialize: (data) => new RegExp(data.source, data.flags),
    });
  }

  registerType(constructor, handlers) {
    this.typeHandlers.set(constructor.name, handlers);
  }

  serialize(obj) {
    // Handle null/undefined
    if (obj == null) return obj;

    // Handle primitive types
    if (typeof obj !== "object") return obj;

    // Check for registered types
    const type = obj.constructor.name;
    const handler = this.typeHandlers.get(type);

    if (handler) {
      return handler.serialize(obj);
    }

    // Handle plain objects and arrays
    if (Array.isArray(obj)) {
      return obj.map((item) => this.serialize(item));
    }

    // Handle regular objects
    const serialized = {};
    for (const [key, value] of Object.entries(obj)) {
      serialized[key] = this.serialize(value);
    }
    return serialized;
  }

  deserialize(data) {
    // Handle null/undefined
    if (data == null) return data;

    // Handle primitive types
    if (typeof data !== "object") return data;

    // Check for type information
    if (data._type && this.typeHandlers.has(data._type)) {
      return this.typeHandlers.get(data._type).deserialize(data);
    }

    // Handle arrays
    if (Array.isArray(data)) {
      return data.map((item) => this.deserialize(item));
    }

    // Handle regular objects
    const deserialized = {};
    for (const [key, value] of Object.entries(data)) {
      deserialized[key] = this.deserialize(value);
    }
    return deserialized;
  }
}

// Usage example
const serializer = new CustomSerializer();

const complexObject = {
  date: new Date(),
  pattern: /hello/gi,
  nested: {
    dates: [new Date(), new Date()],
  },
};

const serialized = serializer.serialize(complexObject);
const deserialized = serializer.deserialize(serialized);
```

# Understanding Object Composition: From Basics to Advanced

## The Problem with Inheritance

Before we dive into composition, let's understand why we often want to avoid traditional inheritance. Imagine you're building a video game with different characters:

```javascript
// Traditional inheritance approach
class Character {
  constructor(name) {
    this.name = name;
    this.health = 100;
  }

  move() {
    console.log(`${this.name} moves`);
  }
}

class Warrior extends Character {
  attack() {
    console.log(`${this.name} attacks`);
  }
}

class Mage extends Character {
  castSpell() {
    console.log(`${this.name} casts a spell`);
  }
}

// What if we want a character that can both attack and cast spells?
// We can't easily inherit from both Warrior and Mage!
```

This is where composition shines. Instead of trying to create a rigid hierarchy, we can build characters by combining different abilities:

```javascript
// First, let's create our building blocks (behaviors)
const hasHealth = (state) => ({
  health: 100,
  takeDamage(amount) {
    state.health -= amount;
    console.log(`${state.name} has ${state.health} health remaining`);
  },
  heal(amount) {
    state.health += amount;
    if (state.health > 100) state.health = 100;
    console.log(`${state.name} healed to ${state.health} health`);
  },
});

const canMove = (state) => ({
  move(direction) {
    console.log(`${state.name} moves ${direction}`);
    // This could update state.position if we had one
  },
});

const canFight = (state) => ({
  attack(target) {
    console.log(`${state.name} attacks ${target}`);
    // We could access state.weaponDamage here if needed
  },
});

const canCastSpells = (state) => ({
  mana: 100,
  castSpell(spellName, target) {
    if (state.mana < 10) {
      console.log(`${state.name} is out of mana!`);
      return;
    }
    state.mana -= 10;
    console.log(`${state.name} casts ${spellName} at ${target}`);
  },
});

// Now we can compose these behaviors to create different character types
function createCharacter(name, ...behaviors) {
  // Create the initial state object
  const state = { name };

  // Combine all behaviors by reducing them into a single object
  return behaviors.reduce(
    (character, behavior) => ({
      ...character,
      ...behavior(state),
    }),
    state
  );
}

// Creating different character types becomes very flexible
const warrior = createCharacter("Conan", hasHealth, canMove, canFight);

const mage = createCharacter("Gandalf", hasHealth, canMove, canCastSpells);

// And we can easily create hybrid characters!
const spellblade = createCharacter(
  "Arthus",
  hasHealth,
  canMove,
  canFight,
  canCastSpells
);

// Using our characters
warrior.move("north"); // "Conan moves north"
warrior.attack("goblin"); // "Conan attacks goblin"
warrior.takeDamage(20); // "Conan has 80 health remaining"

spellblade.attack("troll"); // "Arthus attacks troll"
spellblade.castSpell("fireball", "dragon"); // "Arthus casts fireball at dragon"
```

This composition approach gives us several advantages:

1. Flexibility to combine behaviors in any way
2. Each behavior is isolated and easily testable
3. We can add new behaviors without changing existing code
4. State is shared between behaviors but encapsulated from the outside

Let's see how we can make this even more powerful by adding behavior modifiers:

```javascript
// Adding a modifier system
const withLogging = (behavior) => (state) => {
  const originalBehavior = behavior(state);

  // Return a new object with wrapped methods
  return Object.entries(originalBehavior).reduce((wrapped, [key, value]) => {
    if (typeof value === "function") {
      wrapped[key] = (...args) => {
        console.log(`Before ${key}:`, args);
        const result = value.apply(state, args);
        console.log(`After ${key}:`, result);
        return result;
      };
    } else {
      wrapped[key] = value;
    }
    return wrapped;
  }, {});
};

// Now we can create a character with logged actions
const debugWarrior = createCharacter(
  "Debug Warrior",
  withLogging(hasHealth),
  withLogging(canFight)
);

debugWarrior.attack("enemy");
// Logs:
// "Before attack: ['enemy']"
// "Debug Warrior attacks enemy"
// "After attack: undefined"
```

# Advanced Object Composition Patterns

## 1. Dependency Injection with Composition

Let's say we're building a dashboard application that needs to handle data fetching, caching, and error handling. Instead of hardcoding these dependencies, we can inject them:

```javascript
// First, let's create our core behaviors
const withDataFetching = (fetchImplementation) => (state) => ({
  async fetchData(endpoint) {
    state.loading = true;
    try {
      const data = await fetchImplementation(endpoint);
      state.data = data;
      return data;
    } catch (error) {
      state.error = error;
      throw error;
    } finally {
      state.loading = false;
    }
  },
});

const withCache = (cacheImplementation) => (state) => ({
  async getCached(key) {
    let data = await cacheImplementation.get(key);
    if (!data) {
      data = await state.fetchData(key);
      await cacheImplementation.set(key, data);
    }
    return data;
  },
});

const withErrorHandling = (errorHandler) => (state) => ({
  async executeWithErrors(action) {
    try {
      return await action();
    } catch (error) {
      errorHandler(error);
      state.lastError = error;
    }
  },
});

// Now let's create a dashboard component with these behaviors
function createDashboard(dependencies) {
  const { fetcher, cache, errorHandler } = dependencies;

  return createComponent(
    "Dashboard",
    withDataFetching(fetcher),
    withCache(cache),
    withErrorHandling(errorHandler),
    (state) => ({
      async loadUserData(userId) {
        return state.executeWithErrors(async () => {
          return state.getCached(`/users/${userId}`);
        });
      },
    })
  );
}

// Usage with different implementations
const productionDashboard = createDashboard({
  fetcher: fetch,
  cache: redisCache,
  errorHandler: sendToSentry,
});

const testDashboard = createDashboard({
  fetcher: mockFetch,
  cache: inMemoryCache,
  errorHandler: console.error,
});
```

## 2. Reactive Composition

Let's build a reactive system that automatically updates when dependencies change:

```javascript
// First, let's create a reactive state manager
function createReactiveState(initial = {}) {
  const subscribers = new Map();
  const state = new Proxy(initial, {
    set(target, property, value) {
      const result = Reflect.set(target, property, value);
      if (subscribers.has(property)) {
        subscribers.get(property).forEach((callback) => callback(value));
      }
      return result;
    },
  });

  return {
    state,
    subscribe(property, callback) {
      if (!subscribers.has(property)) {
        subscribers.set(property, new Set());
      }
      subscribers.get(property).add(callback);
      return () => subscribers.get(property).delete(callback);
    },
  };
}

// Now let's create reactive behaviors
const withReactiveData = (initialData) => (state) => {
  const { state: reactiveState, subscribe } = createReactiveState(initialData);

  return {
    data: reactiveState,
    onDataChange(property, callback) {
      return subscribe(property, callback);
    },
  };
};

const withComputedProperties = (state) => ({
  computed(deps, compute) {
    let result = compute(deps.map((dep) => state.data[dep]));

    deps.forEach((dep) => {
      state.onDataChange(dep, () => {
        result = compute(deps.map((dep) => state.data[dep]));
      });
    });

    return result;
  },
});

// Using reactive composition in a component
function createUserProfile(userId) {
  return createComponent(
    "UserProfile",
    withReactiveData({
      firstName: "",
      lastName: "",
      age: 0,
    }),
    withComputedProperties,
    (state) => ({
      displayName: state.computed(
        ["firstName", "lastName"],
        ([first, last]) => `${first} ${last}`
      ),
      isAdult: state.computed(["age"], ([age]) => age >= 18),
    })
  );
}

// Usage
const profile = createUserProfile(1);
profile.data.firstName = "John";
profile.data.lastName = "Doe";
profile.data.age = 25;

console.log(profile.displayName); // "John Doe"
console.log(profile.isAdult); // true
```

## 3. Async Behavior Composition

Let's build a system for handling asynchronous operations with proper loading states and error handling:

```javascript
const withAsync = (state) => ({
  async executeAsync(action) {
    state.loading = true;
    state.error = null;

    try {
      const result = await action();
      state.data = result;
      return result;
    } catch (error) {
      state.error = error;
      throw error;
    } finally {
      state.loading = false;
    }
  },
});

const withRetry =
  (maxAttempts = 3) =>
  (state) => ({
    async executeWithRetry(action) {
      let attempts = 0;
      let lastError;

      while (attempts < maxAttempts) {
        try {
          return await state.executeAsync(action);
        } catch (error) {
          lastError = error;
          attempts++;
          await new Promise((resolve) => setTimeout(resolve, 1000 * attempts));
        }
      }

      throw new Error(`Failed after ${attempts} attempts: ${lastError}`);
    },
  });

// Using async composition in a data fetcher
function createDataFetcher() {
  return createComponent("DataFetcher", withAsync, withRetry(3), (state) => ({
    async fetchUserData(userId) {
      return state.executeWithRetry(async () => {
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error("Failed to fetch");
        return response.json();
      });
    },
  }));
}
```

## 4. Event-Driven Composition

Let's create a system where components can communicate through events:

```javascript
const withEvents = (state) => {
  const listeners = new Map();

  return {
    emit(event, data) {
      if (listeners.has(event)) {
        listeners.get(event).forEach((callback) => callback(data));
      }
    },

    on(event, callback) {
      if (!listeners.has(event)) {
        listeners.set(event, new Set());
      }
      listeners.get(event).add(callback);
      return () => listeners.get(event).delete(callback);
    },
  };
};

// Using events in a chat application
function createChatRoom() {
  return createComponent(
    "ChatRoom",
    withEvents,
    withDataFetching(fetch),
    (state) => ({
      sendMessage(message) {
        state.emit("messageSent", message);
        return state.fetchData("/api/messages", {
          method: "POST",
          body: JSON.stringify(message),
        });
      },

      onNewMessage(callback) {
        return state.on("messageReceived", callback);
      },
    })
  );
}
```

# Advanced Object Composition Patterns

## 1. Dependency Injection with Composition

Let's say we're building a dashboard application that needs to handle data fetching, caching, and error handling. Instead of hardcoding these dependencies, we can inject them:

```javascript
// First, let's create our core behaviors
const withDataFetching = (fetchImplementation) => (state) => ({
  async fetchData(endpoint) {
    state.loading = true;
    try {
      const data = await fetchImplementation(endpoint);
      state.data = data;
      return data;
    } catch (error) {
      state.error = error;
      throw error;
    } finally {
      state.loading = false;
    }
  },
});

const withCache = (cacheImplementation) => (state) => ({
  async getCached(key) {
    let data = await cacheImplementation.get(key);
    if (!data) {
      data = await state.fetchData(key);
      await cacheImplementation.set(key, data);
    }
    return data;
  },
});

const withErrorHandling = (errorHandler) => (state) => ({
  async executeWithErrors(action) {
    try {
      return await action();
    } catch (error) {
      errorHandler(error);
      state.lastError = error;
    }
  },
});

// Now let's create a dashboard component with these behaviors
function createDashboard(dependencies) {
  const { fetcher, cache, errorHandler } = dependencies;

  return createComponent(
    "Dashboard",
    withDataFetching(fetcher),
    withCache(cache),
    withErrorHandling(errorHandler),
    (state) => ({
      async loadUserData(userId) {
        return state.executeWithErrors(async () => {
          return state.getCached(`/users/${userId}`);
        });
      },
    })
  );
}

// Usage with different implementations
const productionDashboard = createDashboard({
  fetcher: fetch,
  cache: redisCache,
  errorHandler: sendToSentry,
});

const testDashboard = createDashboard({
  fetcher: mockFetch,
  cache: inMemoryCache,
  errorHandler: console.error,
});
```

## 2. Reactive Composition

Let's build a reactive system that automatically updates when dependencies change:

```javascript
// First, let's create a reactive state manager
function createReactiveState(initial = {}) {
  const subscribers = new Map();
  const state = new Proxy(initial, {
    set(target, property, value) {
      const result = Reflect.set(target, property, value);
      if (subscribers.has(property)) {
        subscribers.get(property).forEach((callback) => callback(value));
      }
      return result;
    },
  });

  return {
    state,
    subscribe(property, callback) {
      if (!subscribers.has(property)) {
        subscribers.set(property, new Set());
      }
      subscribers.get(property).add(callback);
      return () => subscribers.get(property).delete(callback);
    },
  };
}

// Now let's create reactive behaviors
const withReactiveData = (initialData) => (state) => {
  const { state: reactiveState, subscribe } = createReactiveState(initialData);

  return {
    data: reactiveState,
    onDataChange(property, callback) {
      return subscribe(property, callback);
    },
  };
};

const withComputedProperties = (state) => ({
  computed(deps, compute) {
    let result = compute(deps.map((dep) => state.data[dep]));

    deps.forEach((dep) => {
      state.onDataChange(dep, () => {
        result = compute(deps.map((dep) => state.data[dep]));
      });
    });

    return result;
  },
});

// Using reactive composition in a component
function createUserProfile(userId) {
  return createComponent(
    "UserProfile",
    withReactiveData({
      firstName: "",
      lastName: "",
      age: 0,
    }),
    withComputedProperties,
    (state) => ({
      displayName: state.computed(
        ["firstName", "lastName"],
        ([first, last]) => `${first} ${last}`
      ),
      isAdult: state.computed(["age"], ([age]) => age >= 18),
    })
  );
}

// Usage
const profile = createUserProfile(1);
profile.data.firstName = "John";
profile.data.lastName = "Doe";
profile.data.age = 25;

console.log(profile.displayName); // "John Doe"
console.log(profile.isAdult); // true
```

## 3. Async Behavior Composition

Let's build a system for handling asynchronous operations with proper loading states and error handling:

```javascript
const withAsync = (state) => ({
  async executeAsync(action) {
    state.loading = true;
    state.error = null;

    try {
      const result = await action();
      state.data = result;
      return result;
    } catch (error) {
      state.error = error;
      throw error;
    } finally {
      state.loading = false;
    }
  },
});

const withRetry =
  (maxAttempts = 3) =>
  (state) => ({
    async executeWithRetry(action) {
      let attempts = 0;
      let lastError;

      while (attempts < maxAttempts) {
        try {
          return await state.executeAsync(action);
        } catch (error) {
          lastError = error;
          attempts++;
          await new Promise((resolve) => setTimeout(resolve, 1000 * attempts));
        }
      }

      throw new Error(`Failed after ${attempts} attempts: ${lastError}`);
    },
  });

// Using async composition in a data fetcher
function createDataFetcher() {
  return createComponent("DataFetcher", withAsync, withRetry(3), (state) => ({
    async fetchUserData(userId) {
      return state.executeWithRetry(async () => {
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error("Failed to fetch");
        return response.json();
      });
    },
  }));
}
```

## 4. Event-Driven Composition

Let's create a system where components can communicate through events:

```javascript
const withEvents = (state) => {
  const listeners = new Map();

  return {
    emit(event, data) {
      if (listeners.has(event)) {
        listeners.get(event).forEach((callback) => callback(data));
      }
    },

    on(event, callback) {
      if (!listeners.has(event)) {
        listeners.set(event, new Set());
      }
      listeners.get(event).add(callback);
      return () => listeners.get(event).delete(callback);
    },
  };
};

// Using events in a chat application
function createChatRoom() {
  return createComponent(
    "ChatRoom",
    withEvents,
    withDataFetching(fetch),
    (state) => ({
      sendMessage(message) {
        state.emit("messageSent", message);
        return state.fetchData("/api/messages", {
          method: "POST",
          body: JSON.stringify(message),
        });
      },

      onNewMessage(callback) {
        return state.on("messageReceived", callback);
      },
    })
  );
}
```

TODO:

1. Show how these patterns are used in real production applications?
2. Explain how to test components built with these patterns?
3. Cover performance optimization techniques?
4. Continue with memory management and TypeScript integration?
