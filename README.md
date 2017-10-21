<h1 align="center">Frontend frameworks code comparison</h1>

<div align="center">

![./assets/logos/angular.svg](./assets/logos/angular.svg)
![./assets/logos/react.svg](./assets/logos/react.svg)
![./assets/logos/vue.svg](./assets/logos/vue.svg)

</div>

Comparison of different approaches in writing web applications. Based on React, Angular, AngularJS and Vue.js. It is especially useful when migrating between frameworks or switching projects often.

All examples follow the current best practices and conventions that are used inside the community of a given framework. Angular code is written in TypeScript.

<h3 align="center"> :warning: Work in progress! PRs and Feedback are welcome :warning: </h3>

> Note regarding framework naming:
> - AngularJS refers to Angular v1.x
> - Angular refers to Angular v2+
>
> See: http://angularjs.blogspot.com/2017/01/branding-guidelines-for-angular-and.html

<h1 align="center">Table of contents</h1>

  * [Table of contents](#table-of-contents)
  * [Simple component](#simple-component)
  * [Dependency injection](#dependency-injection)
  * [Templates](#templates)
  * [Interpolation](#interpolation)
  * [Inputs and Outputs](#inputs-and-outputs)
  * [Default inputs](#default-inputs)
  * [Handling Events](#handling-events)
  * [Lifecycle methods](#lifecycle-methods)
  * [Conditional rendering](#conditional-rendering)
  * [Lists](#lists)
  * [Filters](#filters)
  * [Child nodes](#child-nodes)
  * [Transclusion and Containment](#transclusion-and-containment)
  * [Inject HTML template](#inject-html-template)
  * [Class toggling](#class-toggling)
  * [Data binding](#data-binding)
  * [Forms](#forms)
  * [Styling](#styling)

# Simple component

### AngularJS

```js
import angular from 'angular';
import template from './changePassword.html';
import './changePassword.scss';

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
      this.Notification.info('Password has been changed successfully.');
    }).catch(error => {
      this.$log.error(error);
      this.Notification.error('There was an error. Please try again.');
    });
  }
}
```

Every component has to be declared inside a module. After that, it will be available to every other component.

```js
const component = {
  bindings: {},
  template,
  controller: ChangePasswordController,
};

const module = angular
  .module('app.changePassword', [])
  .component('changePassword', component);
```

:arrow_right: https://docs.angularjs.org/guide/component

### Angular

```ts
import { Component } from '@angular/core';
import { Logger } from 'services/logger';
import { Auth } from 'services/auth';
import { Notification } from 'services/notification';

@Component({
  selector: 'change-password',
  templateUrl: './ChangePassword.component.html',
  styleUrls: ['./ChangePassword.component.scss'],
})
export class ChangePasswordComponent {
  password: string = '';

  constructor(
    private logger: Logger,
    private auth: Auth,
    private notification: Notification,
  ) {}

  changePassword() {
    this.auth.changePassword(this.password).subscribe(() => {
      this.notification.info('Password has been changes successfully');
    }).catch(error => {
      this.logger.error(error);
      this.notification.error('There was an error. Please try again');
    });
  }
}
```

Every component has to be declared inside a module in order to be used within this module's other components (it's not available outside of).

```ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { ChangePasswordComponent } from './change-password.component';

@NgModule({
  imports: [CommonModule],
  declarations: [ChangePasswordComponent],
})
export class ChangePasswordModule {}
```

:arrow_right: https://angular.io/api/core/Component

### React

```jsx
import Logger from 'utils/logger';
import Auth from 'actions/auth';
import Notification from 'utils/notification';

class ChangePassword {
  state = {
    password: '',
  };

  changePassword() {
    Auth.changePassword(this.state,password).then(() => {
      Notification.info('Password has been changed successfully.');
    }).catch(error => {
      Logger.error(error);
      Notification.error('There was an error. Please try again.');
    });
  }

  render() {
    return <div>{ /* template */ }</div>;
  }
});
```

:arrow_right: https://reactjs.org/docs/react-component.html

### Vue.js

```js
import Vue from 'vue';
import Logger from 'utils/logger';
import Auth from 'actions/auth';
import Notification from 'utils/notification';

Vue.component('change-password', {
  template: '<div>{{ /* template */ }}</div>',
  data() {
    return {
      password: '',
    };
  },
  methods: {
    changePassword() {
      Auth.changePassword(this.state,password).then(() => {
        Notification.info('Password has been changed successfully.');
      }).catch(error => {
        Logger.error(error);
        Notification.error('There was an error. Please try again.');
      });
    },
  },
});
```

:arrow_right: https://vuejs.org/v2/guide/components.html

# Dependency injection

### AngularJS

In AngularJS the constructor is being used to inject dependencies, which is done implicitly by the [$inject](https://docs.angularjs.org/api/auto/service/$injector) service.

The `'ngInject'` annotation can be used, which allows automatic method annotation by the ng-annotate plugin (e.g. [ng-annotate-loader](https://www.npmjs.com/package/ng-annotate-loader) for Webpack). This is essential to counter [minification problems](https://docs.angularjs.org/guide/di#dependency-annotation).

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

:arrow_right: https://docs.angularjs.org/guide/di

### Angular

You specify the definition of the dependencies in the constructor (leveraging TypeScript's constructor syntax for declaring parameters and properties simultaneously).

```ts
import { Component } from '@angular/core';
import { Logger } from 'services/logger';
import { Auth } from 'services/auth';
import { Notification } from 'services/notification';

@Component({
  selector: 'change-password',
  templateUrl: './ChangePassword.component.html',
})
export class ChangePasswordComponent {
  constructor(
    private logger: Logger,
    private auth: Auth,
    private notification: Notification,
  ) {}

  handleEvent() {
    this.notification.info('Event handled successfully');
  }
}
```

:arrow_right: https://angular.io/guide/dependency-injection

### React

There's no special injection mechanism. ES2015 modules are used for dependency management.

```jsx
import { Component } from 'react';
import Logger from 'utils/logger';
import Notification from 'utils/notification';
import Auth from 'utils/auth';

class ChangePassword extends Component {
  handleEvent = () => {
    Notification.info('Event handled successfully');
  }

  render() {
    return <div>{ /* template */ }</div>;
  }
}
```

### Vue.js

Vue.js uses ES2015 modules for dependency management:

```js
import Vue from 'vue';
import Logger from 'utils/logger';
import Notification from 'utils/notification';
import Auth from 'utils/auth';

Vue.component('change-password', {
  template: '<div>{{ /* template */ }}</div>',
  methods: {
    handleEvent() {
      Notification.info('Event handled successfully');
    },
  },
});
```

There's also [provide and inject](https://vuejs.org/v2/api/#provide-inject) mechanism which is primarily provided for advanced plugin / component library use cases:

Parent component:
```js
import Notification from 'utils/notification';
import Vue from 'vue';

new Vue({
  el: '#app',
  provide: {
    notification: Notification,
  },
});
```

Child component:
```js
import Vue from 'vue';

Vue.component('change-password', {
  inject: ['notification']
  template: '<div>{{ /* template */ }}</div>',
  methods: {
    handleEvent() {
      this.notification.info('Event handled successfully');
    }
  }
});
```

# Templates

### AngularJS

Templates in AngularJS are compiled by the [$compile](https://docs.angularjs.org/api/ng/service/$compile) service.
Values of component properties must be one of the following:
- string binding (defined as `@`)
- expression binding (defined as `<`)
- reference binding (defined as `&`)

```html
<primary-button size="big"
                disabled="true"
                click="$ctrl.saveContent()">
  Save
</primary-button>
```

### Angular

There are three kinds of possible attributes being passed to the component:
- text binding, e.g. `size="string"`
- property binding, e.g. `[disabled]="value"`
- event binding, e.g. `(click)="eventHandler()"`

```html
<primary-button size="big"
                [disabled]="true"
                (click)="saveContent()">
  Save
</primary-button>
```

### React

Templates in React are written inside the JavaScript file using the [JSX language](https://reactjs.org/docs/jsx-in-depth.html). This allows us to utilize all JavaScript capabilities. JSX uses the uppercase and lowercase convention to distinguish between the user-defined components and DOM elements.

```jsx
<PrimaryButton
  size="big"
  disabled
  onClick={ this.saveContent }
>
  Save
</PrimaryButton>;
```

### Vue.js

Component properties can be passed in as:
- literal (as strings) e.g. `size="big"`
- dynamic (using [v-bind](https://vuejs.org/v2/api/#v-bind) or [`:shorthand`](https://vuejs.org/v2/guide/syntax.html#v-bind-Shorthand) with actual values) e.g. `v-bind:disabled="true"`

Events can be listened to using [`v-on`](https://vuejs.org/v2/guide/events.html#Method-Event-Handlers) or [`@shorthand`](https://vuejs.org/v2/guide/syntax.html#v-on-Shorthand) combined with the event name, and a method name as the value, e.g `v-on:click="saveContent"`.

```html
<primary-button 
  size="big"
  v-bind:disabled="true"
  v-on:click="saveContent"
>
    Save
</primary-button>
```

:arrow_right: https://vuejs.org/v2/guide/components.html#Props

# Interpolation

### AngularJS

In AngularJS interpolation is the process of data-binding values of the `scope` to the HTML. You can also interpolate more complicated values e.g. expressions or function invocations.


```html
<img ng-src="{{ $ctrl.image.url }}" alt="{{ $ctrl.image.alt }}" />
```

We use [`ng-src`](https://docs.angularjs.org/api/ng/directive/ngSrc) instead of the regular `src` attribute so that AngularJS can set it up before the browser will try to load the image.

Another way to "bind" data is to use [`ng-bind`](https://docs.angularjs.org/api/ng/directive/ngBind). This allows us to counter the issue with a raw state being displayed before AngularJS compiles the template.

```html
<label>
    Enter name:
    <input type="text" ng-model="$ctrl.name">
</label>
<span ng-bind="$ctrl.name"></span>
```

:arrow_right: https://docs.angularjs.org/guide/interpolation

### Angular

Angular is similar to AngularJS, so we use double curly braces (`{{ }}`) for interpolation. Since Angular offers property binding you often have a choice to use it instead of interpolation. 

:arrow_right: https://angular.io/guide/template-syntax#property-binding-or-interpolation 

```html
<img [src]="image.url" alt="{{ image.alt }}" />
```

`[src]` presents property binding while the `alt` attribute is being interpolated.

:arrow_right: https://angular.io/guide/template-syntax#interpolation

### React

React uses single curly braces for interpolation. Any JavaScript can be interpolated.

```jsx
<img src={ this.props.image.url } alt={ this.props.image.alt } />;
```

:arrow_right: https://reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx

### Vue.js

```html
<img :src="image.url" alt="{{ image.alt }}" />
```

You can also perform one-time interpolations that do not update on data change by using the [v-once](https://vuejs.org/v2/api/#v-once) directive,

```html
<span v-once>Hello {{ username }}!</span>
```

:arrow_right: https://vuejs.org/v2/guide/syntax.html#Interpolations

# Inputs and Outputs

### AngularJS

AngularJs has introduced a new syntax for inputs/outputs which was back ported from Angular. The main purpose of this new syntax is to enforce the One-way dataflow pattern. Read more
in the [official documentation](https://docs.angularjs.org/guide/component#component-based-application-architecture):

```js
class UserPreviewComponent { }

const component = {
  bindings: {
    user: '<',
    onEdit: '&',
  },
  template: `
  <form ng-submit="$ctrl.onEdit();">
    <input type="text" ng-model="$ctrl.user.name">
    <input type="text" ng-model="$ctrl.user.email">
    <button type="submit">Submit</button>
  </form>
  `,
  controller: UserPreviewComponent,
};

export default module.component('user-preview', component);
```

In a parent component, i.e. `SettingsComponent`:

```js
class SettingsComponent {
  user: User;
  constructor() {
    this.user = {
      name: 'Foo Bar',
      email: 'foobar@example.com'
    }
  }
  editedUser(user: User){
    console.log('Name of the edited user is', user.name);
  }
}

const component = {
  template: `<user-preview user="user" on-edit="editedUser()"></user-preview>`,
  controller: SettingsComponent,
};

export default module.component('app-settings', component);
```

### Angular

Angular introduced a new pattern for Component interactions, this pattern follows the [Flux](https://facebook.github.io/flux/) architecture. Read more in the [official documentation](https://angular.io/guide/component-interaction).

```ts
@Component({
  selector: 'user-preview',
  template: `
  <form (ngSubmit)="emitEditedUser();">
    <input type="text" value="{{user.name}}">
    <input type="text" value="{{user.email}}">
    <button type="submit">Submit</button>
  </form>
  `
})
export class UserPreviewComponent {
  @Input() user: User;
  @Output() onEdit: EventEmitter = new EventEmitter<User>();

  emitEditedUser() {
    this.onEdit.emit(this.user);
  }
}

```

In a parent component, i.e. `SettingsComponent`:

```ts
@Component({
  selector: 'app-settings',
  template: `
    <user-preview [user]="user" (onEdit)="editedUser($event)"></user-preview>
  `
})
export class SettingsComponent {
  user: User;
  constructor() {
    this.user = {
      name: 'Foo Bar',
      email: 'foobar@example.com'
    }
  }
  editedUser(user: User){
    console.log('Name of the edited user is', user.name);
  }
}
```

### React

```jsx
import React from 'react';
import PropTypes from 'prop-types';

class UserPreviewComponent extends React.Component {
  render() {
    return (
      <form onSubmit={this.props.onEdit}>
        <input type="text" value={this.props.user.email} />
        <input type="text" value={this.props.user.name} />
        <button type="submit">Submit</button>
      </form>
    );
  }
}

UserPreviewComponent.propTypes = {
  user: PropTypes.instanceOf(User),
};
```

In a parent component, i.e. `SettingsComponent`:

```jsx
import React from 'react';

class SettingsComponent extends React.Component {
  constructor() {
    this.state = {
      user: {
        name: 'Foo Bar',
        email: 'foobar@example.com'
      }
    };
  }
  editedUser(user: User){
    console.log('Name of the edited user is', user.name);
  }
  render() {
    return (
      <UserPreviewComponent user={this.state.user} onEdit={(user) => this.editedUser(user)} />
    );
  }
}
```

Read more about React's [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html).

For communication between two components that don't have a parent-child relationship, you can set up your own global event system. Subscribe to events in `componentDidMount()`, unsubscribe in `componentWillUnmount()`, and call `setState()` when you receive an event. The [Flux](https://facebook.github.io/flux/) pattern is one of the possible ways to arrange this.

### Vue.js

> TODO

# Default inputs

### AngularJS

There's no built-in mechanism for default inputs, because of this we assign them programatically in the `$onChanges` hook.

```js
class CoursesListController {
  $onChanges(bindings) {
    if (typeof bindings.displayPurchased.currentValue === 'undefined') {
      this.displayPurchased = true;
    }
    if (typeof bindings.displayAvailable.currentValue === 'undefined') {
      this.displayAvailable = true;
    }
  }
}

const component = {
  bindings: {
    displayPurchased: '<',
    displayAvailable: '<',
  },
  templateUrl: './coursesList.component.html',
  controller: CoursesListController,
};
```

### Angular

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'courses-list',
  templateUrl: './coursesList.component.html',
})
export class CoursesListController {
  displayPurchased: boolean = true;
  displayAvailable: boolean = true;
}
```

### React

```jsx
class CoursesListController {
  static propTypes = {
    displayPurchased: PropTypes.bool,
    displayAvailable: PropTypes.bool,
  };

  static defaultProps = {
    displayPurchased: true,
    displayAvailable: true,
  };

  render() {
    return <div>{ /* template */ }</div>;
  }
}
```

:arrow_right: https://reactjs.org/docs/typechecking-with-proptypes.html#default-prop-values

### Vue.js

```js
import Vue from 'vue';

Vue.component('courses-list', {
  template: '<div>{{ /* template */ }}</div>',
  props: {
    displayPurchased: {
      type: Boolean,
      default: true,
    },
    displayAvailable: {
      type: Boolean,
      default: true,
    },
  },
});
```

# Handling Events

> TODO

# Lifecycle methods

### AngularJS

`$onInit()` - called when the component has been constructed and has its bindings initialized.

`$postLink()` - called after the component and its children have been linked (mounted).

`$onChanges(changes)` - called whenever one-way bindings are updated.

`$doCheck()` - called on each turn of the digest cycle.

`$onDestroy()` - called when the component (a controller with its containing scope) is being destroyed.

:arrow_right: https://docs.angularjs.org/api/ng/service/$compile

### React

#### Mounting

[`constructor(props)`](https://reactjs.org/docs/react-component.html#constructor) - the first method called in the lifecycle before mounting. If used, it must include `super(props)` as first call:

```jsx
constructor(props) {
  super(props);
  this.state = {
    time: new Date();
  }
}
```

[`componentWillMount()`](https://reactjs.org/docs/react-component.html#componentwillmount) - is invoked just before rendering. Modifying the state here won't trigger a re-render.

[`componentDidMount()`](https://reactjs.org/docs/react-component.html#componentdidmount) - is invoked after render. Useful for initialisations that require DOM nodes.

#### Updating

[`componentWillReceiveProps(nextProps)`](https://reactjs.org/docs/react-component.html#componentwillreceiveprops) - is only called after rendering, but before receiving new props. Because React may call this method without props changing, it is recommended to manually implement a check to see if there's a difference.

[`shouldComponentUpdate(nextProps, nextState)`](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) - this method is called before receiving new props or a change of state. By default, it returns true which means re-rendering is triggered by any change. Modifying this method allows you to only re-render in intended scenarios.

[`componentWillUpdate(nextProps, nextState)`](https://reactjs.org/docs/react-component.html#componentwillupdate) - is invoked whenever `shouldComponentUpdate` returns true before rendering. Note: You can't use `this.setState()` here.

[`componentDidUpdate(prevProps, prevState)`](https://reactjs.org/docs/react-component.html#componentdidupdate) - is invoked after rendering, but not after the initial render. This method is useful for manipulating the DOM when updated.

#### Unmounting

[`componentWillUnmount()`](https://reactjs.org/docs/react-component.html#componentwillunmount) - is invoked immediately before a component is unmounted and destroyed. Useful for resource cleanup.

#### Error Handling

[`componentDidCatch(error,info)`](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html) - is invoked when Javascript throws an error anywhere in the component's tree. Useful for catching errors, showing a fallback interface, and logging errors without breaking the entire application.

### Vue.js

#### beforeCreate

Called synchronously immediately after the instance has been initialized, but before data observation and event/watcher setup. On every Vue instance lifecycle, `this` points to the vm instance itself.

```javascript
new Vue({
  beforeCreate: function () {
    console.log('this method called before instance created')
  }
})
```

#### created

Called synchronously after the instance is created. At this stage, the instance has finished processing the options which means the following have been set up: data observation, computed properties, methods, watch/event callbacks. However, the mounting phase has not been started, and the `$el` property will not be available yet.

```javascript
new Vue({
  created: function () {
    console.log('this method called after instance created')
  }
})
```

#### beforeMount

Called right before the mounting begins: the render function is about to be called for the first time.

_This hook is not called during server-side rendering._

```javascript
new Vue({
  beforeMount: function () {
    console.log('this method called before mounting an instance')
  }
})
```

#### mounted

Called after the instance has been mounted, where [el](https://vuejs.org/v2/api/#el) is replaced by the newly created `vm.$el`. If the root instance is mounted to an in-document element, `vm.$el` will also be in-document when `mounted` is called.

Note that `mounted` does not guarantee that all child components have also been mounted. If you want to wait until the entire view has been rendered, you can use [vm.$nextTick](https://vuejs.org/v2/api/#Vue-nextTick) inside of mounted:

```javascript
new Vue({
  mounted: function () {
    this.$nextTick(function () {
      // Code that will run only after the
      // entire view has been rendered
    })
  }
})
```

#### beforeUpdate

Called whenever the data changes, before the virtual DOM is re-rendered and patched.

You can perform further state changes in this hook and they will not trigger additional re-renders.

_This hook is not called during server-side rendering._

#### updated

Called after a data change causes the virtual DOM to be re-rendered and patched.

The component’s DOM will have been updated when this hook is called, so you can perform DOM-dependent operations here. However, in most cases you should avoid changing state inside the hook.

Note that `updated` does not guarantee that all child components have also been re-rendered. If you want to wait until the entire view has been re-rendered, you can use [vm.$nextTick](https://vuejs.org/v2/api/#Vue-nextTick) inside of `updated`:

```javascript
updated: function () {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been re-rendered
  })
}
```

#### activated

Called when a kept-alive component is activated.

_This hook is not called during server-side rendering._

#### deactivated

Called when a kept-alive component is deactivated.

_This hook is not called during server-side rendering._

#### beforeDestroy

Called right before a Vue instance is destroyed. At this stage the instance is still fully functional.

_This hook is not called during server-side rendering._

#### destroyed

Called after a Vue instance has been destroyed. When this hook is called, all directives of the Vue instance have been unbound, all event listeners have been removed, and all child Vue instances have also been destroyed.

_This hook is not called during server-side rendering._

# Conditional rendering

### AngularJS

Angularjs 1.x has three ways to perform conditional rendering: `ng-if`, `ng-switch` and `ng-hide/ng-show`.

```js
export class RegistrationComponentCtrl {
  this.registrationCompleted = false;
  this.displaySpecialOffer = false;
  this.displayStatus = 'Registered';
}
```
```html
<div ng-if="displaySpecialOffer">
  <special-offer></special-offer>
</div>

<div ng-switch="displayStatus">
  <div ng-switch-when="Registered">
     <registration-completed></registration-completed>
  </div>
</div>

<div ng-show="displaySpecialOffer">
  <special-offer></special-offer>
</div>  
<div ng-hide="displaySpecialOffer">
  <special-offer></special-offer>
</div>  
```

### Angular

Since Angular 4.0.0, alongside standard `ngIf`, it is possible to use `ngIf;else` or `ngIf;then;else` using `<ng-template>` with an alias `#aliasName`.

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'registration',
  template: ``,
})
export class registrationComponent {
  registrationCompleted: boolean = false;
  displaySpecialOffer: boolean = false;
}
```

```html
<div *ngIf="displaySpecialOffer">
  <special-offer></special-offer>
</div>

<div *ngIf="registrationCompleted;else registrationForm">
  <registration-completed></registration-completed>
</div>

<ng-template #registrationForm>
  <registration-form></registration-form>
</ng-template>
```

:arrow_right: https://angular.io/api/common/NgIf

### React

The most common approach to conditional rendering is by using the ternary operator:  
`{ condition ? <Component /> : null }`

```jsx
class Registration extends React.Component {
  this.state = {
    registrationCompleted: false,
  };

  propTypes = {
    displaySpecialOffer: PropTypes.bool,
  }

  render() {
    return (
      <div>
        { this.props.displaySpecialOffer ? <SpecialOffer /> : null }

        { this.state.registrationCompleted ? (
            <RegistrationCompleted />
        ) : (
            <RegistrationForm />
        ) }
      </div>
    );
  }
}
```

### Vue.js

Vue.js has three directives to perform conditional rendering: `v-if`, `v-else-if` and `v-else`.

```html
<template>
  <section v-if="registrationCompleted && !displaySpecialOffer">
    <registration-completed />
  </section>
  <section v-else-if="registrationCompleted && displaySpecialOffer">
    <special-offer />
    <registration-completed />
  </section>
  <section v-else>
    <registration-form />
  </section>
</template>
```

:arrow_right: https://vuejs.org/v2/guide/conditional.html

# Lists

### AngularJS

[ngRepeat](https://docs.angularjs.org/api/ng/directive/ngRepeat)

```js
export class BookListComponentCtrl {
  constructor() {
    this.books = [
      {
        id: 1,
        title: 'Eloquent JavaScript',
        author: 'Marijn Haverbeke',
      },
      {
        id: 2,
        title: 'JavaScript: The Good Parts',
        author: 'Douglas Crockford',
      },
      {
        id: 3,
        title: 'JavaScript: The Definitive Guide',
        author: 'David Flanagan',
      },
    ];
  }
}
```

```html
<ul>
  <li ng-repeat="book in $ctrl.books track by book.id">
    {{ book.title }} by {{ book.author }}
  </li>
</ul>
```

### Angular

[ngFor](https://angular.io/guide/template-syntax#ngfor)

```ts
export interface Book {
  id: number;
  title: string;
  author: string;
}
```

```ts
import { Component } from '@angular/core';
import { Book } from './book.interface';

@Component({
  selector: 'book-list',
  template: `
    <ul>
      <li *ngFor="let book of books; trackBy: trackById">
        {{ book.title }} by {{ book.author }}
      </li>
    </ul>
  `
})
export class BookListComponent {
  this.books: Book[] = [
    {
      id: 1,
      title: "Eloquent JavaScript",
      author: "Marijn Haverbeke"
    },
    {
      id: 2,
      title: "JavaScript: The Good Parts",
      author: "Douglas Crockford"
    },
    {
      id: 3,
      title: "JavaScript: The Definitive Guide",
      author: "David Flanagan"
    }
  ];

  trackById(book) {
    return book.id;
  }
}
```

### React

[Lists and Keys](https://reactjs.org/docs/lists-and-keys.html)

```jsx
class BookList extends React.Component {
  constructor() {
    this.state = {
      books: [
        {
          id: 1,
          title: 'Eloquent JavaScript',
          author: 'Marijn Haverbeke',
        },
        {
          id: 2,
          title: 'JavaScript: The Good Parts',
          author: 'Douglas Crockford',
        },
        {
          id: 3,
          title: 'JavaScript: The Definitive Guide',
          author: 'David Flanagan',
        },
      ],
    };
  }

  render() {
    const { books } = this.state;

    return (
      <ul>
        { books.map(book => {
          return (
            <li key="{ book.id }">
              { book.title } by { book.author }
            </li>
          );
        }) }
      </ul>
    );
  }
}
```

### Vue.js

```html
<template>
  <ul>
    <li v-for="book in books" :key="book.id">
      {{ book.title }} by {{ book.author }}
    </li>
  </ul>
</template>
```

```js
export default {
  data() {
    return {
      books: [
        {
          id: 1,
          title: 'Eloquent JavaScript',
          author: 'Marijn Haverbeke',
        },
        {
          id: 2,
          title: 'JavaScript: The Good Parts',
          author: 'Douglas Crockford',
        },
        {
          id: 3,
          title: 'JavaScript: The Definitive Guide',
          author: 'David Flanagan',
        },
      ],
    };
  },
};
```

# Filters

### AngularJS

AngularJS provides filters to transform data. There are several [built-in filters](https://docs.angularjs.org/api/ng/filter) to use and you can make your own custom filters as well.

Filters can be applied to view templates using the following syntax:

```html
<h1>{{ price | currency }}</h1>
```

Chaining of filters is also possible:

```html
<h1>{{ name | uppercase | appendTitle  }}</h1>
```

Custom Filters:  

```js
angular.module('app', [])
  .filter('reverse', function() {
    return (input = '', uppercase = false) => {
      const out = input.split('').reverse().join('');

      return uppercase ? out.toUpperCase() : out;
    };
  });
```

:arrow_right: https://docs.angularjs.org/guide/filter

### Angular

In Angular filters are called [pipes](https://angular.io/guide/pipes). Built-in pipes available in Angular are: DatePipe, UpperCasePipe, LowerCasePipe, CurrencyPipe, and PercentPipe.

Apart from the built-in pipes, you can create your own, custom pipes.

Create a custom pipe:
this pipe transforms a given URL to a safe style URL, that way it can be used in hyperlinks, for example <img src> or <iframe src>, etc..

```ts
import { Pipe, PipeTransform } from '@angular/core';
import { DomSanitizer} from '@angular/platform-browser';

@Pipe({ name: 'safe' })
export class SafePipe implements PipeTransform {
  constructor(public sanitizer: DomSanitizer) {}
  transform(url) {
    return this.sanitizer.bypassSecurityTrustResourceUrl(url);
  }
}
```

Use the custom pipe in a template:
The given `someUrl` is filtered through the `safe` pipe which transforms it trough the `DomSanitizer` function `bypassSecurityTrustResourceUrl`. More on the DomSanitizer [here](https://angular.io/api/platform-browser/DomSanitizer).

```html
  <iframe [src]="someUrl | safe"></iframe>
```

Note: `[src]` above is an input to the component which 'lives' above the `iframe`.

:arrow_right: https://angular.io/guide/pipes

### React

React doesn't provide any specific filtering mechanism. This can simply be achieved by using ordinary JavaScript functions:

```jsx
export function reverse(input = '', uppercase = false) {
  const out = input.split('').reverse().join('');

  return uppercase ? out.toUpperCase() : out;
}
```

```jsx
import { reverse } from 'utils';

export class App extends Component {
  render() {
    return (
      <div>
        { reverse(this.props.input) }
      </div>
    );
  }
}
```

Filter chaining can be achieved using function composition:

```jsx
<div>
  { truncate(reverse(this.props.input)) }
</div>;
```

### Vue.js

Vue.js provides filters to allow for simple text formatting. The filter utilizes the `|` character which is appended to the expression followed by the filter's name. Vue does not come with any pre-built filters.

Filters are usable within mustache interpolations:

```html
<h1>{{ name | lowercase }}</h1>
```

Filters can also be used within the `v-bind` directive:

```html
<div v-bind:slug="slug | formatSlug"></div>
```

When creating filters, the function always receives the expression's value:

```js
new Vue({
  el: '#app',
  template: '<p> {{ message | lowercase }} </p>',
  filters: {
    lowercase(word) {
      return word.toLowerCase();
    },
  },
  data: {
    message: 'Hello World',
  },
});
```

Filters can also be chained:

```html
<p>{{ description | lowercase | truncate }}</p>
```

Filters can be created locally like the above example and only be available within that component. Filters can also be declared globally:

```js
Vue.filter('lowercase', word => word.toLowerCase());
```

For global filters to work, they should be declared before the Vue instance.

:arrow_right: https://vuejs.org/v2/guide/filters.html

# Child nodes

### AngularJS

Inside a [component](https://docs.angularjs.org/guide/component), we have access to the child node by injecting `$element` to the controller. This object contains a [jqLite](https://docs.angularjs.org/api/ng/function/angular.element) wrapped instance of the DOM element. Accessing `$element[0]` will return the bare DOM element.

Transclusion is also supported - using `ng-transclude` (See [Transclusion and Containment](#transclusion-and-containment) section).

```js
class TextInputController {
  constructor($element) {
    'ngInject';

    this.$element = $element;
  }

  // The $element can be used after the link stage
  $postLink() {
    const input = this.$element.find('input');
    input.on('change', console.log);
  }
}

const component = {
  controller: TextInputController,
  template: `
    <div>
      <input type="text" />
    </div>
  `,
};
```

### Angular

Angular provides two ways to deal with child nodes: `ViewChild` and `ContentChild`. They both have the same purpose, but there are different use cases for them.

* [`ViewChild`](https://angular.io/api/core/ViewChild) works with the **internal DOM of your component**, defined by you in the component's template. You have to use the `@ViewChild` decorator to get the DOM element reference.

* [`ContentChild`](https://angular.io/api/core/ContentChild) works with de **DOM supplied to your component by its end-user** (See [Transclusion and Containment](#transclusion-and-containment)). You have to use the `@ContentChild` decorator to get the DOM element reference.

```ts
import {
  Component,
  Input,
  ViewChild,
  ContentChild,
  AfterViewInit,
  AfterContentInit
} from '@angular/core';

@Component({
  selector: 'child',
  template: `
    <p>Hello, I'm your child #{{ number }}!</p>
  `
})
export class Child {
  @Input() number: number;
}

@Component({
  selector: 'parent',
  template: `
    <child number="1"></child>
    <ng-content></ng-content>
  `
})
export class Parent implements AfterViewInit, AfterContentInit {
  @ViewChild(Child) viewChild: Child;
  @ContentChild(Child) contentChild: Child;

  ngAfterViewInit() {
    // ViewChild element is only avaliable when the
    // ngAfterViewInit lifecycle hook is reached.
    console.log(this.viewChild);
  }

  ngAfterContentInit() {
    // ContentChild element is only avaliable when the
    // ngAfterContentInit lifecycle hook is reached.
    console.log(this.contentChild);
  }
}

@Component({
  selector: 'app',
  template: `
    <parent>
      <child number="2"></child>
      <child number="3"></child>
    </parent>
  `
})
export class AppComponent { }
```

`ViewChild` and `ContentChild` only work with a **single** DOM element. You can use [`ViewChildren`](https://angular.io/api/core/ViewChildren) and [`ContentChildren`](https://angular.io/api/core/ContentChildren) in order to get **multiple elements**. Both return the elements wrapped in a [`QueryList`](https://angular.io/api/core/QueryList).

### React

In React, we have two options to deal with child nodes: [`refs`](https://reactjs.org/docs/refs-and-the-dom) and [`children`](https://reactjs.org/docs/jsx-in-depth.html#children-in-jsx). With `refs`, you have access to the real DOM element. The `children` property lets you manipulate the underlying [React elements](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html).

#### refs

`ref` is a special attribute we can pass to a React element that receives a callback and call it with the corresponding DOM node.

```jsx
// In order to access child nodes from parents, we can pass the `ref` callback
// to the children as props.
const TextInput = ({ inputRef }) => (
  <div>
    <input ref={inputRef} type="text" />
  </div>
);

class Parent extends React.Component {

  componentDidMount() {
    // Refs are only executed after mounting and unmounting. Now `this.textInput`
    // references a real DOM node. So, we can use the raw DOM API
    // (to focus the input, for example)
    this.textInput.focus();
  }

  render() {
    // The child's `inputRef` prop receives the `ref` callback.
    // We can use the callback to store the DOM element in an instance variable.
    return (
      <div>
        <label>This is my child: </label>
        <TextInput
          inputRef={node => { this.textInput = node; }} />
      </div>
    );
  }
}

```

#### children

`children` is a special prop avaliable in all React component instances. You can use it to control _how_ and _where_ the underlying React elements will be rendered.

```jsx
// children is just a prop. In this case, the value of `children` will be
// what you pass to the <Heading /> component as a child node.
const Heading = ({ children }) => (
  <h1 className="Heading">
    {children}
  </h1>
);

// `this.props.children` refers to whatever is a valid node inside the <Layout /> element.
class Layout extends React.Component {
  render() {
    return (
      <div class="Layout">
        {this.props.children}
      </div>
    );
  }
}

const App = () => (
  <div>
    <Heading>I am the child!</Heading>
    <Layout>
      We are
      {'the'}
      <strong>Children!</strong>
    </Layout>
  </div>
);
```

### Vue.js

> TODO

# Transclusion and Containment

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
    template: '<div>Some content</div>',
  }).component('pageFooter', {
    template: '<footer>Some content</footer>',
  });
```
```html
<layout theme="dark">
  <page-content></page-content>
  <page-footer></page-footer>
</layout>
```

### Angular

```ts
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
</Layout>;
```

### Vue.js

> TODO

## Multiple slots

### AngularJS

```js
angular.module('app.layout', [])
  .component('landingSection', {
    bindings: {},
    controller: LandingSectionController,
    transclude: {
      contentSlot: '?content', // '?' indicates an optional slot
      iconSlot: '?icon',
    },
    template: `
    <div>
      <span ng-transclude="contentSlot"></span>
      <div>
        <span ng-transclude="iconSlot">This is the default value</span>
      </dev>
    </div>
  `,
  }).component('pageContent', {
    template: '<div>Some content</div>',
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

> TODO

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
    footer: <Footer />,
  }}
</Layout>;
```

### Vue.js

> TODO

# Class toggling

### AngularJS

The [ng-class](https://docs.angularjs.org/api/ng/directive/ngClass) directive allows you to dynamically set CSS classes on an HTML element.

```html
<main-header
  ng-class="{ 'mainNavbar--fluid': $ctrl.isFluid }">
...
</main-header>
```

### Angular

In Angular, the [ngClass](https://angular.io/guide/ajs-quick-reference#ngclass) directive works similarly. It includes/excludes CSS classes based on an expression.

```html
<main-header
  [ngClass]="{ 'mainNavbar--fluid': isFluid }">
...
</main-header>
```

### React
> TODO

### Vue.js
> TODO

# Data binding

### Vue.js
You can use the `v-model` directive to create **two-way data bindings** on form `input` and `textarea` elements. It automatically picks the correct way to update the element based on the input type. Although a bit magical, `v-model` is essentially syntax sugar for updating data on user input events, plus special care for some edge cases.

> TODO

# Forms

### AngularJS

```js
class SignInController {
  constructor(Auth) {
    'ngInject';

    this.Auth = Auth;
  }

  $onInit() {
    this.email = '';
    this.password = '';
  }

  submit() {
    Auth.signIn(this.email, this.password);
  }
}

```

```html
<form name="$ctrl.form">
  <label>
    Email:
    <input type="text" ng-model="$ctrl.email" />
  </label>

  <label>
    E-mail:
    <input type="email" ng-model="$ctrl.password" />
  </label>
  <input type="submit" ng-click="$ctrl.submit()" value="Save" />
</form>
```

### Angular

Angular offers two ways to build forms:

* [Reactive forms](https://angular.io/guide/reactive-forms)
* [Template-driven forms](https://angular.io/guide/forms#template-driven-forms)

The former uses a reactive (or model-driven) approach to build forms. The latter allows you to build forms by writing templates in the Angular template syntax with the form-specific directives and techniques.

#### Reactive forms example

```ts
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder } from '@angular/forms';

@Component({
  selector: 'reactive-form',
  template: `
    <div>
      <form [formGroup]="form"
            (ngSubmit)="onSubmit(form.value, form.valid)"
            novalidate>
        <div>
          <label>
            Name:
            <input type="text" formControlName="name">
          </label>
        </div>
        <div>
          <label>
            Email:
             <input type="email" formControlName="email">
          </label>
        </div>
      </form>
    </div>
  `
})

export class ReactiveFormComponent implements OnInit {
  public form: FormGroup;

  constructor(private formBuilder: FormBuilder) { }

  ngOnInit() {
    this.form = this.formBuilder.group({name: [''], email: ['']});
  }
}
```
#### Template-driven forms example

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'template-driven-form',
  template: `
    <div>
      <form (ngSubmit)="onSubmit()" #templateDrivenForm="ngForm" novalidate>
        <div>
          <label>
            Name:
            <input type="text" [(ngModel)]="model.name" required>
          </label>
        </div>
        <div>
          <label>
            Email:
            <input type="email" [(ngModel)]="model.email" required>
          </label>
        </div>
        <button type="submit" [disabled]="!templateDrivenForm.form.valid">Submit</button>
        </form>
    </div>
  `
})

export class TemplateDrivenFormComponent {
  public model = { name: '', email: '' };
}
```

The `novalidate` attribute in the `<form>` element prevents the browser from attempting native HTML validations.

### React

Two techniques exists in React to handle form data i.e. [Controlled Components](https://reactjs.org/docs/forms.html#controlled-components) and [Uncontrolled Components](https://reactjs.org/docs/uncontrolled-components.html). A controlled component keeps the input's value in the state and updates it via `setState()`. While in an uncontrolled component, form data is handled by DOM itself and referenced via `ref`. In most cases, it is recommended to use controlled components.

```js
import React from 'react';

export default class ReactForm extends React.Component{
  this.state = {
    email: '',
    password:'',
  }

  handleChange = ({ name, value}) => {
    if (name === 'email') {
      this.setState({ email: value })
    } else if (name === 'password') {
      this.setState({ password: value })
    }
  }

  render() {
    return (
      <form>
        <label>
          Email:
          <input
            name="email"
            type="email"
            value= {this.state.email }
            onChange={ this.handleChange }
          />
        </label>
        <label>
          Password:
          <input
            name="password"
            type="password"
            value={ this.state.password }
            onChange={ this.handleChange }
          />
        </label>
      </form>
    )
  }
}
```

:arrow_right: https://reactjs.org/docs/forms.html

### Vue.js

```html
<template>
  <form v-on:submit.prevent="onSubmit">
   <label>
       Email:
      <input type="email" v-model="email">
   </label>
   <label>
     Password:
     <input type="password" v-model="password" />
   </label>
   <button type="submit">Send</button>
  </form>
</template>

<script>
import Auth form './util/auth.js';

export default {
  data() {
    return {
      email: '',
      password: ''
    }
  },
  methods: {
    onSubmit() {
      Auth.signIn(this.email, this.password);
    }
  }
}
</script>
```

:arrow_right: https://vuejs.org/v2/guide/forms.html

# Styling

### AngularJS

Generally you will use a preprocessor (e.g. [Sass](http://sass-lang.com/)) and assign appropriate classes to the elements.

The [`ng-style`](https://docs.angularjs.org/api/ng/directive/ngStyle) directive allows you to set custom CSS styles dynamically.

```js
class HeaderController {
  constructor(ThemeProvider) {
    'ngInject';

    this.ThemeProvider = ThemeProvider;
    this.headerStyles = {};
  }

  $onInit() {
    this.headerStyles.color = ThemeProvider.getTextPrimaryColor();
  }
}
```

```html
<h1 ng-style="$ctrl.headerStyles"
    class="Header">
    Welcome
</h1>
```

### Angular

When defining Angular components you may also include the CSS styles that will go with the template. By default the styles will be compiled as shadow DOM, which basically means you don't need any namespacing strategy for CSS classes.

The [`ngStyle`](https://angular.io/api/common/NgStyle) directive allows you to set custom CSS styles dynamically.

```ts
@Component({
  selector: 'ng-header',
  template: require('./header.html'),
  styles: [require('./header.scss')],
})
export class HeaderComponent {
  headerStyles = {};

  constructor(private themeProvider: ThemeProvider) {}

  ngOnInit() {
    this.headerStyles.color = ThemeProvider.getTextPrimaryColor();
  }
}
```

```html
<h1 [ngStyle]="headerStyles"
    class="Header">
    Welcome
</h1>
```

```scss
.Header {
    font-weight: normal;
}
```

:arrow_right: https://angular.io/guide/component-styles

### React

In the React community there are many approaches to styling your app. From traditional preprocessors (like in the Angular world) to so-called [CSS in JS](https://github.com/MicheleBertoli/css-in-js). The most popular include:

* [css-modules](https://github.com/gajus/react-css-modules)
* [styled-components](https://github.com/styled-components/styled-components)
* [aphrodite](https://github.com/Khan/aphrodite)

To dynamically apply styles you can directly pass an object to the [style](https://reactjs.org/docs/dom-elements.html#style) attribute.

```jsx
export class HeaderComponent {
  state = {
    color: null,
  };

  componentDidMount() {
    this.setState({
      color: ThemeProvider.getTextPrimaryColor();
    })
  }

  render() {
    return (
      <h1 styles={ { color: this.state.color } }>
        Welcome
      </h1>
    )
  }
}
```

### Vue.js

When using [Single File Components](https://vuejs.org/v2/guide/single-file-components.html) you can simply style a component inside the `<style>` tag. When the tag has the `scoped` attribute, its CSS will apply to elements of the current component only.

To bind styles dynamically you can use the [`v-bind:style`](https://vuejs.org/v2/guide/class-and-style.html#Binding-Inline-Styles) directive.

```html
<template>
  <h1 class="Header"
    v-bind:style="headerStyles">
     Welcome
  </h1>
</template>

<script>
const ThemeProvider = require('./utils/themeProvider');

module.exports = {
  data() {
    return {
      headerStyles: {
        color: null,
      }
    };
  },
  created() {
    this.headerStyles.color = ThemeProvider.getTextPrimaryColor();
  },
};
</script>

<style scoped>
  .Header {
    font-weight: normal
  }
</style>
```

# Inject HTML template

Also known as innerHTML.

### AngularJS

By default, the HTML content will be sanitized using the [$sanitize](https://docs.angularjs.org/api/ngSanitize/service/$sanitize) service. To utilize this functionality, you need to include `ngSanitize` in your module's dependencies. [Read more](https://docs.angularjs.org/api/ng/directive/ngBindHtml)

```html
<p ng-bind-html="$ctrl.article.content"></p>
```

### Angular

The values are automatically sanitized before displaying them using [DomSanitizer](https://angular.io/docs/ts/latest/api/platform-browser/index/DomSanitizer-class.html)

```html
<p [innerHTML]="article.content"></p>
```

### React

All string values are sanitized before being inserted into the DOM. No more details are currently available.
You need to pass an object containing the `__html` property with the desired template contents.

```jsx
<p dangerouslySetInnerHTML={{__html: article.content}} />;
```

### Vue.js

The content passed to [`v-html`](https://vuejs.org/v2/guide/syntax.html#Raw-HTML) is not being [sanitized](https://github.com/vuejs/vue/issues/6333). You have to use an external library, e.g. [sanitize-html](https://www.npmjs.com/package/sanitize-html).

```html
<div v-html="article.content"></div>
```
