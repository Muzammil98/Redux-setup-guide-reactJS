## A step-by-step guide to use redux and redux-persist in your React.js projects

Disclaimer: i am in any way shape or form not the maintainer for the libraries used in this, this is a basic walkthrough guide i created to provide a quick solution for everyone.If you have any issues, refer to the official docs for these libraries. Enjoy

### Table of contents:
1. [Install Redux](#install-redux-libraries)
2. [Making the file structure](#making-the-file-structure)
3. [Setting up for the components](#setting-up-for-the-components)


## 1. Install Redux libraries
To use redux in react.js, first you will require following dependencies, run the following command in your terminal
   ``` 
  $ npm i redux react-redux redux-thunk redux-persist --save
  $ npm i redux-logger --save-dev 
   ```
 __You can always remove any library which you do not need by `npm uninstall <lib name>`__
___


## 2. Making the file structure
In your project folder go to `/src` where your __Index.js__ and __App.js__ are, here you have to create some files and folders, to ease things up just run the following code in your terminal
 ```
$ mkdir redux ; cd redux ; mkdir reducers ; mkdir actions ; touch store.js ; cd reducers ; touch index.js ; cd .. ; cd actions ; touch authAction.js ; touch types.js ; cd .. 

 ```
 you will get something like this structure in ./src/redux
 
 ![Alt text](https://user-images.githubusercontent.com/33463845/77819324-e340d300-70fb-11ea-983b-34ea46394657.png)

You can either wrap _Provider_ and _PersistGate_ in **App.js** _or_ **Index.js**, i'm going to do it in `Index.js`

```
import { Provider } from "react-redux";
import { PersistGate } from 'redux-persist/integration/react';
import { store, persistor } from "./store";

ReactDOM.render(
  <Provider store={store}>
    <PersistGate persistor={persistor}>
     <App />
    </PersistGate>
  </Provider>,
  document.getElementById("root")
);
```

Goto **store.js** in the same directory and copy/paste the following code

```
import { createStore, applyMiddleware, compose } from "redux";
import rootReducer from "./reducers";
import thunk from "redux-thunk";
import logger from 'redux-logger';
import {persistStore} from 'redux-persist';

const initialState = {};
const middlewares = [thunk,logger];

const middleware = [
  applyMiddleware(...middlewares),
  ...(window.__REDUX_DEVTOOLS_EXTENSION__
    ? [window.__REDUX_DEVTOOLS_EXTENSION__()]
    : []),
];

const store = createStore(
  rootReducer,
  initialState,
  compose(...middleware)
);
const persistor = persistStore(store);

export default {store,persistor};
```
Now we will create reducers as our store requires it. Go into `reducers/index.js` and in it copy/paste the following code.
```
import { combineReducers } from "redux";
import {persistReducer} from 'redux-persist';
import storage from 'redux-persist/lib/storage'; // localstorage

import yourReducerName from "./YourReducerFile";  // create this file in the same directory

const persistConfig = {
   key:'root',
   storage,
   whitelist:['reducerName']  // reducer which i want to persist goes here (can be more than one)
}
const rootReducer = combineReducers({
  reducerName: yourReducerName
});
export default persistReducer(persistConfig, rootReducer);
```
You have to add reducers here which you will create later on in future. Add any of those reducers in the same **reducers** directory.

Following is the _example code_ for your custom reducer files.
```
import { SET_CURRENT_USER } from "../actions/types"; // Reducers requires action types for switch cases
import isEmpty from "../validation/is-empty";

const initialState = {
  isAuthenticated: false,
  user: {}
};
export default function(state = initialState, action) {
  switch (action.type) {
    case SET_CURRENT_USER:
      return {
        ...state,
        isAuthenticated: !isEmpty(action.payload),
        user: action.payload
      };
    default:
      return state;
  }
}
```

cd out into **./src** and goto `./actions/types.js`. 

This file will hold your types for actions reducers such as, 
` export const GET_ERRORS = "GET_ERRORS"; `

In the same folder you can create any of your actions file e.g `./src/actions/authAction.js`, following is an example actions file
```
import axios from "axios"; // Library for fetching api

// Register User
export const registerUser = (userData, history) => dispatch => {
  axios
    .post("/api/users/register", userData)
    .then(res => {
      // Do something here on success
    })
    .catch(err =>
      dispatch({
        type: GET_ERRORS,
        payload: err.response.data
      })
    );
};

```


___



## 3. Setting up for the components
Everything is now pretty much done, but now we want to use this in our components.
For that we require __connect__ from __react-redux__ and the _actions_ we want to use in our componets e.g.
```
import { addPerson } from "../../actions/exampleActions"; // Action i want to use
import { connect } from "react-redux";


....
  // This is how i can use my actions inside a component (here it is a class-based) and also pass arguments (if any)
  this.props.addPerson(data);
...

const mapStateToProps = state => ({
  // these are the states which i will use from redux as props
  persons: state.person, 
  errors: state.errors
});

export default connect(mapStateToProps, { addPerson })(ComponentName);
```
___

That is it, now you can use **Redux** in your projects :wink: :+1:  Happy coding


