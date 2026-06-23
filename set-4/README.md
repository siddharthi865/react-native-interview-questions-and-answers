# Set 4

| S.No. | Question                                                                                                                                  |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you handle local notifications?](#question-1-how-do-you-handle-local-notifications)                                               |
| 2.    | [How do you integrate camera and image picker in React Native?](#question-2-how-do-you-integrate-camera-and-image-picker-in-react-native) |
| 3.    | [How do you record audio or video in React Native?](#question-3-how-do-you-record-audio-or-video-in-react-native)                         |
| 4.    | [How do you implement offline caching for API responses?](#question-4-how-do-you-implement-offline-caching-for-api-responses)             |
| 5.    | [How do you handle network connectivity changes?](#question-5-how-do-you-handle-network-connectivity-changes)                             |
| 6.    | [How do you measure app performance in React Native?](#question-6-how-do-you-measure-app-performance-in-react-native)                     |
| 7.    | [How do you reduce app bundle size?](#question-7-how-do-you-reduce-app-bundle-size)                                                       |
| 8.    | [How do you implement deep linking with React Navigation?](#question-8-how-do-you-implement-deep-linking-with-react-navigation)           |
| 9.    | [What are Headless JS tasks?](#question-9-what-are-headless-js-tasks)                                                                     |
| 10.   | [How do you use WebView in React Native?](#question-10-how-do-you-use-webview-in-react-native)                                            |
| 11.   | [What is the bridge in React Native?](#question-11-what-is-the-bridge-in-react-native)                                                    |
| 12.   | [How does React Native communicate with native modules?](#question-12-how-does-react-native-communicate-with-native-modules)              |
| 13.   | [How do you create a custom native module for iOS or Android?](#question-13-how-do-you-create-a-custom-native-module-for-ios-or-android)  |
| 14.   | [How do you integrate third-party native libraries?](#question-14-how-do-you-integrate-third-party-native-libraries)                      |
| 15.   | [How do you debug native crashes in React Native?](#question-15-how-do-you-debug-native-crashes-in-react-native)                          |
| 16.   | [How do you implement background tasks in React Native?](#question-16-how-do-you-implement-background-tasks-in-react-native)              |
| 17.   | [What is Hermes engine and its benefits?](#question-17-what-is-hermes-engine-and-its-benefits)                                            |
| 18.   | [How do you enable Hermes in a React Native project?](#question-18-how-do-you-enable-hermes-in-a-react-native-project)                    |
| 19.   | [How do you implement code splitting in React Native?](#question-19-how-do-you-implement-code-splitting-in-react-native)                  |
| 20.   | [How do you implement lazy loading of components?](#question-20-how-do-you-implement-lazy-loading-of-components)                          |

## Question 1. How do you handle **local notifications**?

# Short answer

Local notifications in React Native are scheduled and displayed by the device itself, without requiring a backend. The recommended approach today is:

- **Expo apps:** Use the `expo-notifications` library.
- **React Native CLI apps:** Use libraries like `react-native-notifications` or `@notifee/react-native` (recommended for advanced features).

A senior implementation includes:

- Requesting notification permissions.
- Scheduling immediate or future notifications.
- Handling foreground, background, and tapped notifications.
- Deep linking users to the correct screen.
- Managing platform differences (Android notification channels, iOS permissions).

---

# Explanation

A **local notification** is generated directly on the user's device rather than being sent from a remote server (push notification).

Typical use cases include:

- Medication reminders
- Daily habit reminders
- Calendar alerts
- Offline task reminders
- Alarm apps

## Basic flow

```
User enables reminders
        │
        ▼
Request notification permission
        │
        ▼
Schedule local notification
        │
        ▼
OS stores notification
        │
        ▼
Notification appears at scheduled time
        │
        ▼
User taps notification
        │
        ▼
Navigate to appropriate screen
```

---

## Expo approach (recommended for Expo apps)

Expo provides a cross-platform API through `expo-notifications`.

Typical workflow:

1. Ask for permissions.
2. Configure notification handler.
3. Schedule notification.
4. Listen when user taps notification.

Advantages:

- Very little native configuration
- OTA update friendly
- Excellent for most apps

---

## React Native CLI approach

For bare React Native apps, production teams commonly use:

- **Notifee** (most feature rich)
- `react-native-notifications`

Notifee supports:

- Android channels
- Scheduled notifications
- Repeating reminders
- Foreground services
- Action buttons
- Grouped notifications
- Rich notifications
- Badge counts

It is generally considered the production standard for advanced notification requirements.

---

## iOS considerations

iOS requires explicit permission.

Possible permission states:

- Granted
- Denied
- Provisional
- Critical Alerts (special entitlement)

If permission is denied, guide users to Settings.

---

## Android considerations

Android 13+ also requires runtime notification permission.

Additionally:

- Notification Channels are mandatory.
- Importance levels determine visibility.
- Channels cannot be modified after creation.

Example:

```
Reminder Channel
Importance: High

↓

Daily reminders appear with sound.
```

---

## Navigation after tapping

A common interview topic is handling notification taps.

Example flow:

```
Notification

↓

User taps

↓

Read notification payload

↓

Navigate

↓

ReminderDetailScreen
```

Example payload:

```json
{
  "screen": "ReminderDetails",
  "id": "42"
}
```

The app reads the payload and navigates accordingly.

---

## Example

**Stack:** Expo + TypeScript

Create the project:

```bash
npx create-expo-app NotificationDemo --template expo-template-blank-typescript

cd NotificationDemo

npx expo install expo-notifications expo-device
```

Run:

```bash
npx expo start
```

```tsx
import { useEffect } from "react";
import { Button, View } from "react-native";
import * as Notifications from "expo-notifications";

Notifications.setNotificationHandler({
  handleNotification: async () => ({
    shouldShowBanner: true,
    shouldShowList: true,
    shouldPlaySound: true,
    shouldSetBadge: false,
  }),
});

export default function App() {
  useEffect(() => {
    Notifications.requestPermissionsAsync();
  }, []);

  const scheduleNotification = async () => {
    await Notifications.scheduleNotificationAsync({
      content: {
        title: "Reminder",
        body: "Time to drink water 💧",
      },
      trigger: {
        type: Notifications.SchedulableTriggerInputTypes.TIME_INTERVAL,
        seconds: 10,
      },
    });
  };

  return (
    <View style={{ marginTop: 100 }}>
      <Button title="Schedule Notification" onPress={scheduleNotification} />
    </View>
  );
}
```

Press the button.

After 10 seconds, the device displays the notification.

---

# Tooling & Setup

**Recommended stacks**

- **Expo + `expo-notifications`**: Best for most applications, quick setup, and OTA updates.
- **React Native CLI + Notifee**: Preferred when you need advanced notification capabilities (foreground services, grouped notifications, custom actions).

Avoid **Create React App (CRA)**, as it is deprecated and intended for web, not mobile.

**Module system**

- React Native projects today primarily use **ES Modules (`import`/`export`)**.
- Some native packages may internally expose CommonJS, but application code should remain ESM.

**Bundler**

- **Metro** is the default React Native bundler.
- Expo also uses Metro, with additional tooling for managed workflows.

**New Architecture**

With the React Native **New Architecture (Fabric + TurboModules)**, well-maintained native notification libraries benefit from lower bridge overhead and more efficient native integration. Notifications themselves are largely OS-driven, so performance is usually dominated by app startup and navigation after a notification tap rather than notification delivery.

---

# Performance

Local notifications themselves have minimal runtime cost, but surrounding code can affect responsiveness.

**Optimization techniques**

- Use `React.memo` for screens that render notification-driven UI.
- Use `useCallback` for notification handlers passed to child components.
- Use `useMemo` for derived reminder lists.
- Avoid expensive work inside notification listeners; defer heavy processing until after navigation.
- Lazy-load non-critical screens with React Navigation where appropriate.
- Optimize notification icons and images to reduce app size.

**Profiling**

- React DevTools Profiler
- Flipper Performance plugin (React Native CLI)
- React Native Performance Monitor (`Show Perf Monitor`)
- Native profilers (Android Studio Profiler, Xcode Instruments) for startup analysis

---

# Testing

**Unit & integration**

Use **Jest** with **React Native Testing Library** to verify scheduling logic and permission handling by mocking notification APIs.

Example:

```bash
npm test
```

Mock `expo-notifications` or Notifee and assert that `scheduleNotificationAsync` (or the equivalent API) is called with the expected payload.

**End-to-end**

Use **Detox** or **Maestro** to validate reminder flows and navigation after tapping notifications on real devices or emulators.

Example:

```bash
npx detox test
```

---

# Ops & Deployment

- Log notification scheduling successes/failures using tools like Sentry or Crashlytics.
- Gracefully handle denied permissions by explaining why reminders are useful and providing a shortcut to system settings.
- Persist reminder schedules locally (e.g., AsyncStorage or SQLite) so they can be recreated if needed after app reinstall or data restoration.
- For CI/CD:
  - **Expo:** Use EAS Build and EAS Update for builds and OTA updates.
  - **React Native CLI:** Use GitHub Actions, Bitrise, or similar services for Android/iOS builds.

- Verify platform-specific behavior before release:
  - Android notification channels and Android 13+ permission flow.
  - iOS notification permissions, background behavior, and App Store compliance.

---

# Pitfalls

- Forgetting to request notification permission before scheduling, leading to silent failures.
- Not creating Android notification channels before sending notifications (required for Android 8+).
- Failing to handle notification taps consistently from foreground, background, and cold-start states, resulting in incorrect navigation.

## Question 2. How do you integrate **camera and image picker** in React Native?

## Question 3. How do you record **audio or video** in React Native?

## Question 4. How do you implement **offline caching** for API responses?

## Question 5. How do you handle **network connectivity changes**?

## Question 6. How do you measure **app performance** in React Native?

## Question 7. How do you reduce **app bundle size**?

## Question 8. How do you implement **deep linking with React Navigation**?

## Question 9. What are **Headless JS tasks**?

## Question 10. How do you use **WebView** in React Native?

## Question 11. What is the **bridge in React Native**?

## Question 12. How does React Native communicate with **native modules**?

## Question 13. How do you create a **custom native module** for iOS or Android?

## Question 14. How do you integrate **third-party native libraries**?

## Question 15. How do you debug **native crashes** in React Native?

## Question 16. How do you implement **background tasks** in React Native?

## Question 17. What is **Hermes** engine and its benefits?

## Question 18. How do you enable **Hermes** in a React Native project?

## Question 19. How do you implement **code splitting** in React Native?

## Question 20. How do you implement **lazy loading** of components?
