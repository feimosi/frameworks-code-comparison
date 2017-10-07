<h1 align="center">Frameworks code comparison</h1>

Comparison of different approaches in writing web applications. Based on React, Angular, AngularJS and Vue.js. Useful when migrating between frameworks or switching projects often.

All examples follow the current best practises and conventions inside the given framework community. Angular code is written in TypeScript.

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

```js
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

### VueJs

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

# Interpolation

> TODO

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
    let out = input.split('').reverse().join('');

    if (uppercase) {
      out = out.toUpperCase();
    }

    return out;
  };
});
```

:arrow_right: https://docs.angularjs.org/guide/filter

### Angular

In Angular filters are called pipes.
Built-in pipes available in Angular: DatePipe, UpperCasePipe, LowerCasePipe, CurrencyPipe, and PercentPipe.
More about pipes in Angular [here](https://angular.io/guide/pipes)

Apart from built in, you can create your own, custom pipes.

Create custom pipe:
This pipe transforms given URL to safe style URL, so it can be used in hyperlinks, <img src> or <iframe src>, etc..

```js
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

```js
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

```js
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

```js
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

Angular offers two ways to build form: [reactive form](https://angular.io/guide/reactive-forms) and template-driven form. The former, demoed below, allows us to listen to form or control changes.

```js
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

## model-options

## submit

# Handling Events

> TODO

# Lifecycle methods

### React

#### Mounting

`constructor(props)` - the first method called in the lifecycle before mounting. If used, it must include super(props) as first call:

```js
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

```js
export interface Book {
  id: number;
  title: string;
  author: string;
}
```

```js
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

```js
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

# Child nodes

> TODO
> ViewChild vs refs
> ContentChild vs this.props.children

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

> TODO

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
