## This is a walkthrough step-by-step guide to implement redux in your react.js projects
Disclaimer: i am in any way shape or form not the maintainer for the libraries used in this, this is a basic walkthrough guide i created to provide a quick solution for everyone. Enjoy

### Table of contents:
1. [Install Redux](#install-redux-libraries)
2. [Making the file structure](#making-the-file-structure)
3. [Setting up for the components](#setting-up-for-the-components)


### Install Redux libraries
To use redux in react.js you will require following dependencies, run the following command in your terminal
   ``` 
  $ npm i react-redux ; npm i redux ; npm i redux-thunk 
   ```

___


### Making the file structure
In your root project folder go to `/src`, here you have to use `<Provider>` to wrap your project.
You can either write it in **App.js** _or_ **Index.js**, i'm going to do it in `Index.js`

```
import { Provider } from "react-redux";
import store from "./store";

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```
Provider requires a **store** which we will create in a moment.

Create a file **store.js** in the same directory and copy/paste the following code

```
import { createStore, applyMiddleware, compose } from "redux";
import rootReducer from "./reducers";
import thunk from "redux-thunk";

const initialState = {};
const middleware = [thunk];

const store = createStore(
  rootReducer,
  initialState,
  compose(
    applyMiddleware(...middleware),
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__() 
    // This is the redux dev extension which you have to add in your browser, either Chrome or Firefox
  )
);

export default store;
```
Now we will create reducers as our store requires it. Create a new directory **reducers** and in it create **index.js**. Copy/paste the following code in `reducers/index.js`
```
import { combineReducers } from "redux";
import yourReducerName from "./YourReducerFile";

export default combineReducers({
  reducerName: yourReducerName
});
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

cd out into **./src** and create an **actions** folder and in it place **types.js** e.g `./src/actions/types.js`. 

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



### Setting up for the components
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
