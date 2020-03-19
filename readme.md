### This is a walkthrough step-by-step guide to implement redux in your react.js projects
First we will do the file's structure.
After installing 
   ``` 
   npm i react-redux ; npm i redux ; npm i redux-thunk 
   ```
In your react root project folder go to /src
You can connect your providers in App.js or Index.js, i will do here do it in Index.js
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
Now, Create a file name `store.js` and copy/paste the following code

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
  )
);

export default store;
```
Create a directory `reducers` and in it create `index.js`
Copy/paste the following code in `reducers/index.js`
```
import { combineReducers } from "redux";
import yourReducerName from "./YourReducerFile";

export default combineReducers({
  reducerName: yourReducerName
});
```
Following is the code for your custom reducer files which you will place in `reducers/YourReducerFile`

```
import { SET_CURRENT_USER } from "../actions/types";
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
cd out into `./src` and create these file's structure `actions/types.js`

this file will hold your types for actions reducers such as
` export const GET_ERRORS = "GET_ERRORS"; `
in the same folder you can create any of your actions file e.g 
`authAction.js` which will hold the following
```
import axios from "axios";

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

Everything is now pretty much setup, now how will we use this in our components.
Whenever we want to use it we require _connect_ from _react-redux_ and the actions we want to use in our componets e.g.
```
import { addStudent } from "../../actions/studentActions"; // Action i want to use
import { connect } from "react-redux";


....
  // This is how i can use my actions inside a component (here it is a class-based) and also pass arguments (if any)
  this.props.addStudent(newStudent);
...

const mapStateToProps = state => ({
  // these are the states which i will use from redux as props
  student: state.student, 
  errors: state.errors
});

export default connect(mapStateToProps, { addStudent })(ComponentName);
```

That's all Folks!!!
