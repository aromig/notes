# Learning Vue.js
###### (personal notes from vuejs.org rewritten for my understanding)

Vue is a progressive framework for building user interfaces. The core library is focused on the view layer (of MVC) only.

For learning use: ```<script src="https://unpkg.com/vue"></script>```

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
});
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
});
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
});
```

Since ```seen``` is set to true, it will render 'Now you see me' however if ```seen``` gets set to false, it will disappear!

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
});
```

This renders an ordered list with the above values as list items. The list can be manipulated since it's an array.

```javascript
app4.todos.push({ text: '... Profit!' }); // Appends an item
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
});
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
});
```

