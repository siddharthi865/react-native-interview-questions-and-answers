# Set 18

| S.No. | Question                                                                                                                                  |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you create a custom reusable button component?](#question-1-how-do-you-create-a-custom-reusable-button-component)                 |
| 2.    | [How do you handle complex forms using Formik?](#question-2-how-do-you-handle-complex-forms-using-formik)                                 |
| 3.    | [How do you handle form validation using Yup?](#question-3-how-do-you-handle-form-validation-using-yup)                                   |
| 4.    | [How do you reset form fields programmatically?](#question-4-how-do-you-reset-form-fields-programmatically)                               |
| 5.    | [How do you handle dependent dropdowns in forms?](#question-5-how-do-you-handle-dependent-dropdowns-in-forms)                             |
| 6.    | [How do you pre-fill form values dynamically?](#question-6-how-do-you-pre-fill-form-values-dynamically)                                   |
| 7.    | [How do you handle network timeouts in fetch?](#question-7-how-do-you-handle-network-timeouts-in-fetch)                                   |
| 8.    | [How do you retry API requests automatically?](#question-8-how-do-you-retry-api-requests-automatically)                                   |
| 9.    | [How do you implement optimistic updates in React Native apps?](#question-9-how-do-you-implement-optimistic-updates-in-react-native-apps) |
| 10.   | [How do you cancel API requests if a component unmounts?](#question-10-how-do-you-cancel-api-requests-if-a-component-unmounts)            |
| 11.   | [How do you implement skeleton loaders for components?](#question-11-how-do-you-implement-skeleton-loaders-for-components)                |
| 12.   | [How do you implement pull-to-refresh with custom indicators?](#question-12-how-do-you-implement-pull-to-refresh-with-custom-indicators)  |
| 13.   | [How do you cache API responses locally?](#question-13-how-do-you-cache-api-responses-locally)                                            |
| 14.   | [How do you handle app-wide notifications using EventEmitter?](#question-14-how-do-you-handle-app-wide-notifications-using-eventemitter)  |
| 15.   | [How do you use React Native SafeAreaContext for notches?](#question-15-how-do-you-use-react-native-safeareacontext-for-notches)          |
| 16.   | [How do you detect safe area insets dynamically?](#question-16-how-do-you-detect-safe-area-insets-dynamically)                            |
| 17.   | [How do you implement rounded images or avatars?](#question-17-how-do-you-implement-rounded-images-or-avatars)                            |
| 18.   | [How do you use React Native MaskedView for custom shapes?](#question-18-how-do-you-use-react-native-maskedview-for-custom-shapes)        |
| 19.   | [How do you implement progress bars dynamically?](#question-19-how-do-you-implement-progress-bars-dynamically)                            |
| 20.   | [How do you implement circular progress indicators?](#question-20-how-do-you-implement-circular-progress-indicators)                      |

## Question 1. How do you **create a custom reusable button component**?

# Short answer

Create a reusable button by encapsulating common behavior (styling, loading state, disabled state, accessibility, and press handling) into a single component that accepts props like `title`, `onPress`, `variant`, and `loading`. In modern React Native, use functional components with TypeScript, `Pressable` (preferred over `TouchableOpacity` for flexibility), and memoize the component if it renders frequently.

---

# Explanation

A reusable button is a **UI abstraction** that centralizes styling and behavior, making the codebase easier to maintain and ensuring consistency across the application.

A production-ready button typically supports:

- Multiple variants (`primary`, `secondary`, `outline`, `danger`)
- Loading state
- Disabled state
- Icons (optional)
- Custom styles
- Accessibility labels
- Press feedback
- Theme integration (light/dark mode)

### Component architecture

Instead of repeating this everywhere:

```tsx
<Pressable style={...}>
  <Text>Save</Text>
</Pressable>
```

Create:

```tsx
<AppButton title="Save" variant="primary" loading={isSaving} onPress={save} />
```

Now every screen shares the same implementation.

---

### Rendering behavior

The button only re-renders when its props change.

For lists containing many buttons:

- Wrap with `React.memo`
- Memoize callbacks using `useCallback`
- Avoid inline style objects
- Avoid recreating anonymous functions unnecessarily

Example:

```tsx
const handleSave = useCallback(() => {
  saveData();
}, []);
```

---

### State management trade-offs

The button should remain mostly **stateless**.

It should receive:

- loading
- disabled
- title

from the parent rather than managing business logic itself.

Good:

```tsx
<AppButton loading={isSaving} />
```

Avoid:

```tsx
const [loading, setLoading] = useState(false);
```

inside every reusable button unless it's purely for UI animations.

Business state belongs in:

- Context
- Redux Toolkit
- Zustand
- React Query
- Parent component

---

### New Architecture (Fabric & TurboModules)

Buttons are rendered through Fabric just like other RN components.

Benefits include:

- Faster mounting
- Better event prioritization
- Lower UI latency
- Synchronous layout improvements

Since `Pressable` is implemented efficiently in modern React Native, it works well with the New Architecture without additional changes.

---

# Example

### Create a new Expo TypeScript project

```bash
npx create-expo-app MyApp --template expo-template-blank-typescript

cd MyApp
npm start
```

### `AppButton.tsx`

```tsx
import React from "react";
import {
  ActivityIndicator,
  Pressable,
  StyleSheet,
  Text,
  ViewStyle,
} from "react-native";

type Props = {
  title: string;
  onPress: () => void;
  loading?: boolean;
  disabled?: boolean;
  variant?: "primary" | "secondary";
  style?: ViewStyle;
};

const AppButton = React.memo(
  ({
    title,
    onPress,
    loading = false,
    disabled = false,
    variant = "primary",
    style,
  }: Props) => {
    const background = variant === "primary" ? "#2563EB" : "#6B7280";

    return (
      <Pressable
        onPress={onPress}
        disabled={disabled || loading}
        accessibilityRole="button"
        style={({ pressed }) => [
          styles.button,
          { backgroundColor: background },
          pressed && styles.pressed,
          (disabled || loading) && styles.disabled,
          style,
        ]}
      >
        {loading ? (
          <ActivityIndicator color="#fff" />
        ) : (
          <Text style={styles.text}>{title}</Text>
        )}
      </Pressable>
    );
  },
);

export default AppButton;

const styles = StyleSheet.create({
  button: {
    paddingVertical: 14,
    borderRadius: 8,
    alignItems: "center",
  },
  text: {
    color: "#fff",
    fontWeight: "600",
    fontSize: 16,
  },
  pressed: {
    opacity: 0.8,
  },
  disabled: {
    opacity: 0.5,
  },
});
```

### Usage

```tsx
import React, { useCallback } from "react";
import { View } from "react-native";
import AppButton from "./AppButton";

export default function App() {
  const handlePress = useCallback(() => {
    console.log("Button pressed");
  }, []);

  return (
    <View style={{ flex: 1, justifyContent: "center", padding: 20 }}>
      <AppButton title="Continue" onPress={handlePress} variant="primary" />
    </View>
  );
}
```

Run:

```bash
npx expo start
```

Press **i** for iOS simulator or **a** for Android emulator.

---

# Tooling & Setup

**Recommended stack**

- Expo (fast setup, OTA updates, EAS integration)
- React Native CLI (when extensive native customization is required)

Avoid **Create React App (CRA)** because it targets web applications and is deprecated for modern React development, making it unsuitable for React Native.

### Module system

- Prefer **ES Modules** (`import` / `export`)
- Metro is React Native's default bundler and supports modern JavaScript and TypeScript without additional configuration.

---

# Performance

For frequently rendered buttons:

- Use `React.memo`
- Memoize `onPress` with `useCallback`
- Prefer `Pressable` over older touchable components
- Keep style objects outside render when possible
- Avoid unnecessary state inside the button
- If rendering hundreds of buttons in a `FlatList`, ensure stable `keyExtractor` values and memoized item components.

### Profiling

Use:

- React DevTools Profiler
- Flipper React DevTools
- React Native Performance Monitor
- Hermes profiling for JavaScript execution

### Native performance

Under the New Architecture:

- Fabric reduces rendering overhead.
- TurboModules improve communication with native modules, though a standard button typically doesn't require native bridge interactions.

---

# Testing

### Unit / Integration

Use:

- Jest
- React Native Testing Library

Example:

```tsx
import { render, fireEvent } from "@testing-library/react-native";
import AppButton from "../AppButton";

test("calls onPress", () => {
  const onPress = jest.fn();

  const { getByText } = render(<AppButton title="Save" onPress={onPress} />);

  fireEvent.press(getByText("Save"));

  expect(onPress).toHaveBeenCalled();
});
```

Run:

```bash
npm test
```

### E2E

Use:

- Detox (React Native CLI)
- Maestro (works well with Expo and native apps)

Example:

```bash
maestro test flows/login.yaml
```

---

# Ops & Deployment

Logging:

- Use structured logging instead of excessive `console.log` in production.
- Integrate crash reporting (e.g., Crashlytics or Sentry).

Error handling:

- Prevent duplicate taps during async operations with the `loading` or `disabled` props.
- Display user-friendly error messages via toasts or alerts.

Offline:

- Queue actions if needed and synchronize when connectivity is restored.
- Cache relevant data using libraries like AsyncStorage or a local database.

CI/CD:

- Expo: EAS Build + EAS Update
- React Native CLI: GitHub Actions or Bitrise
- Automate testing, linting, and builds before release.

Deployment:

- Validate accessibility.
- Test on multiple screen sizes and platforms.
- Use OTA updates (Expo EAS Update or CodePush where appropriate) for eligible JavaScript changes.

---

# Pitfalls

- **Don't duplicate button styles** across screens—use a shared reusable component.
- **Avoid inline anonymous callbacks** in performance-critical lists; memoize handlers with `useCallback`.
- **Don't mix business logic into the button**—keep it focused on presentation and interaction.

## Question 2. How do you **handle complex forms using Formik**?

## Question 3. How do you **handle form validation using Yup**?

## Question 4. How do you **reset form fields programmatically**?

## Question 5. How do you **handle dependent dropdowns** in forms?

## Question 6. How do you **pre-fill form values dynamically**?

## Question 7. How do you **handle network timeouts in fetch**?

## Question 8. How do you **retry API requests automatically**?

## Question 9. How do you **implement optimistic updates** in React Native apps?

## Question 10. How do you **cancel API requests if a component unmounts**?

## Question 11. How do you **implement skeleton loaders for components**?

## Question 12. How do you **implement pull-to-refresh with custom indicators**?

## Question 13. How do you **cache API responses locally**?

## Question 14. How do you **handle app-wide notifications using EventEmitter**?

## Question 15. How do you **use React Native SafeAreaContext** for notches?

## Question 16. How do you **detect safe area insets dynamically**?

## Question 17. How do you **implement rounded images or avatars**?

## Question 18. How do you **use React Native MaskedView for custom shapes**?

## Question 19. How do you **implement progress bars** dynamically?

## Question 20. How do you **implement circular progress indicators**?
