# Frameworks code comparison
Different frameworks' approach to solving problems based on React, Angular 1 and Angular 2.

# Simple component 

## Angular 1
```js
class ChangePasswordController {
    constructor($log, Auth, Notification) {
        'ngInject';

        this.$log = $log;
        this.Auth = Auth;
        this.Notification = Notification;
    }
    
    $onInit() {
        this.password = '';
    }

    changePassword() {
        this.Auth.changePassword(this.password).then(() => {
            this.Notification.info('Password has been changes successfully');
        }).catch(error => {
            this.$log.error(error);
            this.Notification.error('There was an error. Please try again');
        });
    }
}

// Object describing the component
const component = {
    bindings: {},
    template: changePasswordTemplate,
    controller: ChangePasswordController,
};

// Initialize the component in Angular's module
const module = angular.module('app.changePassword', [])
.component('changePassword', component);

```

## React
```js
import Logger from 'utils/logger'
import Notification from 'utils/notification'
import Auth from 'actions/auth'

const Hello = React.createClass({
  state = {
    password: ''
  };
  
  changePassword() {
    Auth.changePassword(this.state,password).then(() => {
      Notification.info('Password has been changes successfully');
    }).catch(error => {
      Logger.error(error);
      Notification.error('There was an error. Please try again');
    });
  }

  render: function() {
    return <div>Hello {this.state.password}</div>;
  }
}); 
```

# Dependency injection

## Angular 1
Constructor is a place to inject dependencies, what is done implicitly by the [$inject](https://docs.angularjs.org/api/auto/service/$injector) service.

`'ngInject'` annotation has been used which allows automatic method annotation by the ng-annotate plugin (e.g. [ng-annotate-loader](https://www.npmjs.com/package/ng-annotate-loader) for Webpack). That's essentailly needed to counter minification problems.

```js
class ChangePasswordController {
  constructor($log, Auth, Notification) {
    'ngInject';

    this.$log = $log;
    this.Auth = Auth;
    this.Notification = Notification;
  }

  handleEvent() {
    this.Notification.info('Event handled successfully');
  }
}
```

## React
There's no special injection mechanism. For dependency managment ES2015 modules are used.
```js
import Logger from 'utils/logger'
import Notification from 'utils/notification'
import Auth from 'actions/auth'

class ChangePassword extends React.Component {
  handleEvent = () => {
    Notification.info('Event handled successfully');
  }

  render() {
    return (
      <button onClick={this.handleEvent}>
        Hello world!
      </button>
    );
  }
}
```

# Templates

# Forms

## Validation

## model-options

# Handling Events

# Lifecycle methods

# Data flow between components

# Lists
ng-repeat, trackby

# Child nodes

ViewChild vs refs

ContentChild vs this.props.children 

# Transclusion

# Styling
