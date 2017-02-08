#react-native-app-navigator
Easily set up navigation within your app: react-native-app-navigator allows pushing and popping scenes based on a centrally maintained stack of routes, all the while showing a customisable navigation bar. 

react-native-app-navigator is built as a wrapper around React Native's [NavigationExperimental](http://facebook.github.io/react-native/docs/navigation.html#navigationexperimental). It makes some of the decisions for you and uses a generic reducer, providing a simpler interface and a couple of extra treats.

## Installation
```npm install --save react-native-app-navigator```

## Usage
The Navigator is intended to be a container component, capturing your whole app. It retains some of the mechanisms of NavigationExperimental, such as passing in a ```renderScene``` function to dynamically define the contents of a given route, as well as a few extra things.

### Props
| Prop Name             | Type     | Description                                                                                                      |
| --------------------- | -------- | ---------------------------------------------------------------------------------------------------------------- |
| renderScene           | function | A callback that dynamically defines the content within Navigator, given a route. Details [below](#renderScene).  |
| navBarStyle           | style    | Will override styling of the NavigationHeader (top nav bar)                                                      |
| renderRightComponent  | function | Optional callback that defines what is at the right of the nav bar (e.g. a logout button)                        |
| renderCentreComponent | function | Optional callback that defines what is in the middle of the nav bar (e.g. a company logo)                        |
| backgroundColor       | string   | Defines the colour behind the scenes                                                                             |
| headerViewProps       | object   | Props to pass through to the view enclosing the nav bar content                                                  |
| title                 | string   | The label to display on the left of the nav bar (next to the back button, e.g. the page title)                   |

### renderScene
An important part of using react-native-app-navigator is implementing renderScene, the callback that dynamically defines the content within Navigator, given a route.

The function takes in 
Initial renderScene called with key 'root'

```
  /**
  * Returns either the Home page or the Calendar page, depending on the route (with any extra props passed on to CalendarPage)
  **/
  renderScene(props) {
    const { key, ...extraProps } = props.scene.route;
    switch (key) {
      default:
      case 'root':
        return <HomePage />;
      case 'calendar':
        return <CalendarPage {...extraProps} />;
    }
  }
```
