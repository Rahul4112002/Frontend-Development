# React Router

Build single-page applications with client-side routing.

## 📚 Topics Covered

### 1. Router Basics
- BrowserRouter vs HashRouter
- Routes and Route components
- Link and NavLink
- Navigate component

### 2. Dynamic Routing
- URL parameters
- Query parameters
- useParams hook
- useSearchParams hook
- useNavigate hook

### 3. Nested Routes
- Outlet component
- Relative paths
- Index routes
- Layout routes

### 4. Advanced Features
- Protected routes
- Route guards
- 404 pages
- Redirects
- Lazy loading routes

## 🚀 Installation

```bash
npm install react-router-dom
```

## Code Examples

### Basic Routing Setup
```jsx
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/contact">Contact</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

function Home() {
  return <h1>Home Page</h1>;
}

function About() {
  return <h1>About Page</h1>;
}

function Contact() {
  return <h1>Contact Page</h1>;
}

function NotFound() {
  return <h1>404 - Page Not Found</h1>;
}
```

### NavLink with Active Styling
```jsx
import { NavLink } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <NavLink 
        to="/"
        className={({ isActive }) => isActive ? 'active' : ''}
      >
        Home
      </NavLink>
      <NavLink 
        to="/products"
        style={({ isActive }) => ({
          color: isActive ? 'red' : 'black',
          fontWeight: isActive ? 'bold' : 'normal'
        })}
      >
        Products
      </NavLink>
    </nav>
  );
}
```

### Dynamic Routes with useParams
```jsx
import { Routes, Route, useParams, Link } from 'react-router-dom';

function App() {
  return (
    <Routes>
      <Route path="/users" element={<Users />} />
      <Route path="/users/:userId" element={<UserProfile />} />
      <Route path="/products/:productId" element={<Product />} />
    </Routes>
  );
}

function Users() {
  const users = [1, 2, 3, 4, 5];
  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map(id => (
          <li key={id}>
            <Link to={`/users/${id}`}>User {id}</Link>
          </li>
        ))}
      </ul>
    </div>
  );
}

function UserProfile() {
  const { userId } = useParams();
  return <h1>User Profile: {userId}</h1>;
}

function Product() {
  const { productId } = useParams();
  const { data, loading } = useFetch(`/api/products/${productId}`);

  if (loading) return <p>Loading...</p>;
  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <p>Price: ${data.price}</p>
    </div>
  );
}
```

### Programmatic Navigation
```jsx
import { useNavigate } from 'react-router-dom';

function LoginForm() {
  const navigate = useNavigate();
  const [email, setEmail] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    // Perform login
    const success = await login(email);
    
    if (success) {
      // Navigate to dashboard
      navigate('/dashboard');
      // Or go back
      // navigate(-1);
      // Or replace current entry
      // navigate('/dashboard', { replace: true });
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input 
        type="email" 
        value={email} 
        onChange={(e) => setEmail(e.target.value)} 
      />
      <button type="submit">Login</button>
      <button type="button" onClick={() => navigate(-1)}>
        Go Back
      </button>
    </form>
  );
}
```

### Query Parameters
```jsx
import { useSearchParams } from 'react-router-dom';

function SearchPage() {
  const [searchParams, setSearchParams] = useSearchParams();
  const query = searchParams.get('q') || '';
  const category = searchParams.get('category') || 'all';

  const updateSearch = (newQuery) => {
    setSearchParams({ q: newQuery, category });
  };

  return (
    <div>
      <input 
        value={query}
        onChange={(e) => updateSearch(e.target.value)}
        placeholder="Search..."
      />
      <p>Searching for: {query} in {category}</p>
    </div>
  );
}
```

### Nested Routes with Outlet
```jsx
import { Routes, Route, Outlet, Link } from 'react-router-dom';

function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<Home />} />
        <Route path="products" element={<Products />}>
          <Route index element={<ProductList />} />
          <Route path=":id" element={<ProductDetail />} />
          <Route path="new" element={<NewProduct />} />
        </Route>
      </Route>
    </Routes>
  );
}

function Layout() {
  return (
    <div>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/products">Products</Link>
      </nav>
      <main>
        <Outlet /> {/* Child routes render here */}
      </main>
      <footer>Footer Content</footer>
    </div>
  );
}

function Products() {
  return (
    <div>
      <h1>Products</h1>
      <nav>
        <Link to="/products">All Products</Link>
        <Link to="/products/new">Add New</Link>
      </nav>
      <Outlet /> {/* Nested routes render here */}
    </div>
  );
}
```

### Protected Routes
```jsx
import { Navigate, Outlet } from 'react-router-dom';

function ProtectedRoute() {
  const { isAuthenticated } = useAuth();

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  return <Outlet />;
}

function App() {
  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      
      {/* Protected routes */}
      <Route element={<ProtectedRoute />}>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="/settings" element={<Settings />} />
      </Route>
    </Routes>
  );
}

// Role-based protection
function RequireRole({ allowedRoles, children }) {
  const { user } = useAuth();

  if (!allowedRoles.includes(user?.role)) {
    return <Navigate to="/unauthorized" replace />;
  }

  return children;
}

// Usage
<Route 
  path="/admin" 
  element={
    <RequireRole allowedRoles={['admin']}>
      <AdminPanel />
    </RequireRole>
  } 
/>
```

### Lazy Loading Routes
```jsx
import { lazy, Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';

const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));
const Profile = lazy(() => import('./pages/Profile'));

function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
        <Route path="/profile" element={<Profile />} />
      </Routes>
    </Suspense>
  );
}
```

## 💡 Key Concepts

### Router Types
- **BrowserRouter**: Uses HTML5 history API (recommended)
- **HashRouter**: Uses URL hash for older browsers
- **MemoryRouter**: For testing and React Native

### Link vs Navigate
- **Link/NavLink**: Declarative navigation (use in JSX)
- **useNavigate**: Programmatic navigation (use in functions)

### Route Matching
- Routes are matched in order
- Use `*` for catch-all routes
- `index` routes match parent path exactly

## 📝 Practice Exercises

1. **Multi-Page App**: Create app with 5+ routes and navigation
2. **Blog with Dynamic Routes**: Build blog with post/:id routes
3. **E-commerce Navigation**: Category pages with nested routes
4. **User Dashboard**: Protected dashboard with role-based access
5. **Search with Filters**: Implement search with query parameters
6. **Breadcrumb Navigation**: Create breadcrumb trail for nested routes

## 🔗 Resources

- [React Router Documentation](https://reactrouter.com/)
- [Tutorial](https://reactrouter.com/en/main/start/tutorial)
- [API Reference](https://reactrouter.com/en/main/routers/picking-a-router)

---

**Next**: Advance to [08-Redux](../08-Redux/) for state management at scale!
