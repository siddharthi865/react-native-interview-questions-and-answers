# Set 5

| S.No. | Question                                                                                                                                                    |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you handle memory leaks in React Native?](#question-1-how-do-you-handle-memory-leaks-in-react-native)                                               |
| 2.    | [How do you profile a React Native app?](#question-2-how-do-you-profile-a-react-native-app)                                                                 |
| 3.    | [How do you reduce JS thread blocking?](#question-3-how-do-you-reduce-js-thread-blocking)                                                                   |
| 4.    | [How do you optimize image loading and caching?](#question-4-how-do-you-optimize-image-loading-and-caching)                                                 |
| 5.    | [What are inline vs native animations?](#question-5-what-are-inline-vs-native-animations)                                                                   |
| 6.    | [How do you implement gesture-based navigation?](#question-6-how-do-you-implement-gesture-based-navigation)                                                 |
| 7.    | [How do you implement offline-first architecture?](#question-7-how-do-you-implement-offline-first-architecture)                                             |
| 8.    | [How do you secure React Native apps?](#question-8-how-do-you-secure-react-native-apps)                                                                     |
| 9.    | [How do you store sensitive data securely?](#question-9-how-do-you-store-sensitive-data-securely)                                                           |
| 10.   | [How do you implement authentication flows (JWT, OAuth) in React Native?](#question-10-how-do-you-implement-authentication-flows-jwt-oauth-in-react-native) |
| 11.   | [How do you integrate biometric authentication?](#question-11-how-do-you-integrate-biometric-authentication)                                                |
| 12.   | [How do you implement multi-language support (i18n)?](#question-12-how-do-you-implement-multi-language-support-i18n)                                        |
| 13.   | [How do you integrate analytics tools (Firebase, Amplitude)?](#question-13-how-do-you-integrate-analytics-tools-firebase-amplitude)                         |
| 14.   | [How do you implement real-time data using WebSockets?](#question-14-how-do-you-implement-real-time-data-using-websockets)                                  |
| 15.   | [How do you handle large lists with complex items?](#question-15-how-do-you-handle-large-lists-with-complex-items)                                          |
| 16.   | [How do you implement drag-and-drop functionality?](#question-16-how-do-you-implement-drag-and-drop-functionality)                                          |
| 17.   | [How do you implement custom transitions between screens?](#question-17-how-do-you-implement-custom-transitions-between-screens)                            |
| 18.   | [How do you implement deep linking with dynamic content?](#question-18-how-do-you-implement-deep-linking-with-dynamic-content)                              |
| 19.   | [How do you handle hot reload vs fast refresh?](#question-19-how-do-you-handle-hot-reload-vs-fast-refresh)                                                  |
| 20.   | [How do you deploy a React Native app to App Store and Play Store?](#question-20-how-do-you-deploy-a-react-native-app-to-app-store-and-play-store)          |

## Question 1. How do you handle **memory leaks** in React Native?

# Short answer

Memory leaks in React Native are primarily prevented by **cleaning up side effects**, **removing event listeners**, **clearing timers**, **canceling async operations**, **avoiding unnecessary object retention**, and **releasing native resources** when components unmount. With the **New Architecture (Fabric + TurboModules)**, lifecycle management is improved, but developers still need to properly clean up JavaScript and native resources.

---

# Explanation

A **memory leak** occurs when objects remain in memory even though they are no longer needed. Over time this increases memory usage, slows the app, and can eventually lead to crashes—especially on lower-end Android devices.

Common sources of memory leaks in React Native include:

### 1. Uncleared timers

Timers continue running after a component unmounts.

```tsx
useEffect(() => {
  const timer = setInterval(fetchData, 5000);

  return () => clearInterval(timer);
}, []);
```

---

### 2. Event listeners

Always unsubscribe from listeners.

```tsx
useEffect(() => {
  const subscription = Dimensions.addEventListener("change", handleResize);

  return () => subscription.remove();
}, []);
```

The same applies to:

- `AppState`
- `Keyboard`
- `NetInfo`
- `Linking`
- NativeEventEmitter
- Third-party libraries

---

### 3. Async requests updating unmounted components

A common warning:

```
Can't perform a React state update on an unmounted component
```

Use `AbortController` (when supported) or ignore results after cleanup.

```tsx
useEffect(() => {
  const controller = new AbortController();

  fetch(url, {
    signal: controller.signal,
  });

  return () => controller.abort();
}, []);
```

Or guard updates:

```tsx
useEffect(() => {
  let mounted = true;

  async function load() {
    const data = await fetchData();

    if (mounted) {
      setData(data);
    }
  }

  load();

  return () => {
    mounted = false;
  };
}, []);
```

---

### 4. WebSocket / Socket connections

Always disconnect.

```tsx
useEffect(() => {
  socket.connect();

  return () => {
    socket.disconnect();
  };
}, []);
```

---

### 5. Native subscriptions

Libraries such as:

- Bluetooth
- GPS
- Camera
- Sensors
- Push notifications

usually expose subscriptions.

Always remove them.

```tsx
return () => subscription.remove();
```

---

### 6. Heavy references

Avoid keeping:

- huge arrays
- image caches
- large JSON
- Map/Set objects

inside global variables unless necessary.

Instead:

- paginate
- virtualize lists
- cache intelligently
- release references

---

### 7. FlatList optimization

Rendering thousands of rows keeps many objects alive.

Use:

- `FlatList`
- `windowSize`
- `removeClippedSubviews`
- `getItemLayout`
- pagination

instead of:

```tsx
ScrollView;
```

for long lists.

---

### 8. Image memory

Large images are one of the biggest memory consumers.

Use:

- properly sized images
- caching libraries
- thumbnails
- lazy loading

Avoid rendering multiple 4K images simultaneously.

---

### 9. Native module leaks

If you write native modules:

- unregister observers
- release camera instances
- stop background threads
- close database connections

In the **New Architecture (Fabric/TurboModules)**, lifecycle integration is better, but native resources still need explicit cleanup.

---

### 10. useEffect cleanup

Every effect that creates something should generally destroy it.

```tsx
useEffect(() => {
  // create resource

  return () => {
    // destroy resource
  };
}, []);
```

This is the single most important React pattern for preventing leaks.

---

# Example

**Stack:** Expo + React Native + TypeScript

Create a project:

```bash
npx create-expo-app MemoryLeakDemo --template
cd MemoryLeakDemo
npx expo start
```

`App.tsx`

```tsx
import { useEffect, useState } from "react";
import { Text, View } from "react-native";

export default function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setCount((c) => c + 1);
    }, 1000);

    return () => {
      clearInterval(timer);
    };
  }, []);

  return (
    <View
      style={{
        flex: 1,
        justifyContent: "center",
        alignItems: "center",
      }}
    >
      <Text>{count}</Text>
    </View>
  );
}
```

Run:

```bash
npx expo start
```

On Android:

```bash
a
```

On iOS:

```bash
i
```

This example demonstrates proper cleanup of an interval when the component unmounts.

---

# Tooling & Setup

- Prefer **Expo** for rapid development or the **React Native CLI** when you need custom native code.
- Avoid **Create React App (CRA)**—it is deprecated and designed for web, not React Native.
- Modern React Native uses **Metro** as the JavaScript bundler.
- Use **ES Modules (ESM)** (`import`/`export`) throughout your app. CommonJS (`require`) is mainly encountered in tooling or legacy packages.
- If targeting the **New Architecture**, enable **Fabric** and **TurboModules** where supported to reduce bridge overhead and improve rendering performance.

---

# Performance

To detect and prevent memory leaks:

- Profile memory usage with **Flipper** (Memory plugin) and React Native DevTools where supported.
- Use the React DevTools **Profiler** to identify unnecessary re-renders.
- Enable the built-in **Perf Monitor** to watch FPS and JS/UI thread performance during development.
- Use `React.memo`, `useMemo`, and `useCallback` to avoid unnecessary allocations and renders.
- Virtualize long lists with `FlatList` instead of `ScrollView`.
- Use appropriately sized images and caching solutions to reduce memory pressure.
- For native modules, ensure listeners, threads, file handles, and other resources are released during cleanup.

---

# Testing

Memory leaks are difficult to unit test directly, but you can verify cleanup behavior.

**Unit/Integration**

Use **Jest** with **React Native Testing Library** to ensure subscriptions and timers are cleaned up.

```bash
npm test
```

Example:

```tsx
jest.useFakeTimers();

// Render component, unmount it, then verify clearInterval or unsubscribe was called.
```

**End-to-end**

Use **Detox** or **Maestro** to navigate repeatedly between screens and monitor that memory usage remains stable over time.

---

# Ops & Deployment

- Log unexpected errors with tools such as **Sentry** or **Crashlytics** to detect crashes caused by excessive memory usage.
- Handle async failures gracefully and cancel pending work when screens unmount.
- Cache data responsibly for offline support, and periodically evict stale cache entries to avoid unbounded growth.
- Automate builds with **EAS Build**, **GitHub Actions**, or **Bitrise**.
- Validate memory usage on real Android and iOS devices before releasing to the App Store or Google Play. If using OTA updates (for example, Expo Updates), ensure cleanup logic remains compatible across app versions.

---

# Pitfalls

- Forgetting to return a cleanup function from `useEffect` for timers, listeners, or subscriptions.
- Updating component state after the component has already unmounted due to unfinished asynchronous work.
- Keeping large objects, image data, or caches in global state without an eviction strategy.

## Question 2. How do you profile a React Native app?

## Question 3. How do you reduce **JS thread blocking**?

## Question 4. How do you optimize **image loading and caching**?

## Question 5. What are **inline vs native animations**?

## Question 6. How do you implement **gesture-based navigation**?

## Question 7. How do you implement **offline-first architecture**?

## Question 8. How do you secure React Native apps?

## Question 9. How do you store **sensitive data** securely?

## Question 10. How do you implement **authentication flows** (JWT, OAuth) in React Native?

## Question 11. How do you integrate **biometric authentication**?

## Question 12. How do you implement **multi-language support (i18n)**?

## Question 13. How do you integrate **analytics tools** (Firebase, Amplitude)?

## Question 14. How do you implement **real-time data** using WebSockets?

## Question 15. How do you handle **large lists with complex items**?

## Question 16. How do you implement **drag-and-drop functionality**?

## Question 17. How do you implement **custom transitions between screens**?

## Question 18. How do you implement **deep linking with dynamic content**?

## Question 19. How do you handle **hot reload vs fast refresh**?

## Question 20. How do you deploy a React Native app to **App Store** and **Play Store**?
