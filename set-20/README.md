# Set 20

| S.No. | Question                                                                                                                                                                                                 |
| ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you integrate Lottie animations?](#question-1-how-do-you-integrate-lottie-animations)                                                                                                            |
| 2.    | [How do you handle custom font loading at runtime?](#question-2-how-do-you-handle-custom-font-loading-at-runtime)                                                                                        |
| 3.    | [How do you implement runtime feature toggles?](#question-3-how-do-you-implement-runtime-feature-toggles)                                                                                                |
| 4.    | [How do you integrate feature flags in React Native?](#question-4-how-do-you-integrate-feature-flags-in-react-native)                                                                                    |
| 5.    | [How do you secure API calls with certificate pinning?](#question-5-how-do-you-secure-api-calls-with-certificate-pinning)                                                                                |
| 6.    | [How do you use AppState to handle background/foreground tasks?](#question-6-how-do-you-use-appstate-to-handle-backgroundforeground-tasks)                                                               |
| 7.    | [How do you implement push notifications with FCM topics?](#question-7-how-do-you-implement-push-notifications-with-fcm-topics)                                                                          |
| 8.    | [How do you handle push notifications while app is killed?](#question-8-how-do-you-handle-push-notifications-while-app-is-killed)                                                                        |
| 9.    | [How do you implement data sync for offline-first apps?](#question-9-how-do-you-implement-data-sync-for-offline-first-apps)                                                                              |
| 10.   | [How do you implement websocket reconnection logic?](#question-10-how-do-you-implement-websocket-reconnection-logic)                                                                                     |
| 11.   | [How do you use Redux Toolkit with RTK Query for API caching?](#question-11-how-do-you-use-redux-toolkit-with-rtk-query-for-api-caching)                                                                 |
| 12.   | [How do you mock network requests in Jest tests?](#question-12-how-do-you-mock-network-requests-in-jest-tests)                                                                                           |
| 13.   | [How do you test UI components with React Native Testing Library?](#question-13-how-do-you-test-ui-components-with-react-native-testing-library)                                                         |
| 14.   | [How do you test animations with Jest or Detox?](#question-14-how-do-you-test-animations-with-jest-or-detox)                                                                                             |
| 15.   | [How do you simulate gestures in Detox tests?](#question-15-how-do-you-simulate-gestures-in-detox-tests)                                                                                                 |
| 16.   | [How do you handle multi-language localization dynamically?](#question-16-how-do-you-handle-multi-language-localization-dynamically)                                                                     |
| 17.   | [How do you implement RTL support for languages like Arabic/Hebrew?](#question-17-how-do-you-implement-rtl-support-for-languages-like-arabichebrew)                                                      |
| 18.   | [How do you use Hermes profiler to optimize JS execution?](#question-18-how-do-you-use-hermes-profiler-to-optimize-js-execution)                                                                         |
| 19.   | [How do you perform OTA updates securely with CodePush?](#question-19-how-do-you-perform-ota-updates-securely-with-codepush)                                                                             |
| 20.   | [How do you prepare a large-scale React Native app for multiple device types and OS versions?](#question-20-how-do-you-prepare-a-large-scale-react-native-app-for-multiple-device-types-and-os-versions) |

## Question 1. How do you **integrate Lottie animations**?

# Short answer

**Lottie** allows you to render high-quality vector animations exported from **Adobe After Effects** (via Bodymovin JSON) in React Native. In modern React Native projects, use **`lottie-react-native`**, and for Expo projects use **`lottie-react-native`** together with Expo's supported runtime. Control playback using props (`autoPlay`, `loop`) or refs (`play()`, `pause()`, `reset()`).

---

# Explanation

Lottie animations are ideal for:

- Loading indicators
- Success/error states
- Empty screens
- Onboarding animations
- Interactive UI feedback

Unlike GIFs or videos, Lottie:

- Uses vector-based JSON animations
- Has much smaller file sizes
- Scales to any screen resolution
- Supports playback controls
- Consumes less memory than equivalent videos (depending on animation complexity)

## How it works

1. Designer creates animation in **Adobe After Effects**
2. Animation is exported using **Bodymovin**
3. JSON file is added to the React Native project
4. `LottieView` renders the animation using native rendering

```text
After Effects
      │
Bodymovin Export
      │
 animation.json
      │
 LottieView
      │
 Native Animation Rendering
```

---

## Basic API

```tsx
<LottieView source={require("./success.json")} autoPlay loop />
```

Common props:

- `source`
- `autoPlay`
- `loop`
- `speed`
- `progress`
- `resizeMode`

Useful methods (via ref):

- `play()`
- `pause()`
- `reset()`

---

## Controlling animations

Instead of auto-playing, you can trigger animations:

- After API success
- After login
- On button press
- When screen becomes visible
- Based on gesture progress

Example:

```tsx
animationRef.current?.play();
```

---

## New Architecture (Fabric)

Recent versions of **`lottie-react-native`** support React Native's **New Architecture** (Fabric and TurboModules). Benefits include:

- Better interoperability with native rendering
- Lower bridge overhead
- Improved animation consistency
- Better performance during concurrent rendering

Lottie rendering itself is primarily native, so it avoids frequent JavaScript-to-native communication once playback starts.

---

# Example

### Expo setup

```bash
npx create-expo-app LottieDemo --template
cd LottieDemo

npx expo install lottie-react-native
```

Place `success.json` inside:

```text
assets/
   success.json
```

```tsx
import { useRef } from "react";
import { Button, View } from "react-native";
import LottieView from "lottie-react-native";

export default function App() {
  const animationRef = useRef<LottieView>(null);

  return (
    <View
      style={{
        flex: 1,
        justifyContent: "center",
        alignItems: "center",
      }}
    >
      <LottieView
        ref={animationRef}
        source={require("./assets/success.json")}
        style={{ width: 220, height: 220 }}
        loop={false}
      />

      <Button
        title="Play Animation"
        onPress={() => animationRef.current?.play()}
      />
    </View>
  );
}
```

Run:

```bash
npx expo start
```

Or for React Native CLI:

```bash
npx react-native run-android
npx react-native run-ios
```

---

# Tooling & Setup

**Recommended stack**

- **Expo** for rapid development and easy native dependency management.
- **React Native CLI** when your app requires custom native integrations or advanced native configuration.
- Avoid **Create React App (CRA)**, as it is deprecated and intended for web rather than React Native.

**Metro Bundler**

- Metro bundles the JavaScript and static assets (including local animation JSON files).
- Keep Lottie JSON files in your project assets and load them with `require()` for optimal bundling.

**ESM vs CommonJS**

- Modern React Native projects use ES Modules (`import`/`export`).
- Metro handles module resolution and transpilation; CommonJS is mainly encountered in older libraries or build tooling.

---

# Performance

Lottie is generally efficient, but large or overly complex animations can still impact performance.

### Best practices

- Keep JSON files as small as possible.
- Remove unused layers from After Effects.
- Avoid unnecessarily high frame rates.
- Prefer vector animations over long GIFs.
- Stop animations when off-screen.
- Use `loop={false}` when continuous looping is unnecessary.

### Profiling

Use:

- React DevTools Profiler
- Flipper Performance plugin
- React Native Perf Monitor
- Android Studio Profiler
- Xcode Instruments

### Optimization techniques

- Wrap parent components with `React.memo` when appropriate.
- Use `useCallback` for playback handlers.
- Use `useMemo` for derived values.
- Lazy-load animation-heavy screens with `React.lazy` where applicable.
- Optimize images used alongside animations.
- If integrating custom native modules, ensure they are compatible with the New Architecture to minimize bridge overhead.

---

# Testing

### Unit / Integration

Use Jest with React Native Testing Library.

Example:

```bash
npm test
```

Example assertion:

```tsx
expect(getByTestId("success-animation")).toBeTruthy();
```

Mock `lottie-react-native` if animation rendering isn't relevant to the test.

### End-to-End

Use:

- Detox
- Maestro

Verify:

- Animation appears
- Button starts playback
- Success screen renders after animation completes

---

# Ops & Deployment

- Use structured logging (e.g., `console` in development and a production logging service such as Sentry or Firebase Crashlytics).
- Handle animation asset loading errors gracefully with fallback UI.
- Bundle animation JSON files with the app for offline availability.
- Use **Expo EAS Build** or **GitHub Actions** / **Bitrise** for CI/CD.
- Validate animations on both iOS and Android before release.
- For updates, Expo OTA Updates or solutions like CodePush (where supported) can deliver animation changes without a full app release, subject to platform policies.

---

# Pitfalls

- Large or highly detailed animation JSON files can cause dropped frames and increased memory usage.
- Forgetting to stop or unmount looping animations can waste CPU and battery.
- Assuming every After Effects feature is supported—Bodymovin/Lottie supports many, but not all, effects and plugins.

## Question 2. How do you **handle custom font loading at runtime**?

## Question 3. How do you **implement runtime feature toggles**?

## Question 4. How do you **integrate feature flags in React Native**?

## Question 5. How do you **secure API calls with certificate pinning**?

## Question 6. How do you **use AppState to handle background/foreground tasks**?

## Question 7. How do you **implement push notifications with FCM topics**?

## Question 8. How do you **handle push notifications while app is killed**?

## Question 9. How do you **implement data sync for offline-first apps**?

## Question 10. How do you **implement websocket reconnection logic**?

## Question 11. How do you **use Redux Toolkit with RTK Query** for API caching?

## Question 12. How do you **mock network requests in Jest tests**?

## Question 13. How do you **test UI components with React Native Testing Library**?

## Question 14. How do you **test animations with Jest or Detox**?

## Question 15. How do you **simulate gestures in Detox tests**?

## Question 16. How do you **handle multi-language localization dynamically**?

## Question 17. How do you **implement RTL support for languages like Arabic/Hebrew**?

## Question 18. How do you **use Hermes profiler to optimize JS execution**?

## Question 19. How do you **perform OTA updates securely with CodePush**?

## Question 20. How do you **prepare a large-scale React Native app for multiple device types and OS versions**?
