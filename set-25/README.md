# Set 25

| S.No. | Question                                                                                                                                                                                                                            |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you debug memory leaks in React Native apps?](#question-1-how-do-you-debug-memory-leaks-in-react-native-apps)                                                                                                               |
| 2.    | [How do you debug native module memory leaks?](#question-2-how-do-you-debug-native-module-memory-leaks)                                                                                                                             |
| 3.    | [How do you use CodePush for over-the-air updates?](#question-3-how-do-you-use-codepush-for-over-the-air-updates)                                                                                                                   |
| 4.    | [How do you secure OTA updates?](#question-4-how-do-you-secure-ota-updates)                                                                                                                                                         |
| 5.    | [How do you encrypt sensitive storage with react-native-keychain?](#question-5-how-do-you-encrypt-sensitive-storage-with-react-native-keychain)                                                                                     |
| 6.    | [How do you perform end-to-end testing with Detox?](#question-6-how-do-you-perform-end-to-end-testing-with-detox)                                                                                                                   |
| 7.    | [How do you simulate push notifications in Detox tests?](#question-7-how-do-you-simulate-push-notifications-in-detox-tests)                                                                                                         |
| 8.    | [How do you implement background sync with Headless JS?](#question-8-how-do-you-implement-background-sync-with-headless-js)                                                                                                         |
| 9.    | [How do you handle background geolocation on iOS with limitations?](#question-9-how-do-you-handle-background-geolocation-on-ios-with-limitations)                                                                                   |
| 10.   | [How do you handle background geolocation on Android with battery optimization?](#question-10-how-do-you-handle-background-geolocation-on-android-with-battery-optimization)                                                        |
| 11.   | [How do you secure API keys and secrets in React Native?](#question-11-how-do-you-secure-api-keys-and-secrets-in-react-native)                                                                                                      |
| 12.   | [How do you integrate OAuth flows securely?](#question-12-how-do-you-integrate-oauth-flows-securely)                                                                                                                                |
| 13.   | [How do you use biometric authentication for sensitive operations?](#question-13-how-do-you-use-biometric-authentication-for-sensitive-operations)                                                                                  |
| 14.   | [How do you implement offline queueing for API requests?](#question-14-how-do-you-implement-offline-queueing-for-api-requests)                                                                                                      |
| 15.   | [How do you sync offline data to server on network availability?](#question-15-how-do-you-sync-offline-data-to-server-on-network-availability)                                                                                      |
| 16.   | [How do you handle app versioning and migrations for persistent storage?](#question-16-how-do-you-handle-app-versioning-and-migrations-for-persistent-storage)                                                                      |
| 17.   | [How do you implement multi-language RTL support dynamically?](#question-17-how-do-you-implement-multi-language-rtl-support-dynamically)                                                                                            |
| 18.   | [How do you integrate GraphQL subscriptions for real-time updates?](#question-18-how-do-you-integrate-graphql-subscriptions-for-real-time-updates)                                                                                  |
| 19.   | [How do you measure FPS and frame drops during animations?](#question-19-how-do-you-measure-fps-and-frame-drops-during-animations)                                                                                                  |
| 20.   | [How do you prepare a production-ready React Native app for App Store and Play Store submission with CI/CD?](#question-20-how-do-you-prepare-a-production-ready-react-native-app-for-app-store-and-play-store-submission-with-cicd) |

## Question 1. How do you **debug memory leaks in React Native apps**?

# Debugging Memory Leaks in React Native Apps

## Short answer

Memory leaks in React Native typically occur when objects remain in memory after they should be garbage collected. Common causes include uncleared timers, event listeners, subscriptions, native module references, navigation lifecycle issues, and large in-memory objects. Debug them using Flipper, Hermes heap snapshots, Xcode Instruments (iOS), Android Studio Memory Profiler, and React DevTools. Always clean up side effects in `useEffect`.

---

# Explanation

A memory leak happens when JavaScript or native objects continue to be referenced after a screen or component has unmounted. Over time this increases memory usage, causes slow rendering, frequent garbage collection, and eventually app crashes (especially on lower-end Android devices).

## Common sources of memory leaks

### 1. Uncleared timers

Bad:

```tsx
useEffect(() => {
  setInterval(fetchData, 5000);
}, []);
```

Good:

```tsx
useEffect(() => {
  const id = setInterval(fetchData, 5000);

  return () => clearInterval(id);
}, []);
```

---

### 2. Event listeners

Examples:

- AppState
- Keyboard
- Dimensions
- BackHandler
- DeviceEventEmitter

Bad:

```tsx
Keyboard.addListener("keyboardDidShow", onShow);
```

Good:

```tsx
useEffect(() => {
  const sub = Keyboard.addListener("keyboardDidShow", onShow);

  return () => sub.remove();
}, []);
```

---

### 3. Network requests

If a request finishes after the screen unmounts, it may still try to update state.

```tsx
useEffect(() => {
  const controller = new AbortController();

  fetch(url, {
    signal: controller.signal,
  })
    .then((res) => res.json())
    .then(setData);

  return () => controller.abort();
}, []);
```

---

### 4. Navigation listeners

Always unsubscribe.

```tsx
useEffect(() => {
  const unsubscribe = navigation.addListener("focus", () => {
    loadData();
  });

  return unsubscribe;
}, [navigation]);
```

---

### 5. Native modules

Leaks are sometimes on the native side:

- Camera
- Maps
- Video
- Bluetooth
- Sensors
- Location

Always release native resources.

Example:

```tsx
cameraRef.current?.stopRecording();
```

or

```tsx
player.release();
```

depending on the library.

---

### 6. Large objects

Avoid keeping:

- huge arrays
- image buffers
- base64 strings
- cached responses

inside component state.

Prefer:

- pagination
- React Query cache
- AsyncStorage
- MMKV

instead of storing everything in memory.

---

### 7. Closures

Closures can unintentionally retain large objects.

```tsx
const hugeData = [...];

const handler = () => {
    console.log(hugeData);
};
```

Even if UI changes, `hugeData` stays referenced.

---

## New Architecture (Fabric + TurboModules)

The New Architecture improves memory management by:

- reducing unnecessary bridge serialization
- using JSI instead of JSON bridge messages
- faster object lifetime management
- fewer duplicated objects between JS and native

However, leaks are still possible if:

- native C++ objects aren't released
- TurboModules keep references
- Fabric components aren't cleaned correctly

Always dispose of native resources explicitly.

---

# Example

**Create an Expo TypeScript project**

```bash
npx create-expo-app MemoryLeakDemo --template expo-template-blank-typescript

cd MemoryLeakDemo

npx expo start
```

**App.tsx**

```tsx
import React, { useEffect, useState } from "react";
import { Button, Text, View } from "react-native";

export default function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((c) => c + 1);
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return (
    <View
      style={{
        flex: 1,
        justifyContent: "center",
        alignItems: "center",
      }}
    >
      <Text>{count}</Text>
      <Button title="Nothing leaks 🎉" onPress={() => {}} />
    </View>
  );
}
```

Run:

```bash
npx expo start
```

or with React Native CLI:

```bash
npx react-native run-android
```

```bash
npx react-native run-ios
```

---

# Tooling & Setup

**Recommended stack**

- Expo SDK (managed workflow) for rapid development.
- React Native CLI when native modules or custom platform code are required.
- Avoid Create React App (CRA); it is deprecated for React Native and not suitable for mobile.

**Bundler**

- Metro is the standard React Native bundler, optimized for native asset resolution and Fast Refresh.
- Expo also uses Metro with additional tooling.

**Modules**

- Prefer ESM (`import`/`export`) in application code.
- React Native tooling transpiles modern JavaScript/TypeScript for supported runtimes.

---

# Performance

## 1. Hermes heap snapshots

Hermes supports heap snapshots.

Look for:

- Detached objects
- Growing arrays
- Retained closures
- Unreleased screens

Excellent for finding JS leaks.

---

## 2. Flipper

Flipper provides:

- Memory plugin
- React DevTools
- Hermes debugger
- Network inspector
- Layout inspector

Watch memory while repeatedly opening and closing screens. If usage continually rises without stabilizing after garbage collection, investigate retained objects.

---

## 3. Xcode Instruments

For iOS:

```
Leaks
Allocations
Memory Graph
```

Memory Graph helps identify retain cycles in native code.

---

## 4. Android Studio Memory Profiler

Useful for:

- Heap dumps
- Allocation tracking
- Leak detection
- JNI/native memory

---

## 5. React DevTools Profiler

React DevTools Profiler primarily identifies unnecessary renders rather than memory leaks, but excessive rerenders can amplify memory pressure.

---

## 6. Memoization

Reduce unnecessary object creation with:

```tsx
React.memo();

useMemo();

useCallback();
```

These optimize rendering performance but do not fix leaks caused by lingering references or missing cleanup.

---

## 7. Images

Large images are common memory consumers.

Use:

- properly sized assets
- caching libraries
- lazy loading
- thumbnails
- avoid Base64 images when possible

---

## 8. Native modules

Watch for:

- Camera
- Maps
- Video
- Bluetooth
- Sensors

Always dispose of native handles when components unmount.

---

# Testing

### Unit

Use Jest with React Native Testing Library.

```bash
npm test
```

Verify cleanup logic by asserting that subscriptions, timers, or mocked listeners are removed on unmount.

### Integration

Test mounting/unmounting screens repeatedly to ensure cleanup functions execute as expected.

### End-to-end

Use Detox or Maestro to automate navigation loops and monitor memory with platform profilers during long-running sessions.

---

# Ops & Deployment

- Enable crash reporting with tools such as Sentry or Firebase Crashlytics to detect out-of-memory (OOM) crashes.
- Use structured logging around component mount/unmount events and long-lived subscriptions.
- Cache data responsibly (e.g., React Query persistence, MMKV, AsyncStorage) instead of holding large datasets in component state.
- Use Expo EAS or GitHub Actions/Bitrise for CI/CD, including automated testing and release builds.
- For OTA updates, Expo Updates works in managed projects; ensure fixes involving native code are released through app store updates when required.

---

# Pitfalls

- **Forgetting cleanup** in `useEffect` (timers, listeners, subscriptions, intervals).
- **Keeping large datasets or Base64 images** in React state instead of using pagination or persistent storage.
- **Ignoring native resource cleanup**, especially for camera, video, Bluetooth, sensors, and custom native modules.

## Question 2. How do you **debug native module memory leaks**?

## Question 3. How do you **use CodePush for over-the-air updates**?

## Question 4. How do you **secure OTA updates**?

## Question 5. How do you **encrypt sensitive storage with react-native-keychain**?

## Question 6. How do you **perform end-to-end testing with Detox**?

## Question 7. How do you **simulate push notifications in Detox tests**?

## Question 8. How do you **implement background sync with Headless JS**?

## Question 9. How do you **handle background geolocation on iOS with limitations**?

## Question 10. How do you **handle background geolocation on Android with battery optimization**?

## Question 11. How do you **secure API keys and secrets in React Native**?

## Question 12. How do you **integrate OAuth flows securely**?

## Question 13. How do you **use biometric authentication for sensitive operations**?

## Question 14. How do you **implement offline queueing for API requests**?

## Question 15. How do you **sync offline data to server on network availability**?

## Question 16. How do you **handle app versioning and migrations** for persistent storage?

## Question 17. How do you **implement multi-language RTL support dynamically**?

## Question 18. How do you **integrate GraphQL subscriptions for real-time updates**?

## Question 19. How do you **measure FPS and frame drops during animations**?

## Question 20. How do you **prepare a production-ready React Native app for App Store and Play Store submission with CI/CD**?
