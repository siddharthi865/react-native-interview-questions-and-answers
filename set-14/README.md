# Set 14

| S.No. | Question                                                                                                                                                            |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you conditionally load components based on platform?](#question-1-how-do-you-conditionally-load-components-based-on-platform)                               |
| 2.    | [How do you handle user logouts securely?](#question-2-how-do-you-handle-user-logouts-securely)                                                                     |
| 3.    | [How do you store temporary session data?](#question-3-how-do-you-store-temporary-session-data)                                                                     |
| 4.    | [How do you monitor app state (AppState API)?](#question-4-how-do-you-monitor-app-state-appstate-api)                                                               |
| 5.    | [How do you handle deep linking with parameters?](#question-5-how-do-you-handle-deep-linking-with-parameters)                                                       |
| 6.    | [How do you open external apps from React Native?](#question-6-how-do-you-open-external-apps-from-react-native)                                                     |
| 7.    | [How do you implement clipboard copy/paste with hooks?](#question-7-how-do-you-implement-clipboard-copypaste-with-hooks)                                            |
| 8.    | [How do you detect if the app is in the foreground/background?](#question-8-how-do-you-detect-if-the-app-is-in-the-foregroundbackground)                            |
| 9.    | [How do you handle hardware back button on Android?](#question-9-how-do-you-handle-hardware-back-button-on-android)                                                 |
| 10.   | [How do you implement swipe gestures for navigation?](#question-10-how-do-you-implement-swipe-gestures-for-navigation)                                              |
| 11.   | [How does React Native bridge work internally?](#question-11-how-does-react-native-bridge-work-internally)                                                          |
| 12.   | [How do you optimize rendering performance with React.memo and useCallback?](#question-12-how-do-you-optimize-rendering-performance-with-reactmemo-and-usecallback) |
| 13.   | [How do you optimize animations using native driver?](#question-13-how-do-you-optimize-animations-using-native-driver)                                              |
| 14.   | [How do you profile JS and native performance?](#question-14-how-do-you-profile-js-and-native-performance)                                                          |
| 15.   | [How do you reduce app startup time?](#question-15-how-do-you-reduce-app-startup-time)                                                                              |
| 16.   | [How do you split code for faster load times?](#question-16-how-do-you-split-code-for-faster-load-times)                                                            |
| 17.   | [How do you use Hermes profiler?](#question-17-how-do-you-use-hermes-profiler)                                                                                      |
| 18.   | [How do you debug memory leaks in React Native?](#question-18-how-do-you-debug-memory-leaks-in-react-native)                                                        |
| 19.   | [How do you debug JS thread bottlenecks?](#question-19-how-do-you-debug-js-thread-bottlenecks)                                                                      |
| 20.   | [How do you integrate custom native modules for iOS?](#question-20-how-do-you-integrate-custom-native-modules-for-ios)                                              |

## Question 1. How do you **conditionally load components based on platform**?

# Short answer

Use the `Platform` API (`Platform.OS`, `Platform.select()`), platform-specific files (`Component.ios.tsx`, `Component.android.tsx`), or dynamic imports when appropriate. For production apps, platform-specific file resolution is the cleanest and most maintainable approach because Metro automatically loads the correct implementation.

---

# Explanation

React Native often requires different UI or native integrations for Android and iOS.

Common approaches:

### 1. `Platform.OS`

Useful for small UI differences.

```tsx
if (Platform.OS === "ios") {
  // iOS-specific logic
} else {
  // Android-specific logic
}
```

Best for:

- Styling differences
- Minor behavior changes
- Small feature flags

---

### 2. `Platform.select()`

Cleaner than multiple `if` statements.

```tsx
const styles = Platform.select({
  ios: {
    paddingTop: 44,
  },
  android: {
    paddingTop: 24,
  },
  default: {
    paddingTop: 0,
  },
});
```

Useful for:

- Styles
- Config objects
- Constants

---

### 3. Platform-specific files (Recommended)

```
components/
   VideoPlayer.ios.tsx
   VideoPlayer.android.tsx
```

Then simply import:

```tsx
import VideoPlayer from "./VideoPlayer";
```

Metro automatically resolves:

- iOS → `VideoPlayer.ios.tsx`
- Android → `VideoPlayer.android.tsx`

This is the preferred solution when implementations differ significantly.

Examples:

- Different camera libraries
- Apple Pay vs Google Pay
- Native UI components
- Platform-specific animations

---

### 4. Conditional Rendering

```tsx
{
  Platform.OS === "ios" ? <IOSButton /> : <AndroidButton />;
}
```

Simple and readable for UI differences.

---

### 5. Dynamic Imports

If a platform-specific feature is rarely used, lazy-load it.

```tsx
const openScanner = async () => {
  if (Platform.OS === "android") {
    const Scanner = await import("./AndroidScanner");
    Scanner.start();
  }
};
```

Benefits:

- Smaller initial bundle
- Faster startup
- Avoid loading unnecessary native code

---

## Rendering Behavior

React only renders the branch that is returned.

```tsx
return Platform.OS === "ios" ? <IOSPlayer /> : <AndroidPlayer />;
```

The unused component is **not mounted**, meaning:

- Effects don't execute
- State isn't created
- Native views aren't allocated

This improves performance when platform-specific screens are substantially different.

---

## State Management Trade-offs

Platform checks should ideally stay near the presentation layer.

Example:

```
Business Logic
       │
       ▼
Platform Component
       │
       ▼
iOS / Android UI
```

Avoid scattering `Platform.OS` checks throughout business logic.

Instead:

- Share hooks
- Share state
- Swap only UI or native integrations

This keeps Redux, Zustand, MobX, or Context logic platform-independent.

---

## New Architecture (Fabric & TurboModules)

Under the New Architecture:

- Fabric renders platform-specific native components more efficiently.
- TurboModules allow platform-specific native implementations behind a shared JavaScript interface.
- Platform-specific file resolution still works exactly the same.

A common pattern:

```
CameraModule.ios.ts
CameraModule.android.ts
```

Each implementation can call different native APIs while exposing the same TypeScript interface.

---

# Example

**Scaffold (Expo + TypeScript):**

```bash
npx create-expo-app PlatformExample --template expo-template-blank-typescript
cd PlatformExample
npm start
```

`PlatformCard.tsx`

```tsx
import React from "react";
import { Platform, Text, View } from "react-native";

export default function PlatformCard() {
  return (
    <View style={{ padding: 20 }}>
      {Platform.OS === "ios" ? (
        <Text>🍎 Running on iOS</Text>
      ) : (
        <Text>🤖 Running on Android</Text>
      )}
    </View>
  );
}
```

Or with platform-specific files:

```
PlatformCard.ios.tsx
PlatformCard.android.tsx
```

```tsx
import PlatformCard from "./PlatformCard";

export default function App() {
  return <PlatformCard />;
}
```

Metro automatically imports the correct implementation.

Run:

```bash
npx expo start
```

Then press:

- **i** → iOS Simulator
- **a** → Android Emulator

---

# Tooling & Setup

**Recommended stack**

- **Expo** for rapid development and OTA updates.
- **React Native CLI** when custom native code is required.
- Avoid **Create React App (CRA)**—it is deprecated and intended for web, not mobile.

**Metro Bundler**

- Automatically resolves `.ios.tsx`, `.android.tsx`, and `.native.tsx`.
- Handles platform-specific module resolution without extra configuration.

**ES Modules vs CommonJS**

- Modern React Native projects use **ES Modules** (`import`/`export`).
- CommonJS (`require`) is mainly encountered in legacy packages or Metro configuration files.

---

# Performance

When platform-loading components:

- Prefer platform-specific files over many inline `Platform.OS` checks.
- Lazy-load heavy platform-only modules using `import()`.
- Use `React.memo` to avoid unnecessary re-renders.
- Memoize callbacks with `useCallback`.
- Memoize computed values with `useMemo`.
- Profile using:
  - React DevTools Profiler
  - Flipper
  - React Native Performance Monitor

- Optimize images with appropriate sizes and caching.
- Under the New Architecture, use TurboModules for performance-critical native integrations.

---

# Testing

**Unit & Integration**

Use **Jest** with **React Native Testing Library**.

Example:

```bash
npm test
```

Mock the platform:

```tsx
jest.mock("react-native/Libraries/Utilities/Platform", () => ({
  OS: "ios",
  select: (obj: any) => obj.ios,
}));
```

**E2E**

Use:

- Detox (native apps)
- Maestro (cross-platform UI automation)

Example:

```bash
maestro test flow.yaml
```

---

# Ops & Deployment

- Use structured logging (e.g., Sentry, Crashlytics) to capture platform-specific issues.
- Handle errors gracefully with error boundaries and fallback UIs.
- Cache offline data with libraries such as AsyncStorage or SQLite-backed solutions when appropriate.
- CI/CD:
  - Expo Application Services (EAS)
  - GitHub Actions
  - Bitrise

- Validate platform-specific code paths on both iOS and Android before release.
- Use OTA updates (Expo Updates or CodePush where applicable) only for JavaScript changes; native code changes still require new app store builds.

---

# Pitfalls

- Don't scatter `Platform.OS` checks across your codebase—encapsulate platform differences.
- Prefer `.ios.tsx`/`.android.tsx` files when implementations diverge significantly instead of large conditional blocks.
- Always test both platforms, as native APIs and UI behavior can differ even when JavaScript logic is shared.

## Question 2. How do you **handle user logouts securely**?

## Question 3. How do you **store temporary session data**?

## Question 4. How do you **monitor app state** (`AppState` API)?

## Question 5. How do you **handle deep linking with parameters**?

## Question 6. How do you **open external apps from React Native**?

## Question 7. How do you **implement clipboard copy/paste** with hooks?

## Question 8. How do you **detect if the app is in the foreground/background**?

## Question 9. How do you **handle hardware back button on Android**?

## Question 10. How do you **implement swipe gestures for navigation**?

## Question 11. How does **React Native bridge** work internally?

## Question 12. How do you **optimize rendering performance with React.memo and useCallback**?

## Question 13. How do you **optimize animations using native driver**?

## Question 14. How do you **profile JS and native performance**?

## Question 15. How do you **reduce app startup time**?

## Question 16. How do you **split code for faster load times**?

## Question 17. How do you **use Hermes profiler**?

## Question 18. How do you **debug memory leaks in React Native**?

## Question 19. How do you **debug JS thread bottlenecks**?

## Question 20. How do you **integrate custom native modules** for iOS?
