# Set 24

| S.No. | Question                                                                                                                                                        |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you use accessibility props for VoiceOver and TalkBack?](#question-1-how-do-you-use-accessibility-props-for-voiceover-and-talkback)                     |
| 2.    | [How do you implement screen reader-friendly components?](#question-2-how-do-you-implement-screen-reader-friendly-components)                                   |
| 3.    | [How do you implement dynamic font scaling?](#question-3-how-do-you-implement-dynamic-font-scaling)                                                             |
| 4.    | [How do you detect device orientation changes dynamically?](#question-4-how-do-you-detect-device-orientation-changes-dynamically)                               |
| 5.    | [How do you use Dimensions API with responsive layouts?](#question-5-how-do-you-use-dimensions-api-with-responsive-layouts)                                     |
| 6.    | [How do you use PixelRatio API for image scaling?](#question-6-how-do-you-use-pixelratio-api-for-image-scaling)                                                 |
| 7.    | [How do you detect device type (phone vs tablet) dynamically?](#question-7-how-do-you-detect-device-type-phone-vs-tablet-dynamically)                           |
| 8.    | [How do you implement a carousel with snapping behavior?](#question-8-how-do-you-implement-a-carousel-with-snapping-behavior)                                   |
| 9.    | [How do you handle touchable areas in complex nested components?](#question-9-how-do-you-handle-touchable-areas-in-complex-nested-components)                   |
| 10.   | [How do you implement custom scroll indicators?](#question-10-how-do-you-implement-custom-scroll-indicators)                                                    |
| 11.   | [How do you integrate React Native with native Android libraries?](#question-11-how-do-you-integrate-react-native-with-native-android-libraries)                |
| 12.   | [How do you integrate React Native with native iOS libraries?](#question-12-how-do-you-integrate-react-native-with-native-ios-libraries)                        |
| 13.   | [How do you implement Hermes bytecode optimization?](#question-13-how-do-you-implement-hermes-bytecode-optimization)                                            |
| 14.   | [How do you measure app startup performance?](#question-14-how-do-you-measure-app-startup-performance)                                                          |
| 15.   | [How do you reduce app bundle size with Metro bundler?](#question-15-how-do-you-reduce-app-bundle-size-with-metro-bundler)                                      |
| 16.   | [How do you split JS bundle for large apps?](#question-16-how-do-you-split-js-bundle-for-large-apps)                                                            |
| 17.   | [How do you lazy load screens dynamically?](#question-17-how-do-you-lazy-load-screens-dynamically)                                                              |
| 18.   | [How do you optimize FlatList with getItemLayout?](#question-18-how-do-you-optimize-flatlist-with-getitemlayout)                                                |
| 19.   | [How do you profile JS thread using Flipper?](#question-19-how-do-you-profile-js-thread-using-flipper)                                                          |
| 20.   | [How do you profile UI thread using Android Profiler / Xcode Instruments?](#question-20-how-do-you-profile-ui-thread-using-android-profiler--xcode-instruments) |

## Question 1. How do you **use accessibility props for VoiceOver and TalkBack**?

# Accessibility Props for VoiceOver and TalkBack in React Native

## Short answer

React Native provides built-in accessibility props such as `accessible`, `accessibilityLabel`, `accessibilityHint`, `accessibilityRole`, `accessibilityState`, `accessibilityValue`, and `accessibilityActions` to make components usable with **VoiceOver (iOS)** and **TalkBack (Android)**. These props allow screen readers to correctly describe UI elements, their purpose, and their current state.

---

# Explanation

Accessibility should be considered part of the component architecture rather than an afterthought. Every interactive element should expose meaningful semantics so assistive technologies can interpret the UI.

## Important accessibility props

| Prop                                  | Purpose                                                              |
| ------------------------------------- | -------------------------------------------------------------------- |
| `accessible`                          | Groups children into a single accessible element.                    |
| `accessibilityLabel`                  | Text announced by the screen reader.                                 |
| `accessibilityHint`                   | Explains what happens after activation.                              |
| `accessibilityRole`                   | Describes the UI element (button, image, header, switch, etc.).      |
| `accessibilityState`                  | Announces state such as disabled, checked, selected, expanded, busy. |
| `accessibilityValue`                  | Announces values for sliders or progress bars.                       |
| `accessibilityLiveRegion` (Android)   | Announces dynamic content updates.                                   |
| `importantForAccessibility` (Android) | Controls accessibility focus behavior.                               |
| `accessibilityElementsHidden` (iOS)   | Hides children from VoiceOver.                                       |
| `onAccessibilityAction`               | Supports custom accessibility actions.                               |

Example announcement:

```
"Submit button.
Double tap to activate."
```

instead of

```
TouchableOpacity
```

---

## Component architecture

A senior React Native application usually wraps common UI elements in reusable components that automatically include accessibility defaults.

Example:

```tsx
<AppButton
  label="Save"
  accessibilityLabel="Save profile"
  accessibilityHint="Saves your changes"
/>
```

instead of manually adding accessibility props throughout the application.

This keeps accessibility consistent and prevents regressions.

---

## Rendering behavior

Accessibility props themselves don't cause expensive re-renders.

However:

- changing labels frequently causes new screen reader announcements
- updating accessibility state should happen only when the UI state changes
- avoid unnecessary updates to accessibility values during animations

Example:

```tsx
accessibilityState={{ disabled: loading }}
```

only changes when `loading` changes.

---

## State management

Accessibility state should always reflect application state.

Example:

```tsx
const loading = useSelector(selectLoading);

<Button
  accessibilityState={{
    disabled: loading,
    busy: loading,
  }}
/>;
```

When using Context, Redux, Zustand, or React Query, synchronize accessibility props with the source of truth rather than maintaining duplicate state.

---

## New Architecture (Fabric)

With the New Architecture:

- accessibility updates flow through Fabric's synchronous rendering model
- native semantics are more consistent
- TurboModules improve communication with native accessibility APIs
- less bridge overhead for frequent UI updates

The accessibility API remains the same, but the rendering pipeline is more efficient.

---

# Example

**Expo + TypeScript**

Create project:

```bash
npx create-expo-app AccessibleDemo --template expo-template-blank-typescript

cd AccessibleDemo

npm start
```

`App.tsx`

```tsx
import React, { useState } from "react";
import { SafeAreaView, Switch, Text, TouchableOpacity } from "react-native";

export default function App() {
  const [enabled, setEnabled] = useState(false);

  return (
    <SafeAreaView style={{ padding: 30 }}>
      <TouchableOpacity
        accessible
        accessibilityRole="button"
        accessibilityLabel="Submit form"
        accessibilityHint="Submits the registration form"
        onPress={() => {}}
        style={{
          backgroundColor: "#1976D2",
          padding: 16,
          borderRadius: 8,
          marginBottom: 30,
        }}
      >
        <Text style={{ color: "white" }}>Submit</Text>
      </TouchableOpacity>

      <Switch
        value={enabled}
        onValueChange={setEnabled}
        accessibilityRole="switch"
        accessibilityLabel="Enable notifications"
        accessibilityState={{ checked: enabled }}
      />
    </SafeAreaView>
  );
}
```

Run:

```bash
npm run android
```

or

```bash
npm run ios
```

Enable:

- **VoiceOver** on iOS
- **TalkBack** on Android

Navigate through the controls to hear the announcements.

---

# Tooling & Setup

**Recommended stack**

- Expo SDK (recommended for most apps)
- React Native CLI for projects requiring custom native code

Avoid **Create React App (CRA)** because it is deprecated and intended for web, not mobile.

### Metro

Metro automatically bundles accessibility-related code without additional configuration.

### Module format

- Prefer **ES Modules (`import/export`)**
- Use CommonJS only when integrating older tooling or libraries.

---

# Performance

Accessibility has minimal runtime cost when implemented correctly.

Best practices:

- memoize reusable accessible components using `React.memo`
- use `useCallback` for accessibility action handlers
- avoid updating accessibility labels every animation frame
- use `useMemo` for computed accessibility values
- optimize images with meaningful `accessibilityLabel` only when they convey information; decorative images should often be hidden from accessibility
- leverage Fabric and TurboModules to reduce bridge overhead in modern React Native apps

Profiling tools:

- React DevTools Profiler
- Flipper
- React Native Performance Monitor (`Show Perf Monitor`)
- Android Accessibility Scanner
- iOS Accessibility Inspector

---

# Testing

Unit & integration:

```bash
npm test
```

Example:

```tsx
import { render } from "@testing-library/react-native";

test("button has accessibility label", () => {
  const { getByLabelText } = render(<App />);

  expect(getByLabelText("Submit form")).toBeTruthy();
});
```

E2E:

- Detox
- Maestro

Example:

```bash
maestro test flow.yaml
```

Also test manually with:

- VoiceOver enabled
- TalkBack enabled
- Dynamic Type / Larger Text
- High Contrast modes

---

# Ops & Deployment

Before release:

- test every major flow with VoiceOver and TalkBack enabled
- ensure analytics and logging don't expose spoken accessibility labels containing sensitive data
- use error boundaries so accessibility focus isn't lost during crashes
- verify offline states announce loading and connectivity changes appropriately
- automate accessibility checks in CI using Jest, React Native Testing Library, and E2E suites (Detox or Maestro)
- use Expo EAS, Bitrise, or GitHub Actions for CI/CD
- validate accessibility during App Store and Play Store release testing
- use OTA updates (Expo Updates) or CodePush (where appropriate and supported) carefully to avoid introducing accessibility regressions without adequate testing

---

# Pitfalls

- **Don't rely on visible text alone**—provide explicit `accessibilityLabel` when the visual label is unclear (for example, icon-only buttons).
- **Don't make every `View` accessible**—only expose meaningful, interactive, or informative elements to avoid excessive screen reader navigation.
- **Don't forget accessibility state**—keep `accessibilityState`, `accessibilityValue`, and related props synchronized with the actual UI state.

## Question 2. How do you **implement screen reader-friendly components**?

## Question 3. How do you **implement dynamic font scaling**?

## Question 4. How do you **detect device orientation changes dynamically**?

## Question 5. How do you **use Dimensions API with responsive layouts**?

## Question 6. How do you **use PixelRatio API for image scaling**?

## Question 7. How do you **detect device type (phone vs tablet) dynamically**?

## Question 8. How do you **implement a carousel with snapping behavior**?

## Question 9. How do you **handle touchable areas in complex nested components**?

## Question 10. How do you **implement custom scroll indicators**?

## Question 11. How do you **integrate React Native with native Android libraries**?

## Question 12. How do you **integrate React Native with native iOS libraries**?

## Question 13. How do you **implement Hermes bytecode optimization**?

## Question 14. How do you **measure app startup performance**?

## Question 15. How do you **reduce app bundle size with Metro bundler**?

## Question 16. How do you **split JS bundle for large apps**?

## Question 17. How do you **lazy load screens dynamically**?

## Question 18. How do you **optimize FlatList with getItemLayout**?

## Question 19. How do you **profile JS thread using Flipper**?

## Question 20. How do you **profile UI thread using Android Profiler / Xcode Instruments**?
