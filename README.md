DEMO: https://github.com/mlizchap/react-hoc-login-app

# Higher Order Components:
- a react component made to reuse code
- examples: `connect` from react-redux, `route` from react-router

## Steps
1. [Write the logic you want to reuse in an existing component](#step-1)
2. [Create a HOC file and add the HOC scaffold boiler plate code](#step-2)
3. [Move the reusable logic into the HOC](#step-3)
4. [Pass the props/config/behavior](#step-4)
5. [Hook-up child component to HOC](#step-5)

## User Authentication HOC Example
### Step 1
- Write the logic you want to reuse in an existing component 
  - logic in the user auth components -
    - check if the connect to redux to check the state props to see if the user is authenticated.  
    - if the user is authenticated (state is true) - allow access to component
    - if user is not authenticated - re-route user back to the home page
    ```javascript
    class CommentList extends Component {
          componentDidMount() {
              this.shouldNavigateAway();
          }

          componentDidUpdate() {
              this.shouldNavigateAway();
          }

          shouldNavigateAway() {
              if (!this.props.auth) {
                  this.props.history.push('/')
              } 
          }
        render() {
            return (
                <div>
                    <h4>comment list</h4>
                    <ul>
                      <li>comment #1</li>
                      <li>comment #2</li>
                    </ul> 
                </div>
            );
         }
      }

      function mapStateToProps(state) {
        return { auth: state.auth }
      }

    export default connect(mapStateToProps)CommentList;
    ```
### Step 2
- Create the HOC file and add the scaffolding
  - create a file for the HOC (begin with a lower-case)
    - lower-case = exports a function
    - upper-case = exports a class
  - create the scaffolding - a `composed component` will take a child component as an arg and return the child component.  When we import the child component we are importing the return value result of the `composed component` class.
    ```javascript
    import React, { Component } from 'react';

    export default (ChildComponent) => {
        class ComposedComponent extends Component {
             render() {
                return <ChildComponent />
            }
        }
      }
    ```
### Step 3
- Move the reusable logic into the HOC
  - for user auth component 
    - move the componentDidMount, componentDidUpdate, shoouldNavigateAway functions
    - move the connect/mapStateToProps functionality  
  ```javascript
    import { connect } from 'react-redux';

    export default (ChildComponent) => {
      class ComposedComponent extends Component {
          componentDidMount() {
              this.shouldNavigateAway();
          }

          componentDidUpdate() {
              this.shouldNavigateAway();
          }

          shouldNavigateAway() {
              if (!this.props.auth) {
                  this.props.history.push('/')
              } 
          }
          render() {
              return <ChildComponent {...this.props}/>
          }
      }

      function mapStateToProps(state) {
          return { auth: state.auth }
      }

      return connect(mapStateToProps)(ComposedComponent);
  };
  ```
### Step 4
- Pass the props/config/behavior 
  - for components using the auth HOC - we want the actions props from the redux `connect` HOC and the `history` prop from react-router to be passed down to the childComponent.  To do this we add `{...this.props}` to the returned child component.
  ```javascript
      class ComposedComponent extends Component {

          /* ... */

          render() {
              return <ChildComponent {...this.props}/>
          }
      }
  ```
  
### Step 5
- import HOC function and pass in the child component class
  ```javascript
  import checkUserAuth from '../../checkUserAuth';

  class NewPost extends Component {
    /* ... */
  }

  export default connect(null, { createPost })(checkUserAuth(NewPost));
  ```

