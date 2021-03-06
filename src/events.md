
## Customized Events 

See [Custom Events](https://vuejs.org/v2/guide/components-custom-events.html) on the  Vue.js guide.


### vm.$emit( eventName, […args] )

See the Vue.js API specification on [`vm.$emit( eventName, […args]`](https://vuejs.org/v2/api/#vm-emit)

It triggers an event with name `eventName` on the current instance. 

**Any additional arguments will be passed into the listener’s callback function** potentially associated with the `v-on`.

```html 

<script>
Vue.component('magic-eight-ball', {
  data: function () {
    return {
      possibleAdvice: ['Yes', 'No', 'Maybe']
    }
  },
  methods: {
    giveAdvice: function () {
      var randomAdviceIndex = Math.floor(Math.random() * this.possibleAdvice.length)
      this.$emit('give-advice', this.possibleAdvice[randomAdviceIndex])
    }
  },
  template: `
    <button v-on:click="giveAdvice">
      Click me for advice
    </button>
  `
})
</script>

<div id="emit-example-argument">
  <magic-eight-ball v-on:give-advice="showAdvice"></magic-eight-ball>
</div>

<script>
let emitExampleArgument = new Vue({
  el: '#emit-example-argument',
  methods: {
    showAdvice: function (advice) {
      alert(advice)
    }
  }
})
</script>
```

The method `showAdvice` is triggered on the `give-advice` event. That is, when inside the `magic-eight-ball` component is executed the code `this.$emit('give-advice', this.possibleAdvice[randomAdviceIndex])`.  The last was triggered by the user click on the button `Click me for advice`.

When called, the method `showAdvice` receives as arguments the additional arguments of the `this.$emit('give-advice', this.possibleAdvice[randomAdviceIndex])`: that is,
the randomly generated element of the `possibleAdvice` array.

This mechanism allows to the root component to obtain information from the child component `magic-eight-ball`. See lab [crguezl/vuejs-components-basics-plan-picker-component](https://github.com/crguezl/vuejs-components-basics-plan-picker-component) for another example.


<script>
Vue.component('magic-eight-ball', {
  data: function () {
    return {
      possibleAdvice: ['Yes', 'No', 'Maybe']
    }
  },
  methods: {
    giveAdvice: function () {
      var randomAdviceIndex = Math.floor(Math.random() * this.possibleAdvice.length)
      this.$emit('give-advice', this.possibleAdvice[randomAdviceIndex])
    }
  },
  template: `
    <button v-on:click="giveAdvice">
      Click me for advice
    </button>
  `
})
</script>

<div id="emit-example-argument">
  <magic-eight-ball v-on:give-advice="showAdvice"></magic-eight-ball>
</div>

<script>
let emitExampleArgument = new Vue({
  el: '#emit-example-argument',
  methods: {
    showAdvice: function (advice) {
      alert(advice)
    }
  }
})
</script>