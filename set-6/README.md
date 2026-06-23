# Set 6

| S.No. | Question                                                                                                                                                                                         |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [What is the difference between React Native and Flutter?](#question-1-what-is-the-difference-between-react-native-and-flutter)                                                                  |
| 2.    | [Can you use HTML and CSS directly in React Native?](#question-2-can-you-use-html-and-css-directly-in-react-native)                                                                              |
| 3.    | [How do you handle text styling differently in React Native vs web?](#question-3-how-do-you-handle-text-styling-differently-in-react-native-vs-web)                                              |
| 4.    | [What are the core components in React Native?](#question-4-what-are-the-core-components-in-react-native)                                                                                        |
| 5.    | [What is the difference between View and SafeAreaView?](#question-5-what-is-the-difference-between-view-and-safeareaview)                                                                        |
| 6.    | [How do you render lists of items in React Native?](#question-6-how-do-you-render-lists-of-items-in-react-native)                                                                                |
| 7.    | [How do you make components reusable?](#question-7-how-do-you-make-components-reusable)                                                                                                          |
| 8.    | [What is the difference between stateless and stateful components?](#question-8-what-is-the-difference-between-stateless-and-stateful-components)                                                |
| 9.    | [How do you pass functions as props?](#question-9-how-do-you-pass-functions-as-props)                                                                                                            |
| 10.   | [How do you prevent a component from re-rendering unnecessarily?](#question-10-how-do-you-prevent-a-component-from-re-rendering-unnecessarily)                                                   |
| 11.   | [How do you conditionally apply styles?](#question-11-how-do-you-conditionally-apply-styles)                                                                                                     |
| 12.   | [What is the difference between position: absolute and position: relative in React Native?](#question-12-what-is-the-difference-between-position-absolute-and-position-relative-in-react-native) |
| 13.   | [How do you handle multiline text input?](#question-13-how-do-you-handle-multiline-text-input)                                                                                                   |
| 14.   | [What is the difference between defaultProps and propTypes?](#question-14-what-is-the-difference-between-defaultprops-and-proptypes)                                                             |
| 15.   | [How do you make a button clickable in React Native?](#question-15-how-do-you-make-a-button-clickable-in-react-native)                                                                           |
| 16.   | [What is the difference between ScrollView and SectionList?](#question-16-what-is-the-difference-between-scrollview-and-sectionlist)                                                             |
| 17.   | [How do you handle orientation changes in UI?](#question-17-how-do-you-handle-orientation-changes-in-ui)                                                                                         |
| 18.   | [How do you center a component vertically and horizontally?](#question-18-how-do-you-center-a-component-vertically-and-horizontally)                                                             |
| 19.   | [How do you conditionally render multiple components?](#question-19-how-do-you-conditionally-render-multiple-components)                                                                         |
| 20.   | [How do you handle images of different sizes?](#question-20-how-do-you-handle-images-of-different-sizes)                                                                                         |

## Question 1. What is the difference between **React Native and Flutter**?

# Short answer

**React Native** uses **JavaScript/TypeScript + React** to build native mobile apps by rendering native UI components (or Fabric components in the New Architecture). **Flutter** uses **Dart** and renders everything using its own **Skia/Impeller rendering engine**, giving it complete control over the UI.

**Choose React Native if:**

- Your team already knows React or JavaScript/TypeScript.
- You need to share logic with a React web app.
- You want a mature JavaScript ecosystem and faster onboarding.

**Choose Flutter if:**

- You want pixel-perfect UI consistency across platforms.
- You're building highly customized animations or graphics-heavy apps.
- You're comfortable adopting Dart.

---

# Explanation

## Architecture

### React Native

- Uses **JavaScript/TypeScript** with React.
- UI is composed of **native platform components** (Android Views and iOS UIKit/SwiftUI interoperability).
- With the **New Architecture**, **Fabric** improves rendering and **TurboModules** replace the old bridge for faster native communication.
- Uses **Metro** as the default bundler.

Flow:

```
React Components
       ↓
React Reconciler
       ↓
Fabric Renderer
       ↓
Native UI Components
```

### Flutter

- Uses **Dart**.
- Doesn't use native UI widgets.
- Draws everything using **Skia** (and increasingly **Impeller** on supported platforms).
- Rendering is entirely controlled by Flutter.

Flow:

```
Flutter Widgets
       ↓
Flutter Framework
       ↓
Rendering Engine (Skia/Impeller)
       ↓
Canvas
```

---

## Rendering behavior

### React Native

Pros:

- Native look and feel.
- Platform UI updates automatically.
- Better integration with native APIs.

Cons:

- Custom UI sometimes requires native modules.
- Performance-sensitive apps benefit from Fabric and TurboModules.

---

### Flutter

Pros:

- Consistent UI across Android and iOS.
- Smooth custom animations.
- Complete rendering control.

Cons:

- Doesn't automatically match native platform appearance.
- Larger application size.

---

## Performance

### React Native

Old Architecture:

```
JS Thread
     ↓
Bridge
     ↓
Native
```

Bridge serialization could become a bottleneck.

New Architecture:

```
JS
 ↓
JSI
 ↓
Fabric + TurboModules
 ↓
Native
```

Benefits:

- Less bridge overhead
- Faster rendering
- Better startup time
- Smoother animations

---

### Flutter

Flutter renders directly through its engine.

Advantages:

- Excellent animation performance
- Predictable frame rendering
- Very smooth scrolling

Trade-off:

- Entire rendering engine ships inside the app, increasing binary size.

---

## Language

| React Native                 | Flutter                |
| ---------------------------- | ---------------------- |
| JavaScript / TypeScript      | Dart                   |
| Huge ecosystem               | Smaller ecosystem      |
| Easy hiring                  | Dart learning required |
| Web developers adapt quickly | Separate skillset      |

---

## State Management

### React Native

Common choices:

- React Context
- Redux Toolkit
- Zustand
- MobX
- Recoil
- React Query / TanStack Query

Uses standard React Hooks.

```tsx
const [count, setCount] = useState(0);
```

---

### Flutter

Popular choices:

- Provider
- Riverpod
- Bloc
- Cubit
- GetX

```dart
final counter = StateProvider((ref) => 0);
```

---

# Example

### React Native (TypeScript)

**Scaffold (Expo):**

```bash
npx create-expo-app MyApp --template expo-template-blank-typescript
cd MyApp
npx expo start
```

`App.tsx`

```tsx
import { useState } from "react";
import { View, Button, Text } from "react-native";

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <View
      style={{
        flex: 1,
        justifyContent: "center",
        alignItems: "center",
      }}
    >
      <Text>{count}</Text>

      <Button title="Increment" onPress={() => setCount((c) => c + 1)} />
    </View>
  );
}
```

Run:

```bash
npx expo start
```

Press:

- **a** → Android
- **i** → iOS (macOS)
- Scan the QR code with Expo Go (where supported)

---

# Tooling & Setup

**Recommended stack:** **Expo** for rapid development or **React Native CLI** when you need custom native code. Avoid **Create React App (CRA)**—it is deprecated and intended for web, not mobile.

- **Bundler:** Metro (default for React Native).
- **Module system:** Prefer **ES Modules (`import`/`export`)**; CommonJS is mainly encountered in older packages or Node.js tooling.
- **New Architecture:** Enable **Fabric** and **TurboModules** for improved rendering and native interoperability when your dependencies support them.
- **TypeScript:** Preferred for production apps because of stronger type safety and tooling.

---

# Performance

### React Native

Use:

- React.memo
- useMemo
- useCallback
- FlatList instead of ScrollView for large lists
- FlashList for very large datasets
- Hermes JavaScript engine
- Fabric
- TurboModules

Profile with:

- React DevTools Profiler
- Flipper (where supported)
- React Native Perf Monitor
- Android Studio Profiler
- Xcode Instruments

Optimize:

- Lazy-load screens with React Navigation where appropriate.
- Compress and cache images.
- Move compute-intensive work to native modules or background tasks if needed.

### Flutter

Flutter has excellent animation performance due to its rendering engine, but developers should still minimize unnecessary widget rebuilds (e.g., using `const` widgets where appropriate), optimize image loading, and profile with Flutter DevTools.

---

# Testing

React Native:

**Unit/Integration**

```bash
npm test
```

Uses:

- Jest
- React Native Testing Library

Example:

```tsx
import { render } from "@testing-library/react-native";
import App from "./App";

test("renders counter", () => {
  expect(render(<App />).getByText("0")).toBeTruthy();
});
```

**E2E**

- Detox
- Maestro

Example:

```bash
maestro test flow.yaml
```

---

# Ops & Deployment

- **Logging:** Use structured logging; integrate crash reporting with services like Firebase Crashlytics or Sentry.
- **Error handling:** Add React error boundaries for rendering errors and handle async failures gracefully.
- **Offline:** Cache API responses with libraries such as TanStack Query or persist state using AsyncStorage/SQLite as appropriate.
- **CI/CD:** Expo Application Services (EAS), GitHub Actions, or Bitrise can automate builds and deployments.
- **Updates:** Expo supports OTA updates (within platform policy limits). For bare React Native, ensure any update mechanism complies with App Store and Play Store policies.
- **Release:** Test thoroughly on physical Android and iOS devices before publishing.

---

# Pitfalls

- **Don't assume Flutter is always faster.** For many business applications, React Native with the New Architecture delivers excellent performance.
- **Avoid unnecessary re-renders.** Memoize expensive components and callbacks, especially in large lists.
- **Choose based on team expertise and product needs.** React Native often offers faster adoption for React teams, while Flutter excels when a highly customized, consistent UI is the priority.

## Question 2. Can you use **HTML and CSS** directly in React Native?

## Question 3. How do you handle **text styling** differently in React Native vs web?

## Question 4. What are the **core components** in React Native?

## Question 5. What is the difference between **View** and **SafeAreaView**?

## Question 6. How do you render **lists of items** in React Native?

## Question 7. How do you make components **reusable**?

## Question 8. What is the difference between **stateless** and **stateful** components?

## Question 9. How do you **pass functions as props**?

## Question 10. How do you prevent a component from **re-rendering unnecessarily**?

## Question 11. How do you **conditionally apply styles**?

## Question 12. What is the difference between **position: absolute** and **position: relative** in React Native?

## Question 13. How do you **handle multiline text input**?

## Question 14. What is the difference between **defaultProps** and **propTypes**?

## Question 15. How do you make a **button clickable** in React Native?

## Question 16. What is the difference between **ScrollView** and **SectionList**?

## Question 17. How do you **handle orientation changes** in UI?

## Question 18. How do you **center a component** vertically and horizontally?

## Question 19. How do you **conditionally render multiple components**?

## Question 20. How do you **handle images of different sizes**?
