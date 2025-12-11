# Redux

Master Redux for predictable state management in large-scale React applications.

## 📚 Topics Covered

### 1. Redux Fundamentals
- What is Redux?
- Core principles
- Store, Actions, Reducers
- Redux data flow
- When to use Redux

### 2. Redux Toolkit (Recommended)
- configureStore
- createSlice
- createAsyncThunk
- RTK Query basics

### 3. React-Redux Integration
- Provider component
- useSelector hook
- useDispatch hook
- Connect function (legacy)

### 4. Advanced Concepts
- Middleware
- Redux Thunk for async
- Redux DevTools
- Normalizing state
- Selectors and Reselect

## 🚀 Installation

```bash
# Redux Toolkit (recommended)
npm install @reduxjs/toolkit react-redux

# Or classic Redux
npm install redux react-redux
```

## Code Examples

### Redux Toolkit - Modern Approach

#### 1. Create Store
```jsx
// store.js
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './features/counterSlice';
import userReducer from './features/userSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    user: userReducer
  }
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

#### 2. Create Slice
```jsx
// features/counterSlice.js
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0,
    history: []
  },
  reducers: {
    increment: (state) => {
      state.value += 1;
      state.history.push(state.value);
    },
    decrement: (state) => {
      state.value -= 1;
      state.history.push(state.value);
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
      state.history.push(state.value);
    },
    reset: (state) => {
      state.value = 0;
      state.history = [];
    }
  }
});

export const { increment, decrement, incrementByAmount, reset } = counterSlice.actions;
export default counterSlice.reducer;
```

#### 3. Setup Provider
```jsx
// index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';
import { store } from './store';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

#### 4. Use in Components
```jsx
// Counter.jsx
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement, incrementByAmount, reset } from './features/counterSlice';

function Counter() {
  const count = useSelector((state) => state.counter.value);
  const history = useSelector((state) => state.counter.history);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => dispatch(increment())}>+1</button>
      <button onClick={() => dispatch(decrement())}>-1</button>
      <button onClick={() => dispatch(incrementByAmount(5))}>+5</button>
      <button onClick={() => dispatch(reset())}>Reset</button>
      
      <div>
        <h3>History:</h3>
        <ul>
          {history.map((val, idx) => <li key={idx}>{val}</li>)}
        </ul>
      </div>
    </div>
  );
}
```

### Async Operations with createAsyncThunk
```jsx
// features/userSlice.js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

// Async thunk
export const fetchUserById = createAsyncThunk(
  'user/fetchById',
  async (userId, { rejectWithValue }) => {
    try {
      const response = await fetch(`/api/users/${userId}`);
      const data = await response.json();
      return data;
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);

const userSlice = createSlice({
  name: 'user',
  initialState: {
    user: null,
    loading: false,
    error: null
  },
  reducers: {
    clearUser: (state) => {
      state.user = null;
      state.error = null;
    }
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchUserById.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchUserById.fulfilled, (state, action) => {
        state.loading = false;
        state.user = action.payload;
      })
      .addCase(fetchUserById.rejected, (state, action) => {
        state.loading = false;
        state.error = action.payload;
      });
  }
});

export const { clearUser } = userSlice.actions;
export default userSlice.reducer;
```

### Using Async Actions
```jsx
import { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { fetchUserById } from './features/userSlice';

function UserProfile({ userId }) {
  const { user, loading, error } = useSelector((state) => state.user);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchUserById(userId));
  }, [dispatch, userId]);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  if (!user) return null;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### Todo App with Redux Toolkit
```jsx
// features/todosSlice.js
import { createSlice, nanoid } from '@reduxjs/toolkit';

const todosSlice = createSlice({
  name: 'todos',
  initialState: {
    items: [],
    filter: 'all' // all, active, completed
  },
  reducers: {
    addTodo: {
      reducer: (state, action) => {
        state.items.push(action.payload);
      },
      prepare: (text) => {
        return {
          payload: {
            id: nanoid(),
            text,
            completed: false,
            createdAt: new Date().toISOString()
          }
        };
      }
    },
    toggleTodo: (state, action) => {
      const todo = state.items.find(item => item.id === action.payload);
      if (todo) {
        todo.completed = !todo.completed;
      }
    },
    deleteTodo: (state, action) => {
      state.items = state.items.filter(item => item.id !== action.payload);
    },
    setFilter: (state, action) => {
      state.filter = action.payload;
    },
    clearCompleted: (state) => {
      state.items = state.items.filter(item => !item.completed);
    }
  }
});

