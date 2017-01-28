# Frameworks code comparison
Different frameworks' approach to solving problems based on React, AngularJS and Angular.

Note regarding framework names:
- AngularJS means Angular v1.x
- Angular menas Angular v2+

See: http://angularjs.blogspot.com/2017/01/branding-guidelines-for-angular-and.html

All examples try to follow the current best practises and convesions inside the given framework community.
Angular code is written in TypeScript.

# Simple component 

### AngularJS
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

### React
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

### AngularJS
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

### React
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

### AngularJS
Values on custom components must be expressions which are interpolated.

```html
<primary-button size="'big'"
                disabled="true"
                click="$ctrl.saveContent()">
  Save
</primary-button>
```

### Angular
There are three kinds of possible attributes being passed:
- text binding e.g. size="string"
- property binding e.g. [disabled]="value"
- event binding e.g. (click)="eventHandler()"

```html
<primary-button size="big"
                [disabled]="true"
                (click)="saveContent()">
  Save
</primary-button>
```

### React
Templates in React are writtien inside the JavaScript file using the [JSX language](https://facebook.github.io/react/docs/jsx-in-depth.html). This allows us to utilize the full JavaScript capabilities. JSX uses the upper vs. lower case convention to distinguish between user-defined components and DOM tags.

```jsx
<PrimaryButton
  size="big"
  disabled
  onClick={this.saveContent}
>
  Save
</PrimaryButton>
```

# Interpolation

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

# Inject HTML template (aka. innerHTML)

### AngularJS
By default, the HTML content will be sanitized using the [$sanitize](https://docs.angularjs.org/api/ngSanitize/service/$sanitize) service. To utilize this functionality you need to include `ngSanitize` in your module's dependencies. [Read more](https://docs.angularjs.org/api/ng/directive/ngBindHtml)

```html
<p ng-bind-html="$ctrl.article.content"></p>
```

### Angular
It automatically sanitizes the values before displaying them using [DomSanitizer](https://angular.io/docs/ts/latest/api/platform-browser/index/DomSanitizer-class.html)

```html
<p [innerHTML]="article.content"></p>
```

### React
All string values are sanitized before being inserted into the DOM. No more details are currently available.
You need to pass an object containing `__html` property with desired template content.

```jsx
<p dangerouslySetInnerHTML={{__html: article.content}} />
```
