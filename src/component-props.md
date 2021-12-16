## Component Props

### Prop Casing (camelCase vs kebab-case)

HTML attribute names are case-insensitive, so browsers will interpret any uppercase characters as lowercase. 

That means when you’re using **in-DOM templates**, *camelCased* prop names need to use their *kebab-cased* (hyphen-delimited) equivalents:

```js 
Vue.component('blog-post', {
  // camelCase in JavaScript
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
<!-- kebab-case in HTML -->
<blog-post post-title="hello!"></blog-post>
```

Again, if you’re using **string templates**, this limitation does not apply.

### DOM Templates and String Templates

See [What exactly is a DOM Template in Vue.js?](https://stackoverflow.com/questions/68578348/what-exactly-is-a-dom-template-in-vue-js)

In the context of Vue, **"DOM template"** (commonly seen as "in-DOM template" in the rest of the Vue docs) refers to a component's markup *specified in the document body/head*, as opposed to a *string template* or *SFC template*.

While DOM templates include `<script type="text/html">` or `<template>` in the document, these two tags (along with string templates and SFC templates) are not subject to the known [DOM template parsing caveats](https://v3.vuejs.org/guide/component-basics.html#dom-template-parsing-caveats) because they're inert.

### DOM templates

*in-DOM template:*

```html
// index.html
<body>
  <div id="app">
    <ul>
      <li v-for="i in 5">{{ i }}</li>
    </ul>
  </div>
</body>
```

[demo](https://codepen.io/tony19/pen/PomePZJ)

*in-DOM template with `<script type="text/html">`:*

```html
// index.html
<body>
  <script type="text/html" id="my-comp-template">
    <ul>
      <li v-for="i in 5">{{ i }}</li>
    </ul>
  </script>

  <div id="app">
    <my-comp></my-comp>
  </div>
</body>
```

[demo](https://codepen.io/tony19/pen/NWjMGWy?editors=1010)

*in-DOM template with `<template>`:*

```html
// index.html
<body>
  <template id="my-comp-template">
    <ul>
      <li v-for="i in 5">{{ i }}</li>
    </ul>
  </template>

  <div id="app">
    <my-comp></my-comp>
  </div>
</body>
```

[demo](https://codepen.io/tony19/pen/BaRxoaw?editors=1010)

### String templates

```js
// MyComponent.js
Vue.component('my-comp', {
  template: `<ul>
               <li v-for="i in 5">{{ i }}</li>
             </ul>`
})
```

[demo](https://codepen.io/tony19/pen/zYwjvKO?editors=1010)

### SFC templates

```html
// MyComponent.vue
<template>
  <ul>
    <li v-for="i in 5">{{ i }}</li>
  </ul>
</template>
```

[demo](https://codepen.io/tony19/pen/PomePGO)