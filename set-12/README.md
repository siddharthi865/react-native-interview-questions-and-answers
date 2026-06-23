# Set 12

| S.No. | Question                                                                                                                                                             |
| ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you focus a TextInput programmatically?](#question-1-how-do-you-focus-a-textinput-programmatically)                                                          |
| 2.    | [How do you show and hide password input?](#question-2-how-do-you-show-and-hide-password-input)                                                                      |
| 3.    | [How do you display a modal component on button press?](#question-3-how-do-you-display-a-modal-component-on-button-press)                                            |
| 4.    | [How do you handle orientation changes in styles?](#question-4-how-do-you-handle-orientation-changes-in-styles)                                                      |
| 5.    | [How do you set global default styles for Text and View?](#question-5-how-do-you-set-global-default-styles-for-text-and-view)                                        |
| 6.    | [How do you show an activity indicator (spinner) during loading?](#question-6-how-do-you-show-an-activity-indicator-spinner-during-loading)                          |
| 7.    | [How do you log warnings and errors in development?](#question-7-how-do-you-log-warnings-and-errors-in-development)                                                  |
| 8.    | [How do you conditionally render components using ternary operators?](#question-8-how-do-you-conditionally-render-components-using-ternary-operators)                |
| 9.    | [How do you use Fragment in React Native?](#question-9-how-do-you-use-fragment-in-react-native)                                                                      |
| 10.   | [How do you prevent keyboard from covering input fields on Android and iOS?](#question-10-how-do-you-prevent-keyboard-from-covering-input-fields-on-android-and-ios) |
| 11.   | [How do you implement Redux Persist to save state across app restarts?](#question-11-how-do-you-implement-redux-persist-to-save-state-across-app-restarts)           |
| 12.   | [How do you implement offline form submissions?](#question-12-how-do-you-implement-offline-form-submissions)                                                         |
| 13.   | [How do you handle network errors gracefully?](#question-13-how-do-you-handle-network-errors-gracefully)                                                             |
| 14.   | [How do you monitor network requests in React Native?](#question-14-how-do-you-monitor-network-requests-in-react-native)                                             |
| 15.   | [How do you show toast messages on Android and iOS?](#question-15-how-do-you-show-toast-messages-on-android-and-ios)                                                 |
| 16.   | [How do you use Snackbar in React Native?](#question-16-how-do-you-use-snackbar-in-react-native)                                                                     |
| 17.   | [How do you implement collapsible sections in FlatList?](#question-17-how-do-you-implement-collapsible-sections-in-flatlist)                                         |
| 18.   | [How do you handle multiple input fields with a single state object?](#question-18-how-do-you-handle-multiple-input-fields-with-a-single-state-object)               |
| 19.   | [How do you implement pull-to-refresh with SectionList?](#question-19-how-do-you-implement-pull-to-refresh-with-sectionlist)                                         |
| 20.   | [How do you animate height changes smoothly?](#question-20-how-do-you-animate-height-changes-smoothly)                                                               |

## Question 1. How do you **focus a TextInput programmatically**?

# Short answer

You can focus a `TextInput` programmatically by storing a reference to it with `useRef` and calling its `.focus()` method. This is commonly used after screen navigation, form validation, OTP flows, search bars, and moving to the next input.

---

# Explanation

`TextInput` exposes imperative methods such as:

- `focus()` – Gives focus to the input and opens the keyboard.
- `blur()` – Removes focus and dismisses the keyboard.
- `clear()` – Clears the text.
- `isFocused()` – Returns whether the input currently has focus.

In modern React Native, the recommended approach is using `useRef`.

```tsx
const inputRef = useRef<TextInput>(null);

inputRef.current?.focus();
```

## Common use cases

### 1. Auto-focus when a screen opens

```tsx
useEffect(() => {
  inputRef.current?.focus();
}, []);
```

Useful for:

- Login screens
- Search screens
- Chat apps

---

### 2. Focus next field

Example:

```
Name → Email → Password
```

When the user presses **Next**, move focus automatically.

```tsx
onSubmitEditing={() => emailRef.current?.focus()}
```

---

### 3. Focus invalid field after validation

Instead of just showing an error:

```text
Email is required
```

Jump directly to the field.

```tsx
if (!email) {
  emailRef.current?.focus();
}
```

This improves UX significantly.

---

### 4. OTP/PIN inputs

Each digit automatically focuses the next input.

```
[1] [2] [ ] [ ]
```

---

## Rendering behavior

Calling `focus()`:

- **Does not trigger a React re-render.**
- Performs an imperative action directly on the native component.
- Uses the native text input underneath (UIKit on iOS, EditText on Android).
- Works well with React's declarative rendering because refs are used only for imperative UI actions.

With the **New Architecture (Fabric)**, imperative commands like `focus()` are dispatched more efficiently through the new rendering pipeline, reducing bridge overhead compared to the legacy architecture.

---

# Example

**Scaffold (Expo + TypeScript)**

```bash
npx create-expo-app@latest FocusInputDemo --template expo-template-blank-typescript

cd FocusInputDemo
npm start
```

`App.tsx`

```tsx
import React, { useRef } from "react";
import { View, TextInput, Button, StyleSheet } from "react-native";

export default function App() {
  const inputRef = useRef<TextInput>(null);

  return (
    <View style={styles.container}>
      <TextInput
        ref={inputRef}
        placeholder="Enter your name"
        style={styles.input}
        returnKeyType="done"
      />

      <Button title="Focus Input" onPress={() => inputRef.current?.focus()} />
    </View>
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
    borderColor: "#ccc",
    padding: 12,
    marginBottom: 16,
    borderRadius: 6,
  },
});
```

Run:

```bash
# Expo
npm start

# Android
npm run android

# iOS
npm run ios
```

### Focusing the next input

```tsx
const emailRef = useRef<TextInput>(null);

<TextInput
  returnKeyType="next"
  onSubmitEditing={() => emailRef.current?.focus()}
/>

<TextInput
  ref={emailRef}
  returnKeyType="done"
/>
```

---

# Tooling & Setup

- **Recommended stack:** Expo (quick setup) or React Native CLI (when you need custom native modules).
- Avoid **Create React App (CRA)**—it is deprecated and intended for web, not React Native.
- **Bundler:** Metro is the standard JavaScript bundler for React Native.
- **Modules:** Modern React Native projects support ES Modules (`import`/`export`). Some build tooling still interoperates with CommonJS for compatibility, but application code should prefer ESM.
- With React Native's **New Architecture**, `TextInput` interactions benefit from **Fabric** (new renderer) and **TurboModules**, improving responsiveness and reducing bridge overhead.

---

# Performance

Focusing a `TextInput` is inexpensive, but surrounding patterns matter:

- Store refs with `useRef` so they persist across renders without causing re-renders.
- Use `React.memo` to avoid unnecessary re-renders of form field components.
- Use `useCallback` for event handlers passed to memoized children.
- Use `useMemo` for expensive derived values in large forms.
- Profile with:
  - React DevTools Profiler
  - Flipper
  - React Native Performance Monitor

- Optimize images and avoid heavy work on the JS thread while the keyboard is appearing.
- For custom native input components, prefer the New Architecture (Fabric/TurboModules) to minimize bridge costs.

---

# Testing

**Unit/Integration (Jest + React Native Testing Library)**

```tsx
fireEvent.press(screen.getByText("Focus Input"));

expect(screen.getByPlaceholderText("Enter your name")).toBeTruthy();
```

Run:

```bash
npm test
```

**E2E**

Use **Detox** or **Maestro** to verify that tapping a button focuses the expected field and opens the keyboard.

---

# Ops & Deployment

- Log unexpected focus failures or keyboard issues with tools such as Sentry or Crashlytics.
- Gracefully handle keyboard interactions on both iOS and Android (for example, combine with `KeyboardAvoidingView` when appropriate).
- Cache form state locally if users may lose connectivity during long forms.
- CI/CD:
  - Expo: EAS Build + EAS Update (OTA updates where appropriate)
  - React Native CLI: GitHub Actions or Bitrise for automated builds and tests

- Validate focus behavior on both platforms before App Store and Play Store releases, as keyboard behavior can differ.

---

# Pitfalls

- Don't call `focus()` before the component is mounted—the ref will be `null`.
- Don't create refs inside loops or conditionals; keep them stable with `useRef`.
- If focus doesn't work after navigation or animations, wait until the screen is fully mounted (for example, using `useEffect`, `InteractionManager`, or a navigation focus callback).

## Question 2. How do you **show and hide password input**?

## Question 3. How do you **display a modal component** on button press?

## Question 4. How do you **handle orientation changes in styles**?

## Question 5. How do you **set global default styles** for Text and View?

## Question 6. How do you **show an activity indicator (spinner) during loading**?

## Question 7. How do you **log warnings and errors** in development?

## Question 8. How do you **conditionally render components using ternary operators**?

## Question 9. How do you **use Fragment in React Native**?

## Question 10. How do you **prevent keyboard from covering input fields** on Android and iOS?

## Question 11. How do you implement **Redux Persist** to save state across app restarts?

## Question 12. How do you implement **offline form submissions**?

## Question 13. How do you **handle network errors gracefully**?

## Question 14. How do you **monitor network requests** in React Native?

## Question 15. How do you **show toast messages** on Android and iOS?

## Question 16. How do you **use Snackbar** in React Native?

## Question 17. How do you implement **collapsible sections in FlatList**?

## Question 18. How do you **handle multiple input fields with a single state object**?

## Question 19. How do you **implement pull-to-refresh with SectionList**?

## Question 20. How do you **animate height changes smoothly**?
