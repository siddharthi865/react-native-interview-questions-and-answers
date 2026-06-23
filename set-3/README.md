# Set 3

| S.No. | Question                                                                                                                   |
| ----- | -------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you use hooks in React Native?](#question-1-how-do-you-use-hooks-in-react-native)                                  |
| 2.    | [Explain useState and useEffect hooks](#question-2-explain-usestate-and-useeffect-hooks)                                   |
| 3.    | [What is useRef and how is it used?](#question-3-what-is-useref-and-how-is-it-used)                                        |
| 4.    | [How do you perform animations in React Native?](#question-4-how-do-you-perform-animations-in-react-native)                |
| 5.    | [Explain Animated API vs LayoutAnimation](#question-5-explain-animated-api-vs-layoutanimation)                             |
| 6.    | [How do you implement gesture handling?](#question-6-how-do-you-implement-gesture-handling)                                |
| 7.    | [What is React Native Reanimated?](#question-7-what-is-react-native-reanimated)                                            |
| 8.    | [How do you optimize performance in a FlatList?](#question-8-how-do-you-optimize-performance-in-a-flatlist)                |
| 9.    | [What are PureComponent and React.memo?](#question-9-what-are-purecomponent-and-reactmemo)                                 |
| 10.   | [How do you prevent unnecessary re-renders?](#question-10-how-do-you-prevent-unnecessary-re-renders)                       |
| 11.   | [What are virtualized lists in React Native?](#question-11-what-are-virtualized-lists-in-react-native)                     |
| 12.   | [How do you implement pull-to-refresh in a list?](#question-12-how-do-you-implement-pull-to-refresh-in-a-list)             |
| 13.   | [How do you implement infinite scrolling?](#question-13-how-do-you-implement-infinite-scrolling)                           |
| 14.   | [How do you use Dimensions API?](#question-14-how-do-you-use-dimensions-api)                                               |
| 15.   | [How do you handle device orientation changes?](#question-15-how-do-you-handle-device-orientation-changes)                 |
| 16.   | [How do you implement dark mode / theming?](#question-16-how-do-you-implement-dark-mode--theming)                          |
| 17.   | [How do you handle deep linking in React Native?](#question-17-how-do-you-handle-deep-linking-in-react-native)             |
| 18.   | [How do you open external apps / URLs?](#question-18-how-do-you-open-external-apps--urls)                                  |
| 19.   | [How do you integrate maps in React Native?](#question-19-how-do-you-integrate-maps-in-react-native)                       |
| 20.   | [How do you handle push notifications in React Native?](#question-20-how-do-you-handle-push-notifications-in-react-native) |

## Question 1. How do you use **hooks** in React Native?

# Short answer

**Hooks** are functions introduced in React that let functional components use state, lifecycle methods, context, refs, and other React features without writing class components. In React Native, hooks work exactly like they do in React because React Native uses the React rendering model.

The most commonly used hooks are:

- `useState` – Manage local component state.
- `useEffect` – Perform side effects (API calls, subscriptions, timers).
- `useMemo` – Memoize expensive calculations.
- `useCallback` – Memoize functions to prevent unnecessary re-renders.
- `useRef` – Store mutable values or access native components.
- `useContext` – Consume shared state from Context API.
- `useReducer` – Manage complex state logic.
- `useImperativeHandle` – Customize ref exposure.
- `useLayoutEffect` – Run effects before the UI is painted.
- `useTransition`, `useDeferredValue` – Useful for concurrent rendering (React 18+).

---

# Explanation

Hooks are central to modern React Native development because they encourage **small, reusable, composable logic**.

Instead of writing lifecycle methods like:

```js
componentDidMount();
componentDidUpdate();
componentWillUnmount();
```

you write:

```tsx
useEffect(() => {
  // setup

  return () => {
    // cleanup
  };
}, []);
```

which keeps related logic together.

## 1. useState

Used for local UI state.

```tsx
const [name, setName] = useState("");
```

Changing state triggers a re-render.

---

## 2. useEffect

Runs side effects.

Examples:

- API requests
- Timers
- Event listeners
- Push notifications
- Location updates
- Analytics

Example:

```tsx
useEffect(() => {
  fetchUsers();

  return () => {
    console.log("cleanup");
  };
}, []);
```

Dependencies determine when the effect runs.

```tsx
// every render
useEffect(() => {});

// once
useEffect(() => {}, []);

// when userId changes
useEffect(() => {}, [userId]);
```

---

## 3. useRef

Useful when values shouldn't trigger re-renders.

Examples:

- Focus TextInput
- Store timers
- Previous values
- Animated references

```tsx
const inputRef = useRef<TextInput>(null);

inputRef.current?.focus();
```

---

## 4. useMemo

Avoid recalculating expensive values.

Without memoization:

```tsx
const filtered = users.filter(...);
```

Every render recomputes.

With memoization:

```tsx
const filtered = useMemo(() => {
  return users.filter(...);
}, [users]);
```

---

## 5. useCallback

Avoid recreating functions every render.

```tsx
const onPress = useCallback(() => {
  console.log("Pressed");
}, []);
```

Useful when passing callbacks to memoized child components.

---

## 6. useContext

Avoid prop drilling.

```tsx
const theme = useContext(ThemeContext);
```

Good for:

- Theme
- Authentication
- Language
- User settings

---

## 7. useReducer

Better than multiple `useState` calls for complex state transitions.

```tsx
const [state, dispatch] = useReducer(reducer, initialState);

dispatch({
  type: "increment",
});
```

Often used with Context for app-wide state.

---

## Rendering behavior

Hooks participate in React's reconciliation process:

- `useState` updates schedule a component re-render.
- Parent re-renders also re-render children unless they are memoized with `React.memo`.
- `useMemo` and `useCallback` help preserve object and function identities, reducing unnecessary work when combined with memoized components.
- `useRef` updates do **not** trigger re-renders, making it ideal for mutable values like timers or imperative handles.

---

## Component architecture

A common architecture in production React Native apps is:

- **Presentation components**: Focus on rendering UI and receiving props.
- **Container or screen components**: Coordinate data fetching and state using hooks.
- **Custom hooks**: Encapsulate reusable business logic, such as authentication, permissions, or network status.

Example:

```tsx
const user = useUser();
const location = useLocation();
const theme = useTheme();
```

This keeps components focused and makes logic easier to test and reuse.

---

## State management trade-offs

- **`useState`**: Best for simple, local component state.
- **`useReducer`**: Better for complex state with multiple transitions or related updates.
- **Context + hooks**: Suitable for lightweight global state (theme, auth), but frequent updates can re-render many consumers.
- **External libraries** (e.g., Redux Toolkit, Zustand, Jotai): Better for large applications requiring scalable global state, middleware, or selective subscriptions.

Choose the simplest solution that meets the application's complexity.

---

## New Architecture (Fabric & TurboModules)

Hooks themselves are part of React and behave the same regardless of architecture. However, under the **New Architecture**:

- **Fabric** improves rendering efficiency and enables tighter integration between React and native UI.
- **TurboModules** reduce overhead when calling native modules.
- Hooks that drive frequent UI updates (animations, gestures, scrolling) benefit indirectly from the improved rendering pipeline.

For performance-critical animations, pair hooks with libraries like **Reanimated** rather than relying on JavaScript-driven state updates for every frame.

---

# Example

**Stack:** Expo + React Native + TypeScript

Create a project:

```bash
npx create-expo-app HooksDemo --template expo-template-blank-typescript

cd HooksDemo

npm start
```

`App.tsx`

```tsx
import React, { useCallback, useMemo, useState } from "react";
import { Button, StyleSheet, Text, View } from "react-native";

export default function App() {
  const [count, setCount] = useState(0);

  const doubled = useMemo(() => count * 2, [count]);

  const increment = useCallback(() => {
    setCount((prev) => prev + 1);
  }, []);

  return (
    <View style={styles.container}>
      <Text>Count: {count}</Text>
      <Text>Doubled: {doubled}</Text>

      <Button title="Increment" onPress={increment} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
  },
});
```

Run:

```bash
# Android
npx expo run:android

# iOS (macOS)
npx expo run:ios
```

---

# Tooling & Setup

- **Preferred stack:** Expo (excellent developer experience, OTA updates, and easy setup) or React Native CLI for projects needing extensive native customization.
- **Avoid Create React App (CRA):** It is deprecated and designed for web, not mobile.
- **Bundler:** React Native uses **Metro**, optimized for mobile development with fast refresh.
- **Modules:** Prefer **ES Modules (`import`/`export`)**. CommonJS (`require`) is still supported where necessary but is no longer the preferred style.
- Enable the **New Architecture** (Fabric and TurboModules) for new projects when compatible with your dependencies.

---

# Performance

- Use `React.memo` for expensive child components receiving stable props.
- Use `useMemo` only for expensive computations—not for trivial values.
- Use `useCallback` when passing callbacks to memoized children or dependency arrays.
- Avoid unnecessary state; derive values when possible.
- Prefer `useRef` for mutable values that don't affect rendering.
- Profile with:
  - React DevTools Profiler
  - Flipper (for supported React Native versions)
  - React Native Performance Monitor

- Optimize images using appropriately sized assets and caching libraries where needed.
- For native integrations, the New Architecture's Fabric and TurboModules reduce bridge overhead, and libraries like Reanimated execute animations on the UI thread for smoother interactions.

---

# Testing

- **Unit & integration:** Jest + React Native Testing Library.

Example:

```bash
npm test
```

Example assertion:

```tsx
import { render } from "@testing-library/react-native";
import App from "./App";

test("renders count", () => {
  const { getByText } = render(<App />);
  expect(getByText("Count: 0")).toBeTruthy();
});
```

- **End-to-end:** Use **Detox** or **Maestro** to validate complete user flows on Android and iOS.

---

# Ops & Deployment

- Use structured logging (e.g., Sentry, Crashlytics) and React Native error boundaries for runtime monitoring.
- Handle async operations with `try/catch` and provide user-friendly fallback UI.
- Support offline scenarios with local persistence (e.g., AsyncStorage or a database like SQLite) and request retry mechanisms.
- Automate builds and releases using **EAS Build**, GitHub Actions, or Bitrise.
- Publish through the App Store and Google Play. For supported Expo apps, use OTA updates carefully for JavaScript-only changes; native code changes still require a new app build.

---

# Pitfalls

- **Don't call hooks conditionally** or inside loops; always call them at the top level of a React component or custom hook.
- **Watch dependency arrays** in `useEffect`, `useMemo`, and `useCallback`; missing or incorrect dependencies can cause stale values or unnecessary re-renders.
- **Avoid overusing memoization**; `useMemo` and `useCallback` add complexity and should be used where profiling shows a measurable benefit.

## Question 2. Explain **useState** and **useEffect** hooks

## Question 3. What is **useRef** and how is it used?

## Question 4. How do you perform **animations** in React Native?

## Question 5. Explain **Animated API** vs **LayoutAnimation**

## Question 6. How do you implement **gesture handling**?

## Question 7. What is **React Native Reanimated**?

## Question 8. How do you optimize performance in a FlatList?

## Question 9. What are **PureComponent** and **React.memo**?

## Question 10. How do you prevent unnecessary re-renders?

## Question 11. What are **virtualized lists** in React Native?

## Question 12. How do you implement **pull-to-refresh** in a list?

## Question 13. How do you implement **infinite scrolling**?

## Question 14. How do you use **Dimensions API**?

## Question 15. How do you handle **device orientation changes**?

## Question 16. How do you implement **dark mode / theming**?

## Question 17. How do you handle **deep linking** in React Native?

## Question 18. How do you open **external apps / URLs**?

## Question 19. How do you integrate **maps** in React Native?

## Question 20. How do you handle **push notifications** in React Native?
