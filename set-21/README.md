# Set 21

| S.No. | Question                                                                                                                                                                                        |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you initialize a new Expo project with TypeScript?](#question-1-how-do-you-initialize-a-new-expo-project-with-typescript)                                                               |
| 2.    | [What are the differences between Expo managed workflow and bare workflow for TypeScript?](#question-2-what-are-the-differences-between-expo-managed-workflow-and-bare-workflow-for-typescript) |
| 3.    | [How do you upgrade React Native dependencies safely?](#question-3-how-do-you-upgrade-react-native-dependencies-safely)                                                                         |
| 4.    | [How do you check the version of React Native in a project?](#question-4-how-do-you-check-the-version-of-react-native-in-a-project)                                                             |
| 5.    | [How do you run a React Native app on a physical iOS device without Xcode?](#question-5-how-do-you-run-a-react-native-app-on-a-physical-ios-device-without-xcode)                               |
| 6.    | [How do you run a React Native app on a physical Android device without Android Studio?](#question-6-how-do-you-run-a-react-native-app-on-a-physical-android-device-without-android-studio)     |
| 7.    | [How do you change the default app icon and splash screen in React Native?](#question-7-how-do-you-change-the-default-app-icon-and-splash-screen-in-react-native)                               |
| 8.    | [How do you implement simple navigation with React Navigation v6?](#question-8-how-do-you-implement-simple-navigation-with-react-navigation-v6)                                                 |
| 9.    | [How do you navigate back programmatically in React Navigation?](#question-9-how-do-you-navigate-back-programmatically-in-react-navigation)                                                     |
| 10.   | [How do you pass parameters to a route in React Navigation?](#question-10-how-do-you-pass-parameters-to-a-route-in-react-navigation)                                                            |
| 11.   | [How do you set default navigation options?](#question-11-how-do-you-set-default-navigation-options)                                                                                            |
| 12.   | [How do you use SafeAreaView with notch devices?](#question-12-how-do-you-use-safeareaview-with-notch-devices)                                                                                  |
| 13.   | [How do you set the status bar color dynamically?](#question-13-how-do-you-set-the-status-bar-color-dynamically)                                                                                |
| 14.   | [How do you use ScrollView with horizontal scrolling?](#question-14-how-do-you-use-scrollview-with-horizontal-scrolling)                                                                        |
| 15.   | [How do you disable horizontal scrolling in ScrollView?](#question-15-how-do-you-disable-horizontal-scrolling-in-scrollview)                                                                    |
| 16.   | [How do you use flexbox to create grids?](#question-16-how-do-you-use-flexbox-to-create-grids)                                                                                                  |
| 17.   | [How do you center components vertically and horizontally using flexbox?](#question-17-how-do-you-center-components-vertically-and-horizontally-using-flexbox)                                  |
| 18.   | [How do you wrap text around images?](#question-18-how-do-you-wrap-text-around-images)                                                                                                          |
| 19.   | [How do you implement a basic Tab Navigator?](#question-19-how-do-you-implement-a-basic-tab-navigator)                                                                                          |
| 20.   | [How do you implement a basic Drawer Navigator?](#question-20-how-do-you-implement-a-basic-drawer-navigator)                                                                                    |

## Question 1. How do you **initialize a new Expo project with TypeScript**?

# Short answer

To initialize a new **Expo project with TypeScript**, use the official Expo template:

```bash
npx create-expo-app MyApp -t expo-template-blank-typescript
```

or (recommended in recent Expo versions):

```bash
npx create-expo-app MyApp
```

Choose the **Blank (TypeScript)** template when prompted.

Then start the development server:

```bash
cd MyApp
npx expo start
```

Run the app:

```bash
# Android
a

# iOS (macOS only)
i

# Web
w
```

---

# Explanation

Expo is the easiest way to start a modern React Native project because it provides:

- Preconfigured **Metro bundler**
- TypeScript support out of the box
- Fast Refresh
- Expo SDK with many native APIs
- OTA (Over-the-Air) updates
- Easy device testing using the **Expo Go** app

A typical project structure looks like:

```
MyApp/
│
├── App.tsx
├── app.json
├── package.json
├── tsconfig.json
├── assets/
├── node_modules/
└── babel.config.js
```

The important TypeScript files are:

- `App.tsx`
- `tsconfig.json`

Expo automatically compiles TypeScript—no additional configuration is required.

Modern Expo versions use the latest React Native, support the **New Architecture (Fabric & TurboModules)**, and integrate well with **Expo Router** if you choose a routing template.

---

# Example

## Create the project

```bash
npx create-expo-app MyApp -t expo-template-blank-typescript

cd MyApp

npx expo start
```

## `App.tsx`

```tsx
import { StatusBar } from "expo-status-bar";
import { SafeAreaView, StyleSheet, Text } from "react-native";

export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <Text>Hello Expo + TypeScript 👋</Text>
      <StatusBar style="auto" />
    </SafeAreaView>
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

Run on Android:

```bash
npx expo start
```

Press:

```
a
```

or scan the QR code using **Expo Go**.

---

# Tooling & Setup

### Recommended stack

- ✅ Expo SDK (latest)
- ✅ TypeScript
- ✅ Functional Components + Hooks
- ✅ Metro bundler

Avoid **Create React App (CRA)**—it is deprecated for React development and was never intended for React Native.

### ESM vs CommonJS

- Application code uses modern ES Modules (`import`/`export`).
- Metro and Babel handle module transformation automatically.
- Configuration files may still use CommonJS (`module.exports`) where required (e.g., `babel.config.js`).

### Metro Bundler

Metro provides:

- Fast Refresh
- Asset bundling
- Source maps
- Tree shaking improvements in modern React Native/Expo toolchains

Useful commands:

```bash
npx expo start

npx expo start --clear
```

Clear cache when troubleshooting Metro issues.

---

# Performance

Although project initialization itself doesn't affect runtime performance, a senior developer should configure the project with performance in mind.

### Enable the New Architecture

Recent Expo SDKs support enabling **Fabric** and **TurboModules**, which reduce bridge overhead and improve rendering performance.

### Profile the app

Use:

- React DevTools Profiler
- Flipper (when using a development build)
- React Native Performance Monitor
- Hermes profiling

### Memoization

Use:

```tsx
React.memo();

useMemo();

useCallback();
```

to avoid unnecessary re-renders.

### Image optimization

Prefer:

- `expo-image`
- appropriately sized assets
- caching remote images

### Native performance

If your app requires heavy native processing (e.g., camera, ML, video editing), use Expo Modules or custom native code rather than large JavaScript computations.

---

# Testing

Typical testing setup:

### Unit / Component tests

```bash
npm test
```

using:

- Jest
- React Native Testing Library

Example:

```tsx
import { render } from "@testing-library/react-native";
import App from "../App";

test("renders greeting", () => {
  expect(render(<App />).getByText(/Hello Expo/i)).toBeTruthy();
});
```

### End-to-end

Use:

- Detox
- Maestro

Example:

```bash
maestro test flow.yaml
```

---

# Ops & Deployment

For production applications:

- Use **EAS Build** for Android/iOS binaries.
- Use **EAS Update** for OTA JavaScript and asset updates.
- Configure error reporting with services such as Sentry.
- Add structured logging for debugging.
- Use secure storage for secrets and tokens.
- Configure GitHub Actions or Bitrise for CI/CD.
- Test release builds before submitting to Google Play or the App Store.

---

# Pitfalls

- **Don't use the deprecated `expo-cli` package**; use `npx create-expo-app` and `npx expo`.
- **Keep the Expo SDK and React Native versions aligned** to avoid dependency conflicts.
- **Avoid disabling TypeScript checks** with `any` or by bypassing compiler errors, as this reduces the benefits of type safety.

## Question 2. What are the **differences between Expo managed workflow and bare workflow for TypeScript**?

## Question 3. How do you **upgrade React Native dependencies safely**?

## Question 4. How do you **check the version of React Native in a project**?

## Question 5. How do you **run a React Native app on a physical iOS device without Xcode**?

## Question 6. How do you **run a React Native app on a physical Android device without Android Studio**?

## Question 7. How do you **change the default app icon and splash screen** in React Native?

## Question 8. How do you **implement simple navigation with React Navigation v6**?

## Question 9. How do you **navigate back programmatically** in React Navigation?

## Question 10. How do you **pass parameters to a route in React Navigation**?

## Question 11. How do you **set default navigation options**?

## Question 12. How do you **use SafeAreaView with notch devices**?

## Question 13. How do you **set the status bar color dynamically**?

## Question 14. How do you **use ScrollView with horizontal scrolling**?

## Question 15. How do you **disable horizontal scrolling** in ScrollView?

## Question 16. How do you **use flexbox to create grids**?

## Question 17. How do you **center components vertically and horizontally** using flexbox?

## Question 18. How do you **wrap text around images**?

## Question 19. How do you **implement a basic Tab Navigator**?

## Question 20. How do you **implement a basic Drawer Navigator**?
