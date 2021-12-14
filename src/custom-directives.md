## Custom Directives

In addition to the built-in directives it is possible to build your own custom directives.

An example would be focusing on an `input` element:

```html
<script>
    // Register a global custom directive called `v-focus`
    Vue.directive('focus', {
        // When the bound element is inserted into the DOM...
        inserted: function (el) {
            // Focus the element
            el.focus()
        }
    })
</script>

<div id="appCustomDirective" class="execution">
    <input> <br/>
    <input v-focus> <br/>
    <input>
</div>

<script>
    new Vue({
        el: "#appCustomDirective"
    })
</script>
```

Follow this [link](examples/custom-directives.html) to see the example working.

A directive definition object can provide several hook functions (all optional) like `bind` or `inserted`.
`inserted` is called when the bound element has been inserted into its parent node.
