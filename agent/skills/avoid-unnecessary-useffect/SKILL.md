---
name: avoid-unnecessary-useeffect
description: Identifies when useEffect is unnecessary in React components and suggests better alternatives. Use when reviewing React code, seeing useEffect usage, or when the user asks about React effects, state synchronization, or component optimization.
---

# Avoid Unnecessary useEffect

Before adding `useEffect`, ask: **"Do I need to synchronize with an external system?"**

If the answer is **no**, you probably don't need `useEffect`.

## When You DON'T Need useEffect

### 1. Transforming Data for Rendering

**❌ Bad:**
```tsx
const [filteredTodos, setFilteredTodos] = useState([]);
useEffect(() => {
  setFilteredTodos(todos.filter(t => !t.completed));
}, [todos]);
```

**✅ Good:**
```tsx
const filteredTodos = todos.filter(t => !t.completed);
```

**Rule:** Calculate during render. If it's expensive, use `useMemo`.

### 2. Updating State Based on Props/State

**❌ Bad:**
```tsx
const [fullName, setFullName] = useState('');
useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);
```

**✅ Good:**
```tsx
const fullName = `${firstName} ${lastName}`;
```

**Rule:** Derived state should be calculated, not stored.

### 3. Handling User Events

**❌ Bad:**
```tsx
const [jsonToSubmit, setJsonToSubmit] = useState(null);
useEffect(() => {
  if (jsonToSubmit !== null) {
    post('/api/register', jsonToSubmit);
  }
}, [jsonToSubmit]);

function handleSubmit(e) {
  e.preventDefault();
  setJsonToSubmit({ firstName, lastName });
}
```

**✅ Good:**
```tsx
function handleSubmit(e) {
  e.preventDefault();
  post('/api/register', { firstName, lastName });
}
```

**Rule:** Event-specific logic belongs in event handlers.

### 4. Resetting State When Prop Changes

**❌ Bad:**
```tsx
useEffect(() => {
  setComment('');
}, [userId]);
```

**✅ Good:**
```tsx
<Profile userId={userId} key={userId} />
```

**Rule:** Use `key` prop to reset component state when identity changes.

### 5. Sharing Logic Between Event Handlers

**❌ Bad:**
```tsx
useEffect(() => {
  if (product.isInCart) {
    showNotification(`Added ${product.name} to cart!`);
  }
}, [product]);

function handleBuyClick() {
  addToCart(product);
}
```

**✅ Good:**
```tsx
function buyProduct() {
  addToCart(product);
  showNotification(`Added ${product.name} to cart!`);
}

function handleBuyClick() {
  buyProduct();
}
```

**Rule:** Extract shared logic into a function, call from event handlers.

### 6. Notifying Parent Components

**❌ Bad:**
```tsx
useEffect(() => {
  onChange(isOn);
}, [isOn, onChange]);
```

**✅ Good:**
```tsx
function updateToggle(nextIsOn) {
  setIsOn(nextIsOn);
  onChange(nextIsOn);
}
```

**Rule:** Update parent state in the same event handler that updates local state.

### 7. Caching Expensive Calculations

**❌ Bad:**
```tsx
const [visibleTodos, setVisibleTodos] = useState([]);
useEffect(() => {
  setVisibleTodos(getFilteredTodos(todos, filter));
}, [todos, filter]);
```

**✅ Good:**
```tsx
const visibleTodos = useMemo(
  () => getFilteredTodos(todos, filter),
  [todos, filter]
);
```

**Rule:** Use `useMemo` for expensive calculations, not `useEffect` + state.

## When You DO Need useEffect

Use `useEffect` only for:
- **Synchronizing with external systems** (browser APIs, third-party libraries, network)
- **Subscribing to external stores** (use `useSyncExternalStore` when possible)
- **Cleanup** (timers, subscriptions, event listeners)

**Example - Valid useEffect:**
```tsx
useEffect(() => {
  const subscription = subscribe();
  return () => subscription.unsubscribe();
}, []);
```

## Decision Tree

```
Is this synchronizing with an external system?
├─ Yes → useEffect might be appropriate
│   └─ Consider useSyncExternalStore for subscriptions
└─ No → Don't use useEffect
    ├─ Transforming data? → Calculate during render
    ├─ User event? → Use event handler
    ├─ Derived state? → Calculate, don't store
    ├─ Reset on prop change? → Use key prop
    └─ Expensive calculation? → Use useMemo
```

## Quick Checklist

Before writing `useEffect`, verify:
- [ ] This synchronizes with an external system (browser API, network, third-party lib)
- [ ] This is NOT transforming data for rendering
- [ ] This is NOT handling a user event
- [ ] This is NOT updating state based on props/state
- [ ] This is NOT sharing logic between handlers

If any check fails, reconsider the approach.

## Common Patterns in This Codebase

Watch for these patterns that might need refactoring:

1. **State updates after form submission** - Move to event handler
2. **Resetting form state** - Use `key` prop or reset in handler
3. **Derived calculations** - Calculate during render
4. **Side effects from state changes** - Often belong in event handlers

## References

- [React: You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
- Effects are escape hatches - use sparingly
- Modern frameworks provide better data fetching than Effects

