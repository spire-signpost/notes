### React Native - Basics - ScrollView

A brief intro to the basics of ScrollViews in React Native app development.

#### Contents
* intro
* general usage

#### Intro
Scrolling in React Native apps is achieved with a generic scrolling container, `ScrollView`.

This specific view container can itself accept multiple child components and views.

Another useful feature of the scrollview container is the simple option to specify direction, either horizontal or vertical.

#### general usage
We can add a `ScrollView` using the same general pattern as a standard `View` component.

We may also return a `ScrollView` as eiher the primary container for a component or a child of a standard `View`.

This means an app's screen may either scroll top to bottom, as a standard scrollable page, or simply present a component with scroll features.

e.g.

```javascript
export default class ScrollTester extends Component {
  render() {
    return (
				<View style={styles.container}>
					<View style={styles.headingBox}>
          	<Text style={styles.heading1}>
            	Scroll View Tester
          	</Text>
        	</View>
        	<View style={styles.subHeadingBox}>
          	<Text style={styles.heading2}>
            	{intro.heading}
          	</Text>
          	<Text style={styles.content}>
            	{intro.description}
          	</Text>
        	</View>
					<ScrollView>
					<View style={styles.contentBox}>
          	<Text style={styles.heading3}>
            	Lorem Ipsum Generator
          	</Text>
          	<Text style={styles.content}>
							...
						</Text>
        	</View>
					</ScrollView>
      	</View>
    );
  }
}
```
