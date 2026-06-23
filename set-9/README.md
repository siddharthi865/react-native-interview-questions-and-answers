# Set 9

| S.No. | Question                                                                                                                                                                 |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How do you handle device rotation for video players?](#question-1-how-do-you-handle-device-rotation-for-video-players)                                                  |
| 2.    | [How do you implement custom loading indicators?](#question-2-how-do-you-implement-custom-loading-indicators)                                                            |
| 3.    | [How do you implement skeleton loaders for lists?](#question-3-how-do-you-implement-skeleton-loaders-for-lists)                                                          |
| 4.    | [How do you handle deep linking for push notifications?](#question-4-how-do-you-handle-deep-linking-for-push-notifications)                                              |
| 5.    | [How do you implement dynamic icons based on state?](#question-5-how-do-you-implement-dynamic-icons-based-on-state)                                                      |
| 6.    | [How do you implement share functionality?](#question-6-how-do-you-implement-share-functionality)                                                                        |
| 7.    | [How do you use Linking API to open URLs?](#question-7-how-do-you-use-linking-api-to-open-urls)                                                                          |
| 8.    | [How do you implement clipboard copy/paste?](#question-8-how-do-you-implement-clipboard-copypaste)                                                                       |
| 9.    | [How do you integrate Google Maps or Mapbox?](#question-9-how-do-you-integrate-google-maps-or-mapbox)                                                                    |
| 10.   | [How do you implement location tracking in React Native?](#question-10-how-do-you-implement-location-tracking-in-react-native)                                           |
| 11.   | [How does React Native handle threading (JS thread vs UI thread)?](#question-11-how-does-react-native-handle-threading-js-thread-vs-ui-thread)                           |
| 12.   | [How do you optimize JS thread usage?](#question-12-how-do-you-optimize-js-thread-usage)                                                                                 |
| 13.   | [How do you integrate native modules for custom functionality?](#question-13-how-do-you-integrate-native-modules-for-custom-functionality)                               |
| 14.   | [How do you create a custom iOS or Android component?](#question-14-how-do-you-create-a-custom-ios-or-android-component)                                                 |
| 15.   | [How do you implement offline storage with Realm or WatermelonDB?](#question-15-how-do-you-implement-offline-storage-with-realm-or-watermelondb)                         |
| 16.   | [How do you implement push notifications using Firebase Cloud Messaging (FCM)?](#question-16-how-do-you-implement-push-notifications-using-firebase-cloud-messaging-fcm) |
| 17.   | [How do you implement background location tracking?](#question-17-how-do-you-implement-background-location-tracking)                                                     |
| 18.   | [How do you implement background fetch tasks?](#question-18-how-do-you-implement-background-fetch-tasks)                                                                 |
| 19.   | [How do you integrate native camera modules?](#question-19-how-do-you-integrate-native-camera-modules)                                                                   |
| 20.   | [How do you handle device permissions in React Native?](#question-20-how-do-you-handle-device-permissions-in-react-native)                                               |

## Question 1. How do you handle **device rotation for video players**?

# Short answer

Handle device rotation by listening to orientation changes (or screen dimensions), updating the video player's layout (e.g., fullscreen in landscape), and locking/unlocking orientation when appropriate. In modern React Native, pair a video library such as `react-native-video` or Expo Video with an orientation library (`expo-screen-orientation` in Expo or `react-native-orientation-locker` for React Native CLI) to provide a smooth fullscreen experience.

---

# Explanation

A production-grade video player should do more than just resize when the device rotates.

### 1. Detect orientation changes

Common approaches:

- **Expo:** `expo-screen-orientation`
- **React Native CLI:** `react-native-orientation-locker`
- **Responsive layouts:** `useWindowDimensions()`

Example flow:

```
Portrait
   │
   ▼
Orientation Changed
   │
   ▼
Landscape?
   │
 ┌─┴──────────────┐
 │                │
Yes              No
 │                │
 ▼                ▼
Enter Fullscreen Exit Fullscreen
Resize Player    Restore Layout
```

---

### 2. Resize the video

Instead of fixed dimensions:

```tsx
const { width, height } = useWindowDimensions();

<Video style={{ width, height: height * 0.3 }} />;
```

In landscape:

```tsx
style={{
  width,
  height,
}}
```

or absolutely position it:

```tsx
StyleSheet.absoluteFillObject;
```

---

### 3. Lock orientation when entering fullscreen

Many streaming apps behave like this:

- Portrait → embedded player
- Tap fullscreen
- Lock to landscape
- Exit fullscreen
- Return to portrait

This avoids accidental rotations.

---

### 4. Handle safe areas

Landscape devices often have:

- camera notch
- Dynamic Island
- rounded corners

Wrap fullscreen video with:

```tsx
SafeAreaView;
```

or use

```tsx
react - native - safe - area - context;
```

to avoid clipped controls.

---

### 5. Hide unnecessary UI

When fullscreen:

- hide header
- hide tab bar
- hide status bar

Example:

```tsx
<StatusBar hidden />
```

---

### 6. New Architecture considerations

With **Fabric**, layout updates after orientation changes are generally smoother because rendering is more efficient. Orientation handling itself is still performed through native APIs or libraries, but the UI updates benefit from reduced rendering overhead.

---

# Example (Expo + TypeScript)

### Create project

```bash
npx create-expo-app VideoRotationDemo --template expo-template-blank-typescript

cd VideoRotationDemo

npx expo install expo-video expo-screen-orientation
```

`App.tsx`

```tsx
import { useEffect, useState } from "react";
import { View, Button, StyleSheet } from "react-native";
import { VideoView, useVideoPlayer } from "expo-video";
import * as ScreenOrientation from "expo-screen-orientation";

export default function App() {
  const [fullscreen, setFullscreen] = useState(false);

  const player = useVideoPlayer(
    "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4",
    (player) => {
      player.play();
    },
  );

  useEffect(() => {
    const sub = ScreenOrientation.addOrientationChangeListener(
      ({ orientationInfo }) => {
        const landscape =
          orientationInfo.orientation ===
            ScreenOrientation.Orientation.LANDSCAPE_LEFT ||
          orientationInfo.orientation ===
            ScreenOrientation.Orientation.LANDSCAPE_RIGHT;

        setFullscreen(landscape);
      },
    );

    return () => {
      ScreenOrientation.removeOrientationChangeListener(sub);
    };
  }, []);

  async function toggleFullscreen() {
    if (fullscreen) {
      await ScreenOrientation.lockAsync(
        ScreenOrientation.OrientationLock.PORTRAIT_UP,
      );
    } else {
      await ScreenOrientation.lockAsync(
        ScreenOrientation.OrientationLock.LANDSCAPE,
      );
    }
  }

  return (
    <View style={styles.container}>
      <VideoView
        player={player}
        style={fullscreen ? styles.fullscreen : styles.normal}
        allowsFullscreen
      />

      <Button
        title={fullscreen ? "Exit Fullscreen" : "Fullscreen"}
        onPress={toggleFullscreen}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
  },
  normal: {
    width: "100%",
    height: 220,
  },
  fullscreen: {
    ...StyleSheet.absoluteFillObject,
  },
});
```

Run:

```bash
npx expo start
```

Open on Android or iOS using Expo Go or a development build.

---

# Tooling & Setup

- **Preferred stack:** Expo for quick setup (`expo-video`, `expo-screen-orientation`) or React Native CLI for apps requiring native customization.
- **Avoid Create React App (CRA):** It is deprecated and intended for web, not React Native.
- **Bundler:** React Native uses **Metro**, which is optimized for mobile development and fast refresh.
- **Modules:** Modern React Native projects use ES Modules (`import`/`export`). CommonJS is primarily encountered in older libraries or build tooling.

---

# Performance

### Minimize unnecessary re-renders

```tsx
const VideoPlayer = React.memo(VideoPlayerComponent);
```

Memoize callbacks:

```tsx
const onFullscreen = useCallback(() => {}, []);
```

Avoid recreating styles:

```tsx
const styles = useMemo(() => ..., [fullscreen]);
```

### Profile performance

- React DevTools Profiler to identify unnecessary renders.
- Flipper (for React Native CLI) to inspect layout, performance, and networking.
- React Native Perf Monitor (`Show Perf Monitor`) to observe FPS during rotation and fullscreen transitions.

### Native performance

- Prefer native fullscreen implementations provided by the video library.
- Keep overlay controls lightweight to avoid dropped frames during rotation.
- Use optimized image assets (thumbnails, posters) and appropriate caching.

---

# Testing

**Unit / Integration (Jest + React Native Testing Library)**

```bash
npm test
```

Verify:

- fullscreen button toggles state
- orientation listener is registered and cleaned up
- correct styles are applied for portrait vs. landscape

**E2E**

Use **Detox** (React Native CLI) or **Maestro** to automate:

- rotate device
- enter fullscreen
- verify controls
- exit fullscreen

Example (Maestro):

```yaml
- launchApp
- tapOn: "Fullscreen"
- rotateDevice: landscape
- assertVisible: "Exit Fullscreen"
```

---

# Ops & Deployment

- Log playback and orientation events for debugging (e.g., fullscreen entered/exited, playback errors).
- Gracefully handle video loading failures with retry UI and error boundaries where appropriate.
- Support offline playback by caching or downloading videos when the product requires it.
- Use **Expo Application Services (EAS)** or **GitHub Actions** for CI/CD, and **Bitrise** for more advanced mobile pipelines.
- Plan deployment for App Store and Google Play requirements, and use Expo OTA updates where applicable (keeping in mind that native dependency changes still require a new binary).

---

# Pitfalls

- Forgetting to remove orientation listeners, causing memory leaks.
- Using fixed width/height values instead of responsive layouts (`useWindowDimensions` or flex-based sizing).
- Not accounting for safe areas, resulting in controls being obscured by notches or system UI.

## Question 2. How do you implement **custom loading indicators**?

## Question 3. How do you implement **skeleton loaders** for lists?

## Question 4. How do you handle **deep linking for push notifications**?

## Question 5. How do you implement **dynamic icons** based on state?

## Question 6. How do you implement **share functionality**?

## Question 7. How do you use **Linking API** to open URLs?

## Question 8. How do you implement **clipboard copy/paste**?

## Question 9. How do you integrate **Google Maps or Mapbox**?

## Question 10. How do you implement **location tracking** in React Native?

## Question 11. How does React Native handle **threading (JS thread vs UI thread)**?

## Question 12. How do you optimize **JS thread usage**?

## Question 13. How do you integrate **native modules for custom functionality**?

## Question 14. How do you create a **custom iOS or Android component**?

## Question 15. How do you implement **offline storage with Realm or WatermelonDB**?

## Question 16. How do you implement **push notifications using Firebase Cloud Messaging (FCM)**?

## Question 17. How do you implement **background location tracking**?

## Question 18. How do you implement **background fetch tasks**?

## Question 19. How do you integrate **native camera modules**?

## Question 20. How do you handle **device permissions** in React Native?
