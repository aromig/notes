# Learning Vue.js
###### (personal notes from vuejs.org rewritten for my understanding)

Vue is a progressive framework for building user interfaces. The core library is focused on the view layer (of MVVM/MVC) only.

For learning use: `<script src="https://unpkg.com/vue"></script>`

## Installing Vue
(later)

---
---

## Declarative Rendering

Vue enables rendering of data to the DOM using a straightforward template syntax.

```html
<div id="app">
    {{ message }}
</div>
```
```javascript
var app = new Vue({
    el: '#app',
    data: {
        message: 'Hello World'
    }
})
```

This renders the string 'Hello World' within the #app div. The data and the DOM are now linked, and everything is now reactive. i.e.
```javascript
app.message = 'Look at me - I changed!';
```

---

### Binding element attributes

Directives - Prefixed with v-  to indicate that they are special attributes provided by Vue.

```html
<div id="app-2">
    <span v-bind:title="message">
        My title is now linked to some js witchcraft.
    </span>
</div>
```
```javascript
var app2 = new Vue({
    el: '#app-2',
    data: {
        message: 'Abracadabra!'
    }
})
```

So element attribute values can be updated along with element content.

```javascript
app2.message = 'Hocus Pocus';
```

---

### Conditionals and Loops

#### v-if


```html
<div id="app-3">
    <span v-if="seen">Now you see me</span>
</div>
```
```javascript
var app3 = new Vue({
    el: '#app-3',
    data: {
        seen: true;
    }
})
```

Since `seen` is set to true, it will render 'Now you see me' however if `seen` gets set to false, it will disappear!

