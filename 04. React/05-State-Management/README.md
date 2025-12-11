# State Management

Master advanced state management patterns with Context API and useReducer.

## 📚 Topics Covered

### 1. Context API
- What is Context API?
- Creating context
- Provider and Consumer
- useContext hook
- When to use Context

### 2. useReducer Hook
- What is useReducer?
- Reducer function pattern
- Actions and action types
- Complex state logic
- useReducer vs useState

### 3. Combining Context + useReducer
- Global state management
- Custom hooks for context
- Best practices
- Performance considerations

### 4. State Management Patterns
- Prop drilling problem
- State lifting
- Component composition
- State colocation

## 🚀 Code Examples

### Context API Basics
```jsx
import { createContext, useContext, useState } from 'react';

// Create Context
const ThemeContext = createContext();

// Provider Component
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Custom Hook
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}

// Usage
function App() {
  return (
    <ThemeProvider>
      <Header />
      <Main />
    </ThemeProvider>
  );
}

function Header() {
  const { theme, toggleTheme } = useTheme();
  return (
    <header style={{ background: theme === 'light' ? '#fff' : '#333' }}>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </header>
  );
}
```

### useReducer Hook
```jsx
import { useReducer } from 'react';

// Action types
const ACTIONS = {
  INCREMENT: 'increment',
  DECREMENT: 'decrement',
  RESET: 'reset',
  SET: 'set'
};

// Reducer function
function counterReducer(state, action) {
  switch (action.type) {
    case ACTIONS.INCREMENT:
      return { count: state.count + 1 };
    case ACTIONS.DECREMENT:
      return { count: state.count - 1 };
    case ACTIONS.RESET:
      return { count: 0 };
    case ACTIONS.SET:
      return { count: action.payload };
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}

function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: ACTIONS.INCREMENT })}>
        +1
      </button>
      <button onClick={() => dispatch({ type: ACTIONS.DECREMENT })}>
        -1
      </button>
      <button onClick={() => dispatch({ type: ACTIONS.RESET })}>
        Reset
      </button>
      <button onClick={() => dispatch({ type: ACTIONS.SET, payload: 10 })}>
        Set to 10
      </button>
    </div>
  );
}
```

### Complex State with useReducer
```jsx
import { useReducer } from 'react';

const initialState = {
  todos: [],
  filter: 'all' // all, active, completed
};

const ACTIONS = {
  ADD_TODO: 'add_todo',
  TOGGLE_TODO: 'toggle_todo',
  DELETE_TODO: 'delete_todo',
  SET_FILTER: 'set_filter'
};

function todoReducer(state, action) {
  switch (action.type) {
    case ACTIONS.ADD_TODO:
      return {
        ...state,
        todos: [...state.todos, {
          id: Date.now(),
          text: action.payload,
          completed: false
        }]
      };
    
    case ACTIONS.TOGGLE_TODO:
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload
            ? { ...todo, completed: !todo.completed }
            : todo
        )
      };
    
    case ACTIONS.DELETE_TODO:
      return {
        ...state,
        todos: state.todos.filter(todo => todo.id !== action.payload)
      };
    
    case ACTIONS.SET_FILTER:
      return { ...state, filter: action.payload };
    
    default:
      return state;
  }
}

function TodoApp() {
  const [state, dispatch] = useReducer(todoReducer, initialState);
  const [input, setInput] = useState('');

  const filteredTodos = state.todos.filter(todo => {
    if (state.filter === 'active') return !todo.completed;
    if (state.filter === 'completed') return todo.completed;
    return true;
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    if (input.trim()) {
      dispatch({ type: ACTIONS.ADD_TODO, payload: input });
      setInput('');
    }
  };

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="Add todo..."
        />
        <button type="submit">Add</button>
      </form>

      <div>
        <button onClick={() => dispatch({ type: ACTIONS.SET_FILTER, payload: 'all' })}>
          All
        </button>
        <button onClick={() => dispatch({ type: ACTIONS.SET_FILTER, payload: 'active' })}>
          Active
        </button>
        <button onClick={() => dispatch({ type: ACTIONS.SET_FILTER, payload: 'completed' })}>
          Completed
        </button>
      </div>

      <ul>
        {filteredTodos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => dispatch({ type: ACTIONS.TOGGLE_TODO, payload: todo.id })}
            />
            <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
              {todo.text}
            </span>
            <button onClick={() => dispatch({ type: ACTIONS.DELETE_TODO, payload: todo.id })}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### Context + useReducer (Global State)
```jsx
import { createContext, useContext, useReducer } from 'react';

