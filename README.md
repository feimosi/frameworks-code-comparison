# Frameworks code comparison
Frameworks approach to solving problems based on React, Angular 1, Angular 2

# Simple component 

## Angular 1
Constructor is a place to inject dependencies by the $inject service.
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

var Hello = React.createClass({
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

# Templates

# Forms

# Form validation