Vue can also apply [transition effects](https://vuejs.org/v2/guide/transitions.html) when elements are updated as well.

#### v-for

```html
<div id="app-4">
    <ol>
        <li v-for="todo in todos">
            {{ todo.text }}
        </li>
    </ol>
</div>
```
```javascript
var app4 = new Vue({
    el: '#app-4',
    data: {
        todos: [
            { text: 'Learn JavaScript' },
            { text: 'Learn Vue' },
            { text: 'Build something awesome' }
        ]
    }
})
```

This renders an ordered list with the above values as list items. The list can be manipulated since it's an array.

```javascript
app4.todos.push({ text: '... Profit!' }) // Appends an item
app4.todos.pop(); // Removes last item
```

---

### Handling User Input

#### v-on

The v-on directive can be used to attach event listeners that invoke methods.

```html
<div id="app-5">
    <p>{{ message }}</p>
    <button v-on:click="reverseMessage">Reverse It!</button>
</div>
```
```javascript
var app5 = new Vue({
    el: '#app-5',
    data: {
        message: 'Supercalifragilisticexpialidocious'
    },
    methods: {
        reverseMessage: function () {
            this.message = this.message.split('').reverse().join('')
        }
    }
})
```

Vue allows us to worry about the logic instead of manipulating the DOM (which Vue handles for us).

#### v-model

The v-model directive handles binding between form inputs and the app.

```html
<div id="app-6">
    <p>{{ message }}</p>
    <input v-model="message">
</div>
```
```javascript
var app6 = new Vue({
    el: '#app-6',
    data: {
        message: 'I am inside a paragraph and a text box!'
    }
})
```

### Components

The component system allows us to build large-scale applications composed of small, self-contained, and reusable components. Almost any type of application interface can be abstracte into a tree of components.

A component is basically a Vue instance with pre-defined options.

```javascript
// Define a new component called todo-item
Vue.component('todo-item', {
    template: '<li>This is a todo item/li>'
})
```

This can now be composed into another component's template.

```html
<ol>
    <!-- Creates an instance of the todo-item component -->
    <todo-item></todo-item>
</ol>
```

This component will just render an `<li>` tag with the text 'This is a todo item' -- BORING. Here is how we can pass data from the parent scope (the component template it sits in) into the child component.

```javascript
// Define a new component called todo-item
// but this time it accepts a 'prop' which acts like a custom attribute
Vue.component('todo-item', {
    props: ['todo'],
    template: '<li>{{ todo.text }}/li>'
})
```

Now we pass each 'todo' into the component using `v-bind`.

```html
<div id="app-7">
    <ol>
        <todo-item
            v-for="item in groceryList"
            v-bind:todo="item"
            v-bind:key="item.id">
        </todo-item>
    </ol>
</div>
```
```javascript
Vue.component('todo-item', {
    props: ['todo'],
    template: '<li>{{ todo.text }}/li>'
})

var app6 = new Vue({
    el: '#app-7',
    data: {
        groceryList: [
            { id: 0, text: 'Vegetables' },
            { id: 1, text: 'Cheese' },
            { id: 2, text: 'Wine' }
        ]
    }
})
```

This will then render:

* Vegetables
* Cheese
* Wine

This example looks like more work than it needs to be for its sake but shows how to decouple the child component from the parent via using props. If the list needed changed on the fly, `groceryList` can be modified simply with a `.push()` instead of trying to find the element within the DOM.

In a large-scale application, it will probably be necessary to divide the whole app into components for just about everything to make development more manageable, especially when multiple people are working on different parts of the app.

Imaginary Crazy Example:

```html
<div id="app">
    <app-nav></app-nav>
    <app-view>
        <app-sidebar></app-sidebar>
        <app-content></app-content>
    </app-view>
</div>
```

**Reference:** [Vue Components](https://vuejs.org/v2/guide/components.html)

---
---

## The Vue Instance

### Creating a Vue Instance

Every Vue application starts by creating a new Vue instance with the Vue function.

```javascript
    // As a convention, vm can be used (short for view model)
var vm = new Vue({
    // options
})
```

When creating a Vue instance, you pass in an _options object_.

**API Reference:** [Options Data](https://vuejs.org/v2/api/#Options-Data)

A Vue app consists of a _root Vue instance_ create with `new Vue`, optionally organized into a tree of nested, reusable [components](https://vuejs.org/v2/guide/components.html).

Example of app organization using the todo list:

```
Root Instance
|- TodoList
   |- TodoItem
      |- DeleteTodoButton
      |- EditTodoButton
   |- TodoListFooter
      |- ClearTodosButton
      |- TodoListStatistics
```

---

### Data and Methods

All properties found within a Vue instance's `data` object are added to Vue's _reactivity system_. When the values of those properties change, the view will _react_ and update them in the DOM to match the new values.

```javascript
// A data object, found in the wild.
var data = {
    a: 1
}

// data object doesn't have to be defined within the instance so we assign it too
var vm = new Vue({
    data: date
})

// These reference the same object
vm.a === data.a // => true

// Setting the property on the instance also affects the original data
vm.a = 2
data.a // => 2

// and the other way around
data.a = 3
vm.a // => 3
```

So when `data.a` changes, the view will re-render to show that. However, only properties in `data` when the instance was created are _reactive_.

If you add a new property later, like: `vm.b = 'hello'`, changes to `data.b` will not trigger any view updates.

If you know you'll need a property later on, but it won't have a value immediately, set some sort of initial value:

```javascript
data: {
    textThing: '',
    numberThing: 0,
    boolThing: false,
    arrayThing: [],
    error: null
}
```

There are also a number of useful instance properties and methods special to Vue instances, which are prefixed with `$` to differentiate themselves from user-defined properties.

```javascript
var data = { a: 1 }
var vm = new Vue({
    el: '#example',
    data: data
})

vm.$data === data // => true
vm.$el === document.getElementById['example'] // => true

// $watch is an instance method
vm.$watch('a', function (newValue, oldValue) {
    // This callback function will be called when vm.a chamges
    // i.e. It "watches" 'a' and then calls the function
})
```

**API Reference:** [Instance Properties](https://vuejs.org/v2/api/#Instance-Properties)

---

### Instance Lifecycle Hooks

Each Vue instance goes through a series of initialization steps when it's created such as:

1. set up data observation
2. compile the template
3. mount the instance to the DOM
4. update the DOM when data changes

During these stages, it also runs functions called _lifecycle hooks_, giving users the opportunity to add their own code at specific stages. All lifecycle hooks are called with their `this` context pointing to the Vue instance invoking it.

* beforeCreate
* created
* beforeMount
* mounted
* beforeUpdate
* updated
* beforeDestroy
* destroyed

Example:
```javascript
var vm = new Vue({
    data: {
        a: 1
    },
    created: function () {
        console.log('a is: ' + this.a)
    }
})
// => "a is: 1"
```

**TIP:** Don't use [arrow functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) on an options property or callback. Since arrow functions are bound to the parent context, `this` will not be the Vue instance you may be expecting, often resulting in errors.

### Lifecycle Diagram

#### From vuejs.org: [Lifecycle Diagram](https://vuejs.org/images/lifecycle.png)

---
---

## Template Syntax

Vue.js uses an HTML-based template syntax that allows you to declaratively bind the rendered DOM to the underlying Vue instances's data. These templates are valid HTML that can be parsed by spec-compliant browsers and HTML parsers.

Vue compiles the templates into Virtual DOM render functions, and only re-renders the minimum amount of components and applies the minimal amount of DOM manipulations when the app state changes.

Vue also supports [writing your own render functions](https://vuejs.org/v2/guide/render-function.html) instead of using templates, with optional JSX support.

---

### Interpolations

#### Text

The most basic form of data binding. Uses the "mustache" syntax (double curly braces)

```html
<span>Message: {{ msg }}</span>
```

`{{ msg }}` will be replaced with the valu of the `msg` property on the corresponding data object. It will also be updated whenever the data object's `msg` property changes.

One-time interpolations (which do not update on data change) can also be done by using the [`v-once` directive](https://vuejs.org/v2/api/#v-once). This will affect any binding on the same node.

```html
<span v-once>This msg will never change: {{ msg }}</span>
```

#### Raw HTML

Text interpolations (mustache tags) interprets the data as plain text, not HTML. To output HTML, the [`v-html` directive](https://vuejs.org/v2/api/#v-html) needs to be used.

```html
<div v-html="rawHtml"></div>
```

The contents of this `div` will be replaced with the value of the `rawHTML` property and will be interpreted as plain HTML (read: data bindings within the node will be ignored). **Note:** You cannot use `v-html` to compose template partials as Vue is not a string-based templating engine. Components are preferred as the fundamental unit for UI reuse and composition.

**Warning:** Dynamically rendering arbitrary HTML on your site can be very dangerous because it can easily lead to [XSS vulnerabilities](https://en.wikipedia.org/wiki/Cross-site_scripting). Only use HTML interpolation on trusted content and _never_ on user-provided content.

#### Attributes

Mustaches cannot be used inside HTML attribuets, instead use the [`v-bind` directive](https://vuejs.org/v2/api/#v-bind).

```html
<div v-bind:id="dynamicId"></div>
```

Boolean attributes also work this way. The attribute will be removed if the condition evaluates to a falsy value.

```html
<button v-bind:disabled="isButtonDisabled">Button</button>
```

#### Using JavaScript Expresssions

Vue.js supports JavaScript expressions inside all data bindings!

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

These JS expressions will be evaluated within the data scope of the owner Vue instance. There is a restriction that limits each binding to only contain **one single expression**.

** Examples that will not work:

```html
<!-- this is a statement, not an expression -->
{{ var a = 1 }}

<!-- flow control will not work either, use ternary expressions instead -->
{{ if (ok) { return message} }}
```

**Warning:** Template expressions are sandboxed and only have access to a whitelist of globals such as `Math` and `Date`. No not attempt to access user defined globals in template expressions.

---

### Directives

Directives are special attributes with the `v-` prefix. Directive attribute values are expected to be _a single JavaScript expression_ (with the exception of `v-for`). A directive's job is to reactively apply side effects to the DOM when the value of its expression changes.

Example:

```html
<p v-if="seen">Now you see me</p>
```

The `v-if` directive would remove/insert the `<p>` element based on the truthiness of the value of `seen`.

#### Arguments

Some directives can take an argument/parameter, denoted by a colon after the directive name. e.g. `v-bind` is used to reactively update an HTML attribute so an attribute is needed there.

```html
<a v-bind:href="url"> ... </a>
```

Here `href` is the argument, which tells the `v-bind` directive to bind the element's `href` attribute to the value of the expression `url`.

```html
<a v-on:click="doSomething"> ... </a>
```

`v-on` can be used for DOM event handling.

#### Modifiers

Modifiers are special postfixes denoted by a dot, which indicate that a directive should be bount in some special way.

```html
<form v-on:submit.prevent="onSubmit"> ... </form>
```

The `.prevent` modifier tells the `v-on` directive to call `event.preventDefault()` on the triggered event.

**References:**

[Event Modifers](https://vuejs.org/v2/guide/events.html#Event-Modifiers) : `v-on`

[Form Input Modifiers](https://vuejs.org/v2/guide/forms.html#Modifiers) : `v-model`

---

### Shorthands

The `v-` prefix serves as a visual cue for identifying Vue-specific attributes in a template. Useful when using Vue.js to apply dynamic behavior to existing markup, but can feel too verbose for some frequently used directives. When building a Single Page Application (SPA) where Vue.js manages every template, there are two special shorthands for the most often used dirctives, `v-bind` and `v-on`.

#### `v-bind` Shorthand

```html
<!-- full syntax -->
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>
```

#### `v-on` Shorthand

```html
<!-- full syntax -->
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>
```

Using these shorthands are completely optional, but may be appreciated later on.

---
---

## Computed Properties and Watchers

### Computed Properties

In-template expressions are meant for simple operations. You shouldn't put too much logic in them or it makes them bloated and hard to maintain.

```html
<div id="example">
    {{ message.split('').reverse().join('') }}
</div>
```

The template should be straightforward to read and explain what it's going to show without too much thinking. Logic like this should be modularized into a _computed property_. This will help readability and reusability in case the same expression needs to be included more than once.

#### Basic Example

```html
<div id="example">
    <p>Original message: "{{ message }}"</p>
    <p>Reversed message: "{{ reversedMessage }}"</p>
</div>
```
```javascript
var vm = new Vue({
    el: '#example',
    data: {
        message: 'In a hole in the ground there lived a hobbit.'
    },
    computed: {
        // a computed getter
        reversedMessage: function () {
            // 'this' points to the vm instance
            return this.message.split('').reverse().join('')
        }
    }
})
```

Result:

```
Original message: "In a hole in the ground there lived a hobbit."
Reversed message: ".tibboh a devil ereht dnuorg eht ni eloh a nI"
```

Now we have declared a computed property `reversedMessage` and the function we wrote will be used as the getter function for the property `vm.reversedMessage`. If `vm.message` changes, `vm.reversedMessage` will follow suit. There is a dependency relationship between `message` and `reversedMessage` now.

#### Computed Caching vs Methods

We can also achieve the same result by invoking a method in the expression.

```html
<p>Reversed message: "{{ reverseMessage }}"</p>
```
```javascript
var vm = new Vue({
    el: '#example',
    data: {
        message: 'In a hole in the ground there lived a hobbit.'
    },
    methods: {
        reverseMessage: function () {
            return this.message.split('').reverse().join('')
        }
    }
})
```

We have now defined the same function as a method instead. Looking at the end result, the two approaches are exactly the same. However, the difference is that _computed properties are cached based on their dependencies_. A computed property will only re-evaluate when some of its dependencies have changed. As long as `message` has not changed, multiple accesses to the `reversedMessage` computed property will immediately return the previously computed result without having to run the function again.

A method invocation will _always_ run the function whenever a re-render happens. This can be costly in terms of performance if the function does a lot of processing through loops, arrays, databases, etc. There are times when caching is preferred over methods and vice versa. Your choice is situational.

#### Computed vs Watched Property

Vue has a more generic way to observe and react to data changes on a Vue instance: _watch properties_.

```javascript
// within a Vue instance...
watch: {
    propertytowatch: function (val) {
        // do something here
    }
}
```

So whenever that particular property changes, it will invoke the callback function as defined.

Just be careful to not overuse `watch` and think about whether or not it makes more sense to use a computed property instead.

```html
<div id="demo">{{ fullName }}</div>
```
```javascript
// updating fullName via a watch property
var vm = new Vue ({
    el: '#demo',
    data: {
        firstName: 'Foo',
        lastName: 'Bar',
        fullName: 'Foo bar'
    },
    watch: {
        firstName: function (val) {
            this.fullName = val + ' ' + this.lastName
        },
        lastName: function (val) {
            this.fullName = this.firstName + ' ' + val
        }
    }
})
```

vs

```javascript
// updating fullName via a computed property
var vm = new Vue ({
    el: '#demo',
    data: {
        firstName: 'Foo',
        lastName: 'Bar'
    },
    computed: {
        fullName: function () {
            return this.firstName + ' ' + this.lastName
        }
    }
})
```

The latter with the computed property makes a lot more sense to use in this situation.

#### Computed Setter

Computed properties are by default _getter-only_, but you can also provide a _setter_ when you need one.

```javascript
// within a Vue instance...
computed: {
    fullName: {
        // getter
        get: function () {
            return this.firstName + ' ' + this.lastName
        },
        // setter
        set: function (newValue) {
            var names = newValue.split(' ')
            this.firstName = names[0]
            this.lastName = names[names.length - 1]
        }
    }
}
```

When `vm.fullName` is set to a new value (e.g. `vm.fullName = 'John Doe'`), the setter function will be invoked, updating `vm.firstName` & `vm.lastName` accordingly (`vm.firstName => 'John'`, `vm.lastName => 'Doe'`).

---

### Watchers

Sometimes a custom watcher is necessary where a computed property isn't really appropriate. This is most useful when you want to perform asynchronous or expensive operations in response to changing data.

Example from [vuejs.org](https://vuejs.org/v2/guide/computed.html#Watchers)

```html
<div id="watch-example">
    <p>
        Ask a yes/no question: <input v-model="question">
    </p>
    <p>{{ answer }}</p>
</div>
```
```html
<!-- Since there is already a rich ecosystem of ajax libraries    -->
<!-- and collections of general-purpose utility methods, Vue core -->
<!-- is able to remain small by not reinventing them. This also   -->
<!-- gives you the freedom to use what you're familiar with. -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue ({
    el: '#watch-example',
    data: {
        question: '',
        answer: 'I cannot give you an answer until you ask a question!'
    },
    watch: {
        // whenever question changes, this function will run
        question: function (newQuestion) {
            this.answer = 'Waiting for you to stop typing...'
            this.getAnswer()
        }
    },
    methods: {
        // _.debounce is a function provided by lodash to limit how
        // often a particularly expensive operation can be run.
        // In this case, we want to limit how often we access
        // yesno.wtf/api, waiting until the user has completely
        // finished typing before making the ajax request. To learn
        // more about the _.debounce function (and its cousin
        // _.throttle), visit: https://lodash.com/docs#debounce
        getAnswer: _.debounce(
            function () {
                if (this.question.indexOf('?') === -1) {
                    this.answer = 'Questions usually contain a question mark. ;-)'
                    return
                }
                this.answer = 'Thinking...'
                var vm = this
                axios.get('https://yesno.wtf/api')
                    .then(function (response) {
                        vm.answer = _.capitalize(response.data.answer)
                    })
                    .catch(function (error) {
                        vm.answer = 'Error! Could not reach the API. ' + error
                    })
            },
            // This is the number of milliseconds we wait for the user to stop typing
            500
        )
    }
})
</script>
```

Using `watch` here allows us to perform an asynchronous operation (accessing the API), limit how often we perform that operation, and set intermediary states until we get a final answer.

In addition to `watch`, you can also use the imperative [`vm.$watch` API](https://vuejs.org/v2/api/#vm-watch).

---
---

## Class and Style Bindings

A common need for data binding is manipulating an element's class list and inline styles. Both are attribuets so we can use `v-bind` to handle them, ultimately calculating a final string with our expressions. However, string concatenations can be annoying, messy, and prone to error.

Vue provides special enhancements when `v-bind` is used with `class` and `style`. In addition to strings, the expressions can also evaluate to objects or arrays.

### Binding HTML Classes

#### Object Syntax

An object can be passed to `v-bind:class` to dynamically toggle classes.

```html
<div v-bind:class="{ active: isActive }"></div>
```

This means that if the property `isActive` is true, the `active` class will be applied.

Since this is an object, multiple classes can be toggled. Also the `v-bind:class` directive can co-exist with a regular `class` attribute.

```html
<div class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
```
```javascript
// ...
data: {
    isActive: true,
    hasError: false
}
```

The above will render: `<div class="static active"></div>`

When `isActive` or `hasError` changes, the class list with update accordingly. If `hasError` becomes `true`, the class list with then be `"static active text-danger"`.

The object in `v-bind:class` does not have to be inline. It can be defined in the data object.

```html
<div v-bind:class="classObject"></div>
```
```javascript
// ...
data: {
    classObject: {
        active: true,
        'text-danger': false
    }
}
```

Same results as before, just a different way to doing it. We can also bind to a computed property that returns an object. This is common and can be a powerful pattern to utilize:

```html
<div v-bind:class="classObject"></div>
```
```javascript
// ...
data: {
    isActive: true,
    error: null
},
computed: {
    classObject: function () {
        return {
            active: this.isActive && !this.error,
            'text-danger': this.error && this.error.type === 'fatal'
        }
    }
}
```

#### Array Syntax

An array can also be passed to `v-bind:class` to apply a list of classes.

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```
```javascript
// ...
data: {
    activeClass: 'active',
    errorClass: 'text-danger'
}
```

This renders: `<div class="active text-danger"></div>`

If you would like to toggle a class in the list conditionally, you can do it with a ternary expression:

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

A bit too verbose? We can use the object syntax _inside_ an array syntax:

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

#### With Components

**Reference:** [Vue Components](https://vuejs.org/v2/guide/components.html)

When using the `class` attribute on a custom component, those classes will be _added_ to the component's root element. Existing classes _will not_ be overwritten.

Example:

```javascript
Vue.component('my-component', {
    template: '<p class="foo bar">Hi</p>'
})
```

Then add a class on it in the template:

```html
<my-component class="baz boo"></my-component>
```

It will render the HTML as:

```html
<p class="foo bar baz boo">Hi</p>
```

This goes the same for `v-bind:class` too.

```html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

If `isActive' is truthy, the HTML renders as:

```html
<p class="foo bar active">Hi</p>
```

### Binding Inline Styles

#### Object Syntax

The object syntax for `v-bind:style` is straightforward (and just about looks like CSS though it's a JavaScript object). For the CSS property names, you can use either camelCase or kebab-case (but use single quotes for kebab-case).

```html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
<!-- or (kebab-case) -->
<div v-bind:style="{ color: activeColor, 'font-size': fontSize + 'px' }"></div>
```
```javascript
// ...
data: {
    activeColor: 'red',
    fontSize: 30
}
```

It is often a good idea to bind to a style object directly so that the template is cleaner.

```html
<div v-bind:style="styleObject"></div>
```
```javascript
// ...
data: {
    styleObject: {
        color: red;
        'font-size': '13px'
    }
}
```

Again, the object syntax can be used in conjuction with computed properties that return objects as explained above for `v-bind:class`.

#### Array Syntax

The array syntax for `v-bind:style` allows you to apply multiple style objects to the same element.

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

#### Auto-prefixing

When you use a CSS property that requires vendor prefixes (e.g. -webkit, -moz, -ms) in `v-bind:style`, Vue will automatically detect and add appropriate prefixes to the applied styles.

#### Multiple Values

You can provide an array of multiple (prefixed) values to a style property.

```html
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

This will only render the last value in the array which the browser supports. So in this example, it will render `display: flex` for browsers that support the unprefixed version of flexbox.

---
---

## Conditional Rendering

### `v-if`

In Vue, we can use the `v-if` directive to write out a conditional block which will render if its argument has a truthy value. We can also add an "else block" with `v-else`.

```html
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

#### Conditional Groups with `v-if` on `<template>`

Since `v-if` is a directive, it needs to be attached to a single element. If you want to toggle more than one element, attach `v-if` to a `<template>` element. This element serves as an invisible wrapper (the final rendered results will not include the `<template>` element).

```html
<template v-if="ok">
    <h1>Title</h1>
    <p>Paragraph 1</p>
    <p>Paragrah 2</p>
</template>
```

#### `v-else`

You can use the `v-else` directive to indicate an "else block" for `v-if`.

```html
<div v-if="Main.random() > 0.5">
    How you see me
</div>
<div v-else>
    Now you don't
</div>
```

`v-else` must immedinately follow a `v-if` or a `v-else-if` element.

#### `v-else-if`

And of course, there's a way to use an "else if block" called `v-else-if`. It must immediately follow a `v-if' or another `v-else-if` element.

```html
<div v-if="type === 'A'">
    A
</div>
<div v-else-if="type === 'B'">
    B
</div>
<div v-else-if="type === 'C'">
    C
</div>
<div v-else>
    Not A/B/C
</div>
```

#### Controlling Reusable Elements with `key`

Vue tries to render elements as efficiently as possible and re-use them so they aren't rendered from scratch too often. Obviously this is great for performance.

Example:

```html
<template v-if="loginTyoe === 'username'">
    <label>Username</label>
    <input placeholder="Enter your username">
</template>
<template v-else>
    <label>Email</label>
    <input placeholder="Enter your email address">
</template>
```

If `loginType`'s value changes from `'username'`, the text the user has already entered into the `<input>` will not be lost. Since both templates use the same elements, it is not replaced, only its `placeholder`.

That's cool and all, but sometimes we don't want it to do that by default. Vue allows us to do that using the `key` attribute.

```html
<template v-if="loginTyoe === 'username'">
    <label>Username</label>
    <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
    <label>Email</label>
    <input placeholder="Enter your email address" key="email-input">
</template>
```

Now when `loginType` changes from `'username'`, the input element will be re-rendered.

### `v-show`

Another way of conditionally displaying an element is the `v-show` directive.

```html
<h1 v-show="ok">Hi there.</h1>
```

This is ddifferent from `v-if`/`v-else` in that the element with `v-show` will always be rendered and remain in the DOM. `v-show` only toggles the `display` CSS property.

**Note:** `v-show` doesn't support the `<template>` element, nor does it work with `v-else`.

### `v-if` vs `v-show`

`v-if` is "real" conditional rendering - it ensures event listeners and child components inside the conditional block are properly destroyed and re-created during toggles.

It is also _lazy_ - if the condition is false on the initial render, it will not do anything. The conditional block will not be rendered until the condition becomes true for the first time.

`v-show` is simpler - the element is always rendered regardless of initial condition (with CSS-based toggling).

`v-if` - higher toggle costs

`v-show` - higher initial render costs

If you need something toggled often enough, use `v-show`. Use `v-if` if the condition is unlikely to change often.

### `v-if` with `v-for`

When `v-if` and `v-for` are used together, `v-for` has a higher priority, meaning that `v-if` will be run on each iteration of the `v-for` loop. This can be useful when you want to render nodes for only _some_ items:

```html
<li v-for="todo in todos" v-if="!todo.isComplete">
    {{ todo }}
</li>
```

Which will render only the todos that are not complete.

If you want to conditionally skip execution of the loop, you can place the `v-if` on a wrapper element.

```html
<ul v-if="todos.length">
    <li v-for="todo in todos">
        {{ todos }}
    </li>
</ul>
<p v-else>No todos left!</p>
```

---
---

## List Rendering

