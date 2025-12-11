# Forms and Refs

Learn to handle forms effectively and use refs for direct DOM manipulation.

## 📚 Topics Covered

### 1. Controlled Components
- Input fields with state
- Textarea and select elements
- Multiple input handling
- Form state management

### 2. Form Validation
- Client-side validation
- Error messages
- Validation on submit vs onChange
- Custom validation logic

### 3. useRef Hook
- Creating refs
- Accessing DOM elements
- Uncontrolled components
- Focus management
- Previous value tracking

### 4. Form Libraries
- Introduction to Formik
- React Hook Form basics
- When to use form libraries

## 🚀 Code Examples

### Controlled Form
```jsx
import { useState } from 'react';

function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted:', formData);
    // Reset form
    setFormData({ name: '', email: '', message: '' });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="name"
        value={formData.name}
        onChange={handleChange}
        placeholder="Name"
      />
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <textarea
        name="message"
        value={formData.message}
        onChange={handleChange}
        placeholder="Message"
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Form Validation
```jsx
import { useState } from 'react';

function SignupForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: ''
  });
  const [errors, setErrors] = useState({});

  const validate = () => {
    const newErrors = {};

    if (!formData.username || formData.username.length < 3) {
      newErrors.username = 'Username must be at least 3 characters';
    }

    if (!formData.email || !/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = 'Email is invalid';
    }

    if (!formData.password || formData.password.length < 6) {
      newErrors.password = 'Password must be at least 6 characters';
    }

    return newErrors;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const validationErrors = validate();
    
    if (Object.keys(validationErrors).length === 0) {
      console.log('Form is valid:', formData);
      // Submit form
    } else {
      setErrors(validationErrors);
    }
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => ({ ...prev, [name]: '' }));
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleChange}
          placeholder="Username"
        />
        {errors.username && <span className="error">{errors.username}</span>}
      </div>
      <div>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          placeholder="Email"
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>
      <div>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          placeholder="Password"
        />
        {errors.password && <span className="error">{errors.password}</span>}
      </div>
      <button type="submit">Sign Up</button>
    </form>
  );
}
```

### useRef Hook
```jsx
import { useRef, useEffect } from 'react';

// Focus management
function SearchInput() {
  const inputRef = useRef(null);

  useEffect(() => {
    // Focus input on component mount
    inputRef.current.focus();
  }, []);

  return (
    <input
      ref={inputRef}
      type="text"
      placeholder="Search..."
    />
  );
}

// Uncontrolled component
function UncontrolledForm() {
  const nameRef = useRef(null);
  const emailRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    const name = nameRef.current.value;
    const email = emailRef.current.value;
    console.log({ name, email });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={nameRef} type="text" placeholder="Name" />
      <input ref={emailRef} type="email" placeholder="Email" />
      <button type="submit">Submit</button>
    </form>
  );
}

// Tracking previous value
function Counter() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();

  useEffect(() => {
    prevCountRef.current = count;
  }, [count]);

  const prevCount = prevCountRef.current;

  return (
    <div>
      <p>Current: {count}</p>
      <p>Previous: {prevCount}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### Checkbox and Radio Buttons
```jsx
function Preferences() {
  const [preferences, setPreferences] = useState({
    newsletter: false,
    notifications: true,
    theme: 'light'
  });

  const handleCheckbox = (e) => {
    setPreferences(prev => ({
      ...prev,
      [e.target.name]: e.target.checked
    }));
  };

  const handleRadio = (e) => {
    setPreferences(prev => ({
      ...prev,
      theme: e.target.value
    }));
  };

  return (
    <div>
      <label>
        <input
          type="checkbox"
          name="newsletter"
          checked={preferences.newsletter}
          onChange={handleCheckbox}
        />
        Subscribe to newsletter
      </label>
      
      <label>
        <input
          type="checkbox"
          name="notifications"
          checked={preferences.notifications}
          onChange={handleCheckbox}
        />
        Enable notifications
      </label>

      <div>
        <label>
          <input
            type="radio"
            value="light"
            checked={preferences.theme === 'light'}
            onChange={handleRadio}
          />
          Light
        </label>
        <label>
          <input
            type="radio"
            value="dark"
            checked={preferences.theme === 'dark'}
            onChange={handleRadio}
          />
          Dark
        </label>
      </div>
    </div>
  );
}
```

## 💡 Key Concepts

### Controlled vs Uncontrolled Components

**Controlled**:
- React state controls input value
- More control over form data
- Easier validation
- Recommended approach

**Uncontrolled**:
- DOM manages input state
- Use refs to access values
- Less React-like
- Useful for simple forms

### When to Use useRef
- Focus management
- Selecting text
- Triggering animations
- Integrating with third-party libraries
- Storing mutable values without re-render

## 📝 Practice Exercises

1. **Login Form**: Create a login form with email and password validation
2. **Multi-Step Form**: Build a wizard with multiple steps
3. **File Upload**: Create a form with file upload functionality
4. **Dynamic Fields**: Add/remove form fields dynamically
5. **Search with Debounce**: Implement search with debounce using useRef
6. **Auto-save Draft**: Save form data automatically using useEffect

## 🔗 Resources

- [Forms in React](https://react.dev/learn/reacting-to-input-with-state)
- [useRef Hook](https://react.dev/reference/react/useRef)
- [Formik Documentation](https://formik.org/)
- [React Hook Form](https://react-hook-form.com/)

---

**Next**: Advance to [05-State-Management](../05-State-Management/) to learn Context API and useReducer!
