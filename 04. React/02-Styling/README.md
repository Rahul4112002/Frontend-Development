# React Styling

Learn different approaches to style your React applications.

## 📚 Topics Covered

### 1. Inline Styles
- Style objects in React
- Dynamic inline styles
- When to use inline styles

### 2. CSS Stylesheets
- External CSS files
- Importing CSS in components
- Global vs component-specific styles

### 3. CSS Modules
- What are CSS Modules?
- Scoped styling
- Naming conventions
- Composing styles

### 4. Bootstrap Integration
- Installing Bootstrap
- React Bootstrap library
- Using Bootstrap components
- Responsive layouts

### 5. Modern CSS Solutions
- Styled Components (CSS-in-JS)
- Tailwind CSS with React
- SASS/SCSS integration

## 🚀 Styling Approaches

### Inline Styles
```jsx
function Button() {
  const buttonStyle = {
    backgroundColor: 'blue',
    color: 'white',
    padding: '10px 20px',
    border: 'none',
    borderRadius: '5px',
    cursor: 'pointer'
  };

  return <button style={buttonStyle}>Click Me</button>;
}
```

### External CSS
```jsx
// Button.css
.btn {
  background-color: blue;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

// Button.js
import './Button.css';

function Button() {
  return <button className="btn">Click Me</button>;
}
```

### CSS Modules
```jsx
// Button.module.css
.btn {
  background-color: blue;
  color: white;
  padding: 10px 20px;
}

// Button.js
import styles from './Button.module.css';

function Button() {
  return <button className={styles.btn}>Click Me</button>;
}
```

### Bootstrap Integration
```bash
# Install Bootstrap
npm install bootstrap

# Install React Bootstrap
npm install react-bootstrap bootstrap
```

```jsx
// Import in index.js or App.js
import 'bootstrap/dist/css/bootstrap.min.css';

// Using React Bootstrap
import { Button, Card, Container } from 'react-bootstrap';

function App() {
  return (
    <Container>
      <Card>
        <Card.Body>
          <Card.Title>Hello Bootstrap</Card.Title>
          <Button variant="primary">Click Me</Button>
        </Card.Body>
      </Card>
    </Container>
  );
}
```

## 💡 Best Practices

### When to Use Each Approach

**Inline Styles**:
- Dynamic styles based on props/state
- Quick prototyping
- Very specific styling

**CSS Stylesheets**:
- Global styles
- Simple projects
- Familiar CSS syntax

**CSS Modules**:
- Component-scoped styles
- Avoiding naming conflicts
- Medium to large projects

**Bootstrap/UI Libraries**:
- Rapid development
- Consistent design system
- Responsive layouts

### Styling Tips
1. Keep styles close to components
2. Use meaningful class names
3. Avoid deep nesting
4. Make styles reusable
5. Consider mobile-first approach

## 📝 Practice Exercises

1. **Styled Card**: Create a card component with CSS modules
2. **Theme Switcher**: Build a light/dark theme toggle
3. **Bootstrap Layout**: Design a responsive navbar and hero section
4. **Button Variants**: Create button component with different styles
5. **Form Styling**: Style a complete form with validation states

## 🔗 Resources

- [React Bootstrap Documentation](https://react-bootstrap.github.io/)
- [CSS Modules Guide](https://github.com/css-modules/css-modules)
- [Styled Components](https://styled-components.com/)
- [Tailwind CSS with React](https://tailwindcss.com/docs/guides/create-react-app)

---

**Next**: Proceed to [03-Core-Concepts](../03-Core-Concepts/) to learn React state and event handling!
