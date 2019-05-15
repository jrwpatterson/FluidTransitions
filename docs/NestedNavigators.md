# Nesting FluidTransitions inside other routers

There is a gesture problem wih the FluidTransitions and either createBottomTabNavigator or createStackNavigator which means that both the core navigators and Fluid navigation which often means that the swipe back gesture can swipe back to the FluidTransitions parent page rather than previous page.

This can be fixed by dynamically turning off the gesture handler of the stack navigator.

## Nesting inside of createStackNavigator

If you want to show a fluid navigator inside of create stacknavigator we have to disable gestures as below

We have a FluidNavigator called storeFrontNavigator which we want to have a dynamic navigation between store and product

So we set up storefront with a screen with dynamic navigation options which if the state.index > 0 we want that screen to not have gestures.

if the index of the storeFrontNavigator is 0 we turn them back on allowing the router to take us back to the home screen

```javascript

const storeFrontNavigator = FluidNavigator(
  {
    store: { screen: StoreScene },
    product: { screen: ProductScene },
  },
  {
    initialRouteName: 'store'
    navigationOptions: {
      gesturesEnabled: true,
    },
    mode: 'card',
    transitionConfig: {
      duration: 200,
      useNativeDriver: true,
    },
  },
)

const MenuNavigator = createStackNavigator(
  {
    home: {
      screen: HomeScene,
    },
    storefront: {
      screen: storeFrontNavigator,
      navigationOptions: (obj) => {
        const { state } = obj.navigation
        if (state.index > 0) {
          return {
            gesturesEnabled: false,
          }
        }

        return {
          gesturesEnabled: true,
        }
      },
    },
  },
  {
    headerMode: 'none',
    initialRouteName: 'home',
    headerBackTitleVisible: true,
    navigationOptions: {
      gesturesEnabled: true,
    },
    mode: 'card',
  },
)
```

## Nesting inside of createBottomTabNavigator

This is very similar to the fix above, only now you have account for the route tab too.

so in this situation we have a stack navigator with a tabnavigator which then has a fluidNavigator inside on the third tab.

So to stop the back gesture its almost identical code but on the stack navigator we have to disable the back button on the 3rd tab so now we look for the state.routes[2].index and if its more than one we turn off the navigator.

```javascript
const storeFrontNavigator = FluidNavigator(
  {
    store: { screen: StoreScene },
    product: { screen: ProductScene },
  },
  {
    initialRouteName: 'store'
    navigationOptions: {
      gesturesEnabled: true,
    },
    mode: 'card',
    transitionConfig: {
      duration: 200,
      useNativeDriver: true,
    },
  },
)

const menuTabNavigator = createBottomTabNavigator(
  {
    accountTab: {
      screen: Account,
    },
    offersTab: {
      screen: Offers
    },
    storeTab: {
      screen: storeFrontNavigator,
    },
    basketTab: {
      screen: basket
    }
  },
  {
    initialRouteName: 'storeTab'
    navigationOptions: {
      gesturesEnabled: true,
    },
  },
)

const RootNavigator = createStackNavigator(
  {
    home: {
      screen: HomeScene,
    },
    menuTabNavigator: {
      screen: menuTabNavigator,
      navigationOptions: (obj) => {
        const { state } = obj.navigation
        if (state.routes[2].index > 0) {
          return {
            gesturesEnabled: false,
          }
        }

        return {
          gesturesEnabled: true,
        }
      },
    },
  },
  {
    headerMode: 'none',
    initialRouteName: 'home',
    navigationOptions: {
      gesturesEnabled: true,
    },
    mode: 'card',
  },
)
```
