React Router Testing With Jest
====================
Testing has become much easier since React Router version 1.x. For Testing prior React Router versions, see   [Old testing docs](https://github.com/rackt/react-router/blob/57543eb41ce45b994a29792d77c86cc10b51eac9/docs/guides/testing.md)

It is recommended that you read the following two tutorials prior:
- [Jest Getting Started docs](https://facebook.github.io/jest/docs/getting-started.html)
- [Jest ReactJS docs](https://facebook.github.io/jest/docs/tutorial-react.html)
- [ReactJS TestUtils docs](https://facebook.github.io/react/docs/test-utils.html)

Testing with React-Router 1.x should just work. But if you are having issues see the following. Many users had issues when upgrading prior setups from react-router 0.x.

Updating from testing setup from React-Router 0.x to 1.x
----------------------------------------------
Firstly, ensure you are using at least the following versions of each package.
- "react": "^0.14.0"
- "react-dom": "^0.14.0"
- "react-router": "^1.0.0"
- "react-addons-test-utils": "^0.14.0"
- "jest-cli": "^0.5.10"
- "babel-jest": "^5.3.0"

Also, make sure you are using node 4.x

In prior setups, react-tools was needed. This is not longer the case. You will need to remove it from your package.json and environment.

```
"react-tools": "~0.13.3",
```

Lastly, anywhere you have the following, needs to be replaced with this:

```
var React              = require('react/addons');
var TestUtils          = React.addons.TestUtils;
```

with this:

```
var TestUtils          = require('react-addons-test-utils');
var ReactDOM           = require('react-dom');
var React              = require('react');
```

Make sure you do an npm clean, install, etc. and make sure you add react-addons-test-utils and react-dom to your unmocked paths.

```json
  ...
  "unmockedModulePathPatterns": [
    "./node_modules/react",
    "./node_modules/react-dom",
    "./node_modules/react-addons-test-utils",
  ],
  ...

```

Lastly ensure you are using babel-jest for the script preproccessor:

```js
  ...
  "scriptPreprocessor": "./node_modules/babel-jest",
  ...
```


Example:
----------------------------------------------
A component:
```js
//../components/BasicPage.js

let React = require('react');

import { Button } from 'react-bootstrap';
import { Link } from 'react-router';

let BasicPage =
  React.createClass({
    propTypes: {
      authenticated: React.PropTypes.bool,
    },
    render:function(){
      let mainContent;
      let authenticated = this.props.authenticated;

      if(authenticated) {
        mainContent = (
          <div>
            <Link to="/admin"><Button bsStyle="primary">Login</Button></Link>
          </div>
        );
      } else {
        mainContent = (
          <div>
            <Link to="/admin"><Button bsStyle="primary">Login</Button></Link>
          </div>
        );

      }

      return (
        <div>
          {mainContent}
        </div>
      );
    },
  });
module.exports = BasicPage;
```

The test for that component:
```js
//../components/__tests__/BasicPage-test.js

jest.dontMock('../BasicPage');

describe('BasicPage', function() {
  let BasicPage          = require('../BasicPage');
  let TestUtils          = require('react-addons-test-utils');
  let ReactDOM           = require('react-dom');
  let React              = require('react');

  it('renders the Login button if not logged in', function() {
    let page = TestUtils.renderIntoDocument(<BasicPage />);
    let button = TestUtils.findRenderedDOMComponentWithTag(page, 'button');
    expect(ReactDOM.findDOMNode(button).textContent).toBe('Login');
  });

  it('renders the Account button if logged in', function() {
    let page = TestUtils.renderIntoDocument(<BasicPage authenticated={true} />);
    let button = TestUtils.findRenderedDOMComponentWithTag(page, 'button');
    expect(ReactDOM.findDOMNode(button).textContent).toBe('Your Account');
  });
});
```
