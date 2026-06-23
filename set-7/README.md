# Set 7

| S.No. | Question                                                                                                                                                                                 |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you use local vs remote images?](#question-1-how-do-you-use-local-vs-remote-images)                                                                                              |
| 2.    | [What is the difference between TouchableWithoutFeedback and Pressable?](#question-2-what-is-the-difference-between-touchablewithoutfeedback-and-pressable)                              |
| 3.    | [How do you implement a simple counter app in React Native?](#question-3-how-do-you-implement-a-simple-counter-app-in-react-native)                                                      |
| 4.    | [How do you debug console.log in React Native?](#question-4-how-do-you-debug-consolelog-in-react-native)                                                                                 |
| 5.    | [How do you handle keyboard avoiding for input fields?](#question-5-how-do-you-handle-keyboard-avoiding-for-input-fields)                                                                |
| 6.    | [How do you install and use vector icons?](#question-6-how-do-you-install-and-use-vector-icons)                                                                                          |
| 7.    | [How do you link assets in React Native (fonts, images)?](#question-7-how-do-you-link-assets-in-react-native-fonts-images)                                                               |
| 8.    | [How do you restart an app programmatically?](#question-8-how-do-you-restart-an-app-programmatically)                                                                                    |
| 9.    | [How do you use platform-specific code (Platform.OS)?](#question-9-how-do-you-use-platform-specific-code-platformos)                                                                     |
| 10.   | [How do you check the platform version (Android/iOS)?](#question-10-how-do-you-check-the-platform-version-androidios)                                                                    |
| 11.   | [How do you implement custom fonts in React Native?](#question-11-how-do-you-implement-custom-fonts-in-react-native)                                                                     |
| 12.   | [How do you handle form validation?](#question-12-how-do-you-handle-form-validation)                                                                                                     |
| 13.   | [How do you implement multi-step forms?](#question-13-how-do-you-implement-multi-step-forms)                                                                                             |
| 14.   | [How do you handle text input focus and blur events?](#question-14-how-do-you-handle-text-input-focus-and-blur-events)                                                                   |
| 15.   | [What is the difference between KeyboardAvoidingView and manual handling of keyboard?](#question-15-what-is-the-difference-between-keyboardavoidingview-and-manual-handling-of-keyboard) |
| 16.   | [How do you implement dynamic styling based on screen size?](#question-16-how-do-you-implement-dynamic-styling-based-on-screen-size)                                                     |
| 17.   | [How do you handle orientation-specific layouts?](#question-17-how-do-you-handle-orientation-specific-layouts)                                                                           |
| 18.   | [How do you handle image caching?](#question-18-how-do-you-handle-image-caching)                                                                                                         |
| 19.   | [How do you implement image placeholders and lazy loading?](#question-19-how-do-you-implement-image-placeholders-and-lazy-loading)                                                       |
| 20.   | [How do you implement pull-to-refresh with FlatList?](#question-20-how-do-you-implement-pull-to-refresh-with-flatlist)                                                                   |

## Question 1. How do you use **local vs remote images**?

# Short answer

In React Native, **local images** are bundled with the app and loaded using `require()`, while **remote images** are fetched from a URL using the `uri` property. Local images are faster and work offline, whereas remote images allow dynamic content but require caching, placeholders, and error handling for the best user experience.

---

# Explanation

React Native's `Image` component supports two primary image sources:

### 1. Local Images

These images are included in your app bundle during build time.

```tsx
<Image source={require("./assets/logo.png")} />
```

**Advantages**

- Fast loading (bundled with the app)
- Available offline
- Automatically includes image dimensions
- No network dependency

**Disadvantages**

- Increases app bundle size
- Cannot dynamically generate the path
- Requires app update when image changes

Example of **invalid** dynamic require:

```tsx
// ❌ Not supported
<Image source={require(`./assets/${name}.png`)} />
```

Instead:

```tsx
const images = {
  logo: require("./assets/logo.png"),
  avatar: require("./assets/avatar.png"),
};

<Image source={images.logo} />;
```

---

### 2. Remote Images

Images are downloaded from a server.

```tsx
<Image
  source={{
    uri: "https://example.com/profile.jpg",
  }}
/>
```

Useful for:

- User profile pictures
- Product images
- CMS content
- Social media feeds

Since they come over the network, consider:

- Loading indicators
- Error fallback
- Caching
- Retry logic
- Progressive loading

Example:

```tsx
<Image source={{ uri: imageUrl }} resizeMode="cover" />
```

---

## Rendering behavior

Local images:

- Resolved immediately by Metro.
- No network request.
- Minimal rendering overhead.

Remote images:

- Require asynchronous download.
- Trigger loading states.
- May re-render when download completes.
- Large images increase memory usage.

In the New Architecture (Fabric), image rendering is more efficient, but downloading and decoding very large images still impacts memory and UI responsiveness.

---

## State management trade-offs

Remote images often require UI state:

```tsx
const [loading, setLoading] = useState(true);
const [error, setError] = useState(false);
```

Useful callbacks:

```tsx
onLoadStart;
onLoad;
onLoadEnd;
onError;
```

Example:

```tsx
<Image
  source={{ uri }}
  onLoadStart={() => setLoading(true)}
  onLoadEnd={() => setLoading(false)}
  onError={() => setError(true)}
/>
```

---

## When to use each

| Local Images         | Remote Images      |
| -------------------- | ------------------ |
| App logo             | User avatar        |
| Icons                | Product catalog    |
| Splash illustrations | News thumbnails    |
| Static backgrounds   | Social media posts |
| Offline assets       | CMS images         |

---

# Example

**Expo (TypeScript)**

Create a new project:

```bash
npx create-expo-app ImageDemo --template expo-template-blank-typescript
cd ImageDemo
npm start
```

`App.tsx`

```tsx
import React from "react";
import { View, Image, StyleSheet } from "react-native";

export default function App() {
  return (
    <View style={styles.container}>
      {/* Local image */}
      <Image source={require("./assets/logo.png")} style={styles.image} />

      {/* Remote image */}
      <Image
        source={{
          uri: "https://picsum.photos/300",
        }}
        style={styles.image}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "space-evenly",
    alignItems: "center",
  },
  image: {
    width: 200,
    height: 200,
    borderRadius: 10,
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

- Prefer **Expo** for rapid development or **React Native CLI** when you need custom native modules.
- Avoid **Create React App (CRA)**—it is deprecated and intended for web, not mobile.
- React Native uses **Metro** as its JavaScript bundler, optimized for mobile asset resolution and fast refresh.
- Modern React Native projects use **ES Modules (`import`/`export`)**. CommonJS (`require`) is still used specifically for static local image assets because Metro resolves them at build time.

---

# Performance

### Image caching

React Native's default caching behavior varies by platform.

For production apps, libraries like:

- `expo-image` (Expo)
- `react-native-fast-image` (bare React Native)

offer:

- Memory cache
- Disk cache
- Priority loading
- Better performance
- Placeholder support

### Resize images

Never download a 4000×4000 image to display it at 100×100.

Serve appropriately sized images from your backend.

### Memoize image-heavy lists

```tsx
const Avatar = React.memo(({ uri }: { uri: string }) => (
  <Image source={{ uri }} style={{ width: 60, height: 60 }} />
));
```

### Virtualized lists

Use:

- `FlatList`
- `FlashList` (for very large datasets)

instead of rendering hundreds of `Image` components inside a `ScrollView`.

### Profiling

Use:

- React DevTools Profiler
- Flipper (where supported)
- React Native Performance Monitor (`Show Perf Monitor`)
- Native profiling tools (Android Studio Profiler and Xcode Instruments)

Monitor:

- Memory usage
- Image decode time
- Re-renders
- Network requests

---

# Testing

**Unit / Integration**

Use **Jest** with **React Native Testing Library**:

```bash
npm test
```

Example assertion:

```tsx
expect(getByTestId("profile-image")).toBeTruthy();
```

Mock remote image requests when testing components to avoid network dependencies.

**E2E**

Use:

- Detox
- Maestro

Verify:

- Images appear
- Placeholder is shown while loading
- Error UI is displayed for invalid URLs

---

# Ops & Deployment

- Log image loading failures using services such as **Sentry** or **Firebase Crashlytics** for visibility into broken URLs or rendering issues.
- Display placeholders or fallback images when remote requests fail.
- Cache frequently used remote images for better offline support.
- In CI/CD (e.g., **Expo EAS**, **Bitrise**, or **GitHub Actions**), optimize image assets before builds to reduce app size.
- For deployment, bundle only essential local assets and serve frequently updated media remotely. If using Expo, OTA updates can change JavaScript and bundled assets included in the update, while larger media is often better delivered from a CDN.

---

# Pitfalls

- **Don't use dynamic `require()`**—Metro must statically analyze local asset paths.
- **Don't load oversized remote images**—serve images sized for the target display to reduce memory usage.
- **Always handle loading and error states** for remote images to avoid blank or broken UI.

## Question 2. What is the difference between **TouchableWithoutFeedback** and **Pressable**?

## Question 3. How do you implement a **simple counter app** in React Native?

## Question 4. How do you **debug console.log** in React Native?

## Question 5. How do you handle **keyboard avoiding** for input fields?

## Question 6. How do you **install and use vector icons**?

## Question 7. How do you **link assets** in React Native (fonts, images)?

## Question 8. How do you **restart an app programmatically**?

## Question 9. How do you **use platform-specific code** (`Platform.OS`)?

## Question 10. How do you **check the platform version** (Android/iOS)?

## Question 11. How do you implement **custom fonts** in React Native?

## Question 12. How do you handle **form validation**?

## Question 13. How do you implement **multi-step forms**?

## Question 14. How do you handle **text input focus** and **blur events**?

## Question 15. What is the difference between **KeyboardAvoidingView** and manual handling of keyboard?

## Question 16. How do you implement **dynamic styling** based on screen size?

## Question 17. How do you handle **orientation-specific layouts**?

## Question 18. How do you handle **image caching**?

## Question 19. How do you implement **image placeholders and lazy loading**?

## Question 20. How do you implement **pull-to-refresh with FlatList**?
