# Set 10

| S.No. | Question                                                                                                                                                               |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you implement fingerprint / face ID authentication?](#question-1-how-do-you-implement-fingerprint--face-id-authentication)                                     |
| 2.    | [How do you implement secure token storage using Keychain / Keystore?](#question-2-how-do-you-implement-secure-token-storage-using-keychain--keystore)                 |
| 3.    | [How do you implement end-to-end encryption for stored data?](#question-3-how-do-you-implement-end-to-end-encryption-for-stored-data)                                  |
| 4.    | [How do you implement hot updates with CodePush?](#question-4-how-do-you-implement-hot-updates-with-codepush)                                                          |
| 5.    | [How do you debug production crashes in React Native?](#question-5-how-do-you-debug-production-crashes-in-react-native)                                                |
| 6.    | [How do you integrate Crashlytics for crash reporting?](#question-6-how-do-you-integrate-crashlytics-for-crash-reporting)                                              |
| 7.    | [How do you measure FPS and memory usage in React Native?](#question-7-how-do-you-measure-fps-and-memory-usage-in-react-native)                                        |
| 8.    | [How do you optimize scrolling performance in large lists?](#question-8-how-do-you-optimize-scrolling-performance-in-large-lists)                                      |
| 9.    | [How do you reduce JS bundle size?](#question-9-how-do-you-reduce-js-bundle-size)                                                                                      |
| 10.   | [How do you implement multi-language support (i18n) with translations?](#question-10-how-do-you-implement-multi-language-support-i18n-with-translations)               |
| 11.   | [How do you implement right-to-left (RTL) support?](#question-11-how-do-you-implement-right-to-left-rtl-support)                                                       |
| 12.   | [How do you implement offline-first architecture with caching strategies?](#question-12-how-do-you-implement-offline-first-architecture-with-caching-strategies)       |
| 13.   | [How do you implement real-time data updates using WebSockets?](#question-13-how-do-you-implement-real-time-data-updates-using-websockets)                             |
| 14.   | [How do you implement GraphQL in React Native?](#question-14-how-do-you-implement-graphql-in-react-native)                                                             |
| 15.   | [How do you handle complex state management with Redux Toolkit?](#question-15-how-do-you-handle-complex-state-management-with-redux-toolkit)                           |
| 16.   | [How do you implement memoization for heavy computations?](#question-16-how-do-you-implement-memoization-for-heavy-computations)                                       |
| 17.   | [How do you implement lazy-loading of components/screens?](#question-17-how-do-you-implement-lazy-loading-of-componentsscreens)                                        |
| 18.   | [How do you implement custom keyboard input accessory views?](#question-18-how-do-you-implement-custom-keyboard-input-accessory-views)                                 |
| 19.   | [How do you implement animations across screens (shared element transitions)?](#question-19-how-do-you-implement-animations-across-screens-shared-element-transitions) |
| 20.   | [How do you prepare a React Native app for enterprise deployment with MDM?](#question-20-how-do-you-prepare-a-react-native-app-for-enterprise-deployment-with-mdm)     |

## Question 1. How do you implement **fingerprint / face ID authentication**?

# Short answer

Fingerprint and Face ID authentication in React Native are typically implemented using platform biometrics APIs through libraries like **`react-native-biometrics`** or **`expo-local-authentication`** (Expo). The flow is:

1. Check if biometric hardware exists.
2. Check whether the user has enrolled fingerprints/Face ID.
3. Prompt biometric authentication.
4. On success, unlock the app or authorize a sensitive action.
5. Store tokens securely using Keychain/Keystore (never store credentials in AsyncStorage).

---

# Explanation

Biometric authentication is **not a replacement for backend authentication**. It is a secure way to:

- Unlock an already authenticated session
- Confirm sensitive actions
- Re-authenticate before accessing protected information

Typical production flow:

```text
User logs in with username/password
          │
          ▼
Backend issues JWT/Refresh Token
          │
          ▼
Store securely (Keychain/Keystore)
          │
          ▼
Next app launch
          │
          ▼
Prompt Face ID/Fingerprint
          │
          ▼
If success → Read secure token
          │
          ▼
Access application
```

## Common libraries

### Expo

```bash
npx create-expo-app MyApp
```

Install:

```bash
npx expo install expo-local-authentication
```

Good for managed Expo projects.

---

### React Native CLI

```bash
npx @react-native-community/cli init MyApp
```

Install:

```bash
npm install react-native-biometrics
```

Supports:

- Face ID
- Touch ID
- Fingerprint
- Android BiometricPrompt

---

## Authentication flow

### Step 1

Check hardware availability

```ts
const compatible = await LocalAuthentication.hasHardwareAsync();
```

---

### Step 2

Check enrollment

```ts
const enrolled = await LocalAuthentication.isEnrolledAsync();
```

If not enrolled:

- Ask user to enable biometrics in device settings.

---

### Step 3

Prompt biometric dialog

```ts
await LocalAuthentication.authenticateAsync();
```

The OS handles:

- Face ID animation
- Fingerprint prompt
- Retry
- Lockout
- Cancellation

Never build a custom biometric UI.

---

## Secure storage

Never do:

```ts
AsyncStorage.setItem("token", token);
```

Instead use:

- Expo SecureStore
- iOS Keychain
- Android Keystore
- `react-native-keychain`

---

## Backend integration

The backend should still validate tokens.

Biometrics only protects local access.

Typical flow:

```
Face ID
    ↓
Read Refresh Token
    ↓
Refresh JWT
    ↓
API Calls
```

---

## New Architecture considerations

With the React Native New Architecture:

- TurboModules reduce bridge overhead for native biometric APIs.
- Fabric is unrelated to authentication itself but improves overall UI responsiveness.
- Native biometric prompts are asynchronous, so bridge latency is rarely a bottleneck, but TurboModules can reduce overhead for repeated native interactions.

---

# Example

**Expo + TypeScript**

```tsx
import * as LocalAuthentication from "expo-local-authentication";
import React from "react";
import { Button, Alert, View } from "react-native";

export default function App() {
  const authenticate = async () => {
    const compatible = await LocalAuthentication.hasHardwareAsync();

    if (!compatible) {
      Alert.alert("Biometric hardware unavailable");
      return;
    }

    const enrolled = await LocalAuthentication.isEnrolledAsync();

    if (!enrolled) {
      Alert.alert("No biometrics enrolled");
      return;
    }

    const result = await LocalAuthentication.authenticateAsync({
      promptMessage: "Authenticate",
    });

    if (result.success) {
      Alert.alert("Authenticated");
    } else {
      Alert.alert("Authentication failed");
    }
  };

  return (
    <View
      style={{
        flex: 1,
        justifyContent: "center",
        padding: 20,
      }}
    >
      <Button title="Login with Face ID" onPress={authenticate} />
    </View>
  );
}
```

Run:

```bash
npx create-expo-app MyApp
cd MyApp

npx expo install expo-local-authentication

npx expo start
```

---

# Tooling & Setup

**Preferred stacks**

- **Expo**: `expo-local-authentication` for quick setup and OTA updates with Expo.
- **React Native CLI**: `react-native-biometrics` when you need deeper native integration or custom native modules.

**Metro bundler**

- React Native uses **Metro** to bundle JavaScript and assets.

**Module system**

- Modern React Native projects use **ES Modules (`import`/`export`)**. CommonJS (`require`) is still supported for compatibility but should generally be avoided in new code.

**Avoid CRA**

- Do **not** use Create React App (CRA). It is for web applications, is deprecated for modern workflows, and is not suitable for React Native.

**Platform configuration**

- iOS: Add the required `NSFaceIDUsageDescription` entry in `Info.plist` to explain why Face ID is needed.
- Android: Ensure the app targets appropriate Android API levels and uses the platform biometric APIs through the chosen library.

---

# Performance

Biometric authentication is generally lightweight because the heavy work happens in the operating system.

Optimization tips:

- Trigger biometric prompts only when required (app unlock or sensitive actions).
- Cache authentication state only for the duration allowed by your security policy.
- Use `React.memo`, `useCallback`, and `useMemo` to avoid unnecessary re-renders around authentication screens.
- Keep biometric prompts outside expensive render cycles.
- Store tokens in secure storage rather than repeatedly requesting them from a backend.
- Profile app performance with:
  - React DevTools Profiler
  - Flipper
  - React Native Performance Monitor

- Native biometric modules benefit from the New Architecture (TurboModules) by reducing JS↔native communication overhead.

---

# Testing

**Unit & integration**

Use Jest with React Native Testing Library:

```bash
npm test
```

Mock biometric APIs to simulate:

- Successful authentication
- Failed authentication
- User cancellation
- Hardware unavailable
- No enrolled biometrics

**End-to-end**

Use Detox or Maestro to validate authentication flows on real devices or supported simulators/emulators where biometric simulation is available.

Example:

```bash
detox test
```

---

# Ops & Deployment

- Log authentication outcomes without recording biometric data or sensitive tokens.
- Handle errors such as user cancellation, biometric lockout, or hardware unavailability gracefully.
- Store tokens securely in Keychain/Keystore (or Expo SecureStore), not AsyncStorage.
- Support offline use by allowing biometrics to unlock locally stored encrypted credentials when appropriate.
- CI/CD:
  - Expo: EAS Build and EAS Update.
  - React Native CLI: GitHub Actions or Bitrise for automated builds and testing.

- Before App Store and Play Store release, verify required permissions, privacy disclosures, and platform-specific biometric configuration.
- For OTA updates, ensure authentication logic remains compatible with the native binary version.

---

# Pitfalls

- **Never store authentication tokens in AsyncStorage**; use Keychain/Keystore or SecureStore.
- **Do not rely solely on biometrics**; always validate user sessions and authorization with your backend.
- **Always handle fallback cases**, including unsupported devices, unenrolled biometrics, user cancellation, and biometric lockout.

## Question 2. How do you implement **secure token storage** using Keychain / Keystore?

## Question 3. How do you implement **end-to-end encryption** for stored data?

## Question 4. How do you implement **hot updates** with CodePush?

## Question 5. How do you debug **production crashes** in React Native?

## Question 6. How do you integrate **Crashlytics** for crash reporting?

## Question 7. How do you measure **FPS and memory usage** in React Native?

## Question 8. How do you optimize **scrolling performance** in large lists?

## Question 9. How do you reduce **JS bundle size**?

## Question 10. How do you implement **multi-language support (i18n)** with translations?

## Question 11. How do you implement **right-to-left (RTL) support**?

## Question 12. How do you implement **offline-first architecture** with caching strategies?

## Question 13. How do you implement **real-time data updates** using WebSockets?

## Question 14. How do you implement **GraphQL in React Native**?

## Question 15. How do you handle **complex state management** with Redux Toolkit?

## Question 16. How do you implement **memoization** for heavy computations?

## Question 17. How do you implement **lazy-loading of components/screens**?

## Question 18. How do you implement **custom keyboard input accessory views**?

## Question 19. How do you implement **animations across screens** (shared element transitions)?

## Question 20. How do you prepare a React Native app for **enterprise deployment with MDM**?
