# Senior-JavaScript-ReactJS-UI-Developer-Interview-Guide
# Senior JavaScript & ReactJS UI Developer Interview Guide

## Table of Contents
1. [Core JavaScript & TypeScript Fundamentals](#1-core-javascript--typescript-fundamentals)
2. [React Core + Advanced Concepts](#2-react-core--advanced-concepts)
3. [Micro-frontend Architecture](#3-micro-frontend-architecture)
4. [Hands-on Coding Scenarios](#4-hands-on-coding-scenarios)

---

## 1. Core JavaScript & TypeScript Fundamentals

### 1.1 Event Loop, Call Stack, and Async/Sync Execution

#### Q1: Explain the JavaScript Event Loop and how it handles async operations. What will be the output of this code?

```javascript
console.log('Start');

setTimeout(() => {
  console.log('setTimeout');
}, 0);

Promise.resolve()
  .then(() => {
    console.log('Promise 1');
  })
  .then(() => {
    console.log('Promise 2');
  });

console.log('End');
```

**Answer:**
Output:
```
Start
End
Promise 1
Promise 2
setTimeout
```

**Explanation:**
- **Synchronous Code First**: Console logs for 'Start' and 'End' execute immediately on the call stack
- **Microtask Queue vs Macrotask Queue**: 
  - Promises (`.then()`) are microtasks
  - `setTimeout()` is a macrotask
- **Event Loop Execution Order**:
  1. Execute all synchronous code (Call Stack)
  2. Execute all microtasks (Promises, `queueMicrotask()`, `MutationObserver`)
  3. Execute one macrotask (`setTimeout`)
  4. Repeat

The event loop checks: Call Stack → Microtask Queue → Macrotask Queue → Repeat

---

#### Q2: What's the difference between `Promise`, `async/await`, and callbacks? When would you use each?

**Answer:**

**Callbacks:**
```javascript
function fetchData(callback) {
  setTimeout(() => {
    callback(null, { data: 'success' });
  }, 1000);
}

fetchData((err, data) => {
  if (err) console.error(err);
  console.log(data);
});
```
- **Pros**: Simple for basic operations
- **Cons**: Callback hell, error handling difficult, hard to read

**Promises:**
```javascript
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({ data: 'success' });
    }, 1000);
  });
}

fetchData()
  .then(data => console.log(data))
  .catch(err => console.error(err));
```
- **Pros**: Better error handling, chaining, readable
- **Cons**: Still can have `.then()` chains

**Async/Await:**
```javascript
async function getData() {
  try {
    const data = await fetchData();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}

getData();
```
- **Pros**: Looks synchronous, easier to read, better debugging
- **Cons**: Requires understanding of Promises underneath

**When to use:**
- **Callbacks**: Legacy code, simple operations
- **Promises**: When you need flexibility or chaining
- **Async/Await**: Modern approach, preferred for new code

---

#### Q3: Explain the difference between `var`, `let`, and `const`. Why should we avoid `var`?

**Answer:**

| Feature | var | let | const |
|---------|-----|-----|-------|
| Scope | Function | Block | Block |
| Hoisting | Yes (undefined) | Yes (TDZ) | Yes (TDZ) |
| Reassignment | Yes | Yes | No |
| Redeclaration | Yes | No | No |
| Temporal Dead Zone (TDZ) | No | Yes | Yes |

```javascript
// var - Function scoped
function test1() {
  if (true) {
    var x = 1;
  }
  console.log(x); // 1 (accessible outside block)
}

// let - Block scoped
function test2() {
  if (true) {
    let y = 1;
  }
  console.log(y); // ReferenceError: y is not defined
}

// Hoisting difference
console.log(a); // undefined (hoisted as undefined)
var a = 1;

console.log(b); // ReferenceError: Cannot access 'b' before initialization
let b = 2;
```

**Why avoid `var`:**
- Function scoping causes unexpected behavior
- Redeclaration issues
- Hoisting confusion (undefined state)
- Use `const` by default, `let` if reassignment needed

---

#### Q4: What are ES6+ features every developer should know?

**Answer:**

**1. Arrow Functions:**
```javascript
// Traditional
const add = function(a, b) { return a + b; };

// Arrow function
const add = (a, b) => a + b;

// Important: Arrow functions have no 'this' of their own
const obj = {
  name: 'Alice',
  greet: () => {
    console.log(this.name); // undefined (no this binding)
  }
};
```

**2. Destructuring:**
```javascript
// Object destructuring
const { name, age } = { name: 'Alice', age: 25 };

// Array destructuring
const [first, second] = [1, 2, 3];

// Rest operator
const [first, ...rest] = [1, 2, 3];
console.log(rest); // [2, 3]
```

**3. Template Literals:**
```javascript
const name = 'Alice';
const greeting = `Hello, ${name}!`;
```

**4. Classes:**
```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    return `Hello, ${this.name}`;
  }
}
```

**5. Spread Operator:**
```javascript
const arr1 = [1, 2];
const arr2 = [...arr1, 3, 4]; // [1, 2, 3, 4]

const obj1 = { a: 1 };
const obj2 = { ...obj1, b: 2 }; // { a: 1, b: 2 }
```

**6. Default Parameters:**
```javascript
const greet = (name = 'Guest') => `Hello, ${name}`;
greet(); // "Hello, Guest"
```

**7. Modules (Import/Export):**
```javascript
// Export
export const add = (a, b) => a + b;
export default class User { }

// Import
import User, { add } from './module.js';
```

---

### 1.2 TypeScript Core Concepts

#### Q5: What are the key benefits of TypeScript? How would you set it up in a React project?

**Answer:**

**Key Benefits:**
1. **Type Safety**: Catch errors at compile time, not runtime
2. **Better IDE Support**: Autocomplete, refactoring
3. **Self-Documenting**: Types serve as documentation
4. **Refactoring Safety**: Know what breaks immediately
5. **Scalability**: Better for large codebases

**Setup in React:**

```bash
# Create React app with TypeScript
npx create-react-app my-app --template typescript

# Or with Vite
npm create vite@latest my-app -- --template react-ts
```

**Basic TypeScript Types in React:**

```typescript
// Interface for Props
interface UserProps {
  name: string;
  age: number;
  email?: string; // Optional
  onAction: (id: number) => void;
}

// Component with TypeScript
const User: React.FC<UserProps> = ({ name, age, email, onAction }) => {
  return (
    <div>
      <h1>{name}</h1>
      <p>Age: {age}</p>
      {email && <p>Email: {email}</p>}
      <button onClick={() => onAction(1)}>Action</button>
    </div>
  );
};
```

---

#### Q6: Explain generics in TypeScript with a React example.

**Answer:**

Generics allow you to write reusable code that works with different types.

```typescript
// Generic function
function getFirstElement<T>(arr: T[]): T {
  return arr[0];
}

const num = getFirstElement([1, 2, 3]); // number
const str = getFirstElement(['a', 'b']); // string

// Generic interface
interface ApiResponse<T> {
  status: number;
  data: T;
  error?: string;
}

// Generic React component
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

const List = <T,>({ items, renderItem }: ListProps<T>) => (
  <ul>
    {items.map((item, idx) => (
      <li key={idx}>{renderItem(item)}</li>
    ))}
  </ul>
);

// Usage
<List<string>
  items={['Apple', 'Banana']}
  renderItem={(item) => <span>{item}</span>}
/>
```

---

#### Q7: What's the difference between `interface` and `type` in TypeScript?

**Answer:**

| Feature | interface | type |
|---------|-----------|------|
| Extension | `extends` | `&` (intersection) |
| Declaration Merging | Yes | No |
| Union Types | No | Yes |
| Performance | Slightly better | N/A |

```typescript
// Interface
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

// Type
type Animal = {
  name: string;
};

type Dog = Animal & {
  breed: string;
};

// Union types (only with type)
type Status = 'pending' | 'success' | 'error';

// Declaration merging (only with interface)
interface User {
  name: string;
}

interface User {
  age: number;
}

// Now User has both properties
```

**Best Practice**: Use `interface` for object shapes, `type` for unions and complex types.

---

#### Q8: Explain the concept of "Temporal Dead Zone (TDZ)" in JavaScript.

**Answer:**

TDZ is the period between entering a scope and a variable being declared where it cannot be accessed.

```javascript
// With var - NO TDZ
console.log(a); // undefined (hoisted)
var a = 5;

// With let/const - HAS TDZ
console.log(b); // ReferenceError: Cannot access 'b' before initialization
let b = 5;

// Example showing TDZ
function example() {
  // ← TDZ starts here for 'x'
  // x is not accessible
  
  let x = 10; // ← TDZ ends here
  
  console.log(x); // 10
}

// Common TDZ gotcha with closures
function outer() {
  console.log(typeof inner); // ReferenceError!
  
  let inner = () => {};
}

// TDZ with for loops
for (let i = 0; i < 3; i++) {
  // Each iteration has its own binding
  setTimeout(() => console.log(i), 100);
}
// Output: 0, 1, 2 (with let)
// Output: 3, 3, 3 (if using var)
```

**Why it matters:**
- Forces you to declare before use (better code quality)
- Prevents hoisting bugs
- Improves code predictability

---

#### Q9: What are Promises? How do they differ from callbacks and async/await?

**Answer:**

```javascript
// Callback approach (pyramd of doom)
function fetchUserCallback(userId, callback) {
  setTimeout(() => {
    callback(null, { id: userId, name: 'Alice' });
  }, 1000);
}

fetchUserCallback(1, (err, user) => {
  if (err) console.error(err);
  fetchUserCallback(user.id, (err, profile) => {
    if (err) console.error(err);
    console.log(profile);
  });
});

// Promise approach (chaining)
function fetchUserPromise(userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (userId > 0) {
        resolve({ id: userId, name: 'Alice' });
      } else {
        reject(new Error('Invalid ID'));
      }
    }, 1000);
  });
}

fetchUserPromise(1)
  .then(user => fetchUserPromise(user.id))
  .then(profile => console.log(profile))
  .catch(err => console.error(err));

// Async/await approach (looks synchronous)
async function getUser() {
  try {
    const user = await fetchUserPromise(1);
    const profile = await fetchUserPromise(user.id);
    console.log(profile);
  } catch (err) {
    console.error(err);
  }
}

getUser();

// Promise states
const p = new Promise((resolve, reject) => {
  // pending initially
  resolve('success'); // fulfilled
  // reject('error'); // rejected
});

// Promise methods
Promise.all([p1, p2, p3]); // All succeed or fail
Promise.race([p1, p2, p3]); // First to complete
Promise.allSettled([p1, p2, p3]); // All complete (success/fail)
Promise.any([p1, p2, p3]); // First to succeed
```

---

#### Q10: Explain `this` binding in JavaScript. Show all methods of binding.

**Answer:**

```javascript
// 1. Default binding (global/undefined in strict mode)
function greet() {
  console.log(this);
}
greet(); // Window (non-strict) or undefined (strict)

// 2. Implicit binding (object method)
const person = {
  name: 'Alice',
  greet() {
    console.log(this.name); // Alice
  }
};
person.greet();

// 3. Explicit binding with call, apply, bind
function introduce(age, city) {
  console.log(`${this.name}, ${age}, from ${city}`);
}

const alice = { name: 'Alice' };

// call - immediate execution
introduce.call(alice, 25, 'NYC'); // Alice, 25, from NYC

// apply - immediate execution with array
introduce.apply(alice, [25, 'NYC']); // Alice, 25, from NYC

// bind - returns new function
const boundIntroduce = introduce.bind(alice);
boundIntroduce(25, 'NYC'); // Alice, 25, from NYC

// 4. Constructor binding (new keyword)
function Person(name) {
  this.name = name; // 'this' refers to new instance
}
const p = new Person('Bob');
console.log(p.name); // Bob

// 5. Arrow functions (lexical this)
const obj = {
  name: 'Alice',
  regular: function() {
    console.log(this.name); // Alice (implicit binding)
    
    const arrow = () => {
      console.log(this.name); // Alice (lexical this)
    };
    arrow();
  },
  arrow: () => {
    console.log(this.name); // undefined (this from outer scope)
  }
};

obj.regular();
obj.arrow();

// Common pitfall
class Counter {
  constructor() {
    this.count = 0;
  }

  // ❌ Problem: 'this' lost when passed as callback
  increment() {
    this.count++;
  }

  // ✅ Solution 1: Arrow function
  incrementArrow = () => {
    this.count++;
  };

  // ✅ Solution 2: Bind in constructor
  incrementBound = this.incrementBound.bind(this);

  incrementBound() {
    this.count++;
  }
}

const counter = new Counter();
setTimeout(counter.increment, 1000); // 'this' is undefined
setTimeout(counter.incrementArrow, 1000); // Works fine
```

**Binding Priority (high to low):**
1. new binding
2. Explicit binding (call, apply, bind)
3. Implicit binding (object method)
4. Default binding (global/undefined)

---

## 2. React Core + Advanced Concepts

### 2.1 Component Lifecycle

#### Q8: Explain React lifecycle methods. How do they differ in Class vs Functional components?

**Answer:**

**Class Component Lifecycle:**

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  // Mounting Phase
  componentDidMount() {
    console.log('Component mounted');
    // Setup subscriptions, timers, fetch data
  }

  // Updating Phase
  componentDidUpdate(prevProps, prevState) {
    console.log('Component updated');
    if (prevState.count !== this.state.count) {
      console.log('Count changed');
    }
  }

  // Unmounting Phase
  componentWillUnmount() {
    console.log('Component unmounting');
    // Cleanup: remove subscriptions, timers
  }

  render() {
    return <div>{this.state.count}</div>;
  }
}
```

**Functional Component Lifecycle (using Hooks):**

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  // Replaces componentDidMount and componentDidUpdate
  useEffect(() => {
    console.log('Component mounted or updated');
    return () => {
      console.log('Cleanup before unmount or re-run');
    };
  }, [count]); // Dependency array

  // Only on mount
  useEffect(() => {
    console.log('Component mounted');
  }, []);

  // Only on unmount
  useEffect(() => {
    return () => {
      console.log('Component unmounting');
    };
  }, []);

  return <div>{count}</div>;
}
```

**Lifecycle Phases:**

1. **Mounting**: Constructor → render → componentDidMount/useEffect([], [])
2. **Updating**: New props/state → render → componentDidUpdate/useEffect([dependencies])
3. **Unmounting**: componentWillUnmount/useEffect cleanup

---

### 2.2 React Hooks

#### Q9: Explain React Hooks. What's the difference between `useState`, `useEffect`, and `useContext`?

**Answer:**

**useState - State Management:**
```javascript
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```
- **Purpose**: Add state to functional components
- **Returns**: [currentValue, setterFunction]
- **Initial Value**: Can be value or function

**useEffect - Side Effects:**
```javascript
useEffect(() => {
  console.log('Effect runs');

  return () => {
    console.log('Cleanup function');
  };
}, [dependency1, dependency2]);
```
- **Purpose**: Handle side effects (API calls, subscriptions, DOM updates)
- **Cleanup Function**: Runs before effect re-runs or on unmount
- **Dependency Array**:
  - `[]` - Runs once on mount
  - `[dep]` - Runs when `dep` changes
  - No array - Runs after every render (use carefully!)

**useContext - Prop Drilling Solution:**
```javascript
const ThemeContext = React.createContext();

function App() {
  return (
    <ThemeContext.Provider value={{ theme: 'dark' }}>
      <NestedComponent />
    </ThemeContext.Provider>
  );
}

function NestedComponent() {
  const { theme } = useContext(ThemeContext);
  return <div>Theme: {theme}</div>;
}
```
- **Purpose**: Share data across components without prop drilling
- **Downside**: Causes re-render of all consumers when value changes

---

#### Q10: How do you create a custom hook? Give a real-world example.

**Answer:**

```javascript
// Custom hook for fetching data
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        if (!response.ok) throw new Error('Fetch failed');
        const json = await response.json();
        setData(json);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserList() {
  const { data: users, loading, error } = useFetch('/api/users');

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <ul>
      {users?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

**Another Example - useLocalStorage Hook:**
```javascript
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

// Usage
function App() {
  const [name, setName] = useLocalStorage('name', 'Guest');
  return (
    <div>
      <p>Name: {name}</p>
      <button onClick={() => setName('Alice')}>Set Name</button>
    </div>
  );
}
```

---

#### Q11: What are uncontrolled vs controlled components? When should you use each?

**Answer:**

**Controlled Component (Recommended):**
```javascript
function ControlledForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    subscribe: false,
  });

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value,
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Submit:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="name"
        value={formData.name}
        onChange={handleChange}
        placeholder="Name"
      />
      <input
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <input
        name="subscribe"
        type="checkbox"
        checked={formData.subscribe}
        onChange={handleChange}
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

**Uncontrolled Component:**
```javascript
function UncontrolledForm() {
  const nameRef = useRef();
  const emailRef = useRef();
  const subscribeRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    const formData = {
      name: nameRef.current.value,
      email: emailRef.current.value,
      subscribe: subscribeRef.current.checked,
    };
    console.log('Submit:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={nameRef} placeholder="Name" />
      <input ref={emailRef} placeholder="Email" />
      <input ref={subscribeRef} type="checkbox" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

**Comparison:**

| Feature | Controlled | Uncontrolled |
|---------|-----------|--------------|
| State | React state | DOM |
| Predictability | ✅ Highly predictable | ❌ Less predictable |
| Validation | ✅ Easy | ⚠️ Complex |
| Conditional Logic | ✅ Easy | ❌ Hard |
| Testing | ✅ Easy | ⚠️ Requires DOM |
| Performance | ⚠️ Extra re-renders | ✅ Better |

**When to use:**
- **Controlled**: Most cases (forms with validation, conditional rendering)
- **Uncontrolled**: Integrating with non-React code, file inputs

---

#### Q12: How do you handle form validation in React? Show custom validation patterns.

**Answer:**

```javascript
// Simple validation
function useFormValidation(initialValues, validate, onSubmit) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
  };

  const handleBlur = (e) => {
    const { name } = e.target;
    setTouched(prev => ({ ...prev, [name]: true }));
    
    const fieldErrors = validate(values);
    setErrors(fieldErrors);
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    
    const newErrors = validate(values);
    setErrors(newErrors);
    
    if (Object.keys(newErrors).length === 0) {
      setIsSubmitting(true);
      try {
        await onSubmit(values);
      } finally {
        setIsSubmitting(false);
      }
    }
  };

  return {
    values,
    errors,
    touched,
    isSubmitting,
    handleChange,
    handleBlur,
    handleSubmit,
    setFieldValue: (name, value) => 
      setValues(prev => ({ ...prev, [name]: value })),
  };
}

// Validation schema
const validateForm = (values) => {
  const errors = {};

  if (!values.email) {
    errors.email = 'Email is required';
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(values.email)) {
    errors.email = 'Invalid email format';
  }

  if (!values.password) {
    errors.password = 'Password is required';
  } else if (values.password.length < 8) {
    errors.password = 'Password must be at least 8 characters';
  } else if (!/[A-Z]/.test(values.password)) {
    errors.password = 'Password must contain uppercase letter';
  } else if (!/[0-9]/.test(values.password)) {
    errors.password = 'Password must contain number';
  }

  return errors;
};

// Form component
function LoginForm() {
  const form = useFormValidation(
    { email: '', password: '' },
    validateForm,
    async (values) => {
      const res = await fetch('/api/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(values),
      });
      if (!res.ok) throw new Error('Login failed');
    }
  );

  return (
    <form onSubmit={form.handleSubmit}>
      <div>
        <label>Email:</label>
        <input
          name="email"
          value={form.values.email}
          onChange={form.handleChange}
          onBlur={form.handleBlur}
        />
        {form.touched.email && form.errors.email && (
          <span className="error">{form.errors.email}</span>
        )}
      </div>

      <div>
        <label>Password:</label>
        <input
          type="password"
          name="password"
          value={form.values.password}
          onChange={form.handleChange}
          onBlur={form.handleBlur}
        />
        {form.touched.password && form.errors.password && (
          <span className="error">{form.errors.password}</span>
        )}
      </div>

      <button type="submit" disabled={form.isSubmitting}>
        {form.isSubmitting ? 'Logging in...' : 'Login'}
      </button>
    </form>
  );
}

// Using Formik library (production choice)
import { Formik, Form, Field, ErrorMessage } from 'formik';
import * as Yup from 'yup';

const validationSchema = Yup.object({
  email: Yup.string()
    .email('Invalid email')
    .required('Email is required'),
  password: Yup.string()
    .min(8, 'Min 8 characters')
    .required('Password is required'),
});

function LoginFormWithFormik() {
  return (
    <Formik
      initialValues={{ email: '', password: '' }}
      validationSchema={validationSchema}
      onSubmit={(values) => {
        console.log(values);
      }}
    >
      <Form>
        <Field name="email" type="email" />
        <ErrorMessage name="email" />

        <Field name="password" type="password" />
        <ErrorMessage name="password" />

        <button type="submit">Submit</button>
      </Form>
    </Formik>
  );
}
```

---

### 2.3 Refs and Advanced State Management

#### Q13: When should you use `useRef`? What's the difference between `useRef` and `useState`?

**Answer:**

| Feature | useRef | useState |
|---------|--------|----------|
| Trigger Re-render | No | Yes |
| Mutable | Yes | No (via setter) |
| Return Value | `.current` | [value, setter] |
| Persist Across Renders | Yes | Yes |
| Use Case | DOM access, timers | Component state |

**useRef Use Cases:**

```javascript
// 1. Access DOM directly
function TextInput() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus Input</button>
    </>
  );
}

// 2. Store mutable values that don't trigger re-render
function Stopwatch() {
  const intervalRef = useRef(null);
  const [seconds, setSeconds] = useState(0);

  const start = () => {
    intervalRef.current = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);
  };

  const stop = () => {
    clearInterval(intervalRef.current);
  };

  return (
    <>
      <p>{seconds}s</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </>
  );
}

// 3. Keep track of previous value
function Counter() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();

  useEffect(() => {
    prevCountRef.current = count;
  }, [count]);

  return (
    <div>
      <p>Now: {count}, Before: {prevCountRef.current}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

---

#### Q14: Explain Context API vs Redux. When would you use each?

**Answer:**

**Context API:**
```javascript
// Simple state management, prop drilling solution
const UserContext = React.createContext();

function App() {
  const [user, setUser] = useState(null);

  return (
    <UserContext.Provider value={{ user, setUser }}>
      <UserProfile />
    </UserContext.Provider>
  );
}

function UserProfile() {
  const { user, setUser } = useContext(UserContext);
  return <div>{user?.name}</div>;
}
```
- **Pros**: Built-in, simple, no external dependencies
- **Cons**: Performance issues with frequent updates, basic features

**Redux:**
```javascript
import { createSlice, configureStore } from '@reduxjs/toolkit';

const userSlice = createSlice({
  name: 'user',
  initialState: { data: null },
  reducers: {
    setUser: (state, action) => {
      state.data = action.payload;
    },
  },
});

const store = configureStore({
  reducer: { user: userSlice.reducer },
});

function App() {
  return (
    <Provider store={store}>
      <UserProfile />
    </Provider>
  );
}

function UserProfile() {
  const user = useSelector(state => state.user.data);
  const dispatch = useDispatch();

  return <div>{user?.name}</div>;
}
```
- **Pros**: Predictable state, time-travel debugging, middleware support
- **Cons**: Boilerplate, steeper learning curve

**When to Use:**

| Scenario | Use |
|----------|-----|
| Small app, simple state | Context API |
| Theme/Auth across app | Context API |
| Complex state, many updates | Redux |
| Time-travel debugging needed | Redux |
| Large team project | Redux |

---

### 2.4 Routing

#### Q15: Explain React Router. How does `react-router-dom` work? Build a multi-page app structure.

**Answer:**

```javascript
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/products/1">Product 1</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/products/:id" element={<ProductDetail />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

// Component with route parameters
function ProductDetail() {
  const { id } = useParams();
  const navigate = useNavigate();

  return (
    <div>
      <h1>Product {id}</h1>
      <button onClick={() => navigate(-1)}>Go Back</button>
    </div>
  );
}
```

**Key Concepts:**

1. **Route Guards (Protected Routes):**
```javascript
function ProtectedRoute({ children }) {
  const isAuthenticated = !!localStorage.getItem('token');
  return isAuthenticated ? children : <Navigate to="/login" />;
}

<Routes>
  <Route 
    path="/dashboard" 
    element={
      <ProtectedRoute>
        <Dashboard />
      </ProtectedRoute>
    } 
  />
</Routes>
```

2. **Nested Routes:**
```javascript
<Routes>
  <Route path="/admin" element={<AdminLayout />}>
    <Route path="users" element={<UserList />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>
```

3. **Query Parameters:**
```javascript
// URL: /products?category=electronics&sort=price
function Products() {
  const [searchParams] = useSearchParams();
  const category = searchParams.get('category');
  const sort = searchParams.get('sort');

  return <div>Category: {category}, Sort: {sort}</div>;
}
```

---

### 2.5 Scenario-Based Component Design

#### Q16: Design a reusable form component with validation. Show best practices.

**Answer:**

```javascript
// useForm custom hook
function useForm(initialValues, onSubmit) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
  };

  const handleBlur = (e) => {
    const { name } = e.target;
    setTouched(prev => ({ ...prev, [name]: true }));
  };

  const validate = () => {
    const newErrors = {};
    if (!values.email) newErrors.email = 'Email is required';
    if (!values.password) newErrors.password = 'Password is required';
    if (values.password && values.password.length < 8) {
      newErrors.password = 'Password must be at least 8 characters';
    }
    return newErrors;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const newErrors = validate();
    setErrors(newErrors);

    if (Object.keys(newErrors).length === 0) {
      onSubmit(values);
    }
  };

  return {
    values,
    errors,
    touched,
    handleChange,
    handleBlur,
    handleSubmit,
  };
}

// Form component
function LoginForm() {
  const { values, errors, touched, handleChange, handleBlur, handleSubmit } =
    useForm({ email: '', password: '' }, (data) => {
      console.log('Submitting:', data);
    });

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>Email:</label>
        <input
          type="email"
          name="email"
          value={values.email}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.email && errors.email && (
          <span className="error">{errors.email}</span>
        )}
      </div>

      <div>
        <label>Password:</label>
        <input
          type="password"
          name="password"
          value={values.password}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.password && errors.password && (
          <span className="error">{errors.password}</span>
        )}
      </div>

      <button type="submit">Login</button>
    </form>
  );
}
```

---

#### Q17: Create a reusable Modal component with TypeScript. Show composition patterns.

**Answer:**

```typescript
import React, { ReactNode, useEffect, useRef } from 'react';

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: ReactNode;
  footer?: ReactNode;
}

const Modal: React.FC<ModalProps> = ({
  isOpen,
  onClose,
  title,
  children,
  footer,
}) => {
  const modalRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const handleEscape = (e: KeyboardEvent) => {
      if (e.key === 'Escape') onClose();
    };

    if (isOpen) {
      window.addEventListener('keydown', handleEscape);
      document.body.style.overflow = 'hidden';
    }

    return () => {
      window.removeEventListener('keydown', handleEscape);
      document.body.style.overflow = 'unset';
    };
  }, [isOpen, onClose]);

  if (!isOpen) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div
        className="modal-content"
        onClick={(e) => e.stopPropagation()}
        ref={modalRef}
      >
        <header className="modal-header">
          <h2>{title}</h2>
          <button className="modal-close" onClick={onClose}>
            ×
          </button>
        </header>

        <section className="modal-body">{children}</section>

        {footer && <footer className="modal-footer">{footer}</footer>}
      </div>
    </div>
  );
};

// Usage
function App() {
  const [isOpen, setIsOpen] = React.useState(false);

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Open Modal</button>

      <Modal
        isOpen={isOpen}
        onClose={() => setIsOpen(false)}
        title="Confirm Action"
        footer={
          <>
            <button onClick={() => setIsOpen(false)}>Cancel</button>
            <button onClick={() => setIsOpen(false)}>Confirm</button>
          </>
        }
      >
        <p>Are you sure you want to proceed?</p>
      </Modal>
    </>
  );
}
```

---

#### Q18: What is the difference between Shallow and Deep rendering in React? When should you use each?

**Answer:**

**Shallow Rendering:**
```javascript
import { shallow } from 'enzyme';
import { ParentComponent } from './ParentComponent';

describe('Shallow Rendering', () => {
  it('renders only the component, not children', () => {
    const wrapper = shallow(<ParentComponent />);
    
    // Renders ParentComponent but not ChildComponent
    expect(wrapper.find('ChildComponent')).toHaveLength(1);
    // Can check props passed to child
    expect(wrapper.find('ChildComponent').prop('name')).toBe('test');
  });

  it('tests component in isolation', () => {
    const handleClick = jest.fn();
    const wrapper = shallow(<Button onClick={handleClick} />);
    
    wrapper.find('button').simulate('click');
    expect(handleClick).toHaveBeenCalled();
  });
});
```

**Deep Rendering (Full):**
```javascript
import { mount } from 'enzyme';
import { render } from '@testing-library/react';

describe('Deep/Full Rendering', () => {
  it('renders component and all children', () => {
    const wrapper = mount(<ParentComponent />);
    
    // Renders full tree
    expect(wrapper.find('ChildComponent').dive()).toExist();
  });

  it('tests user interactions with DOM', () => {
    render(<ParentComponent />);
    const input = screen.getByRole('textbox');
    
    fireEvent.change(input, { target: { value: 'test' } });
    expect(input.value).toBe('test');
  });
});
```

**Comparison:**

| Feature | Shallow | Deep |
|---------|---------|------|
| Speed | ✅ Fast | ❌ Slower |
| Isolation | ✅ Great | ⚠️ Mixed |
| Child Testing | ❌ No | ✅ Yes |
| DOM Interaction | ❌ No | ✅ Yes |
| Setup | ✅ Simple | ⚠️ Complex |

**When to use:**
- **Shallow**: Unit tests for single component logic
- **Deep**: Integration tests, user interaction tests

---

#### Q19: Explain React Fiber architecture. How does it improve performance?

**Answer:**

React Fiber is the new reconciliation engine introduced in React 16+.

**Why Fiber?**
- **Stack Reconciler (Old)**: Synchronous, blocks main thread, can't pause
- **Fiber (New)**: Asynchronous, can pause/resume, prioritizes updates

```javascript
// Conceptual understanding (internal to React)
const fiber = {
  component: MyComponent,
  props: { name: 'Alice' },
  state: { count: 0 },
  effectTag: 'UPDATE', // Placement, Update, Deletion
  hooks: [useState_hook, useEffect_hook],
  parent: parentFiber,
  child: firstChildFiber,
  sibling: nextSiblingFiber,
  alternate: previousFiber, // For comparison
};

// Fiber allows React to:
// 1. Break work into small units
// 2. Prioritize updates
// 3. Pause and resume rendering
// 4. Reuse previously completed work
```

**Practical Impact:**
```javascript
// With Fiber, this doesn't block UI
function ExpensiveComponent() {
  const items = Array.from({ length: 10000 }, (_, i) => i);

  return (
    <ul>
      {items.map(item => (
        <li key={item}>Item {item}</li>
      ))}
    </ul>
  );
}

// React can render this with interruptible rendering
// - Start rendering
- User clicks button
// - React pauses rendering
// - Process click
// - Resume rendering
```

**Priority-based Updates:**
```javascript
// React 18+ Concurrent Features
import { startTransition } from 'react';

function SearchUsers() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  const handleChange = (e) => {
    // Urgent update - typing in input
    setQuery(e.target.value);

    // Non-urgent update - filtering results
    startTransition(() => {
      const filtered = users.filter(u => 
        u.name.includes(e.target.value)
      );
      setResults(filtered);
    });
  };

  return (
    <>
      <input value={query} onChange={handleChange} />
      <Suspense fallback={<Loading />}>
        <Results results={results} />
      </Suspense>
    </>
  );
}
```

---

#### Q20: How do you optimize re-renders in large React applications?

**Answer:**

```javascript
// 1. Code splitting and lazy loading
const Dashboard = lazy(() => import('./Dashboard'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Dashboard />
    </Suspense>
  );
}

// 2. useMemo for expensive computations
function DataTable({ data, filter }) {
  const filteredData = useMemo(
    () => data.filter(item => item.category === filter),
    [data, filter]
  );

  return <Table data={filteredData} />;
}

// 3. useCallback for function memoization
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  return <Child onClick={handleClick} />;
}

// 4. React.memo for component memoization
const Child = React.memo(({ onClick }) => {
  console.log('Child rendered');
  return <button onClick={onClick}>Click</button>;
});

// 5. Virtual scrolling for long lists
import { FixedSizeList } from 'react-window';

function VirtualList({ items }) {
  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={35}
    >
      {({ index, style }) => (
        <div style={style}>{items[index]}</div>
      )}
    </FixedSizeList>
  );
}

// 6. State structure optimization
// ❌ Bad: Monolithic state
const [appState, setAppState] = useState({
  user: {},
  posts: [],
  comments: [],
  notifications: [],
});

// ✅ Good: Split state
const [user, setUser] = useState({});
const [posts, setPosts] = useState([]);
const [comments, setComments] = useState([]);
const [notifications, setNotifications] = useState([]);

// 7. Context splitting for better performance
<UserContext.Provider value={user}>
  <PostContext.Provider value={posts}>
    <App />
  </PostContext.Provider>
</UserContext.Provider>

// 8. Profiling with React DevTools
import { Profiler } from 'react';

function onRenderCallback(id, phase, actualDuration) {
  console.log(`${id} (${phase}) took ${actualDuration}ms`);
}

<Profiler id="MyComponent" onRender={onRenderCallback}>
  <MyComponent />
</Profiler>
```

---

## 3. Micro-frontend Architecture

### 3.1 Micro-frontend Concepts

#### Q21: What is a micro-frontend architecture? What are the benefits and challenges?

**Answer:**

**Definition**: Breaking a large frontend application into smaller, independently deployable units.

**Benefits:**
- **Scalability**: Teams can work independently
- **Technology Agnostic**: Use different frameworks (React, Vue, Angular)
- **Independent Deployment**: Deploy without coordinating entire app
- **Fault Isolation**: One micro-frontend failure doesn't crash entire app
- **Parallel Development**: Multiple teams develop simultaneously

**Challenges:**
- **Shared Dependencies**: Managing common libraries across micro-frontends
- **Version Conflicts**: Different versions of same library
- **Performance**: Loading multiple bundles
- **State Management**: Sharing state across boundaries
- **Testing**: Complex integration testing

---

#### Q22: How do you implement data sharing across micro-frontends?

**Answer:**

**Method 1: Event-Based Communication (Pub/Sub)**
```javascript
// Shared event bus
const eventBus = new EventTarget();

// Micro-frontend 1 (Header)
function Header() {
  const handleNotification = () => {
    eventBus.dispatchEvent(
      new CustomEvent('notificationCreated', {
        detail: { message: 'New notification' },
      })
    );
  };

  return <button onClick={handleNotification}>Create Notification</button>;
}

// Micro-frontend 2 (Notification Center)
function NotificationCenter() {
  const [notifications, setNotifications] = useState([]);

  useEffect(() => {
    const handleEvent = (e) => {
      setNotifications(prev => [...prev, e.detail]);
    };

    eventBus.addEventListener('notificationCreated', handleEvent);

    return () => {
      eventBus.removeEventListener('notificationCreated', handleEvent);
    };
  }, []);

  return <div>{notifications.length} notifications</div>;
}
```

**Method 2: Shared State Container (Redux/Context)**
```javascript
// Shared store accessible by all micro-frontends
import { createStore } from '@reduxjs/toolkit';

const globalStore = createStore(rootReducer);

// Container exposes the store
window.__GLOBAL_STORE__ = globalStore;

// Each micro-frontend accesses it
function App() {
  const store = window.__GLOBAL_STORE__;
  return (
    <Provider store={store}>
      <MicroFrontend />
    </Provider>
  );
}
```

**Method 3: URL Query Parameters**
```javascript
// Micro-frontend 1 navigates and passes data
function UserCard() {
  const handleSelect = (userId) => {
    window.location.href = `/?selectedUserId=${userId}`;
  };

  return <button onClick={() => handleSelect(123)}>Select User</button>;
}

// Micro-frontend 2 reads from URL
function UserDetail() {
  const [searchParams] = useSearchParams();
  const userId = searchParams.get('selectedUserId');

  useEffect(() => {
    if (userId) {
      fetchUserDetails(userId);
    }
  }, [userId]);

  return <div>User details for {userId}</div>;
}
```

**Method 4: Web Components & Custom Elements**
```javascript
// Micro-frontend 1: Define custom element
class UserCard extends HTMLElement {
  connectedCallback() {
    this.render();
  }

  handleSelect(userId) {
    this.dispatchEvent(
      new CustomEvent('userSelected', { detail: { userId } })
    );
  }

  render() {
    this.innerHTML = '<button>Select User</button>';
  }
}

customElements.define('user-card', UserCard);

// Micro-frontend 2: Listen to event
function UserDetail() {
  useEffect(() => {
    const handler = (e) => {
      console.log('User selected:', e.detail.userId);
    };

    window.addEventListener('userSelected', handler);
    return () => window.removeEventListener('userSelected', handler);
  }, []);

  return <div><user-card></user-card></div>;
}
```

**Method 5: Module Federation (Webpack 5)**
```javascript
// Shared config in webpack
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'header',
      filename: 'remoteEntry.js',
      exposes: {
        './store': './src/store',
      },
      shared: ['react', 'react-dom', 'redux'],
    }),
  ],
};

// Consumer micro-frontend
import store from 'header/store';
```

---

#### Q23: What are the deployment strategies for micro-frontends? Compare different approaches.

**Answer:**

**1. Build-Time Integration:**
```bash
# Each micro-frontend published as npm package
npm install @company/header-app @company/sidebar-app @company/footer-app
```

```javascript
// Shell application
import Header from '@company/header-app';
import Sidebar from '@company/sidebar-app';
import Footer from '@company/footer-app';

function App() {
  return (
    <>
      <Header />
      <Sidebar />
      <Footer />
    </>
  );
}
```

**Pros:** Simple, good type safety
**Cons:** Need rebuild/redeploy shell for any micro-frontend update

---

**2. Runtime Integration with Module Federation:**
```javascript
// Webpack 5 configuration
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'header',
      filename: 'remoteEntry.js',
      exposes: {
        './Header': './src/Header',
      },
      shared: {
        react: { singleton: true, requiredVersion: '^18.0.0' },
        'react-dom': { singleton: true, requiredVersion: '^18.0.0' },
      },
    }),
  ],
};
```

```javascript
// Shell app
const Header = React.lazy(() => import('header/Header'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Header />
    </Suspense>
  );
}
```

**Pros:** Independent deployment, shared dependencies
**Cons:** Complex setup, debugging harder

---

**3. Server-Side Integration (SSI):**
```html
<!-- Shell HTML -->
<html>
  <body>
    <!--#include virtual="/api/header-app" -->
    <!--#include virtual="/api/sidebar-app" -->
    <!--#include virtual="/api/footer-app" -->
  </body>
</html>
```

**Pros:** True independence, simpler for backends
**Cons:** Harder state management, slow

---

**4. iframe Integration:**
```javascript
function MicroFrontendIframe({ src, title }) {
  return <iframe src={src} title={title} style={{ width: '100%', height: '100%' }} />;
}

function App() {
  return (
    <>
      <MicroFrontendIframe src="/header-app" title="Header" />
      <MicroFrontendIframe src="/main-app" title="Main" />
      <MicroFrontendIframe src="/footer-app" title="Footer" />
    </>
  );
}
```

**Pros:** Complete isolation
**Cons:** CSS conflicts solved, but heavy, slow communication

---

**Comparison:**

| Strategy | Independence | Performance | Complexity | Type Safety |
|----------|-------------|-------------|-----------|------------|
| Build-time | Low | High | Low | ✅ High |
| Module Federation | High | Medium | High | Medium |
| SSI | Very High | Low | Medium | Low |
| iframe | Very High | Low | Medium | Low |

**Best Practice:** Module Federation for modern SPAs

---

#### Q24: What are common pitfalls in micro-frontend architecture and how do you avoid them?

**Answer:**

```javascript
// ❌ Pitfall 1: Shared state nightmare
// Each micro-frontend has its own Redux store
// They can't share state easily
const headerStore = createStore(headerReducer);
const mainStore = createStore(mainReducer);

// ✅ Solution: Global event bus or centralized store
const globalEventBus = new EventTarget();

// Micro-frontend A emits
globalEventBus.dispatchEvent(
  new CustomEvent('userLoggedIn', { detail: { userId: 123 } })
);

// Micro-frontend B listens
globalEventBus.addEventListener('userLoggedIn', (e) => {
  console.log('User logged in:', e.detail);
});

// ❌ Pitfall 2: Dependency duplication
// Each MFE bundles React separately = bloated
// Webpack file size: 200KB + 200KB + 200KB = 600KB

// ✅ Solution: Share common dependencies
new ModuleFederationPlugin({
  shared: {
    react: { singleton: true },
    'react-dom': { singleton: true },
    axios: { singleton: true },
  },
});
// Total size: ~200KB (React once) + small MFEs

// ❌ Pitfall 3: Version conflicts
// Header uses React 17, Main uses React 18
// Incompatible behavior

// ✅ Solution: Version pinning and compatibility
module.exports = {
  shared: {
    react: {
      singleton: true,
      requiredVersion: '^18.0.0',
      strictVersion: true,
    },
  },
};

// ❌ Pitfall 4: One MFE failure crashes shell
function App() {
  return (
    <>
      <Header /> {/* If this errors, whole app breaks */}
      <Main />
    </>
  );
}

// ✅ Solution: Error boundaries per MFE
function App() {
  return (
    <>
      <ErrorBoundary fallback={<HeaderFallback />}>
        <Header />
      </ErrorBoundary>
      
      <ErrorBoundary fallback={<MainFallback />}>
        <Main />
      </ErrorBoundary>
    </>
  );
}

// ❌ Pitfall 5: CSS conflicts
// Header uses .title, Main also uses .title
// Styling becomes unpredictable

// ✅ Solution: CSS-in-JS or namespace prefixes
const HeaderTitle = styled.h1`
  /* Scoped to component */
`;

// Or use BEM naming
<h1 className="header__title">Header</h1>
<h1 className="main__title">Main</h1>

// ❌ Pitfall 6: Poor communication between MFEs
// No clear contract, tight coupling

// ✅ Solution: Event-driven architecture with clear APIs
class MessageBus {
  static subscribe(event, callback) {
    document.addEventListener(event, callback);
  }

  static publish(event, data) {
    document.dispatchEvent(new CustomEvent(event, { detail: data }));
  }
}

// Header publishes
MessageBus.publish('auth:login', { userId: 123 });

// Main subscribes
MessageBus.subscribe('auth:login', (e) => {
  console.log('User logged in');
});
```

---

## 4. Hands-on Coding Scenarios

### Scenario 1: Build a Todo Application with Advanced Features

```javascript
import React, { useState, useCallback, useMemo } from 'react';

function TodoApp() {
  const [todos, setTodos] = useState([
    { id: 1, title: 'Learn React', completed: false },
    { id: 2, title: 'Build a project', completed: true },
  ]);
  const [filter, setFilter] = useState('all'); // all, active, completed
  const [newTodo, setNewTodo] = useState('');

  // Add todo
  const addTodo = useCallback((title) => {
    if (!title.trim()) return;

    const newItem = {
      id: Date.now(),
      title,
      completed: false,
    };

    setTodos(prev => [...prev, newItem]);
    setNewTodo('');
  }, []);

  // Toggle todo
  const toggleTodo = useCallback((id) => {
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  }, []);

  // Delete todo
  const deleteTodo = useCallback((id) => {
    setTodos(prev => prev.filter(todo => todo.id !== id));
  }, []);

  // Filter todos
  const filteredTodos = useMemo(() => {
    return todos.filter(todo => {
      if (filter === 'active') return !todo.completed;
      if (filter === 'completed') return todo.completed;
      return true;
    });
  }, [todos, filter]);

  const stats = useMemo(() => ({
    total: todos.length,
    completed: todos.filter(t => t.completed).length,
    active: todos.filter(t => !t.completed).length,
  }), [todos]);

  return (
    <div style={{ maxWidth: '500px', margin: '0 auto', padding: '20px' }}>
      <h1>Todo App</h1>

      {/* Stats */}
      <div style={{ marginBottom: '20px', padding: '10px', background: '#f0f0f0' }}>
        <p>Total: {stats.total} | Active: {stats.active} | Completed: {stats.completed}</p>
      </div>

      {/* Input */}
      <div style={{ marginBottom: '20px' }}>
        <input
          type="text"
          value={newTodo}
          onChange={(e) => setNewTodo(e.target.value)}
          onKeyPress={(e) => e.key === 'Enter' && addTodo(newTodo)}
          placeholder="Add a new todo..."
          style={{ width: '100%', padding: '10px' }}
        />
        <button onClick={() => addTodo(newTodo)} style={{ marginTop: '10px' }}>
          Add Todo
        </button>
      </div>

      {/* Filter */}
      <div style={{ marginBottom: '20px' }}>
        {['all', 'active', 'completed'].map(f => (
          <button
            key={f}
            onClick={() => setFilter(f)}
            style={{
              marginRight: '10px',
              fontWeight: filter === f ? 'bold' : 'normal',
            }}
          >
            {f.charAt(0).toUpperCase() + f.slice(1)}
          </button>
        ))}
      </div>

      {/* Todo List */}
      <ul style={{ listStyle: 'none', padding: 0 }}>
        {filteredTodos.map(todo => (
          <li
            key={todo.id}
            style={{
              display: 'flex',
              gap: '10px',
              padding: '10px',
              borderBottom: '1px solid #ddd',
              textDecoration: todo.completed ? 'line-through' : 'none',
            }}
          >
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <span style={{ flex: 1 }}>{todo.title}</span>
            <button onClick={() => deleteTodo(todo.id)}>Delete</button>
          </li>
        ))}
      </ul>

      {filteredTodos.length === 0 && <p>No todos found.</p>}
    </div>
  );
}

export default TodoApp;
```

---

### Scenario 2: Create a Data Table Component with Pagination & Sorting

```typescript
import React, { useState, useMemo } from 'react';

interface DataColumn {
  key: string;
  label: string;
  sortable?: boolean;
}

interface DataTableProps {
  data: any[];
  columns: DataColumn[];
  itemsPerPage?: number;
}

const DataTable: React.FC<DataTableProps> = ({
  data,
  columns,
  itemsPerPage = 10,
}) => {
  const [currentPage, setCurrentPage] = useState(1);
  const [sortKey, setSortKey] = useState<string | null>(null);
  const [sortOrder, setSortOrder] = useState<'asc' | 'desc'>('asc');

  // Sort data
  const sortedData = useMemo(() => {
    if (!sortKey) return data;

    return [...data].sort((a, b) => {
      const aVal = a[sortKey];
      const bVal = b[sortKey];

      if (aVal < bVal) return sortOrder === 'asc' ? -1 : 1;
      if (aVal > bVal) return sortOrder === 'asc' ? 1 : -1;
      return 0;
    });
  }, [data, sortKey, sortOrder]);

  // Paginate data
  const paginatedData = useMemo(() => {
    const startIdx = (currentPage - 1) * itemsPerPage;
    return sortedData.slice(startIdx, startIdx + itemsPerPage);
  }, [sortedData, currentPage, itemsPerPage]);

  const totalPages = Math.ceil(sortedData.length / itemsPerPage);

  const handleSort = (key: string) => {
    if (sortKey === key) {
      setSortOrder(sortOrder === 'asc' ? 'desc' : 'asc');
    } else {
      setSortKey(key);
      setSortOrder('asc');
    }
    setCurrentPage(1);
  };

  return (
    <div>
      <table style={{ width: '100%', borderCollapse: 'collapse' }}>
        <thead>
          <tr style={{ background: '#f5f5f5' }}>
            {columns.map(col => (
              <th
                key={col.key}
                onClick={() => col.sortable && handleSort(col.key)}
                style={{
                  padding: '10px',
                  textAlign: 'left',
                  cursor: col.sortable ? 'pointer' : 'default',
                  fontWeight: 'bold',
                }}
              >
                {col.label}
                {col.sortable && sortKey === col.key && (
                  <span> {sortOrder === 'asc' ? '↑' : '↓'}</span>
                )}
              </th>
            ))}
          </tr>
        </thead>
        <tbody>
          {paginatedData.map((row, idx) => (
            <tr key={idx} style={{ borderBottom: '1px solid #ddd' }}>
              {columns.map(col => (
                <td key={col.key} style={{ padding: '10px' }}>
                  {row[col.key]}
                </td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>

      {/* Pagination */}
      <div style={{ marginTop: '20px', display: 'flex', gap: '10px', justifyContent: 'center' }}>
        <button
          onClick={() => setCurrentPage(prev => Math.max(1, prev - 1))}
          disabled={currentPage === 1}
        >
          Previous
        </button>
        <span>
          Page {currentPage} of {totalPages}
        </span>
        <button
          onClick={() => setCurrentPage(prev => Math.min(totalPages, prev + 1))}
          disabled={currentPage === totalPages}
        >
          Next
        </button>
      </div>
    </div>
  );
};

// Usage
function App() {
  const users = [
    { id: 1, name: 'Alice', email: 'alice@example.com', age: 28 },
    { id: 2, name: 'Bob', email: 'bob@example.com', age: 32 },
    { id: 3, name: 'Charlie', email: 'charlie@example.com', age: 25 },
    // ... more users
  ];

  return (
    <DataTable
      data={users}
      columns={[
        { key: 'id', label: 'ID', sortable: true },
        { key: 'name', label: 'Name', sortable: true },
        { key: 'email', label: 'Email', sortable: true },
        { key: 'age', label: 'Age', sortable: true },
      ]}
      itemsPerPage={5}
    />
  );
}

export default App;
```

---

### Scenario 3: API Error Handling with Retry Logic

```javascript
function useAsync(asyncFunction, immediate = true) {
  const [status, setStatus] = useState('idle');
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);

  const execute = useCallback(async () => {
    setStatus('pending');
    setData(null);
    setError(null);

    try {
      const response = await asyncFunction();
      setData(response);
      setStatus('success');
      return response;
    } catch (err) {
      setError(err);
      setStatus('error');
      throw err;
    }
  }, [asyncFunction]);

  useEffect(() => {
    if (immediate) {
      execute();
    }
  }, [execute, immediate]);

  return { execute, status, data, error };
}

// Retry wrapper with exponential backoff
async function fetchWithRetry(url, options = {}, retries = 3) {
  const { delay = 1000, backoff = 2 } = options;

  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(url);
      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      return await response.json();
    } catch (err) {
      if (i === retries - 1) throw err;

      const waitTime = delay * Math.pow(backoff, i);
      await new Promise(resolve => setTimeout(resolve, waitTime));
    }
  }
}

// Usage
function UserList() {
  const { status, data: users, error, execute } = useAsync(
    () => fetchWithRetry('/api/users', { delay: 1000, backoff: 2 }),
    true
  );

  return (
    <div>
      {status === 'pending' && <p>Loading...</p>}
      {status === 'success' && (
        <ul>
          {users?.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
      {status === 'error' && (
        <div>
          <p>Error: {error?.message}</p>
          <button onClick={execute}>Retry</button>
        </div>
      )}
    </div>
  );
}
```

---

---

### Scenario 4: Build a Real-time Collaborative Editor

```javascript
import React, { useState, useEffect, useRef, useCallback } from 'react';

function CollaborativeEditor() {
  const [content, setContent] = useState('');
  const [collaborators, setCollaborators] = useState([]);
  const [cursorPositions, setCursorPositions] = useState({});
  const wsRef = useRef(null);
  const editorRef = useRef(null);

  // Connect to WebSocket
  useEffect(() => {
    wsRef.current = new WebSocket('ws://localhost:8080/editor');

    wsRef.current.onmessage = (event) => {
      const { type, data } = JSON.parse(event.data);

      if (type === 'contentUpdate') {
        setContent(data.content);
      } else if (type === 'cursorMove') {
        setCursorPositions(prev => ({
          ...prev,
          [data.userId]: data.position,
        }));
      } else if (type === 'collaboratorsUpdate') {
        setCollaborators(data);
      }
    };

    return () => wsRef.current?.close();
  }, []);

  // Send updates to other collaborators
  const handleChange = useCallback((e) => {
    const newContent = e.target.value;
    setContent(newContent);

    wsRef.current?.send(JSON.stringify({
      type: 'contentUpdate',
      data: { content: newContent },
    }));
  }, []);

  const handleCursorMove = useCallback((e) => {
    const position = e.target.selectionStart;

    wsRef.current?.send(JSON.stringify({
      type: 'cursorMove',
      data: { position },
    }));
  }, []);

  return (
    <div>
      <h2>Collaborative Editor</h2>
      <div>
        <h3>Collaborators ({collaborators.length}):</h3>
        {collaborators.map(collab => (
          <div key={collab.id}>
            {collab.name} - Position: {cursorPositions[collab.id] || 0}
          </div>
        ))}
      </div>

      <textarea
        ref={editorRef}
        value={content}
        onChange={handleChange}
        onKeyUp={handleCursorMove}
        style={{
          width: '100%',
          height: '400px',
          fontFamily: 'monospace',
          padding: '10px',
        }}
      />
    </div>
  );
}

export default CollaborativeEditor;
```

---

### Scenario 5: Build a Search Component with Advanced Features

```javascript
import React, { useState, useEffect, useRef, useCallback } from 'react';

function AdvancedSearch() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  const [selectedIndex, setSelectedIndex] = useState(-1);
  const [isOpen, setIsOpen] = useState(false);
  const abortRef = useRef(null);
  const debounceRef = useRef(null);

  // Debounced search
  const search = useCallback(async (searchQuery) => {
    if (!searchQuery.trim()) {
      setResults([]);
      return;
    }

    // Cancel previous request
    abortRef.current?.abort();
    abortRef.current = new AbortController();

    setLoading(true);
    try {
      const response = await fetch(
        `/api/search?q=${encodeURIComponent(searchQuery)}`,
        { signal: abortRef.current.signal }
      );

      if (!response.ok) throw new Error('Search failed');
      const data = await response.json();
      setResults(data);
      setSelectedIndex(-1);
    } catch (err) {
      if (err.name !== 'AbortError') {
        console.error(err);
      }
    } finally {
      setLoading(false);
    }
  }, []);

  // Debounce search input
  const handleInputChange = useCallback((e) => {
    const value = e.target.value;
    setQuery(value);
    setIsOpen(true);

    clearTimeout(debounceRef.current);
    debounceRef.current = setTimeout(() => {
      search(value);
    }, 300);
  }, [search]);

  // Keyboard navigation
  const handleKeyDown = useCallback((e) => {
    switch (e.key) {
      case 'ArrowDown':
        e.preventDefault();
        setSelectedIndex(prev =>
          prev === results.length - 1 ? 0 : prev + 1
        );
        break;

      case 'ArrowUp':
        e.preventDefault();
        setSelectedIndex(prev =>
          prev === 0 ? results.length - 1 : prev - 1
        );
        break;

      case 'Enter':
        e.preventDefault();
        if (selectedIndex >= 0) {
          handleSelectResult(results[selectedIndex]);
        }
        break;

      case 'Escape':
        setIsOpen(false);
        break;

      default:
        break;
    }
  }, [results, selectedIndex]);

  const handleSelectResult = (result) => {
    console.log('Selected:', result);
    setQuery(result.name);
    setIsOpen(false);
  };

  useEffect(() => {
    return () => {
      clearTimeout(debounceRef.current);
      abortRef.current?.abort();
    };
  }, []);

  return (
    <div className="search-container">
      <input
        type="text"
        value={query}
        onChange={handleInputChange}
        onKeyDown={handleKeyDown}
        onFocus={() => setIsOpen(true)}
        placeholder="Search..."
        style={{ width: '300px', padding: '10px' }}
      />

      {loading && <div>Loading...</div>}

      {isOpen && results.length > 0 && (
        <ul
          className="dropdown"
          style={{
            border: '1px solid #ccc',
            maxHeight: '300px',
            overflow: 'auto',
            listStyle: 'none',
            margin: 0,
            padding: 0,
          }}
        >
          {results.map((result, index) => (
            <li
              key={result.id}
              onClick={() => handleSelectResult(result)}
              style={{
                padding: '10px',
                background: selectedIndex === index ? '#e0e0e0' : 'white',
                cursor: 'pointer',
              }}
            >
              {result.name}
            </li>
          ))}
        </ul>
      )}

      {isOpen && results.length === 0 && query && !loading && (
        <div style={{ padding: '10px', color: '#999' }}>
          No results found
        </div>
      )}
    </div>
  );
}

export default AdvancedSearch;
```

---

### Scenario 6: Build a Notification System with Priority Queue

```javascript
import React, { useState, useCallback, useRef } from 'react';

// Notification manager
class NotificationManager {
  constructor() {
    this.notifications = [];
    this.subscribers = [];
    this.idCounter = 0;
  }

  subscribe(callback) {
    this.subscribers.push(callback);
    return () => {
      this.subscribers = this.subscribers.filter(cb => cb !== callback);
    };
  }

  notify(message, options = {}) {
    const {
      type = 'info',
      duration = 3000,
      priority = 0,
    } = options;

    const id = this.idCounter++;
    const notification = { id, message, type, priority };

    this.notifications.push(notification);
    this.notifications.sort((a, b) => b.priority - a.priority);

    this.subscribers.forEach(cb => cb([...this.notifications]));

    if (duration > 0) {
      setTimeout(() => this.remove(id), duration);
    }

    return id;
  }

  remove(id) {
    this.notifications = this.notifications.filter(n => n.id !== id);
    this.subscribers.forEach(cb => cb([...this.notifications]));
  }

  clear() {
    this.notifications = [];
    this.subscribers.forEach(cb => cb([]));
  }
}

const manager = new NotificationManager();

function NotificationProvider({ children }) {
  const [notifications, setNotifications] = useState([]);

  React.useEffect(() => {
    return manager.subscribe(setNotifications);
  }, []);

  return (
    <>
      <NotificationContext.Provider value={manager}>
        {children}
      </NotificationContext.Provider>
      <NotificationContainer notifications={notifications} />
    </>
  );
}

function NotificationContainer({ notifications }) {
  return (
    <div style={{ position: 'fixed', top: '10px', right: '10px' }}>
      {notifications.map(notification => (
        <NotificationItem key={notification.id} notification={notification} />
      ))}
    </div>
  );
}

function NotificationItem({ notification }) {
  const [isExiting, setIsExiting] = useState(false);

  const handleClose = () => {
    setIsExiting(true);
    setTimeout(() => {
      manager.remove(notification.id);
    }, 300);
  };

  const colors = {
    success: '#4caf50',
    error: '#f44336',
    warning: '#ff9800',
    info: '#2196f3',
  };

  return (
    <div
      style={{
        background: colors[notification.type] || colors.info,
        color: 'white',
        padding: '15px',
        margin: '10px 0',
        borderRadius: '4px',
        opacity: isExiting ? 0 : 1,
        transition: 'opacity 0.3s',
      }}
    >
      <div style={{ display: 'flex', justifyContent: 'space-between' }}>
        <span>{notification.message}</span>
        <button
          onClick={handleClose}
          style={{
            background: 'none',
            border: 'none',
            color: 'white',
            cursor: 'pointer',
            fontSize: '18px',
          }}
        >
          ×
        </button>
      </div>
    </div>
  );
}

// Usage
const NotificationContext = React.createContext();

function useNotification() {
  return React.useContext(NotificationContext);
}

function App() {
  const notify = useNotification();

  return (
    <div>
      <button
        onClick={() =>
          notify.notify('Success!', { type: 'success', priority: 1 })
        }
      >
        Show Success (High Priority)
      </button>

      <button
        onClick={() =>
          notify.notify('Error occurred', { type: 'error', priority: 2 })
        }
      >
        Show Error (Highest Priority)
      </button>

      <button
        onClick={() => notify.notify('Info message', { type: 'info', priority: 0 })}
      >
        Show Info (Low Priority)
      </button>

      <button onClick={() => notify.clear()}>Clear All</button>
    </div>
  );
}

export default App;
```

---

## 5. Advanced Performance Optimization

### Q25: Explain React's rendering performance. How do you identify and fix performance bottlenecks?

**Answer:**

**Key Concepts:**
1. **Reconciliation**: React's diffing algorithm
2. **Re-rendering**: Component updates
3. **Virtual DOM**: In-memory representation

**Identifying Bottlenecks:**

```javascript
// 1. React DevTools Profiler
import { Profiler } from 'react';

function App() {
  return (
    <Profiler id="app" onRender={onRenderCallback}>
      <MainComponent />
    </Profiler>
  );
}

function onRenderCallback(id, phase, actualDuration) {
  console.log(`${id} (${phase}) took ${actualDuration}ms`);
}

// 2. Performance API
function measurePerformance() {
  performance.mark('componentStart');
  // component logic
  performance.mark('componentEnd');
  performance.measure('component', 'componentStart', 'componentEnd');
}

// 3. Chrome DevTools Performance tab
// Record → Analyze frames, scripting time, rendering time
```

**Common Bottlenecks & Solutions:**

```javascript
// ❌ Problem: Unnecessary re-renders
function ParentBad() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);
  };

  return <Child onClick={handleClick} />; // New function created each render
}

// ✅ Solution: useCallback
function ParentGood() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  return <Child onClick={handleClick} />;
}

// ❌ Problem: Large lists render all items
function ListBad({ items }) {
  return (
    <ul>
      {items.map(item => (
        <ListItem key={item.id} item={item} />
      ))}
    </ul>
  );
}

// ✅ Solution: Virtual scrolling with react-window
import { FixedSizeList } from 'react-window';

function ListGood({ items }) {
  return (
    <FixedSizeList height={600} itemCount={items.length} itemSize={35}>
      {({ index, style }) => (
        <ListItem style={style} item={items[index]} />
      )}
    </FixedSizeList>
  );
}

// ❌ Problem: Computing expensive values on every render
function ExpensiveComponentBad() {
  const [count, setCount] = useState(0);
  const expensiveValue = computeExpensiveValue(count); // Computed every render

  return <div>{expensiveValue}</div>;
}

// ✅ Solution: useMemo
function ExpensiveComponentGood() {
  const [count, setCount] = useState(0);
  const expensiveValue = useMemo(
    () => computeExpensiveValue(count),
    [count]
  );

  return <div>{expensiveValue}</div>;
}

// ❌ Problem: Creating new objects in props
function ChildBad() {
  return <GrandChild style={{ color: 'red' }} />; // New object each render
}

// ✅ Solution: Memoize object props
const STYLE = { color: 'red' };
function ChildGood() {
  return <GrandChild style={STYLE} />;
}

// ✅ Or use useMemo for dynamic objects
const ChildGoodDynamic = ({ color }) => {
  const style = useMemo(() => ({ color }), [color]);
  return <GrandChild style={style} />;
};
```

**Code Splitting & Lazy Loading:**

```javascript
import React, { lazy, Suspense } from 'react';

// Lazy load component
const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <HeavyComponent />
    </Suspense>
  );
}

// Route-based code splitting
const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));

function Router() {
  return (
    <Suspense fallback={<Loading />}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  );
}
```

---

### Q17: What is memoization in React? Explain `React.memo`, `useMemo`, and `useCallback` with performance implications.

**Answer:**

**React.memo - Shallow Comparison:**
```javascript
// Without memo: Child re-renders even if props unchanged
function ChildBad(props) {
  console.log('Child rendered');
  return <div>{props.name}</div>;
}

// With memo: Only re-renders if props change
const Child = React.memo(function Child(props) {
  console.log('Child rendered');
  return <div>{props.name}</div>;
});

function Parent() {
  const [count, setCount] = useState(0);
  return (
    <>
      <button onClick={() => setCount(c => c + 1)}>Count: {count}</button>
      <Child name="Alice" />
      {/* Child won't re-render because 'name' prop is always "Alice" */}
    </>
  );
}

// Custom comparison for complex props
const ChildWithCustomCompare = React.memo(
  function Child(props) {
    return <div>{props.user.name}</div>;
  },
  (prevProps, nextProps) => {
    // Return true if props are equal (DON'T re-render)
    return prevProps.user.id === nextProps.user.id;
  }
);
```

**useMemo - Memoize Expensive Computations:**
```javascript
function ExpensiveComponent({ items, filter }) {
  // Without useMemo: Computes every render
  // const filteredItems = items.filter(item => item.includes(filter));

  // With useMemo: Only recomputes when items or filter change
  const filteredItems = useMemo(
    () => items.filter(item => item.includes(filter)),
    [items, filter]
  );

  return (
    <ul>
      {filteredItems.map(item => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
}
```

**useCallback - Memoize Functions:**
```javascript
function Parent() {
  const [count, setCount] = useState(0);

  // Without useCallback: New function every render
  // const handleClick = () => setCount(c => c + 1);

  // With useCallback: Same function reference across renders
  const handleClick = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  // Safe to pass to memoized child
  return <MemoizedChild onClick={handleClick} />;
}

const MemoizedChild = React.memo(({ onClick }) => (
  <button onClick={onClick}>Click me</button>
));
```

**When NOT to use memoization:**
```javascript
// ❌ Premature optimization - adds overhead
const SimpleComponent = React.memo(({ name }) => (
  <div>{name}</div> // Very simple, no expensive computation
));

// ✅ Only memoize when:
// 1. Component is expensive to render
// 2. Props change infrequently
// 3. Profiler confirms bottleneck
```

**Performance Impact:**
- **Memory**: Memoization stores values in memory
- **CPU**: Comparison adds small overhead
- **Trade-off**: Use when render cost > comparison cost

---

## 6. Advanced React Patterns

### Q18: Explain Higher-Order Components (HOC) vs Render Props vs Hooks. When to use each?

**Answer:**

**Higher-Order Component (HOC):**
```javascript
// HOC pattern: A function that returns a component
function withAuthentication(Component) {
  return function ProtectedComponent(props) {
    const [isAuthenticated, setIsAuthenticated] = useState(false);

    useEffect(() => {
      // Check authentication
      const token = localStorage.getItem('token');
      setIsAuthenticated(!!token);
    }, []);

    if (!isAuthenticated) {
      return <Navigate to="/login" />;
    }

    return <Component {...props} />;
  };
}

// Usage
const Dashboard = ({ user }) => <div>Welcome {user}</div>;
export default withAuthentication(Dashboard);

// Composition
const EnhancedDashboard = withAuthentication(
  withLogging(
    withTheme(Dashboard)
  )
);
```

**Render Props:**
```javascript
class DataFetcher extends React.Component {
  state = { data: null, loading: true, error: null };

  componentDidMount() {
    fetch(this.props.url)
      .then(res => res.json())
      .then(data => this.setState({ data, loading: false }))
      .catch(error => this.setState({ error, loading: false }));
  }

  render() {
    return this.props.children(this.state);
  }
}

// Usage
<DataFetcher url="/api/users">
  {({ data, loading, error }) => (
    <>
      {loading && <p>Loading...</p>}
      {error && <p>Error: {error}</p>}
      {data && <UserList users={data} />}
    </>
  )}
</DataFetcher>
```

**Custom Hooks (Modern Approach):**
```javascript
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(data => setData(data))
      .catch(err => setError(err));
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}

// Usage - Simplest and most modern
function Users() {
  const { data: users, loading, error } = useFetch('/api/users');

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <UserList users={users} />;
}
```

**Comparison:**

| Aspect | HOC | Render Props | Hooks |
|--------|-----|--------------|-------|
| Complexity | High | Medium | Low |
| Readability | ❌ Wrapper hell | ⚠️ Callback hell | ✅ Clean |
| Composition | Multiple wrappers | Nested callbacks | Sequential |
| Performance | Can be optimized | Similar to HOC | Best |
| Type Safety (TS) | ⚠️ Complex | ✅ Good | ✅ Good |
| Reusability | ✅ High | ✅ High | ✅ High |
| Legacy Support | ✅ Works | ✅ Works | ❌ Not in class |

**When to use:**
- **HOC**: Legacy codebases, third-party libraries (Redux `connect`)
- **Render Props**: Learning, complex state logic
- **Hooks**: All new code (recommended)

---

### Q19: What are Compound Components? Show a real-world example.

**Answer:**

Compound Components is a pattern where related components work together, sharing state implicitly through context.

```javascript
// ❌ Non-compound approach (prop drilling)
<Accordion items={[
  { title: 'Section 1', content: 'Content 1' },
  { title: 'Section 2', content: 'Content 2' },
]} />

// ✅ Compound component approach (cleaner composition)
<Accordion>
  <Accordion.Item>
    <Accordion.Trigger>Section 1</Accordion.Trigger>
    <Accordion.Content>Content 1</Accordion.Content>
  </Accordion.Item>
  <Accordion.Item>
    <Accordion.Trigger>Section 2</Accordion.Trigger>
    <Accordion.Content>Content 2</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

**Implementation:**

```javascript
import React, { createContext, useContext, useState } from 'react';

// Create context for sharing state
const AccordionContext = createContext();

// Main component
function Accordion({ children }) {
  const [activeId, setActiveId] = useState(null);

  return (
    <AccordionContext.Provider value={{ activeId, setActiveId }}>
      <div className="accordion">{children}</div>
    </AccordionContext.Provider>
  );
}

// Sub-components
function Item({ id, children }) {
  return <div className="accordion-item">{children}</div>;
}

function Trigger({ id, children }) {
  const { activeId, setActiveId } = useContext(AccordionContext);

  return (
    <button
      className="accordion-trigger"
      onClick={() => setActiveId(activeId === id ? null : id)}
      aria-expanded={activeId === id}
    >
      {children}
    </button>
  );
}

function Content({ id, children }) {
  const { activeId } = useContext(AccordionContext);

  return activeId === id ? (
    <div className="accordion-content">{children}</div>
  ) : null;
}

// Attach sub-components
Accordion.Item = Item;
Accordion.Trigger = Trigger;
Accordion.Content = Content;

// Usage
export default function App() {
  return (
    <Accordion>
      <Accordion.Item id="1">
        <Accordion.Trigger id="1">Section 1</Accordion.Trigger>
        <Accordion.Content id="1">Content 1</Accordion.Content>
      </Accordion.Item>
      <Accordion.Item id="2">
        <Accordion.Trigger id="2">Section 2</Accordion.Trigger>
        <Accordion.Content id="2">Content 2</Accordion.Content>
      </Accordion.Item>
    </Accordion>
  );
}
```

**Benefits:**
- **Flexibility**: Easy to customize structure
- **Readability**: Clear parent-child relationships
- **Reusability**: Components can be used independently
- **Encapsulation**: Internal state management hidden

---

## 7. Testing & Debugging

### Q20: How do you test React components? Show unit, integration, and snapshot tests.

**Answer:**

**Setup:**
```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom jest
```

**Unit Test:**
```javascript
import { render, screen } from '@testing-library/react';
import { Button } from './Button';

describe('Button Component', () => {
  test('renders with correct text', () => {
    render(<Button>Click me</Button>);
    const button = screen.getByRole('button', { name: /click me/i });
    expect(button).toBeInTheDocument();
  });

  test('calls onClick handler when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    
    const button = screen.getByRole('button');
    button.click();
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  test('is disabled when prop is set', () => {
    render(<Button disabled>Click</Button>);
    const button = screen.getByRole('button');
    expect(button).toBeDisabled();
  });
});
```

**Integration Test:**
```javascript
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

describe('LoginForm Integration', () => {
  test('submits form with correct data', async () => {
    const mockSubmit = jest.fn();
    render(<LoginForm onSubmit={mockSubmit} />);

    // Fill form
    const emailInput = screen.getByLabelText(/email/i);
    const passwordInput = screen.getByLabelText(/password/i);
    
    await userEvent.type(emailInput, 'test@example.com');
    await userEvent.type(passwordInput, 'password123');

    // Submit
    const submitButton = screen.getByRole('button', { name: /submit/i });
    await userEvent.click(submitButton);

    // Verify
    await waitFor(() => {
      expect(mockSubmit).toHaveBeenCalledWith({
        email: 'test@example.com',
        password: 'password123',
      });
    });
  });

  test('shows validation errors', async () => {
    render(<LoginForm />);
    
    const submitButton = screen.getByRole('button', { name: /submit/i });
    await userEvent.click(submitButton);

    expect(screen.getByText(/email is required/i)).toBeInTheDocument();
    expect(screen.getByText(/password is required/i)).toBeInTheDocument();
  });
});
```

**Snapshot Test:**
```javascript
import { render } from '@testing-library/react';
import { UserCard } from './UserCard';

describe('UserCard Snapshot', () => {
  test('matches snapshot', () => {
    const { container } = render(
      <UserCard user={{ id: 1, name: 'Alice', email: 'alice@example.com' }} />
    );
    expect(container.firstChild).toMatchSnapshot();
  });
});

// Generated snapshot file (.snap)
// exports[`UserCard Snapshot matches snapshot 1`] = `
// <div class="user-card">
//   <h2>Alice</h2>
//   <p>alice@example.com</p>
// </div>
// `;
```

**Testing Hooks:**
```javascript
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

describe('useCounter Hook', () => {
  test('initializes with 0', () => {
    const { result } = renderHook(() => useCounter());
    expect(result.current.count).toBe(0);
  });

  test('increments count', () => {
    const { result } = renderHook(() => useCounter());

    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(1);
  });
});
```

**E2E Test (Cypress/Playwright):**
```javascript
describe('Login Flow', () => {
  beforeEach(() => {
    cy.visit('http://localhost:3000');
  });

  it('completes login flow', () => {
    cy.get('input[type="email"]').type('test@example.com');
    cy.get('input[type="password"]').type('password123');
    cy.get('button[type="submit"]').click();

    cy.url().should('include', '/dashboard');
    cy.get('h1').should('contain', 'Welcome');
  });
});
```

---

### Q21: How do you debug a React application? Show various techniques.

**Answer:**

**1. React DevTools (Browser Extension):**
```javascript
// Install: React DevTools Chrome/Firefox extension

// Inspect component props and state in DevTools
function Counter() {
  const [count, setCount] = useState(0);
  return <div>{count}</div>;
}

// DevTools shows: Props, Hooks, Component Tree
```

**2. Console Logging & Debugging:**
```javascript
function Component({ data }) {
  console.log('Component rendered', { data });
  console.group('User Data');
  console.log('Name:', data.name);
  console.log('Email:', data.email);
  console.groupEnd();

  return <div>{data.name}</div>;
}
```

**3. Browser DevTools Debugger:**
```javascript
function handleClick() {
  debugger; // Execution stops here when DevTools open
  // Step through code line by line
}

// Or use breakpoints in DevTools Sources panel
```

**4. Error Boundaries (Catch Errors):**
```javascript
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught:', error, errorInfo);
    // Log to error reporting service
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong: {this.state.error?.message}</h1>;
    }

    return this.props.children;
  }
}

// Usage
<ErrorBoundary>
  <App />
</ErrorBoundary>
```

**5. Custom Hooks for Debugging:**
```javascript
function useDebug(componentName, props) {
  useEffect(() => {
    console.log(`${componentName} mounted`);
    return () => console.log(`${componentName} unmounted`);
  }, [componentName]);

  useEffect(() => {
    console.log(`${componentName} props changed:`, props);
  }, [props, componentName]);
}

// Usage
function MyComponent(props) {
  useDebug('MyComponent', props);
  return <div>...</div>;
}
```

**6. Performance Profiling:**
```javascript
import { Profiler } from 'react';

function onRenderCallback(id, phase, actualDuration, baseDuration, startTime, commitTime) {
  console.log({
    id,
    phase, // "mount" or "update"
    actualDuration, // Time to render
    baseDuration,
    startTime,
    commitTime,
  });
}

<Profiler id="App" onRender={onRenderCallback}>
  <App />
</Profiler>
```

---

## 8. Advanced Async Patterns

### Q22: Explain the difference between imperative and declarative code. Show React examples.

**Answer:**

**Imperative (How to do it):**
```javascript
// Manual DOM manipulation
const button = document.getElementById('myButton');
button.textContent = 'Click me';
button.addEventListener('click', () => {
  alert('Clicked!');
});

// Managing state manually
let count = 0;
function increment() {
  count++;
  document.getElementById('count').textContent = count;
}
```

**Declarative (What we want):**
```javascript
// React declarative approach
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
    </div>
  );
}
```

**Benefits of Declarative:**
- **Readability**: Clear intent
- **Maintenance**: Easier to modify
- **Testing**: Simpler to test output
- **Composition**: Better component reuse

---

### Q23: How do you handle race conditions in async operations?

**Answer:**

```javascript
function useAsyncEffect(asyncFunction, dependencies) {
  useEffect(() => {
    let isMounted = true;

    const execute = async () => {
      const result = await asyncFunction();
      if (isMounted) {
        // Only update if component still mounted
        updateState(result);
      }
    };

    execute();

    return () => {
      isMounted = false; // Cleanup on unmount
    };
  }, dependencies);
}

// Problem: Race condition with fetching
function UserSearchBad({ searchTerm }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const fetchUser = async () => {
      const data = await fetch(`/api/users/${searchTerm}`);
      setUser(await data.json());
      // If searchTerm changes quickly, wrong data might be displayed
    };

    fetchUser();
  }, [searchTerm]);

  return <div>{user?.name}</div>;
}

// Solution 1: Abort Controller
function UserSearchGood({ searchTerm }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    const fetchUser = async () => {
      try {
        const response = await fetch(`/api/users/${searchTerm}`, {
          signal: controller.signal,
        });
        const data = await response.json();
        setUser(data);
      } catch (err) {
        if (err.name !== 'AbortError') {
          console.error(err);
        }
      }
    };

    fetchUser();

    return () => controller.abort(); // Cancel on cleanup
  }, [searchTerm]);

  return <div>{user?.name}</div>;
}

// Solution 2: Ignore stale requests
function UserSearchWithVersion({ searchTerm }) {
  const [user, setUser] = useState(null);
  const versionRef = useRef(0);

  useEffect(() => {
    const version = ++versionRef.current;

    const fetchUser = async () => {
      const data = await fetch(`/api/users/${searchTerm}`);
      const json = await data.json();
      
      if (version === versionRef.current) {
        setUser(json); // Only update if this is the latest request
      }
    };

    fetchUser();
  }, [searchTerm]);

  return <div>{user?.name}</div>;
}

// Solution 3: Debouncing
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

function UserSearchWithDebounce({ searchTerm }) {
  const debouncedSearchTerm = useDebounce(searchTerm, 500);
  const [user, setUser] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    fetch(`/api/users/${debouncedSearchTerm}`, {
      signal: controller.signal,
    })
      .then(res => res.json())
      .then(setUser);

    return () => controller.abort();
  }, [debouncedSearchTerm]);

  return <div>{user?.name}</div>;
}
```

---

## 9. Security & Best Practices

### Q24: What are common security vulnerabilities in React? How do you prevent them?

**Answer:**

**1. XSS (Cross-Site Scripting):**
```javascript
// ❌ Vulnerable: Using dangerouslySetInnerHTML
function CommentBad({ comment }) {
  return <div dangerouslySetInnerHTML={{ __html: comment }} />;
}

// If comment = "<img src=x onerror='alert(\"XSS\")'>"
// This would execute the script

// ✅ Safe: Let React escape by default
function CommentGood({ comment }) {
  return <div>{comment}</div>; // React automatically escapes
}

// ✅ If you must use HTML: Sanitize it
import DOMPurify from 'dompurify';

function CommentSafe({ comment }) {
  const sanitized = DOMPurify.sanitize(comment);
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
}
```

**2. Injection Attacks:**
```javascript
// ❌ Vulnerable: Building queries with user input
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ✅ Safe: Use parameterized queries
const query = 'SELECT * FROM users WHERE id = ?';
db.query(query, [userId]);
```

**3. CSRF (Cross-Site Request Forgery):**
```javascript
// ✅ Include CSRF token in requests
async function deleteUser(userId) {
  const csrfToken = document.querySelector('meta[name="csrf-token"]').content;

  const response = await fetch(`/api/users/${userId}`, {
    method: 'DELETE',
    headers: {
      'X-CSRF-Token': csrfToken,
      'Content-Type': 'application/json',
    },
  });

  return response.json();
}
```

**4. Sensitive Data Exposure:**
```javascript
// ❌ Vulnerable: Storing sensitive data in localStorage
localStorage.setItem('password', userPassword);
localStorage.setItem('creditCard', '4111-1111-1111-1111');

// ✅ Safe: Store only non-sensitive tokens
localStorage.setItem('authToken', jwt);

// Use secure, httpOnly cookies for sensitive data
// Set-Cookie: authToken=xyz; Secure; HttpOnly; SameSite=Strict
```

**5. Dependency Vulnerabilities:**
```bash
# Regularly audit and update dependencies
npm audit
npm audit fix
npm update

# Use lock files
# package-lock.json or yarn.lock for reproducible installations
```

**6. Environment Variables:**
```javascript
// ✅ Use environment variables (never in code)
// .env.local (not committed to git)
REACT_APP_API_URL=https://api.example.com
REACT_APP_API_KEY=secret123

// Access in code
const apiUrl = process.env.REACT_APP_API_URL;

// ❌ Never do this:
const API_KEY = 'sk_live_51234567890abc';
```

**7. Content Security Policy:**
```html
<!-- Set CSP header on server -->
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; script-src 'self' 'unsafe-inline'">
```

---

### Q25: How do you handle authentication and authorization in a React app?

**Answer:**

```javascript
// Create auth context
const AuthContext = createContext();

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Check if user is already logged in
    const token = localStorage.getItem('token');
    if (token) {
      validateToken(token)
        .then(user => setUser(user))
        .catch(() => localStorage.removeItem('token'))
        .finally(() => setLoading(false));
    } else {
      setLoading(false);
    }
  }, []);

  const login = async (email, password) => {
    const response = await fetch('/api/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password }),
    });

    if (!response.ok) throw new Error('Login failed');

    const { token, user } = await response.json();
    localStorage.setItem('token', token);
    setUser(user);
  };

  const logout = () => {
    localStorage.removeItem('token');
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, loading, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

// Protected route
function ProtectedRoute({ children }) {
  const { user, loading } = useContext(AuthContext);

  if (loading) return <div>Loading...</div>;
  if (!user) return <Navigate to="/login" />;

  return children;
}

// Role-based access
function RoleRoute({ roles, children }) {
  const { user } = useContext(AuthContext);

  if (!user || !roles.includes(user.role)) {
    return <Navigate to="/unauthorized" />;
  }

  return children;
}

// Usage
<Routes>
  <Route path="/login" element={<Login />} />
  <Route
    path="/dashboard"
    element={
      <ProtectedRoute>
        <Dashboard />
      </ProtectedRoute>
    }
  />
  <Route
    path="/admin"
    element={
      <RoleRoute roles={['admin']}>
        <AdminPanel />
      </RoleRoute>
    }
  />
</Routes>
```

---

## 10. TypeScript Advanced Patterns

### Q26: Show advanced TypeScript patterns used in React development.

**Answer:**

**Generic Components:**
```typescript
// Before: Single type
interface ListProps {
  items: string[];
}

// After: Generic - reusable with any type
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
  keyExtractor: (item: T) => string | number;
}

function List<T>({ items, renderItem, keyExtractor }: ListProps<T>) {
  return (
    <ul>
      {items.map(item => (
        <li key={keyExtractor(item)}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}

// Usage with different types
<List<User>
  items={users}
  renderItem={(user) => <span>{user.name}</span>}
  keyExtractor={(user) => user.id}
/>

<List<Post>
  items={posts}
  renderItem={(post) => <span>{post.title}</span>}
  keyExtractor={(post) => post.id}
/>
```

**Conditional Types:**
```typescript
// Extract the type from a Promise
type Awaited<T> = T extends Promise<infer U> ? U : T;

type A = Awaited<Promise<string>>; // string
type B = Awaited<number>; // number

// Use case: API response types
async function fetchUser() {
  return { id: 1, name: 'Alice' };
}

type UserType = Awaited<ReturnType<typeof fetchUser>>;
// UserType = { id: number; name: string }
```

**Mapped Types:**
```typescript
// Create a type with same keys but different values
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

interface User {
  name: string;
  age: number;
}

type UserGetters = Getters<User>;
// UserGetters = {
//   getName: () => string;
//   getAge: () => number;
// }

// Practical: Make all properties optional
type Partial<T> = {
  [K in keyof T]?: T[K];
};

// Practical: Make all properties readonly
type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};
```

**Discriminated Unions:**
```typescript
// Type-safe state management
type SuccessState = { status: 'success'; data: any };
type ErrorState = { status: 'error'; error: string };
type LoadingState = { status: 'loading' };

type State = SuccessState | ErrorState | LoadingState;

function handleState(state: State) {
  if (state.status === 'success') {
    // TypeScript knows state is SuccessState here
    console.log(state.data);
  } else if (state.status === 'error') {
    // TypeScript knows state is ErrorState here
    console.log(state.error);
  }
  // Loading state has no extra properties
}
```

**Utility Types in React:**
```typescript
// Extract component props
type ComponentProps = React.ComponentProps<'button'>;
// = { type?: 'button' | 'submit' | 'reset'; onClick?: ... }

type MyComponentProps = React.ComponentProps<typeof MyComponent>;

// Extract event types
type ChangeEvent = React.ChangeEvent<HTMLInputElement>;

// Get function return type
type Result = ReturnType<typeof useFetch>;
```

---

## 11. Build Optimization & Bundling

### Q27: How do you optimize a React app's bundle size and build performance?

**Answer:**

**1. Code Splitting:**
```javascript
// Automatic with dynamic imports
const HeavyComponent = lazy(() => import('./HeavyComponent'));

<Suspense fallback={<Loading />}>
  <HeavyComponent />
</Suspense>
```

**2. Tree Shaking (Remove Unused Code):**
```javascript
// ❌ Don't use default exports for utils (hard to tree shake)
export default {
  add: (a, b) => a + b,
  subtract: (a, b) => a - b,
};

// ✅ Use named exports
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// Only 'add' will be bundled if only that is imported
import { add } from './math';
```

**3. Production Build:**
```bash
# React removes development-only code
npm run build

# Minification
# 1000 lines → 50 lines
```

**4. Webpack Analysis:**
```bash
npm install --save-dev webpack-bundle-analyzer

# Visualize bundle size
```

**5. Gzip Compression:**
```javascript
// Server compression (express example)
const compression = require('compression');
app.use(compression());

// Reduces transferred size by ~70%
```

**6. Dependency Analysis:**
```bash
# Find large dependencies
npm install -g npm-audit
npm audit

# Check individual package sizes
npm ls package-name

# Use lighter alternatives
# moment.js (2.5MB) → date-fns (10KB)
```

**7. Dynamic Imports:**
```javascript
// Webpack automatically creates separate chunk
function App() {
  const [showChart, setShowChart] = useState(false);

  if (showChart) {
    // Only loads when needed
    const { Chart } = await import('react-charts');
    return <Chart />;
  }

  return <button onClick={() => setShowChart(true)}>Show Chart</button>;
}
```

---

## 12. Accessibility (a11y)

### Q28: How do you build accessible React applications?

**Answer:**

```javascript
// 1. Semantic HTML
// ❌ Bad
<div onClick={handleClick}>Button</div>

// ✅ Good
<button onClick={handleClick}>Button</button>

// 2. ARIA Attributes
function Modal({ isOpen, onClose }) {
  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
      aria-hidden={!isOpen}
    >
      <h2 id="modal-title">Confirm Action</h2>
      <button aria-label="Close modal" onClick={onClose}>×</button>
    </div>
  );
}

// 3. Keyboard Navigation
function Menu() {
  const [selectedIndex, setSelectedIndex] = useState(0);
  const items = ['Home', 'About', 'Contact'];

  const handleKeyDown = (e) => {
    switch (e.key) {
      case 'ArrowDown':
        setSelectedIndex((i) => (i + 1) % items.length);
        break;
      case 'ArrowUp':
        setSelectedIndex((i) => (i - 1 + items.length) % items.length);
        break;
    }
  };

  return (
    <ul onKeyDown={handleKeyDown} role="menu">
      {items.map((item, i) => (
        <li
          key={item}
          role="menuitem"
          tabIndex={selectedIndex === i ? 0 : -1}
        >
          {item}
        </li>
      ))}
    </ul>
  );
}

// 4. Form Accessibility
function Form() {
  return (
    <form>
      <label htmlFor="email">Email:</label>
      <input
        id="email"
        type="email"
        aria-label="Email address"
        aria-required="true"
        required
      />

      <label htmlFor="message">Message:</label>
      <textarea
        id="message"
        aria-describedby="message-hint"
      />
      <span id="message-hint">Max 500 characters</span>
    </form>
  );
}

// 5. Color Contrast & Focus Styles
// ✅ In CSS
button {
  background-color: #0066cc; /* Good contrast on white */
  color: white;
}

button:focus {
  outline: 3px solid #ffaa00; /* Visible focus indicator */
}

// 6. Skip Navigation Links
function Layout() {
  return (
    <>
      <a href="#main-content" className="skip-link">
        Skip to main content
      </a>
      <header>Navigation</header>
      <main id="main-content">Main Content</main>
    </>
  );
}

// 7. Screen Reader Text
function Icon({ type }) {
  return (
    <>
      <svg>...</svg>
      <span className="sr-only">{type}</span>
    </>
  );
}

// CSS for sr-only
// .sr-only {
//   position: absolute;
//   width: 1px;
//   height: 1px;
//   padding: 0;
//   margin: -1px;
//   overflow: hidden;
//   clip: rect(0, 0, 0, 0);
//   white-space: nowrap;
//   border-width: 0;
// }

// 8. Testing Accessibility
import { render, screen } from '@testing-library/react';
import { axe } from 'jest-axe';

test('has no accessibility violations', async () => {
  const { container } = render(<App />);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

---

## COMPREHENSIVE TOPIC EXPANSION

### Section 1A: JavaScript - EXPANDED (20+ Questions)

#### Q11: What is shallow copy vs deep copy? Implement both.

**Answer:**

```javascript
// Shallow Copy - only copies top-level references
const original = { 
  name: 'Alice', 
  address: { city: 'NYC', zip: '10001' },
  hobbies: ['reading', 'gaming']
};

// Method 1: Spread operator
const shallow1 = { ...original };
shallow1.address.city = 'Boston'; // Mutates original
shallow1.hobbies.push('coding'); // Mutates original

// Method 2: Object.assign
const shallow2 = Object.assign({}, original);
shallow2.address.city = 'LA'; // Mutates original

// Deep Copy - copies all nested references
// Method 1: JSON (simple objects only, no functions/undefined)
const deep1 = JSON.parse(JSON.stringify(original));
deep1.address.city = 'Chicago'; // Doesn't affect original
deep1.hobbies.push('painting'); // Doesn't affect original

// Method 2: Recursive function
function deepCopy(obj) {
  if (obj === null || typeof obj !== 'object') return obj;
  if (Array.isArray(obj)) return obj.map(deepCopy);
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof Set) return new Set([...obj].map(deepCopy));
  if (obj instanceof Map) {
    const map = new Map();
    for (const [k, v] of obj) map.set(deepCopy(k), deepCopy(v));
    return map;
  }
  const copy = {};
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      copy[key] = deepCopy(obj[key]);
    }
  }
  return copy;
}

// Method 3: structuredClone (modern browser API)
const deep2 = structuredClone(original);

// When to use:
// - Shallow copy: Simple objects, better performance
// - Deep copy: Complex nested structures, when you need true independence
```

---

#### Q12: Explain prototypal inheritance and the prototype chain.

**Answer:**

```javascript
// Prototype chain lookup
const grandparent = { greet: () => 'Hello from grandparent' };
const parent = Object.create(grandparent);
parent.work = () => 'Working';

const child = Object.create(parent);
child.play = () => 'Playing';

console.log(child.greet()); // Looks up: child → parent → grandparent
console.log(child.work());
console.log(child.play());

// Constructor function pattern
function Animal(name) {
  this.name = name;
}

Animal.prototype.sound = function() {
  return `${this.name} makes a sound`;
};

function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}

// Set up prototype chain
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.sound = function() {
  return `${this.name} barks`;
};

const dog = new Dog('Rex', 'Golden');
console.log(dog.sound()); // "Rex barks"
console.log(dog instanceof Dog); // true
console.log(dog instanceof Animal); // true

// Check prototype chain
console.log(Object.getPrototypeOf(dog) === Dog.prototype); // true
console.log(Dog.prototype.isPrototypeOf(dog)); // true
```

---

#### Q13: What is event delegation and why is it useful?

**Answer:**

```javascript
// Without delegation - inefficient for dynamic content
const items = document.querySelectorAll('li');
items.forEach(item => {
  item.addEventListener('click', handleClick); // Doesn't work for new items
});

// With delegation - works for current AND future items
const list = document.querySelector('ul');
list.addEventListener('click', (e) => {
  if (e.target.tagName === 'LI') {
    e.target.classList.toggle('active');
  }
});

// Practical: Comment system
class CommentSystem {
  constructor(container) {
    this.container = container;
    this.comments = [];
    this.setupListeners();
  }

  setupListeners() {
    this.container.addEventListener('click', (e) => {
      if (e.target.classList.contains('delete-btn')) {
        this.deleteComment(e.target.closest('.comment'));
      } else if (e.target.classList.contains('reply-btn')) {
        this.showReply(e.target.closest('.comment'));
      } else if (e.target.classList.contains('like-btn')) {
        this.toggleLike(e.target.closest('.comment'));
      }
    });
  }

  deleteComment(el) { el.remove(); }
  showReply(el) { /* show reply form */ }
  toggleLike(el) { el.classList.toggle('liked'); }
}
```

---

#### Q14: Explain memory leaks in JavaScript and prevention strategies.

**Answer:**

```javascript
// Memory Leak 1: Global variables from closures
function createClosure() {
  const largeArray = new Array(1000000).fill('data');
  return () => console.log(largeArray.length); // Closure holds reference
}

// Memory Leak 2: Forgotten timers and listeners
class Component {
  constructor() {
    this.data = new Array(1000000);
    this.handleScroll = this.onScroll.bind(this);
    window.addEventListener('scroll', this.handleScroll);
    this.intervalId = setInterval(() => this.update(), 1000);
  }

  onScroll() { /* ... */ }
  
  destroy() {
    window.removeEventListener('scroll', this.handleScroll);
    clearInterval(this.intervalId);
    this.data = null; // Help garbage collection
  }
}

// Memory Leak 3: Detached DOM nodes
function leakDOM() {
  const div = document.createElement('div');
  const hugeData = new Array(1000000);
  div.data = hugeData;
  document.body.appendChild(div);
  document.body.removeChild(div); // Still in memory!
}

// Fix: Clean up references
function fixDOM() {
  const div = document.createElement('div');
  const hugeData = new Array(1000000);
  div.data = hugeData;
  document.body.appendChild(div);
  document.body.removeChild(div);
  div.data = null; // Release reference
  div = null;
}

// Prevention patterns:
// 1. Always clear listeners in cleanup
// 2. Use WeakMap for cache with object keys
// 3. Avoid circular references
// 4. Clear intervals/timeouts
// 5. Nullify large object references
```

---

#### Q15: What are WeakMap and WeakSet? When would you use them?

**Answer:**

```javascript
// Regular Map - strong references
const map = new Map();
let obj1 = { id: 1 };
map.set(obj1, 'value');
obj1 = null; // Object still in memory

// WeakMap - weak references
const weakMap = new WeakMap();
let obj2 = { id: 2 };
weakMap.set(obj2, 'value');
obj2 = null; // Object can be garbage collected

// Practical: Private data storage
const privateData = new WeakMap();

class User {
  constructor(name, password) {
    this.name = name;
    privateData.set(this, { password });
  }

  authenticate(pwd) {
    return privateData.get(this).password === pwd;
  }
}

// Practical: DOM node cache
const nodeCache = new WeakMap();

function cacheDOMNode(node, data) {
  nodeCache.set(node, data);
  return nodeCache.get(node);
}

// When node is removed from DOM, it's garbage collected
// and automatically removed from WeakMap

// Practical: Event listener tracking
const listeners = new WeakMap();

class EventEmitter {
  on(target, event, handler) {
    if (!listeners.has(target)) {
      listeners.set(target, {});
    }
    const events = listeners.get(target);
    if (!events[event]) events[event] = [];
    events[event].push(handler);
  }
}

// Key differences:
// WeakMap: not enumerable, no .size, can't iterate
// WeakSet: not enumerable, no .size, only objects
```

---

#### Q16: Explain destructuring assignment and advanced patterns.

**Answer:**

```javascript
// Object destructuring with renaming
const user = { name: 'Alice', age: 25, email: 'alice@example.com' };
const { name: userName, age: userAge = 18 } = user;

// Nested destructuring
const data = {
  user: { profile: { bio: 'Developer' } }
};
const { user: { profile: { bio } } } = data;

// Array destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];
const [a, , , d] = [1, 2, 3, 4]; // Skip elements

// Function parameters
function greet({ name, age = 18 } = {}) {
  return `${name} is ${age}`;
}

// Swapping with destructuring
let x = 1, y = 2;
[x, y] = [y, x];

// Object restructuring
const { address: { city, zip }, ...rest } = user;

// Pattern in loops
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' }
];
for (const { id, name } of users) {
  console.log(id, name);
}

// Practical: API response handling
const handleResponse = ({ status, data: { user: { name, email } } }) => {
  return { status, name, email };
};
```

---

#### Q17: What are generators and how do they differ from iterators?

**Answer:**

```javascript
// Iterator protocol
const myIterable = {
  [Symbol.iterator]() {
    let count = 0;
    return {
      next: () => ({
        value: ++count,
        done: count > 3
      })
    };
  }
};

for (const val of myIterable) console.log(val); // 1, 2, 3

// Generator function (simpler syntax)
function* countGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

// Can also use for...of
for (const val of countGenerator()) console.log(val);

// Generator with parameters
function* range(start, end) {
  for (let i = start; i <= end; i++) yield i;
}

// Infinite generator
function* infiniteIds() {
  let id = 0;
  while (true) yield ++id;
}

const idGen = infiniteIds();
console.log(idGen.next().value); // 1
console.log(idGen.next().value); // 2

// Generator delegation
function* delegated() {
  yield* range(1, 3);
  yield* countGenerator();
}

// Practical: Async generator
async function* fetchPages(pageNum) {
  while (true) {
    const data = await fetch(`/api/pages/${pageNum}`).then(r => r.json());
    yield data;
    pageNum++;
  }
}
```

---

#### Q18: What is the difference between Object.create, Object.assign, and spread operator?

**Answer:**

```javascript
// Object.create - creates new object with specified prototype
const proto = { 
  greet() { return `Hello from ${this.name}`; }
};
const obj = Object.create(proto);
obj.name = 'Alice';
console.log(obj.greet()); // "Hello from Alice"
console.log(obj.hasOwnProperty('greet')); // false (inherited)

// Object.assign - mutates target and copies own enumerable properties
const source1 = { a: 1 };
const source2 = { b: 2, c: 3 };
const target = Object.assign({}, source1, source2);
console.log(target); // { a: 1, b: 2, c: 3 }

// Spread operator - copies own enumerable properties (shallow)
const spread = { ...source1, ...source2 };
console.log(spread); // { a: 1, b: 2, c: 3 }

// Key differences
const descriptor = {};
Object.defineProperty(descriptor, 'hidden', {
  value: 'secret',
  enumerable: false
});

Object.assign({}, descriptor); // Copies hidden
{ ...descriptor }; // Doesn't copy hidden

// Object.create with property descriptors
const person = Object.create(Object.prototype, {
  name: { value: 'Bob', writable: true, enumerable: true },
  age: { value: 25, writable: true, enumerable: true },
  secret: { value: 'sshhh', writable: false, enumerable: false }
});
```

---

#### Q19: Explain the "use strict" directive and its implications.

**Answer:**

```javascript
'use strict';

// 1. Variables must be declared
function test() {
  // x = 10; // ReferenceError in strict mode
  const x = 10; // Must declare
}

// 2. this is undefined in functions
function strictThis() {
  console.log(this); // undefined (not window/global)
}

// 3. Can't delete variables
// delete x; // SyntaxError

// 4. Function parameters must be unique
// function sum(a, a, b) { } // SyntaxError

// 5. Octal syntax not allowed
// const num = 010; // SyntaxError

// 6. with statement not allowed
// with(Math) { x = cos(2); } // SyntaxError

// 7. eval doesn't pollute scope
eval('var x = 1');
console.log(typeof x); // undefined (x is local to eval)

// 8. arguments is fixed
function args(a, b) {
  arguments[0] = 99;
  console.log(a); // 'a' not affected by arguments
}

// Module level strict mode
// export function strictFunc() {
//   // This function is always strict
// }
```

---

#### Q20: What is the Symbol primitive type and its use cases?

**Answer:**

```javascript
// Symbols are unique and immutable
const sym1 = Symbol('id');
const sym2 = Symbol('id');
console.log(sym1 === sym2); // false

// Use as object keys
const user = {
  name: 'Alice',
  [Symbol.for('userId')]: 123
};

console.log(user[Symbol.for('userId')]); // 123
console.log(Object.keys(user)); // ['name'] (symbols hidden)

// Well-known symbols
class MyIterable {
  [Symbol.iterator]() {
    return {
      next: () => ({ done: true })
    };
  }
}

class MyType {
  [Symbol.toStringTag] = 'MyType';
}

console.log(Object.prototype.toString.call(new MyType())); 
// [object MyType]

// Practical: Private data
const privateData = Symbol('private');

class Bank {
  constructor(balance) {
    this[privateData] = balance;
  }

  getBalance() {
    return this[privateData];
  }
}
```

---

#### Q21: Compare == vs === and explain type coercion.

**Answer:**

```javascript
// == loose equality (with coercion)
console.log(1 == '1'); // true
console.log(1 == true); // true
console.log(0 == false); // true
console.log(null == undefined); // true

// === strict equality (no coercion)
console.log(1 === '1'); // false
console.log(1 === true); // false
console.log(null === undefined); // false

// Coercion rules for ==
// 1. Same type: compare as ===
// 2. null == undefined (special case)
// 3. Number vs String: convert string to number
console.log(5 == '5'); // true
// 4. Boolean vs anything: convert boolean to number
console.log(true == 1); // true
// 5. Object vs Primitive: convert object to primitive
console.log([] == ''); // true
console.log([1] == '1'); // true

// Confusing cases
console.log('0' == false); // true (!)
console.log([] == false); // true (!)
console.log([] == ![]); // true (!)
console.log({} == {}); // false (different references)

// Best practice: Always use ===
// Avoid == except for null == undefined check
const value = null;
if (value == null) { } // Safe check for null or undefined
```

---

#### Q22: What is JSON.stringify and JSON.parse? Show advanced usage.

**Answer:**

```javascript
// Basic stringify
const obj = { name: 'Alice', age: 25 };
JSON.stringify(obj); // '{"name":"Alice","age":25}'

// With formatting
JSON.stringify(obj, null, 2);
// {
//   "name": "Alice",
//   "age": 25
// }

// With replacer function (filter/transform)
JSON.stringify(obj, (key, value) => {
  if (typeof value === 'string') return value.toUpperCase();
  return value;
});

// With replacer array (include only specific keys)
JSON.stringify(obj, ['name']); // Only 'name'

// Handling circular references
const circular = { name: 'Alice' };
circular.self = circular;

const seen = new WeakSet();
JSON.stringify(circular, (key, value) => {
  if (typeof value === 'object' && value !== null) {
    if (seen.has(value)) return undefined;
    seen.add(value);
  }
  return value;
});

// Custom toJSON method
class CustomDate {
  constructor(date) {
    this.date = new Date(date);
  }
  toJSON() {
    return this.date.toISOString();
  }
}

// Parse with reviver function
const jsonStr = '{"date":"2024-01-21"}';
JSON.parse(jsonStr, (key, value) => {
  if (key === 'date') return new Date(value);
  return value;
});
```

---

#### Q23: Explain nullish coalescing (??) and optional chaining (?.).

**Answer:**

```javascript
// Nullish coalescing (??)
const v1 = null ?? 'default'; // 'default'
const v2 = undefined ?? 'default'; // 'default'
const v3 = 0 ?? 'default'; // 0 (0 is not nullish!)
const v4 = false ?? 'default'; // false
const v5 = '' ?? 'default'; // ''

// vs || operator (checks for falsy)
const o1 = 0 || 'default'; // 'default' (0 is falsy)
const o2 = 0 ?? 'default'; // 0 (0 is not nullish)

// Optional chaining (?.)
const user = null;
console.log(user?.name); // undefined
console.log(user?.getName?.()); // undefined
console.log(user?.[0]); // undefined

// Practical: Safe property access
const config = {
  api: {
    timeout: 5000
  }
};

const timeout = config?.api?.timeout ?? 3000;
const retries = config?.api?.retries ?? 3;

// Method chaining
const result = obj?.method?.subMethod?.();

// Array access
const value = array?.[index];

// Combination
const email = response?.data?.user?.email ?? 'no-email@example.com';
```

---

#### Q24: What are template literals and tagged templates?

**Answer:**

```javascript
// Basic template literals
const name = 'Alice';
console.log(`Hello, ${name}!`);

// Multi-line strings
const text = `
  Line 1
  Line 2
  Line 3
`;

// Expression evaluation
console.log(`2 + 2 = ${2 + 2}`);

// Tagged template functions
function highlight(strings, ...values) {
  let result = '';
  for (let i = 0; i < strings.length; i++) {
    result += strings[i] + `**${values[i] ?? ''}**`;
  }
  return result;
}

const message = highlight`Hello, ${name}!`;
// "Hello, **Alice**!"

// Practical: HTML escaping
function html(strings, ...values) {
  const escape = (str) => str
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;');
  
  return strings.reduce((result, string, i) => {
    return result + string + (values[i] ? escape(String(values[i])) : '');
  }, '');
}

const userInput = '<script>alert("xss")</script>';
const safe = html`<div>${userInput}</div>`;

// Practical: i18n
function t(strings, ...values) {
  const template = strings.join('{}');
  return translate(template, values);
}

// Practical: SQL queries
function sql(strings, ...values) {
  let query = strings[0];
  for (let i = 0; i < values.length; i++) {
    query += `'${values[i]}'` + strings[i + 1];
  }
  return query;
}
```

---

#### Q25: What is the difference between Map/Set and Object/Array?

**Answer:**

```javascript
// Map vs Object
const mapObj = new Map();
const plainObj = {};

// Map keys can be any type
mapObj.set({ id: 1 }, 'value1');
mapObj.set(NaN, 'value2'); // Works with NaN
mapObj.set(function() {}, 'value3');

// Object keys are strings/symbols
plainObj[{ id: 1 }] = 'value1'; // Key is "[object Object]"
plainObj[NaN] = 'value2'; // Key is "NaN"

// Map performance
const largeMap = new Map();
const largeObj = {};
for (let i = 0; i < 1000000; i++) {
  largeMap.set(i, `value${i}`);
  largeObj[i] = `value${i}`;
}

// Set vs Array
const set = new Set([1, 2, 2, 3, 3, 3]);
console.log(set.size); // 3

const array = [1, 2, 2, 3, 3, 3];
console.log(array.length); // 6

// Set has O(1) lookup, Array has O(n)
console.time('Set has');
set.has(999999); // Fast
console.timeEnd('Set has');

console.time('Array includes');
array.includes(999999); // Slower
console.timeEnd('Array includes');

// Practical: Unique collection
const uniqueUsers = new Set([1, 2, 1, 3, 2]);
console.log(uniqueUsers.size); // 3

// Practical: Frequency tracking
const frequency = new Map();
[1, 2, 1, 3, 1].forEach(num => {
  frequency.set(num, (frequency.get(num) || 0) + 1);
});
```

---

#### Q26: Explain function composition and currying.

**Answer:**

```javascript
// Function composition (right to left)
const compose = (...fns) => (arg) =>
  fns.reduceRight((acc, fn) => fn(acc), arg);

const double = x => x * 2;
const addOne = x => x + 1;
const square = x => x * x;

const composed = compose(square, addOne, double);
console.log(composed(5)); // ((5 * 2 + 1)^2) = 121

// Pipe (left to right)
const pipe = (...fns) => (arg) =>
  fns.reduce((acc, fn) => fn(acc), arg);

const piped = pipe(double, addOne, square);
console.log(piped(5)); // ((5 * 2 + 1)^2) = 121

// Currying (convert function with multiple params to chain of functions)
function curry(fn) {
  const arity = fn.length;
  return function curried(...args) {
    if (args.length >= arity) return fn(...args);
    return (...nextArgs) => curried(...args, ...nextArgs);
  };
}

const add = (a, b, c) => a + b + c;
const curriedAdd = curry(add);
console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6

// Partial application
const partial = (fn, ...args) => (...nextArgs) =>
  fn(...args, ...nextArgs);

const fetchAPI = (method, url, data) => fetch(url, { method, body: JSON.stringify(data) });
const post = partial(fetchAPI, 'POST');
const getUser = partial(post, '/api/user');
```

---

#### Q27: What are Proxy and Reflect objects? Show practical examples.

**Answer:**

```javascript
// Proxy - intercept and customize operations
const handler = {
  get(target, prop) {
    console.log(`Getting ${prop}`);
    return target[prop];
  },
  set(target, prop, value) {
    console.log(`Setting ${prop} = ${value}`);
    target[prop] = value;
    return true;
  }
};

const target = { name: 'Alice' };
const proxy = new Proxy(target, handler);
proxy.name; // "Getting name"
proxy.name = 'Bob'; // "Setting name = Bob"

// Validation proxy
const validationHandler = {
  set(target, prop, value) {
    if (prop === 'age' && typeof value !== 'number') {
      throw new TypeError('Age must be number');
    }
    target[prop] = value;
    return true;
  }
};

const person = new Proxy({}, validationHandler);
// person.age = 'twenty'; // TypeError

// Observable pattern
function observable(target, observers = []) {
  return new Proxy(target, {
    set(target, prop, value) {
      target[prop] = value;
      observers.forEach(observer => observer());
      return true;
    }
  });
}

// Reflect - standard object interaction
const obj = { x: 1 };
Reflect.defineProperty(obj, 'y', { value: 2 });
Reflect.deleteProperty(obj, 'x');
Reflect.set(obj, 'z', 3);

// Reflect with Proxy
const handler2 = {
  get(target, prop) {
    return Reflect.get(target, prop);
  },
  set(target, prop, value) {
    return Reflect.set(target, prop, value);
  }
};
```

---

#### Q28: What is Promise.all vs Promise.race vs Promise.allSettled?

**Answer:**

```javascript
const p1 = Promise.resolve(1);
const p2 = new Promise(r => setTimeout(() => r(2), 100));
const p3 = Promise.reject('Error');

// Promise.all - resolves when ALL settle, rejects if ANY reject
Promise.all([p1, p2])
  .then(values => console.log(values)); // [1, 2]

Promise.all([p1, p3])
  .catch(error => console.log(error)); // "Error"

// Promise.race - resolves/rejects with first settled
Promise.race([p1, p2])
  .then(value => console.log(value)); // 1 (fastest)

// Promise.allSettled - waits for all, returns results regardless of rejection
Promise.allSettled([p1, p2, p3])
  .then(results => console.log(results));
// [
//   { status: 'fulfilled', value: 1 },
//   { status: 'fulfilled', value: 2 },
//   { status: 'rejected', reason: 'Error' }
// ]

// Promise.any - resolves with first fulfilled, rejects if all reject
Promise.any([Promise.reject('e1'), p1, p2])
  .then(value => console.log(value)); // 1

// Practical: timeout race
const timeout = new Promise((_, reject) =>
  setTimeout(() => reject('Timeout'), 5000)
);

Promise.race([fetchData(), timeout])
  .then(data => console.log(data))
  .catch(err => console.log('Request failed or timed out'));

// Practical: error handling with allSettled
const requests = [fetch('/api/1'), fetch('/api/2'), fetch('/api/3')];
const results = await Promise.allSettled(requests);
const successful = results.filter(r => r.status === 'fulfilled');
```

---

#### Q29: Explain async/await error handling patterns.

**Answer:**

```javascript
// Basic try/catch
async function getData() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) throw new Error('HTTP error');
    return await response.json();
  } catch (error) {
    console.error('Failed:', error);
    throw error; // Re-throw or return default
  } finally {
    console.log('Done');
  }
}

// Error handling with fallback
async function getDataWithFallback() {
  try {
    return await fetch('/api/data').then(r => r.json());
  } catch (error) {
    console.warn('Fallback to local data');
    return { cached: true };
  }
}

// Retry logic
async function retryableOperation(fn, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === retries - 1) throw error;
      await new Promise(r => setTimeout(r, 1000 * (i + 1)));
    }
  }
}

// Promise.all with error handling
async function allWithErrorHandling(promises) {
  const results = await Promise.allSettled(promises);
  const errors = results.filter(r => r.status === 'rejected');
  if (errors.length > 0) {
    console.warn(`${errors.length} requests failed`);
  }
  return results.filter(r => r.status === 'fulfilled').map(r => r.value);
}

// Cleanup pattern
async function withResourceCleanup() {
  const resource = await acquireResource();
  try {
    return await useResource(resource);
  } finally {
    await resource.release(); // Always called
  }
}
```

---

#### Q30: What is the ArrayBuffer and TypedArray? When would you use them?

**Answer:**

```javascript
// ArrayBuffer - fixed-length raw binary data
const buffer = new ArrayBuffer(16); // 16 bytes
console.log(buffer.byteLength); // 16

// Can't directly manipulate buffer - use TypedArray
// Different TypedArray types:
// Int8Array, Uint8Array, Uint8ClampedArray
// Int16Array, Uint16Array
// Int32Array, Uint32Array
// Float32Array, Float64Array
// BigInt64Array, BigUint64Array

// Creating TypedArrays
const uint8 = new Uint8Array(buffer); // View as 8-bit unsigned integers
const float32 = new Float32Array(buffer); // View as 32-bit floats

uint8[0] = 255;
console.log(float32[0]); // Different interpretation of same bytes

// TypedArray from array
const arr = new Uint8Array([1, 2, 3, 4]);
console.log(arr[0]); // 1

// Practical: Image processing
const imageData = new Uint8ClampedArray(width * height * 4);
for (let i = 0; i < imageData.length; i += 4) {
  imageData[i] = 255; // R
  imageData[i + 1] = 0; // G
  imageData[i + 2] = 0; // B
  imageData[i + 3] = 255; // A
}

// Practical: WebSocket binary messages
const encoder = new TextEncoder();
const data = encoder.encode('Hello');
socket.send(data); // Send as binary

const decoder = new TextDecoder();
socket.onmessage = (e) => {
  const text = decoder.decode(e.data);
};

// Practical: Crypto operations
const secretKey = new Uint8Array(32); // 256-bit key
crypto.getRandomValues(secretKey);
```

---

### Section 2A: TypeScript - EXPANDED (20+ Questions)

#### Q31: What are the different TypeScript utility types? Provide examples.

**Answer:**

```typescript
// Partial<T> - make all properties optional
interface User {
  name: string;
  age: number;
  email: string;
}

const updateUser: Partial<User> = {
  name: 'Alice' // age and email are optional
};

// Required<T> - make all properties required
const requiredUser: Required<User> = {
  name: 'Bob',
  age: 25,
  email: 'bob@example.com'
};

// Pick<T, K> - pick specific properties
type UserPreview = Pick<User, 'name' | 'age'>;
const preview: UserPreview = { name: 'Charlie', age: 30 };

// Omit<T, K> - exclude specific properties
type UserPublic = Omit<User, 'email'>;
const publicUser: UserPublic = { name: 'David', age: 25 };

// Record<K, T> - create object with specific keys
type Status = 'pending' | 'completed' | 'failed';
const statusMessages: Record<Status, string> = {
  pending: 'In progress',
  completed: 'Done',
  failed: 'Error occurred'
};

// Exclude<U, T> - exclude types from union
type NonString = Exclude<string | number | boolean, string>; // number | boolean

// Extract<U, T> - extract types from union
type StringOrNumber = Extract<string | number | boolean, string | number>; // string | number

// NonNullable<T> - remove null and undefined
type NotNull = NonNullable<string | null | undefined>; // string

// ReturnType<T> - get return type of function
type Func = (x: number) => string;
type FuncReturn = ReturnType<Func>; // string

// Parameters<T> - get parameter types
type FuncParams = Parameters<Func>; // [x: number]

// Awaited<T> - unwrap Promise
type PromiseString = Promise<string>;
type Unwrapped = Awaited<PromiseString>; // string

// Readonly<T> - make all properties readonly
type ReadonlyUser = Readonly<User>;
const roUser: ReadonlyUser = { name: 'Eve', age: 25, email: 'eve@example.com' };
// roUser.name = 'Frank'; // Error: readonly
```

---

#### Q32: Explain conditional types in TypeScript.

**Answer:**

```typescript
// Basic conditional type
type IsString<T> = T extends string ? true : false;

type A = IsString<'hello'>; // true
type B = IsString<number>; // false

// Practical: Get array element type
type GetArrayElement<T> = T extends (infer U)[] ? U : never;
type ElementType = GetArrayElement<[1, 2, 3]>; // number

// Practical: Function return type extraction
type GetFunctionReturn<T> = T extends (...args: any[]) => infer R ? R : never;

function getName() { return 'Alice'; }
type NameReturn = GetFunctionReturn<typeof getName>; // string

// Nested conditionals
type Flatten<T> = T extends Array<infer U>
  ? Flatten<U> // Recursively flatten
  : T;

type Nested = Flatten<[[[1, 2], [3, 4]], [5, 6]]>; // number

// With distributed conditionals
type ToArray<T> = T extends any ? T[] : never;
type StrOrNumArray = ToArray<string | number>; // string[] | number[]

// Practical: API response typing
type ApiResponse<T> = {
  status: 'success' | 'error';
  data: T extends string ? string : T;
};

type StringResponse = ApiResponse<string>; // { status, data: string }
type ObjectResponse = ApiResponse<{ id: number }>; // { status, data: { id } }

// Practical: Form field types
type FormField<T> = T extends boolean
  ? { type: 'checkbox'; value: boolean }
  : T extends string
  ? { type: 'text'; value: string }
  : { type: 'input'; value: T };

type CheckboxField = FormField<boolean>; // { type: 'checkbox', value: boolean }
type TextField = FormField<string>; // { type: 'text', value: string }
```

---

#### Q33: What are mapped types and how do they work?

**Answer:**

```typescript
// Basic mapped type - iterate over union or object keys
type Keys = 'name' | 'age' | 'email';
type KeyToString = {
  [K in Keys]: string;
};
// Result: { name: string; age: string; email: string }

// Mapped types with objects
interface User {
  name: string;
  age: number;
  email: string;
}

// Make all properties optional
type Partial<T> = {
  [K in keyof T]?: T[K];
};

type PartialUser = Partial<User>;
// Result: { name?: string; age?: number; email?: string }

// Make all properties readonly
type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};

type ReadonlyUser = Readonly<User>;
// Result: { readonly name: string; readonly age: number; readonly email: string }

// Transform property types
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type UserGetters = Getters<User>;
// Result: { getName: () => string; getAge: () => number; getEmail: () => string }

// Add properties with key remapping
type Setters<T> = {
  [K in keyof T as `set${Capitalize<string & K>}`]: (value: T[K]) => void;
};

// Filter properties by type
type StringPropertiesOnly<T> = {
  [K in keyof T as T[K] extends string ? K : never]: T[K];
};

type UserStrings = StringPropertiesOnly<User>; // { name: string; email: string }

// Practical: API request type generator
type ApiRequest<T> = {
  [K in keyof T]: { field: K; value: T[K] };
};

type UserRequest = ApiRequest<User>;
// { name: { field: 'name'; value: string }, age: { field: 'age'; value: number }, ... }
```

---

#### Q34: What are discriminated unions and how do they help with type narrowing?

**Answer:**

```typescript
// Basic discriminated union
type Circle = { kind: 'circle'; radius: number };
type Square = { kind: 'square'; side: number };
type Shape = Circle | Square;

function getArea(shape: Shape) {
  if (shape.kind === 'circle') {
    return Math.PI * shape.radius ** 2; // shape is Circle here
  } else {
    return shape.side ** 2; // shape is Square here
  }
}

// API responses with discriminated unions
type SuccessResponse = {
  status: 'success';
  data: unknown;
};

type ErrorResponse = {
  status: 'error';
  message: string;
  code: number;
};

type ApiResponse = SuccessResponse | ErrorResponse;

function handleResponse(response: ApiResponse) {
  if (response.status === 'success') {
    console.log(response.data); // response is SuccessResponse
  } else {
    console.log(response.message); // response is ErrorResponse
    console.log(response.code);
  }
}

// More complex discriminator
type Pending = { status: 'pending' };
type Loading = { status: 'loading'; progress: number };
type Complete = { status: 'complete'; result: string };
type Failed = { status: 'failed'; error: Error };

type AsyncState = Pending | Loading | Complete | Failed;

function handleAsyncState(state: AsyncState) {
  switch (state.status) {
    case 'pending':
      return null; // state is Pending
    case 'loading':
      return `Loading: ${state.progress}%`; // state is Loading
    case 'complete':
      return state.result; // state is Complete
    case 'failed':
      throw state.error; // state is Failed
  }
}

// Practical: Form field discriminated union
type TextField = { type: 'text'; placeholder: string };
type CheckboxField = { type: 'checkbox'; label: string };
type SelectField = { type: 'select'; options: string[] };

type FormField = TextField | CheckboxField | SelectField;

function renderField(field: FormField) {
  switch (field.type) {
    case 'text':
      return `<input placeholder="${field.placeholder}" />`;
    case 'checkbox':
      return `<input type="checkbox" /><label>${field.label}</label>`;
    case 'select':
      return `<select>${field.options.map(o => `<option>${o}</option>`).join('')}</select>`;
  }
}
```

---

#### Q35: What are generic constraints in TypeScript?

**Answer:**

```typescript
// Basic constraint - T must extend a type
function getLength<T extends { length: number }>(obj: T): number {
  return obj.length;
}

getLength('hello'); // 5
getLength([1, 2, 3]); // 3
// getLength(123); // Error: number doesn't have length

// Constraint with keyof
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: 'Alice', age: 25 };
const name = getProperty(user, 'name'); // Type: string
// getProperty(user, 'invalid'); // Error

// Chained constraints
interface HasId {
  id: number;
}

function process<T extends HasId>(obj: T): number {
  return obj.id;
}

// Constraints with extends and conditionals
function getProp<T, K extends keyof T, V extends T[K]>(obj: T, key: K, defaultValue: V): T[K] | V {
  return obj[key] ?? defaultValue;
}

// Multiple constraints
function merge<T extends object, U extends object>(obj1: T, obj2: U): T & U {
  return { ...obj1, ...obj2 };
}

// Practical: Generic API client
interface ApiModel {
  id: string;
  createdAt: Date;
}

class Repository<T extends ApiModel> {
  async get(id: string): Promise<T> {
    // ...
  }

  async list(): Promise<T[]> {
    // ...
  }

  async delete(id: string): Promise<void> {
    // ...
  }
}

// Constructor constraints
function create<T extends new (...args: any[]) => any>(Constructor: T): InstanceType<T> {
  return new Constructor();
}

// Default generic values
function processArray<T = string>(arr: T[] = []): T[] {
  return arr;
}
```

---

#### Q36: What are TypeScript enums? Show different types and best practices.

**Answer:**

```typescript
// Numeric enum
enum Direction {
  Up = 0,
  Down = 1,
  Left = 2,
  Right = 3
}

const dir: Direction = Direction.Up;

// String enum
enum Status {
  Pending = 'PENDING',
  Complete = 'COMPLETE',
  Failed = 'FAILED'
}

const status: Status = Status.Pending;

// Heterogeneous enum (not recommended)
enum Mixed {
  No = 0,
  Yes = 'YES'
}

// Const enum (better performance, inlined at compile)
const enum Color {
  Red = 'RED',
  Green = 'GREEN',
  Blue = 'BLUE'
}

// Enums at runtime
function getStatusLabel(status: Status): string {
  switch (status) {
    case Status.Pending:
      return 'In Progress';
    case Status.Complete:
      return 'Done';
    case Status.Failed:
      return 'Error';
  }
}

// Better practice: Use union types instead
type StatusUnion = 'PENDING' | 'COMPLETE' | 'FAILED';

// Or with object
const StatusEnum = {
  Pending: 'PENDING',
  Complete: 'COMPLETE',
  Failed: 'FAILED'
} as const;

type StatusFromEnum = typeof StatusEnum[keyof typeof StatusEnum];

// Enum with computed members
enum Math {
  One = 1,
  Two = One * 2,
  Three = Two + 1
}
```

---

#### Q37: What are decorators in TypeScript? Show practical examples.

**Answer:**

```typescript
// Class decorator
function sealed(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}

@sealed
class Person {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

// Property decorator
function logged(target: any, propertyKey: string) {
  let value: any;

  const getter = () => {
    console.log(`Getting ${propertyKey}`);
    return value;
  };

  const setter = (newValue: any) => {
    console.log(`Setting ${propertyKey} to ${newValue}`);
    value = newValue;
  };

  Object.defineProperty(target, propertyKey, {
    get: getter,
    set: setter,
    enumerable: true,
    configurable: true
  });
}

class User {
  @logged
  name: string = '';
}

// Method decorator
function timing(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;

  descriptor.value = function(...args: any[]) {
    console.time(propertyKey);
    const result = originalMethod.apply(this, args);
    console.timeEnd(propertyKey);
    return result;
  };

  return descriptor;
}

class Calculator {
  @timing
  add(a: number, b: number): number {
    return a + b;
  }
}

// Parameter decorator
function validate(target: any, propertyKey: string, parameterIndex: number) {
  // Metadata about parameter validation
}

// Practical: Validation decorator
function required(target: any, propertyKey: string) {
  let value: any;

  Object.defineProperty(target, propertyKey, {
    set(newValue: any) {
      if (newValue == null) {
        throw new Error(`${propertyKey} cannot be empty`);
      }
      value = newValue;
    },
    get() {
      return value;
    }
  });
}

class Product {
  @required
  name: string = '';
}
```

---

#### Q38: Explain TypeScript module system. What's the difference between namespaces and modules?

**Answer:**

```typescript
// Modern modules (ES6)
// user.ts
export interface User {
  id: number;
  name: string;
}

export function getUser(id: number): User {
  return { id, name: 'Alice' };
}

// app.ts
import { User, getUser } from './user';
const user = getUser(1);

// Default exports
export default class Logger {
  log(msg: string) {
    console.log(msg);
  }
}

// Import default
import Logger from './logger';
const logger = new Logger();

// Re-export
export { User } from './user';
export * from './helpers';

// Namespaces (older, avoid in new code)
namespace Geometry {
  export interface Point {
    x: number;
    y: number;
  }

  export function distance(p1: Point, p2: Point): number {
    return Math.sqrt((p2.x - p1.x) ** 2 + (p2.y - p1.y) ** 2);
  }
}

const p1: Geometry.Point = { x: 0, y: 0 };
const p2: Geometry.Point = { x: 3, y: 4 };
Geometry.distance(p1, p2); // 5

// Triple-slash reference (old way, avoid)
/// <reference path="./types.d.ts" />

// Modern way: Declare files
// types.d.ts
declare module 'my-library' {
  export interface Config {
    // ...
  }
}
```

---

#### Q39: What are type guards and how do they work?

**Answer:**

```typescript
// typeof guard
function processValue(value: string | number) {
  if (typeof value === 'string') {
    return value.toUpperCase(); // value is string
  } else {
    return value.toFixed(2); // value is number
  }
}

// instanceof guard
class Dog {
  bark() { return 'Woof'; }
}

class Cat {
  meow() { return 'Meow'; }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    return animal.bark(); // animal is Dog
  } else {
    return animal.meow(); // animal is Cat
  }
}

// Truthiness guard
function printLength(str: string | null) {
  if (str) {
    console.log(str.length); // str is string
  }
}

// Equality guard
function compare(x: string | number, y: string | boolean) {
  if (x === y) {
    // x and y must be string
    return x.toLowerCase();
  }
}

// Custom type guard function
function isUser(obj: any): obj is User {
  return obj && typeof obj === 'object' && 'name' in obj && 'age' in obj;
}

interface User {
  name: string;
  age: number;
}

const data: unknown = { name: 'Alice', age: 25 };
if (isUser(data)) {
  console.log(data.name, data.age); // data is User
}

// in operator
interface Admin {
  adminLevel: number;
}

function isAdmin(user: User | Admin): user is Admin {
  return 'adminLevel' in user;
}

// Practical: API response guard
interface SuccessResponse<T> {
  status: 'success';
  data: T;
}

interface ErrorResponse {
  status: 'error';
  error: string;
}

function isSuccess<T>(response: SuccessResponse<T> | ErrorResponse): response is SuccessResponse<T> {
  return response.status === 'success';
}
```

---

#### Q40: What are abstract classes and interfaces? When would you use each?

**Answer:**

```typescript
// Abstract class - can have implementation
abstract class Animal {
  abstract makeSound(): string;

  move() {
    console.log('Moving...');
  }
}

class Dog extends Animal {
  makeSound(): string {
    return 'Woof';
  }
}

// Interface - only contracts
interface Drawable {
  draw(): void;
}

class Circle implements Drawable {
  draw() {
    // ...
  }
}

// Abstract class with properties
abstract class Vehicle {
  abstract type: string;
  abstract start(): void;

  stop() {
    console.log('Stopped');
  }

  constructor(public brand: string) {}
}

// Multiple interface implementation
interface Movable {
  move(): void;
}

interface Drawable2 {
  draw(): void;
}

class Robot implements Movable, Drawable2 {
  move() {}
  draw() {}
}

// When to use:
// Abstract class:
// - Share code between classes
// - Have protected/private members
// - Have state (properties with values)
// - Want access modifiers

// Interface:
// - Define contract
// - Multiple implementation
// - Lightweight type checking
// - No implementation
```

---

#### Q41: Explain TypeScript readonly keyword and its use cases.

**Answer:**

```typescript
// Readonly properties
interface Config {
  readonly apiUrl: string;
  readonly timeout: number;
}

const config: Config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000
};

// config.apiUrl = 'https://other.com'; // Error: readonly

// Readonly array
const readonlyArray: readonly string[] = ['a', 'b', 'c'];
// readonlyArray.push('d'); // Error

// Readonly tuple
type Point = readonly [number, number];
const p: Point = [1, 2];
// p[0] = 3; // Error

// Readonly with objects
interface User {
  readonly id: number;
  name: string;
}

const user: User = { id: 1, name: 'Alice' };
// user.id = 2; // Error
user.name = 'Bob'; // OK

// Readonly classes
class ImmutablePoint {
  readonly x: number;
  readonly y: number;

  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}

// as const - make all properties readonly
const config2 = {
  apiUrl: 'https://api.example.com',
  timeout: 5000
} as const;

// config2.apiUrl = 'other'; // Error
// config2.timeout = 10000; // Error
```

---

#### Q42: What is the difference between any, unknown, and never types?

**Answer:**

```typescript
// any - opt-out of type checking (avoid!)
let anyValue: any = 'string';
anyValue = 123;
anyValue.foo.bar(); // No error even if doesn't exist

// unknown - type-safe alternative to any
let unknownValue: unknown = 'string';
// unknownValue.toUpperCase(); // Error: need type guard first

if (typeof unknownValue === 'string') {
  unknownValue.toUpperCase(); // OK: narrowed to string
}

// never - impossible type (nothing can be assigned)
function throwError(msg: string): never {
  throw new Error(msg);
}

function infiniteLoop(): never {
  while (true) {}
}

// Practical: never with exhaustive checks
type Status = 'pending' | 'complete' | 'failed';

function handleStatus(status: Status) {
  switch (status) {
    case 'pending':
      return 'Pending';
    case 'complete':
      return 'Complete';
    case 'failed':
      return 'Failed';
    default:
      // If new status added, this will error (exhaustiveness check)
      const _exhaustive: never = status;
      return _exhaustive;
  }
}

// unknown vs any
function processUnknown(value: unknown) {
  if (typeof value === 'string') {
    console.log(value.length); // Safe
  }
}

function processAny(value: any) {
  console.log(value.length); // No error, but could fail at runtime
}
```

---

#### Q43: What are function overloads in TypeScript?

**Answer:**

```typescript
// Function overload signatures
function add(a: number, b: number): number;
function add(a: string, b: string): string;
function add(a: number | string, b: number | string): number | string {
  if (typeof a === 'string' && typeof b === 'string') {
    return a + b;
  }
  if (typeof a === 'number' && typeof b === 'number') {
    return a + b;
  }
  return '';
}

const sum = add(1, 2); // Type: number
const concat = add('hello', 'world'); // Type: string

// Method overloads in classes
class Logger {
  log(message: string): void;
  log(message: string, level: 'info' | 'error' | 'warn'): void;
  log(message: string, level?: string) {
    console.log(`[${level || 'info'}] ${message}`);
  }
}

// Practical: Flexible API
class Api {
  fetch(url: string): Promise<unknown>;
  fetch(url: string, options: RequestInit): Promise<unknown>;
  fetch(url: string, options?: RequestInit): Promise<unknown> {
    return fetch(url, options);
  }
}

// Optional parameters vs overloads
// Less clear:
function getUser(id: number | string, includeEmail?: boolean): User | null {}

// More clear with overloads:
function getUser(id: number): User | null;
function getUser(id: string, includeEmail: boolean): User | null;
function getUser(id: number | string, includeEmail?: boolean): User | null {
  // implementation
  return null;
}
```

---

#### Q44: Explain TypeScript's intersection types and when to use them.

**Answer:**

```typescript
// Basic intersection type
interface Person {
  name: string;
  age: number;
}

interface Employee {
  employeeId: number;
  salary: number;
}

type PersonEmployee = Person & Employee;

const person: PersonEmployee = {
  name: 'Alice',
  age: 25,
  employeeId: 123,
  salary: 50000
};

// Intersection with primitives
type StringNumber = string & number; // never (impossible type)

// Intersection with objects
interface Admin {
  adminLevel: number;
}

interface User {
  userId: number;
}

type AdminUser = Admin & User; // Must have all properties from both

// Practical: Extend functionality
interface Component {
  render(): JSX.Element;
}

interface WithTheme {
  theme: Theme;
}

type ThemedComponent = Component & WithTheme;

// Intersection can resolve conflicts
interface A {
  x: string;
}

interface B {
  x: string; // Same type - OK
}

type AB = A & B; // x: string

// Practical: Merge options
type Options1 = {
  color?: string;
  size?: number;
};

type Options2 = {
  shape?: 'circle' | 'square';
  padding?: number;
};

type AllOptions = Options1 & Options2;

const options: AllOptions = {
  color: 'red',
  size: 10,
  shape: 'circle',
  padding: 5
};
```

---

#### Q45: What is TypeScript's satisfies operator? Show modern pattern usage.

**Answer:**

```typescript
// satisfies operator - validate type without changing inference
const config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
  retries: 3
} satisfies { apiUrl: string; timeout: number; retries: number };

// config is inferred as exact type, not generic object
// So config.apiUrl is inferred as string literal 'https://api.example.com'

// Without satisfies:
const config2 = {
  apiUrl: 'https://api.example.com',
  timeout: 5000
} as { apiUrl: string; timeout: number };

// config2.apiUrl is type string, not literal

// Practical: Routes configuration
const routes = {
  home: { path: '/', name: 'Home' },
  about: { path: '/about', name: 'About' },
  contact: { path: '/contact', name: 'Contact' }
} satisfies Record<string, { path: string; name: string }>;

// routes.home.path is inferred as '/' not string
const homePath = routes.home.path; // Type: '/'

// Status mapping
const statusMap = {
  pending: 'PENDING',
  complete: 'COMPLETE',
  failed: 'FAILED'
} satisfies Record<'pending' | 'complete' | 'failed', string>;

// statusMap.pending is inferred as 'PENDING' not string
type Status = typeof statusMap[keyof typeof statusMap]; // 'PENDING' | 'COMPLETE' | 'FAILED'
```

---

#### Q46: What is a type assertion (`as` keyword)? When is it dangerous?

**Answer:**

```typescript
// Type assertion - tell compiler to treat as specific type
const elem = document.getElementById('myButton') as HTMLButtonElement;

// Without assertion: elem is HTMLElement | null
// With assertion: elem is HTMLButtonElement

// Dangerous - incorrect assertion
const user = '{ "name": "John" }' as User;
// Compiler trusts you - no validation at runtime!

// Safe - validation first
const validateUser = (data: unknown): User => {
  if (typeof data === 'object' && data !== null && 'name' in data) {
    return data as User;
  }
  throw new Error('Invalid user');
};

// Double assertion to bypass checks
const weird = 'hello' as any as number; // Bypass safety!

// Best practice - use type guards instead
function isUser(value: unknown): value is User {
  return (
    typeof value === 'object' &&
    value !== null &&
    'name' in value &&
    'email' in value
  );
}

if (isUser(data)) {
  // Type narrowed to User safely
  console.log(data.name);
}
```

---

#### Q47: What is the `keyof` operator? Show practical examples.

**Answer:**

```typescript
// keyof - get type union of object keys
interface User {
  name: string;
  age: number;
  email: string;
}

type UserKeys = keyof User; // 'name' | 'age' | 'email'

// Use in function
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

const user: User = { name: 'Alice', age: 28, email: 'alice@example.com' };
const name = getProperty(user, 'name'); // OK
// getProperty(user, 'invalid'); // Error - 'invalid' not in User

// Dynamic property access safely
const updateUser = <K extends keyof User>(key: K, value: User[K]) => {
  console.log(`Updating ${key} to ${value}`);
};

updateUser('name', 'Bob'); // OK
updateUser('age', 30); // OK
// updateUser('name', 30); // Error - type mismatch

// Get keys from arrays
type TupleKeys = keyof [string, number]; // '0' | '1' | 'length' | ...

// Get keys from Record
type Config = Record<'api' | 'timeout' | 'retries', string | number>;
type ConfigKeys = keyof Config; // 'api' | 'timeout' | 'retries'
```

---

#### Q48: What's the difference between `Pick` and `Omit` utility types?

**Answer:**

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  role: 'admin' | 'user';
}

// Pick - select specific properties
type UserPreview = Pick<User, 'id' | 'name' | 'email'>;
// {
//   id: number;
//   name: string;
//   email: string;
// }

// Omit - exclude specific properties
type UserPublic = Omit<User, 'password' | 'role'>;
// {
//   id: number;
//   name: string;
//   email: string;
// }

// Practical: API responses
type UserDTO = Pick<User, 'id' | 'name' | 'email'>;

// Form input (exclude id)
type UserInput = Omit<User, 'id' | 'role'>;

// Conditional picking
type SafeUser = Omit<User, 'password'>;

// Combining with other types
type AdminUser = Pick<User, 'id' | 'name'> & { permissions: string[] };
```

---

#### Q50: What is type-safe form handling in React with TypeScript?

**Answer:**

```tsx
interface FormData {
  name: string;
  email: string;
  age: number;
  subscribe: boolean;
}

function Form() {
  const [form, setForm] = useState<FormData>({
    name: '',
    email: '',
    age: 0,
    subscribe: false,
  });

  // Type-safe field update
  const handleChange = <K extends keyof FormData>(
    field: K,
    value: FormData[K]
  ) => {
    setForm(prev => ({
      ...prev,
      [field]: value,
    }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log(form); // form is correctly typed
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={form.name}
        onChange={(e) => handleChange('name', e.target.value)}
      />
      <input
        type="email"
        value={form.email}
        onChange={(e) => handleChange('email', e.target.value)}
      />
      <input
        type="number"
        value={form.age}
        onChange={(e) => handleChange('age', Number(e.target.value))}
      />
      <input
        type="checkbox"
        checked={form.subscribe}
        onChange={(e) => handleChange('subscribe', e.target.checked)}
      />
      <button type="submit">Submit</button>
    </form>
  );
}

// Better approach - custom hook
function useTypedForm<T>(initialState: T) {
  const [form, setForm] = useState<T>(initialState);

  const handleChange = <K extends keyof T>(field: K, value: T[K]) => {
    setForm(prev => ({
      ...prev,
      [field]: value,
    }));
  };

  return { form, handleChange };
}
```

---

#### Q49: What are Discriminated Unions in TypeScript? How are they used in React?

**Answer:**

```typescript
// Discriminated Union Pattern
type Result<T> = 
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: string };

function processResult<T>(result: Result<T>) {
  if (result.status === 'loading') {
    return <div>Loading...</div>;
  } else if (result.status === 'success') {
    // TypeScript knows data exists here
    console.log(result.data);
  } else if (result.status === 'error') {
    console.error(result.error);
  }
}

// In React components
type Action = 
  | { type: 'SET_USER'; payload: User }
  | { type: 'CLEAR_USER' }
  | { type: 'SET_ERROR'; error: Error };

function reducer(state: State, action: Action): State {
  switch(action.type) {
    case 'SET_USER':
      return { ...state, user: action.payload };
    case 'CLEAR_USER':
      return { ...state, user: null };
    case 'SET_ERROR':
      return { ...state, error: action.error };
  }
}
```

---

### Section 2B: Advanced JavaScript - DEEP DIVE (15+ Questions)

#### Q50A: What is closure? Show practical patterns and memory implications.

**Answer:**

```javascript
// Closure - function has access to outer scope variables
function outer() {
  let count = 0;
  
  function inner() {
    count++;
    console.log(count);
  }
  
  return inner;
}

const counter = outer();
counter(); // 1
counter(); // 2
counter(); // 3
// 'count' persists in memory due to closure

// Practical: Private variables pattern
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Private variable
  
  return {
    deposit(amount) {
      balance += amount;
      return balance;
    },
    withdraw(amount) {
      balance -= amount;
      return balance;
    },
    getBalance() {
      return balance;
    }
  };
}

const account = createBankAccount(1000);
account.deposit(100); // 1100
account.withdraw(50); // 1050
// Can't access balance directly: account.balance is undefined

// Common closure gotchas
const functions = [];
for (var i = 0; i < 3; i++) {
  functions.push(() => console.log(i)); // Bug: all reference same 'i'
}
functions.forEach(fn => fn()); // 3, 3, 3

// Fix with closure
const functionsFixed = [];
for (var i = 0; i < 3; i++) {
  functionsFixed.push(((num) => () => console.log(num))(i));
}
functionsFixed.forEach(fn => fn()); // 0, 1, 2

// Better: Use let instead
const functionsLet = [];
for (let i = 0; i < 3; i++) {
  functionsLet.push(() => console.log(i));
}
functionsLet.forEach(fn => fn()); // 0, 1, 2

// Memory implications
function createLargeClosure() {
  const largeArray = new Array(1000000).fill('data'); // Persists in memory
  return () => largeArray.length; // Holds reference to largeArray
}

const fn = createLargeClosure();
// largeArray won't be garbage collected as long as fn exists
fn(); // returns 1000000
```

**Key Concepts:**
- Functions remember variables from their outer scope
- Each closure has its own independent copy of variables
- Useful for data privacy and factory functions
- Can cause memory leaks if not careful

---

#### Q50B: Explain Prototype Chain. How does inheritance work in JavaScript?

**Answer:**

```javascript
// Every object has a prototype
const obj = {};
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// Prototype chain
const parent = {
  greet() { return 'Hello'; }
};

const child = Object.create(parent);
child.greet(); // 'Hello' - looks up prototype chain

// Constructor functions and prototypes
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  return `Hi, I'm ${this.name}`;
};

const person = new Person('John');
person.greet(); // "Hi, I'm John"

// Prototype chain lookup
console.log(person.toString()); // Looks in:
// 1. person object itself - not found
// 2. Person.prototype - not found
// 3. Object.prototype - found!

// Class syntax (modern, cleaner)
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  makeSound() {
    return 'Some sound';
  }
}

class Dog extends Animal {
  makeSound() {
    return 'Woof!';
  }
}

const dog = new Dog('Rex');
dog.makeSound(); // 'Woof!'

// Inheritance chain
console.log(dog instanceof Dog); // true
console.log(dog instanceof Animal); // true
console.log(dog instanceof Object); // true

// Checking prototype chain
console.log(Person.prototype.isPrototypeOf(person)); // true
```

**Key Concepts:**
- Prototype chain enables inheritance
- Objects look up properties through the chain
- `new` keyword sets up prototype relationship
- Classes are syntactic sugar over prototypes

---

#### Q50C: What's the difference between `var`, `let`, `const`? Explain Temporal Dead Zone.

**Answer:**

```javascript
// var - function scoped, hoisted
function example() {
  if (true) {
    var x = 1;
  }
  console.log(x); // 1 (accessible outside block!)
}

// let - block scoped, temporal dead zone
function example2() {
  // console.log(y); // ReferenceError - TDZ
  let y = 1;
  console.log(y); // 1
}

// Temporal Dead Zone (TDZ)
function tdz() {
  try {
    console.log(x); // ReferenceError: can't access 'x' before initialization
  } catch (e) {
    console.error(e.message);
  }
  
  let x = 1;
}

// var is hoisted and initialized to undefined
console.log(typeof a); // 'undefined' (not ReferenceError!)
var a = 1;

// const - block scoped, immutable reference
const obj = { name: 'John' };
obj.name = 'Jane'; // OK - property changed
// obj = {}; // Error - can't reassign

// Best practices
const PI = 3.14159; // Constant
let count = 0; // Will change
count++;

function scope() {
  if (true) {
    let blockScoped = 'only here';
  }
  // blockScoped is not accessible here
}
```

**Rules:**
- `const` by default
- `let` when reassignment needed
- Avoid `var` (except legacy code)
- Always aware of scope

---

#### Q50D: Explain memory management and garbage collection in JavaScript.

**Answer:**

```javascript
// Memory lifecycle: allocation → use → deallocation

// 1. Reference counting (old)
// Object kept in memory as long as references exist
let obj = { data: 'large' };
let ref = obj; // 2 references
obj = null; // 1 reference
ref = null; // 0 references → eligible for GC

// 2. Mark and sweep (modern)
// GC traces reachable objects from root

// Memory leaks
// Problem 1: Global variables
function badFunction() {
  globalVar = 'leaks'; // Creates global reference
}

// Problem 2: Forgotten timers
const timer = setInterval(() => {
  console.log('Still running');
}, 1000);
// Forgot to clearInterval - keeps callback in memory

// Problem 3: Detached DOM nodes
let element = document.getElementById('myElement');
document.body.removeChild(element);
// element reference still exists, keeps node in memory

// Problem 4: Closures holding references
function createLeak() {
  const largeData = new Array(1000000).fill('data');
  return {
    getData: () => largeData.length, // Keeps largeData in memory
  };
}

// Good practices
// Explicit cleanup
class EventListener {
  constructor() {
    this.handler = () => console.log('clicked');
    document.addEventListener('click', this.handler);
  }
  
  cleanup() {
    document.removeEventListener('click', this.handler);
  }
}

const listener = new EventListener();
listener.cleanup(); // Cleanup when done

// WeakMap for caching without memory leaks
const cache = new WeakMap();

function processData(obj) {
  if (cache.has(obj)) {
    return cache.get(obj);
  }
  
  const result = expensiveComputation(obj);
  cache.set(obj, result);
  return result;
}

// If obj is garbage collected, entry is automatically removed

// AbortController for cleanup
const controller = new AbortController();

fetch('/api/data', { signal: controller.signal })
  .then(r => r.json())
  .catch(e => {
    if (e.name === 'AbortError') {
      console.log('Request cancelled');
    }
  });

// Cancel when component unmounts
controller.abort();
```

**Key Insights:**
- Objects are freed when no longer reachable
- Avoid global variables and forgotten timers
- Clean up event listeners
- Use WeakMap for caches

---

#### Q50E: What are iterators and generators? Show practical use cases.

**Answer:**

```javascript
// Iterator - object with next() method
function createIterator() {
  let count = 0;
  return {
    next() {
      return {
        value: count++,
        done: count > 3
      };
    }
  };
}

const iterator = createIterator();
console.log(iterator.next()); // { value: 0, done: false }
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: true }

// Generator - function that returns iterator
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }

// Generators for lazy evaluation
function* infiniteNumbers() {
  let n = 0;
  while (true) {
    yield n++;
  }
}

const nums = infiniteNumbers();
console.log(nums.next()); // 0
console.log(nums.next()); // 1
console.log(nums.next()); // 2

// Practical: Paginated data
function* paginate(data, pageSize) {
  for (let i = 0; i < data.length; i += pageSize) {
    yield data.slice(i, i + pageSize);
  }
}

const items = Array.from({ length: 100 }, (_, i) => i);
for (const page of paginate(items, 10)) {
  console.log(`Page: ${page.length} items`);
}

// Generator with two-way communication
function* dialog() {
  const name = yield 'What is your name?';
  const age = yield `Hello ${name}, what is your age?`;
  return `${name} is ${age} years old`;
}

const conv = dialog();
console.log(conv.next().value); // 'What is your name?'
console.log(conv.next('John').value); // 'Hello John, what is your age?'
console.log(conv.next(30).value); // 'John is 30 years old'

// Async generator
async function* fetchPages(url) {
  let page = 1;
  while (true) {
    const response = await fetch(`${url}?page=${page}`);
    const data = await response.json();
    yield data;
    if (!data.hasMore) break;
    page++;
  }
}

for await (const pageData of fetchPages('/api/items')) {
  console.log(pageData);
}
```

**Use Cases:**
- Lazy evaluation of large datasets
- Infinite sequences
- Custom iteration logic
- Async data streaming

---

#### Q50F: Explain Proxy and Reflect. Show practical applications.

**Answer:**

```javascript
// Proxy - intercepts operations on objects
const target = { name: 'John', age: 30 };

const handler = {
  get(target, prop) {
    console.log(`Accessing ${prop}`);
    return target[prop];
  },
  set(target, prop, value) {
    console.log(`Setting ${prop} to ${value}`);
    if (typeof value === 'number' && value < 0) {
      throw new Error('Age cannot be negative');
    }
    target[prop] = value;
    return true;
  }
};

const proxy = new Proxy(target, handler);
console.log(proxy.name); // Logs: Accessing name
proxy.age = -5; // Throws error

// Practical 1: Validation
function createValidatedObject(schema) {
  return new Proxy({}, {
    set(target, prop, value) {
      const validator = schema[prop];
      if (validator && !validator(value)) {
        throw new TypeError(`Invalid ${prop}: ${value}`);
      }
      target[prop] = value;
      return true;
    }
  });
}

const userSchema = {
  email: v => /^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/.test(v),
  age: v => Number.isInteger(v) && v > 0
};

const user = createValidatedObject(userSchema);
user.email = 'john@example.com'; // OK
// user.email = 'invalid'; // Throws error

// Practical 2: Computed properties
const person = new Proxy({ firstName: 'John', lastName: 'Doe' }, {
  get(target, prop) {
    if (prop === 'fullName') {
      return `${target.firstName} ${target.lastName}`;
    }
    return target[prop];
  }
});

console.log(person.fullName); // 'John Doe'

// Reflect - provides methods to interact with objects
const obj = { x: 1, y: 2 };

// Old way
console.log(Object.keys(obj)); // ['x', 'y']

// Reflect way (parallel API)
console.log(Reflect.ownKeys(obj)); // ['x', 'y']

// Useful for consistent behavior
function safeProp(obj, prop, defaultValue) {
  try {
    return Reflect.get(obj, prop) ?? defaultValue;
  } catch {
    return defaultValue;
  }
}

console.log(safeProp(obj, 'x', 0)); // 1
console.log(safeProp(null, 'x', 0)); // 0 (no error)

// Proxy + Reflect combination
const middleware = new Proxy(target, {
  get(target, prop) {
    console.log(`Getting ${prop}`);
    return Reflect.get(target, prop);
  },
  set(target, prop, value) {
    console.log(`Setting ${prop}`);
    return Reflect.set(target, prop, value);
  },
  deleteProperty(target, prop) {
    console.log(`Deleting ${prop}`);
    return Reflect.deleteProperty(target, prop);
  }
});
```

**Applications:**
- Input validation
- Observable/reactive objects
- Access control
- Performance monitoring

---

#### Q50G: What are WeakMap and WeakSet? When to use them?

**Answer:**

```javascript
// WeakMap - keys must be objects, keys are weakly referenced
const wm = new WeakMap();

const obj1 = { id: 1 };
const obj2 = { id: 2 };

wm.set(obj1, 'data for obj1');
wm.set(obj2, 'data for obj2');

console.log(wm.get(obj1)); // 'data for obj1'

// Key difference: keys can be garbage collected
let key = { id: 3 };
wm.set(key, 'data');
console.log(wm.has(key)); // true
key = null; // obj eligible for GC
// Eventually: wm.has(that obj) will be false

// Use case: DOM node metadata
const domMetadata = new WeakMap();

function attachMetadata(element, meta) {
  domMetadata.set(element, meta);
}

function getMetadata(element) {
  return domMetadata.get(element);
}

const button = document.querySelector('button');
attachMetadata(button, { clickCount: 0 });
// If button is removed from DOM: metadata auto-removed

// WeakSet - stores objects weakly
const ws = new WeakSet();

const obj = { name: 'test' };
ws.add(obj);

console.log(ws.has(obj)); // true
obj = null; // eligible for GC

// Use case: Mark objects as processed
const processed = new WeakSet();

function processIf NotProcessed(obj) {
  if (!processed.has(obj)) {
    console.log('Processing:', obj);
    processed.add(obj);
  }
}

const item1 = { id: 1 };
processIfNotProcessed(item1); // Processes
processIfNotProcessed(item1); // Doesn't process

// Caching with WeakMap
const cache = new WeakMap();

function expensiveOperation(obj) {
  if (cache.has(obj)) {
    console.log('From cache');
    return cache.get(obj);
  }
  
  const result = complexCalculation(obj);
  cache.set(obj, result);
  return result;
}

// Compared to Map (strong references)
const strongMap = new Map();
let temp = { id: 1 };
strongMap.set(temp, 'data');
temp = null; // Still in strongMap!

const weakMap = new WeakMap();
temp = { id: 2 };
weakMap.set(temp, 'data');
temp = null; // Auto-removed when GC runs
```

**Key Differences:**
- **WeakMap/WeakSet**: Keys are weakly referenced, not enumerable
- **Map/Set**: Strong references, fully enumerable
- Use WeakMap for private data and caches
- Use Map for regular collections

---

### Section 3A: React Core - EXPANDED (20+ Questions)

#### Q51: What are React Hooks? Explain useState, useEffect, and useContext.

**Answer:**

```javascript
// useState - Manage component state
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
    </>
  );
}

// useEffect - Side effects (data fetching, subscriptions, etc)
function DataFetcher({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    let mounted = true;

    const fetchUser = async () => {
      const data = await fetch(`/api/users/${userId}`).then(r => r.json());
      if (mounted) setUser(data);
    };

    fetchUser();

    return () => {
      mounted = false; // Cleanup
    };
  }, [userId]); // Re-run when userId changes

  return <div>{user?.name}</div>;
}

// useContext - Access context without wrapping
const ThemeContext = React.createContext('light');

function Button() {
  const theme = useContext(ThemeContext);
  return <button className={theme}>Click</button>;
}

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Button />
    </ThemeContext.Provider>
  );
}
```

---

#### Q52: What are controlled vs uncontrolled components?

**Answer:**

```javascript
// Controlled - React controls form state
function ControlledInput() {
  const [value, setValue] = useState('');

  return (
    <input
      value={value}
      onChange={(e) => setValue(e.target.value)}
    />
  );
}

// Uncontrolled - DOM controls state
function UncontrolledInput() {
  const inputRef = useRef();

  const handleSubmit = () => {
    console.log(inputRef.current.value);
  };

  return (
    <>
      <input ref={inputRef} defaultValue="initial" />
      <button onClick={handleSubmit}>Submit</button>
    </>
  );
}

// When to use:
// ✓ Controlled: Form validation, conditional rendering
// ✓ Uncontrolled: Simple forms, integrating with non-React code
```

---

#### Q53: How do you implement form validation in React?

**Answer:**

```javascript
function Form() {
  const [formData, setFormData] = useState({ email: '', password: '' });
  const [errors, setErrors] = useState({});

  const validate = (data) => {
    const newErrors = {};

    if (!data.email.includes('@')) {
      newErrors.email = 'Invalid email';
    }

    if (data.password.length < 6) {
      newErrors.password = 'Password too short';
    }

    return newErrors;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const newErrors = validate(formData);

    if (Object.keys(newErrors).length === 0) {
      // Submit
      console.log('Form valid');
    } else {
      setErrors(newErrors);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={formData.email}
        onChange={(e) => setFormData({ ...formData, email: e.target.value })}
      />
      {errors.email && <span>{errors.email}</span>}
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

#### Q54: How do you handle routing in React? Show React Router patterns.

**Answer:**

```javascript
import { BrowserRouter, Routes, Route, Link, useParams } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/users">Users</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/users" element={<UsersList />} />
        <Route path="/users/:id" element={<UserDetail />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

function UserDetail() {
  const { id } = useParams();
  return <div>User ID: {id}</div>;
}
```

---

#### Q55: What is lazy loading and code splitting in React?

**Answer:**

```javascript
import { lazy, Suspense } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  );
}

// With React Router
const Profile = lazy(() => import('./Profile'));
const Dashboard = lazy(() => import('./Dashboard'));

<Routes>
  <Route path="/profile" element={<Suspense fallback={<Loading />}><Profile /></Suspense>} />
  <Route path="/dashboard" element={<Suspense fallback={<Loading />}><Dashboard /></Suspense>} />
</Routes>
```

---

#### Q56: How do you manage form state in React? Show patterns beyond useState.

**Answer:**

```javascript
// useReducer for complex forms
const initialState = { name: '', email: '', errors: {} };

function formReducer(state, action) {
  switch(action.type) {
    case 'SET_FIELD':
      return { ...state, [action.field]: action.value };
    case 'SET_ERRORS':
      return { ...state, errors: action.errors };
    case 'RESET':
      return initialState;
    default:
      return state;
  }
}

function Form() {
  const [state, dispatch] = useReducer(formReducer, initialState);

  const handleChange = (e) => {
    const { name, value } = e.target;
    dispatch({ type: 'SET_FIELD', field: name, value });
  };

  return (
    <form>
      <input name="name" value={state.name} onChange={handleChange} />
      {state.errors.name && <span>{state.errors.name}</span>}
    </form>
  );
}
```

---

#### Q57: What is React Context API? When should you use it vs Redux?

**Answer:**

```javascript
// Context API
const UserContext = React.createContext();

export function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  
  return (
    <UserContext.Provider value={{ user, setUser }}>
      {children}
    </UserContext.Provider>
  );
}

// Use it
function UserProfile() {
  const { user } = useContext(UserContext);
  return <div>{user?.name}</div>;
}

// When to use:
// Context: Simple, app-wide state (theme, auth)
// Redux: Complex state, time-travel debugging, large teams
```

---

#### Q58: How do you optimize React component rendering?

**Answer:**

```javascript
// React.memo - Prevent re-renders
const MemoizedComponent = React.memo(({ prop }) => {
  return <div>{prop}</div>;
});

// useMemo - Memoize computations
const sortedList = useMemo(() => {
  return items.sort(...);
}, [items]);

// useCallback - Memoize callbacks
const handleClick = useCallback(() => {
  console.log('clicked');
}, []);

// useTransition - Non-blocking updates
const [isPending, startTransition] = useTransition();

startTransition(() => {
  setItems(heavyComputation());
});
```

---

#### Q59: How do you implement error boundaries?

**Answer:**

```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <div>Something went wrong</div>;
    }
    return this.props.children;
  }
}

// Usage
<ErrorBoundary>
  <MyComponent />
</ErrorBoundary>
```

---

#### Q60: What are React Portals and when do you use them?

**Answer:**

```javascript
import { createPortal } from 'react-dom';

function Modal({ children }) {
  return createPortal(
    <div className="modal">{children}</div>,
    document.getElementById('modal-root')
  );
}

// Use cases: Modals, Tooltips, Dropdowns
```

---

#### Q61: How do you handle performance monitoring in React?

**Answer:**

```javascript
import { Suspense, lazy } from 'react';

// Code splitting
const HeavyComponent = lazy(() => import('./Heavy'));

// useTransition for non-blocking updates
const [isPending, startTransition] = useTransition();

// Profiler API
import { Profiler } from 'react';

<Profiler id="main" onRender={onRender}>
  <App />
</Profiler>
```

---

#### Q62: What is key prop and why is it important in lists?

**Answer:**

```javascript
// ✓ Correct - Unique, stable ID
<ul>
  {items.map(item => (
    <li key={item.id}>{item.name}</li>
  ))}
</ul>

// ✗ Wrong - Using index causes issues
{items.map((item, index) => (
  <li key={index}>{item.name}</li>
))}

// Why:
// - React uses key to match elements between renders
// - Index as key causes problems when list reorders
// - Each key must be unique and stable
```

---

#### Q63: How do you implement authentication in React?

**Answer:**

```javascript
function useAuth() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    checkAuth().then(user => {
      setUser(user);
      setLoading(false);
    });
  }, []);

  return { user, loading };
}

// Protected route
function ProtectedRoute({ children }) {
  const { user, loading } = useAuth();

  if (loading) return <div>Loading...</div>;
  if (!user) return <Navigate to="/login" />;

  return children;
}
```

---

#### Q64: How do you handle data fetching in React?

**Answer:**

```javascript
function useData(url) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let isMounted = true;

    fetch(url)
      .then(r => r.json())
      .then(data => {
        if (isMounted) setData(data);
      })
      .catch(err => {
        if (isMounted) setError(err);
      })
      .finally(() => {
        if (isMounted) setLoading(false);
      });

    return () => {
      isMounted = false;
    };
  }, [url]);

  return { data, error, loading };
}
```

---

#### Q65: How do you test React components?

**Answer:**

```javascript
import { render, screen } from '@testing-library/react';

describe('Button', () => {
  it('renders', () => {
    render(<button>Click</button>);
    expect(screen.getByRole('button')).toBeInTheDocument();
  });

  it('calls onClick', () => {
    const handleClick = jest.fn();
    render(<button onClick={handleClick}>Click</button>);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalled();
  });
});
```

---

### Section 3B: React Hooks Deep Dive (18 Questions - ADVANCED)

#### Q51A: Deep dive into useCallback. When and why to use it? Common pitfalls?

**Answer:**

```javascript
// useCallback - memoizes function reference
// Problem without useCallback:
function ParentWithoutMemo() {
  const [count, setCount] = useState(0);
  
  // This function is recreated on EVERY render
  const handleClick = () => setCount(c => c + 1);
  
  return <Child onClick={handleClick} />;
}

// Child receives new function reference every render
// Even if props haven't "changed"
function Child({ onClick }) {
  // Renders every time, even if onClick logic is identical!
  console.log('Child rendered');
  return <button onClick={onClick}>Click</button>;
}

// Solution with useCallback:
function ParentWithMemo() {
  const [count, setCount] = useState(0);
  const [data, setData] = useState([]);
  
  const handleClick = useCallback(
    () => setCount(c => c + 1),
    [] // Dependencies
  );
  
  return (
    <>
      <button onClick={handleClick}>Count: {count}</button>
      <MemoizedChild onClick={handleClick} />
    </>
  );
}

const MemoizedChild = React.memo(({ onClick }) => {
  console.log('Child rendered'); // Only once unless onClick changes
  return <button onClick={onClick}>Click me</button>;
});

// Common Pitfall 1: Including state directly in deps
function BadExample() {
  const [count, setCount] = useState(0);
  
  // ❌ BAD: callback depends on current 'count'
  const handleClick = useCallback(
    () => console.log(count), // Always needs new callback
    [count] // Dependency changes every time count changes
  );
  
  return <button onClick={handleClick}>Click</button>;
}

// ✅ Good: Use functional update
function GoodExample() {
  const [count, setCount] = useState(0);
  
  const handleClick = useCallback(
    () => console.log(count), // Uses current value from functional update
    []
  );
  
  return <button onClick={handleClick}>Click</button>;
}

// Common Pitfall 2: Incorrect dependency array
function PitfallExample() {
  const [count, setCount] = useState(0);
  const userObj = { name: 'John' }; // New object every render!
  
  // ❌ This will create new callback every render
  const callback = useCallback(
    () => console.log(userObj.name),
    [userObj] // userObj is new every render
  );
  
  return <div>{callback}</div>;
}

// ✅ Fix: Memoize the object or use just the value
function FixedExample() {
  const [count, setCount] = useState(0);
  const userName = 'John';
  
  const callback = useCallback(
    () => console.log(userName),
    [userName] // Primitive, won't cause unnecessary updates
  );
  
  return <div>{callback}</div>;
}

// Advanced: useCallback with exhaustive deps
function AdvancedExample() {
  const [items, setItems] = useState([]);
  const [filter, setFilter] = useState('');
  
  const filteredItems = useCallback(
    () => items.filter(item => item.includes(filter)),
    [items, filter] // All dependencies included
  );
  
  useEffect(() => {
    const result = filteredItems();
    console.log('Filtered:', result);
  }, [filteredItems]); // Now safe to use
  
  return <div>{filteredItems().length} items</div>;
}

// Real-world: API request with cleanup
function UserForm() {
  const [user, setUser] = useState(null);
  
  const fetchUser = useCallback(async (id) => {
    const response = await fetch(`/api/users/${id}`);
    setUser(await response.json());
  }, []);
  
  useEffect(() => {
    const id = 123;
    let cancelled = false;
    
    fetchUser(id).then(() => {
      if (!cancelled) {
        // Update state
      }
    });
    
    return () => { cancelled = true; };
  }, [fetchUser]);
}
```

**Best Practices:**
- Use useCallback when passing callbacks to memoized children
- Keep dependency arrays accurate
- Don't over-memoize (adds complexity)
- Profile before optimizing

---

#### Q51B: useReducer vs useState - when to use which? Complex state management.

**Answer:**

```javascript
// useState - simple state
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// useReducer - complex state transitions
function ComplexCounter() {
  const initialState = { count: 0, step: 1, history: [] };
  
  function reducer(state, action) {
    switch (action.type) {
      case 'INCREMENT':
        return {
          ...state,
          count: state.count + state.step,
          history: [...state.history, state.count]
        };
      case 'SET_STEP':
        return { ...state, step: action.payload };
      case 'RESET':
        return initialState;
      default:
        return state;
    }
  }
  
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return (
    <div>
      <p>Count: {state.count}</p>
      <p>Step: {state.step}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>
        Increment
      </button>
      <input
        value={state.step}
        onChange={(e) => 
          dispatch({ 
            type: 'SET_STEP', 
            payload: Number(e.target.value) 
          })
        }
      />
      <button onClick={() => dispatch({ type: 'RESET' })}>Reset</button>
    </div>
  );
}

// Decision Matrix
class DecisionHelper {
  // Use useState when:
  static useState = {
    singleValue: true, // One simple value
    independentUpdates: true, // Updates don't depend on other state
    simple: true, // Logic is simple
    example: 'username, isLoading, email'
  };
  
  // Use useReducer when:
  static useReducer = {
    complexState: true, // Multiple related values
    relatedUpdates: true, // Updates depend on current state
    complexLogic: true, // Many action types
    predictable: true, // Can predict state transitions
    example: 'form with multiple fields, complex workflows'
  };
}

// Complex Form Example with useReducer
function ComplexForm() {
  const initialState = {
    values: { name: '', email: '', password: '' },
    errors: {},
    touched: {},
    isSubmitting: false,
    submitCount: 0
  };
  
  function formReducer(state, action) {
    switch (action.type) {
      case 'SET_FIELD':
        return {
          ...state,
          values: {
            ...state.values,
            [action.field]: action.value
          }
        };
      case 'SET_ERROR':
        return {
          ...state,
          errors: {
            ...state.errors,
            [action.field]: action.error
          }
        };
      case 'TOUCH_FIELD':
        return {
          ...state,
          touched: {
            ...state.touched,
            [action.field]: true
          }
        };
      case 'SET_SUBMITTING':
        return {
          ...state,
          isSubmitting: action.payload,
          submitCount: action.payload ? state.submitCount + 1 : state.submitCount
        };
      case 'RESET':
        return initialState;
      default:
        return state;
    }
  }
  
  const [state, dispatch] = useReducer(formReducer, initialState);
  
  const handleChange = (field, value) => {
    dispatch({ type: 'SET_FIELD', field, value });
  };
  
  return (
    <form>
      <input
        value={state.values.name}
        onChange={(e) => handleChange('name', e.target.value)}
      />
      {state.touched.name && state.errors.name && <span>{state.errors.name}</span>}
    </form>
  );
}

// Combining both approaches
function SmartComponent() {
  const [simpleValue, setSimpleValue] = useState('');
  const [complexState, dispatch] = useReducer(complexReducer, initialState);
  
  // Best of both worlds
  return <div>{simpleValue}</div>;
}
```

**Decision Guide:**
- **useState**: Simple, single values, independent logic
- **useReducer**: Complex state, multiple related values, predictable transitions

---

#### Q51C: Custom Hooks - composition patterns, best practices, common mistakes.

**Answer:**

```javascript
// Simple custom hook
function useFetch(url) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    let cancelled = false;
    
    (async () => {
      try {
        const response = await fetch(url);
        const json = await response.json();
        if (!cancelled) {
          setData(json);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    })();
    
    return () => { cancelled = true; };
  }, [url]);
  
  return { data, error, loading };
}

// Hook composition - building complex hooks from simple ones
function useUser(userId) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);
  const [preferences, setPreferences] = useState(null);
  
  useEffect(() => {
    if (user) {
      // Fetch preferences based on user
      (async () => {
        const prefs = await fetch(`/api/preferences/${user.id}`);
        setPreferences(await prefs.json());
      })();
    }
  }, [user]);
  
  return { user, preferences, loading, error };
}

// Reducer inside custom hook
function useAsync(asyncFunction, immediate = true) {
  const [state, dispatch] = useReducer(
    (state, action) => {
      switch (action.type) {
        case 'PENDING':
          return { ...state, loading: true };
        case 'SUCCESS':
          return { data: action.payload, loading: false, error: null };
        case 'ERROR':
          return { data: null, loading: false, error: action.payload };
        default:
          return state;
      }
    },
    { data: null, loading: false, error: null }
  );
  
  const execute = useCallback(async (...args) => {
    dispatch({ type: 'PENDING' });
    try {
      const response = await asyncFunction(...args);
      dispatch({ type: 'SUCCESS', payload: response });
    } catch (error) {
      dispatch({ type: 'ERROR', payload: error });
    }
  }, [asyncFunction]);
  
  useEffect(() => {
    if (immediate) {
      execute();
    }
  }, [execute, immediate]);
  
  return { ...state, execute };
}

// Using combined hook
function MyComponent() {
  const { data: users, loading, execute } = useAsync(
    () => fetch('/api/users').then(r => r.json()),
    true
  );
  
  return (
    <div>
      {loading ? 'Loading...' : users?.map(u => <div key={u.id}>{u.name}</div>)}
      <button onClick={() => execute()}>Refresh</button>
    </div>
  );
}

// Advanced: localStorage hook
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });
  
  const setValue = useCallback((value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  }, [key, storedValue]);
  
  return [storedValue, setValue];
}

// Usage
function TodoApp() {
  const [todos, setTodos] = useLocalStorage('todos', []);
  
  const addTodo = (text) => {
    setTodos(prev => [...prev, { id: Date.now(), text }]);
  };
  
  return (
    <div>
      {todos.map(todo => <div key={todo.id}>{todo.text}</div>)}
      <button onClick={() => addTodo('New todo')}>Add</button>
    </div>
  );
}

// Common mistake: Infinite loop
function BadHook() {
  const [data, setData] = useState(null);
  
  // ❌ WRONG: infinite loop
  useEffect(() => {
    fetch('/api/data').then(r => r.json()).then(setData);
  }); // No dependency array!
  
  return <div>{data}</div>;
}

// ✅ CORRECT
function GoodHook() {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    fetch('/api/data').then(r => r.json()).then(setData);
  }, []); // Runs once on mount
  
  return <div>{data}</div>;
}
```

**Best Practices:**
- Follow Hook Rules (only at top level, in components/hooks)
- Use proper cleanup functions
- Keep dependencies accurate
- Compose hooks for complex logic
- Document hook parameters and return values

---

#### Q51D: useRef - when to use, memoization, DOM access, forwarding refs.

**Answer:**

```javascript
// useRef - persistent reference across renders
function Timer() {
  const intervalRef = useRef(null);
  const [seconds, setSeconds] = useState(0);
  
  const startTimer = () => {
    // Store interval ID to clear later
    intervalRef.current = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);
  };
  
  const stopTimer = () => {
    clearInterval(intervalRef.current);
  };
  
  return (
    <div>
      <p>Seconds: {seconds}</p>
      <button onClick={startTimer}>Start</button>
      <button onClick={stopTimer}>Stop</button>
    </div>
  );
}

// DOM access
function TextInput() {
  const inputRef = useRef(null);
  
  const focusInput = () => {
    inputRef.current.focus();
  };
  
  return (
    <>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus Input</button>
    </>
  );
}

// Forwarding refs - pass ref through components
const TextInputWrapper = forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});

function Parent() {
  const inputRef = useRef(null);
  
  return (
    <>
      <TextInputWrapper ref={inputRef} />
      <button onClick={() => inputRef.current.focus()}>Focus</button>
    </>
  );
}

// useRef for previous value
function Counter() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();
  
  useEffect(() => {
    prevCountRef.current = count;
  }, [count]);
  
  return (
    <div>
      <p>Now: {count}, Before: {prevCountRef.current}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// useImperativeHandle - customize ref interface
const CustomTextInput = forwardRef((props, ref) => {
  const inputRef = useRef(null);
  
  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
    clear: () => { inputRef.current.value = ''; },
    getValue: () => inputRef.current.value
  }), []);
  
  return <input ref={inputRef} {...props} />;
});

function Form() {
  const customInputRef = useRef(null);
  
  const handleReset = () => {
    customInputRef.current.clear();
  };
  
  const handleSubmit = () => {
    const value = customInputRef.current.getValue();
    console.log('Form value:', value);
  };
  
  return (
    <form>
      <CustomTextInput ref={customInputRef} />
      <button onClick={handleReset}>Reset</button>
      <button onClick={handleSubmit}>Submit</button>
    </form>
  );
}

// Memoizing expensive computations with useRef
function expensive Component() {
  const cacheRef = useRef({});
  
  const computeExpensive = (input) => {
    if (cacheRef.current[input]) {
      return cacheRef.current[input];
    }
    
    // Expensive computation
    const result = Array.from({ length: 1000000 })
      .reduce((acc) => acc + Math.random(), 0);
    
    cacheRef.current[input] = result;
    return result;
  };
  
  return <div>{computeExpensive('key')}</div>;
}

// Important: useRef doesn't cause re-render
function Counter2() {
  const renderCount = useRef(0);
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    renderCount.current++;
    console.log('Component rendered:', renderCount.current, 'times');
  });
  
  // Changing renderCount.current won't trigger re-render
  return (
    <div>
      <p>Count: {count}</p>
      <p>Renders: {renderCount.current}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

**Use Cases:**
- Managing focus, text selection, media playback
- Storing timeout/interval IDs
- Triggering animations
- Integrating with third-party DOM libraries

---

#### Q51E: useEffect cleanup, dependencies, and common pitfalls.

**Answer:**

```javascript
// Basic useEffect with cleanup
function Component() {
  const [isActive, setIsActive] = useState(true);
  
  useEffect(() => {
    console.log('Effect running');
    
    // Cleanup function runs before effect runs again or component unmounts
    return () => {
      console.log('Cleanup running');
    };
  }, []); // Runs once on mount, cleanup on unmount
  
  return <div>{isActive ? 'Active' : 'Inactive'}</div>;
}

// Event listener cleanup
function MouseTracker() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  useEffect(() => {
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };
    
    window.addEventListener('mousemove', handleMouseMove);
    
    // Cleanup: remove listener
    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
    };
  }, []); // Empty deps = run once
  
  return <div>Mouse: {position.x}, {position.y}</div>;
}

// Subscription cleanup
function UserStatus({ userId }) {
  const [status, setStatus] = useState('offline');
  
  useEffect(() => {
    const unsubscribe = subscribeToUserStatus(userId, (newStatus) => {
      setStatus(newStatus);
    });
    
    // Cleanup: unsubscribe when component unmounts or userId changes
    return () => {
      unsubscribe();
    };
  }, [userId]); // Re-subscribe when userId changes
  
  return <div>Status: {status}</div>;
}

// Dependency array pitfalls
function PitfallDemo() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  
  // ❌ No dependencies - runs every render
  useEffect(() => {
    console.log('Runs every render');
  });
  
  // ✅ Empty array - runs once on mount
  useEffect(() => {
    console.log('Runs once on mount');
  }, []);
  
  // ✅ With dependencies - runs when count changes
  useEffect(() => {
    console.log('Count changed to:', count);
  }, [count]);
  
  // ✅ Multiple dependencies
  useEffect(() => {
    console.log('Count or name changed');
  }, [count, name]);
  
  // ❌ Missing dependencies - stale closure bug
  useEffect(() => {
    const timer = setInterval(() => {
      console.log('Count is:', count); // Always sees initial count!
    }, 1000);
    
    return () => clearInterval(timer);
  }, []); // BUG: count should be in dependencies
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// Correct version
function CorrectDemo() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const timer = setInterval(() => {
      console.log('Count is:', count); // Current count
    }, 1000);
    
    return () => clearInterval(timer);
  }, [count]); // Include count in dependencies
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// Multiple effects - separation of concerns
function ComplexComponent() {
  const [userData, setUserData] = useState(null);
  const [posts, setPosts] = useState([]);
  
  // Effect 1: Fetch user
  useEffect(() => {
    fetchUser().then(setUserData);
  }, []);
  
  // Effect 2: Fetch posts based on user
  useEffect(() => {
    if (userData) {
      fetchUserPosts(userData.id).then(setPosts);
    }
  }, [userData]);
  
  return <div>{userData?.name}: {posts.length} posts</div>;
}

// useLayoutEffect - runs before paint
function AnimationComponent() {
  const ref = useRef(null);
  
  // Runs after render but before browser paints
  useLayoutEffect(() => {
    const element = ref.current;
    // Update DOM synchronously to prevent flashing
    element.style.opacity = '0';
    
    setTimeout(() => {
      element.style.opacity = '1';
    }, 0);
  }, []);
  
  return <div ref={ref}>Animated content</div>;
}
```

**Key Points:**
- Cleanup runs before next effect or unmount
- Dependencies determine when effect runs
- Missing dependencies cause stale closures
- useLayoutEffect runs before paint, useEffect after

---

### Section 4A: Redux & State Management - EXPANDED (20+ Questions)

#### Q66: What are Redux actions, reducers, and the store? Show a complete example.

**Answer:**

```javascript
import { createStore, combineReducers } from 'redux';

// 1. Actions - describe what happened
const INCREMENT = 'INCREMENT';
const DECREMENT = 'DECREMENT';
const SET_USER = 'SET_USER';

function incrementAction(amount = 1) {
  return { type: INCREMENT, payload: amount };
}

function decrementAction(amount = 1) {
  return { type: DECREMENT, payload: amount };
}

function setUserAction(user) {
  return { type: SET_USER, payload: user };
}

// 2. Reducers - pure functions that update state
function counterReducer(state = 0, action) {
  switch (action.type) {
    case INCREMENT:
      return state + action.payload;
    case DECREMENT:
      return state - action.payload;
    default:
      return state;
  }
}

function userReducer(state = null, action) {
  switch (action.type) {
    case SET_USER:
      return action.payload;
    default:
      return state;
  }
}

// 3. Combine reducers for multiple state slices
const rootReducer = combineReducers({
  count: counterReducer,
  user: userReducer,
});

// 4. Create store
const store = createStore(rootReducer);

// 5. Subscribe to changes
store.subscribe(() => {
  console.log('State updated:', store.getState());
});

// 6. Dispatch actions
store.dispatch(incrementAction(5)); // count: 5
store.dispatch(incrementAction(3)); // count: 8
store.dispatch(setUserAction({ name: 'Alice' }));

console.log(store.getState());
// { count: 8, user: { name: 'Alice' } }
```

---

#### Q67: What is Redux middleware? Show examples like redux-thunk and custom middleware.

**Answer:**

```javascript
import { createStore, applyMiddleware } from 'redux';

// Custom logging middleware
function loggerMiddleware(store) {
  return (next) => (action) => {
    console.log('Dispatching:', action);
    const result = next(action);
    console.log('New state:', store.getState());
    return result;
  };
}

// Custom error handling middleware
function errorMiddleware(store) {
  return (next) => (action) => {
    try {
      return next(action);
    } catch (err) {
      console.error('Error:', err);
      store.dispatch({ type: 'ERROR', payload: err.message });
    }
  };
}

// Redux-thunk (for async actions)
function thunkMiddleware(store) {
  return (next) => (action) => {
    if (typeof action === 'function') {
      return action(store.dispatch, store.getState);
    }
    return next(action);
  };
}

// Apply middleware
const store = createStore(
  rootReducer,
  applyMiddleware(loggerMiddleware, errorMiddleware, thunkMiddleware)
);

// Using thunk for async operations
function fetchUser(userId) {
  return async (dispatch, getState) => {
    dispatch({ type: 'FETCH_USER_START' });

    try {
      const response = await fetch(`/api/users/${userId}`);
      const data = await response.json();
      dispatch({ type: 'FETCH_USER_SUCCESS', payload: data });
    } catch (err) {
      dispatch({ type: 'FETCH_USER_ERROR', payload: err.message });
    }
  };
}

// Dispatch async action
store.dispatch(fetchUser(1));
```

---

#### Q68: Explain Redux Toolkit and how it simplifies Redux development.

**Answer:**

```javascript
import { createSlice, configureStore, createAsyncThunk } from '@reduxjs/toolkit';

// Async thunk - simplified async action handling
export const fetchUser = createAsyncThunk(
  'user/fetchUser',
  async (userId) => {
    const response = await fetch(`/api/users/${userId}`);
    return await response.json();
  }
);

// Create slice - combines reducer + actions
const userSlice = createSlice({
  name: 'user',
  initialState: {
    data: null,
    loading: false,
    error: null,
  },
  reducers: {
    setUser: (state, action) => {
      state.data = action.payload;
    },
    clearUser: (state) => {
      state.data = null;
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchUser.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchUser.fulfilled, (state, action) => {
        state.loading = false;
        state.data = action.payload;
      })
      .addCase(fetchUser.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message;
      });
  },
});

// Extract actions and reducer
export const { setUser, clearUser } = userSlice.actions;

// Create store
const store = configureStore({
  reducer: {
    user: userSlice.reducer,
  },
});

// In React component
import { useSelector, useDispatch } from 'react-redux';

function UserProfile() {
  const dispatch = useDispatch();
  const { data: user, loading, error } = useSelector(state => state.user);

  useEffect(() => {
    dispatch(fetchUser(1));
  }, [dispatch]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return <div>{user?.name}</div>;
}

// Advantages of Redux Toolkit:
// 1. createSlice - combines reducers + actions
// 2. createAsyncThunk - handles async logic
// 3. Immer integration - write "mutating" code
// 4. configureStore - simplified store setup
// 5. Built-in DevTools integration
```

---

#### Q69: What are Redux selectors? Show how to create and use them.

**Answer:**

```javascript
import { createSelector } from '@reduxjs/toolkit';

// Basic selectors - simple state extraction
export const selectUser = (state) => state.user;
export const selectUserName = (state) => state.user?.name;
export const selectUserAge = (state) => state.user?.age;

// Memoized selectors with createSelector
export const selectUserInfo = createSelector(
  [selectUser],
  (user) => ({
    name: user?.name || 'Unknown',
    age: user?.age || 0,
    adult: (user?.age || 0) >= 18,
  })
);

// Selectors with parameters
export const selectUserById = createSelector(
  [(state) => state.users, (_, userId) => userId],
  (users, userId) => users.find(u => u.id === userId)
);

// Complex selector with multiple inputs
export const selectFilteredUsers = createSelector(
  [(state) => state.users, (state) => state.filter],
  (users, filter) => {
    return users.filter(u =>
      u.name.toLowerCase().includes(filter.toLowerCase())
    );
  }
);

// In component
function UserList() {
  const dispatch = useDispatch();
  
  // These selectors memoize - only re-compute if inputs change
  const userInfo = useSelector(selectUserInfo);
  const filteredUsers = useSelector(selectFilteredUsers);
  const userById = useSelector(state => selectUserById(state, 1));

  return (
    <div>
      <p>User: {userInfo.name}, Adult: {userInfo.adult}</p>
      <ul>
        {filteredUsers.map(u => (
          <li key={u.id}>{u.name}</li>
        ))}
      </ul>
    </div>
  );
}

// Benefits of selectors:
// 1. Memoization prevents unnecessary re-renders
// 2. Reusable across components
// 3. Easier to refactor state structure
// 4. Encapsulation of state shape knowledge
```

---

#### Q70: Compare Redux, MobX, and Zustand. When would you use each?

**Answer:**

```javascript
// Redux - predictable, explicit
import { createStore } from 'redux';

const reducer = (state = 0, action) => {
  if (action.type === 'INCREMENT') return state + 1;
  return state;
};

const store = createStore(reducer);

// MobX - reactive, implicit
import { makeAutoObservable } from 'mobx';

class CounterStore {
  count = 0;

  constructor() {
    makeAutoObservable(this); // Auto reactions
  }

  increment() {
    this.count++; // Directly mutate
  }
}

const counterStore = new CounterStore();

// Zustand - minimal, flexible
import create from 'zustand';

const useCounterStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}));

// Usage
function App() {
  const count = useCounterStore((state) => state.count);
  const increment = useCounterStore((state) => state.increment);

  return (
    <button onClick={increment}>
      Count: {count}
    </button>
  );
}

// Comparison:
// Redux:
// - Pros: Predictable, DevTools, large ecosystem
// - Cons: Boilerplate, steep learning curve
// - Use: Large apps, team projects, time-travel debugging needed

// MobX:
// - Pros: Less boilerplate, intuitive
// - Cons: Less predictable, harder to debug
// - Use: Medium apps, familiar with OOP

// Zustand:
// - Pros: Minimal, no provider needed, lightweight
// - Cons: Smaller ecosystem, fewer DevTools
// - Use: Small to medium apps, simple state
```

---

#### Q71: What is normalization in Redux? Show how to structure normalized state.

**Answer:**

```javascript
// Denormalized state (problematic)
{
  posts: [
    {
      id: 1,
      title: 'Post 1',
      author: { id: 1, name: 'Alice' },
      comments: [
        { id: 1, text: 'Comment 1', author: { id: 2, name: 'Bob' } },
      ],
    },
  ],
}

// Problems:
// - Duplicated author data
// - Difficult to update nested data
// - Complex reducers for updates

// Normalized state (better)
{
  entities: {
    posts: {
      byId: {
        1: { id: 1, title: 'Post 1', authorId: 1, commentIds: [1] },
      },
      allIds: [1],
    },
    authors: {
      byId: {
        1: { id: 1, name: 'Alice' },
        2: { id: 2, name: 'Bob' },
      },
      allIds: [1, 2],
    },
    comments: {
      byId: {
        1: { id: 1, text: 'Comment 1', authorId: 2 },
      },
      allIds: [1],
    },
  },
}

// Reducer for normalized state
const initialState = {
  entities: {
    posts: { byId: {}, allIds: [] },
    authors: { byId: {}, allIds: [] },
  },
};

function entitiesReducer(state = initialState, action) {
  switch (action.type) {
    case 'ADD_POST':
      const postId = action.payload.id;
      return {
        entities: {
          ...state.entities,
          posts: {
            byId: {
              ...state.entities.posts.byId,
              [postId]: action.payload,
            },
            allIds: [...state.entities.posts.allIds, postId],
          },
        },
      };

    case 'UPDATE_POST':
      return {
        entities: {
          ...state.entities,
          posts: {
            byId: {
              ...state.entities.posts.byId,
              [action.payload.id]: action.payload,
            },
            allIds: state.entities.posts.allIds,
          },
        },
      };

    default:
      return state;
  }
}

// Selectors for normalized state
export const selectPostById = (state, postId) => {
  const post = state.entities.posts.byId[postId];
  if (!post) return null;

  const author = state.entities.authors.byId[post.authorId];
  const comments = post.commentIds.map(
    (cId) => state.entities.comments.byId[cId]
  );

  return { ...post, author, comments };
};

export const selectAllPosts = (state) => {
  return state.entities.posts.allIds.map((id) =>
    selectPostById(state, id)
  );
};

// Benefits:
// 1. Single source of truth - no duplication
// 2. Easy updates - modify in one place
// 3. Efficient queries - O(1) lookups
// 4. Better performance - selector memoization
```

---

#### Q72: What is the Redux middleware ecosystem? Show examples of popular middlewares.

**Answer:**

```javascript
import { configureStore } from '@reduxjs/toolkit';
import { createLogger } from 'redux-logger';
import thunk from 'redux-thunk';
import saga from 'redux-saga';

// 1. redux-thunk - for async actions
// (already covered in Q67)

// 2. redux-logger - automatic logging middleware
const logger = createLogger({
  collapsed: true,
  duration: true,
});

// 3. redux-saga - complex async workflows
import { call, put, takeEvery } from 'redux-saga/effects';

function* fetchUserSaga(action) {
  try {
    const user = yield call(fetch, `/api/users/${action.payload}`);
    yield put({ type: 'FETCH_USER_SUCCESS', payload: user });
  } catch (err) {
    yield put({ type: 'FETCH_USER_ERROR', payload: err });
  }
}

function* rootSaga() {
  yield takeEvery('FETCH_USER_REQUEST', fetchUserSaga);
}

// 4. Custom middleware example - analytics
const analyticsMiddleware = (store) => (next) => (action) => {
  // Send to analytics
  trackEvent(action.type, action.payload);
  return next(action);
};

// Configure store with multiple middlewares
const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware()
      .concat(logger, thunk, analyticsMiddleware)
      .concat(saga),
});

// Comparison of async handling:
// redux-thunk: Good for simple async, like fetch
// redux-saga: Better for complex flows, cancelation, batching
// Async thunks (RTK): Modern, balanced approach
```

---

#### Q73: How do you test Redux code? Show unit and integration tests.

**Answer:**

```javascript
import { configureStore } from '@reduxjs/toolkit';
import userReducer, { setUser, fetchUser } from './userSlice';
import { renderHook, act } from '@testing-library/react-hooks';
import { useSelector, useDispatch } from 'react-redux';

// Unit test - reducer
describe('userReducer', () => {
  it('should handle setUser', () => {
    const action = setUser({ name: 'Alice' });
    const newState = userReducer(undefined, action);
    expect(newState.data.name).toBe('Alice');
  });

  it('should handle fetchUser.pending', () => {
    const state = { data: null, loading: false };
    const action = { type: fetchUser.pending.type };
    const newState = userReducer(state, action);
    expect(newState.loading).toBe(true);
  });
});

// Unit test - selector
describe('selectors', () => {
  it('should select user info', () => {
    const state = {
      user: { data: { name: 'Alice', age: 28 } },
    };
    const result = selectUserInfo(state);
    expect(result.adult).toBe(true);
  });
});

// Unit test - async thunk
describe('fetchUser', () => {
  it('should dispatch actions on success', async () => {
    global.fetch = jest.fn(() =>
      Promise.resolve({
        json: () => Promise.resolve({ name: 'Alice' }),
      })
    );

    const dispatch = jest.fn();
    const getState = () => ({});

    await fetchUser(1)(dispatch, getState);

    expect(dispatch).toHaveBeenCalledWith(
      expect.objectContaining({ type: 'user/fetchUser/pending' })
    );
    expect(dispatch).toHaveBeenCalledWith(
      expect.objectContaining({ type: 'user/fetchUser/fulfilled' })
    );
  });
});

// Integration test - component with Redux
import { Provider } from 'react-redux';
import { render, screen } from '@testing-library/react';

describe('UserProfile component', () => {
  it('should display user from Redux', () => {
    const store = configureStore({
      reducer: { user: userReducer },
      preloadedState: {
        user: { data: { name: 'Alice' }, loading: false },
      },
    });

    render(
      <Provider store={store}>
        <UserProfile />
      </Provider>
    );

  });
});
```

---

#### Q74: How do you handle side effects with Redux? Middleware vs Thunks vs Saga.

**Answer:**

```javascript
// 1. Redux Thunk - simplest approach
const fetchUser = (userId) => async (dispatch) => {
  dispatch({ type: 'FETCH_USER_START' });
  try {
    const response = await fetch(`/api/users/${userId}`);
    const data = await response.json();
    dispatch({ type: 'FETCH_USER_SUCCESS', payload: data });
  } catch (err) {
    dispatch({ type: 'FETCH_USER_ERROR', payload: err.message });
  }
};

// 2. Custom Middleware - for cross-cutting concerns
const logger = (store) => (next) => (action) => {
  console.log('Dispatching:', action);
  const result = next(action);
  console.log('New state:', store.getState());
  return result;
};

// 3. Redux Saga - for complex async workflows
import { put, call, takeEvery, fork, join } from 'redux-saga/effects';

function* fetchUserSaga(action) {
  try {
    const user = yield call(fetch, `/api/users/${action.payload}`);
    yield put({ type: 'FETCH_USER_SUCCESS', payload: user });
  } catch (err) {
    yield put({ type: 'FETCH_USER_ERROR', payload: err });
  }
}

function* rootSaga() {
  yield takeEvery('FETCH_USER_REQUEST', fetchUserSaga);
}

// Comparison:
// Thunk: Best for simple async, direct dispatch control
// Middleware: Best for logging, analytics, monitoring
// Saga: Best for complex flows, cancellation, concurrency
```

---

#### Q75: What is immutability in Redux? How do you handle it with Immer?

**Answer:**

```javascript
// Without Immer - manual immutability
function counterReducer(state = 0, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      return state;
  }
}

// Complex nested state without Immer
function todoReducer(state = { todos: [] }, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [...state.todos, action.payload],
      };
    case 'UPDATE_TODO':
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload.id
            ? { ...todo, ...action.payload }
            : todo
        ),
      };
    case 'DELETE_TODO':
      return {
        ...state,
        todos: state.todos.filter(t => t.id !== action.payload),
      };
    default:
      return state;
  }
}

// With Immer - write mutating code
import produce from 'immer';

const todoReducer = produce((draft, action) => {
  switch (action.type) {
    case 'ADD_TODO':
      draft.todos.push(action.payload);
      break;
    case 'UPDATE_TODO':
      const todo = draft.todos.find(t => t.id === action.payload.id);
      if (todo) Object.assign(todo, action.payload);
      break;
    case 'DELETE_TODO':
      draft.todos = draft.todos.filter(t => t.id !== action.payload);
      break;
  }
});

// Redux Toolkit uses Immer by default
import { createSlice } from '@reduxjs/toolkit';

const todoSlice = createSlice({
  name: 'todos',
  initialState: { items: [] },
  reducers: {
    addTodo: (state, action) => {
      state.items.push(action.payload); // Looks mutating but isn't
    },
    updateTodo: (state, action) => {
      const todo = state.items.find(t => t.id === action.payload.id);
      if (todo) Object.assign(todo, action.payload);
    },
  },
});
```

---

#### Q76: What is the difference between action creators and thunks?

**Answer:**

```javascript
// Action creator - returns plain action object
const setUser = (user) => ({
  type: 'SET_USER',
  payload: user,
});

// Thunk - returns function that receives dispatch
const fetchUser = (userId) => async (dispatch) => {
  dispatch({ type: 'FETCH_USER_START' });
  try {
    const response = await fetch(`/api/users/${userId}`);
    const data = await response.json();
    dispatch({ type: 'FETCH_USER_SUCCESS', payload: data });
  } catch (err) {
    dispatch({ type: 'FETCH_USER_ERROR', payload: err.message });
  }
};

// Usage
store.dispatch(setUser({ name: 'Alice' })); // Direct action
store.dispatch(fetchUser(1)); // Thunk - middleware handles

// Redux Toolkit createAsyncThunk
import { createAsyncThunk } from '@reduxjs/toolkit';

export const fetchUserData = createAsyncThunk(
  'user/fetchUserData',
  async (userId, { rejectWithValue }) => {
    try {
      const response = await fetch(`/api/users/${userId}`);
      if (!response.ok) throw new Error('Failed');
      return await response.json();
    } catch (err) {
      return rejectWithValue(err.message);
    }
  }
);

// Thunk lifecycle actions
fetchUserData.pending
fetchUserData.fulfilled
fetchUserData.rejected
```

---

#### Q77: How do you debug Redux applications?

**Answer:**

```javascript
// 1. Redux DevTools
import { configureStore } from '@reduxjs/toolkit';

const store = configureStore({
  reducer: rootReducer,
  // DevTools automatically enabled in dev mode
});

// 2. Manual logging middleware
const logger = (store) => (next) => (action) => {
  console.group(action.type);
  console.info('Dispatching:', action);
  const result = next(action);
  console.log('Next State:', store.getState());
  console.groupEnd();
  return result;
};

// 3. Time-travel debugging setup
const devTools = window.__REDUX_DEVTOOLS_EXTENSION__;

const store = createStore(
  rootReducer,
  devTools && devTools()
);

// 4. Logging selectors
const selectUserWithLogging = (state) => {
  const user = state.user;
  console.log('selectUserWithLogging called with user:', user);
  return user;
};

// 5. Redux DevTools subscription
store.subscribe(() => {
  console.log('State changed:', store.getState());
});

// 6. Action history
const actions = [];
const actionLogger = (store) => (next) => (action) => {
  actions.push({ action, timestamp: Date.now(), state: store.getState() });
  return next(action);
};

// Export and inspect
window.__REDUX_ACTIONS__ = actions;
```

---

#### Q78: What is Redux state shape? How do you design it?

**Answer:**

```javascript
// Bad state shape - deeply nested, inconsistent structure
const badState = {
  user: {
    profile: {
      info: {
        personal: {
          name: 'Alice',
          email: 'alice@example.com',
        },
      },
    },
  },
  posts: [
    {
      id: 1,
      title: 'Post',
      author: {
        id: 1,
        name: 'Alice',
        email: 'alice@example.com',
      },
    },
  ],
};

// Good state shape - flat, normalized, consistent
const goodState = {
  entities: {
    users: {
      byId: {
        1: { id: 1, name: 'Alice', email: 'alice@example.com' },
      },
      allIds: [1],
    },
    posts: {
      byId: {
        1: { id: 1, title: 'Post', authorId: 1 },
      },
      allIds: [1],
    },
  },
  ui: {
    loading: false,
    error: null,
    selectedUserId: 1,
  },
};

// Design principles:
// 1. Normalize data - no duplication
// 2. Separate UI state from domain state
// 3. Keep selectors close to data
// 4. Flat structure - avoid deep nesting
// 5. Use IDs instead of objects for references

// Example selector composition
const selectUserById = (state, userId) => state.entities.users.byId[userId];
const selectPostsByUserId = (state, userId) =>
  state.entities.posts.allIds
    .map(id => state.entities.posts.byId[id])
    .filter(post => post.authorId === userId);

const selectUserWithPosts = (state, userId) => ({
  user: selectUserById(state, userId),
  posts: selectPostsByUserId(state, userId),
});
```

---

#### Q79: How do you handle form state in Redux vs local state?

**Answer:**

```javascript
// Option 1: Redux (for shared state)
const formSlice = createSlice({
  name: 'form',
  initialState: {
    values: { name: '', email: '' },
    errors: {},
    touched: {},
    isSubmitting: false,
  },
  reducers: {
    setFieldValue: (state, action) => {
      state.values[action.payload.field] = action.payload.value;
    },
    setFieldTouched: (state, action) => {
      state.touched[action.payload.field] = true;
    },
    setErrors: (state, action) => {
      state.errors = action.payload;
    },
    resetForm: (state) => {
      state.values = { name: '', email: '' };
      state.errors = {};
      state.touched = {};
    },
  },
});

// Option 2: Local state (for isolated forms)
function Form() {
  const [form, setForm] = useState({ name: '', email: '' });
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm(prev => ({ ...prev, [name]: value }));
  };

  return (
    <form>
      <input name="name" value={form.name} onChange={handleChange} />
    </form>
  );
}

// Option 3: Custom hook (balanced)
function useForm(initialValues, onSubmit) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    setValues(prev => ({
      ...prev,
      [e.target.name]: e.target.value,
    }));
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    const newErrors = validate(values);
    if (Object.keys(newErrors).length === 0) {
      await onSubmit(values);
    } else {
      setErrors(newErrors);
    }
  };

  return { values, errors, handleChange, handleSubmit };
}

// When to use Redux for forms:
// - Multi-step forms
// - Shared across components
// - Undo/redo needed
// - Auto-save functionality

// When to use local state:
// - Single component forms
// - Simple validation
// - No cross-component sharing
```

---

#### Q80: What is the Redux payment processing flow pattern?

**Answer:**

```javascript
// Complete Redux flow for payment processing
const paymentSlice = createSlice({
  name: 'payment',
  initialState: {
    status: 'idle', // 'idle' | 'processing' | 'success' | 'error'
    amount: 0,
    transactionId: null,
    error: null,
  },
  reducers: {
    paymentStarted: (state, action) => {
      state.status = 'processing';
      state.amount = action.payload;
    },
    paymentSucceeded: (state, action) => {
      state.status = 'success';
      state.transactionId = action.payload;
    },
    paymentFailed: (state, action) => {
      state.status = 'error';
      state.error = action.payload;
    },
    resetPayment: (state) => {
      state.status = 'idle';
      state.transactionId = null;
      state.error = null;
    },
  },
});

// Thunk for payment processing
export const processPayment = createAsyncThunk(
  'payment/processPayment',
  async (paymentData, { rejectWithValue }) => {
    try {
      const response = await fetch('/api/payment', {
        method: 'POST',
        body: JSON.stringify(paymentData),
      });
      if (!response.ok) throw new Error('Payment failed');
      const { transactionId } = await response.json();
      return transactionId;
    } catch (err) {
      return rejectWithValue(err.message);
    }
  }
);

// Handle async thunk outcomes
paymentSlice.extraReducers = (builder) => {
  builder
    .addCase(processPayment.pending, (state) => {
      state.status = 'processing';
    })
    .addCase(processPayment.fulfilled, (state, action) => {
      state.status = 'success';
      state.transactionId = action.payload;
    })
    .addCase(processPayment.rejected, (state, action) => {
      state.status = 'error';
      state.error = action.payload;
    });
};

// In component
function Payment() {
  const dispatch = useDispatch();
  const { status, error } = useSelector(state => state.payment);

  const handlePay = async () => {
    const result = await dispatch(processPayment({
      amount: 99.99,
      cardToken: 'tok_123',
    }));

    if (processPayment.fulfilled.match(result)) {
      // Success - show confirmation
      showSuccess(`Payment confirmed: ${result.payload}`);
    } else if (processPayment.rejected.match(result)) {
      // Error - show error
      showError(result.payload);
    }
  };

  return (
    <div>
      {status === 'processing' && <Spinner />}
      {status === 'success' && <Success />}
      {status === 'error' && <Error message={error} />}
      <button onClick={handlePay} disabled={status === 'processing'}>
        Pay Now
      </button>
    </div>
  );
}
```

---

#### Q81: How do you manage paginated data in Redux?

**Answer:**

```javascript
const paginatedSlice = createSlice({
  name: 'paginated',
  initialState: {
    items: [],
    currentPage: 1,
    pageSize: 10,
    total: 0,
    loading: false,
  },
  reducers: {
    goToPage: (state, action) => {
      state.currentPage = action.payload;
    },
    setPageSize: (state, action) => {
      state.pageSize = action.payload;
      state.currentPage = 1; // Reset to first page
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchPaginatedData.pending, (state) => {
        state.loading = true;
      })
      .addCase(fetchPaginatedData.fulfilled, (state, action) => {
        state.loading = false;
        state.items = action.payload.items;
        state.total = action.payload.total;
      });
  },
});

export const fetchPaginatedData = createAsyncThunk(
  'paginated/fetchData',
  async ({ page, pageSize }, { rejectWithValue }) => {
    try {
      const response = await fetch(
        `/api/items?page=${page}&limit=${pageSize}`
      );
      const data = await response.json();
      return data;
    } catch (err) {
      return rejectWithValue(err.message);
    }
  }
);

// Selector with pagination logic
const selectPaginatedItems = createSelector(
  [(state) => state.paginated],
  (paginated) => ({
    items: paginated.items,
    currentPage: paginated.currentPage,
    total: paginated.total,
    pageCount: Math.ceil(paginated.total / paginated.pageSize),
    hasNextPage: paginated.currentPage < Math.ceil(paginated.total / paginated.pageSize),
    hasPrevPage: paginated.currentPage > 1,
  })
);

// Usage in component
function PaginatedList() {
  const dispatch = useDispatch();
  const pagination = useSelector(selectPaginatedItems);

  useEffect(() => {
    dispatch(fetchPaginatedData({
      page: pagination.currentPage,
      pageSize: pagination.pageSize,
    }));
  }, [pagination.currentPage]);

  return (
    <>
      <ItemList items={pagination.items} />
      <Pagination
        current={pagination.currentPage}
        total={pagination.pageCount}
        onPageChange={(page) => dispatch(goToPage(page))}
      />
    </>
  );
}
```

---

#### Q82: What is Redux middleware composition? How do you create custom middleware?

**Answer:**

```javascript
// Middleware signature: store => next => action => result

// 1. Simple logging middleware
const loggerMiddleware = (store) => (next) => (action) => {
  console.log('Previous state:', store.getState());
  console.log('Dispatching:', action);
  const result = next(action);
  console.log('Next state:', store.getState());
  return result;
};

// 2. Error handling middleware
const errorMiddleware = (store) => (next) => (action) => {
  try {
    return next(action);
  } catch (err) {
    console.error('Error in reducer:', err);
    store.dispatch({
      type: 'ERROR_OCCURRED',
      payload: err.message,
    });
    throw err;
  }
};

// 3. Performance monitoring middleware
const performanceMiddleware = (store) => (next) => (action) => {
  const start = performance.now();
  const result = next(action);
  const duration = performance.now() - start;

  if (duration > 1000) {
    console.warn(`Slow action: ${action.type} took ${duration}ms`);
  }

  return result;
};

// 4. Action transformer middleware
const transformerMiddleware = (store) => (next) => (action) => {
  if (action.type === 'FETCH_DATA') {
    return next({
      ...action,
      meta: {
        ...action.meta,
        timestamp: Date.now(),
        userId: getUserId(),
      },
    });
  }
  return next(action);
};

// Compose multiple middlewares
const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware()
      .concat(loggerMiddleware, errorMiddleware, performanceMiddleware),
});

// Custom middleware with cleanup
const subscriptionMiddleware = (store) => {
  const subscriptions = {};

  return (next) => (action) => {
    if (action.type === 'SUBSCRIBE') {
      subscriptions[action.payload.id] = action.payload.callback;
    } else if (action.type === 'UNSUBSCRIBE') {
      delete subscriptions[action.payload.id];
    }

    const result = next(action);

    // Notify subscribers
    Object.values(subscriptions).forEach(cb => cb(store.getState()));

    return result;
  };
};
```

---

#### Q83: How do you handle complex Redux state updates with reselect or other selector libraries?

**Answer:**

```javascript
import { createSelector } from 'reselect';

// State shape
const initialState = {
  users: {
    byId: {
      1: { id: 1, name: 'John', departmentId: 10 },
      2: { id: 2, name: 'Jane', departmentId: 20 },
    },
    allIds: [1, 2],
  },
  departments: {
    byId: {
      10: { id: 10, name: 'Engineering' },
      20: { id: 20, name: 'Design' },
    },
    allIds: [10, 20],
  },
  filters: {
    selectedDepartmentId: 10,
    searchTerm: '',
  },
};

// Basic selectors
const selectUsers = (state) => state.users;
const selectDepartments = (state) => state.departments;
const selectFilters = (state) => state.filters;

// Memoized selectors with reselect
const selectUserIds = createSelector(
  [selectUsers],
  (users) => users.allIds
);

const selectUserList = createSelector(
  [selectUsers],
  (users) => users.allIds.map(id => users.byId[id])
);

// Complex derived selector
const selectUsersByDepartment = createSelector(
  [selectUserList, selectFilters, selectDepartments],
  (users, filters, departments) => {
    return users
      .filter(user => user.departmentId === filters.selectedDepartmentId)
      .filter(user => 
        user.name.toLowerCase().includes(filters.searchTerm.toLowerCase())
      )
      .map(user => ({
        ...user,
        department: departments.byId[user.departmentId].name,
      }))
      .sort((a, b) => a.name.localeCompare(b.name));
  }
);

// Usage in component
function UserList() {
  const users = useSelector(selectUsersByDepartment);
  
  // Only re-renders if the filtered/mapped users actually change
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>
          {user.name} - {user.department}
        </li>
      ))}
    </ul>
  );
}

// Advanced: Parametrized selectors
const makeSelectUserById = () =>
  createSelector(
    [selectUsers, (_, userId) => userId],
    (users, userId) => users.byId[userId]
  );

// Usage
function UserDetail({ userId }) {
  const selectUserById = useMemo(
    () => makeSelectUserById(),
    []
  );
  const user = useSelector(state => selectUserById(state, userId));
  return <div>{user?.name}</div>;
}
```

**Key Benefits:**
- **Memoization**: Selectors only recompute when their inputs change
- **Performance**: Prevents unnecessary component re-renders
- **Reusability**: Selectors can be composed and combined
- **Testability**: Pure functions that are easy to test
- **Normalization**: Works best with normalized state structure

---

#### Q84: What are Redux DevTools and how do you integrate them? Show advanced debugging techniques.

**Answer:**

```javascript
import { configureStore } from '@reduxjs/toolkit';
import { composeWithDevTools } from 'redux-devtools-extension';

// 1. Basic Setup with Redux Toolkit (automatic)
const store = configureStore({
  reducer: rootReducer,
  // DevTools are automatically enabled in development
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        // Ignore certain action types or paths
        ignoredActions: ['ACTION_WITH_DATE'],
        ignoredPaths: ['payload.timestamp'],
      },
    }),
});

// 2. Custom DevTools Configuration
const composeEnhancers =
  typeof window === 'object' && window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
    ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({
        trace: true, // Enable action stack traces
        traceLimit: 25,
        features: {
          pause: true, // Enable pause/lock functionality
          lock: true,
          persist: true, // Persist state across page reloads
          export: true,
          import: 'custom',
          jump: true,
          skip: true,
          reorder: true,
          dispatch: true,
          test: true,
        },
      })
    : compose;

const store = createStore(
  rootReducer,
  composeEnhancers(applyMiddleware(...middlewares))
);

// 3. Time-traveling Debugging
// In DevTools: Use arrow buttons to step through actions
// Right-click on action to:
// - Dispatch it again
// - Jump to that point in time
// - Delete the action from history
// - Edit the state at that point

// 4. State Comparison
// DevTools allows comparing state before/after each action

// 5. Middleware Subscription Debugging
store.subscribe(() => {
  console.log('State changed:', store.getState());
});

// 6. Action Creator with Metadata
const fetchUser = (userId) => ({
  type: 'FETCH_USER',
  payload: userId,
  meta: {
    timestamp: Date.now(),
    source: 'UserDetail',
  },
});

// 7. Custom Action in DevTools
// You can dispatch custom actions directly from DevTools
// Format: { type: 'ACTION_TYPE', payload: {...} }

// 8. Middleware with DevTools Tracing
const apiMiddleware = (store) => (next) => (action) => {
  if (action.type.startsWith('API_')) {
    console.trace(`API action: ${action.type}`); // Shows stack trace
    const start = performance.now();
    const result = next(action);
    console.log(`${action.type} took ${performance.now() - start}ms`);
    return result;
  }
  return next(action);
};

// 9. Debugging Tips
const debugMiddleware = (store) => (next) => (action) => {
  console.group(`🔄 ${action.type}`);
  console.info('Action:', action);
  console.log('Previous State:', store.getState());
  
  const result = next(action);
  
  console.log('New State:', store.getState());
  console.groupEnd();
  
  return result;
};

// 10. Monitor Specific State Changes
const monitoringMiddleware = (store) => (next) => (action) => {
  const previousState = store.getState();
  const result = next(action);
  const nextState = store.getState();

  // Deep diff
  const changes = detectChanges(previousState, nextState);
  
  if (changes.length > 0) {
    console.warn('State changed paths:', changes);
  }

  return result;
};
```

**Advanced Features:**
- **Time Travel**: Step forward/backward through action history
- **Action Replay**: Replay specific actions
- **State Snapshots**: Compare state at any point
- **Stack Traces**: See exactly where actions are dispatched from
- **Persistence**: DevTools can save/load state
- **Performance**: See action execution times

---

#### Q85: How do you handle performance optimization in Redux? When do you use Redux vs local state?

**Answer:**

```javascript
// 1. Redux for Global, Shared State
// ✓ Use Redux when:
// - State is shared across many components
// - State changes need to be tracked/audited
// - Multiple sources can update the same state
// - You need time-travel debugging

const store = configureStore({
  reducer: {
    user: userReducer,
    notifications: notificationsReducer,
    theme: themeReducer,
  },
});

// 2. Local State for UI-Only State
// ✓ Use local state (useState) when:
// - State affects only one component or its children
// - State is temporary (form inputs, modals)
// - State doesn't need to persist
// - Frequent updates (e.g., animations)

function SearchComponent() {
  // Local state for search input - frequent updates
  const [searchTerm, setSearchTerm] = useState('');
  const [isDropdownOpen, setIsDropdownOpen] = useState(false);
  
  // Redux for persistent search results
  const searchResults = useSelector(selectSearchResults);
  const dispatch = useDispatch();
  
  const handleSearch = useCallback((term) => {
    setSearchTerm(term);
    if (term.length > 2) {
      dispatch(searchUsers(term)); // Goes to Redux
    }
  }, [dispatch]);
  
  return (
    <div>
      <input
        value={searchTerm}
        onChange={(e) => handleSearch(e.target.value)}
      />
      <button onClick={() => setIsDropdownOpen(!isDropdownOpen)}>
        Toggle
      </button>
      {isDropdownOpen && (
        <ul>
          {searchResults.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}

// 3. Selective Subscriptions (Prevent Unnecessary Re-renders)
function OptimizedComponent() {
  // Only subscribes to user data
  const userId = useSelector(selectCurrentUserId);
  
  // This only causes re-render if userId actually changes
  // Not if other parts of Redux state change
  
  return <div>User: {userId}</div>;
}

// 4. Batch Multiple Dispatches
import { batch } from 'react-redux';

function handleMultipleActions() {
  batch(() => {
    dispatch(action1());
    dispatch(action2());
    dispatch(action3());
  });
  // Component only re-renders once instead of 3 times
}

// 5. Use Immer in Reducers for Simpler Immutable Updates
import { createSlice } from '@reduxjs/toolkit';

const userSlice = createSlice({
  name: 'users',
  initialState: {
    byId: {},
    allIds: [],
  },
  reducers: {
    // Immer allows "mutating" but keeps it immutable
    addUser(state, action) {
      const user = action.payload;
      state.byId[user.id] = user;
      state.allIds.push(user.id);
      // No need for spread operator!
    },
    updateUser(state, action) {
      const { id, changes } = action.payload;
      Object.assign(state.byId[id], changes);
    },
  },
});

// 6. Normalized State Structure
const normalizedState = {
  users: {
    byId: {
      '1': { id: '1', name: 'John', departmentId: '10' },
      '2': { id: '2', name: 'Jane', departmentId: '10' },
    },
    allIds: ['1', '2'],
  },
  departments: {
    byId: {
      '10': { id: '10', name: 'Engineering', managerId: '1' },
    },
    allIds: ['10'],
  },
};
// ✓ Better than: [{ id: 1, name: 'John', department: { ... } }, ...]
// Why: Easier to update nested data, prevents data duplication

// 7. Memoized Selectors to Prevent Re-renders
const selectUsersByDepartment = createSelector(
  [selectUsers, selectCurrentDepartmentId],
  (users, deptId) => users.filter(u => u.deptId === deptId)
);

// 8. Code Splitting Redux State
// Split large stores into feature slices
const store = configureStore({
  reducer: {
    core: coreReducer,
    feature1: feature1Reducer,
    feature2: feature2Reducer,
  },
});

// 9. Lazy Load Reducers
function useReducerInjection(feature) {
  useEffect(() => {
    store.asyncReducers[feature] = require(`./features/${feature}`);
    store.replaceReducer(createRootReducer(store.asyncReducers));
  }, [feature]);
}

// 10. Performance Monitoring
const performanceMiddleware = (store) => (next) => (action) => {
  const start = performance.now();
  const result = next(action);
  const duration = performance.now() - start;
  
  if (duration > 5) {
    console.warn(`Slow reducer: ${action.type} took ${duration}ms`);
  }
  
  return result;
};
```

**Redux vs Local State Decision Tree:**
```
Is the state needed by multiple components? 
  → YES: Consider Redux
  → NO: Use local state

Does the state rarely change?
  → YES: Redux (easier to debug)
  → NO: Could be local state if isolated

Is it in a deeply nested component?
  → YES: Redux (avoid prop drilling)
  → NO: Local state might be fine

Do you need to persist it?
  → YES: Redux
  → NO: Could be local state
```

---

### Section 5A: Advanced React Patterns - EXPANDED (20+ Questions)

#### Q86: What is the Higher-Order Component (HOC) pattern? Show practical examples.

**Answer:**

```jsx
// HOC - wraps component with additional functionality
function withTheme(Component) {
  function ThemedComponent(props) {
    const [theme, setTheme] = useState('light');

    const toggleTheme = () => {
      setTheme(theme === 'light' ? 'dark' : 'light');
    };

    return (
      <Component
        {...props}
        theme={theme}
        toggleTheme={toggleTheme}
      />
    );
  }

  ThemedComponent.displayName = `WithTheme(${Component.displayName || Component.name})`;
  return ThemedComponent;
}

// Usage
const ThemedButton = withTheme(Button);
const ThemedForm = withTheme(Form);

// HOC for authentication
function withAuth(Component) {
  function AuthenticatedComponent(props) {
    const [isAuth, setIsAuth] = useState(false);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
      checkAuth().then(result => {
        setIsAuth(result);
        setLoading(false);
      });
    }, []);

    if (loading) return <div>Loading...</div>;
    if (!isAuth) return <Navigate to="/login" />;

    return <Component {...props} />;
  }

  return AuthenticatedComponent;
}

// HOC for logging props
function withLogger(Component) {
  return function LoggedComponent(props) {
    useEffect(() => {
      console.log(`${Component.name} mounted with props:`, props);

      return () => {
        console.log(`${Component.name} unmounted`);
      };
    }, [props]);

    return <Component {...props} />;
  };
}

// Composing HOCs
const SecuredLogger = withLogger(withAuth(Dashboard));

// Pros:
// - Share logic across components
// - Manipulation of props
// - Render hijacking

// Cons:
// - Wrapper hell (prop drilling)
// - Static methods not copied
// - Ref forwarding required
```

---

#### Q87: What is Memoization and how do you use React.memo, useMemo, and useCallback?

**Answer:**

```javascript
// React.memo - Memoize component
const MemoizedButton = React.memo(({ label, onClick }) => {
  console.log('Button rendered');
  return <button onClick={onClick}>{label}</button>;
});

// useMemo - Memoize expensive computations
function ExpensiveComponent({ items }) {
  const sortedItems = useMemo(() => {
    return items.sort((a, b) => a.value - b.value);
  }, [items]);
  
  return <div>{sortedItems.map(item => <div key={item.id}>{item.value}</div>)}</div>;
}

// useCallback - Memoize callbacks
function Parent() {
  const handleSelectItem = useCallback((id) => {
    console.log('Selected:', id);
  }, []);

  return <Child onSelect={handleSelectItem} />;
}
```

**When to use:**
- React.memo: Expensive render functions
- useMemo: Expensive calculations
- useCallback: Passing callbacks to memoized children

---

#### Q88: What are Portals in React and when would you use them?

**Answer:**

```javascript
import { createPortal } from 'react-dom';

// Modal using Portal
function Modal({ children, isOpen }) {
  if (!isOpen) return null;

  return createPortal(
    <div className="modal-overlay">
      <div className="modal-content">{children}</div>
    </div>,
    document.getElementById('modal-root')
  );
}

// Use cases:
// - Modals/Dialogs
// - Tooltips/Dropdowns
// - Escape CSS stacking context
```

**Benefits:**
- Escape CSS overflow issues
- Better event handling
- Cleaner DOM hierarchy

---

#### Q89: What is the virtual DOM and how does React reconciliation work?

**Answer:**

```javascript
// Key-based reconciliation
function List() {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}

// Reconciliation process:
// 1. Create new VDOM
// 2. Compare with old fiber
// 3. Mark effects (UPDATE, PLACEMENT, DELETION)
// 4. Commit changes to DOM

// Fiber architecture breaks work into units
const fiber = {
  type: 'div',
  props: {},
  parent: null,
  child: null,
  sibling: null,
  effectTag: 'UPDATE',
};

// React 18: Automatic batching
setTimeout(() => {
  setState(1);
  setState(2);
  // Both batched automatically
}, 0);
```

**Key Concepts:**
- Virtual DOM: In-memory representation
- Diffing: Algorithm for minimal changes
- Fiber: Scheduling unit
- Key: Essential for list stability

---

#### Q90: How do you handle refs in React? When should you use them?

**Answer:**

```javascript
// useRef hook
function TextInput() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus</button>
    </>
  );
}

// Forwarding refs
const FancyInput = React.forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});

// useImperativeHandle - expose methods
const Modal = React.forwardRef(({ onClose }, ref) => {
  useImperativeHandle(ref, () => ({
    open: () => setIsOpen(true),
    close: () => setIsOpen(false),
  }), []);

  return <dialog>{/* ... */}</dialog>;
});
```

**When to use refs:**
- ✓ Focus management
- ✓ Text selection
- ✓ Media playback
- ✗ Avoid for state management

---

#### Q91: What is Concurrent rendering and how does it improve UX?

**Answer:**

```javascript
import { useTransition, useDeferredValue } from 'react';

// useTransition - non-urgent updates
function SearchResults() {
  const [searchTerm, setSearchTerm] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleSearch = (e) => {
    const value = e.target.value;
    setSearchTerm(value); // Urgent

    startTransition(() => {
      fetchResults(value); // Can be interrupted
    });
  };

  return (
    <>
      <input value={searchTerm} onChange={handleSearch} />
      {isPending && <Spinner />}
      <Results />
    </>
  );
}

// useDeferredValue - defer updates
function DeferredSearch() {
  const [value, setValue] = useState('');
  const deferredValue = useDeferredValue(value);

  return (
    <>
      <input value={value} onChange={e => setValue(e.target.value)} />
      <SlowComponent query={deferredValue} />
    </>
  );
}
```

**Benefits:**
- UI remains responsive
- Better perceived performance
- Interruptible rendering

---

#### Q92: How do you handle accessibility (a11y) in React?

**Answer:**

```javascript
// Semantic HTML
<button onClick={handleClick}>Click me</button>

// ARIA attributes
<button
  role="switch"
  aria-checked={isOpen}
  aria-label="Toggle menu"
  onClick={onChange}
/>

// Form labels
<label htmlFor="name">Name:</label>
<input id="name" type="text" />

// Image alt text
<img src="logo.png" alt="Company Logo" />

// Keyboard navigation
const handleKeyDown = (e) => {
  if (e.key === 'Enter') {
    onSelect(items[focusedIndex]);
  }
};

// Announce changes
<div role="status" aria-live="polite">
  {message}
</div>

// Focus management
useEffect(() => {
  closeButtonRef.current?.focus();
}, []);
```

**WCAG Guidelines:**
- Perceivable: Visible content
- Operable: Keyboard navigation
- Understandable: Clear language
- Robust: Works with assistive tech

---

#### Q93: What are render props? Show when to use them vs hooks.

**Answer:**

```jsx
// Render props pattern - pass a function as prop
function DataProvider({ children }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchData().then(d => {
      setData(d);
      setLoading(false);
    });
  }, []);

  return children({ data, loading });
}

// Usage
<DataProvider>
  {({ data, loading }) => (
    <>
      {loading ? <div>Loading...</div> : <div>{data}</div>}
    </>
  )}
</DataProvider>

// Render prop component
class Mouse extends React.Component {
  state = { x: 0, y: 0 };

  componentDidMount() {
    window.addEventListener('mousemove', this.handleMouseMove);
  }

  componentWillUnmount() {
    window.removeEventListener('mousemove', this.handleMouseMove);
  }

  handleMouseMove = (e) => {
    this.setState({ x: e.clientX, y: e.clientY });
  };

  render() {
    return this.props.render(this.state);
  }
}

// Usage
<Mouse render={({ x, y }) => (
  <div>{x}, {y}</div>
)} />

// Modern approach - custom hooks (replaces render props)
function useMouse() {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener('mousemove', handleMouseMove);
    return () => window.removeEventListener('mousemove', handleMouseMove);
  }, []);

  return position;
}

// Usage is simpler with hooks
function Component() {
  const { x, y } = useMouse();
  return <div>{x}, {y}</div>;
}

// Hooks vs Render Props:
// Hooks are generally preferred because:
// 1. Simpler to read
// 2. Less nesting
// 3. Better performance
// 4. No wrapper hell
```

---

#### Q94: What is Suspense? How does it work with lazy loading?

**Answer:**

```jsx
import { Suspense, lazy } from 'react';

// Lazy load component
const HeavyChart = lazy(() => import('./HeavyChart'));
const DataTable = lazy(() => import('./DataTable'));

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>

      {/* Each suspense boundary shows its own fallback */}
      <Suspense fallback={<div>Loading chart...</div>}>
        <HeavyChart />
      </Suspense>

      <Suspense fallback={<div>Loading table...</div>}>
        <DataTable />
      </Suspense>
    </div>
  );
}

// Nested Suspense for granular loading
function App() {
  return (
    <Suspense fallback={<PageLoader />}>
      <Header />
      
      <Suspense fallback={<SidebarLoader />}>
        <Sidebar />
      </Suspense>

      <Suspense fallback={<ContentLoader />}>
        <MainContent />
      </Suspense>
    </Suspense>
  );
}

// Suspense with error boundary
function SuspenseWithError({ children }) {
  return (
    <ErrorBoundary fallback={<ErrorFallback />}>
      <Suspense fallback={<LoadingFallback />}>
        {children}
      </Suspense>
    </ErrorBoundary>
  );
}

// Concurrent rendering with Suspense
import { useTransition } from 'react';

function App() {
  const [tab, setTab] = useState('home');
  const [isPending, startTransition] = useTransition();

  const handleTabChange = (newTab) => {
    startTransition(() => {
      setTab(newTab);
    });
  };

  return (
    <div>
      <button onClick={() => handleTabChange('home')} disabled={isPending}>
        Home
      </button>
      <button onClick={() => handleTabChange('about')} disabled={isPending}>
        About
      </button>

      <Suspense fallback={<TabLoader />}>
        {tab === 'home' && <HomePage />}
        {tab === 'about' && <AboutPage />}
      </Suspense>
    </div>
  );
}
```

---

#### Q95: Explain the compound component pattern. Show a practical implementation.

**Answer:**

```jsx
// Compound component pattern - components that work together
const Dialog = ({ children }) => {
  const [open, setOpen] = useState(false);

  return (
    <>
      {React.Children.map(children, (child) =>
        React.cloneElement(child, { open, setOpen })
      )}
    </>
  );
};

Dialog.Trigger = ({ open, setOpen, children, ...props }) => (
  <button onClick={() => setOpen(!open)} {...props}>
    {children}
  </button>
);

Dialog.Content = ({ open, children }) => {
  if (!open) return null;
  return <div className="dialog">{children}</div>;
};

Dialog.Close = ({ setOpen, children }) => (
  <button onClick={() => setOpen(false)}>{children}</button>
);

// Usage
<Dialog>
  <Dialog.Trigger>Open Dialog</Dialog.Trigger>
  <Dialog.Content>
    <h2>Dialog Title</h2>
    <p>Dialog content</p>
    <Dialog.Close>Close</Dialog.Close>
  </Dialog.Content>
</Dialog>

// Better with Context
const DialogContext = createContext();

function Dialog({ children }) {
  const [open, setOpen] = useState(false);

  return (
    <DialogContext.Provider value={{ open, setOpen }}>
      {children}
    </DialogContext.Provider>
  );
}

Dialog.Trigger = function DialogTrigger({ children }) {
  const { open, setOpen } = useContext(DialogContext);
  return (
    <button onClick={() => setOpen(!open)}>
      {children}
    </button>
  );
};

Dialog.Content = function DialogContent({ children }) {
  const { open } = useContext(DialogContext);
  return open && <div className="dialog">{children}</div>;
};

Dialog.Close = function DialogClose({ children }) {
  const { setOpen } = useContext(DialogContext);
  return (
    <button onClick={() => setOpen(false)}>
      {children}
    </button>
  );
};

// Practical example: Form component
const Form = ({ children }) => {
  const [values, setValues] = useState({});
  const [errors, setErrors] = useState({});

  return (
    <FormContext.Provider value={{ values, setValues, errors, setErrors }}>
      <form>{children}</form>
    </FormContext.Provider>
  );
};

Form.Input = function FormInput({ name, label }) {
  const { values, setValues } = useContext(FormContext);
  return (
    <div>
      <label>{label}</label>
      <input
        value={values[name] || ''}
        onChange={(e) =>
          setValues(prev => ({ ...prev, [name]: e.target.value }))
        }
      />
    </div>
  );
};

Form.Submit = function FormSubmit({ onSubmit }) {
  const { values } = useContext(FormContext);
  return (
    <button onClick={() => onSubmit(values)}>
      Submit
    </button>
  );
};
```

---

#### Q96: What is the provider pattern and when to use it?

**Answer:**

```jsx
// Provider pattern - centralized state & logic
const DataContext = createContext();

function DataProvider({ children }) {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const fetchData = async () => {
    setLoading(true);
    try {
      const response = await fetch('/api/data');
      const result = await response.json();
      setData(result);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  const addItem = (item) => {
    setData(prev => [...prev, item]);
  };

  const removeItem = (id) => {
    setData(prev => prev.filter(item => item.id !== id));
  };

  const value = {
    data,
    loading,
    error,
    fetchData,
    addItem,
    removeItem,
  };

  return (
    <DataContext.Provider value={value}>
      {children}
    </DataContext.Provider>
  );
}

// Custom hook to use the provider
function useData() {
  const context = useContext(DataContext);
  if (!context) {
    throw new Error('useData must be used within DataProvider');
  }
  return context;
}

// Application setup
function App() {
  return (
    <DataProvider>
      <Router>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/details" element={<Details />} />
        </Routes>
      </Router>
    </DataProvider>
  );
}

// Use in components
function Home() {
  const { data, fetchData, loading } = useData();

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return (
    <div>
      {loading && <p>Loading...</p>}
      {data.map(item => (
        <Item key={item.id} item={item} />
      ))}
    </div>
  );
}

// Multiple providers pattern
function App() {
  return (
    <AuthProvider>
      <ThemeProvider>
        <NotificationProvider>
          <DataProvider>
            <Router>
              <Routes>
                {/* routes */}
              </Routes>
            </Router>
          </DataProvider>
        </NotificationProvider>
      </ThemeProvider>
    </AuthProvider>
  );
}

// Performance optimization - split contexts
const UserContext = createContext(); // Changes frequently
const PreferencesContext = createContext(); // Rarely changes

// This way, changing user doesn't trigger preferences re-render
```

---

#### Q97: What are render optimization techniques beyond React.memo?

**Answer:**

```jsx
// 1. Component splitting - smaller trees re-render
// ❌ Bad: Large component re-renders entire tree
function Dashboard() {
  const [count, setCount] = useState(0);
  return (
    <>
      <Header /> {/* Re-renders even though no change */}
      <Counter count={count} onClick={() => setCount(count + 1)} />
      <Footer /> {/* Re-renders even though no change */}
    </>
  );
}

// ✅ Good: Isolated state, smaller re-renders
function DashboardCounter() {
  const [count, setCount] = useState(0);
  return <Counter count={count} onClick={() => setCount(count + 1)} />;
}

function Dashboard() {
  return (
    <>
      <Header />
      <DashboardCounter />
      <Footer />
    </>
  );
}

// 2. Keys for lists
// ❌ Bad: Index as key
{items.map((item, i) => <Item key={i} item={item} />)}

// ✅ Good: Unique, stable key
{items.map(item => <Item key={item.id} item={item} />)}

// 3. State structure optimization
// ❌ Bad: Monolithic state causes full re-render
const [state, setState] = useState({
  user: {},
  posts: [],
  comments: [],
  settings: {},
});

// ✅ Good: Split state
const [user, setUser] = useState({});
const [posts, setPosts] = useState([]);
const [comments, setComments] = useState([]);
const [settings, setSettings] = useState({});

// 4. useCallback for callbacks
function Parent() {
  const [count, setCount] = useState(0);

  // ❌ Bad: New function every render
  const handleClick = () => {
    setCount(count + 1);
  };

  // ✅ Good: Same function reference
  const handleClick2 = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  return <Child onClick={handleClick2} />;
}

// 5. Virtual scrolling for long lists
import { FixedSizeList } from 'react-window';

function LargeList({ items }) {
  return (
    <FixedSizeList height={600} itemCount={items.length} itemSize={35}>
      {({ index, style }) => (
        <div style={style}>{items[index].name}</div>
      )}
    </FixedSizeList>
  );
}

// 6. Throttling/debouncing expensive operations
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(handler);
  }, [value, delay]);

  return debouncedValue;
}

function Search() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);

  useEffect(() => {
    // Only search after user stops typing
    if (debouncedQuery) {
      performSearch(debouncedQuery);
    }
  }, [debouncedQuery]);

  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
    />
  );
}

// 7. Profiling with React DevTools Profiler
import { Profiler } from 'react';

function onRenderCallback(id, phase, actualDuration) {
  console.log(`${id} (${phase}) took ${actualDuration}ms`);
}

<Profiler id="Navigation" onRender={onRenderCallback}>
  <NavBar />
</Profiler>
```

---

#### Q98: What is concurrent rendering and how does it improve UX?

**Answer:**

```jsx
import { useTransition, useDeferredValue } from 'react';

// useTransition - mark updates as non-urgent
function SearchPage() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    const value = e.target.value;
    
    // Urgent: update input immediately
    setQuery(value);

    // Non-urgent: search can be interrupted
    startTransition(() => {
      const filtered = performSearch(value);
      setResults(filtered);
    });
  };

  return (
    <>
      <input value={query} onChange={handleChange} />
      {isPending && <Spinner />}
      <SearchResults results={results} />
    </>
  );
}

// useDeferredValue - defer updating a value
function DetailPage({ itemId }) {
  // itemId updates immediately
  // deferredId updates only when urgent work is done
  const deferredId = useDeferredValue(itemId);

  return (
    <>
      <Header itemId={itemId} />
      <Suspense fallback={<div>Loading details...</div>}>
        <Details itemId={deferredId} />
      </Suspense>
    </>
  );
}

// Concurrent rendering benefits:
// 1. Better responsiveness - UI feels faster
// 2. Graceful degradation - work can be interrupted
// 3. Improved UX - no janky animations
// 4. Automatic batching - fewer re-renders

// Before concurrent rendering:
// User types -> Search starts -> UI blocks -> Results show

// With concurrent rendering:
// User types -> Input updates immediately
// Search starts in background
// If user types again, search can be abandoned
// Results show when ready

// Example: Collaborative editor with concurrent rendering
function Editor() {
  const [content, setContent] = useState('');
  const [suggestions, setSuggestions] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    // Urgent: update text input
    setContent(e.target.value);

    // Non-urgent: get suggestions
    startTransition(() => {
      const newSuggestions = getSuggestions(e.target.value);
      setSuggestions(newSuggestions);
    });
  };

  return (
    <>
      <textarea value={content} onChange={handleChange} />
      {isPending && <div>Getting suggestions...</div>}
      <Suggestions suggestions={suggestions} />
    </>
  );
}
```

---

#### Q99: What are custom hooks? How do you create reusable logic across components?

**Answer:**

```javascript
// 1. Simple Hook: useLocalStorage
function useLocalStorage(key, initialValue) {
  // Store state value in localStorage
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  // Update localStorage when state changes
  const setValue = useCallback((value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  }, [key, storedValue]);

  return [storedValue, setValue];
}

// Usage
function UserProfile() {
  const [user, setUser] = useLocalStorage('user', null);
  return (
    <div>
      <input
        value={user?.name || ''}
        onChange={(e) => setUser({ ...user, name: e.target.value })}
      />
    </div>
  );
}

// 2. Complex Hook: useAsync
function useAsync(asyncFunction, immediate = true) {
  const [status, setStatus] = useState('idle');
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);

  const execute = useCallback(async () => {
    setStatus('pending');
    setData(null);
    setError(null);
    try {
      const result = await asyncFunction();
      setData(result);
      setStatus('success');
      return result;
    } catch (err) {
      setError(err);
      setStatus('error');
    }
  }, [asyncFunction]);

  useEffect(() => {
    if (immediate) {
      execute();
    }
  }, [execute, immediate]);

  return { execute, status, data, error };
}

// Usage
function UsersList() {
  const { data: users, status, execute } = useAsync(
    () => fetch('/api/users').then(r => r.json()),
    true
  );

  if (status === 'pending') return <div>Loading...</div>;
  if (status === 'error') return <div>Error fetching users</div>;
  
  return (
    <div>
      <button onClick={execute}>Refresh</button>
      <ul>
        {users?.map(user => <li key={user.id}>{user.name}</li>)}
      </ul>
    </div>
  );
}

// 3. Hook with Cleanup: usePrevious
function usePrevious(value) {
  const ref = useRef();
  
  useEffect(() => {
    ref.current = value;
  }, [value]);
  
  return ref.current;
}

// Usage
function Counter({ count }) {
  const prevCount = usePrevious(count);
  return <div>Now: {count}, Before: {prevCount}</div>;
}

// 4. Performance Hook: useDebounce
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(handler);
  }, [value, delay]);

  return debouncedValue;
}

// Usage
function SearchUsers({ onSearch }) {
  const [searchTerm, setSearchTerm] = useState('');
  const debouncedSearchTerm = useDebounce(searchTerm, 500);

  useEffect(() => {
    if (debouncedSearchTerm) {
      onSearch(debouncedSearchTerm);
    }
  }, [debouncedSearchTerm, onSearch]);

  return (
    <input
      value={searchTerm}
      onChange={(e) => setSearchTerm(e.target.value)}
      placeholder="Search (debounced)..."
    />
  );
}

// 5. State Hook: useReducer Hook
function useLocalReducer(reducer, initialValue) {
  const [state, dispatch] = useReducer(reducer, initialValue, (initial) => {
    const stored = localStorage.getItem('state');
    return stored ? JSON.parse(stored) : initial;
  });

  useEffect(() => {
    localStorage.setItem('state', JSON.stringify(state));
  }, [state]);

  return [state, dispatch];
}
```

**Key Principles:**
- Start with "use" prefix
- Rely only on other Hooks
- Return data and setter/updater functions
- Handle cleanup in useEffect
- Use useCallback for stable function references

---

#### Q100: What is Error Boundary? How do you implement them?

**Answer:**

```javascript
// Class component for Error Boundary
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught:', error, errorInfo);
    // Log to error reporting service
    logErrorToService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div style={{ padding: '20px', textAlign: 'center' }}>
          <h2>Something went wrong</h2>
          <details>
            {this.state.error?.toString()}
            <pre>{this.state.error?.stack}</pre>
          </details>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}

// Usage with specific error boundaries
export default function App() {
  return (
    <ErrorBoundary>
      <Header />
      <ErrorBoundary>
        <MainContent />
      </ErrorBoundary>
      <ErrorBoundary>
        <Sidebar />
      </ErrorBoundary>
      <Footer />
    </ErrorBoundary>
  );
}

// Functional error boundary using third-party library (react-error-boundary)
import { ErrorBoundary, FallbackComponent } from 'react-error-boundary';

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert">
      <p>Something went wrong:</p>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

export function App() {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onReset={() => window.location.href = '/'}
    >
      <MainContent />
    </ErrorBoundary>
  );
}

// Granular error boundaries
export function UserDetail({ userId }) {
  return (
    <div>
      <ErrorBoundary fallback={<div>Failed to load user</div>}>
        <UserCard userId={userId} />
      </ErrorBoundary>
      <ErrorBoundary fallback={<div>Failed to load posts</div>}>
        <UserPosts userId={userId} />
      </ErrorBoundary>
    </div>
  );
}

// Note: Error Boundaries do NOT catch:
// - Event handlers (use try/catch)
// - Async code (setTimeout, promises)
// - Server-side rendering
// - Errors in the boundary itself
```

**Best Practices:**
- Place boundaries at strategic points in component tree
- Use granular boundaries to isolate failures
- Log errors for monitoring
- Provide meaningful fallback UI
- Allow users to recover from errors

---

#### Q101: How do you handle code splitting and lazy loading in React?

**Answer:**

```javascript
import React, { Suspense, lazy } from 'react';

// Lazy load components
const UserProfile = lazy(() => import('./pages/UserProfile'));
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Admin = lazy(() => import('./pages/Admin'));

// Loading fallback component
function LoadingSpinner() {
  return (
    <div style={{ textAlign: 'center', padding: '40px' }}>
      <div className="spinner">Loading...</div>
    </div>
  );
}

// Router with code splitting
function App() {
  const [page, setPage] = useState('home');

  return (
    <div>
      <nav>
        <button onClick={() => setPage('profile')}>Profile</button>
        <button onClick={() => setPage('dashboard')}>Dashboard</button>
        <button onClick={() => setPage('admin')}>Admin</button>
      </nav>

      <Suspense fallback={<LoadingSpinner />}>
        {page === 'profile' && <UserProfile />}
        {page === 'dashboard' && <Dashboard />}
        {page === 'admin' && <Admin />}
      </Suspense>
    </div>
  );
}

// React Router with lazy loading
import { BrowserRouter, Routes, Route } from 'react-router-dom';

const routes = [
  { path: '/', component: lazy(() => import('./pages/Home')) },
  { path: '/about', component: lazy(() => import('./pages/About')) },
  { path: '/contact', component: lazy(() => import('./pages/Contact')) },
];

export function AppWithRouter() {
  return (
    <BrowserRouter>
      <Suspense fallback={<LoadingSpinner />}>
        <Routes>
          {routes.map(({ path, component: Component }) => (
            <Route key={path} path={path} element={<Component />} />
          ))}
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}

// Image lazy loading
function LazyImage({ src, alt }) {
  const [imageSrc, setImageSrc] = useState(null);
  const [imageRef, setImageRef] = useState();

  useEffect(() => {
    let observer;
    
    if (imageRef) {
      observer = new IntersectionObserver(
        ([entry]) => {
          if (entry.isIntersecting) {
            setImageSrc(src);
            observer.unobserve(imageRef);
          }
        },
        { threshold: 0.1 }
      );
      observer.observe(imageRef);
    }

    return () => observer?.disconnect();
  }, [imageRef, src]);

  return (
    <img
      ref={setImageRef}
      src={imageSrc}
      alt={alt}
      style={{ width: '100%', height: 'auto' }}
    />
  );
}

// Webpack dynamic imports
async function loadModule(moduleName) {
  return import(`./modules/${moduleName}`);
}

// Usage
function DynamicComponent() {
  const [Component, setComponent] = useState(null);

  useEffect(() => {
    loadModule('MyComponent').then(module => {
      setComponent(() => module.default);
    });
  }, []);

  if (!Component) return <LoadingSpinner />;
  return <Component />;
}

// Route-based code splitting
const Home = lazy(() => import('./Home'));
const About = lazy(() => import('./About'));
const App = () => (
  <Suspense fallback={<LoadingSpinner />}>
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
    </Routes>
  </Suspense>
);
```

**Benefits:**
- Smaller initial bundle size
- Faster initial page load
- Better performance on slow networks
- Progressive loading of features

---

#### Q102: How do you optimize performance with React Context API?

**Answer:**

```javascript
// Problem: Context causes unnecessary re-renders
const UserContext = React.createContext();

export function BadContextProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');
  
  // Every change in user OR theme causes re-render of all consumers
  return (
    <UserContext.Provider value={{ user, setUser, theme, setTheme }}>
      {children}
    </UserContext.Provider>
  );
}

// Solution 1: Split context
const UserContext = React.createContext();
const ThemeContext = React.createContext();

export function OptimizedProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');

  const userValue = useMemo(() => ({ user, setUser }), [user]);
  const themeValue = useMemo(() => ({ theme, setTheme }), [theme]);

  return (
    <UserContext.Provider value={userValue}>
      <ThemeContext.Provider value={themeValue}>
        {children}
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}

// Solution 2: Use custom hooks to memoize selectors
export function useUser() {
  const { user, setUser } = useContext(UserContext);
  return useMemo(() => ({ user, setUser }), [user]);
}

export function useTheme() {
  const { theme, setTheme } = useContext(ThemeContext);
  return useMemo(() => ({ theme, setTheme }), [theme]);
}

// Solution 3: Separate state updates
export function SmartProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');
  const [notifications, setNotifications] = useState([]);

  const userContextValue = useMemo(
    () => ({ user, setUser }),
    [user]
  );
  
  const uiContextValue = useMemo(
    () => ({ theme, setTheme, notifications, setNotifications }),
    [theme, notifications]
  );

  return (
    <UserContext.Provider value={userContextValue}>
      <UIContext.Provider value={uiContextValue}>
        {children}
      </UIContext.Provider>
    </UserContext.Provider>
  );
}

// Solution 4: Use useCallback for memoized callbacks
export function AdvancedProvider({ children }) {
  const [state, dispatch] = useReducer(reducer, initialState);

  const updateUser = useCallback(
    (userData) => dispatch({ type: 'UPDATE_USER', payload: userData }),
    []
  );

  const updateTheme = useCallback(
    (theme) => dispatch({ type: 'UPDATE_THEME', payload: theme }),
    []
  );

  const value = useMemo(
    () => ({ ...state, updateUser, updateTheme }),
    [state, updateUser, updateTheme]
  );

  return (
    <AppContext.Provider value={value}>
      {children}
    </AppContext.Provider>
  );
}

// Solution 5: Combine with useReducer for complex state
function reducer(state, action) {
  switch (action.type) {
    case 'UPDATE_USER':
      return { ...state, user: action.payload };
    case 'UPDATE_THEME':
      return { ...state, theme: action.payload };
    default:
      return state;
  }
}
```

**Performance Tips:**
- Split large contexts into smaller ones
- Use useMemo to memoize context values
- Use useCallback for update functions
- Consider Redux/Zustand for very complex state
- Use React DevTools Profiler to identify bottlenecks

---

#### Q103: How do you test React components? Show different testing approaches.

**Answer:**

```javascript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { render as rtlRender } from '@testing-library/react';

// 1. Basic Component Rendering Test
describe('Button Component', () => {
  it('renders button with correct text', () => {
    render(<button>Click me</button>);
    expect(screen.getByRole('button')).toHaveTextContent('Click me');
  });

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<button onClick={handleClick}>Click</button>);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});

// 2. Testing Hooks
describe('useCounter Hook', () => {
  it('increments counter', () => {
    const { result } = renderHook(() => useCounter(0));
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
});

// 3. Testing Async Operations
describe('User Fetching', () => {
  it('fetches and displays user data', async () => {
    render(<UserProfile userId={1} />);
    
    await waitFor(() => {
      expect(screen.getByText(/John/)).toBeInTheDocument();
    });
  });

  it('handles errors gracefully', async () => {
    jest.spyOn(global, 'fetch').mockRejectedValueOnce(new Error('API Error'));
    
    render(<UserProfile userId={1} />);
    
    await waitFor(() => {
      expect(screen.getByText(/Error/)).toBeInTheDocument();
    });
  });
});

// 4. Testing Redux Integration
describe('Redux Connected Component', () => {
  it('dispatches actions', () => {
    const store = createTestStore();
    render(
      <Provider store={store}>
        <UserForm />
      </Provider>
    );
    
    fireEvent.change(screen.getByPlaceholderText('Name'), {
      target: { value: 'John' },
    });
    fireEvent.click(screen.getByText('Submit'));
    
    expect(store.getState().users).toContainEqual(
      expect.objectContaining({ name: 'John' })
    );
  });
});

// 5. Snapshot Testing
describe('Card Component', () => {
  it('matches snapshot', () => {
    const { container } = render(
      <Card title="Test" content="Content" />
    );
    expect(container).toMatchSnapshot();
  });
});

// 6. Testing Context
describe('ThemeProvider', () => {
  it('provides theme context', () => {
    const TestComponent = () => {
      const { theme } = useContext(ThemeContext);
      return <div>{theme}</div>;
    };
    
    render(
      <ThemeProvider initialTheme="dark">
        <TestComponent />
      </ThemeProvider>
    );
    
    expect(screen.getByText('dark')).toBeInTheDocument();
  });
});

// 7. Testing User Interactions
describe('Form Component', () => {
  it('validates form input', async () => {
    const user = userEvent.setup();
    render(<LoginForm />);
    
    const emailInput = screen.getByLabelText(/email/i);
    const submitButton = screen.getByRole('button', { name: /submit/i });
    
    await user.type(emailInput, 'invalid-email');
    await user.click(submitButton);
    
    expect(screen.getByText(/invalid email/i)).toBeInTheDocument();
  });
});

// 8. Testing Component Props
describe('Button Component Props', () => {
  it.each([
    ['primary', 'btn-primary'],
    ['secondary', 'btn-secondary'],
    ['danger', 'btn-danger'],
  ])('applies correct class for %s variant', (variant, expectedClass) => {
    render(<Button variant={variant}>Test</Button>);
    expect(screen.getByRole('button')).toHaveClass(expectedClass);
  });
});

// 9. Setup and Teardown
describe('Component with Cleanup', () => {
  beforeEach(() => {
    // Mock setup
    jest.spyOn(window, 'localStorage', 'get').mockReturnValue({
      getItem: jest.fn(),
      setItem: jest.fn(),
    });
  });

  afterEach(() => {
    jest.restoreAllMocks();
  });

  it('uses localStorage', () => {
    render(<StorageComponent />);
    expect(window.localStorage.getItem).toHaveBeenCalled();
  });
});

// 10. Mock Third-Party Libraries
describe('Component Using External API', () => {
  it('handles external library calls', () => {
    jest.mock('external-library', () => ({
      someFunction: jest.fn(() => 'mocked value'),
    }));
    
    render(<ComponentUsingLibrary />);
    expect(screen.getByText('mocked value')).toBeInTheDocument();
  });
});
```

**Testing Best Practices:**
- Test behavior, not implementation
- Use data-testid sparingly
- Query by role when possible
- Test user interactions, not internal state
- Mock external dependencies
- Use proper assertions
- Keep tests focused and isolated

---

#### Q104: What are the different strategies for state management beyond Redux?

**Answer:**

```javascript
// Strategy 1: Context API with useReducer (Best for medium apps)
const StateContext = React.createContext();

function StateProvider({ children }) {
  const [state, dispatch] = useReducer(appReducer, initialState);

  return (
    <StateContext.Provider value={{ state, dispatch }}>
      {children}
    </StateContext.Provider>
  );
}

// Strategy 2: Zustand (Lightweight alternative to Redux)
import create from 'zustand';

const useStore = create((set) => ({
  user: null,
  theme: 'light',
  
  setUser: (user) => set({ user }),
  setTheme: (theme) => set({ theme }),
  
  // Reset
  reset: () => set({ user: null, theme: 'light' }),
}));

// Usage
function App() {
  const { user, theme, setUser, setTheme } = useStore();
  
  return (
    <div>
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </div>
  );
}

// Strategy 3: MobX (Reactive state management)
import { makeAutoObservable } from 'mobx';
import { observer } from 'mobx-react-lite';

class AppStore {
  user = null;
  theme = 'light';

  constructor() {
    makeAutoObservable(this);
  }

  setUser(user) {
    this.user = user;
  }

  setTheme(theme) {
    this.theme = theme;
  }
}

const store = new AppStore();

const App = observer(() => (
  <div style={{ background: store.theme === 'dark' ? '#000' : '#fff' }}>
    <button onClick={() => store.setTheme(store.theme === 'light' ? 'dark' : 'light')}>
      Toggle
    </button>
  </div>
));

// Strategy 4: Jotai (Primitive atoms for state)
import { atom, useAtom } from 'jotai';

const userAtom = atom(null);
const themeAtom = atom('light');

function App() {
  const [user, setUser] = useAtom(userAtom);
  const [theme, setTheme] = useAtom(themeAtom);
  
  return <div>{/* ... */}</div>;
}

// Strategy 5: Local Component State (Simple cases)
function UserProfile() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);

  return (
    <div>
      {/* Component code */}
    </div>
  );
}

// Strategy 6: Prop Drilling with Custom Hooks (For small trees)
function useAppState() {
  const [state, setState] = useState(initialState);
  return [state, setState];
}

function App() {
  const [appState, setAppState] = useAppState();
  return <Header state={appState} setState={setAppState} />;
}

// Comparison Table
const strategies = {
  'Context API': { complexity: 'medium', performance: 'good', boilerplate: 'medium' },
  'Redux': { complexity: 'high', performance: 'excellent', boilerplate: 'high' },
  'Zustand': { complexity: 'low', performance: 'excellent', boilerplate: 'low' },
  'MobX': { complexity: 'medium', performance: 'excellent', boilerplate: 'low' },
  'Jotai': { complexity: 'low', performance: 'excellent', boilerplate: 'low' },
  'LocalState': { complexity: 'low', performance: 'excellent', boilerplate: 'none' },
};
```

**When to Use Each:**
- **Local State**: Single component state
- **Context API**: Medium apps, avoid prop drilling
- **Redux**: Large apps, complex state, time-travel debugging
- **Zustand**: Simple, lightweight alternative to Redux
- **MobX**: Reactive programming preferences
- **Jotai**: Fine-grained reactivity with atoms

---

### Section 5B: Functional Programming Patterns for React (12 Questions)

#### Q88A: Explain pure functions, immutability, and functional composition.

**Answer:**

```javascript
// Pure functions - same input always produces same output, no side effects
function add(a, b) {
  return a + b; // Pure: deterministic, no side effects
}

// Impure function - depends on external state, has side effects
let total = 0;
function addToTotal(value) {
  total += value; // Impure: modifies external state
  console.log(total); // Side effect
}

// Immutability - don't mutate original data
// ❌ Mutating (impure)
const arr = [1, 2, 3];
function addBad(arr, item) {
  arr.push(item); // Mutates original
  return arr;
}

// ✅ Immutable (pure)
function addGood(arr, item) {
  return [...arr, item]; // Creates new array
}

// Object immutability
const user = { name: 'John', age: 30 };

// ❌ Mutating
user.age = 31;
user.address = '123 Main St';

// ✅ Immutable
const updatedUser = { ...user, age: 31 };
const userWithAddress = { ...user, address: '123 Main St' };
// Original user unchanged

// Deep immutability
const user2 = {
  name: 'John',
  profile: { city: 'NY', country: 'USA' }
};

// ❌ Shallow spread still mutates nested
const copy1 = { ...user2 };
copy1.profile.city = 'LA'; // Mutates original!

// ✅ Deep immutability
const copy2 = {
  ...user2,
  profile: { ...user2.profile, city: 'LA' }
};

// Function composition - combining functions
const double = x => x * 2;
const addOne = x => x + 1;
const square = x => x * x;

// Compose manually
const result = square(addOne(double(5))); // Nested, hard to read
// 5 -> 10 -> 11 -> 121

// Compose helper
const compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);

// Pipeline helper
const pipe = (...fns) => x => fns.reduce((v, f) => f(v), x);

const process = pipe(double, addOne, square);
const result2 = process(5); // 5 -> 10 -> 11 -> 121

// React example: Composing hooks
const withValidation = (validator) => (Component) => (props) => {
  const [isValid, setIsValid] = useState(false);
  
  useEffect(() => {
    setIsValid(validator(props));
  }, [props]);
  
  return isValid ? <Component {...props} /> : <div>Invalid</div>;
};

const withLoading = (Component) => ({ loading, ...props }) => (
  loading ? <div>Loading...</div> : <Component {...props} />
);

const EmailValidator = ({ email }) => <div>Email valid: {email}</div>;

// Compose HOCs
const Enhanced = withValidation(
  (props) => /^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/.test(props.email)
)(withLoading(EmailValidator));

// Use
<Enhanced email="test@example.com" loading={false} />
```

**Benefits:**
- Easier to test and reason about
- Prevent bugs from unexpected mutations
- Enable time-travel debugging
- Better performance optimization

---

#### Q88B: Higher-order functions (HOF) and currying - practical patterns.

**Answer:**

```javascript
// Higher-order function - takes or returns function
function createMultiplier(factor) {
  return (number) => number * factor; // Returns function
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15

// Currying - transform function into chain of functions
function add(a) {
  return (b) => a + b; // Curry: one param at a time
}

const add5 = add(5);
console.log(add5(10)); // 15

// Curried function factory
function curry(fn) {
  const arity = fn.length; // Number of parameters
  
  return function curried(...args) {
    if (args.length >= arity) {
      return fn(...args);
    } else {
      return (...nextArgs) => curried(...args, ...nextArgs);
    }
  };
}

const multiply = (a, b, c) => a * b * c;
const curriedMultiply = curry(multiply);

console.log(curriedMultiply(2)(3)(4)); // 24
console.log(curriedMultiply(2, 3)(4)); // 24 (partial application)

// Real-world: Event handler with data
function handleClick(userId, action) {
  return (e) => {
    console.log(`User ${userId} triggered ${action}`);
  };
}

const handleUserDelete = handleClick(123, 'delete');
<button onClick={handleUserDelete}>Delete User</button>

// Curry for API requests
const apiCall = (method) => (endpoint) => (data) => {
  return fetch(`/api${endpoint}`, {
    method,
    body: JSON.stringify(data)
  }).then(r => r.json());
};

const post = apiCall('POST');
const postUser = post('/users');
const createUser = postUser({ name: 'John' });

// React with HOF and currying
function useAsync(fn) {
  const [state, setState] = useState({ data: null, loading: false, error: null });
  
  const execute = useCallback(async (...args) => {
    setState({ loading: true });
    try {
      const data = await fn(...args);
      setState({ data, loading: false, error: null });
    } catch (error) {
      setState({ data: null, loading: false, error });
    }
  }, [fn]);
  
  return [state, execute];
}

function UserForm() {
  const handleSubmit = (userId) => (formData) => async () => {
    const response = await fetch(`/api/users/${userId}`, {
      method: 'PUT',
      body: JSON.stringify(formData)
    });
    return response.json();
  };
  
  const updateUser = handleSubmit(123);
  
  return (
    <form>
      <button onClick={() => updateUser({ name: 'Jane' })()}>
        Update
      </button>
    </form>
  );
}
```

**Use Cases:**
- Partial application and parameters binding
- Function factories
- Event handlers with context
- API request factories

---

#### Q88C: Memoization and performance optimization patterns.

**Answer:**

```javascript
// Memoization - cache function results
function memoize(fn) {
  const cache = {};
  
  return function(...args) {
    const key = JSON.stringify(args);
    
    if (key in cache) {
      console.log('From cache');
      return cache[key];
    }
    
    console.log('Computing');
    const result = fn(...args);
    cache[key] = result;
    return result;
  };
}

const expensiveCompute = (n) => {
  return Array.from({ length: n }).reduce((sum, _, i) => sum + i, 0);
};

const memoizedCompute = memoize(expensiveCompute);

console.log(memoizedCompute(100)); // Computing...
console.log(memoizedCompute(100)); // From cache

// Memoization with TTL (time to live)
function memoizeWithTTL(fn, ttl) {
  const cache = {};
  
  return function(...args) {
    const key = JSON.stringify(args);
    const cached = cache[key];
    const now = Date.now();
    
    if (cached && now - cached.timestamp < ttl) {
      return cached.value;
    }
    
    const result = fn(...args);
    cache[key] = { value: result, timestamp: now };
    return result;
  };
}

const fetchUser = memoizeWithTTL(
  (id) => fetch(`/api/users/${id}`).then(r => r.json()),
  5000 // 5 second TTL
);

// Memoization with WeakMap (for objects)
function memoizeObjects(fn) {
  const cache = new WeakMap();
  
  return function(obj) {
    if (cache.has(obj)) {
      return cache.get(obj);
    }
    
    const result = fn(obj);
    cache.set(obj, result);
    return result;
  };
}

// React performance pattern
function useMemoizedCallback(callback, deps) {
  const memoRef = useRef(new Map());
  
  return useCallback((...args) => {
    const key = JSON.stringify(args);
    const memo = memoRef.current;
    
    if (memo.has(key)) {
      return memo.get(key);
    }
    
    const result = callback(...args);
    memo.set(key, result);
    return result;
  }, deps);
}

// Debouncing and throttling
function debounce(fn, delay) {
  let timeoutId;
  
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn(...args), delay);
  };
}

function throttle(fn, limit) {
  let lastRun = 0;
  
  return function(...args) {
    const now = Date.now();
    if (now - lastRun >= limit) {
      fn(...args);
      lastRun = now;
    }
  };
}

// React usage
function SearchInput() {
  const [query, setQuery] = useState('');
  
  const debouncedSearch = useMemo(
    () => debounce((q) => {
      fetch(`/api/search?q=${q}`);
    }, 500),
    []
  );
  
  const handleChange = (e) => {
    setQuery(e.target.value);
    debouncedSearch(e.target.value);
  };
  
  return <input onChange={handleChange} />;
}
```

**Patterns:**
- Cache expensive computations
- Use TTL for dynamic data
- WeakMap for object-based caching
- Debounce/throttle for event handlers

---

#### Q88D: Functors, Monads basics, and practical functional patterns in React.

**Answer:**

```javascript
// Functor - an object with a map method
class Box {
  constructor(value) {
    this.value = value;
  }
  
  static of(value) {
    return new Box(value);
  }
  
  map(fn) {
    return Box.of(fn(this.value));
  }
  
  flatMap(fn) {
    return fn(this.value); // Monadic bind
  }
  
  getOrElse(defaultValue) {
    return this.value ?? defaultValue;
  }
}

// Using Functor
const result = Box.of(5)
  .map(x => x * 2)
  .map(x => x + 1)
  .getOrElse(0);

console.log(result); // 11

// Maybe Monad - handling null/undefined
class Maybe {
  constructor(value) {
    this.value = value;
  }
  
  static of(value) {
    return new Maybe(value);
  }
  
  isNothing() {
    return this.value === null || this.value === undefined;
  }
  
  map(fn) {
    return this.isNothing() ? this : Maybe.of(fn(this.value));
  }
  
  flatMap(fn) {
    return this.isNothing() ? this : fn(this.value);
  }
  
  getOrElse(defaultValue) {
    return this.isNothing() ? defaultValue : this.value;
  }
}

// Using Maybe
const getUserName = (user) => {
  return Maybe.of(user)
    .map(u => u.profile)
    .map(p => p.name)
    .getOrElse('Unknown User');
};

console.log(getUserName({ profile: { name: 'John' } })); // John
console.log(getUserName(null)); // Unknown User

// Either Monad - success or failure
class Either {
  constructor(value, isRight) {
    this.value = value;
    this.isRight = isRight;
  }
  
  static right(value) {
    return new Either(value, true);
  }
  
  static left(value) {
    return new Either(value, false);
  }
  
  map(fn) {
    return this.isRight ? Either.right(fn(this.value)) : this;
  }
  
  flatMap(fn) {
    return this.isRight ? fn(this.value) : this;
  }
  
  fold(leftFn, rightFn) {
    return this.isRight ? rightFn(this.value) : leftFn(this.value);
  }
}

// Using Either
const validate = (email) => {
  if (/^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/.test(email)) {
    return Either.right(email);
  } else {
    return Either.left('Invalid email');
  }
};

validate('test@example.com')
  .map(email => ({ email }))
  .fold(
    error => console.log('Error:', error),
    user => console.log('Success:', user)
  );

// Ramda.js patterns (functional library)
// import * as R from 'ramda';

const users = [
  { id: 1, name: 'John', age: 30 },
  { id: 2, name: 'Jane', age: 25 },
  { id: 3, name: 'Bob', age: 35 }
];

// Composition
const getAdults = R.filter(R.propGt('age', 25));
const getNames = R.pluck('name');

const adultNames = R.pipe(
  getAdults,
  getNames
)(users);

console.log(adultNames); // ['John', 'Bob']

// React with functional patterns
function useAsync(fn) {
  const [state, setState] = useState({ data: null, error: null, loading: false });
  
  useEffect(() => {
    (async () => {
      try {
        const data = await fn();
        setState({ data, error: null, loading: false });
      } catch (error) {
        setState({ data: null, error, loading: false });
      }
    })();
  }, []);
  
  return state;
}

// Using Either pattern in React
function useValidatedInput(initialValue, validator) {
  const [input, setInput] = useState(initialValue);
  
  const validation = validator(input);
  
  return {
    input,
    setInput,
    isValid: validation.isRight,
    error: validation.fold(
      error => error,
      () => null
    )
  };
}

function Form() {
  const emailValidator = (email) => {
    return /^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/.test(email)
      ? Either.right(email)
      : Either.left('Invalid email');
  };
  
  const { input, setInput, isValid, error } = useValidatedInput('', emailValidator);
  
  return (
    <div>
      <input value={input} onChange={(e) => setInput(e.target.value)} />
      {error && <span style={{ color: 'red' }}>{error}</span>}
      <button disabled={!isValid}>Submit</button>
    </div>
  );
}
```

**Key Concepts:**
- Functors: Mappable objects
- Monads: Composable computations
- Maybe: Optional values
- Either: Success/Failure handling
- Ramda: Functional utility library

---

### Section 6A: Micro-frontends - ADDITIONAL (20+ Questions)

#### Q99: What is module federation? How does it work with Webpack 5?

**Answer:**

```javascript
// Micro-frontend 1: Header (producer)
// webpack.config.js
const ModuleFederationPlugin = require('webpack/lib/container/ModuleFederationPlugin');

module.exports = {
  mode: 'production',
  entry: './src/index',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  plugins: [
    new ModuleFederationPlugin({
      name: 'header', // Unique name
      filename: 'remoteEntry.js', // Entry file
      exposes: {
        './HeaderComponent': './src/Header', // Expose component
        './userStore': './src/store', // Can expose anything
      },
      shared: {
        react: { singleton: true, requiredVersion: '^18.0.0' },
        'react-dom': { singleton: true, requiredVersion: '^18.0.0' },
      },
    }),
  ],
};

// Shell application (consumer)
// webpack.config.js
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'shell',
      remotes: {
        header: 'header@http://localhost:3001/remoteEntry.js',
        sidebar: 'sidebar@http://localhost:3002/remoteEntry.js',
      },
      shared: {
        react: { singleton: true },
        'react-dom': { singleton: true },
      },
    }),
  ],
};

// Use in shell
import React, { Suspense, lazy } from 'react';

const Header = lazy(() => import('header/HeaderComponent'));
const Sidebar = lazy(() => import('sidebar/SidebarComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Header />
      <Sidebar />
    </Suspense>
  );
}

// Key concepts:
// - name: Unique identifier for the micro-frontend
// - filename: Entry point file for remote access
// - exposes: Components/modules to share
// - remotes: Remote modules to consume
// - shared: Shared dependencies (avoid duplication)
```

---

#### Q120: How do you handle versioning in micro-frontends?

**Answer:**

```javascript
// Version management strategies

// 1. Semver version in remoteEntry
const ModuleFederationPlugin = require('webpack/lib/container/ModuleFederationPlugin');

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'header@v2.0.0', // Include version
      filename: 'remoteEntry.js',
      exposes: {
        './Header': './src/Header',
      },
      shared: {
        react: { singleton: true, requiredVersion: '^18.0.0' },
      },
    }),
  ],
};

// 2. Manifest file approach
// manifest.json
{
  "header": {
    "versions": ["1.0.0", "1.1.0", "2.0.0"],
    "current": "2.0.0",
    "urls": {
      "1.0.0": "https://header-v1.example.com",
      "1.1.0": "https://header-v1-1.example.com",
      "2.0.0": "https://header.example.com"
    }
  }
}

// Load versioned remote
async function loadRemote(name, version) {
  const manifest = await fetch('/manifest.json').then(r => r.json());
  const url = manifest[name].urls[version];
  const script = document.createElement('script');
  script.src = `${url}/remoteEntry.js`;
  document.head.appendChild(script);
  return window[name];
}

// 3. Version compatibility checking
const ModuleFederationPlugin = require('webpack/lib/container/ModuleFederationPlugin');

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'header',
      exposes: {
        './Header': './src/Header',
      },
      shared: {
        react: {
          singleton: true,
          requiredVersion: '^18.2.0', // Min version
          strictVersion: true, // Fail if version mismatch
        },
      },
    }),
  ],
};

// 4. Graceful fallback for versions
async function loadComponent(name, version) {
  try {
    // Try to load exact version
    return await import(`${name}@${version}`);
  } catch (err) {
    console.warn(`${name} v${version} not available, loading latest`);
    // Fall back to latest
    return await import(name);
  }
}

// Best practices:
// 1. Use semantic versioning
// 2. Maintain backward compatibility
// 3. Document breaking changes
// 4. Use feature flags for gradual rollout
// 5. Monitor version usage metrics
```

---

#### Q121: How do you share state between micro-frontends?

**Answer:**

```javascript
// Already covered in detail (Q22), but let's extend:

// 1. Event Bus - Simple but loose coupling
class EventBus {
  private listeners = {};

  subscribe(event, callback) {
    if (!this.listeners[event]) {
      this.listeners[event] = [];
    }
    this.listeners[event].push(callback);
    return () => {
      this.listeners[event] = this.listeners[event].filter(cb => cb !== callback);
    };
  }

  publish(event, data) {
    this.listeners[event]?.forEach(cb => cb(data));
  }
}

// Global event bus
window.__eventBus__ = new EventBus();

// Micro-frontend 1: User service
window.__eventBus__.publish('user:login', { userId: 123, name: 'Alice' });

// Micro-frontend 2: Listen for login
window.__eventBus__.subscribe('user:login', (user) => {
  console.log('User logged in:', user);
  updateHeader(user);
});

// 2. Module Federation - Shared store
// Store micro-frontend exposes Redux store
const ModuleFederationPlugin = require('webpack/lib/container/ModuleFederationPlugin');

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'store',
      exposes: {
        './store': './src/store',
      },
      shared: ['redux', 'react-redux'],
    }),
  ],
};

// Other micro-frontends import shared store
import store from 'store/store';

// 3. Local Storage - Persistent sharing
class PersistentStore {
  set(key, value) {
    localStorage.setItem(key, JSON.stringify(value));
    window.dispatchEvent(new StorageEvent('storage', { key }));
  }

  get(key) {
    return JSON.parse(localStorage.getItem(key));
  }

  subscribe(key, callback) {
    const handler = (e) => {
      if (e.key === key) {
        callback(JSON.parse(e.newValue));
      }
    };
    window.addEventListener('storage', handler);
    return () => window.removeEventListener('storage', handler);
  }
}

const persistentStore = new PersistentStore();
persistentStore.set('user', { id: 1, name: 'Alice' });
persistentStore.subscribe('user', (user) => console.log('User updated:', user));

// 4. Query Parameters - URL-based sharing
// Navigation with data
function navigateWithData(path, data) {
  const params = new URLSearchParams(data);
  window.location.href = `${path}?${params}`;
}

navigateWithData('/micro-frontend-2', { userId: 123 });

// Read from URL
const params = new URLSearchParams(window.location.search);
const userId = params.get('userId');

// 5. Window object - Simple sharing
window.__SHARED_STATE__ = {
  user: null,
  notifications: [],
  setUser: (user) => {
    window.__SHARED_STATE__.user = user;
    // Notify listeners
  },
};

// Micro-frontend subscribes
setInterval(() => {
  if (window.__SHARED_STATE__.user) {
    updateUI(window.__SHARED_STATE__.user);
  }
}, 100);
```

---

#### Q122: How do you handle CSS and styling conflicts between micro-frontends?

**Answer:**

```jsx
// CSS conflicts - different micro-frontends using same class names

// ❌ Problem: Global CSS conflicts
// Header: .title { color: blue; }
// Main: .title { color: red; }
// Result: Unpredictable styling

// ✅ Solution 1: CSS Modules
// Header/Title.module.css
.title {
  color: blue;
  font-size: 20px;
}

// Header/Title.jsx
import styles from './Title.module.css';
export function Title() {
  return <h1 className={styles.title}>Title</h1>;
}

// Main/Title.jsx - Different styles without conflict
import styles from './Title.module.css';
export function Title() {
  return <h2 className={styles.title}>Title</h2>;
}

// ✅ Solution 2: CSS-in-JS (Styled Components)
import styled from 'styled-components';

const HeaderTitle = styled.h1`
  color: blue;
  font-size: 20px;
`;

const MainTitle = styled.h2`
  color: red;
  font-size: 18px;
`;

// ✅ Solution 3: BEM naming convention
// Header
<h1 className="header__title">Title</h1>

// Main
<h2 className="main__title">Title</h2>

// ✅ Solution 4: Shadow DOM (Web Components)
class HeaderElement extends HTMLElement {
  connectedCallback() {
    const shadow = this.attachShadow({ mode: 'open' });
    const style = document.createElement('style');
    style.textContent = `
      h1 { color: blue; }
    `;
    shadow.appendChild(style);
    
    const title = document.createElement('h1');
    title.textContent = 'Title';
    shadow.appendChild(title);
  }
}

customElements.define('header-title', HeaderElement);

// ✅ Solution 5: CSS-in-JS with namespacing
const ThemingContext = createContext();

export function MicroFrontendWrapper({ name, children }) {
  return (
    <ThemingContext.Provider value={{ namespace: name }}>
      <div className={`mfe-${name}`}>
        {children}
      </div>
    </ThemingContext.Provider>
  );
}

// Global CSS with namespace
.mfe-header .title { color: blue; }
.mfe-main .title { color: red; }

// ✅ Solution 6: Utility-first CSS (Tailwind)
// Tailwind's scoped class names prevent conflicts
<h1 className="text-blue-600 text-2xl">Header Title</h1>
<h1 className="text-red-600 text-xl">Main Title</h1>
```

---

#### Q123: How do you handle session management and authentication across micro-frontends?

**Answer:**

```javascript
// Centralized auth service
class AuthService {
  private token = null;
  private refreshToken = null;
  private subscribers = [];

  login(credentials) {
    return fetch('/api/auth/login', {
      method: 'POST',
      body: JSON.stringify(credentials),
    })
      .then(r => r.json())
      .then(data => {
        this.token = data.token;
        this.refreshToken = data.refreshToken;
        // Store in localStorage for persistence
        localStorage.setItem('auth_token', this.token);
        localStorage.setItem('refresh_token', this.refreshToken);
        this.notifySubscribers();
        return data;
      });
  }

  getToken() {
    return this.token || localStorage.getItem('auth_token');
  }

  subscribe(callback) {
    this.subscribers.push(callback);
    return () => {
      this.subscribers = this.subscribers.filter(cb => cb !== callback);
    };
  }

  notifySubscribers() {
    this.subscribers.forEach(cb => cb(this.getToken()));
  }

  logout() {
    this.token = null;
    this.refreshToken = null;
    localStorage.removeItem('auth_token');
    localStorage.removeItem('refresh_token');
    this.notifySubscribers();
  }
}

// Expose globally for all MFEs
window.__AUTH__ = new AuthService();

// In each MFE
const useAuth = () => {
  const [token, setToken] = useState(window.__AUTH__.getToken());

  useEffect(() => {
    return window.__AUTH__.subscribe(setToken);
  }, []);

  return {
    token,
    isAuthenticated: !!token,
    login: window.__AUTH__.login.bind(window.__AUTH__),
    logout: window.__AUTH__.logout.bind(window.__AUTH__),
  };
};
```

---

#### Q124: How do you implement lazy loading of micro-frontends?

**Answer:**

```javascript
// Dynamic remote loading
const remotes = {
  header: {
    url: 'http://localhost:3001/remoteEntry.js',
    loaded: false,
  },
  sidebar: {
    url: 'http://localhost:3002/remoteEntry.js',
    loaded: false,
  },
};

async function loadRemote(name) {
  if (remotes[name].loaded) {
    return window[name];
  }

  return new Promise((resolve, reject) => {
    const script = document.createElement('script');
    script.src = remotes[name].url;
    script.onload = () => {
      remotes[name].loaded = true;
      resolve(window[name]);
    };
    script.onerror = reject;
    document.head.appendChild(script);
  });
}

// React component with lazy loading
const Header = lazy(async () => {
  const remote = await loadRemote('header');
  return { default: remote.HeaderComponent };
});

function App() {
  return (
    <Suspense fallback={<HeaderSkeleton />}>
      <Header />
    </Suspense>
  );
}

// Preload important MFEs
useEffect(() => {
  preloadRemote('header');
}, []);

function preloadRemote(name) {
  const link = document.createElement('link');
  link.rel = 'prefetch';
  link.href = remotes[name].url;
  document.head.appendChild(link);
}
```

---

#### Q125: What is the strangler pattern for migrating monoliths to micro-frontends?

**Answer:**

```javascript
// Strangler pattern - gradually replace old monolith with MFEs

// Phase 1: Proxy layer
const proxyRouter = (req, res, next) => {
  // Route to old monolith
  if (req.path.startsWith('/legacy-')) {
    proxyToMonolith(req, res);
  } else if (req.path.startsWith('/api/')) {
    // New API, all MFEs use this
    forwardToNewAPI(req, res);
  } else {
    // Route to appropriate MFE
    const mfe = determineMFE(req.path);
    proxyToMFE(mfe, req, res);
  }
};

// Phase 2: Migrate feature by feature
const routes = [
  // Old monolith routes (being phased out)
  { path: '/dashboard', target: 'monolith', status: 'deprecated' },
  // New MFE routes (replacing old)
  { path: '/dashboard', target: 'dashboard-mfe', status: 'active' },
];

// Client-side strangler
function migrateFeature(featureName) {
  const newMFE = dynamicImport(`./mfes/${featureName}`);
  const oldComponent = components[featureName];

  // A/B testing during migration
  const useNewVersion = shouldUseNewMFE(featureName, userId);

  return useNewVersion ? newMFE : oldComponent;
}

// Monitoring migration progress
const migrationMetrics = {
  'dashboard': 0.8, // 80% users on new MFE
  'settings': 0.3, // 30% users on new MFE
  'profile': 0.95, // 95% users on new MFE
};

// Gradually increase traffic to new MFE
function getTrafficSplit(featureName) {
  const adoption = migrationMetrics[featureName];
  return Math.random() < adoption;
}
```

---

#### Q126: How do you manage dependencies between micro-frontends?

**Answer:**

```javascript
// Dependency graph management
class DependencyManager {
  private dependencies = {
    auth: { version: '2.0.0', dependents: ['header', 'profile'] },
    ui-kit: { version: '3.1.0', dependents: ['header', 'sidebar', 'footer'] },
    api-client: { version: '1.5.0', dependents: ['all'] },
  };

  getDependencies(mfeName) {
    return Object.entries(this.dependencies).reduce((deps, [name, info]) => {
      if (info.dependents.includes(mfeName) || info.dependents.includes('all')) {
        deps.push({ name, version: info.version });
      }
      return deps;
    }, []);
  }

  validateCompatibility(mfeName, version) {
    const deps = this.getDependencies(mfeName);
    // Check if dependencies are compatible with this version
    return deps.every(dep => {
      const installedVersion = this.getInstalledVersion(dep.name);
      return this.isCompatible(installedVersion, dep.version);
    });
  }

  isCompatible(installed, required) {
    // Semantic versioning check
    const [iMajor] = installed.split('.');
    const [rMajor] = required.split('.');
    return iMajor === rMajor;
  }

  getInstalledVersion(name) {
    return (window.__VERSIONS__ && window.__VERSIONS__[name]) || null;
  }
}

// Webpack module federation with shared dependencies
module.exports = {
  shared: {
    'api-client': {
      singleton: true,
      strictVersion: '^1.5.0',
    },
    'ui-kit': {
      singleton: true,
      eager: true, // Load immediately
    },
  },
};

// Health check for dependencies
async function checkDependencyHealth() {
  const health = {};
  const deps = dependencyManager.getDependencies(appName);

  for (const dep of deps) {
    try {
      const response = await fetch(`/api/health/${dep.name}`);
      health[dep.name] = response.ok ? 'healthy' : 'degraded';
    } catch (err) {
      health[dep.name] = 'unhealthy';
    }
  }

  return health;
}
```

---

#### Q127: How do you monitor and log errors across micro-frontends?

**Answer:**

```javascript
// Centralized error reporting
class ErrorReporter {
  private endpoint = '/api/errors';
  private queue = [];

  captureError(error, context) {
    const errorData = {
      message: error.message,
      stack: error.stack,
      mfe: context.mfe,
      userId: context.userId,
      timestamp: Date.now(),
      severity: this.calculateSeverity(error),
      source: context.source,
    };

    this.queue.push(errorData);
    this.reportIfReady();
  }

  private reportIfReady() {
    if (this.queue.length >= 10 || this.shouldFlush()) {
      this.flush();
    }
  }

  private flush() {
    if (this.queue.length === 0) return;

    const errors = [...this.queue];
    this.queue = [];

    // Use sendBeacon for reliability
    navigator.sendBeacon(
      this.endpoint,
      JSON.stringify({ errors })
    );
  }

  calculateSeverity(error) {
    if (error.message.includes('Authentication')) return 'high';
    if (error.message.includes('Network')) return 'medium';
    return 'low';
  }
}

// Global error handler per MFE
window.addEventListener('error', (event) => {
  window.__ERROR_REPORTER__.captureError(event.error, {
    mfe: 'header',
    userId: window.__AUTH__.userId,
    source: 'uncaughtException',
  });
});

// Promise rejection handler
window.addEventListener('unhandledrejection', (event) => {
  window.__ERROR_REPORTER__.captureError(event.reason, {
    mfe: 'header',
    userId: window.__AUTH__.userId,
    source: 'unhandledRejection',
  });
});

// React error boundary
class ErrorBoundary extends React.Component {
  componentDidCatch(error, errorInfo) {
    window.__ERROR_REPORTER__.captureError(error, {
      mfe: 'header',
      userId: window.__AUTH__.userId,
      source: 'componentError',
      component: this.constructor.name,
    });
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback />;
    }
    return this.props.children;
  }
}

// Logging aggregation
const LogAggregator = {
  logs: [],

  log(level, mfe, message, data) {
    this.logs.push({
      level,
      mfe,
      message,
      data,
      timestamp: Date.now(),
    });

    // Send batch to server
    if (this.logs.length >= 50) {
      this.flush();
    }
  },

  flush() {
    navigator.sendBeacon('/api/logs', JSON.stringify(this.logs));
    this.logs = [];
  },
};
```

---

#### Q128: How do you handle performance monitoring across micro-frontends?

**Answer:**

```javascript
// Performance tracking for MFEs
class PerformanceMonitor {
  private metrics = {};

  recordMetric(mfe, metricName, value) {
    if (!this.metrics[mfe]) {
      this.metrics[mfe] = {};
    }
    this.metrics[mfe][metricName] = value;
  }

  captureWebVitals(mfe) {
    // Largest Contentful Paint
    const lcpObserver = new PerformanceObserver((list) => {
      const entries = list.getEntries();
      const lastEntry = entries[entries.length - 1];
      this.recordMetric(mfe, 'LCP', lastEntry.renderTime || lastEntry.loadTime);
    });

    lcpObserver.observe({ entryTypes: ['largest-contentful-paint'] });

    // Cumulative Layout Shift
    let clsValue = 0;
    const clsObserver = new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        if (!entry.hadRecentInput) {
          clsValue += entry.value;
          this.recordMetric(mfe, 'CLS', clsValue);
        }
      }
    });

    clsObserver.observe({ entryTypes: ['layout-shift'] });

    // First Input Delay
    const fidObserver = new PerformanceObserver((list) => {
      const entries = list.getEntries();
      entries.forEach(entry => {
        this.recordMetric(mfe, 'FID', entry.processingDuration);
      });
    });

    fidObserver.observe({ entryTypes: ['first-input'] });
  }

  reportMetrics() {
    const report = {
      mfeMetrics: this.metrics,
      timestamp: Date.now(),
    };

    navigator.sendBeacon('/api/metrics', JSON.stringify(report));
  }

  measureLoadTime(mfe) {
    const startTime = performance.now();
    return {
      markComplete: () => {
        const endTime = performance.now();
        this.recordMetric(mfe, 'loadTime', endTime - startTime);
      },
    };
  }
}

// Usage in MFE
const perf = new PerformanceMonitor();
perf.captureWebVitals('header');

const loadTimer = perf.measureLoadTime('header');
// ... load MFE ...
loadTimer.markComplete();

// Automatic reporting
setInterval(() => {
  perf.reportMetrics();
}, 60000);
```

---

#### Q129: What is the hub-and-spoke architecture for micro-frontends?

**Answer:**

```javascript
// Hub-and-spoke (Star topology) architecture

// Hub (Shell/Container)
const HubApp = () => {
  const [activeApp, setActiveApp] = useState('home');
  const [sharedState, setSharedState] = useState({});

  return (
    <div className="hub">
      <Navigation onAppSelect={setActiveApp} />
      
      <Router>
        <Routes>
          {/* Spokes - individual MFEs */}
          <Route path="/home" element={<HomeSpoke />} />
          <Route path="/products" element={<ProductsSpoke />} />
          <Route path="/cart" element={<CartSpoke />} />
          <Route path="/checkout" element={<CheckoutSpoke />} />
        </Routes>
      </Router>
    </div>
  );
};

// Each spoke (MFE) communicates through hub
const ProductsSpoke = () => {
  const { dispatch } = useContext(HubContext);

  const addToCart = (product) => {
    // Send action to hub
    dispatch({
      type: 'ADD_TO_CART',
      payload: product,
    });
  };

  return (
    <ProductList onAddToCart={addToCart} />
  );
};

// Hub manages all state
function hubReducer(state, action) {
  switch (action.type) {
    case 'ADD_TO_CART':
      return {
        ...state,
        cart: [...state.cart, action.payload],
      };
    case 'CHECKOUT':
      return {
        ...state,
        orders: [...state.orders, action.payload],
      };
    default:
      return state;
  }
}

// Advantages:
// - Single entry point
// - Centralized routing
// - Shared state management
// - Easy authentication

// Disadvantages:
// - Hub becomes bottleneck
// - Hub is critical (single point of failure)
// - MFEs tightly coupled to hub interface
```

---

#### Q130: What is the multi-spa architecture approach?

**Answer:**

```javascript
// Multi-SPA (Single Page Application) approach
// Each MFE is a completely independent SPA

// Application Registry
const apps = {
  'home': {
    url: 'http://localhost:3001',
    activeWhen: '/',
    props: {
      appStore: globalStore,
    },
  },
  'products': {
    url: 'http://localhost:3002',
    activeWhen: '/products',
    props: {
      appStore: globalStore,
    },
  },
  'cart': {
    url: 'http://localhost:3003',
    activeWhen: '/cart',
    props: {
      appStore: globalStore,
    },
  },
};

// Router decides which SPA to load
function Router() {
  const currentPath = window.location.pathname;
  const appEntry = Object.values(apps).find(
    app => currentPath.startsWith(app.activeWhen)
  );

  if (!appEntry) return <NotFound />;

  return <IFrame src={appEntry.url} />;
}

// Using single-spa library
import { registerApplication, start } from 'single-spa';

registerApplication({
  name: '@app/home',
  app: () => System.import('@app/home'),
  activeWhen: '/',
});

registerApplication({
  name: '@app/products',
  app: () => System.import('@app/products'),
  activeWhen: '/products',
});

start();

// Advantages:
// - Complete isolation between SPAs
// - Independent deployment
// - Different frameworks possible
// - No shared state concerns

// Disadvantages:
// - More overhead
// - Navigation between SPAs is slow
// - State sharing is complex
// - Larger bundle size
```

---

#### Q131: How do you implement feature flags in micro-frontends?

**Answer:**

```javascript
// Feature flag management
class FeatureFlagManager {
  private flags = {};
  private cache = new Map();

  async initialize() {
    try {
      const response = await fetch('/api/feature-flags');
      this.flags = await response.json();
      this.startPolling();
    } catch (err) {
      console.error('Failed to load feature flags', err);
    }
  }

  isEnabled(flagName, context = {}) {
    // Check cache first
    const cacheKey = `${flagName}:${JSON.stringify(context)}`;
    if (this.cache.has(cacheKey)) {
      return this.cache.get(cacheKey);
    }

    const flag = this.flags[flagName];
    if (!flag) return false;

    // Evaluate conditions
    const enabled = this.evaluateFlag(flag, context);
    this.cache.set(cacheKey, enabled);
    return enabled;
  }

  evaluateFlag(flag, context) {
    if (typeof flag === 'boolean') return flag;

    // Percentage rollout
    if (flag.rollout) {
      const hash = hashUserId(context.userId);
      return hash % 100 < flag.rollout;
    }

    // User segment
    if (flag.segment) {
      return flag.segment.includes(context.userId);
    }

    // Geo-targeting
    if (flag.regions) {
      return flag.regions.includes(context.region);
    }

    return false;
  }

  startPolling() {
    setInterval(async () => {
      const response = await fetch('/api/feature-flags');
      this.flags = await response.json();
      this.cache.clear(); // Invalidate cache
    }, 30000);
  }
}

// Usage in React
function useFeatureFlag(flagName, context) {
  const [enabled, setEnabled] = useState(false);

  useEffect(() => {
    setEnabled(window.__FEATURE_FLAGS__.isEnabled(flagName, context));
  }, [flagName, context]);

  return enabled;
}

// Component example
function CheckoutButton() {
  const newCheckoutEnabled = useFeatureFlag('new_checkout', {
    userId: currentUserId,
    region: userRegion,
  });

  return (
    <>
      {newCheckoutEnabled ? (
        <NewCheckoutComponent />
      ) : (
        <LegacyCheckoutComponent />
      )}
    </>
  );
}

// Server-side feature flags for MFE visibility
const serverFeatureFlags = {
  'new_checkout': true,
  'beta_dashboard': false,
  'experimental_ui': { rollout: 10 }, // 10% of users
};
```

---

#### Q132: What are the differences between vertical and horizontal split of micro-frontends?

**Answer:**

```javascript
// Vertical Split - Feature-based (Recommended)
// Each MFE owns full stack: UI, API, Data
/*
┌─────────────────────────────────────────────────┐
│                  Shell/Container                 │
├──────────────┬──────────────┬────────────────────┤
│   Products   │   Cart       │   Checkout         │
│   (UI + API) │   (UI + API) │   (UI + API)       │
│   (DB)       │   (DB)       │   (DB)             │
└──────────────┴──────────────┴────────────────────┘
*/

// Each MFE is independent
const ProductsMFE = {
  UI: 'products-ui-app',
  API: '/products-api',
  Database: 'products_db',
};

const CartMFE = {
  UI: 'cart-ui-app',
  API: '/cart-api',
  Database: 'cart_db',
};

// Horizontal Split - Layer-based
// Each MFE owns one layer: shared UI, API, or Data
/*
┌─────────────────────────────────────────────────┐
│           UI Layer (Components/Styles)          │
├─────────────────────────────────────────────────┤
│         Business Logic Layer (State/Hooks)      │
├─────────────────────────────────────────────────┤
│              API Layer (Data Access)            │
├─────────────────────────────────────────────────┤
│              Database Layer                     │
└─────────────────────────────────────────────────┘
*/

// Shared UI components
export const UIComponents = {
  Button: lazy(() => import('ui-mfe/Button')),
  Modal: lazy(() => import('ui-mfe/Modal')),
};

// Shared business logic
export const BusinessLogic = {
  useUser: () => import('logic-mfe/useUser'),
  useCart: () => import('logic-mfe/useCart'),
};

// Comparison:
const comparison = {
  vertical: {
    pros: [
      'Independent deployments',
      'Easier onboarding (full stack)',
      'Clear ownership',
      'Better isolation',
    ],
    cons: [
      'Potential code duplication',
      'Harder to share UI components',
    ],
  },
  horizontal: {
    pros: [
      'Better code reuse',
      'Consistent UI across app',
      'Shared styling',
    ],
    cons: [
      'Coupled layers',
      'Complex dependency management',
      'Hard to test in isolation',
    ],
  },
};

// Best practice: Mostly vertical with shared layer
const hybridApproach = {
  shell: 'container',
  verticalMFEs: ['products', 'cart', 'checkout'],
  sharedLayer: ['ui-kit', 'api-client', 'auth'],
};
```

---

#### Q133: How do you implement gradual migrations with micro-frontends?

**Answer:**

```javascript
// Gradual migration strategy - move components incrementally

// Phase 1: Parallel running
function DualComponent() {
  const [useNewVersion, setUseNewVersion] = useState(false);

  // A/B test: 50% users get new, 50% get old
  useEffect(() => {
    setUseNewVersion(shouldUseNewVersion(userId));
  }, []);

  return useNewVersion ? <NewComponent /> : <OldComponent />;
}

// Phase 2: Feature flag based
function MigratingComponent() {
  const featureEnabled = useFeatureFlag('new_component_enabled', {
    userId,
  });

  const Component = featureEnabled ? NewComponent : OldComponent;
  return <Component />;
}

// Phase 3: Automated migration with rollback
class MigrationController {
  private rolloutPercentage = 0;

  async startMigration(componentName) {
    while (this.rolloutPercentage <= 100) {
      await this.setRollout(componentName, this.rolloutPercentage);

      // Monitor metrics
      const metrics = await this.getMetrics(componentName);
      const errorRate = metrics.errors / metrics.total;

      if (errorRate > 0.05) {
        // Error rate > 5%, rollback
        console.error('Migration failed, rolling back');
        await this.rollback(componentName);
        return;
      }

      // Increment rollout
      this.rolloutPercentage += 10; // 10% at a time
      await this.sleep(3600000); // Wait 1 hour
    }

    console.log('Migration complete');
  }

  async setRollout(componentName, percentage) {
    return fetch(`/api/flags/${componentName}`, {
      method: 'PATCH',
      body: JSON.stringify({ rollout: percentage }),
    });
  }

  async getMetrics(componentName) {
    const response = await fetch(`/api/metrics/${componentName}`);
    return response.json();
  }

  async rollback(componentName) {
    await this.setRollout(componentName, 0);
  }

  sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}

// Usage
const migrationController = new MigrationController();
migrationController.startMigration('products-component');

// Health-checked migration
async function canMigrate(mfeName) {
  const healthChecks = await Promise.all([
    fetch(`/api/health/${mfeName}`),
    fetch(`/api/dependencies/${mfeName}`),
    fetch(`/api/performance/${mfeName}`),
  ]);

  return healthChecks.every(r => r.ok);
}
```

---

#### Q134: How do you handle testing in micro-frontend architecture?

**Answer:**

```javascript
// Unit Testing Individual MFEs
// micro-frontend-1/__tests__/Button.test.js
import { render, screen } from '@testing-library/react';
import Button from '../Button';

describe('MFE1 - Button Component', () => {
  it('renders button correctly', () => {
    render(<Button label="Click me" />);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });
});

// Integration Testing with Module Federation
// __tests__/integration.test.js
import { render, screen, waitFor } from '@testing-library/react';
import React from 'react';

// Mock module federation for tests
jest.mock('shared/Button', () => ({
  default: () => <button>Mocked Button</button>,
}));

describe('MFE Integration', () => {
  it('loads remote module and renders', async () => {
    render(<App />);
    
    await waitFor(() => {
      expect(screen.getByText('Mocked Button')).toBeInTheDocument();
    });
  });
});

// E2E Testing with Multiple MFEs
// cypress/e2e/micro-frontends.cy.js
describe('Micro-frontend E2E Tests', () => {
  it('loads host and remote MFEs', () => {
    cy.visit('http://localhost:3000');
    
    // Wait for host to load
    cy.contains('Host App').should('be.visible');
    
    // Wait for remote module
    cy.contains('Remote Component', { timeout: 10000 }).should('be.visible');
    
    // Test interaction across MFEs
    cy.get('[data-testid="mfe-button"]').click();
    cy.get('[data-testid="mfe-result"]').should('contain', 'Success');
  });
});

// Isolated MFE Testing
// jest.config.js for individual MFE
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.js'],
  moduleNameMapper: {
    '^@shared/(.*)$': '<rootDir>/../shared/$1',
  },
};

// API Mocking for MFEs
// __mocks__/api.js
export const mockApiResponses = {
  users: [{ id: 1, name: 'John' }],
  posts: [{ id: 1, title: 'Test Post' }],
};

// Contract Testing
// __tests__/contract.test.js
describe('MFE Contract Tests', () => {
  it('exports required modules', () => {
    const MFE1 = require('mfe1/Index');
    
    expect(MFE1.default).toBeDefined();
    expect(typeof MFE1.default).toBe('function');
  });

  it('exposes expected version', () => {
    const { version } = require('mfe1/package.json');
    expect(version).toMatch(/^\d+\.\d+\.\d+$/);
  });
});
```

**Testing Strategies:**
- Unit tests for each MFE
- Integration tests for federation
- E2E tests for cross-MFE flows
- Contract tests for stability
- Mock external modules

---

#### Q135: How do you manage deployments and versioning in micro-frontends?

**Answer:**

```javascript
// 1. Semantic Versioning Strategy
// mfe-header/package.json
{
  "name": "@company/mfe-header",
  "version": "2.1.0",
  "dependencies": {
    "react": "^18.0.0",
    "react-dom": "^18.0.0"
  }
}

// 2. Module Federation with Version Negotiation
// webpack.config.js
const ModuleFederationPlugin = require('webpack/lib/container/ModuleFederationPlugin');

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'mfeHeader',
      filename: 'remoteEntry.js',
      exposes: {
        './Header': './src/Header',
      },
      shared: {
        react: {
          singleton: true,
          requiredVersion: '^18.0.0',
          strictVersion: false, // Allow patch version flexibility
        },
        'react-dom': {
          singleton: true,
          requiredVersion: '^18.0.0',
          strictVersion: false,
        },
      },
    }),
  ],
};

// 3. Deployment Pipeline
// deploy.js
async function deployMFE(mfeName, version) {
  // 1. Build MFE
  await runBuild(mfeName);

  // 2. Version the bundle
  await tagBundleVersion(mfeName, version);

  // 3. Upload to CDN
  await uploadToCDN(mfeName, version);

  // 4. Update manifest
  await updateManifest(mfeName, version);

  // 5. Health check
  const isHealthy = await healthCheck(`https://cdn.example.com/${mfeName}/${version}/remoteEntry.js`);
  
  if (!isHealthy) {
    throw new Error('Deployment failed health check');
  }

  // 6. Rollout gradually
  await gradualRollout(mfeName, version);
}

// 4. Version Resolution at Runtime
// remoteEntryLoader.js
async function loadRemoteEntry(mfeName, versionConstraint) {
  // Fetch available versions
  const versions = await fetch(`/api/versions/${mfeName}`).then(r => r.json());

  // Find compatible version
  const selectedVersion = semver.maxSatisfying(
    versions,
    versionConstraint
  );

  if (!selectedVersion) {
    throw new Error(`No compatible version of ${mfeName} found`);
  }

  // Load the remote entry
  const script = document.createElement('script');
  script.src = `https://cdn.example.com/${mfeName}/${selectedVersion}/remoteEntry.js`;
  document.body.appendChild(script);

  return window[mfeName];
}

// 5. Rollback Strategy
// rollback.js
async function rollbackMFE(mfeName, toVersion) {
  // 1. Update manifest to point to previous version
  await updateManifest(mfeName, toVersion);

  // 2. Notify clients to reload
  await notifyClientsToRefresh(mfeName);

  // 3. Monitor for success
  await monitorMetrics(mfeName, { timeout: 5 * 60 * 1000 });

  console.log(`Rolled back ${mfeName} to version ${toVersion}`);
}

// 6. Canary Deployment
// canary.js
async function canaryDeployment(mfeName, newVersion, percentage = 10) {
  // Route 10% of traffic to new version
  const manifest = await getManifest(mfeName);
  
  manifest.versions = {
    stable: manifest.current,
    canary: newVersion,
  };
  
  manifest.routing = {
    stable: 100 - percentage,
    canary: percentage,
  };

  await updateManifest(mfeName, manifest);

  // Monitor canary metrics
  const errorRate = await monitorErrorRate(newVersion);
  
  if (errorRate < 0.01) { // Less than 1% error
    // Promote canary to stable
    await promoteCanary(mfeName, newVersion);
  } else {
    // Rollback
    await rollbackMFE(mfeName, manifest.current);
  }
}

// 7. Blue-Green Deployment
// blue-green.js
async function blueGreenDeployment(mfeName, newVersion) {
  const manifest = await getManifest(mfeName);
  
  // Current = Blue (stable)
  const blue = manifest.current;
  // New = Green (staging)
  const green = newVersion;

  // Health check green
  const isHealthy = await healthCheckVersion(mfeName, green);
  
  if (!isHealthy) {
    throw new Error('Green environment failed health checks');
  }

  // Switch traffic to green
  manifest.current = green;
  await updateManifest(mfeName, manifest);

  // Keep blue as rollback
  console.log(`Switched from ${blue} to ${green}, keeping ${blue} as rollback`);
}

// 8. Version Monitoring
// monitoring.js
async function monitorVersions() {
  const mfes = ['header', 'sidebar', 'content', 'footer'];

  const versions = await Promise.all(
    mfes.map(name => 
      fetch(`/api/mfe/${name}/current-version`).then(r => r.json())
    )
  );

  console.table(versions);

  // Alert on outdated versions
  versions.forEach(({ name, version }) => {
    if (isOutdated(version)) {
      alertDevOps(`${name} is running outdated version: ${version}`);
    }
  });
}
```

**Best Practices:**
- Use semantic versioning
- Implement canary deployments
- Keep rollback capability
- Health check after deployments
- Monitor version metrics
- Gradual rollouts for safety

---

#### Q136: How do you implement observability and monitoring for micro-frontends?

**Answer:**

```javascript
// 1. Error Tracking
// errorTracking.js
import * as Sentry from "@sentry/react";

Sentry.init({
  dsn: "https://key@sentry.io/project",
  environment: process.env.ENV,
  integrations: [
    new Sentry.Replay({
      maskAllText: true,
      blockAllMedia: true,
    }),
  ],
  tracesSampleRate: 0.1,
});

// Capture MFE-specific errors
window.addEventListener('error', (event) => {
  Sentry.captureException(event.error, {
    tags: {
      'mfe.name': getMFEName(),
      'mfe.version': getMFEVersion(),
    },
  });
});

// 2. Performance Monitoring
// performanceMonitoring.js
function monitorMFEPerformance() {
  // Measure load time
  const startTime = performance.now();
  
  const observer = new PerformanceObserver((list) => {
    list.getEntries().forEach((entry) => {
      if (entry.name.includes('remoteEntry')) {
        const loadTime = entry.duration;
        
        // Report to monitoring service
        reportMetric({
          name: 'mfe_load_time',
          value: loadTime,
          mfe: getMFEName(),
          version: getMFEVersion(),
        });

        // Alert if slow
        if (loadTime > 5000) {
          alertSlowLoad('mfe', getMFEName(), loadTime);
        }
      }
    });
  });

  observer.observe({ entryTypes: ['resource', 'measure'] });
}

// 3. Logging Strategy
// logger.js
class MFELogger {
  constructor(mfeName) {
    this.mfeName = mfeName;
    this.version = getMFEVersion();
  }

  log(level, message, context = {}) {
    const logEntry = {
      timestamp: new Date().toISOString(),
      level,
      message,
      mfe: this.mfeName,
      version: this.version,
      context,
      userAgent: navigator.userAgent,
      url: window.location.href,
    };

    // Send to log aggregator (ELK, DataDog, etc.)
    fetch('/api/logs', {
      method: 'POST',
      body: JSON.stringify(logEntry),
    });
  }

  info(msg, ctx) { this.log('INFO', msg, ctx); }
  warn(msg, ctx) { this.log('WARN', msg, ctx); }
  error(msg, ctx) { this.log('ERROR', msg, ctx); }
}

const logger = new MFELogger('header');
logger.info('Header MFE loaded', { userId: 123 });

// 4. User Session Tracking
// sessionTracking.js
class SessionTracker {
  constructor() {
    this.sessionId = this.generateSessionId();
    this.mfeInteractions = [];
  }

  trackInteraction(mfeName, eventType, data) {
    this.mfeInteractions.push({
      mfe: mfeName,
      event: eventType,
      data,
      timestamp: Date.now(),
      sessionId: this.sessionId,
    });

    // Send in batches
    if (this.mfeInteractions.length >= 10) {
      this.flush();
    }
  }

  flush() {
    fetch('/api/analytics', {
      method: 'POST',
      body: JSON.stringify(this.mfeInteractions),
    });
    this.mfeInteractions = [];
  }

  generateSessionId() {
    return `${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
  }
}

// 5. Custom Metrics
// customMetrics.js
class MetricsCollector {
  static reportMetric(name, value, tags = {}) {
    const metric = {
      name,
      value,
      timestamp: Date.now(),
      tags: {
        ...tags,
        environment: process.env.ENV,
        version: process.env.VERSION,
      },
    };

    // Send to metrics service (Prometheus, CloudWatch, etc.)
    fetch('/api/metrics', {
      method: 'POST',
      body: JSON.stringify(metric),
    });
  }

  static measureFunctionExecution(fn, name) {
    const start = performance.now();
    const result = fn();
    const duration = performance.now() - start;
    
    this.reportMetric(`${name}_duration_ms`, duration);
    return result;
  }
}

// Usage
MetricsCollector.reportMetric('api_call_count', 1, { endpoint: '/users' });

// 6. Real User Monitoring (RUM)
// rum.js
function initializeRUM() {
  // Track Core Web Vitals
  import('web-vitals').then(({ getCLS, getFID, getFCP, getLCP, getTTFB }) => {
    getCLS(metric => reportVital('cls', metric));
    getFID(metric => reportVital('fid', metric));
    getFCP(metric => reportVital('fcp', metric));
    getLCP(metric => reportVital('lcp', metric));
    getTTFB(metric => reportVital('ttfb', metric));
  });
}

function reportVital(name, metric) {
  fetch('/api/vitals', {
    method: 'POST',
    body: JSON.stringify({
      name,
      value: metric.value,
      id: metric.id,
      mfe: getMFEName(),
    }),
  });
}

// 7. Health Check Endpoint
// healthCheck.js
app.get('/health', (req, res) => {
  res.json({
    status: 'healthy',
    mfe: 'header',
    version: process.env.VERSION,
    uptime: process.uptime(),
    dependencies: {
      react: '18.2.0',
      redux: '4.2.0',
    },
    lastCheck: new Date().toISOString(),
  });
});

// 8. Distributed Tracing
// tracing.js
import { trace } from '@opentelemetry/api';

const tracer = trace.getTracer('mfe-header');

function loadRemoteModule(moduleName) {
  const span = tracer.startSpan('load_remote_module');
  
  try {
    span.setAttributes({
      'module.name': moduleName,
      'module.mfe': getMFEName(),
    });

    // Load module
    const module = loadModule(moduleName);
    
    span.addEvent('module_loaded');
    return module;
  } catch (err) {
    span.addEvent('module_load_failed', { 'error': err.message });
    throw err;
  } finally {
    span.end();
  }
}
```

**Monitoring Stack:**
- Error tracking: Sentry/DataDog
- Logging: ELK/Splunk
- Metrics: Prometheus/CloudWatch
- RUM: Web Vitals
- Tracing: OpenTelemetry
- Analytics: Custom events

---

#### Q137: How do you implement authentication and authorization across micro-frontends?

**Answer:**

```javascript
// 1. Shared Authentication Service
// authService.js
class SharedAuthService {
  constructor() {
    this.token = localStorage.getItem('auth_token');
    this.user = this.parseUser();
  }

  async login(email, password) {
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      body: JSON.stringify({ email, password }),
    });

    const { token, user } = await response.json();
    
    // Store in both local storage and shared location
    localStorage.setItem('auth_token', token);
    sessionStorage.setItem('shared_user', JSON.stringify(user));
    
    this.token = token;
    this.user = user;

    // Notify all MFEs about login
    this.broadcastEvent('user:login', user);

    return user;
  }

  async logout() {
    await fetch('/api/auth/logout', {
      method: 'POST',
      headers: { 'Authorization': `Bearer ${this.token}` },
    });

    localStorage.removeItem('auth_token');
    sessionStorage.removeItem('shared_user');
    
    this.token = null;
    this.user = null;

    this.broadcastEvent('user:logout');
  }

  parseUser() {
    const token = this.token;
    if (!token) return null;

    // Decode JWT
    const payload = JSON.parse(atob(token.split('.')[1]));
    return { id: payload.sub, roles: payload.roles };
  }

  broadcastEvent(eventName, data) {
    const event = new CustomEvent(eventName, { detail: data });
    window.dispatchEvent(event);
  }

  hasPermission(requiredRoles) {
    if (!this.user) return false;
    return requiredRoles.some(role => this.user.roles.includes(role));
  }
}

const authService = new SharedAuthService();

// 2. Token Refresh Strategy
// tokenRefresh.js
async function setupTokenRefresh() {
  const interval = setInterval(async () => {
    const token = localStorage.getItem('auth_token');
    const expiry = getTokenExpiry(token);
    const timeUntilExpiry = expiry - Date.now();

    // Refresh if less than 5 minutes left
    if (timeUntilExpiry < 5 * 60 * 1000) {
      try {
        const newToken = await fetch('/api/auth/refresh', {
          method: 'POST',
          headers: { 'Authorization': `Bearer ${token}` },
        }).then(r => r.json()).then(d => d.token);

        localStorage.setItem('auth_token', newToken);
        window.dispatchEvent(new CustomEvent('token:refreshed'));
      } catch (err) {
        authService.logout();
      }
    }
  }, 60000); // Check every minute

  return () => clearInterval(interval);
}

// 3. Role-Based Access Control (RBAC)
// rbac.js
const PERMISSIONS = {
  'admin': ['read', 'write', 'delete', 'manage_users'],
  'editor': ['read', 'write'],
  'viewer': ['read'],
};

function ProtectedComponent({ requiredRole, children }) {
  const user = authService.user;

  if (!user) {
    return <Redirect to="/login" />;
  }

  if (!user.roles.includes(requiredRole)) {
    return <div>Access Denied</div>;
  }

  return children;
}

// Usage
<ProtectedComponent requiredRole="admin">
  <AdminPanel />
</ProtectedComponent>

// 4. Cross-MFE Authorization
// crossMFEAuth.js
async function validateMFEAccess(mfeName) {
  const user = authService.user;
  
  if (!user) return false;

  // Check if user has access to this MFE
  const response = await fetch(`/api/mfes/${mfeName}/access`, {
    headers: { 'Authorization': `Bearer ${authService.token}` },
  });

  return response.ok;
}

// 5. Secure Communication Between MFEs
// secureCommunication.js
class SecureMFEEventBus {
  static emit(eventName, data) {
    // Sign the event
    const signature = this.signEvent(eventName, data);
    
    const event = new CustomEvent(`secure:${eventName}`, {
      detail: { data, signature },
    });
    
    window.dispatchEvent(event);
  }

  static on(eventName, handler) {
    const listener = (event) => {
      if (!this.verifySignature(eventName, event.detail.data, event.detail.signature)) {
        console.error('Invalid event signature');
        return;
      }
      
      handler(event.detail.data);
    };

    window.addEventListener(`secure:${eventName}`, listener);
    return () => window.removeEventListener(`secure:${eventName}`, listener);
  }

  static signEvent(eventName, data) {
    // Use HMAC to sign
    return btoa(JSON.stringify({ eventName, data })); // Simplified
  }

  static verifySignature(eventName, data, signature) {
    // Verify signature
    return this.signEvent(eventName, data) === signature;
  }
}

// 6. SSO Integration (Single Sign-On)
// sso.js
async function initSSO() {
  // Detect if user is already authenticated in other MFE
  const ssoToken = sessionStorage.getItem('sso_token');

  if (ssoToken) {
    // Validate and use SSO token
    const user = await validateSSOToken(ssoToken);
    if (user) {
      localStorage.setItem('auth_token', ssoToken);
      authService.token = ssoToken;
      authService.user = user;
    }
  }
}

// 7. OAuth2 Integration
// oauth.js
function initializeOAuth() {
  const clientId = process.env.REACT_APP_OAUTH_CLIENT_ID;
  const redirectUri = `${window.location.origin}/oauth/callback`;

  const authUrl = `https://auth-provider.com/authorize?client_id=${clientId}&redirect_uri=${redirectUri}`;
  
  // After redirect
  if (window.location.pathname === '/oauth/callback') {
    const code = new URLSearchParams(window.location.search).get('code');
    
    fetch('/api/auth/oauth/token', {
      method: 'POST',
      body: JSON.stringify({ code }),
    })
    .then(r => r.json())
    .then(({ token }) => {
      localStorage.setItem('auth_token', token);
      window.location.href = '/';
    });
  }
}

// 8. API Interceptor with Auth Headers
// apiInterceptor.js
function setupApiInterceptor() {
  const originalFetch = window.fetch;

  window.fetch = function(resource, config) {
    const token = localStorage.getItem('auth_token');

    if (token) {
      config = config || {};
      config.headers = config.headers || {};
      config.headers['Authorization'] = `Bearer ${token}`;
    }

    return originalFetch.apply(this, [resource, config]);
  };
}
```

**Best Practices:**
- Centralize authentication
- Use JWT for stateless auth
- Implement token refresh
- Share auth state via events
- Use HTTPS for all communications
- Validate on both client and server
- Implement proper logout flow

---

#### Q138: How do you handle data consistency and eventual consistency across micro-frontends?

**Answer:**

```javascript
// 1. Event-Driven Architecture for Consistency
// eventBus.js
class EventBus {
  constructor() {
    this.subscribers = {};
  }

  subscribe(event, handler) {
    if (!this.subscribers[event]) {
      this.subscribers[event] = [];
    }
    this.subscribers[event].push(handler);

    return () => {
      this.subscribers[event] = this.subscribers[event].filter(h => h !== handler);
    };
  }

  publish(event, data) {
    if (this.subscribers[event]) {
      this.subscribers[event].forEach(handler => {
        try {
          handler(data);
        } catch (err) {
          console.error(`Error in event handler for ${event}:`, err);
        }
      });
    }
  }
}

const eventBus = new EventBus();

// 2. Using Optimistic Updates
// optimisticUpdates.js
async function updateUserWithOptimism(userId, newData) {
  const previousData = getUserData(userId);

  // 1. Update UI immediately (optimistic)
  updateUIData(userId, newData);

  try {
    // 2. Send to server
    const response = await fetch(`/api/users/${userId}`, {
      method: 'PUT',
      body: JSON.stringify(newData),
    });

    if (!response.ok) throw new Error('Update failed');

    // 3. Emit event for other MFEs
    eventBus.publish('user:updated', { userId, newData });

    // 4. Server confirms update
    const confirmedData = await response.json();
    updateUIData(userId, confirmedData);

  } catch (err) {
    // Revert on error
    updateUIData(userId, previousData);
    eventBus.publish('user:update-failed', { userId, error: err.message });
  }
}

// 3. Handling Eventual Consistency
// consistencyHandler.js
class ConsistencyManager {
  constructor() {
    this.pendingUpdates = new Map();
    this.retryAttempts = 3;
  }

  async ensureConsistency(key, updateFn) {
    const maxRetries = this.retryAttempts;
    let attempt = 0;

    while (attempt < maxRetries) {
      try {
        const result = await updateFn();
        this.pendingUpdates.delete(key);
        return result;
      } catch (err) {
        attempt++;
        if (attempt < maxRetries) {
          // Exponential backoff
          await new Promise(resolve =>
            setTimeout(resolve, Math.pow(2, attempt) * 1000)
          );
        } else {
          // Store for later retry
          this.pendingUpdates.set(key, { updateFn, timestamp: Date.now() });
          throw err;
        }
      }
    }
  }

  async retryPendingUpdates() {
    for (const [key, { updateFn }] of this.pendingUpdates.entries()) {
      try {
        await updateFn();
        this.pendingUpdates.delete(key);
      } catch (err) {
        console.error(`Failed to retry update ${key}:`, err);
      }
    }
  }
}

// 4. Cache Invalidation Strategy
// cacheManager.js
class CacheManager {
  constructor() {
    this.cache = new Map();
    this.timestamps = new Map();
    this.TTL = 5 * 60 * 1000; // 5 minutes
  }

  set(key, value) {
    this.cache.set(key, value);
    this.timestamps.set(key, Date.now());
  }

  get(key) {
    const timestamp = this.timestamps.get(key);
    const isExpired = Date.now() - timestamp > this.TTL;

    if (isExpired) {
      this.invalidate(key);
      return null;
    }

    return this.cache.get(key);
  }

  invalidate(key) {
    this.cache.delete(key);
    this.timestamps.delete(key);

    // Notify other MFEs about cache invalidation
    eventBus.publish('cache:invalidated', { key });
  }

  invalidatePattern(pattern) {
    for (const key of this.cache.keys()) {
      if (new RegExp(pattern).test(key)) {
        this.invalidate(key);
      }
    }
  }
}

// 5. Conflict Resolution
// conflictResolver.js
class ConflictResolver {
  static resolveConflict(localVersion, remoteVersion) {
    // Last-write-wins strategy
    if (remoteVersion.timestamp > localVersion.timestamp) {
      return remoteVersion;
    }
    return localVersion;
  }

  static merge(localData, remoteData) {
    // Deep merge with conflict detection
    return {
      ...localData,
      ...remoteData,
      conflicts: this.detectConflicts(localData, remoteData),
    };
  }

  static detectConflicts(local, remote) {
    const conflicts = [];
    
    for (const key in local) {
      if (remote[key] && local[key] !== remote[key]) {
        conflicts.push({
          key,
          localValue: local[key],
          remoteValue: remote[key],
        });
      }
    }

    return conflicts;
  }
}

// 6. Distributed Transactions
// distributedTransactions.js
class DistributedTransaction {
  constructor() {
    this.steps = [];
    this.results = [];
  }

  addStep(name, operation) {
    this.steps.push({ name, operation });
  }

  async execute() {
    const completedSteps = [];

    try {
      for (const { name, operation } of this.steps) {
        const result = await operation();
        this.results.push({ step: name, result });
        completedSteps.push(name);
      }

      return { success: true, results: this.results };
    } catch (err) {
      // Rollback on failure
      await this.rollback(completedSteps);
      throw err;
    }
  }

  async rollback(completedSteps) {
    console.log('Rolling back transaction. Completed steps:', completedSteps);
    
    for (const step of completedSteps.reverse()) {
      try {
        // Implement rollback logic
        await this.executeRollback(step);
      } catch (err) {
        console.error(`Failed to rollback ${step}:`, err);
      }
    }
  }

  async executeRollback(step) {
    // Call compensation transaction
    eventBus.publish('transaction:rollback', { step });
  }
}

// Usage
const transaction = new DistributedTransaction();
transaction.addStep('updateUser', () => updateUser());
transaction.addStep('updateSettings', () => updateSettings());
transaction.addStep('notifyOtherMFEs', () => notifyChanges());

await transaction.execute();

// 7. Heartbeat / Health Check Pattern
// healthCheck.js
function startHealthCheckLoop() {
  setInterval(async () => {
    const mfes = ['header', 'sidebar', 'content'];

    const results = await Promise.all(
      mfes.map(mfe => checkMFEHealth(mfe))
    );

    results.forEach(({ mfe, isHealthy }) => {
      if (!isHealthy) {
        eventBus.publish('mfe:unhealthy', { mfe });
      }
    });
  }, 30000); // Every 30 seconds
}

async function checkMFEHealth(mfeName) {
  try {
    const response = await fetch(`/api/mfe/${mfeName}/health`);
    return { mfe: mfeName, isHealthy: response.ok };
  } catch {
    return { mfe: mfeName, isHealthy: false };
  }
}
```

**Consistency Strategies:**
- Event-driven updates
- Optimistic UI updates with rollback
- Eventual consistency with retries
- Cache invalidation patterns
- Conflict resolution
- Distributed transactions
- Health checks for synchronization

---

## Interview Tips for Confidence & Authenticity

| Area | Focus |
|------|-------|
| **JavaScript** | Event loop, async/await, closures, prototypes |
| **TypeScript** | Interfaces, generics, type guards, enums |
| **React Hooks** | useState, useEffect, custom hooks, performance optimization |
| **State Management** | Context API vs Redux, choosing right tool |
| **Component Design** | Reusability, composition, separation of concerns |
| **Performance** | useMemo, useCallback, code splitting, lazy loading |
| **Micro-frontends** | Module Federation, event-based communication, shared state |
| **Best Practices** | Error handling, accessibility, testing, security |

---

Good luck with your interview! Remember: **Clarity, Confidence, and Clean Code** 🚀

