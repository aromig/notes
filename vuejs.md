# Learning Vue.js
###### (personal notes from vuejs.org rewritten for my understanding)

Vue is a progressive framework for building user interfaces. The core library is focused on the view layer (of MVVM/MVC) only.

For learning use: `<script src="https://unpkg.com/vue"></script>`

## Installing Vue
(later)

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

## Conditionals and Loops

### v-if


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

### v-for

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

## Handling User Input

### v-on

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

### v-model

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

## Components

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

### Data and Methods

