# Advanced React Concepts

Master advanced React patterns, performance optimization, and custom hooks.

## 📚 Topics Covered

### 1. Custom Hooks
- Creating reusable hooks
- Hook composition
- Common custom hook patterns
- Testing custom hooks

### 2. Performance Optimization
- React.memo
- useMemo hook
- useCallback hook
- Code splitting with lazy and Suspense
- Profiling React apps

### 3. Error Boundaries
- Error boundary components
- Handling errors gracefully
- Error logging

### 4. Advanced Patterns
- Higher-Order Components (HOC)
- Render Props
- Compound Components
- Controlled vs Uncontrolled components

### 5. Refs and DOM Manipulation
- useImperativeHandle
- forwardRef
- DOM measurements

## 🚀 Code Examples

### Custom Hooks
```jsx
import { useState, useEffect } from 'react';

// useFetch custom hook
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let isCancelled = false;

    fetch(url)
      .then(res => res.json())
      .then(data => {
        if (!isCancelled) {
          setData(data);
          setLoading(false);
        }
      })
      .catch(err => {
        if (!isCancelled) {
          setError(err);
          setLoading(false);
        }
      });

    return () => {
      isCancelled = true;
    };
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;
  return <div>{user.name}</div>;
}

// useLocalStorage custom hook
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  const setStoredValue = (newValue) => {
    try {
      setValue(newValue);
      localStorage.setItem(key, JSON.stringify(newValue));
    } catch (error) {
      console.error(error);
    }
  };

  return [value, setStoredValue];
}

// Usage
function Settings() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Current: {theme}
    </button>
  );
}

// useToggle custom hook
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);
  const toggle = () => setValue(prev => !prev);
  return [value, toggle];
}

// Usage
function Modal() {
  const [isOpen, toggleModal] = useToggle(false);
  return (
    <div>
      <button onClick={toggleModal}>Open Modal</button>
      {isOpen && <div>Modal Content</div>}
    </div>
  );
}
```

### Performance Optimization
```jsx
import { memo, useMemo, useCallback } from 'react';

// React.memo - prevents unnecessary re-renders
const ExpensiveComponent = memo(function ExpensiveComponent({ data }) {
  console.log('ExpensiveComponent rendered');
  return <div>{data}</div>;
});

// useMemo - memoizes computed values
function SearchList({ items, query }) {
  const filteredItems = useMemo(() => {
    console.log('Filtering items...');
    return items.filter(item => 
      item.name.toLowerCase().includes(query.toLowerCase())
    );
  }, [items, query]); // Only recompute when items or query changes

  return (
    <ul>
      {filteredItems.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}

// useCallback - memoizes function references
function TodoList() {
  const [todos, setTodos] = useState([]);

  // Without useCallback, this function is recreated on every render
  const handleToggle = useCallback((id) => {
    setTodos(prev => prev.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  }, []); // Empty deps - function never changes

  return (
    <ul>
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} onToggle={handleToggle} />
      ))}
    </ul>
  );
}

const TodoItem = memo(({ todo, onToggle }) => {
  console.log(`TodoItem ${todo.id} rendered`);
  return (
    <li onClick={() => onToggle(todo.id)}>
      {todo.text}
    </li>
  );
});
```

### Code Splitting and Lazy Loading
```jsx
import { lazy, Suspense } from 'react';

// Lazy load components
const Dashboard = lazy(() => import('./Dashboard'));
const Settings = lazy(() => import('./Settings'));

function App() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/settings" element={<Settings />} />
        </Routes>
      </Suspense>
    </div>
  );
}

// Lazy load with error boundary
function LazyLoadedApp() {
  return (
    <ErrorBoundary>
      <Suspense fallback={<LoadingSpinner />}>
        <Dashboard />
      </Suspense>
    </ErrorBoundary>
  );
}
```

### Error Boundaries
```jsx
import { Component } from 'react';

class ErrorBoundary extends Component {
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
  }

  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h1>Something went wrong</h1>
          <p>{this.state.error?.message}</p>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}

// Usage
function App() {
  return (
    <ErrorBoundary>
      <MyComponent />
    </ErrorBoundary>
  );
}
```

### forwardRef and useImperativeHandle
```jsx
import { forwardRef, useRef, useImperativeHandle } from 'react';

// forwardRef - pass ref to child component
const Input = forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});

function Form() {
  const inputRef = useRef();

  const handleSubmit = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <Input ref={inputRef} />
      <button onClick={handleSubmit}>Focus Input</button>
    </div>
  );
}

// useImperativeHandle - customize ref value
const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
    clear: () => { inputRef.current.value = ''; },
    getValue: () => inputRef.current.value
  }));

  return <input ref={inputRef} {...props} />;
});

function ParentComponent() {
  const fancyInputRef = useRef();

  return (
    <div>
      <FancyInput ref={fancyInputRef} />
      <button onClick={() => fancyInputRef.current.focus()}>Focus</button>
      <button onClick={() => fancyInputRef.current.clear()}>Clear</button>
      <button onClick={() => alert(fancyInputRef.current.getValue())}>
        Get Value
      </button>
    </div>
  );
}
```

### Higher-Order Components (HOC)
```jsx
// HOC for authentication
function withAuth(Component) {
  return function AuthenticatedComponent(props) {
    const { isAuthenticated } = useAuth();

    if (!isAuthenticated) {
      return <Redirect to="/login" />;
    }

    return <Component {...props} />;
  };
}

// Usage
const ProtectedDashboard = withAuth(Dashboard);

// HOC for loading state
function withLoading(Component) {
  return function LoadingComponent({ isLoading, ...props }) {
    if (isLoading) {
      return <LoadingSpinner />;
    }
    return <Component {...props} />;
  };
}

const UserListWithLoading = withLoading(UserList);
```

## 💡 Key Concepts

### When to Optimize
- Profile first, optimize later
- Focus on actual bottlenecks
- Don't premature optimize
- Measure impact of optimizations

### Custom Hooks Best Practices
1. Start with "use" prefix
2. Keep them focused and reusable
3. Return arrays for 2 values, objects for 3+
4. Document dependencies and side effects

### Memo vs useMemo vs useCallback
- **React.memo**: Prevents component re-renders
- **useMemo**: Memoizes computed values
- **useCallback**: Memoizes function references

## 📝 Practice Exercises

1. **useDebounce Hook**: Create a debounce custom hook
2. **useWindowSize Hook**: Track window dimensions
3. **usePrevious Hook**: Get previous prop/state value
4. **Optimize List**: Optimize a large list rendering
5. **Error Boundary**: Implement comprehensive error handling
6. **useAsync Hook**: Handle async operations with loading/error states

## 🔗 Resources

- [Custom Hooks](https://react.dev/learn/reusing-logic-with-custom-hooks)
- [Performance Optimization](https://react.dev/reference/react/memo)
- [Code Splitting](https://react.dev/reference/react/lazy)
- [Error Boundaries](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary)
- [React Profiler](https://react.dev/learn/react-developer-tools)

---

**Next**: Continue to [07-React-Router](../07-React-Router/) to build single-page applications!
