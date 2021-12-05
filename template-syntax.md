## Template Syntax

Vue.js uses an HTML-based template syntax that allows you to declaratively bind the rendered DOM to the underlying Vue instance’s data. 

All Vue.js templates are valid HTML that can be parsed by spec-compliant browsers and HTML parsers.

Under the hood, Vue compiles the templates into Virtual DOM[^virtualDom] render functions. 

[^virtualDom]: The virtual DOM (VDOM) is a programming concept where an ideal, or *virtual*, representation of a UI is kept in memory and synced with the “real” DOM by a library like Vue or React

Combined with the reactivity system, Vue is able to intelligently figure out the minimal number of components to re-render and apply the minimal amount of DOM manipulations when the app state changes.

If you are familiar with Virtual DOM concepts and prefer the raw power of JavaScript, [you can also directly write **render functions** instead of templates](https://vuejs.org/v2/guide/render-function.html), with optional JSX support.


Here follows an example using a render function.
Say you want to generate anchored headings:

```html
<div id="app-XXX2" class="execution">
  <anchored-heading level="1"> <a name="hello" href="#hello">
    Section Hello
  </a></anchored-heading>
  Blah blah
  <anchored-heading level="2"> <a name="world" href="#world">
    Subsection world
  </a></anchored-heading>
  More blah ...
  <anchored-heading level="1"> <a name="bye-world" href="#bye-world">
    Section Bye world
  </a></anchored-heading>
  This is the last section ...
</div>
```

In this case, we have to know the [v-slot](https://vuejs.org/v2/guide/components-slots.html) directive and that children are stored on the component instance at `$slots.default`. 

```js

<script>
Vue.component('anchored-heading', {
  render: function (createElement) {
    return createElement(
      'h' + this.level,   
      this.$slots.default // array of children
    )
  },
  props: {
    level: {
      type: String,
      required: true
    }
  }
})

var appXXX2 = new Vue({
  el: '#app-XXX2'
})
</script>
```

The example produces the following output:

<div id="app-XXX2" class="execution">
  <anchored-heading level="1"> <a name="hello" href="#hello">
    Section Hello
  </a></anchored-heading>
  Blah blah
  <anchored-heading level="2"> <a name="world" href="#world">
    Subsection world
  </a></anchored-heading>
  More blah ...
  <anchored-heading level="1"> <a name="bye-world" href="#bye-world">
    Section Bye world
  </a></anchored-heading>
  This is the last section ...
</div>

<script>
Vue.component('anchored-heading', {
  render: function (createElement) {
    return createElement(
      'h' + this.level,   
      this.$slots.default // array of children
    )
  },
  props: {
    level: {
      type: String,
      required: true
    }
  }
})

var appXXX2 = new Vue({
  el: '#app-XXX2'
})
</script>