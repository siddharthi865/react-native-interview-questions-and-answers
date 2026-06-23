# Set 22

| S.No. | Question                                                                                                                                                  |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you conditionally render screens based on user authentication?](#question-1-how-do-you-conditionally-render-screens-based-on-user-authentication) |
| 2.    | [How do you use React Native Picker/Dropdown?](#question-2-how-do-you-use-react-native-pickerdropdown)                                                    |
| 3.    | [How do you implement simple color theming?](#question-3-how-do-you-implement-simple-color-theming)                                                       |
| 4.    | [How do you apply platform-specific styles using Platform.select?](#question-4-how-do-you-apply-platform-specific-styles-using-platformselect)            |
| 5.    | [How do you disable gestures for certain screens in navigation?](#question-5-how-do-you-disable-gestures-for-certain-screens-in-navigation)               |
| 6.    | [How do you set minimum and maximum font scaling?](#question-6-how-do-you-set-minimum-and-maximum-font-scaling)                                           |
| 7.    | [How do you apply shadows on iOS?](#question-7-how-do-you-apply-shadows-on-ios)                                                                           |
| 8.    | [How do you apply shadows on Android?](#question-8-how-do-you-apply-shadows-on-android)                                                                   |
| 9.    | [How do you use ImageBackground component?](#question-9-how-do-you-use-imagebackground-component)                                                         |
| 10.   | [How do you use ActivityIndicator with conditional loading states?](#question-10-how-do-you-use-activityindicator-with-conditional-loading-states)        |
| 11.   | [How do you implement dynamic lists with varying item heights?](#question-11-how-do-you-implement-dynamic-lists-with-varying-item-heights)                |
| 12.   | [How do you implement a grid layout with FlatList?](#question-12-how-do-you-implement-a-grid-layout-with-flatlist)                                        |
| 13.   | [How do you implement infinite scroll with SectionList?](#question-13-how-do-you-implement-infinite-scroll-with-sectionlist)                              |
| 14.   | [How do you memoize FlatList items for performance?](#question-14-how-do-you-memoize-flatlist-items-for-performance)                                      |
| 15.   | [How do you handle multi-step wizards/forms with dynamic validations?](#question-15-how-do-you-handle-multi-step-wizardsforms-with-dynamic-validations)   |
| 16.   | [How do you create reusable input components with validation?](#question-16-how-do-you-create-reusable-input-components-with-validation)                  |
| 17.   | [How do you handle keyboard avoiding views on iOS and Android?](#question-17-how-do-you-handle-keyboard-avoiding-views-on-ios-and-android)                |
| 18.   | [How do you create a modal stack for multiple modals?](#question-18-how-do-you-create-a-modal-stack-for-multiple-modals)                                  |
| 19.   | [How do you animate modal entrance and exit?](#question-19-how-do-you-animate-modal-entrance-and-exit)                                                    |
| 20.   | [How do you use react-native-gesture-handler for complex gestures?](#question-20-how-do-you-use-react-native-gesture-handler-for-complex-gestures)        |

## Question 1. How do you **conditionally render screens based on user authentication**?

# Short answer

Conditionally rendering screens based on authentication is typically done by **maintaining an authentication state** (e.g., `isAuthenticated`) and **rendering different navigation stacks** depending on that state. In modern React Native, this is commonly implemented with **React Navigation**, **Context API/Zustand/Redux**, secure token storage, and restoring the user's session during app startup.

---

# Explanation

In production React Native apps, authentication is usually handled at the **navigation layer**, not inside individual screens.

A common architecture is:

```
App
 ├── AuthProvider
 │     ├── auth state
 │     ├── login()
 │     ├── logout()
 │     └── restoreSession()
 │
 └── NavigationContainer
        ├── Auth Stack
        │      Login
        │      Register
        │
        └── App Stack
               Home
               Profile
               Settings
```

Instead of checking authentication inside every screen:

```tsx
if (!user) {
  return <Login />;
}
```

you conditionally render **entire navigation stacks**.

```tsx
return user ? <AppStack /> : <AuthStack />;
```

This approach:

- avoids unauthorized navigation
- simplifies routing
- improves maintainability
- prevents users from accessing protected screens

---

### Authentication Flow

1. App launches.
2. Read JWT/token from secure storage.
3. Validate or refresh token.
4. Update authentication state.
5. Render appropriate navigator.

```
Loading
    │
    ▼
Restore Token
    │
 ┌──┴──────────┐
 │             │
Valid       Invalid
 │             │
 ▼             ▼
AppStack   AuthStack
```

---

### State Management Choices

Small apps:

- React Context
- useReducer

Medium apps:

- Zustand

Large apps:

- Redux Toolkit

Server state:

- TanStack Query for user/session fetching

---

### Secure Token Storage

Never store tokens in:

- AsyncStorage (for sensitive secrets)

Prefer:

- Expo: `expo-secure-store`
- React Native CLI: `react-native-keychain`

Store:

- access token
- refresh token
- expiry

---

### Rendering Behavior

When auth state changes:

```
login()
   ↓
setUser(user)
   ↓
Navigation re-renders
   ↓
AuthStack removed
   ↓
AppStack mounted
```

This avoids manually navigating to every screen.

---

### New Architecture Considerations

With the New Architecture:

- **Fabric** speeds UI rendering.
- **TurboModules** reduce native bridge overhead.
- Authentication itself isn't bridge-heavy, but secure storage and biometric APIs benefit from faster native module access.
- Keep auth state updates lightweight to avoid unnecessary re-renders of the navigation tree.

---

# Example

**Scaffold (Expo + TypeScript):**

```bash
npx create-expo-app AuthDemo --template expo-template-blank-typescript

cd AuthDemo

npx expo install @react-navigation/native
npx expo install react-native-screens react-native-safe-area-context
npm install @react-navigation/native-stack
npx expo start
```

**App.tsx**

```tsx
import React, { createContext, useContext, useState } from "react";
import { Button, Text, View } from "react-native";
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigator } from "@react-navigation/native-stack";

type AuthContextType = {
  isAuthenticated: boolean;
  login: () => void;
  logout: () => void;
};

const AuthContext = createContext<AuthContextType>(null!);

const Stack = createNativeStackNavigator();

function LoginScreen() {
  const { login } = useContext(AuthContext);

  return (
    <View style={{ flex: 1, justifyContent: "center", alignItems: "center" }}>
      <Button title="Login" onPress={login} />
    </View>
  );
}

function HomeScreen() {
  const { logout } = useContext(AuthContext);

  return (
    <View style={{ flex: 1, justifyContent: "center", alignItems: "center" }}>
      <Text>Welcome!</Text>
      <Button title="Logout" onPress={logout} />
    </View>
  );
}

function AuthNavigator() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Login" component={LoginScreen} />
    </Stack.Navigator>
  );
}

function AppNavigator() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Home" component={HomeScreen} />
    </Stack.Navigator>
  );
}

export default function App() {
  const [isAuthenticated, setAuthenticated] = useState(false);

  return (
    <AuthContext.Provider
      value={{
        isAuthenticated,
        login: () => setAuthenticated(true),
        logout: () => setAuthenticated(false),
      }}
    >
      <NavigationContainer>
        {isAuthenticated ? <AppNavigator /> : <AuthNavigator />}
      </NavigationContainer>
    </AuthContext.Provider>
  );
}
```

Run:

```bash
npx expo start
```

This demonstrates switching between authenticated and unauthenticated navigation stacks based on application state.

---

# Tooling & Setup

- **Recommended stack:** Expo + TypeScript for rapid development, secure storage integration, and OTA updates. Use React Native CLI if extensive native customization is required.
- **Navigation:** `@react-navigation/native` with the native stack navigator.
- **Secure storage:** `expo-secure-store` (Expo) or `react-native-keychain` (CLI) for tokens.
- **Bundler:** Metro (default for React Native and Expo).
- **Modules:** Prefer ESM (`import`/`export`) in modern React Native projects; CommonJS is mainly used for some tooling or legacy packages.
- Avoid **Create React App (CRA)**, as it is deprecated and intended for web, not React Native.

---

# Performance

- Render **separate navigators** (`AuthStack` vs. `AppStack`) instead of guarding every screen.
- Memoize context values with `useMemo` to avoid unnecessary re-renders of consumers.
- Use `React.memo` for frequently rendered components that receive stable props.
- Memoize callbacks (`useCallback`) passed deep into the component tree.
- Profile navigation transitions with React DevTools Profiler, React Native Perf Monitor, and Flipper.
- Optimize images with appropriate formats and caching libraries when displaying user avatars or profile assets.
- Under the New Architecture, TurboModules improve performance of native authentication helpers (e.g., secure storage, biometrics).

---

# Testing

**Unit/Integration**

Use **Jest** with **React Native Testing Library** to verify that the correct navigator or screen renders for authenticated and unauthenticated states.

```bash
npm test
```

Example assertion:

```tsx
expect(screen.getByText("Login")).toBeTruthy();
```

**End-to-End**

Use **Detox** or **Maestro** to automate flows such as:

- Login
- Session restoration
- Logout
- Access protection for authenticated screens

---

# Ops & Deployment

- Log authentication failures using services like Sentry or Crashlytics, but never log tokens or sensitive credentials.
- Handle token expiration by refreshing tokens and redirecting to the authentication flow on failure.
- Support offline behavior by securely caching minimal session metadata and validating tokens when connectivity returns.
- Use GitHub Actions, EAS Build, or Bitrise to automate testing and builds.
- For deployments, leverage Expo OTA updates (where compatible) or native app store releases. Be mindful that changes involving native authentication modules require a new binary release.

---

# Pitfalls

- **Do not store JWTs or refresh tokens in plain AsyncStorage** if they contain sensitive information; use secure storage.
- **Avoid navigating manually after every login/logout**; switch entire navigation stacks based on auth state.
- **Handle the startup loading state** while restoring the session to prevent a brief flash of the login screen before authenticated content appears.

## Question 2. How do you **use React Native Picker/Dropdown**?

## Question 3. How do you **implement simple color theming**?

## Question 4. How do you **apply platform-specific styles using Platform.select**?

## Question 5. How do you **disable gestures for certain screens** in navigation?

## Question 6. How do you **set minimum and maximum font scaling**?

## Question 7. How do you **apply shadows on iOS**?

## Question 8. How do you **apply shadows on Android**?

## Question 9. How do you **use ImageBackground component**?

## Question 10. How do you **use ActivityIndicator with conditional loading states**?

## Question 11. How do you **implement dynamic lists with varying item heights**?

## Question 12. How do you **implement a grid layout with FlatList**?

## Question 13. How do you **implement infinite scroll with SectionList**?

## Question 14. How do you **memoize FlatList items for performance**?

## Question 15. How do you **handle multi-step wizards/forms** with dynamic validations?

## Question 16. How do you **create reusable input components with validation**?

## Question 17. How do you **handle keyboard avoiding views on iOS and Android**?

## Question 18. How do you **create a modal stack for multiple modals**?

## Question 19. How do you **animate modal entrance and exit**?

## Question 20. How do you **use react-native-gesture-handler for complex gestures**?
