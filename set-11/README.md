# Set 11

| S.No. | Question                                                                                                                                            |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is Hot Reloading vs Fast Refresh in React Native?](#question-1-what-is-hot-reloading-vs-fast-refresh-in-react-native)                         |
| 2.    | [How do you create React Native components using ES6 classes?](#question-2-how-do-you-create-react-native-components-using-es6-classes)             |
| 3.    | [How do you use React Native CLI to run apps on emulator or device?](#question-3-how-do-you-use-react-native-cli-to-run-apps-on-emulator-or-device) |
| 4.    | [How do you install and link third-party libraries?](#question-4-how-do-you-install-and-link-third-party-libraries)                                 |
| 5.    | [How do you use npm vs yarn in React Native projects?](#question-5-how-do-you-use-npm-vs-yarn-in-react-native-projects)                             |
| 6.    | [How do you update React Native to a newer version safely?](#question-6-how-do-you-update-react-native-to-a-newer-version-safely)                   |
| 7.    | [How do you display lists with multiple columns?](#question-7-how-do-you-display-lists-with-multiple-columns)                                       |
| 8.    | [How do you implement nested components?](#question-8-how-do-you-implement-nested-components)                                                       |
| 9.    | [How do you conditionally hide/show components?](#question-9-how-do-you-conditionally-hideshow-components)                                          |
| 10.   | [How do you handle default values for props?](#question-10-how-do-you-handle-default-values-for-props)                                              |
| 11.   | [How do you handle text truncation (ellipsis) in Text components?](#question-11-how-do-you-handle-text-truncation-ellipsis-in-text-components)      |
| 12.   | [How do you handle line breaks in Text components?](#question-12-how-do-you-handle-line-breaks-in-text-components)                                  |
| 13.   | [How do you disable a button conditionally?](#question-13-how-do-you-disable-a-button-conditionally)                                                |
| 14.   | [How do you use Platform-specific styles?](#question-14-how-do-you-use-platform-specific-styles)                                                    |
| 15.   | [How do you use React Native Vector Icons in buttons?](#question-15-how-do-you-use-react-native-vector-icons-in-buttons)                            |
| 16.   | [How do you use StyleSheet.flatten?](#question-16-how-do-you-use-stylesheetflatten)                                                                 |
| 17.   | [How do you set minimum and maximum width/height?](#question-17-how-do-you-set-minimum-and-maximum-widthheight)                                     |
| 18.   | [How do you detect user touches outside of a component?](#question-18-how-do-you-detect-user-touches-outside-of-a-component)                        |
| 19.   | [How do you implement simple counters using hooks?](#question-19-how-do-you-implement-simple-counters-using-hooks)                                  |
| 20.   | [How do you handle user text input and validation?](#question-20-how-do-you-handle-user-text-input-and-validation)                                  |

## Question 1. What is **Hot Reloading** vs **Fast Refresh** in React Native?

# Short answer

**Hot Reloading** is the older React Native feature that attempts to inject updated code into a running app while preserving state, but it could leave the app in an inconsistent state.

**Fast Refresh** is the modern replacement. It intelligently refreshes only the changed modules, preserves component state whenever safe, automatically falls back to a full reload when necessary, and provides a much more reliable development experience. **Fast Refresh is the recommended and default approach in modern React Native.**

---

# Explanation

## Hot Reloading (Legacy)

Hot Reloading updates JavaScript modules without restarting the app.

### Advantages

- Faster than rebuilding the application
- Attempts to preserve component state
- Good for small UI changes

### Limitations

- Could leave the application in an inconsistent state
- Changes to hooks or component exports sometimes weren't reflected correctly
- Native module changes still required rebuilding
- Frequently required manual reloads

Example issues:

- Updated `useEffect` not running correctly
- Navigation state becoming inconsistent
- Styles updating but logic remaining stale

Because of these issues, Hot Reloading has largely been replaced.

---

## Fast Refresh (Modern)

Fast Refresh is built on React's refresh runtime and is now the default in React Native.

It intelligently determines what changed.

### If only a component changed

Only that component is refreshed.

```tsx
const Counter = () => {
  const [count, setCount] = useState(0);

  return <Button title={`${count}`} onPress={() => setCount(count + 1)} />;
};
```

Changing the button color or text usually preserves `count`.

---

### If hooks changed safely

Fast Refresh re-runs the component while trying to preserve local state.

---

### If module boundaries changed

For example:

```tsx
export const API_URL = "...";
```

becomes

```tsx
export default API_URL;
```

Fast Refresh performs a larger reload.

---

### If the change affects application startup

Examples:

- Navigation root
- Redux store creation
- Context providers
- App registry
- Metro configuration

Fast Refresh performs a full reload because preserving state would be unsafe.

---

## State Preservation Rules

Fast Refresh attempts to preserve:

- `useState`
- `useReducer`
- `useRef`

It may reset state when:

- Component signature changes
- Hook order changes
- Export structure changes
- Non-component modules are modified

Example:

```tsx
// Before

const Example = () => {
  const [name] = useState("");
};
```

Changing to:

```tsx
const Example = () => {
  const [name] = useState("");
  const [age] = useState(20);
};
```

can cause state to reset because the hook signature changed.

---

## Rendering Behavior

Fast Refresh works at the JavaScript layer.

With the **New Architecture (Fabric)**:

- Fabric still uses Fast Refresh during development.
- Only affected React components are re-rendered when possible.
- Native UI remains mounted unless a full reload is required.
- TurboModules are **not** reloaded dynamically; changes to native code still require rebuilding the app.

---

# Example

### Create a project (Expo + TypeScript)

```bash
npx create-expo-app MyApp --template expo-template-blank-typescript

cd MyApp

npm start
```

Run on Android:

```bash
npm run android
```

Run on iOS:

```bash
npm run ios
```

`App.tsx`

```tsx
import { useState } from "react";
import { Button, SafeAreaView, Text } from "react-native";

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <SafeAreaView style={{ marginTop: 80, alignItems: "center" }}>
      <Text style={{ fontSize: 28 }}>Count: {count}</Text>

      <Button title="Increment" onPress={() => setCount((c) => c + 1)} />
    </SafeAreaView>
  );
}
```

Now edit:

```tsx
fontSize: 28;
```

to

```tsx
fontSize: 36;
```

The text size updates immediately, and the current counter value is preserved by Fast Refresh.

---

# Tooling & Setup

**Recommended stack**

- **Expo** for rapid development and OTA updates.
- **React Native CLI** for projects requiring custom native modules or extensive platform-specific code.

Avoid **Create React App (CRA)**, as it is intended for web applications, is deprecated, and is not suitable for React Native.

**Metro Bundler**

- React Native uses **Metro** to bundle JavaScript.
- Metro watches files and triggers Fast Refresh automatically when changes are saved.

**ES Modules vs CommonJS**

- Modern React Native projects use **ES Modules** (`import`/`export`).
- Metro transpiles modules for the target JavaScript engine (such as Hermes).
- CommonJS (`require`) is still supported for compatibility but should generally be avoided in new code unless required by a dependency.

---

# Performance

Fast Refresh is a **development-only** feature and has no impact on production performance.

For production optimization:

- Use `React.memo` for expensive components.
- Use `useMemo` for costly calculations.
- Use `useCallback` for stable callback references passed to memoized children.
- Optimize images with appropriately sized assets and caching.
- Prefer the New Architecture (Fabric and TurboModules) to reduce bridge overhead for native interactions.

### Profiling

- React DevTools Profiler for render analysis.
- **Flipper** (where supported) for inspecting React Native apps, network traffic, and performance.
- React Native Performance Monitor to observe FPS and memory usage during development.

---

# Testing

Fast Refresh is a developer convenience and should not replace testing.

**Unit & Integration**

- Jest
- React Native Testing Library

Example:

```bash
npm test
```

Example assertion:

```tsx
expect(getByText("Increment")).toBeTruthy();
```

**End-to-End**

- Detox for native E2E testing.
- Maestro for declarative cross-platform UI flows.

Example:

```bash
detox test
```

---

# Ops & Deployment

- Use structured logging instead of relying solely on `console.log`.
- Capture production crashes with services such as Sentry.
- Implement offline support with persistent storage (e.g., AsyncStorage) and request retry strategies.
- Use **Expo Application Services (EAS)**, Bitrise, or GitHub Actions for CI/CD.
- Distribute through the Apple App Store and Google Play.
- For Expo projects, use OTA updates where appropriate; for bare React Native apps, services like CodePush (where supported and compliant with store policies) can provide JavaScript bundle updates.

---

# Pitfalls

- **Do not assume Fast Refresh updates native code.** Changes to native modules, Gradle files, Info.plist, AndroidManifest.xml, or native dependencies require rebuilding the app.
- **Avoid changing hook order during development.** This often forces state to reset because React's hook rules are based on call order.
- **Don't rely on preserved state to validate initialization logic.** Periodically perform a full reload to ensure startup code behaves correctly.

## Question 2. How do you create **React Native components using ES6 classes**?

## Question 3. How do you use **React Native CLI to run apps on emulator or device**?

## Question 4. How do you **install and link third-party libraries**?

## Question 5. How do you use **npm vs yarn** in React Native projects?

## Question 6. How do you **update React Native to a newer version** safely?

## Question 7. How do you display **lists with multiple columns**?

## Question 8. How do you implement **nested components**?

## Question 9. How do you **conditionally hide/show components**?

## Question 10. How do you handle **default values for props**?

## Question 11. How do you handle **text truncation (ellipsis) in Text components**?

## Question 12. How do you handle **line breaks in Text** components?

## Question 13. How do you **disable a button** conditionally?

## Question 14. How do you **use Platform-specific styles**?

## Question 15. How do you **use React Native Vector Icons** in buttons?

## Question 16. How do you **use StyleSheet.flatten**?

## Question 17. How do you **set minimum and maximum width/height**?

## Question 18. How do you **detect user touches outside of a component**?

## Question 19. How do you **implement simple counters using hooks**?

## Question 20. How do you **handle user text input and validation**?
