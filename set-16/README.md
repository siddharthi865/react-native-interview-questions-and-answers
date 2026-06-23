# Set 16

| S.No. | Question                                                                                                                                                      |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is the difference between Expo managed workflow and bare workflow?](#question-1-what-is-the-difference-between-expo-managed-workflow-and-bare-workflow) |
| 2.    | [How do you eject from an Expo project?](#question-2-how-do-you-eject-from-an-expo-project)                                                                   |
| 3.    | [How do you run a React Native app on a physical device?](#question-3-how-do-you-run-a-react-native-app-on-a-physical-device)                                 |
| 4.    | [How do you use React Native CLI vs Expo CLI for building apps?](#question-4-how-do-you-use-react-native-cli-vs-expo-cli-for-building-apps)                   |
| 5.    | [What are controlled components in React Native forms?](#question-5-what-are-controlled-components-in-react-native-forms)                                     |
| 6.    | [What are uncontrolled components in React Native forms?](#question-6-what-are-uncontrolled-components-in-react-native-forms)                                 |
| 7.    | [How do you handle keyboard dismiss on tapping outside?](#question-7-how-do-you-handle-keyboard-dismiss-on-tapping-outside)                                   |
| 8.    | [How do you show placeholder text in TextInput?](#question-8-how-do-you-show-placeholder-text-in-textinput)                                                   |
| 9.    | [How do you disable a TextInput?](#question-9-how-do-you-disable-a-textinput)                                                                                 |
| 10.   | [How do you handle auto-capitalization in TextInput?](#question-10-how-do-you-handle-auto-capitalization-in-textinput)                                        |
| 11.   | [How do you mask text input for passwords or sensitive fields?](#question-11-how-do-you-mask-text-input-for-passwords-or-sensitive-fields)                    |
| 12.   | [How do you limit the max length of input in TextInput?](#question-12-how-do-you-limit-the-max-length-of-input-in-textinput)                                  |
| 13.   | [How do you apply margin and padding using StyleSheet?](#question-13-how-do-you-apply-margin-and-padding-using-stylesheet)                                    |
| 14.   | [How do you apply border radius to buttons and views?](#question-14-how-do-you-apply-border-radius-to-buttons-and-views)                                      |
| 15.   | [How do you change font size based on screen size?](#question-15-how-do-you-change-font-size-based-on-screen-size)                                            |
| 16.   | [How do you use default fonts in React Native?](#question-16-how-do-you-use-default-fonts-in-react-native)                                                    |
| 17.   | [How do you align text to center, left, or right?](#question-17-how-do-you-align-text-to-center-left-or-right)                                                |
| 18.   | [How do you use lineHeight in Text components?](#question-18-how-do-you-use-lineheight-in-text-components)                                                    |
| 19.   | [How do you show multiple Text components in a row?](#question-19-how-do-you-show-multiple-text-components-in-a-row)                                          |
| 20.   | [How do you apply shadows on Android vs iOS?](#question-20-how-do-you-apply-shadows-on-android-vs-ios)                                                        |

## Question 1. What is the difference between **Expo managed workflow** and **bare workflow**?

# Short answer

**Expo Managed Workflow** abstracts native iOS/Android configuration and lets you build apps mostly with JavaScript/TypeScript using Expo's SDK. It provides faster development, OTA updates, and simplified builds, but has some native customization limits.

**Bare Workflow** (or a standard React Native CLI project) gives you full access to the native Android and iOS projects, allowing any native SDK or custom native code at the cost of more setup, maintenance, and platform-specific knowledge.

For most applications, **start with Expo Managed** unless you have native requirements that Expo cannot support.

---

# Explanation

## Expo Managed Workflow

In the managed workflow, Expo manages the native project for you.

**Characteristics**

- No need to maintain `android/` and `ios/` folders (unless prebuilding).
- Uses the Expo SDK for camera, location, notifications, sensors, authentication, etc.
- Faster onboarding and development.
- Built-in OTA (Over-the-Air) updates using Expo Updates.
- Cloud builds using EAS Build.
- Works well for most production applications.

### Advantages

- Very quick project setup
- Minimal native configuration
- Easier upgrades
- Excellent developer experience
- Great for MVPs and startups
- Cross-platform consistency

### Limitations

- Cannot freely edit native code unless you prebuild/eject.
- Some third-party native SDKs may require config plugins or a bare workflow.
- Highly customized native integrations can be more challenging.

---

## Bare Workflow

The bare workflow is essentially a standard React Native application.

You own the native projects.

```
android/
ios/
```

You can:

- Write Java/Kotlin
- Write Objective-C/Swift
- Add any native SDK
- Create TurboModules
- Build Fabric components
- Modify Gradle/Xcode settings

### Advantages

- Complete flexibility
- Any native library can be integrated
- Better for enterprise apps with heavy native requirements
- Easier integration of proprietary SDKs

### Disadvantages

- More maintenance
- Native build issues
- More complicated upgrades
- Requires Android/iOS knowledge

---

## Comparison

| Feature               | Expo Managed                   | Bare Workflow                                           |
| --------------------- | ------------------------------ | ------------------------------------------------------- |
| Native folders        | Hidden/managed                 | Fully available                                         |
| Native code           | Limited (without prebuild)     | Full access                                             |
| Setup                 | Very easy                      | More involved                                           |
| OTA Updates           | Built-in via Expo Updates      | Requires setup (e.g., Expo Updates or another solution) |
| EAS Build             | Excellent support              | Supported                                               |
| Custom Native Modules | Limited without prebuild       | Fully supported                                         |
| Upgrade complexity    | Easier                         | Harder                                                  |
| Development speed     | Faster                         | Slower initially                                        |
| Best for              | Most apps, MVPs, business apps | Enterprise, custom native SDKs, advanced integrations   |

---

## When to choose Expo Managed

Choose Expo Managed when:

- Building CRUD/business apps
- Social apps
- E-commerce apps
- Education apps
- Internal enterprise apps
- Startup MVPs
- Most production apps
- You don't need custom native SDKs

Example:

```
Food Delivery App
├── Authentication
├── Maps
├── Push Notifications
├── Camera
├── Payments
└── OTA Updates

→ Expo Managed is an excellent fit.
```

---

## When to choose Bare Workflow

Choose Bare when you need:

- Custom native modules
- Proprietary company SDKs
- Specialized Bluetooth libraries
- Advanced background services
- Custom camera pipelines
- Custom Fabric components
- TurboModules
- Native rendering engines
- Deep Android/iOS customization

Example:

```
Medical Device App
├── BLE Communication
├── Native C++ Library
├── Custom Hardware SDK
├── Background Services
└── Proprietary Authentication SDK

→ Bare Workflow is the better choice.
```

---

# Example

### Create an Expo (Managed) app

```bash
npx create-expo-app MyApp --template expo-template-blank-typescript

cd MyApp

npx expo start
```

Example component (`App.tsx`):

```tsx
import { Button, View } from "react-native";
import * as Haptics from "expo-haptics";

export default function App() {
  return (
    <View style={{ flex: 1, justifyContent: "center", alignItems: "center" }}>
      <Button title="Vibrate" onPress={() => Haptics.impactAsync()} />
    </View>
  );
}
```

Run:

```bash
npx expo start
```

If you later need native customization while staying in the Expo ecosystem:

```bash
npx expo prebuild
```

This generates the `ios/` and `android/` projects, allowing native modifications while still using Expo tooling.

---

# Tooling & Setup

- **Preferred stack:** **Expo** for most new React Native projects because it offers a streamlined developer experience, integrated APIs, EAS Build, and OTA updates. Use **React Native CLI** when you know you'll need unrestricted native customization from the outset.
- **Avoid Create React App (CRA):** It is for web and deprecated for new projects; it is not appropriate for React Native.
- **Bundler:** React Native uses **Metro**. Expo builds on Metro with sensible defaults.
- **Module system:** Modern React Native projects use **ES Modules** (`import`/`export`). CommonJS (`require`) is still supported where necessary, especially in some configuration files.

---

# Performance

Expo itself does not significantly impact runtime performance; the same React Native optimization practices apply, especially with the New Architecture (Fabric and TurboModules).

- Enable the **New Architecture** when supported by your dependencies to reduce bridge overhead and improve rendering performance.
- Use `React.memo`, `useMemo`, and `useCallback` to avoid unnecessary re-renders.
- Profile with **React DevTools Profiler**, **Flipper**, and the React Native **Perf Monitor** to identify rendering bottlenecks.
- Optimize images with appropriate sizes and caching strategies (e.g., Expo Image or optimized native image components).
- Keep expensive work off the JavaScript thread and prefer TurboModules/JSI-based libraries over legacy bridge-based modules when available.

---

# Testing

Use a layered testing strategy:

- **Unit/Integration:** Jest + React Native Testing Library.
- **E2E:** Detox or Maestro.

Example:

```bash
npm test
```

Example test:

```tsx
import { render } from "@testing-library/react-native";
import App from "../App";

test("renders app", () => {
  expect(render(<App />)).toBeTruthy();
});
```

---

# Ops & Deployment

- Use **EAS Build** for cloud Android/iOS builds and **EAS Submit** for store submissions.
- Use **Expo Updates** for OTA updates (or configure Expo Updates in a bare app if desired).
- Integrate logging and crash reporting with services such as Sentry or Firebase Crashlytics.
- Handle offline scenarios with local persistence (e.g., AsyncStorage or SQLite) and request retries.
- Automate builds and tests using GitHub Actions, Bitrise, or EAS Workflows before deploying to the App Store and Google Play.

---

# Pitfalls

- Don't choose the bare workflow unless you truly need unrestricted native customization—it increases maintenance.
- Before adding a native dependency in Expo, verify it supports Expo or has a compatible config plugin.
- Keep Expo SDK and React Native versions aligned during upgrades to avoid compatibility issues.

## Question 2. How do you eject from an Expo project?

## Question 3. How do you run a React Native app on a **physical device**?

## Question 4. How do you use **React Native CLI vs Expo CLI** for building apps?

## Question 5. What are **controlled components** in React Native forms?

## Question 6. What are **uncontrolled components** in React Native forms?

## Question 7. How do you handle **keyboard dismiss on tapping outside**?

## Question 8. How do you **show placeholder text in TextInput**?

## Question 9. How do you **disable a TextInput**?

## Question 10. How do you handle **auto-capitalization** in TextInput?

## Question 11. How do you **mask text input** for passwords or sensitive fields?

## Question 12. How do you **limit the max length of input** in TextInput?

## Question 13. How do you **apply margin and padding** using StyleSheet?

## Question 14. How do you **apply border radius to buttons and views**?

## Question 15. How do you **change font size based on screen size**?

## Question 16. How do you **use default fonts** in React Native?

## Question 17. How do you **align text to center, left, or right**?

## Question 18. How do you **use lineHeight** in Text components?

## Question 19. How do you **show multiple Text components in a row**?

## Question 20. How do you **apply shadows on Android vs iOS**?
