#react-native-app-navigator
Easily set up navigation within your app: react-native-app-navigator allows pushing and popping scenes based on a centrally maintained stack of routes, all the while showing a customisable navigation bar. 

react-native-app-navigator is built as a wrapper around React Native's [NavigationExperimental](http://facebook.github.io/react-native/docs/navigation.html#navigationexperimental). It makes some of the decisions for you and uses a generic reducer, providing a simpler interface and a couple of extra treats.

## Installation
```npm install --save react-native-app-navigator```

## Why Use
* Simple interface due to internal implementation of a generic reducer, taking away some of the pain of NavigationExperimental
* Provides easily customisable navigation bar across the app and/or for specific pages - just pass through functions as props to render the centre and right components
* Extra props can be seamlessly passed through from parent to child scene, as an argument in the call to onNavigate
* Every scene can check a prop ```topRoute``` to check if it is currently at the top of the navigation stack
* 

## Usage
The Navigator is intended to be a container component, capturing your whole app. It retains some of the mechanisms of NavigationExperimental, such as passing in a ```renderScene``` function to dynamically define the contents of a given route, as well as a few extra things.

### Props
| Prop Name             | Type     | Description                                                                                                                                                                                                             |
| --------------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| renderScene           | function | A callback that dynamically defines the content within Navigator, given a route. Details [below](#renderScene(navigationProps)).                                                                                        |
| navBarStyle           | style    | Will override styling of the NavigationHeader (top nav bar)                                                                                                                                                             |
| renderRightComponent  | function | Optional callback that defines what is at the right of the nav bar (e.g. a logout button) N.B. will be overridden by any renderCentreComponent passed through within the extraProps argument to onNavigate (see below)                                                                                                                               |
| renderCentreComponent | function | Optional callback that defines what is in the middle of the nav bar (e.g. a company logo). |
| backgroundColor       | string   | Defines the colour behind the scenes                                                                                                                                                                                    |
| headerViewProps       | object   | Props to pass through to the view enclosing the nav bar content                                                                                                                                                         |
| title                 | string   | The label to display on the left of the nav bar (next to the back button, e.g. the page title)                                                                                                                          |

### renderScene(navigationProps)
An important part of using react-native-app-navigator is implementing renderScene, the callback that dynamically defines the content within Navigator, given a route.

The function takes in two arguments
* route (object) - contains all the details about the scene to be rendered, with the following properties:
    * key (string) - A unique key indicating what page/scene should be rendered. Before any navigation, this will be 'root', so renderScene should handle rendering the default page in this instance
    * topRoute (boolean) - True if this scene is currently on the top of the navigation stack (being rendered)
    * Any additional properties that were provided in the ```navigationAction``` passed to ```onNavigate``` (other than those used by Navigator, see below)
* onNavigate (function(key, title, extraProps, navType)) - a function that can be called to navigate to a different scene, taking a single ```navigationAction``` parameter defining the navigation. This ```navigationAction``` is an object with the following properties:
    * key (string, required) - A unique key indicating what page/scene is being navigated to. This is generally used in renderScene to determine what to render
    * title (string, optional) - A string to display on the left of the navigation bar after navigating (e.g. the title of the new page)
    * extraProps (object, optional) - Any additional properties that are simply passed through as extra properties in the ```route``` given to ```renderScene```. A good way to get information from the parent page to the child in the form of props. If a special key, ```renderRightComponent```, is included, this will be the function used to render the right hand component of the navigation bar for that scene
    * navType (string, optional) - Optionally define what kind of navigation action this is. Defaults to a push. Options are:
        * ```push``` - Adds a new scene on to the stack and navigates to it
        * ```pop``` - Removes the top scene from the stack, navigating to the one just below
        * ```replace``` - Adds a new scene on to the stack, navigates to it, and removes the current top one
        * ```replacePreviousAndPop``` - Replaces the scene one below the top with a new scene and pops the current scene to navigate to the new one

Note: Initial renderScene is called with a route having the key 'root', so renderScene should handle rendering the default page in this instance

```
  /**
  * Returns either the Home page or the Calendar page, depending on the route (with any extra props passed on to CalendarPage)
  **/
  renderScene(route, onNavigate) {
    const { key, ...extraProps } = route;
    switch (key) {
      default:
      case 'root':
        return (
          <HomePage handleTappingMonth={(month) => {
            onNavigate(
              'calender', 
              month, 
              { monthToDisplay: month, backgroundImage: this.props.backgroundImage }
            )}} 
           />);
      case 'calendar':
        return <CalendarPage {...extraProps} />;
    }
  }
```
