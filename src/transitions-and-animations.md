## Transitions

Vue provides a transition wrapper component, allowing you to add entering/leaving transitions for any element or component in several  contexts like in Conditional rendering (using `v-if`)

```html 
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <style>
        .fade-enter-active,
        .fade-leave-active {
            transition: opacity .5s;
        }

        .fade-enter,
        .fade-leave-to  {
            opacity: 0;
        }
    </style>
</head>
<body>
    <div id="demo">
        <button v-on:click="show = !show">
            Toggle
        </button>
        <transition name="fade">
            <p v-if="show">hello</p>
        </transition>
    </div>

    <script>
        new Vue({
            el: '#demo',
            data: {
                show: true
            }
        })
    </script>
```

See it working at [examples/css-transitions-2.html](examples/css-transitions-2.html)


The way that this works  is that Vue takes the name of the transition `"fade"` and uses it to add 
CSS classes to the contained element (the paragraph `<p>hello</p>`) at various points through the transition.

Two types of transition **enter** and **leave**, are applied when the element is added and removed from the document, respectively.

There are six classes applied for enter/leave transitions.

1. {name}-enter: Starting state for enter. Added before element is inserted, removed one frame after element is inserted.
2. {name}-enter-active: Active state for enter. Applied during the entire entering phase. Added before element is inserted, removed when transition/animation finishes. This class can be used to define the duration, delay and easing curve for the entering transition.
3. {name}-enter-to: Only available in versions 2.1.8+. Ending state for enter. Added one frame after element is inserted (at the same time {name}-enter is removed), removed when transition/animation finishes.
4. {name}-leave: Starting state for leave. Added immediately when a leaving transition is triggered, removed after one frame.
5. {name}-leave-active: Active state for leave. Applied during the entire leaving phase. Added immediately when leave transition is triggered, removed when the transition/animation finishes. This class can be used to define the duration, delay and easing curve for the leaving transition.
6. {name}-leave-to: Only available in versions 2.1.8+. Ending state for leave. Added one frame after a leaving transition is triggered (at the same time {name}-leave is removed), removed when the transition/animation finishes.

For more details see [Transitioning Single Elements/Components](https://vuejs.org/v2/guide/transitions.html)
