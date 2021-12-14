## Transitions and Animations

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