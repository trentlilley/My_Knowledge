# Table of Contents

# Overview

# Setup

## Expo
- The fastest way to set up an app is to set up the expo cli
- Initialise the project in VSCode
- Finally, start project
```bash
npm install -g expo-cli
expo init myProject
expo start
```

- After `expo start` a QR code should appear on the screen. On Android, use the Expo Go app to scan the QR code and run the development environment. On iOS, use the camera app instead
- You may need to refresh the window in order to see your live changes
- The starting code is in App.js
```jsx
import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View } from 'react-native';

// App.js
export default function App() {
  return (
    <View style={styles.container}>
      <Text>Hello World</Text>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

# Rules and Syntax
- Since there is no DOM in a mobile application, we cannot use any HTML tags in React Native
- Similarly, since we are not working in a browser, CSS cannot be used to style, however React Native provides StyleSheet objects that can be used to contain data similar to CSS classes
- Instead, we must use the built-in components provided to us by React Native to build our UI
- All text must be enclosed in `<Text></Text>` component tags

# Components
- All components must be imported from the react-native package
```jsx
import { StyleSheet, Text, View } from 'react-native';
```

Basic components
- `<View>` functionally similar to a div in HTML, they are used to contain other elements
- `<Text>` all text to be displayed must be wrapped in these component tags
- `<Header>` A text element for headers
- `<Image>` all images must be wrapped in these tags
- `<TextInput>` a box where the user can supply text
- `<Button>` a button
- `<Switch>` an on-off toggle

Layout Components
- `<ScrollView>` (Not recommended) a scrolling container that can host multiple components. It has poor performance because it renders everything outside of the user's view
- `<FlatList>` a scroll view that does not render components outside the user's view. Ideal for representing long pages or several columns/rows of data

# Styles
- Styles still follow the typical CSS box model
- Colors support hex values and shortcuts

## Box Model

- `margin`
- `padding`
- `borderWidth` border thickness
- `borderColor` 
- `borderRadius`

## Flexbox
- `flex` accepts an integer. Integer represents the ratio of space that it should share with other flex containers on its same heirarchy level. If there are no other flex containers, put 1 to ensure your container uses all available space. 
- `flexDirection` column by default, but can be changed to 'row'
- `flex-wrap` controls what happens to childern on overflow
- `justifyContent` aligns children on the main axis (vertical if column, horizontal if row). Defaults to flex-start.
- `alignItems` aligns children on the secondary axis. Defaults to stretch which makes children of a container match the height of the container's secondary axis
- `alignSelf` same as alignItems but applies to a single child, overrides alignItems
- 

## Applying Styles
- Each component should have a styles constant. This can contain one or more style classes
```jsx
const styles = StyleSheet.create({
  container-white: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
  container-black: {
    flex: 1,
    backgroundColor: '#000',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

- To apply the style, invoke the style prop on the component you wish to style and reference the style constant and class

```jsx
return (
  <View style={styles.container-white}>
  <View style={styles.container-black}>
)
```
