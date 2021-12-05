## Template Syntax

Vue.js uses an HTML-based template syntax that allows you to declaratively bind the rendered DOM to the underlying Vue instance’s data. 

All Vue.js templates are valid HTML that can be parsed by spec-compliant browsers and HTML parsers.

Under the hood, Vue compiles the templates into Virtual DOM[^virtualDom] render functions. 

[^virtualDom]: The virtual DOM (VDOM) is a programming concept where an ideal, or *virtual*, representation of a UI is kept in memory and synced with the “real” DOM by a library like Vue or React

Combined with the reactivity system, Vue is able to intelligently figure out the minimal number of components to re-render and apply the minimal amount of DOM manipulations when the app state changes.

### An Introduction to Render Functions 

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

In this case, we have to know a few things:

1. The [v-slot](https://vuejs.org/v2/guide/components-slots.html) directive and
2. That children of a component are stored on the component instance at `this.$slots.default`,
   

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

### Interpolations


#### Text

The most basic form of data binding is text interpolation using the *Mustache* syntax (double curly braces):

```html
<span>Message: {{ msg }}</span>
``` 

The mustache tag will be replaced with the value of the `msg` property on the corresponding data object. It will also be updated whenever the data object’s `msg` property changes.

You can also perform one-time interpolations that do not update on data change by using the `v-once` directive, but keep in mind this will also affect any other bindings on the same node:

```html
<span v-once>This will never change: {{ msg }}</span>
``` 

Check this example:

```html
<h2 class="execution">
  <div id="appXXX4">
  <span>Message: {{ msg }}</span>
  <br/>
  <span v-once>This will never change: {{ msg }}</span>
  </div>
</h2>

<script>
  var appXXX4 = new Vue({
    el: '#appXXX4',
    data: {
      msg: 'Hello Vue!'
    }
  })
</script>
```

Open the  dev tools, try to change `appXXX4.msg` and see what happens:

<h2 class="execution">
  <div id="appXXX4">
  <span>Message: {{ msg }}</span>
  <br/>
  <span v-once>This will never change: {{ msg }}</span>
  </div>
</h2>

<script>
  var appXXX4 = new Vue({
    el: '#appXXX4',
    data: {
      msg: 'Hello Vue!'
    }
  })
</script>

#### Raw HTML

The double mustaches interprets the data as plain text, not HTML. In order to output real HTML, you will need to use the `v-html` directive:

```html
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
``` 

The contents of the span will be replaced with the value of the `rawHtml` property, interpreted as plain HTML - data bindings are ignored. 

See this example:

```html 
<div class="execution">
  <div id="appXXX5">
   <p>Using mustaches: {{ rawHtml }}</p>
   <p>Using v-html directive: <span v-html="rawHtml"></span></p>
  </div>
</div>

<script>
  var appXXX5 = new Vue({
    el: '#appXXX5',
    data: {
      rawHtml: '<i>Hello</i> {{ text }} <b>Vue</b>!',
      text: "Chazam!"
    }
  })
</script>
```

<div class="execution">
  <div id="appXXX5">
   <p>Using mustaches: {{ rawHtml }}</p>
   <p>Using v-html directive: <span v-html="rawHtml"></span></p>
  </div>
</div>

<script>
  var appXXX5 = new Vue({
    el: '#appXXX5',
    data: {
      rawHtml: '<i>Hello</i> {{ text }} <b>Vue</b>!',
      text: "Chazam!"
    }
  })
</script>

Note that you cannot use `v-html `to compose template partials, 
because Vue is not a string-based templating engine. 

Instead, components are preferred as the fundamental unit for UI reuse and composition.

Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to XSS vulnerabilities. 

Only use HTML interpolation on trusted content and never on user-provided content.

#### Attributes

Mustaches cannot be used inside HTML attributes. Instead, use a `v-bind` directive:

```html
<div v-bind:id="dynamicId"></div>
```

In the case of boolean attributes, where their mere existence implies `true`,
`v-bind` works a little differently. 

In this example:

```html
<button v-bind:disabled="isButtonDisabled">Button</button>
```

If `isButtonDisabled` has the value of `null`, `undefined`, or `false`, 
the disabled attribute will not even be included in the rendered 
`<button>` element.

#### Using JavaScript Expressions

So far we’ve only been binding to simple property keys in our templates. But Vue.js actually supports the full power of JavaScript expressions inside all data bindings:

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

These expressions will be evaluated as JavaScript in the data scope of the owner Vue instance. 

One restriction is that each binding can only contain one single expression, so the following will NOT work:

```html
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```

Template expressions are sandboxed and only have access to a whitelist of globals such as Math and Date. You should not attempt to access user-defined globals in template expressions.
