# React Core Concepts

Master the fundamental concepts of React including state, events, and rendering.

## 📚 Topics Covered

### 1. State Management
- useState hook
- State updates and re-renders
- Multiple state variables
- State with objects and arrays
- Immutable state updates

### 2. Event Handling
- onClick, onChange, onSubmit events
- Event object and preventDefault
- Passing arguments to event handlers
- Synthetic events in React

### 3. Conditional Rendering
- if-else statements
- Ternary operators
- Logical && operator
- Conditional CSS classes

### 4. Lists and Keys
- Rendering arrays with map()
- Key prop importance
- Filtering and sorting lists
- Dynamic list rendering

### 5. Component Lifecycle
- useEffect hook
- Effect dependencies
- Cleanup functions
- Common use cases

## 🚀 Code Examples

### useState Hook
```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

### Event Handling
```jsx
function Form() {
  const [name, setName] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Hello, ${name}!`);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Enter your name"
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Conditional Rendering
```jsx
function Greeting({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? (
        <h1>Welcome back!</h1>
      ) : (
        <h1>Please sign in.</h1>
      )}
    </div>
  );
}

// Using && operator
function Notification({ messages }) {
  return (
    <div>
      {messages.length > 0 && (
        <p>You have {messages.length} new messages</p>
      )}
    </div>
  );
}
```

### Lists and Keys
```jsx
function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: 'Learn React', completed: false },
    { id: 2, text: 'Build a project', completed: false },
    { id: 3, text: 'Master hooks', completed: true }
  ]);

  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id} style={{
          textDecoration: todo.completed ? 'line-through' : 'none'
        }}>
          {todo.text}
        </li>
      ))}
    </ul>
  );
}
```

### useEffect Hook
```jsx
import { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    // Cleanup function
    return () => clearInterval(interval);
  }, []); // Empty dependency array - runs once

  return <p>Elapsed time: {seconds}s</p>;
}

// Effect with dependencies
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`https://api.example.com/users/${userId}`)
      .then(res => res.json())
      .then(data => setUser(data));
  }, [userId]); // Runs when userId changes

  if (!user) return <p>Loading...</p>;
  return <div>{user.name}</div>;
}
```

## 💡 Key Concepts

### State Updates
- State updates are asynchronous
- Use functional updates for state based on previous state
- Never mutate state directly
- Create new arrays/objects for state updates

### Keys in Lists
- Keys help React identify which items changed
- Use unique, stable IDs (not array index)
- Keys must be unique among siblings

### useEffect Dependencies
- `[]` - runs once on mount
- `[dep1, dep2]` - runs when dependencies change
- No array - runs after every render

## 📝 Practice Exercises

1. **Toggle Component**: Create a component that toggles between two states
2. **Todo App**: Build a todo list with add, delete, and toggle complete
3. **Search Filter**: Implement a searchable list of items
4. **Form Validation**: Create a form with validation and error messages
5. **Fetch Data**: Build a component that fetches and displays API data
6. **Timer App**: Create a stopwatch with start, pause, and reset

## 🔗 Resources

- [React Hooks Reference](https://react.dev/reference/react)
- [useState Hook](https://react.dev/reference/react/useState)
- [useEffect Hook](https://react.dev/reference/react/useEffect)
- [Handling Events](https://react.dev/learn/responding-to-events)
- [Rendering Lists](https://react.dev/learn/rendering-lists)

---

**Next**: Continue to [04-Forms-and-Refs](../04-Forms-and-Refs/) to master form handling in React!
