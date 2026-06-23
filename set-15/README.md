# Set 15

| S.No. | Question                                                                                                                                                                 |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How do you integrate custom native modules for Android?](#question-1-how-do-you-integrate-custom-native-modules-for-android)                                            |
| 2.    | [How do you call native functions from JS?](#question-2-how-do-you-call-native-functions-from-js)                                                                        |
| 3.    | [How do you return data from native modules asynchronously?](#question-3-how-do-you-return-data-from-native-modules-asynchronously)                                      |
| 4.    | [How do you handle device-specific bugs in iOS vs Android?](#question-4-how-do-you-handle-device-specific-bugs-in-ios-vs-android)                                        |
| 5.    | [How do you handle background tasks on iOS?](#question-5-how-do-you-handle-background-tasks-on-ios)                                                                      |
| 6.    | [How do you handle background tasks on Android?](#question-6-how-do-you-handle-background-tasks-on-android)                                                              |
| 7.    | [How do you use Headless JS for background tasks?](#question-7-how-do-you-use-headless-js-for-background-tasks)                                                          |
| 8.    | [How do you integrate with biometric authentication natively?](#question-8-how-do-you-integrate-with-biometric-authentication-natively)                                  |
| 9.    | [How do you encrypt sensitive data at rest in React Native?](#question-9-how-do-you-encrypt-sensitive-data-at-rest-in-react-native)                                      |
| 10.   | [How do you secure API keys in React Native?](#question-10-how-do-you-secure-api-keys-in-react-native)                                                                   |
| 11.   | [How do you implement enterprise-level app updates (CodePush, OTA updates)?](#question-11-how-do-you-implement-enterprise-level-app-updates-codepush-ota-updates)        |
| 12.   | [How do you measure FPS and UI thread performance?](#question-12-how-do-you-measure-fps-and-ui-thread-performance)                                                       |
| 13.   | [How do you handle real-time messaging using WebSockets?](#question-13-how-do-you-handle-real-time-messaging-using-websockets)                                           |
| 14.   | [How do you use GraphQL subscriptions in React Native?](#question-14-how-do-you-use-graphql-subscriptions-in-react-native)                                               |
| 15.   | [How do you handle offline-first architecture with real-time sync?](#question-15-how-do-you-handle-offline-first-architecture-with-real-time-sync)                       |
| 16.   | [How do you optimize images and video loading?](#question-16-how-do-you-optimize-images-and-video-loading)                                                               |
| 17.   | [How do you implement caching strategies for network requests?](#question-17-how-do-you-implement-caching-strategies-for-network-requests)                               |
| 18.   | [How do you test React Native apps with Jest and Detox?](#question-18-how-do-you-test-react-native-apps-with-jest-and-detox)                                             |
| 19.   | [How do you mock native modules for testing?](#question-19-how-do-you-mock-native-modules-for-testing)                                                                   |
| 20.   | [How do you prepare a React Native app for large-scale enterprise deployment?](#question-20-how-do-you-prepare-a-react-native-app-for-large-scale-enterprise-deployment) |

## Question 1. How do you **integrate custom native modules** for Android?

# Short answer

Custom native modules on Android let you expose Java/Kotlin code to your React Native app when JavaScript alone isn't enough (e.g., accessing platform APIs, SDKs, hardware, or improving performance). In the **New Architecture**, prefer **TurboModules** (JSI-based) for better performance and type safety, while the legacy **Bridge** (`ReactContextBaseJavaModule`) is still widely used and supported.

---

# Explanation

## When do you need a custom native module?

Use a native module when you need to:

- Access Android-only APIs (Bluetooth, NFC, Sensors, Health Connect, etc.)
- Integrate third-party Android SDKs
- Perform CPU-intensive work natively
- Access APIs not available through existing React Native libraries
- Reduce Bridge overhead for frequent native interactions

Typical examples include:

- Payment SDKs
- Camera extensions
- Custom biometric logic
- Background services
- Proprietary enterprise SDKs

---

## Legacy Bridge Architecture

In the classic architecture:

```
JavaScript
     │
 React Native Bridge
     │
 Java/Kotlin Module
     │
 Android SDK
```

A module:

1. Extends `ReactContextBaseJavaModule`
2. Registers itself in a `ReactPackage`
3. Exposes methods using `@ReactMethod`
4. Returns values using:
   - Promise
   - Callback
   - Events

Example flow:

```
JS
 ↓
NativeModules.DeviceModule.getBatteryLevel()
 ↓
Bridge serialization
 ↓
Kotlin
 ↓
BatteryManager API
 ↓
Bridge serialization
 ↓
JS Promise
```

### Pros

- Easy to implement
- Huge ecosystem
- Stable

### Cons

- Serialization overhead
- Async-only API (mostly)
- Bridge becomes bottleneck for frequent calls

---

## New Architecture (TurboModules)

React Native's **New Architecture** introduces:

- TurboModules
- Fabric
- JSI

Architecture:

```
JavaScript
      │
JSI
      │
TurboModule
      │
Kotlin/Java
      │
Android SDK
```

Benefits:

- Direct JSI communication
- Less serialization
- Faster execution
- Lazy loading
- Better memory usage
- Codegen-generated TypeScript interfaces

Senior teams building new apps should enable the New Architecture whenever practical.

---

## Android implementation steps

### 1. Create Kotlin module

```kotlin
class DeviceModule(private val reactContext: ReactApplicationContext)
    : ReactContextBaseJavaModule(reactContext) {

    override fun getName() = "DeviceModule"

    @ReactMethod
    fun getGreeting(name: String, promise: Promise) {
        promise.resolve("Hello $name from Android")
    }
}
```

---

### 2. Create Package

```kotlin
class DevicePackage : ReactPackage {

    override fun createNativeModules(
        reactContext: ReactApplicationContext
    ): List<NativeModule> {
        return listOf(DeviceModule(reactContext))
    }

    override fun createViewManagers(
        reactContext: ReactApplicationContext
    ) = emptyList<ViewManager<*, *>>()
}
```

---

### 3. Register package

Inside `MainApplication.kt`

```kotlin
override fun getPackages(): List<ReactPackage> =
    PackageList(this).packages.apply {
        add(DevicePackage())
    }
```

---

### 4. Call from JavaScript

# Example

**Expo (Development Build) or React Native CLI (TypeScript)**

> Native modules require a **React Native CLI app** or an **Expo Development Build**. They do **not** work in Expo Go because custom native code isn't included.

Scaffold:

```bash
npx react-native@latest init MyApp
# or
npx create-expo-app MyApp
npx expo prebuild
```

TypeScript:

```tsx
import { NativeModules, Button } from "react-native";

const { DeviceModule } = NativeModules;

export default function App() {
  const callNative = async () => {
    const message = await DeviceModule.getGreeting("John");
    console.log(message);
  };

  return <Button title="Call Native" onPress={callNative} />;
}
```

Run:

```bash
npx react-native run-android

# Expo Development Build
npx expo run:android
```

---

# Tooling & Setup

### Preferred stack

- React Native CLI for full native control.
- Expo with **Development Builds** if you want Expo tooling plus custom native code.
- Avoid **Create React App (CRA)**—it is deprecated and not applicable to mobile apps.

### Language

Prefer:

- Kotlin over Java for Android
- TypeScript for React Native

### Metro

React Native uses the **Metro** bundler. Metro handles module resolution, fast refresh, and asset bundling. It supports both CommonJS and ESM imports in modern React Native projects, though Metro transpiles modules appropriately for the runtime.

---

# Performance

When discussing native modules in interviews, mention Bridge overhead.

### Bridge cost

Each JS ↔ Native call requires:

- serialization
- thread switching
- deserialization

Thousands of Bridge calls per second can noticeably impact performance.

Example:

Bad:

```tsx
for (let i = 0; i < 10000; i++) {
  NativeModules.DeviceModule.doSomething();
}
```

Better:

```tsx
NativeModules.DeviceModule.processItems(items);
```

Batch work into fewer native calls.

---

### TurboModules improvements

TurboModules provide:

- Lazy initialization
- Reduced serialization
- Faster startup
- Better memory efficiency
- JSI direct communication

---

### Profiling

Useful tools:

- React DevTools Profiler
- Flipper (Network, Layout, React DevTools, Hermes debugging)
- React Native Performance Monitor
- Android Studio Profiler (CPU, Memory, Network)

---

### Memoization

Prevent unnecessary re-renders around components that invoke native modules:

- `React.memo`
- `useMemo`
- `useCallback`

---

### Images

Prefer:

- Cached image libraries
- Correct image sizing
- Modern formats like WebP (Android)

---

### Native performance

- Batch Bridge calls
- Prefer TurboModules for new native integrations
- Offload heavy work to background threads
- Avoid blocking the Android UI thread

---

# Testing

### Unit

Mock native modules with Jest.

```ts
jest.mock("react-native", () => ({
  NativeModules: {
    DeviceModule: {
      getGreeting: jest.fn(),
    },
  },
}));
```

Run:

```bash
npm test
```

### Integration

Use **React Native Testing Library** to verify components that interact with mocked native modules.

### End-to-end

Use:

- Detox
- Maestro

Example:

```bash
maestro test flow.yaml
```

or

```bash
detox test
```

---

# Ops & Deployment

For production-ready native modules:

- Log native exceptions (e.g., Android Logcat) and report crashes with services like Crashlytics.
- Handle errors by rejecting Promises with meaningful error codes/messages.
- Design modules to tolerate offline scenarios when applicable (e.g., queue work or cache data).
- Automate Android builds with GitHub Actions, Bitrise, or Expo Application Services (EAS) for Expo projects.
- Rebuild and redistribute the Android app whenever native code changes; unlike pure JavaScript updates, new native binaries must be shipped through app releases. Over-the-air JavaScript updates (where supported) cannot add or modify native module implementations.

---

# Pitfalls

- Forgetting to register the module/package, causing `NativeModules.DeviceModule` to be `undefined`.
- Performing long-running work on the Android main thread, leading to UI freezes or ANRs.
- Making many small JS-to-native calls instead of batching operations, which increases Bridge overhead.

## Question 2. How do you **call native functions from JS**?

## Question 3. How do you **return data from native modules asynchronously**?

## Question 4. How do you **handle device-specific bugs** in iOS vs Android?

## Question 5. How do you **handle background tasks on iOS**?

## Question 6. How do you **handle background tasks on Android**?

## Question 7. How do you **use Headless JS** for background tasks?

## Question 8. How do you **integrate with biometric authentication natively**?

## Question 9. How do you **encrypt sensitive data** at rest in React Native?

## Question 10. How do you **secure API keys in React Native**?

## Question 11. How do you **implement enterprise-level app updates** (CodePush, OTA updates)?

## Question 12. How do you **measure FPS and UI thread performance**?

## Question 13. How do you **handle real-time messaging using WebSockets**?

## Question 14. How do you **use GraphQL subscriptions in React Native**?

## Question 15. How do you **handle offline-first architecture with real-time sync**?

## Question 16. How do you **optimize images and video loading**?

## Question 17. How do you **implement caching strategies** for network requests?

## Question 18. How do you **test React Native apps with Jest and Detox**?

## Question 19. How do you **mock native modules for testing**?

## Question 20. How do you **prepare a React Native app for large-scale enterprise deployment**?
