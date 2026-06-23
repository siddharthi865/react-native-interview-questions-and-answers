# Set 19

| S.No. | Question                                                                                                                                             |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you implement countdown timers?](#question-1-how-do-you-implement-countdown-timers)                                                          |
| 2.    | [How do you implement interval-based animations?](#question-2-how-do-you-implement-interval-based-animations)                                        |
| 3.    | [How do you implement component fade-in on mount?](#question-3-how-do-you-implement-component-fade-in-on-mount)                                      |
| 4.    | [How do you animate component position using interpolate?](#question-4-how-do-you-animate-component-position-using-interpolate)                      |
| 5.    | [How do you handle gestures for pinch-to-zoom?](#question-5-how-do-you-handle-gestures-for-pinch-to-zoom)                                            |
| 6.    | [How do you handle swipe-to-dismiss modals?](#question-6-how-do-you-handle-swipe-to-dismiss-modals)                                                  |
| 7.    | [How do you integrate local SQLite database?](#question-7-how-do-you-integrate-local-sqlite-database)                                                |
| 8.    | [How do you run raw SQL queries in React Native?](#question-8-how-do-you-run-raw-sql-queries-in-react-native)                                        |
| 9.    | [How do you handle complex relational data in SQLite?](#question-9-how-do-you-handle-complex-relational-data-in-sqlite)                              |
| 10.   | [How do you integrate Realm database for offline apps?](#question-10-how-do-you-integrate-realm-database-for-offline-apps)                           |
| 11.   | [How do you debug native crashes on Android?](#question-11-how-do-you-debug-native-crashes-on-android)                                               |
| 12.   | [How do you debug native crashes on iOS?](#question-12-how-do-you-debug-native-crashes-on-ios)                                                       |
| 13.   | [How do you analyze memory usage with Xcode Instruments?](#question-13-how-do-you-analyze-memory-usage-with-xcode-instruments)                       |
| 14.   | [How do you analyze memory usage with Android Profiler?](#question-14-how-do-you-analyze-memory-usage-with-android-profiler)                         |
| 15.   | [How do you profile JS thread performance using Flipper?](#question-15-how-do-you-profile-js-thread-performance-using-flipper)                       |
| 16.   | [How do you profile layout and rendering performance?](#question-16-how-do-you-profile-layout-and-rendering-performance)                             |
| 17.   | [How do you reduce memory usage in large lists?](#question-17-how-do-you-reduce-memory-usage-in-large-lists)                                         |
| 18.   | [How do you optimize image rendering using react-native-fast-image?](#question-18-how-do-you-optimize-image-rendering-using-react-native-fast-image) |
| 19.   | [How do you implement shared element transitions?](#question-19-how-do-you-implement-shared-element-transitions)                                     |
| 20.   | [How do you implement complex animations with Reanimated 2?](#question-20-how-do-you-implement-complex-animations-with-reanimated-2)                 |

## Question 1. How do you **implement countdown timers**?

# Short answer

A countdown timer in React Native is typically implemented using `useState`, `useEffect`, and `setInterval` (or `setTimeout`) while ensuring proper cleanup to avoid memory leaks. For production apps, account for app backgrounding using `AppState` or persist the target end time so the timer remains accurate after the app resumes.

---

# Explanation

A countdown timer repeatedly updates the remaining time until it reaches zero.

Typical flow:

1. Store the remaining seconds in state.
2. Start an interval when the component mounts.
3. Decrease the value every second.
4. Stop the timer when it reaches zero.
5. Clear the interval when the component unmounts.

```text
Start Timer
      │
      ▼
Every 1 second
      │
      ▼
Decrease remaining time
      │
      ▼
Reached 0?
   │        │
  No       Yes
   │        │
   ▼        ▼
Continue  Stop interval
```

### Why cleanup matters

If you don't clear an interval:

- it continues running after navigation
- it wastes CPU and battery
- it can cause "Can't perform a React state update on an unmounted component" warnings

Always return a cleanup function from `useEffect`.

### Production considerations

For OTP timers or flash-sale countdowns:

- Don't rely only on decrementing state.
- Store the target end timestamp.
- Compute:

```ts
remaining = endTime - Date.now();
```

This prevents incorrect timers after:

- app backgrounding
- screen lock
- device sleep
- JS thread pauses

---

# Example

**Expo (TypeScript)**

Create project:

```bash
npx create-expo-app CountdownDemo --template
cd CountdownDemo
npm start
```

```tsx
import React, { useEffect, useState } from "react";
import { View, Text, Button, StyleSheet } from "react-native";

const INITIAL_SECONDS = 10;

export default function App() {
  const [seconds, setSeconds] = useState(INITIAL_SECONDS);
  const [running, setRunning] = useState(false);

  useEffect(() => {
    if (!running) return;

    const interval = setInterval(() => {
      setSeconds((prev) => {
        if (prev <= 1) {
          clearInterval(interval);
          setRunning(false);
          return 0;
        }
        return prev - 1;
      });
    }, 1000);

    return () => clearInterval(interval);
  }, [running]);

  const reset = () => {
    setSeconds(INITIAL_SECONDS);
    setRunning(false);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.timer}>{seconds}s</Text>

      <Button
        title={running ? "Running..." : "Start"}
        onPress={() => setRunning(true)}
        disabled={running}
      />

      <Button title="Reset" onPress={reset} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    padding: 24,
  },
  timer: {
    fontSize: 48,
    textAlign: "center",
    marginBottom: 24,
  },
});
```

Run:

```bash
npm run android
```

or

```bash
npm run ios
```

---

# Tooling & Setup

**Recommended stack**

- Expo for most applications (quick setup, OTA updates, easy development).
- React Native CLI when native customization is required.
- Avoid Create React App (CRA); it is deprecated and intended for web, not React Native.

**Module system**

- React Native primarily uses ES Modules (`import`/`export`).
- CommonJS (`require`) is still supported for some dynamic imports and legacy packages.

**Bundler**

- Metro is the default React Native bundler.
- Enable Fast Refresh during development.
- Hermes is recommended for improved startup time and runtime performance.

---

# Performance

For a simple countdown, `setInterval` is sufficient, but there are important optimizations.

### 1. Minimize re-renders

Only update the timer component instead of the whole screen.

```tsx
const Timer = React.memo(() => {
  // countdown
});
```

---

### 2. Persist an end timestamp

Instead of:

```ts
seconds--;
```

use:

```ts
const remaining = Math.max(0, Math.floor((endTime - Date.now()) / 1000));
```

This avoids timer drift.

---

### 3. Handle backgrounding

Use `AppState` to recalculate remaining time when the app becomes active again.

---

### 4. For smooth animations

For visual countdowns (circular progress, progress bars), use:

- `Animated`
- `react-native-reanimated`

instead of updating React state every frame.

---

### 5. Profile

Use:

- React DevTools Profiler
- Flipper
- React Native Performance Monitor
- Hermes profiling

to verify the timer isn't causing unnecessary renders.

---

# Testing

### Unit test (Jest)

Use fake timers.

```tsx
jest.useFakeTimers();

act(() => {
  jest.advanceTimersByTime(5000);
});

expect(screen.getByText("5s")).toBeTruthy();
```

Run:

```bash
npm test
```

### Integration

Use React Native Testing Library to verify:

- timer starts
- timer reaches zero
- reset works

### E2E

Use:

- Detox (React Native CLI)
- Maestro (works well with Expo and CLI)

Example:

```bash
maestro test flow.yaml
```

---

# Ops & Deployment

For production timers:

- Log timer completion events.
- Handle background/foreground transitions with `AppState`.
- Persist end timestamps in storage if the timer must survive app restarts.
- Use OTA updates with Expo EAS where appropriate.
- Automate builds using GitHub Actions, Bitrise, or Expo EAS Build.
- For server-driven countdowns (e.g., sales or OTP expiry), synchronize with server time instead of relying solely on the device clock.

---

# Pitfalls

- **Don't forget cleanup**—always clear intervals in `useEffect`.
- **Don't rely solely on decrementing state**—calculate remaining time from an end timestamp to avoid drift.
- **Don't update the entire screen every second**—isolate the timer or memoize surrounding components.

## Question 2. How do you **implement interval-based animations**?

## Question 3. How do you **implement component fade-in on mount**?

## Question 4. How do you **animate component position using interpolate**?

## Question 5. How do you **handle gestures for pinch-to-zoom**?

## Question 6. How do you **handle swipe-to-dismiss modals**?

## Question 7. How do you **integrate local SQLite database**?

## Question 8. How do you **run raw SQL queries in React Native**?

## Question 9. How do you **handle complex relational data in SQLite**?

## Question 10. How do you **integrate Realm database for offline apps**?

## Question 11. How do you **debug native crashes on Android**?

## Question 12. How do you **debug native crashes on iOS**?

## Question 13. How do you **analyze memory usage with Xcode Instruments**?

## Question 14. How do you **analyze memory usage with Android Profiler**?

## Question 15. How do you **profile JS thread performance using Flipper**?

## Question 16. How do you **profile layout and rendering performance**?

## Question 17. How do you **reduce memory usage in large lists**?

## Question 18. How do you **optimize image rendering using react-native-fast-image**?

## Question 19. How do you **implement shared element transitions**?

## Question 20. How do you **implement complex animations with Reanimated 2**?
