# Set 2

| S.No. | Question                                                                                                                                                                       |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [What are TextInput components and how do you handle input changes?](#question-1-what-are-textinput-components-and-how-do-you-handle-input-changes)                            |
| 2.    | [How do you manage multiple screens in React Native?](#question-2-how-do-you-manage-multiple-screens-in-react-native)                                                          |
| 3.    | [What is React Navigation and why is it used?](#question-3-what-is-react-navigation-and-why-is-it-used)                                                                        |
| 4.    | [Explain the difference between StackNavigator, TabNavigator, and DrawerNavigator](#question-4-explain-the-difference-between-stacknavigator-tabnavigator-and-drawernavigator) |
| 5.    | [How do you pass data between screens in React Native?](#question-5-how-do-you-pass-data-between-screens-in-react-native)                                                      |
| 6.    | [How do you debug a React Native app?](#question-6-how-do-you-debug-a-react-native-app)                                                                                        |
| 7.    | [How do you handle errors in React Native?](#question-7-how-do-you-handle-errors-in-react-native)                                                                              |
| 8.    | [Explain the purpose of keyExtractor in FlatList](#question-8-explain-the-purpose-of-keyextractor-in-flatlist)                                                                 |
| 9.    | [What is the difference between stateful and stateless components?](#question-9-what-is-the-difference-between-stateful-and-stateless-components)                              |
| 10.   | [How do you reload an app in development mode?](#question-10-how-do-you-reload-an-app-in-development-mode)                                                                     |
| 11.   | [What is Context API in React Native?](#question-11-what-is-context-api-in-react-native)                                                                                       |
| 12.   | [How do you manage global state in React Native?](#question-12-how-do-you-manage-global-state-in-react-native)                                                                 |
| 13.   | [Explain the difference between Context API and Redux](#question-13-explain-the-difference-between-context-api-and-redux)                                                      |
| 14.   | [What is Redux Thunk and why is it used?](#question-14-what-is-redux-thunk-and-why-is-it-used)                                                                                 |
| 15.   | [What is Redux Saga?](#question-15-what-is-redux-saga)                                                                                                                         |
| 16.   | [How do you perform API calls in React Native?](#question-16-how-do-you-perform-api-calls-in-react-native)                                                                     |
| 17.   | [What is the difference between fetch and Axios?](#question-17-what-is-the-difference-between-fetch-and-axios)                                                                 |
| 18.   | [How do you handle offline support in React Native apps?](#question-18-how-do-you-handle-offline-support-in-react-native-apps)                                                 |
| 19.   | [What is AsyncStorage and how do you use it?](#question-19-what-is-asyncstorage-and-how-do-you-use-it)                                                                         |
| 20.   | [What is SecureStore in Expo?](#question-20-what-is-securestore-in-expo)                                                                                                       |

## Question 1. What are **TextInput** components and how do you handle input changes?

# Short answer

`TextInput` is the built-in React Native component used to capture user input (single-line or multi-line). Input changes are typically handled using the `onChangeText` callback with React state (`useState`) to create a **controlled component**, ensuring the UI always reflects the latest state.

---

# Explanation

`TextInput` is React Native's equivalent of the HTML `<input>` or `<textarea>` element. It provides a native text entry experience on both Android and iOS while exposing a consistent JavaScript API.

## Common TextInput Props

- `value` – Current value (controlled input)
- `onChangeText` – Called whenever text changes
- `placeholder` – Hint text
- `keyboardType` – Numeric, email, phone, etc.
- `secureTextEntry` – Password fields
- `multiline` – Multiple lines
- `editable` – Enable/disable editing
- `maxLength` – Restrict input length
- `autoCapitalize`
- `autoCorrect`
- `returnKeyType`
- `onSubmitEditing`
- `onFocus`
- `onBlur`

---

## Controlled vs Uncontrolled Inputs

### Controlled (Recommended)

The input value lives inside React state.

**Advantages**

- Predictable UI
- Easy validation
- Form libraries work well
- Easy to reset/update values
- Better debugging

```tsx
const [name, setName] = useState("");

<TextInput value={name} onChangeText={setName} />;
```

This is the recommended approach for almost all production apps.

---

### Uncontrolled

You can use `defaultValue` and read the value later using a ref.

Useful only in rare cases where minimizing re-renders is more important than state synchronization.

```tsx
<TextInput defaultValue="John" />
```

---

## Handling Input Changes

The simplest approach is:

```tsx
const [email, setEmail] = useState("");

<TextInput value={email} onChangeText={setEmail} />;
```

Or with custom logic:

```tsx
const handleEmailChange = (text: string) => {
  setEmail(text.trim());
};

<TextInput value={email} onChangeText={handleEmailChange} />;
```

This allows:

- validation
- formatting
- analytics
- API search
- debouncing

---

## Handling Multiple Inputs

Instead of multiple states:

```tsx
const [name, setName] = useState("");
const [email, setEmail] = useState("");
```

Many teams prefer a single object:

```tsx
const [form, setForm] = useState({
  name: "",
  email: "",
});

const updateField = (key: keyof typeof form, value: string) => {
  setForm((prev) => ({
    ...prev,
    [key]: value,
  }));
};
```

Usage:

```tsx
<TextInput
  value={form.name}
  onChangeText={(text) => updateField('name', text)}
/>

<TextInput
  value={form.email}
  onChangeText={(text) => updateField('email', text)}
/>
```

For large forms, libraries like **React Hook Form** or **Formik** help reduce boilerplate.

---

## Rendering Behavior

Every call to `setState` triggers a React render for the component. Since typing generates frequent updates:

- Keep state as local as possible.
- Avoid placing rapidly changing input state high in the component tree.
- Split complex screens into smaller components.
- Memoize unrelated child components with `React.memo`.

Example:

```tsx
const Header = React.memo(() => {
  return <Text>Profile</Text>;
});
```

Typing won't re-render `Header` if its props don't change.

---

## Component Architecture

A scalable form architecture typically separates:

```
Screen
 ├── Form
 │     ├── InputField
 │     ├── EmailField
 │     ├── PasswordField
 │     └── SubmitButton
```

Each field manages only its own rendering while the parent manages form state or delegates it to a form library.

For large applications:

- React Hook Form
- Zustand
- Redux Toolkit (when form state must be global)

are common choices depending on requirements.

---

## New Architecture (Fabric & TurboModules)

`TextInput` is a core native component.

With the **New Architecture (Fabric)**:

- Faster rendering pipeline
- Better synchronization between React and native UI
- Reduced bridge overhead
- More efficient updates during rapid typing

Because `TextInput` generates many events, Fabric helps reduce latency compared to the legacy bridge-based architecture, particularly in complex screens.

---

# Example

**Stack:** Expo + React Native + TypeScript

### Create the project

```bash
npx create-expo-app TextInputDemo --template expo-template-blank-typescript

cd TextInputDemo

npx expo start
```

**App.tsx**

```tsx
import React, { useState } from "react";
import { SafeAreaView, Text, TextInput, StyleSheet } from "react-native";

export default function App() {
  const [name, setName] = useState("");

  return (
    <SafeAreaView style={styles.container}>
      <TextInput
        style={styles.input}
        placeholder="Enter your name"
        value={name}
        onChangeText={setName}
        autoCapitalize="words"
        returnKeyType="done"
      />

      <Text>Hello, {name || "Guest"}!</Text>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    padding: 20,
  },
  input: {
    borderWidth: 1,
    padding: 12,
    marginBottom: 20,
    borderRadius: 8,
  },
});
```

Run:

```bash
npx expo start
```

Press **i** for iOS simulator or **a** for Android emulator (or scan the QR code with Expo Go).

---

# Tooling & Setup

- **Preferred:** Expo (fast setup, OTA updates, excellent developer experience) or React Native CLI for apps requiring extensive native customization.
- **Avoid:** Create React App (CRA); it is deprecated and intended for web, not mobile.
- **Bundler:** Metro is the standard JavaScript bundler for React Native. Expo uses Metro under the hood.
- **Modules:** Modern React Native projects increasingly use ES Modules (`import`/`export`), while Metro and Babel handle compatibility with the native runtime.

---

# Performance

For text-heavy screens:

- Use `React.memo` for unrelated child components.
- Memoize callbacks with `useCallback` when passing them deeply.
- Use `useMemo` only for expensive derived values.
- Debounce API calls triggered by `onChangeText` (e.g., search) to avoid excessive network requests.
- Optimize images and avoid unnecessary layout work while typing.
- Prefer the New Architecture (Fabric/TurboModules) for lower event-processing overhead and improved responsiveness.

Profiling tools:

- React DevTools Profiler
- Flipper (where supported)
- React Native Performance Monitor (`Show Perf Monitor`)
- Native profilers (Android Studio Profiler, Xcode Instruments) for CPU, memory, and rendering analysis

---

# Testing

**Unit/Integration (Jest + React Native Testing Library)**

```tsx
import { fireEvent, render } from "@testing-library/react-native";
import App from "./App";

test("updates text input", () => {
  const { getByPlaceholderText, getByText } = render(<App />);

  fireEvent.changeText(getByPlaceholderText("Enter your name"), "Alice");

  expect(getByText("Hello, Alice!")).toBeTruthy();
});
```

Run:

```bash
npm test
```

**End-to-End**

Use **Detox** (native) or **Maestro** (cross-platform) to verify typing, keyboard behavior, and form submission flows on real devices or emulators.

---

# Ops & Deployment

- Use structured logging (e.g., console in development, centralized logging in production).
- Validate and sanitize user input before sending it to APIs.
- Cache drafts locally with AsyncStorage or a secure storage solution when offline support is needed.
- Configure CI/CD with Expo Application Services (EAS), GitHub Actions, or Bitrise for automated builds and testing.
- Plan app distribution through the App Store and Google Play. If using Expo, leverage OTA updates (Expo Updates) for JavaScript changes while respecting platform policies.

---

# Pitfalls

- Don't mix `defaultValue` and `value`; use controlled inputs unless there's a specific need for uncontrolled behavior.
- Avoid expensive computations or API calls directly inside `onChangeText`; debounce or throttle when appropriate.
- Don't store rapidly changing input state in global state unless multiple parts of the app truly need it.

## Question 2. How do you manage multiple screens in React Native?

## Question 3. What is **React Navigation** and why is it used?

## Question 4. Explain the difference between **StackNavigator**, **TabNavigator**, and **DrawerNavigator**

## Question 5. How do you pass data between screens in React Native?

## Question 6. How do you debug a React Native app?

## Question 7. How do you handle errors in React Native?

## Question 8. Explain the purpose of **keyExtractor** in FlatList

## Question 9. What is the difference between **stateful** and **stateless** components?

## Question 10. How do you reload an app in development mode?

## Question 11. What is **Context API** in React Native?

## Question 12. How do you manage global state in React Native?

## Question 13. Explain the difference between **Context API** and **Redux**

## Question 14. What is **Redux Thunk** and why is it used?

## Question 15. What is **Redux Saga**?

## Question 16. How do you perform API calls in React Native?

## Question 17. What is the difference between **fetch** and **Axios**?

## Question 18. How do you handle offline support in React Native apps?

## Question 19. What is **AsyncStorage** and how do you use it?

## Question 20. What is **SecureStore** in Expo?