export const { addTodo, toggleTodo, deleteTodo, setFilter, clearCompleted } = todosSlice.actions;

// Selectors
export const selectFilteredTodos = (state) => {
  const { items, filter } = state.todos;
  switch (filter) {
    case 'active':
      return items.filter(todo => !todo.completed);
    case 'completed':
      return items.filter(todo => todo.completed);
    default:
      return items;
  }
};

export const selectTodoStats = (state) => {
  const items = state.todos.items;
  return {
    total: items.length,
    active: items.filter(t => !t.completed).length,
    completed: items.filter(t => t.completed).length
  };
};

export default todosSlice.reducer;
```

### Todo Component
```jsx
import { useState } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import {
  addTodo,
  toggleTodo,
  deleteTodo,
  setFilter,
  clearCompleted,
  selectFilteredTodos,
  selectTodoStats
} from './features/todosSlice';

function TodoApp() {
  const [input, setInput] = useState('');
  const todos = useSelector(selectFilteredTodos);
  const stats = useSelector(selectTodoStats);
  const filter = useSelector((state) => state.todos.filter);
  const dispatch = useDispatch();

  const handleSubmit = (e) => {
    e.preventDefault();
    if (input.trim()) {
      dispatch(addTodo(input));
      setInput('');
    }
  };

  return (
    <div>
      <h1>Todo App</h1>
      
      <form onSubmit={handleSubmit}>
        <input
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="Add todo..."
        />
        <button type="submit">Add</button>
      </form>

      <div>
        <button onClick={() => dispatch(setFilter('all'))}>
          All ({stats.total})
        </button>
        <button onClick={() => dispatch(setFilter('active'))}>
          Active ({stats.active})
        </button>
        <button onClick={() => dispatch(setFilter('completed'))}>
          Completed ({stats.completed})
        </button>
        <button onClick={() => dispatch(clearCompleted())}>
          Clear Completed
        </button>
      </div>

      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => dispatch(toggleTodo(todo.id))}
            />
            <span style={{ 
              textDecoration: todo.completed ? 'line-through' : 'none' 
            }}>
              {todo.text}
            </span>
            <button onClick={() => dispatch(deleteTodo(todo.id))}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### Redux DevTools
```jsx
// Already integrated in Redux Toolkit
// For classic Redux:
import { createStore } from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(
  rootReducer,
  composeWithDevTools()
);
```

## 💡 Key Concepts

### Three Principles of Redux
1. **Single source of truth**: One store for entire app
2. **State is read-only**: Only change via actions
3. **Changes made with pure functions**: Reducers are pure

### Redux vs Context API
**Use Redux when**:
- Complex state logic
- Many components need state
- State updates frequently
- Need time-travel debugging
- Large team with established patterns

**Use Context when**:
- Simple global state
- Infrequent updates
- Small/medium apps
- No complex middleware needed

### Best Practices
1. Use Redux Toolkit (not classic Redux)
2. Normalize state shape
3. Keep state minimal
4. Use selectors for derived data
5. Name actions descriptively
6. Handle loading and error states

## 📝 Practice Exercises

1. **Counter with History**: Build counter tracking all changes
2. **Todo App**: Full CRUD todo with filters
3. **Shopping Cart**: E-commerce cart with Redux
4. **User Authentication**: Login/logout state management
5. **Async Data Fetching**: Fetch and display API data
6. **Undo/Redo**: Implement undo/redo functionality

## 🔗 Resources

- [Redux Toolkit Documentation](https://redux-toolkit.js.org/)
- [Redux Official Tutorial](https://redux.js.org/tutorials/essentials/part-1-overview-concepts)
- [React-Redux Hooks](https://react-redux.js.org/api/hooks)
- [Redux DevTools Extension](https://github.com/reduxjs/redux-devtools)

---

**Next**: Build real projects in [09-Projects](../09-Projects/) to consolidate your learning!
