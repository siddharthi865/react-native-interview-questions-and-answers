# Set 1

| S.No. | Question                                                                                                                                                                                     |
| ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is React Native and how is it different from ReactJS?](#question-1-what-is-react-native-and-how-is-it-different-from-reactjs)                                                          |
| 2.    | [What are the advantages of using React Native over native development?](#question-2-what-are-the-advantages-of-using-react-native-over-native-development)                                  |
| 3.    | [Explain the role of JSX in React Native](#question-3-explain-the-role-of-jsx-in-react-native)                                                                                               |
| 4.    | [How do you create a new React Native project?](#question-4-how-do-you-create-a-new-react-native-project)                                                                                    |
| 5.    | [What is the difference between React Native CLI and Expo?](#question-5-what-is-the-difference-between-react-native-cli-and-expo)                                                            |
| 6.    | [Explain the concept of components in React Native](#question-6-explain-the-concept-of-components-in-react-native)                                                                           |
| 7.    | [What is the difference between Functional and Class Components?](#question-7-what-is-the-difference-between-functional-and-class-components)                                                |
| 8.    | [What are props in React Native?](#question-8-what-are-props-in-react-native)                                                                                                                |
| 9.    | [What is state in React Native and how is it used?](#question-9-what-is-state-in-react-native-and-how-is-it-used)                                                                            |
| 10.   | [How do you handle user input in React Native?](#question-10-how-do-you-handle-user-input-in-react-native)                                                                                   |
| 11.   | [What is the difference between controlled and uncontrolled components?](#question-11-what-is-the-difference-between-controlled-and-uncontrolled-components)                                 |
| 12.   | [Explain the React Native lifecycle methods in class components](#question-12-explain-the-react-native-lifecycle-methods-in-class-components)                                                |
| 13.   | [How do you perform conditional rendering in React Native?](#question-13-how-do-you-perform-conditional-rendering-in-react-native)                                                           |
| 14.   | [What is the role of FlatList in React Native?](#question-14-what-is-the-role-of-flatlist-in-react-native)                                                                                   |
| 15.   | [What is the difference between FlatList and ScrollView?](#question-15-what-is-the-difference-between-flatlist-and-scrollview)                                                               |
| 16.   | [How do you style components in React Native?](#question-16-how-do-you-style-components-in-react-native)                                                                                     |
| 17.   | [Explain the difference between inline styles, StyleSheet.create(), and styled-components](#question-17-explain-the-difference-between-inline-styles-stylesheetcreate-and-styled-components) |
| 18.   | [How do you handle touch events in React Native?](#question-18-how-do-you-handle-touch-events-in-react-native)                                                                               |
| 19.   | [What is the difference between TouchableOpacity, TouchableHighlight, and Pressable?](#question-19-what-is-the-difference-between-touchableopacity-touchablehighlight-and-pressable)         |
| 20.   | [How do you use Image component in React Native?](#question-20-how-do-you-use-image-component-in-react-native)                                                                               |

## Question 1. What is React Native and how is it different from ReactJS?

# Short answer

**React Native** is an open-source framework for building **native mobile applications** (iOS and Android) using JavaScript/TypeScript and React. It renders to **native UI components** (e.g., `UIView`, `TextView`) rather than HTML.

**ReactJS (React for the web)** is a JavaScript library for building **web applications**. It renders to the **DOM** (HTML/CSS) in a browser.

The biggest difference is the **rendering target**:

- **ReactJS → Browser DOM**
- **React Native → Native platform UI**

---

# Explanation

Although React Native and ReactJS share the same core React concepts (components, props, state, hooks, reconciliation), they differ significantly in rendering, architecture, styling, navigation, and performance.

## 1. Rendering

### ReactJS

React renders HTML elements like:

```jsx
<div>
  <h1>Hello</h1>
</div>
```

The browser converts these into DOM nodes.

Rendering flow:

```
React
   ↓
Virtual DOM
   ↓
Browser DOM
   ↓
HTML/CSS
```

---

### React Native

React Native renders native UI components:

```tsx
<View>
  <Text>Hello</Text>
</View>
```

These become actual platform widgets.

On iOS:

```
<View> → UIView
<Text> → UILabel
```

On Android:

```
<View> → android.view.View
<Text> → TextView
```

Modern React Native (New Architecture with **Fabric**) reduces overhead by enabling more direct, efficient communication between JavaScript and native rendering compared to the legacy bridge.

---

## 2. Platform Target

| ReactJS  | React Native             |
| -------- | ------------------------ |
| Websites | Mobile apps              |
| Browser  | Android/iOS              |
| HTML     | Native components        |
| CSS      | Flexbox-based StyleSheet |
| DOM      | Native UI                |

---

## 3. Styling

### ReactJS

```jsx
<div className="container">
```

Uses:

- CSS
- SCSS
- Tailwind CSS
- CSS Modules
- Styled Components

---

### React Native

No HTML or CSS files are required.

```tsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
  },
});
```

Uses:

- `StyleSheet`
- Flexbox (default layout engine)
- Styled Components (React Native variant)
- Utility-first libraries like NativeWind (optional)

No CSS selectors like:

- `div`
- `.container`
- `#id`

---

## 4. Navigation

### ReactJS

Uses routing libraries like:

- React Router

URL example:

```
/products/123
```

---

### React Native

No browser URLs.

Uses navigation libraries such as:

- React Navigation
- Expo Router (file-based routing for Expo projects)

Navigation example:

```tsx
navigation.navigate("Profile");
```

---

## 5. Native APIs

ReactJS cannot directly access mobile hardware.

React Native can access:

- Camera
- GPS
- Bluetooth
- Contacts
- Biometrics
- Push notifications
- Accelerometer
- NFC (where supported)

Often through built-in APIs, Expo SDK modules, or native modules.

---

## 6. Performance

### ReactJS

Performance depends on:

- DOM updates
- Browser rendering
- JavaScript execution

---

### React Native

Performance depends on:

- JavaScript thread
- UI thread
- Native rendering
- New Architecture (Fabric + TurboModules)

Senior interview point:

- Legacy React Native used an asynchronous bridge between JavaScript and native code.
- The New Architecture introduces **Fabric** (renderer) and **TurboModules**, reducing serialization overhead, improving startup time, responsiveness, and interoperability with native code.

---

## 7. Code Sharing

React concepts are identical:

- Components
- Hooks
- Context
- Redux/Zustand
- React Query/TanStack Query
- TypeScript

Only platform-specific UI differs.

Example:

```tsx
// Shared logic
const useCounter = () => {
  const [count, setCount] = useState(0);
  return { count, increment: () => setCount((c) => c + 1) };
};
```

This hook can be reused across ReactJS and React Native.

---

## 8. Component Architecture

A senior-level React Native app commonly separates concerns into:

```
Presentation Components
        ↓
Business Logic (Hooks)
        ↓
State Management
        ↓
API Layer
        ↓
Native Modules (when needed)
```

For example:

```
HomeScreen
    ↓
useProducts()
    ↓
TanStack Query
    ↓
REST API
```

This architecture keeps UI platform-specific while allowing business logic to be shared.

---

# Example

**Stack:** Expo + React Native + TypeScript

Create a project:

```bash
npx create-expo-app MyApp --template expo-template-blank-typescript
cd MyApp
npm start
```

`App.tsx`

```tsx
import React from "react";
import { SafeAreaView, Text, StyleSheet } from "react-native";

export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <Text style={styles.title}>Hello React Native</Text>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
  },
  title: {
    fontSize: 24,
    fontWeight: "600",
  },
});
```

Run the app:

```bash
# Start Metro
npm start

# Android
npm run android

# iOS (macOS only)
npm run ios
```

This demonstrates the key distinction from ReactJS: instead of HTML elements (`div`, `h1`), React Native uses native components (`SafeAreaView`, `Text`).

---

# Tooling & Setup

- **Preferred stack:** Expo for rapid development and easy access to native APIs; React Native CLI for projects requiring custom native code or advanced platform integration.
- **Avoid Create React App (CRA):** It is deprecated and intended for web, not mobile development.
- **Bundler:** React Native uses **Metro**, optimized for mobile bundling, fast refresh, and asset resolution.
- **Module system:** Modern React Native projects primarily use **ES Modules** (`import`/`export`). Tooling transpiles them as needed for the runtime. CommonJS may still appear in some configuration or legacy packages but should not be the default for application code.
- **New Architecture:** Enable **Fabric** and **TurboModules** where supported to improve rendering and native interoperability.

---

# Performance

- Use **React.memo** to avoid unnecessary re-renders of pure components.
- Use **useMemo** for expensive computations and **useCallback** for stable callback references passed to child components.
- Prefer **FlatList** over mapping large arrays into `ScrollView` for virtualization.
- Optimize images with appropriately sized assets and caching libraries where needed.
- Keep expensive work off the JavaScript thread; move performance-critical functionality into native modules when appropriate.
- Profile with:
  - React DevTools Profiler
  - Flipper (for supported React Native versions)
  - React Native Performance Monitor
  - Platform profilers (Xcode Instruments and Android Studio Profiler)

---

# Testing

- **Unit/Integration:** Jest + React Native Testing Library
- **E2E:** Detox or Maestro

Example:

```bash
npm test
```

Simple test example:

```tsx
import { render } from "@testing-library/react-native";
import App from "../App";

test("renders welcome text", () => {
  expect(render(<App />).getByText("Hello React Native")).toBeTruthy();
});
```

---

# Ops & Deployment

- **Logging:** Use structured logging and crash reporting tools (e.g., Sentry) in production.
- **Error handling:** Implement React error boundaries and graceful API error handling.
- **Offline:** Cache server state with libraries like TanStack Query and persist important local data with AsyncStorage or SQLite when appropriate.
- **CI/CD:** Use GitHub Actions, Bitrise, or Expo Application Services (EAS) for automated builds, testing, and releases.
- **Deployment:** Publish through the Apple App Store and Google Play. Expo projects can use OTA updates via EAS Update where appropriate; bare React Native projects may also use supported update strategies while ensuring compatibility with native changes.

---

# Pitfalls

- **Don't assume HTML/CSS knowledge transfers directly.** React Native uses native components and a Flexbox-based styling system rather than the DOM and CSS.
- **Avoid unnecessary re-renders.** Large component trees without memoization or proper state placement can hurt performance.
- **Choose the right toolchain.** Use Expo unless your project requires custom native code or unsupported native integrations, in which case React Native CLI may be more appropriate.

## Question 2. What are the advantages of using React Native over native development?

## Question 3. Explain the role of **JSX** in React Native

## Question 4. How do you create a new React Native project?

## Question 5. What is the difference between **React Native CLI** and **Expo**?

## Question 6. Explain the concept of **components** in React Native

## Question 7. What is the difference between **Functional** and **Class Components**?

## Question 8. What are **props** in React Native?

## Question 9. What is **state** in React Native and how is it used?

## Question 10. How do you handle user input in React Native?

## Question 11. What is the difference between **controlled** and **uncontrolled** components?

## Question 12. Explain the **React Native lifecycle methods** in class components

## Question 13. How do you perform **conditional rendering** in React Native?

## Question 14. What is the role of **FlatList** in React Native?

## Question 15. What is the difference between **FlatList** and **ScrollView**?

## Question 16. How do you style components in React Native?

## Question 17. Explain the difference between **inline styles**, **StyleSheet.create()**, and **styled-components**

## Question 18. How do you handle **touch events** in React Native?

## Question 19. What is the difference between **TouchableOpacity**, **TouchableHighlight**, and **Pressable**?

## Question 20. How do you use **Image** component in React Native?
