# Set 8

| S.No. | Question                                                                                                                                 |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you implement section headers in SectionList?](#question-1-how-do-you-implement-section-headers-in-sectionlist)                  |
| 2.    | [How do you optimize large lists?](#question-2-how-do-you-optimize-large-lists)                                                          |
| 3.    | [How do you implement swipe-to-delete functionality?](#question-3-how-do-you-implement-swipe-to-delete-functionality)                    |
| 4.    | [How do you handle local file storage?](#question-4-how-do-you-handle-local-file-storage)                                                |
| 5.    | [How do you read device info (OS version, model)?](#question-5-how-do-you-read-device-info-os-version-model)                             |
| 6.    | [How do you detect screen dimensions and pixel ratio?](#question-6-how-do-you-detect-screen-dimensions-and-pixel-ratio)                  |
| 7.    | [How do you implement responsive design in React Native?](#question-7-how-do-you-implement-responsive-design-in-react-native)            |
| 8.    | [How do you implement custom modal dialogs?](#question-8-how-do-you-implement-custom-modal-dialogs)                                      |
| 9.    | [How do you implement alerts and confirmation popups?](#question-9-how-do-you-implement-alerts-and-confirmation-popups)                  |
| 10.   | [How do you handle time zones and date formatting?](#question-10-how-do-you-handle-time-zones-and-date-formatting)                       |
| 11.   | [How do you use moment.js / dayjs in React Native?](#question-11-how-do-you-use-momentjs--dayjs-in-react-native)                         |
| 12.   | [How do you implement custom hooks in React Native?](#question-12-how-do-you-implement-custom-hooks-in-react-native)                     |
| 13.   | [How do you handle debouncing and throttling in input fields?](#question-13-how-do-you-handle-debouncing-and-throttling-in-input-fields) |
| 14.   | [How do you implement search functionality in a FlatList?](#question-14-how-do-you-implement-search-functionality-in-a-flatlist)         |
| 15.   | [How do you implement animations on scroll?](#question-15-how-do-you-implement-animations-on-scroll)                                     |
| 16.   | [How do you implement staggered animations?](#question-16-how-do-you-implement-staggered-animations)                                     |
| 17.   | [How do you implement fade in/out animations?](#question-17-how-do-you-implement-fade-inout-animations)                                  |
| 18.   | [How do you use PanResponder to handle gestures?](#question-18-how-do-you-use-panresponder-to-handle-gestures)                           |
| 19.   | [How do you implement swipeable cards?](#question-19-how-do-you-implement-swipeable-cards)                                               |
| 20.   | [How do you implement drag-and-drop lists?](#question-20-how-do-you-implement-drag-and-drop-lists)                                       |

## Question 1. How do you implement **section headers** in SectionList?

# Short answer

Use the `renderSectionHeader` prop of `SectionList` to render a header for each section. Each section is an object containing a `title` (or any custom field) and a `data` array. For sticky headers, enable `stickySectionHeadersEnabled` (enabled by default on iOS).

---

# Explanation

`SectionList` is React Native's optimized component for rendering grouped lists, such as:

- Contacts grouped by alphabet
- Chat messages grouped by date
- Products grouped by category
- Settings grouped into sections

Each section has this shape:

```ts
{
  title: string;
  data: Item[];
}
```

The key prop is:

```tsx
renderSectionHeader={({ section }) => (
  <Text>{section.title}</Text>
)}
```

where:

- `section` is the current section object.
- You can access any custom properties (`title`, `count`, `color`, etc.).
- The header is rendered once per section.

Example data:

```tsx
const sections = [
  {
    title: "Fruits",
    data: ["Apple", "Banana"],
  },
  {
    title: "Vegetables",
    data: ["Carrot", "Potato"],
  },
];
```

### Rendering behavior

`SectionList` is built on top of `VirtualizedList`.

- Section headers participate in virtualization.
- Only visible headers/items are mounted.
- Large grouped datasets remain memory efficient.

### Sticky section headers

```tsx
stickySectionHeadersEnabled;
```

or

```tsx
stickySectionHeadersEnabled={true}
```

This keeps the current section header pinned to the top while scrolling.

Android and iOS both support sticky headers (behavior has become consistent in modern React Native versions).

### Custom section metadata

You aren't limited to `title`.

```tsx
const sections = [
  {
    title: "Today",
    color: "#0A84FF",
    count: 12,
    data: messages,
  },
];
```

Then:

```tsx
renderSectionHeader={({ section }) => (
    <View style={{ backgroundColor: section.color }}>
      <Text>{section.title}</Text>
      <Text>{section.count} items</Text>
    </View>
)}
```

This makes section headers useful for displaying totals, badges, timestamps, or status information.

---

# Example

**Expo (recommended)**

```bash
npx create-expo-app SectionListDemo --template expo-template-blank-typescript

cd SectionListDemo
npm start
```

`App.tsx`

```tsx
import React from "react";
import {
  SafeAreaView,
  SectionList,
  Text,
  View,
  StyleSheet,
} from "react-native";

const sections = [
  {
    title: "Fruits",
    data: ["Apple", "Banana", "Orange"],
  },
  {
    title: "Vegetables",
    data: ["Carrot", "Potato", "Tomato"],
  },
];

export default function App() {
  return (
    <SafeAreaView style={{ flex: 1 }}>
      <SectionList
        sections={sections}
        keyExtractor={(item) => item}
        stickySectionHeadersEnabled
        renderItem={({ item }) => <Text style={styles.item}>{item}</Text>}
        renderSectionHeader={({ section }) => (
          <View style={styles.header}>
            <Text style={styles.headerText}>{section.title}</Text>
          </View>
        )}
      />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  header: {
    backgroundColor: "#eee",
    padding: 10,
  },
  headerText: {
    fontWeight: "bold",
    fontSize: 18,
  },
  item: {
    padding: 12,
    borderBottomWidth: 1,
    borderBottomColor: "#ddd",
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

**Recommended stack:** Expo (TypeScript) for rapid development, or React Native CLI if you need custom native modules. Avoid Create React App (CRA); it is for web and deprecated for modern React workflows, not suitable for mobile.

- **Bundler:** Metro (default for React Native), optimized for native module resolution and fast refresh.
- **Module system:** Prefer ESM (`import`/`export`) with TypeScript. Metro supports modern JavaScript syntax and transpilation.
- **New Architecture:** When using Fabric and TurboModules, `SectionList` benefits from improved rendering and reduced UI synchronization overhead, though list optimization practices remain the same.

---

# Performance

For large grouped lists:

- Use stable `keyExtractor` values (avoid array indexes).
- Memoize expensive row and header components with `React.memo`.
- Use `useCallback` for `renderItem` and `renderSectionHeader` to avoid unnecessary function recreation.
- Tune virtualization with props such as `initialNumToRender`, `maxToRenderPerBatch`, `windowSize`, and `removeClippedSubviews` (test before enabling everywhere).
- If headers perform heavy computations (counts, formatting), compute them ahead of time or memoize with `useMemo`.
- Profile with:
  - React DevTools Profiler
  - Flipper React DevTools plugin
  - React Native Performance Monitor (`Show Perf Monitor`)

- Optimize remote images in list items with proper sizing and caching libraries if needed.
- If list items interact with native modules frequently, batch operations where possible to reduce bridge overhead on the old architecture; Fabric reduces some of this cost but doesn't eliminate inefficient rendering.

---

# Testing

- **Unit/Integration:** Use Jest with React Native Testing Library to verify headers render correctly.

Example:

```tsx
expect(getByText("Fruits")).toBeTruthy();
```

Run:

```bash
npm test
```

- **E2E:** Use Detox or Maestro to verify sticky headers, scrolling, and grouped content on real devices/emulators.

---

# Ops & Deployment

- Add logging for list loading states and rendering errors using your logging solution (e.g., Sentry).
- Handle empty sections gracefully with placeholders or omit them if appropriate.
- Cache grouped data for offline scenarios using libraries like AsyncStorage or state persistence solutions.
- Use GitHub Actions, Bitrise, or Expo Application Services (EAS) for CI/CD.
- For Expo apps, use EAS Update for OTA updates (when compatible with your release strategy); otherwise publish through the App Store and Google Play following native release requirements.

---

# Pitfalls

- Don't use array indexes as keys; unstable keys cause incorrect row reuse and unnecessary re-renders.
- Avoid expensive logic inside `renderSectionHeader`; memoize or precompute values instead.
- Keep section objects immutable—creating new section arrays on every render can cause unnecessary updates.

## Question 2. How do you **optimize large lists**?

## Question 3. How do you implement **swipe-to-delete functionality**?

## Question 4. How do you handle **local file storage**?

## Question 5. How do you read **device info** (OS version, model)?

## Question 6. How do you detect **screen dimensions** and **pixel ratio**?

## Question 7. How do you implement **responsive design** in React Native?

## Question 8. How do you implement **custom modal dialogs**?

## Question 9. How do you implement **alerts and confirmation popups**?

## Question 10. How do you handle **time zones and date formatting**?

## Question 11. How do you use **moment.js / dayjs** in React Native?

## Question 12. How do you implement **custom hooks** in React Native?

## Question 13. How do you handle **debouncing and throttling** in input fields?

## Question 14. How do you implement **search functionality** in a FlatList?

## Question 15. How do you implement **animations on scroll**?

## Question 16. How do you implement **staggered animations**?

## Question 17. How do you implement **fade in/out animations**?

## Question 18. How do you use **PanResponder** to handle gestures?

## Question 19. How do you implement **swipeable cards**?

## Question 20. How do you implement **drag-and-drop lists**?
