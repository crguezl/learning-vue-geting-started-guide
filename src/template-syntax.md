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
  <anchored-heading level="1">Section Hello</anchored-heading>
  Begining Blah blah ... 
  <anchored-heading level="2">Subsection World</anchored-heading>
  Blah blah in subsection ...
    <anchored-heading level="1">Section Bye</anchored-heading>
  Blah blah final ...
</div>
```

In this case, we have to know a few things:

1. That children of a component are stored on the component instance at `this.$slots.default`,
2. That `render`  has to produce the VDOM element using its argument `createElement`
3. How it works the [createElement](https://vuejs.org/v2/guide/render-function.html#createElement-Arguments) function passed to `render`  
4. It is also convenient to know about the [`v-slot`](https://vuejs.org/v2/guide/components-slots.html) directive 

```js
<script>
Vue.component('anchored-heading', {
  render: function (createElement) {
    let default0 = this.$slots.default[0]; 
    let text = default0.text; // The text between the brackets
    console.log(default0);

    let name = text.replace(/\s+/g,'').replace(/\W+/g,'-');
    return createElement(
      'h' + this.level,   
      [ createElement('a', // Building the child 
                    { attrs: 
                       { 
                          name: name, 
                          href: `#${name}`
                       }
                    },  text) ],
    )
  },
  props: [ "level" ]
})

var appXXX2 = new Vue({
  el: '#app-XXX2'
})
</script>
```

The example produces the following output:

<div id="app-XXX2" class="execution">
  <anchored-heading level="1">Section Hello</anchored-heading>
  Begining Blah blah ... 
  <anchored-heading level="2">Subsection World</anchored-heading>
  Blah blah in subsection ...
  <anchored-heading level="1">Section Bye</anchored-heading>
  Blah blah final ...
</div>

<script>
Vue.component('anchored-heading', {
  render: function (createElement) {
    let default0 = this.$slots.default[0];
    let text = default0.text;
    console.log(default0);

    let name = text.replace(/\s+/g,'').replace(/\W+/g,'-');
    return createElement(
      'h' + this.level,   
      [ createElement('a', 
                    { attrs: 
                       { 
                          name: name, 
                          href: `#${name}`
                       }
                    },  text) ],
     
      // this.$slots.default // array of children
    )
  },
  props: [ "level" ]
})

var appXXX2 = new Vue({
  el: '#app-XXX2'
})
</script>

### Exercise: Introduction to Render Functions

See the video [@renderfunctions]. Reproduce the steps.
Leave the result in the folder `renderfunctions` of the assignment repo.

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

#### Raw HTML. The `v-html` directive

Sometimes you might want to set the HTML of an element from a expression. Let's say  you're calling an API that returns some HTML which you need to display on the page. Vue has automatic HTML escaping built in, so the double mustaches interprets the data as plain text, not HTML. In order to output real HTML, you will need to use the `v-html` directive:

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

Note that you cannot use `v-html` to compose template partials, 
because Vue is not a string-based templating engine. 

Instead, components are preferred as the fundamental unit for UI reuse and composition.

