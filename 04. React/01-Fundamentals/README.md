# React Fundamentals

Learn the basics of React and build your first React applications.

## 📚 Topics Covered

### 1. Introduction to React
- What is React?
- Why use React?
- Virtual DOM concept
- React vs Vanilla JavaScript

### 2. Setting Up React Environment
- Installing Node.js and npm
- Create React App (CRA)
- Project structure overview
- Development server

### 3. JSX (JavaScript XML)
- JSX syntax and rules
- Embedding expressions in JSX
- JSX vs HTML differences
- JSX attributes and children
- Fragments

### 4. Components
- Functional components
- Component naming conventions
- Creating your first component
- Importing and exporting components
- Component composition

### 5. Props
- What are props?
- Passing props to components
- Props destructuring
- Default props
- Props children

## 🚀 Getting Started

### Create Your First React App
```bash
npx create-react-app first-react-app
cd first-react-app
npm start
```

### Basic Component Example
```jsx
// App.js
import React from 'react';

function App() {
  return (
    <div className="App">
      <h1>Hello, React!</h1>
      <p>Welcome to your first React application.</p>
    </div>
  );
}

export default App;
```

### Component with Props
```jsx
// Greeting.js
function Greeting({ name, message }) {
  return (
    <div>
      <h2>Hello, {name}!</h2>
      <p>{message}</p>
    </div>
  );
}

export default Greeting;

// Usage in App.js
import Greeting from './Greeting';

function App() {
  return (
    <div>
      <Greeting name="Rahul" message="Welcome to React!" />
    </div>
  );
}
```

## 💡 Key Concepts

### JSX Rules
1. Return a single parent element
2. Close all tags (self-closing for single tags)
3. Use `className` instead of `class`
4. Use camelCase for attributes
5. JavaScript expressions in curly braces `{}`

### Component Best Practices
1. One component per file
2. Use PascalCase for component names
3. Keep components small and focused
4. Extract reusable logic into separate components

## 📝 Practice Exercises

1. **Hello World**: Create a simple Hello World component
2. **Profile Card**: Build a profile card component with props (name, bio, image)
3. **Button Component**: Create a reusable button component
4. **Card List**: Display a list of cards using component composition

## 🔗 Resources

- [React Official Tutorial](https://react.dev/learn)
- [Create React App Documentation](https://create-react-app.dev/)
- [JSX In Depth](https://react.dev/learn/writing-markup-with-jsx)

---

**Next**: Move on to [02-Styling](../02-Styling/) to learn about styling React components!
