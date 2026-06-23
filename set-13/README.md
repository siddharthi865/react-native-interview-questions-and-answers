# Set 13

| S.No. | Question                                                                                                                                               |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How do you animate opacity changes?](#question-1-how-do-you-animate-opacity-changes)                                                                  |
| 2.    | [How do you animate translation (move components)?](#question-2-how-do-you-animate-translation-move-components)                                        |
| 3.    | [How do you combine multiple animations in sequence or parallel?](#question-3-how-do-you-combine-multiple-animations-in-sequence-or-parallel)          |
| 4.    | [How do you handle swipe gestures for items?](#question-4-how-do-you-handle-swipe-gestures-for-items)                                                  |
| 5.    | [How do you create swipeable rows with actions?](#question-5-how-do-you-create-swipeable-rows-with-actions)                                            |
| 6.    | [How do you handle keyboard events (keyboardDidShow, keyboardDidHide)?](#question-6-how-do-you-handle-keyboard-events-keyboarddidshow-keyboarddidhide) |
| 7.    | [How do you implement responsive font sizes?](#question-7-how-do-you-implement-responsive-font-sizes)                                                  |
| 8.    | [How do you handle orientation-specific image layouts?](#question-8-how-do-you-handle-orientation-specific-image-layouts)                              |
| 9.    | [How do you detect device type (tablet vs phone)?](#question-9-how-do-you-detect-device-type-tablet-vs-phone)                                          |
| 10.   | [How do you fetch and display paginated API data?](#question-10-how-do-you-fetch-and-display-paginated-api-data)                                       |
| 11.   | [How do you cancel API requests if user navigates away?](#question-11-how-do-you-cancel-api-requests-if-user-navigates-away)                           |
| 12.   | [How do you debounce API calls while typing?](#question-12-how-do-you-debounce-api-calls-while-typing)                                                 |
| 13.   | [How do you implement infinite scroll with pagination?](#question-13-how-do-you-implement-infinite-scroll-with-pagination)                             |
| 14.   | [How do you display error messages inline?](#question-14-how-do-you-display-error-messages-inline)                                                     |
| 15.   | [How do you implement search with filtering?](#question-15-how-do-you-implement-search-with-filtering)                                                 |
| 16.   | [How do you integrate with Firebase Firestore?](#question-16-how-do-you-integrate-with-firebase-firestore)                                             |
| 17.   | [How do you integrate Firebase Realtime Database?](#question-17-how-do-you-integrate-firebase-realtime-database)                                       |
| 18.   | [How do you implement offline caching with AsyncStorage?](#question-18-how-do-you-implement-offline-caching-with-asyncstorage)                         |
| 19.   | [How do you share data between components without Redux?](#question-19-how-do-you-share-data-between-components-without-redux)                         |
| 20.   | [How do you manage multiple themes dynamically?](#question-20-how-do-you-manage-multiple-themes-dynamically)                                           |

## Question 1. How do you **animate opacity changes**?

# Short answer

Animate opacity changes by driving the `opacity` style property with either the React Native **Animated API** (`Animated.Value` + `Animated.timing`) or **React Native Reanimated** (preferred for complex and high-performance animations). For simple fade-in/fade-out effects, `Animated.timing` with `useNativeDriver: true` is sufficient because opacity is a natively supported property.

---

# Explanation

Opacity animation is one of the cheapest and most common UI animations in React Native. Typical use cases include:

- Fade in/out loaders
- Showing or hiding modals
- Screen transitions
- Button press feedback
- Skeleton loaders

## Using the Animated API

The built-in `Animated` API works well for simple animations.

Typical flow:

1. Create an `Animated.Value`.
2. Animate it using `Animated.timing()`.
3. Bind it to the component's `opacity` style.

```text
Animated.Value
       │
Animated.timing()
       │
Animated.View
       │
opacity style
```

Example:

- 0 → completely transparent
- 1 → fully visible

---

## Why `useNativeDriver: true`?

For opacity:

```tsx
useNativeDriver: true;
```

moves the animation to the native UI thread.

Benefits:

- Smooth 60 FPS animations
- Doesn't depend on JavaScript thread
- Less jank during heavy JS work

Opacity is one of the properties fully supported by the native driver.

---

## React Native Reanimated (Recommended)

For modern React Native apps using the New Architecture (Fabric), **React Native Reanimated** is generally the preferred animation library because it:

- Runs animations on the UI thread
- Supports gesture-driven animations
- Integrates with shared values
- Avoids bridge overhead
- Works well with Fabric and TurboModules

Example workflow:

```text
SharedValue
      ↓
withTiming()
      ↓
useAnimatedStyle()
      ↓
Animated.View
```

Reanimated is ideal for:

- Complex transitions
- Gesture animations
- Interactive UI
- Navigation animations

---

## Rendering behavior

Changing opacity:

- does **not** trigger React re-renders after the animation starts
- updates only the animated value
- keeps layout unchanged
- is more efficient than conditionally mounting/unmounting components when you simply need a fade effect

If the component is completely hidden and no longer needed, unmount it after the fade-out completes to reduce memory usage.

---

## State management considerations

Avoid storing animation progress in React state:

❌

```tsx
const [opacity, setOpacity] = useState(1);
```

Every update causes a React render.

Instead:

```tsx
const opacity = useRef(new Animated.Value(0)).current;
```

or

```tsx
const opacity = useSharedValue(0);
```

Animation values should remain outside React state.

---

# Example

**Expo (TypeScript)**

Create a new project:

```bash
npx create-expo-app FadeExample --template expo-template-blank-typescript
cd FadeExample
npm start
```

`App.tsx`

```tsx
import React, { useRef } from "react";
import { Animated, Button, SafeAreaView, StyleSheet } from "react-native";

export default function App() {
  const opacity = useRef(new Animated.Value(1)).current;

  const fadeOut = () => {
    Animated.timing(opacity, {
      toValue: 0,
      duration: 500,
      useNativeDriver: true,
    }).start();
  };

  const fadeIn = () => {
    Animated.timing(opacity, {
      toValue: 1,
      duration: 500,
      useNativeDriver: true,
    }).start();
  };

  return (
    <SafeAreaView style={styles.container}>
      <Animated.View style={[styles.box, { opacity }]} />

      <Button title="Fade Out" onPress={fadeOut} />
      <Button title="Fade In" onPress={fadeIn} />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    gap: 16,
    alignItems: "center",
  },
  box: {
    width: 120,
    height: 120,
    backgroundColor: "tomato",
  },
});
```

Run:

```bash
npx expo start
```

Press the buttons to smoothly fade the box in and out.

---

# Tooling & Setup

- Prefer **Expo** for quick setup or the React Native CLI for projects requiring custom native modules.
- Avoid Create React App (CRA); it is deprecated and not applicable to React Native.
- Modern React Native uses **ES Modules** (`import`/`export`).
- **Metro** is the default JavaScript bundler. Expo builds on Metro while adding a streamlined development experience.
- For advanced animations, install **React Native Reanimated** (which requires its Babel plugin and, on bare React Native, native installation steps).

---

# Performance

- Use `useNativeDriver: true` for opacity animations whenever possible.
- Prefer **React Native Reanimated** for gesture-driven or highly interactive animations because they execute on the UI thread.
- Avoid animating React state every frame; use `Animated.Value` or Reanimated shared values instead.
- Memoize unaffected child components with `React.memo`.
- Use `useCallback` for event handlers passed to children.
- Profile with:
  - React DevTools Profiler
  - Flipper (where supported)
  - React Native Performance Monitor (`Show Perf Monitor`)

- Optimize images to reduce JS and rendering work during transitions.
- Under the New Architecture (Fabric/TurboModules), keep expensive work off the JS thread to maintain smooth animations.

---

# Testing

- **Unit/Integration:** Test that the correct animated styles or callbacks are triggered using Jest and React Native Testing Library.

```bash
npm test
```

- **E2E:** Use Detox or Maestro to verify fade-in/fade-out behavior after user interactions.

---

# Ops & Deployment

- Log animation-related issues sparingly in development to avoid affecting performance.
- Handle animation completion callbacks carefully if components may unmount before an animation finishes.
- Consider offline states when fading between loading and cached content.
- Automate builds with **EAS Build**, Bitrise, or GitHub Actions.
- Test animations on both Android and iOS release builds, as debug mode can distort animation performance.
- If using OTA updates (Expo Updates), verify that animation library versions remain compatible with the deployed runtime.

---

# Pitfalls

- Forgetting `useNativeDriver: true` for supported properties like opacity, causing animations to run on the JS thread.
- Recreating `Animated.Value` on every render instead of storing it in `useRef`.
- Using React state to update animation progress, leading to unnecessary re-renders.

## Question 2. How do you **animate translation (move components)**?

## Question 3. How do you **combine multiple animations** in sequence or parallel?

## Question 4. How do you **handle swipe gestures for items**?

## Question 5. How do you **create swipeable rows with actions**?

## Question 6. How do you **handle keyboard events** (`keyboardDidShow`, `keyboardDidHide`)?

## Question 7. How do you **implement responsive font sizes**?

## Question 8. How do you **handle orientation-specific image layouts**?

## Question 9. How do you **detect device type (tablet vs phone)**?

## Question 10. How do you **fetch and display paginated API data**?

## Question 11. How do you **cancel API requests** if user navigates away?

## Question 12. How do you **debounce API calls while typing**?

## Question 13. How do you **implement infinite scroll with pagination**?

## Question 14. How do you **display error messages inline**?

## Question 15. How do you **implement search with filtering**?

## Question 16. How do you **integrate with Firebase Firestore**?

## Question 17. How do you **integrate Firebase Realtime Database**?

## Question 18. How do you **implement offline caching with AsyncStorage**?

## Question 19. How do you **share data between components without Redux**?

## Question 20. How do you **manage multiple themes** dynamically?