// Context
const AuthContext = createContext();

// Actions
const AUTH_ACTIONS = {
  LOGIN: 'login',
  LOGOUT: 'logout',
  UPDATE_USER: 'update_user'
};

// Reducer
function authReducer(state, action) {
  switch (action.type) {
    case AUTH_ACTIONS.LOGIN:
      return {
        user: action.payload,
        isAuthenticated: true
      };
    case AUTH_ACTIONS.LOGOUT:
      return {
        user: null,
        isAuthenticated: false
      };
    case AUTH_ACTIONS.UPDATE_USER:
      return {
        ...state,
        user: { ...state.user, ...action.payload }
      };
    default:
      return state;
  }
}

// Provider
function AuthProvider({ children }) {
  const [state, dispatch] = useReducer(authReducer, {
    user: null,
    isAuthenticated: false
  });

  const login = (userData) => {
    dispatch({ type: AUTH_ACTIONS.LOGIN, payload: userData });
  };

  const logout = () => {
    dispatch({ type: AUTH_ACTIONS.LOGOUT });
  };

  const updateUser = (updates) => {
    dispatch({ type: AUTH_ACTIONS.UPDATE_USER, payload: updates });
  };

  return (
    <AuthContext.Provider value={{ ...state, login, logout, updateUser }}>
      {children}
    </AuthContext.Provider>
  );
}

// Custom Hook
function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}

// Usage
function App() {
  return (
    <AuthProvider>
      <Dashboard />
    </AuthProvider>
  );
}

function Dashboard() {
  const { user, isAuthenticated, login, logout } = useAuth();

  if (!isAuthenticated) {
    return (
      <button onClick={() => login({ name: 'Rahul', email: 'rahul@example.com' })}>
        Login
      </button>
    );
  }

  return (
    <div>
      <h1>Welcome, {user.name}!</h1>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```

## 💡 Key Concepts

### When to Use Context API
- Theme settings
- User authentication
- Language/localization
- Shopping cart
- Shared UI state

### When to Use useReducer
- Complex state logic
- Multiple sub-values
- Next state depends on previous
- State transitions are complex

### Performance Tips
1. Split contexts by frequency of change
2. Memoize context values
3. Use multiple contexts instead of one large context
4. Consider state colocation

### Best Practices
1. Create custom hooks for context
2. Validate context usage
3. Use action types constants
4. Keep reducers pure
5. Document state shape

## 📝 Practice Exercises

1. **Theme Manager**: Build a theme system with multiple themes
2. **Shopping Cart**: Create a shopping cart with Context + useReducer
3. **Auth System**: Implement login/logout with protected routes
4. **Multi-language**: Build a language switcher with Context
5. **Notification System**: Create a global notification manager
6. **Form Wizard**: Multi-step form with global state

## 🔗 Resources

- [Context API](https://react.dev/reference/react/useContext)
- [useReducer Hook](https://react.dev/reference/react/useReducer)
- [Passing Data Deeply with Context](https://react.dev/learn/passing-data-deeply-with-context)
- [Extracting State Logic into a Reducer](https://react.dev/learn/extracting-state-logic-into-a-reducer)

---

**Next**: Move to [06-Advanced-Concepts](../06-Advanced-Concepts/) for performance optimization and custom hooks!
