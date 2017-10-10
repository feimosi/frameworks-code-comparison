<h1 align="center">Frameworks code comparison</h1>

<div align="center">

![./assets/logos/angular.svg](./assets/logos/angular.svg)
![./assets/logos/react.svg](./assets/logos/react.svg)
![./assets/logos/vue.svg](./assets/logos/vue.svg)

</div>

Comparison of different approaches in writing web applications. Based on React, Angular, AngularJS and Vue.js. Useful when migrating between frameworks or switching projects often.

All examples follow the current best practices and conventions inside the given framework community. Angular code is written in TypeScript.

### :warning: **Work in progress** :warning:

### :exclamation: PRs and Feedback are welcome :exclamation:

> Note regarding framework naming:
> - AngularJS refers to Angular v1.x
> - Angular refers to Angular v2+
>
> See: http://angularjs.blogspot.com/2017/01/branding-guidelines-for-angular-and.html

<h1 align="center">Table of contents</h1>

  * [Table of contents](#table-of-contents)
  * [Simple component](#simple-component)
  * [Default inputs](#default-inputs)
  * [Dependency injection](#dependency-injection)
  * [Templates](#templates)
  * [Interpolation](#interpolation)
  * [Filters](#filters)
  * [Inputs and Outputs](#inputs-and-outputs)
  * [Forms](#forms)
  * [Handling Events](#handling-events)
  * [Lifecycle methods](#lifecycle-methods)
  * [Lists](#lists)
  * [Child nodes](#child-nodes)
  * [Transclusion and Containment](#transclusion-and-containment)
  * [Styling](#styling)
  * [Inject HTML template](#inject-html-template)

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

// Object describing the component
const component = {
    bindings: {},
    template,
    controller: ChangePasswordController,
};

// Initialize the component in Angular's module
const module = angular.module('app.changePassword', [])
.component('changePassword', component);

```

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

Every component has to be declared inside a module, in order to be used within this module's other components.

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

### React

```jsx
import Logger from 'utils/logger'
import Notification from 'utils/notification'
import Auth from 'actions/auth'

class ChangePassword {
  state = {
    password: ''
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

### Vue.js

```js
import Vue from 'vue';
import Logger from 'utils/logger';
import Notification from 'utils/notification';
import Auth from 'actions/auth';

Vue.component('change-password', {
  template: '<div>{{ /* template */ }}</div>'
  data() {
      return {
        password: ''
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
      }
  }
});
```

# Default inputs

### AngularJS

There's no built-in mechanism for default inputs, so we assign them programatically in the `$onChanges` hook.

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
            default: true
        },
        displayAvailable: {
            type: Boolean,
            default: true
        }
    }
});
```

# Dependency injection

### AngularJS

Constructor is used to inject dependencies, what is done implicitly by the [$inject](https://docs.angularjs.org/api/auto/service/$injector) service.

`'ngInject'` annotation has been used which allows automatic method annotation by the ng-annotate plugin (e.g. [ng-annotate-loader](https://www.npmjs.com/package/ng-annotate-loader) for Webpack). That's essentially needed to counter minification problems.

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

### Angular

You specify the definition of the dependencies in the constructor (leveraging TypeScript's constructor syntax for declaring parameters and properties simultaneously).

```ts
import { Component } from '@angular/core';
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

There's no special injection mechanism. For dependency management, ES2015 modules are used.

```jsx
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

Values on component inputs must be one of the following.
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

There are three kinds of possible attributes being passed:
- text binding, e.g. size="string"
- property binding, e.g. [disabled]="value"
- event binding, e.g. (click)="eventHandler()"

```html
<primary-button size="big"
                [disabled]="true"
                (click)="saveContent()">
  Save
</primary-button>
```

### React

Templates in React are written inside the JavaScript file using the [JSX language](https://reactjs.org/docs/jsx-in-depth.html). This allows us to utilize the full JavaScript capabilities. JSX uses the uppercase vs. lowercase convention to distinguish between the user-defined components and DOM tags.

```jsx
<PrimaryButton
  size="big"
  disabled
  onClick={this.saveContent}
>
  Save
</PrimaryButton>
```

### Vue.js

Component props can be passed in as:
* literal (using strings) e.g. `size="big"`
* dynamic (using v-bind with actual values) e.g. `v-bind:disabled="true"`

:arrow_right: https://vuejs.org/v2/guide/components.html#Literal-vs-Dynamic

Events can be listened to using `v-on` combined with the event name, and a method name as the value, e.g `v-on:click="saveContent"`.

:arrow_right: https://vuejs.org/v2/guide/events.html#Method-Event-Handlers
```html
<primary-button size="big"
                v-bind:disabled="true"
                v-on:click="saveContent">
    Save
</primary-button>
```

# Interpolation

### AngularJS

Interpolation is the process of data-binding values on the AngularJS `scope` to values in the HTML. You can read more on the [official documentation](https://docs.angularjs.org/guide/interpolation):

Let's say we have a value `heroImageUrl` on our scope that is defined as `superman.jpg`. (We use `ng-src` here instead of the regular `src` attribute so that Angular can set it up. If you just you source, the browser will try to load the image before Angular has a chance to interpolate.)

The following HTML

```html
<p><img ng-src="images/{{heroImageUrl}}"> is the <i>interpolated</i> image.</p>
```

will render to

```html
<p><img ng-src="images/superman.jpg"> is the <i>interpolated</i> image.</p>
```

You can interpolate more complicated values within the curly braces. For example, `{{ getVal() }}` will interpolate to the return value of the function `getVal`.

Another way to "bind" data is to use `ng-bind`.

```html
<label>Enter name: <input type="text" ng-model="name"></label><br>
Hello <span ng-bind="name"></span>!
```
In this example, whatever is typed into the input will be placed on the `name` variable on the scope. The `ng-bind` will cause the content of the span to be updated to be the value of `name`. whenever the input changed. See the full example [here](https://docs.angularjs.org/api/ng/directive/ngBind).

### Angular

Angular is similar to AngularJS. You can read more on the [official documentation](https://angular.io/guide/template-syntax#interpolation----).

`{{color}}` Will still interpolate to `red`.

However, Angular has the topic of choosing Property Binding or Interpolation (read more here [official documentation](https://angular.io/guide/template-syntax#property-binding-or-interpolation)).  

Say you have a `heroImageUrl` that you wish to set.

```html
<p><img src="images/{{heroImageUrl}}"> is the <i>interpolated</i> image.</p>
<p><img [src]="'images/' + heroImageUrl"> is the <i>property bound</i> image.</p>
```

Both of these methods will work the same. Angular suggest to pick whichever your team picks as its coding style.

The property bound style is analogous to the `ng-bind` strategy used above.

However, when setting an element property to a non-string data value, you must use property binding.


### React

Unlike Angular, React uses single curly braces. It does not support variable interpolation inside an attribute value, but anything in the curly braces is just javascript.

For example (taken from [here](https://stackoverflow.com/questions/21668025/react-jsx-access-props-in-quotes))

The following will not work:
```html
<p><img src="images/{this.props.heroImageUrl}" /></p>
```

But this will
```jsx
<p><img src={"images/" + this.props.heroImageUrl"} /></p>
```

Or if you wish to use ES6 string interpolation
```jsx
<p><img src={`images/${this.props.heroImageUrl}`} /></p>
```

There is no specific official documentation for interpolation in React, but you can read about embedding expressions [here](https://reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx).

# Filters

### AngularJS

AngularJS provides filters to transform data. There are several [built-in filters](https://docs.angularjs.org/api/ng/filter) to use or you can make your own custom filters as well.

Filters can be applied to view template using the following syntax:

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

Apart from built-in, you can create your own, custom pipes.

Create custom pipe:
This pipe transforms given URL to safe style URL, so it can be used in hyperlinks, <img src> or <iframe src>, etc..

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

Use custom pipe in the template:
Pipes given `someUrl` through `safe` pipe and transforms it over the `DomSanitizer` function `bypassSecurityTrustResourceUrl`. More on DomSanitizer [here](https://angular.io/api/platform-browser/DomSanitizer)

```html
  <iframe [src]="someUrl | safe"></iframe>
```

Note `[src]` above is an input to the component where aboves `iframe` 'lives'.

:arrow_right: https://angular.io/guide/pipes

### React

React doesn't provide any specific filters mechanism. This can simply be achieved by using ordinary JavaScript functions.

```jsx
export function reverse(input = '', uppercase = false) {
    const out = input.split('').reverse().join('');

    return uppercase ? out.toUpperCase() : out;
}
```

```jsx
import { reverse } from 'utils'

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

Filters chaining can be achieved using function composition.

```jsx
<div>
    { truncate(reverse(this.props.input)) }
</div>
```

### Vue.js

Vue.js provides filters to allow for simple text formatting. The filter utilizes the `|` character which is appended to the expression followed by the filter's name. Vue does not come with any pre-built filters.

Filters are usable within mustache interpolations.

```html
<h1>{{ name | lowercase }}</h1> 
```

Filters can also be used within the `v-bind` directive.

```html
<div v-bind:slug="slug | formatSlug"></div>
```

When creating filters, the function always receives the expression's value. 

```js
new Vue({
  el: '#app',
  template: '<p> {{ message | lowercase }} </p>',
  filters: {
    lowercase(word) {
      return word.toLowerCase();
    }
  },
  data: {
    message: 'Hello World'
  },
});
```

Filters can also be chained.

```html
<p>{{ description | lowercase | truncate }}</p>
```

Filters can be created locally like the above example and only be available within that component. Filters can also be declared globally.

```js
Vue.filter('lowercase', word => word.toLowerCase());
``` 

For global filters to work, they should be declared before the Vue instance.

:arrow_right: https://vuejs.org/v2/guide/filters.html

# Inputs and Outputs

### AngularJS

AngularJs has introduced a new syntax for inputs/outputs which was back ported from Angular. The main purpose of this new syntax is to enforce the One-way dataflow pattern. Read more
on the [official documentation](https://docs.angularjs.org/guide/component#component-based-application-architecture):

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

In a parent component, ie `SettingsComponent`:

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

Angular introduced a new pattern for Component interactions, this pattern follows the [Flux](https://facebook.github.io/flux/) architecture. Read more on [the official documentation](https://angular.io/guide/component-interaction).

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

In a parent component, ie `SettingsComponent`:

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
      <form  onSubmit={this.props.onEdit}>
        <input type="text" value={this.props.user.email} />
        <input type="text" value={this.props.user.name} />
        <button type="submit">Submit</button>
      </form>
    );
  }
}

UserPreviewComponent.propTypes = {
  user: PropTypes.instanceOf(User)
};
```

In a parent component, ie `SettingsComponent`:

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

For communication between two components that don't have a parent-child relationship, you can set up your own global event system. Subscribe to events in componentDidMount(), unsubscribe in componentWillUnmount(), and call setState() when you receive an event. [Flux](https://facebook.github.io/flux/) pattern is one of the possible ways to arrange this.

# Forms

### Angular

Angular offers two ways to build form: [reactive form](https://angular.io/guide/reactive-forms) and template-driven form. The former demoed below, allows us to listen to form or control changes.

```ts
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder } from '@angular/forms';
@Component({
  selector: 'reactive-form',
  template: `
    <div>
    <form [formGroup]="form" (ngSubmit)="onSubmit(form.value, form.valid)" novalidate>
      <div>
        <label>Name:</label>
        <input type="text" formControlName="name">
      </div>
      <div>
        <label>Email:</label>
        <input type="text" formControlName="email">
      </div>
    </form>
    <div>Form details: <pre>form value: <br>{{form.value | json}}</pre></div>
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

## Validation

## Data-binding

## Submitting

# Handling Events

> TODO

# Lifecycle methods

### React

#### Mounting

`constructor(props)` - the first method called in the lifecycle before mounting. If used, it must include super(props) as the first call:

```jsx
constructor(props) {
  super(props);
  this.state = {
    time: new Date();
  }
}
```

`componentWillMount()` - is invoked just before render. Modifying the state here won't trigger a re-render.

`componentDidMount()` - is invoked after render. Useful for initialisations that require DOM nodes.

#### Updating

`componentWillReceiveProps(nextProps)` - is only called after rendering, but before receiving new props. Because React may call this method although props stay the same. It is recommended to manually implement a check to see if there's a difference.

`shouldComponentUpdate(nextProps, nextState)` - the method is called before receiving new props or state. By default, it returns true which means render is triggered by any change. Modifying this method allows you to only re-render in intended scenarios.

`componentWillUpdate(nextProps, nextState)` - is invoked if shouldComponentUpdate returns true before render. Note: You can't use this.setState() here.

`componentDidUpdate(prevProps, prevState)` - is invoked after render, but not after the initial one. This method is useful for manipulating the DOM when updated.

#### Unmounting

`componentWillUnmount()` - is invoked immediately before a component is unmounted and destroyed. Useful for resource cleanup.

# Lists

### AngularJS

[ngRepeat](https://docs.angularjs.org/api/ng/directive/ngRepeat)

```js
export class BookListComponentCtrl {
  constructor() {
    this.books = [
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
      ]
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
      ]
    }
  }
};
```

# Child nodes

### AngularJS

Inside a [component](https://docs.angularjs.org/guide/component), we have access to the child node by injecting `$element` to the controller. It has [jqLite](https://docs.angularjs.org/api/ng/function/angular.element) wrapped instance of the DOM element. Also, accessing `$element[0]` will return the bare DOM element.

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

`ViewChild` and `ContentChild` work with **only one** DOM element. You can use [`ViewChildren`](https://angular.io/api/core/ViewChildren) and [`ContentChildren`](https://angular.io/api/core/ContentChildren) in order to get **multiple elements**. Both return the elements wrapped in a [`QueryList`](https://angular.io/api/core/QueryList).

### React

In React, we have two options to deal with child nodes: [`refs`](https://reactjs.org/docs/refs-and-the-dom) and [`children`](https://reactjs.org/docs/jsx-in-depth.html#children-in-jsx). With `refs`, you have access to the real DOM element. The `children` property let you manipulate the underlying [React elements](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html).

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
    )
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
        <span ng-transclude="iconSlot">This is the default value</span>
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
  footer: <Footer />
}}
</Layout>
```

# Styling

### AngularJS

Generally you will use some preprocessor (e.g. [Sass](http://sass-lang.com/)) and assign appropriate classes to the elements.

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

When defining Angular component you may also include the CSS styles that will go with the template. By default the styles will be compiled as shadow DOM, which basically means you don't need any namespacing strategy for CSS classes.

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

In React community there are many approaches to styling your app. From traditional preprocessors (like in the Angular world) to so-called [CSS in JS](https://github.com/MicheleBertoli/css-in-js). The most popular include:

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

aka. innerHTML

### AngularJS

By default, the HTML content will be sanitized using the [$sanitize](https://docs.angularjs.org/api/ngSanitize/service/$sanitize) service. To utilize this functionality, you need to include `ngSanitize` in your module's dependencies. [Read more](https://docs.angularjs.org/api/ng/directive/ngBindHtml)

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

### Vue.js

The content passed to [`v-html`](https://vuejs.org/v2/guide/syntax.html#Raw-HTML) is not being [sanitized](https://github.com/vuejs/vue/issues/6333). You have to use an external library, e.g. [sanitize-html](https://www.npmjs.com/package/sanitize-html)

```html
<div v-html="article.content"></div>
```