:::  tip
Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to [XSS vulnerabilities](https://en.wikipedia.org/wiki/Cross-site_scripting). Never put user input in `v-html` or allow users to modify anything that goes through `v-html` without carefully validating and escaping what they have written.

Only use HTML interpolation on trusted content and never on user-provided content.
:::

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

::: tip
**Template expressions are sandboxed** and only have access to a whitelist of globals such as `Math` and `Date`. 

*You should not attempt to access user-defined globals* in template expressions.
:::

### Directives

Directives are special attributes with the `v-` prefix. 

**Directive attribute values are expected to be a single JavaScript expression** (with the exception of `v-for`, which will be discussed later). 

**A directive’s job is to reactively apply side effects to the DOM when the value of its expression changes**. 

Let’s review the example we saw in the [introduction](#introduction):

```html 
<p v-if="seen">Now you see me</p>
```

Here, the `v-if` directive would remove/insert the `<p>` element based on the truthiness of the value of the expression `seen`.

#### Arguments

Some directives can take an *argument*, denoted by a colon after the directive name. 

For example, the `v-bind` directive is used to reactively update an HTML attribute:

```html
<a v-bind:href="url"> ... </a>
```

Here `href` is the argument, which tells the `v-bind ` directive to bind the element’s `href` attribute to the value of the expression `url`.

Another example is the `v-on` directive, which listens to DOM events:

```html
<a v-on:click="doSomething"> ... </a>
```

Here the argument is the event name to listen to. 

#### Dynamic Arguments


Starting in version 2.6.0, it is also possible to use a JavaScript expression in a directive argument by wrapping it with square brackets:


Note that there are some constraints to the argument expression, as explained
in the "Dynamic Argument Expression Constraints" section below.

```html
<a v-bind:[attributeName]="url"> ... </a>
```

Here `attributeName` will be dynamically evaluated as a JavaScript expression, 
and its evaluated value will be used as the final value for the argument. 

For example, if your Vue instance has a `data` property, `attributeName`, whose value is `"href"`, then this binding will be equivalent to `v-bind:href`.

Similarly, you can use dynamic arguments to bind a handler 
to a dynamic event name:

```html
<a v-on:[eventName]="doSomething"> ... </a>
```

In this example, when `eventName`‘s value is `"focus"`, `v-on:[eventName]` will be equivalent to `v-on:focus`.

##### Dynamic Argument Value Constraints

Dynamic arguments are expected to evaluate to a string, with the exception of `null`. 
The special value `null` can be used to explicitly remove the binding. 
Any other non-string value will trigger a warning.

##### Dynamic Argument Expression Constraints

Dynamic argument expressions have some syntax constraints because certain characters, such as spaces and quotes, are invalid inside HTML attribute names. For example, the following is invalid:

```html
<!-- This will trigger a compiler warning. -->
<a v-bind:['foo' + bar]="value"> ... </a>
```

The workaround is to either use expressions without spaces or quotes, or replace the complex expression with a computed property.

When using in-DOM templates (i.e., templates written directly in an HTML file), you should also avoid naming keys with uppercase characters, as browsers will coerce attribute names into lowercase:

```html
<!--
This will be converted to v-bind:[someattr] in in-DOM templates.
Unless you have a "someattr" property in your instance, your code won't work.
-->
<a v-bind:[someAttr]="value"> ... </a>
```
#### Modifiers

Modifiers are special postfixes **denoted by a dot**, which indicate that a directive should be bound in some special way. 

For example, the `.prevent` modifier tells the `v-on` directive to call event 
`.preventDefault()` on the triggered event:

```html
<form v-on:submit.prevent="onSubmit"> ... </form>
```

Read the tutorial [Handling Click Events with Vue](https://masteringjs.io/tutorials/vue/click). Here is another example of modifier:

```html
<div id="modifier-prevent" class="execution"></div>
<script>
const appModifierPrevent = new Vue({
  el: "#modifier-prevent",
  data: () => ({ev: null}),
  methods: {
    setEv(ev) {
      this.ev = JSON.stringify(ev); 
    }
  },
  template: `
    <div class="execution">
      <p>Default Behavior: to take you to www.google.com</p>
      <p>
        <a v-on:click.prevent="setEv" href="https://www.google.com">
          Click Me, and I show you the value of the event
        </a>
      </p>
      <p>{{ ev }}</p>
    </div>
  `
});
</script>
``` 

<div id="modifier-prevent" class="execution"></div>
<script>
const appModifierPrevent = new Vue({
  el: "#modifier-prevent",
  data: () => ({ev: null}),
  methods: {
    setEv(ev) {
      this.ev = JSON.stringify(ev); 
    }
  },
  template: `
    <div class="execution">
      <p>Default Behavior: to take you to www.google.com</p>
      <p>
        <a v-on:click.prevent="setEv" href="https://www.google.com">Click Me, and I show you the value of the event</a>
      </p>
      <p>{{ ev }}</p>
    </div>
  `
});
</script>


### Shorthands

The `v-` prefix serves as a visual cue for identifying Vue-specific attributes in your templates. 

This is useful when you are using Vue.js to apply dynamic behavior to some existing markup, but can feel verbose for some frequently used directives. 

At the same time, the need for the `v-` prefix becomes less important when you are building a SPA, where Vue manages every template. 

Therefore, Vue provides special shorthands for two of the most often used directives, `v-bind` and `v-on`:

#### v-bind Shorthand

```html
<!-- full syntax -->
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a :[key]="url"> ... </a>
```

#### v-on Shorthand

```html
<!-- full syntax -->
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```

They may look a bit different from normal HTML, but `:` and `@` are valid characters for attribute names and all Vue-supported browsers can parse it correctly. 

In addition, they do not appear in the final rendered markup. The shorthand syntax is totally optional, but you will likely appreciate it when you learn more about its usage later.