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

# Inputs / Outputs

# Forms

## Validation

## model-options

## submit

# Handling Events

# Lifecycle methods

# Data flow between components

# Lists
ng-repeat, trackby

# Child nodes

ViewChild vs refs

ContentChild vs this.props.children 

# Transclusion / Containment

## Basic

### AngularJS

```js
angular.module('app.layout', [])
.component('layout', {
  bindings: {
    theme: '@',
  },
  controller: LayoutController,
  transclude: true,
  template: `
    <div ng-class="'theme-' + $ctrl.theme">
      <ng-transclude></ng-transclude>
    </div>
  `,
}).component('pageContent', {
  template: `<div>Some content</div>`,
}).component('pageFooter', {
  template: `<footer>Some content</footer>`,
});
```
```html
<layout theme="dark">
  <page-content></page-content>
  <page-footer></page-footer>
</layout>
```

### Angular

```js
@Component({
  selector: 'layout',
  template: `
    <div>
      <ng-content></ng-content>
    </div>
  `
})
export class Layout {}

@Component({
  selector: 'page-content',
  template: `<div>Some content</div>`,
})
export class PageContent {}

@Component({
  selector: 'page-footer',
  template: `<footer>Some content</footer>`,
})
export class PageFooter {}
```
```html
<layout>
  <page-content></page-content>
  <page-footer></page-footer>
</layout>
```

### React

```jsx
const Layout = ({ children, theme }) => (
  <div className={`theme-${theme}`}>
    {children}
  </div>
);

const PageContent = () => (
  <div>Some content</div>
);

const PageFooter = () => (
  <div>Some content</div>
);

<Layout theme='dark'>
  <PageContent />
  <PageFooter />
</Layout>
```

## Multiple slots

### AngularJS

```js
angular.module('app.layout', [])
.component('landingSection', {
  bindings: {},
  controller: LandingSectionController,
  transclude: {
    contentSlot: '?content', // '?' indicates an optional slot
    iconSlot: '?icon'
  },
  template: `
    <div>
      <span ng-transclude="contentSlot"></span>
      <div>
        <span ng-transclude="iconSlot">This is a default value</span>
      </dev>
    </div>
  `,
}).component('pageContent', {
  template: `<div>Some content</div>`,
});
```
```html
<div>
  <h1>Page title</h1>
  <landing-section>
    <page-content></page-content>
  </landing-section>
</div>
```

### Angular

### React

```jsx
const Layout = ({ children, theme }) => (
  <div className={`theme-${theme}`}>
    <header>{children.header}</header>
    <main>{children.content}</main>
    <footer>{children.footer}</footer>
  </div>
);

const Header = () => (
  <h1>My Header</h1>
);

const Footer = () => (
  <div>My Footer</div>
);

const Content = () => (
  <div>Some fancy content</div>
);

<Layout theme='dark'>
{{
  header: <Header />,
  content: <Content />,
  footer: <Footer />
}}
</Layout>
```

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
