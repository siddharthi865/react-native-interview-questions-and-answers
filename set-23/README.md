# Set 23

| S.No. | Question                                                                                                                                                         |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you use react-native-reanimated for performant animations?](#question-1-how-do-you-use-react-native-reanimated-for-performant-animations)                |
| 2.    | [How do you combine react-native-reanimated with gesture-handler?](#question-2-how-do-you-combine-react-native-reanimated-with-gesture-handler)                  |
| 3.    | [How do you animate items in FlatList on scroll?](#question-3-how-do-you-animate-items-in-flatlist-on-scroll)                                                    |
| 4.    | [How do you create parallax scrolling effects?](#question-4-how-do-you-create-parallax-scrolling-effects)                                                        |
| 5.    | [How do you implement collapsible toolbars?](#question-5-how-do-you-implement-collapsible-toolbars)                                                              |
| 6.    | [How do you implement pull-to-refresh with custom animations?](#question-6-how-do-you-implement-pull-to-refresh-with-custom-animations)                          |
| 7.    | [How do you implement swipeable tab navigation?](#question-7-how-do-you-implement-swipeable-tab-navigation)                                                      |
| 8.    | [How do you implement nested navigators with independent states?](#question-8-how-do-you-implement-nested-navigators-with-independent-states)                    |
| 9.    | [How do you conditionally render navigation based on role/permissions?](#question-9-how-do-you-conditionally-render-navigation-based-on-rolepermissions)         |
| 10.   | [How do you handle offline-first API calls with caching?](#question-10-how-do-you-handle-offline-first-api-calls-with-caching)                                   |
| 11.   | [How do you implement optimistic UI updates with Redux or RTK Query?](#question-11-how-do-you-implement-optimistic-ui-updates-with-redux-or-rtk-query)           |
| 12.   | [How do you perform background fetch using react-native-background-fetch?](#question-12-how-do-you-perform-background-fetch-using-react-native-background-fetch) |
| 13.   | [How do you implement silent push notifications?](#question-13-how-do-you-implement-silent-push-notifications)                                                   |
| 14.   | [How do you track app opens and user engagement with analytics?](#question-14-how-do-you-track-app-opens-and-user-engagement-with-analytics)                     |
| 15.   | [How do you use Flipper to debug network requests?](#question-15-how-do-you-use-flipper-to-debug-network-requests)                                               |
| 16.   | [How do you mock API calls during testing?](#question-16-how-do-you-mock-api-calls-during-testing)                                                               |
| 17.   | [How do you write unit tests for custom hooks?](#question-17-how-do-you-write-unit-tests-for-custom-hooks)                                                       |
| 18.   | [How do you test FlatList and SectionList rendering?](#question-18-how-do-you-test-flatlist-and-sectionlist-rendering)                                           |
| 19.   | [How do you simulate gestures in testing environments?](#question-19-how-do-you-simulate-gestures-in-testing-environments)                                       |
| 20.   | [How do you perform accessibility testing?](#question-20-how-do-you-perform-accessibility-testing)                                                               |

## Question 1. How do you **use react-native-reanimated for performant animations**?

# Short answer

**React Native Reanimated** is a high-performance animation library that executes animation logic on the **UI thread** using **worklets**, avoiding frequent JavaScript-to-native communication. This results in smooth 60/120 FPS animations, gesture-driven interactions, and interruptible animations, especially under React Native's **New Architecture (Fabric + JSI)**.

---

# Explanation

`react-native-reanimated` is the recommended solution for production-grade animations in modern React Native apps because it minimizes dependence on the JavaScript thread.

### Why Reanimated instead of Animated API?

| React Native Animated                             | Reanimated                                        |
| ------------------------------------------------- | ------------------------------------------------- |
| Mostly JS-driven (unless native driver supported) | Runs animations on UI thread                      |
| Can stutter if JS thread is busy                  | Continues smoothly even if JS thread blocks       |
| Limited gesture support                           | Excellent integration with Gesture Handler        |
| Basic animations                                  | Complex physics, shared values, layout animations |

---

## Core concepts

### 1. Shared Values

Shared values hold mutable animation state.

```tsx
const translateX = useSharedValue(0);
```

Unlike React state:

- updates don't trigger React re-render
- value lives on UI thread
- extremely fast

---

### 2. Worklets

Functions marked automatically by Reanimated execute on the UI thread.

Examples:

```tsx
useAnimatedStyle(() => {
  return {
    transform: [{ translateX: translateX.value }],
  };
});
```

This avoids:

```
JS Thread
   ↓
Bridge
   ↓
Native
```

Instead:

```
UI Thread
   ↓
Animation
```

---

### 3. Animated Styles

```tsx
const style = useAnimatedStyle(() => ({
  opacity: opacity.value,
}));
```

Animated styles react automatically whenever shared values change.

---

### 4. Animation Helpers

Common helpers include:

- `withTiming()`
- `withSpring()`
- `withDecay()`
- `withRepeat()`
- `withSequence()`
- `withDelay()`

Example:

```tsx
translateX.value = withSpring(200);
```

---

### 5. Gesture Integration

Reanimated works seamlessly with **React Native Gesture Handler**.

Example:

```tsx
Pan()
    .onUpdate(...)
```

Everything runs on the UI thread.

Ideal for:

- swipe cards
- bottom sheets
- image viewers
- drag & drop
- maps

---

## Rendering behavior

React component:

```
Render
   ↓
Shared Value updated
        ↓
Animated Style updates
             ↓
No React render occurs
```

Meaning:

- React stays idle
- only the view updates
- very low CPU usage

---

## React Native New Architecture

Reanimated fully embraces:

- Fabric renderer
- JSI
- TurboModules

Instead of:

```
JS
 ↓
Bridge
 ↓
Native
```

It executes worklets directly through JSI:

```
JSI
 ↓
UI Runtime
 ↓
Animation
```

Benefits:

- lower latency
- smoother gestures
- fewer dropped frames
- less bridge overhead

---

## When should you use Reanimated?

Use it for:

- gesture animations
- shared element transitions
- bottom sheets
- drawers
- parallax scrolling
- swipeable lists
- interactive charts
- image zoom
- drag & drop

Avoid using it for:

- simple fade-ins only
- static transitions where the built-in Animated API is sufficient

---

# Example

**Expo (TypeScript)**

Create the project:

```bash
npx create-expo-app MyApp --template expo-template-blank-typescript
```

Install:

```bash
npx expo install react-native-reanimated
```

Add the Reanimated Babel plugin (per the installed version's setup instructions) and restart Metro.

Run:

```bash
npx expo start
```

```tsx
import React from "react";
import { Button, StyleSheet } from "react-native";
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
} from "react-native-reanimated";

export default function App() {
  const offset = useSharedValue(0);

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: offset.value }],
  }));

  return (
    <>
      <Animated.View style={[styles.box, animatedStyle]} />

      <Button
        title="Move"
        onPress={() => {
          offset.value = withSpring(offset.value === 0 ? 200 : 0);
        }}
      />
    </>
  );
}

const styles = StyleSheet.create({
  box: {
    width: 100,
    height: 100,
    backgroundColor: "tomato",
    marginTop: 100,
  },
});
```

Pressing the button updates the shared value, and the animation runs on the UI thread without causing a React re-render.

---

# Tooling & Setup

- **Preferred stack:** Expo SDK (or React Native CLI for apps requiring custom native configuration). Expo simplifies installation and upgrades while supporting Reanimated.
- **Avoid CRA:** `create-react-app` is for web and is deprecated for React Native development.
- **Metro:** React Native uses the Metro bundler. After configuring Reanimated (such as the Babel plugin), restart Metro and clear its cache if needed:

  ```bash
  npx expo start -c
  ```

- **Modules:** Modern React Native projects increasingly use **ES Modules** (`import`/`export`). Metro handles transpilation, while some tooling may still use CommonJS internally.

---

# Performance

### Why it's fast

- Animations execute on the **UI thread**.
- Shared values update views without React re-renders.
- JSI removes much of the bridge overhead under the New Architecture.

### Optimization tips

- Prefer `useSharedValue()` over React state for animation state.
- Keep worklets lightweight; avoid expensive calculations inside `useAnimatedStyle`.
- Memoize non-animation props with `React.memo`, `useMemo`, and `useCallback` to reduce unrelated renders.
- Use `useDerivedValue` for values computed from other shared values.
- Optimize images (appropriate sizes, caching) when animating image-heavy screens.
- Profile using:
  - React DevTools Profiler (React render performance)
  - React Native Perf Monitor (FPS)
  - Flipper (where supported by your React Native version/workflow) for performance inspection.

---

# Testing

### Unit/Integration

Use **Jest** with **React Native Testing Library**. Mock Reanimated using its provided Jest mock.

```bash
npm test
```

Example Jest setup:

```ts
jest.mock("react-native-reanimated", () =>
  require("react-native-reanimated/mock"),
);
```

### End-to-End

Use **Detox** or **Maestro** to verify user interactions and animation-driven flows on real or virtual devices.

---

# Ops & Deployment

- Log animation-related errors with tools such as Sentry while avoiding excessive logging inside animation callbacks.
- Ensure graceful degradation if animations are disabled via accessibility settings (e.g., reduced motion).
- Cache assets for smoother startup and offline experiences.
- CI/CD:
  - **Expo:** EAS Build and EAS Update (OTA updates where applicable).
  - **React Native CLI:** GitHub Actions or Bitrise for automated builds and testing.

- Test animation performance on lower-end Android devices before releasing to the App Store or Google Play.

---

# Pitfalls

- **Don't use React state for every animation frame**—use shared values instead.
- **Avoid heavy computations inside worklets**; move expensive logic outside or derive values efficiently.
- **Ensure Reanimated is configured correctly** (including the required Babel configuration for your version); misconfiguration often causes worklets to fall back or fail.

## Question 2. How do you **combine react-native-reanimated with gesture-handler**?

## Question 3. How do you **animate items in FlatList on scroll**?

## Question 4. How do you **create parallax scrolling effects**?

## Question 5. How do you **implement collapsible toolbars**?

## Question 6. How do you **implement pull-to-refresh with custom animations**?

## Question 7. How do you **implement swipeable tab navigation**?

## Question 8. How do you **implement nested navigators with independent states**?

## Question 9. How do you **conditionally render navigation based on role/permissions**?

## Question 10. How do you **handle offline-first API calls with caching**?

## Question 11. How do you **implement optimistic UI updates with Redux or RTK Query**?

## Question 12. How do you **perform background fetch using react-native-background-fetch**?

## Question 13. How do you **implement silent push notifications**?

## Question 14. How do you **track app opens and user engagement with analytics**?

## Question 15. How do you **use Flipper to debug network requests**?

## Question 16. How do you **mock API calls during testing**?

## Question 17. How do you **write unit tests for custom hooks**?

## Question 18. How do you **test FlatList and SectionList rendering**?

## Question 19. How do you **simulate gestures in testing environments**?

## Question 20. How do you **perform accessibility testing**?
