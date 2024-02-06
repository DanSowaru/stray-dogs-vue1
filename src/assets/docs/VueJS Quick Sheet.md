# VueJS Quick Sheet

## Setup

### Vue CLI

- npm install -g @vue/cli
- vue --version
- vue create app-name

- import this script at the `<body>` bottom:

        <script src="https://unpkg.com/vue@3/dist/vue.global.js"> </script>

- open a `<script>` tag below and add

        <script>
            Vue.createApp({})
        </script>

- save the createApp in a variable then mount it:

        let app = createApp({})
        app.mount()

- You can name get a div id as parameter to tell where will it be mounted

        <div id="app">
        </div>

        app.mount('#app')

- We can create **variables** inside the **options object**, using the `data: function() {}` and **functions** using the `methods: function() {}`. The **options object** is the {} inside the createApp():

        let app = Vue.createApp({
            data: function () {
                return {
                    greeting: "Hello, world",
                };
            },

            methods: {
                toggleBox() {};
                toggleLight: function() {};
            }
        });

- To use a variable, we can use a {{}} syntax to parse Javascript:

        <div id="app">
      	    <h1>{{ greeting }}</h1>
        </div>

## Directives

A way to connect elements in the HTML with the Vue.js Javascript

- Vue directives uses the prefix "v-" to separate from HTML attributes.

- `v-model` is a directive where we can add a two-way databinding to a tag. We create an input and add the `v-model` directive, then add the variable it'll be binded to. It can use some modifiers like `v-model.number` to cast the value as a number.

        <input v-model="greeting" />

- `v-if` is a directive that changing it to false will hide the div:

        <div v-if="isVisible" class="box"></div>

        data: function () {
            return {
                isVisible: true,
            };
        },

- `v-show` works the same, but instead of deleting the element, it'll just add `display:none`.

  - `v-if` is better used when you need to render something or not.
  - `v-show`is better used when you will toggle something on and off constantly and doesn't want to have to load the element again every time.

- We can use chain `v-else-if` and `v-else` to the `v-if` directive:

    	<div v-if="isVisible" class="box"></div>
  	    <div v-else-if="isVisible2" class="box2"></div>
  	    <div v-else class="box3"></div>

- `v-cloak` will prevent anything from rendering at the page until the whole page has loaded. The recommended pattern in VueJS is to add the `display:none` style to it:

        <style>
            [v-cloak] {
      		display: none;
      	}
        </style>

        <div id="app" v-cloak>

## Events & Methods

- `v-on` is used for custom events or any of the standard browser events. It can also be written `@eventname` (like @click):

        <button v-on:click="isVisible = !isVisible">Show Box</button>

        <button @click="isVisible = !isVisible">Show Box</button>

- `@keyup` is for key inputs. The most common have shortcuts like `@keyup.enter`, but for the other keys you go with the keycode, like `@keyup.13`. :

        <input @keyup.enter="greetMethod" v-model="greeting" />

- If a method need parameters, just call it with the () and it's parameters:

        @keyup.enter="greetMethod(greetingAttribute)"

- The ".enter" parte is called _event modifier_, meaning that not only you're listening to the key event but specifically the "enter" key:

        <button @click.right="toggleBox">Show Box</button>

- You can chain modifiers, like ".prevent" to prevent a form to be submitted and ".stop" to stop propagation

        <button @click.prevent.stop="toggleBox">Show Box</button>

- `@submit` event:
  <form @submit="handleSubmit">

## Components

- The _options object_ is a single component. We can create custom components that are called as <**custom-tags**>. They must be defined as a Vue component after the **app** variable (The Vue instance; `let app = Vue.createApp({})`) and before mounting the DOM (`app.mount()`).
- We create a component with `app.component()` with two arguments: the name and the options object (that can use the previous _data_, _methods_ etc).
- The custom component also need a **template**, created inside the component:

        app.component('custom-form', {
                template:
                `
                <div>
                        <input type="email"/>
                        <input type="password"/>
                </div>
                `
        })

## Component Props

- We've got to "register" a component so a parent component can use it. We add below the data object of the parent component:

        components: ['custom-input'],

- To pass down variables to the child component, we can use the `v-bind` modifier in the parent component. The v-bind turned the HTML attribute into something that can be parsed as Javascript. It can be written in two forms:

        <custom-input v-bind:label="emailLabel"/>
        <custom-input :label="emailLabel"/>

- Then in the child component we receive the variables with a `props` array of Strings, after the template. If using in a tag attribute, v-bind the attribute too:

       props: ["label"],

- If we use `v-model` plus the name of the variable create a `modelValue` prop with the same name. The example is creating a `modelValue="email"`. where the prop _modelValue_ is being parsed down with the name to the child. _modelValue_ is a object containing the _email_ string variable:

        <custom-input v-model="email" :label="emailLabel"/>

        props: ["label", "modelValue"],

- To pass up variables to the father component, one of the ways is to use the `computed: {}` object where we can put variable names as keys. There we can create _getters_ and _setters_ to the props:

        computed: {
                inputValue: {
                        get() {},
                        set(value) {},
                },
        },

- The _getter_ will act when we call the _inputValue_ property. The _setter_ will act when we update the _inputValue_ property.
- To **emit** the variable or and event to other components, we use `this.$emit()`.
- We can use the Vue event called `update:`. In `$emit()`, the first argument is the **type of event** and the second argument is the value passing through that event:

        set(value) {
                this.$emit("update:modelValue", value);
        },

- The `v-model` in the parent is listening to updates on the `modelValue` property in the child.
- Props passed down are **immutable** in the child, read only. That's why to change it we create a new variable (inputValue) and `$emit()` the update event to the parent, so the parent updates it over there.
- The `methods`object could also do the same update;
- Functions can be passed down to children component to be called by them;

## Loops

Used when we have to create multiple instances of the same tag, like the inputs of the form.

- To use a loop, we create an Array in the data() object. The we can iterate of it with `v-for` and use a key to keep track and uniquely identify each node in the loop (like and "id"). The best way is to create an index in the loop:

        inputs: [
                {
                        label: "Email Label",
                        value: "",
                        type: "email",
                },
                {
                        label: "Password Label",
                        value: "",
                        type: "password",
                },
        ],

        <custom-input
                v-for="(input, index) of inputs"
                key="index"
                v-model="input.value"
                :label="input.label"
                :type="input.type"
        />

## Lifecycle Hooks

beforeCreate -> created -> beforeMount -> mounted -> beforeUpdate -> updated -> beforeUnmount -> unmounted

- It's commonly used to:
  - Check if user is authorized;
  - API calls from backend to feed the component;
  - Creating or removing events;
  - Getting or cleaning up data.

## Router

- `<router-link to="/destination">`
- `<router-view/>`

## Styles Scope

- Anything inside the `<style>` tag in the **App.vue** will override globally. `<style scoped>` solves this.
- We can import the CSS file directly inside the homonimous JS file

        import './assets/styles/main.scss'

- _Good practice:_ Have a main styles file that import all of the others and import only that one in tha main.js file
