## Creating a Transparent Base Component with Vue.js

See the article [@trasparentcomponent], [@basecomponent] and the repo <https://github.com/crguezl/transparent-base-component> 
for additional information.

According to the official Vue js style guide you should name a component a **base component** if 

1. **It is unique to your application** and **you are using it in many places** inside your app. 
2. You can name it like *BaseIcon.vue* or every other name starting with *Base*.

Sometimes you have a couple different components that are almost just native elements, but with a slight wrapper around them that adds functionality. 
Examples include an input field or  a textarea that auto resizes.

A **Component is Transparent**  when it behaves as closely as possible to some native component - allowing us to *transparently pass through attributes, add event listeners, and more*. 

You usually want to have a label along with an input field (or select, radio button, etc) but you would also like to avoid to repeat the same code over and over again for multiple input fields. 


### Creating the WorkSpace with \@vue/cli 

```
$ npm update -g @vue/cli
$ npm install -g @vue/cli-service-global
$  pwd
/Users/casianorodriguezleon/campus-virtual/2122/learning/vue-learning/creating-a-transparent-base-component-with-vue.js
$ vue create transparent-base-component
$ cd transparent-base-component
$ tree -I node_modules
.
├── README.md
├── babel.config.js
├── package-lock.json
├── package.json
├── public
│   ├── favicon.ico
│   └── index.html
└── src
    ├── App.vue
    ├── assets
    │   └── logo.png
    ├── components
    │   └── HelloWorld.vue
    └── main.js
```

### How `v-model` works under the hood


Let us remember the DOM `input` event.

The classical [DOM `input` event](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event) fires when the value of an `<input>`, `<select>`, or `<textarea>` element has been changed.

See this example running at [examples/dom-input-event.html](examples/dom-input-event.html):

```html
    <div class="execution">
        <input placeholder="Enter some text" name="name" id="#dom-input-event"/>
        <p id="values-for-input-event"></p>
        </div>
        
        <script>
        const input = document.getElementById('#dom-input-event');
        const log = document.getElementById('values-for-input-event');
        
        input.addEventListener('input', updateValue);
        
        function updateValue(e) {
          console.log(e);
          log.textContent = e.target.value;
        }
        </script>
    </div>   
```

To build this base component, we're going to use Vue's `v-model` directive. 

To recap, let's a simple component `src/components/BaseInput.vue`  to remember  how to use `v-model`:

```vue
<template>
  <div class="hello">
  <input type="text" v-model="username">  </div>
</template>

<script>
export default {
  data() {
    return {
      username: ''
    }
  }
}
</script>
```

In our Vue.js instance, we have the `data` property `username` and in our `template` we bind that variable to the input field. 

According to the [Vue Guide on components](https://zendev.com/2018/05/31/transparent-wrapper-components-in-vue.html), `v-model `on a component essentially works by passing in a `value` prop, and applying and `input` event handler.

The `v-model` directive binds the value of `username` to the `value` property of the `input` field 
(and therefor keep the content of that `input` field always in **sync** with the variable) and update the `username` variable with the new `value` whenever someone types something into the `input` field.

That is, this code:

```html
<input v-model="searchText">
``` 

is the same as 

```html
  <input
      v-bind:value="searchText"
      v-on:input="searchText = $event.target.value"
    >
    <h1>{{searchText}}</h1>
```

We can use this directive also for our base component, but we first need to understand, how `v-model` works under the hood. 

By default, on a `input`, the `v-model="variable"` 

1. Binds the `variable` you pass to the directive to the `value` property of the `input` and 
2. Whenever that `input` element emits an `input` event,  the `variable` will be updated with the new `value` automatically.

When used on a component, `v-model` instead does this:

```html
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event"
></custom-input>
```

For this to actually work though, the `<input>` inside the component must:

* Bind the `value` attribute to a `value` prop
* On `input`, **emit its own custom `input` event** with the new value

This means, that in our base component we can expect 

1. To listen for the `input` event and get the `target.value` of the event and 
2. To `$emit` an `input` event back to the parent with the new `$event.target.value` 

### Building the base component

We name our component `BaseInput.vue`  and store it in the `components` folder following the recomendations.

It has props `label` for the label of the input, and `type` (`button`, `checkbox`, `date`, ... ) plus the `placeholder` prop.

```vue
<template>
  <div class="hello">
    <label>
      {{ label }}
      <input
        :type="type"
        :placeholder="placeholder"
        @input="$emit('input', $event.target.value)"
      >
    </label>
  </div>
</template>

<script>
export default {
  name: "BaseInput",
  props: {
    label: String,
    type: String,
    placeholder: String
  }
};
</script>
```

We listen for the `input` event and `emit` another `input` event back to the parent component in order to let the parent `v-model` directive update the corresponding variable accordingly. 

This way, our `BaseInput` component is a **transparent component**, which can be used with `v-model`.

Here is our main `App.vue` component:

```vue 
<template>
  <div id="app">
    <BaseInput label="Username" type="text" placeholder="Username" v-model="username"/>
    <BaseInput label="Password" type="password" placeholder="********" v-model="password"/>

    <hr>
    {{ username }} - {{ password }}
  </div>
</template>

<script>
import BaseInput from "./components/BaseInput";

export default {
  name: "App",
  components: {
    BaseInput
  },
  data() {
    return {
      username: "",
      password: ""
    };
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```
