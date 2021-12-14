## Accesing Elements Directly Using ref


There are times when we want to access the DOM element. 
May be we want the position of an element in the page or 
we are using a library that manipulates the DOM and it need to access to specific elements.

We can use [ref](https://vuejs.org/v2/guide/components-edge-cases.html#Accessing-Child-Component-Instances-amp-Child-Elements)
to access the DOM element directly without having to use `querySelector`or one of the other native ways to select an element of the DON.

If you use `this.$ref.blah`in a component, it will refer to the element in that component, not anywhere else in the document.

In the following example, taken from [@refsvue2], the idea is that clicking the button will set the focus on the textarea below.

<div id="appRefs" class="execution">
  <editor></editor>
</div>

<script>
Vue.component("editor", {
    template: `
        <div>
            <h2>Editor</h2>
            <button @click="focusEditor">Focus Editor</button>
            <br/>
            <textarea ref="editorRef" name="" id="" cols="30" rows="3"></textarea>
        </div>
    `,
    methods: {
        focusEditor(e) {
            this.$refs.editorRef.focus()
        }
    }
})

new Vue({
    el: "#appRefs"
})
</script>

To achieve it we have to have access to the textarea DOM element and for that we use the `<textarea ref="editorRef" ...>`

When the button is clicked the execution of the method `focusEditor`  uses the [HTMLElement.focus()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) method to set the focus. To get the element we use  `this.$refs.editorRef`


```html
<div id="appRefs" class="execution">
  <editor></editor>
</div>

<script>
Vue.component("editor", {
    template: `
        <div>
            <h2>Editor</h2>
            <button @click="focusEditor">Focus Editor</button>
            <br/>
            <textarea ref="editorRef" name="" id="" cols="30" rows="3"></textarea>
        </div>
    `,
    methods: {
        focusEditor(e) {
            this.$refs.editorRef.focus()
        }
    }
})

new Vue({
    el: "#appRefs"
})
</script>
```

::: tip
`$ref`s are only populated after the component has been rendered, and they are not reactive. 

It is only meant as an escape hatch for direct child manipulation 
- you should avoid accessing `$refs` from within templates or computed properties.

In most cases, it’s best to avoid reaching into other component instances or manually manipulating DOM elements. 
There are cases, however, when it can be appropriate.
:::