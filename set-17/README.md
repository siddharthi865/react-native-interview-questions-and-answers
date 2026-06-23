# Set 17

| S.No. | Question                                                                                                                                         |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How do you use flexbox for layout in React Native?](#question-1-how-do-you-use-flexbox-for-layout-in-react-native)                              |
| 2.    | [How do you stack components vertically and horizontally?](#question-2-how-do-you-stack-components-vertically-and-horizontally)                  |
| 3.    | [How do you wrap components in a ScrollView?](#question-3-how-do-you-wrap-components-in-a-scrollview)                                            |
| 4.    | [How do you disable scrolling in ScrollView?](#question-4-how-do-you-disable-scrolling-in-scrollview)                                            |
| 5.    | [How do you apply sticky headers in ScrollView?](#question-5-how-do-you-apply-sticky-headers-in-scrollview)                                      |
| 6.    | [How do you handle orientation changes in ScrollView?](#question-6-how-do-you-handle-orientation-changes-in-scrollview)                          |
| 7.    | [How do you measure component dimensions using onLayout?](#question-7-how-do-you-measure-component-dimensions-using-onlayout)                    |
| 8.    | [How do you conditionally render components based on state?](#question-8-how-do-you-conditionally-render-components-based-on-state)              |
| 9.    | [How do you use fragments to avoid extra View wrappers?](#question-9-how-do-you-use-fragments-to-avoid-extra-view-wrappers)                      |
| 10.   | [How do you set background color dynamically?](#question-10-how-do-you-set-background-color-dynamically)                                         |
| 11.   | [How do you handle multiple FlatLists on one screen?](#question-11-how-do-you-handle-multiple-flatlists-on-one-screen)                           |
| 12.   | [How do you implement animated headers on scroll?](#question-12-how-do-you-implement-animated-headers-on-scroll)                                 |
| 13.   | [How do you measure scroll position in FlatList?](#question-13-how-do-you-measure-scroll-position-in-flatlist)                                   |
| 14.   | [How do you programmatically scroll FlatList to a specific index?](#question-14-how-do-you-programmatically-scroll-flatlist-to-a-specific-index) |
| 15.   | [How do you use sticky headers in SectionList?](#question-15-how-do-you-use-sticky-headers-in-sectionlist)                                       |
| 16.   | [How do you optimize SectionList for large datasets?](#question-16-how-do-you-optimize-sectionlist-for-large-datasets)                           |
| 17.   | [How do you use FlatList item separators?](#question-17-how-do-you-use-flatlist-item-separators)                                                 |
| 18.   | [How do you handle item clicks in FlatList or SectionList?](#question-18-how-do-you-handle-item-clicks-in-flatlist-or-sectionlist)               |
| 19.   | [How do you implement item selection in lists?](#question-19-how-do-you-implement-item-selection-in-lists)                                       |
| 20.   | [How do you implement swipeable FlatList items?](#question-20-how-do-you-implement-swipeable-flatlist-items)                                     |

## Question 1. How do you **use flexbox for layout** in React Native?

# Short answer

React Native uses **Flexbox as its primary layout system**, but unlike CSS on the web, the default `flexDirection` is **`column`** (instead of `row`). You arrange UI using properties like `flex`, `flexDirection`, `justifyContent`, `alignItems`, `alignSelf`, `flexWrap`, and `gap` (supported in modern React Native versions).

---

# Explanation

Flexbox in React Native is powered by **Yoga**, Facebook's cross-platform layout engine. Yoga calculates layouts consistently across iOS and Android, making Flexbox the recommended way to build responsive mobile UIs.

## Core properties

### 1. `flex`

Determines how much available space a component occupies.

```tsx
<View style={{ flex: 1 }} />
```

```tsx
<View style={{ flex: 1 }}>
  <View style={{ flex: 1 }} />
  <View style={{ flex: 2 }} />
</View>
```

The second child receives twice as much space.

---

### 2. `flexDirection`

Controls the main axis.

```tsx
column; // default
row;
column - reverse;
row - reverse;
```

Example:

```tsx
<View style={{ flexDirection: 'row' }}>
```

Children appear horizontally.

---

### 3. `justifyContent`

Aligns items along the **main axis**.

```tsx
justifyContent: "flex-start";
justifyContent: "center";
justifyContent: "space-between";
justifyContent: "space-around";
justifyContent: "space-evenly";
justifyContent: "flex-end";
```

---

### 4. `alignItems`

Aligns items on the **cross axis**.

```tsx
alignItems: "flex-start";
alignItems: "center";
alignItems: "stretch";
alignItems: "flex-end";
```

---

### 5. `alignSelf`

Overrides alignment for an individual child.

```tsx
alignSelf: "center";
```

---

### 6. `flexWrap`

Wraps children onto multiple lines.

```tsx
flexWrap: "wrap";
```

Useful for tags, chips, and image grids.

---

### 7. `gap`

Modern React Native versions support spacing between children.

```tsx
gap: 12;
```

This is cleaner than manually adding margins, though verify support in the React Native version used by your project.

---

## Common layout patterns

### Equal columns

```tsx
<View style={{ flexDirection: "row" }}>
  <View style={{ flex: 1 }} />
  <View style={{ flex: 1 }} />
</View>
```

---

### Sidebar + content

```tsx
<View style={{ flexDirection: "row" }}>
  <View style={{ width: 80 }} />
  <View style={{ flex: 1 }} />
</View>
```

---

### Center everything

```tsx
<View
  style={{
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  }}
>
```

---

### Bottom button

```tsx
<View style={{ flex: 1 }}>
  <View style={{ flex: 1 }} />
  <Button title="Continue" />
</View>
```

---

## React Native vs Web

| Web CSS            | React Native              |
| ------------------ | ------------------------- |
| default `row`      | default `column`          |
| CSS engine         | Yoga layout engine        |
| `%` widths common  | `flex` preferred          |
| CSS Grid available | No CSS Grid (use Flexbox) |
| Browser layout     | Native layout calculation |

---

## Flexbox in the New Architecture (Fabric)

With the **New Architecture (Fabric)**, layout is still calculated using **Yoga**, but rendering is better integrated with React's concurrent rendering model. This reduces synchronization overhead between JavaScript and native rendering compared with the legacy bridge. Flexbox APIs remain the same, so existing layouts generally continue to work without changes.

---

# Example

### Create the project (Expo + TypeScript)

```bash
npx create-expo-app FlexboxDemo --template expo-template-blank-typescript

cd FlexboxDemo

npx expo start
```

`App.tsx`

```tsx
import React from "react";
import { SafeAreaView, StyleSheet, Text, View } from "react-native";

export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.row}>
        <View style={[styles.box, { backgroundColor: "#4CAF50" }]}>
          <Text>A</Text>
        </View>

        <View style={[styles.box, { backgroundColor: "#03A9F4" }]}>
          <Text>B</Text>
        </View>

        <View style={[styles.box, { backgroundColor: "#FF9800" }]}>
          <Text>C</Text>
        </View>
      </View>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    padding: 20,
  },
  row: {
    flexDirection: "row",
    justifyContent: "space-between",
    alignItems: "center",
  },
  box: {
    flex: 1,
    height: 100,
    marginHorizontal: 6,
    justifyContent: "center",
    alignItems: "center",
  },
});
```

Run:

```bash
# Android
npx expo run:android

# iOS
npx expo run:ios
```

This example demonstrates `flexDirection`, `flex`, `justifyContent`, and `alignItems`.

---

# Tooling & Setup

- **Preferred stack:** **Expo (TypeScript)** for rapid development and excellent developer experience. Use the React Native CLI when you need extensive native customization or custom build pipelines.
- **Avoid Create React App (CRA):** CRA is deprecated and intended for web applications, not React Native.
- **Bundler:** React Native uses **Metro**, which understands platform-specific files (e.g., `Component.ios.tsx` and `Component.android.tsx`) and supports Fast Refresh.
- **Modules:** Modern React Native projects increasingly use **ES Modules (`import`/`export`)**. Metro handles module resolution, while CommonJS is still supported for compatibility with some packages.

---

# Performance

- Use **Flexbox** instead of manually calculating positions with absolute layouts whenever possible, allowing Yoga to optimize layout calculations.
- Prefer `StyleSheet.create()` to define reusable styles outside the render path.
- Memoize expensive child components with `React.memo` and stabilize callbacks using `useCallback` when passing props deeply.
- Avoid deeply nested view hierarchies, as excessive nesting increases layout work.
- For long lists, use `FlatList` or `SectionList` rather than mapping large arrays into `ScrollView`.
- Profile layout performance using:
  - React DevTools **Profiler**
  - **Flipper** (Layout Inspector and React DevTools plugins)
  - React Native **Perf Monitor**

- Optimize images using appropriately sized assets or libraries such as Expo Image when applicable.
- Under the New Architecture, **Fabric** reduces bridge overhead for rendering, while **TurboModules** improve native module invocation performance.

---

# Testing

- **Unit/Component tests:** **Jest** + **React Native Testing Library**
- **End-to-end tests:** **Detox** (native) or **Maestro** (cross-platform UI flows)

Example:

```bash
npm test
```

Example assertion:

```tsx
expect(getByText("A")).toBeTruthy();
```

---

# Ops & Deployment

- Use structured logging and report runtime crashes with services such as Sentry.
- Handle layout changes (e.g., orientation) gracefully using responsive Flexbox rather than hard-coded dimensions.
- Cache data for offline support using libraries like AsyncStorage together with a state management solution.
- **CI/CD:** Use **Expo EAS Build**, Bitrise, or GitHub Actions to automate builds and tests.
- **Deployment:** Publish through the App Store and Google Play. For Expo apps, use **EAS Update** for supported over-the-air (OTA) JavaScript and asset updates. For bare React Native apps, OTA solutions depend on your chosen tooling and platform policies.

---

# Pitfalls

- **Remember the default direction is `column`**, not `row`.
- **Avoid fixed widths/heights** unless necessary; prefer `flex` for responsive layouts.
- **Don't overuse absolute positioning** for layouts that Flexbox can handle more reliably and responsively.

## Question 2. How do you **stack components vertically and horizontally**?

## Question 3. How do you **wrap components in a ScrollView**?

## Question 4. How do you **disable scrolling in ScrollView**?

## Question 5. How do you **apply sticky headers in ScrollView**?

## Question 6. How do you **handle orientation changes in ScrollView**?

## Question 7. How do you **measure component dimensions using onLayout**?

## Question 8. How do you **conditionally render components based on state**?

## Question 9. How do you **use fragments to avoid extra View wrappers**?

## Question 10. How do you **set background color dynamically**?

## Question 11. How do you **handle multiple FlatLists on one screen**?

## Question 12. How do you **implement animated headers on scroll**?

## Question 13. How do you **measure scroll position** in FlatList?

## Question 14. How do you **programmatically scroll FlatList to a specific index**?

## Question 15. How do you **use sticky headers in SectionList**?

## Question 16. How do you **optimize SectionList for large datasets**?

## Question 17. How do you **use FlatList item separators**?

## Question 18. How do you **handle item clicks in FlatList or SectionList**?

## Question 19. How do you **implement item selection in lists**?

## Question 20. How do you **implement swipeable FlatList items**?
