## Computed Properties and Watchers

### Computed Properties

In-template expressions are very convenient, but they are meant for simple operations. Putting too much logic in your templates can make them bloated and hard to maintain. For example:

```html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

At this point, the template is no longer simple and declarative. You have to look at it for a second before realizing that it displays message in reverse. The problem is made worse when you want to include the reversed message in your template more than once.

That’s why for any complex logic, you should use a computed property.

#### Basic Example

```html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>

<script>
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // a computed getter
    reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
```

Result:

<div id="example" class="execution">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>

<script>
let vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // a computed getter
    reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  }
})
</script>


Here we have declared a computed property `reversedMessage`. 

The function we provided will be used as the getter function for the property `vm.reversedMessage`:

```js
console.log(vm.reversedMessage) // => 'olleH'
vm.message = 'Goodbye'
console.log(vm.reversedMessage) // => 'eybdooG'
```

You can open the console and play with the example `vm` yourself. The value of `vm.reversedMessage` is always dependent on the value of `vm.message`.

**You can data-bind to computed properties in templates just like a normal property**.

Vue is aware that `vm.reversedMessage` depends on `vm.message`, so it will update any bindings that depend on `vm.reversedMessage` when `vm.message` changes. 

And the best part is that we’ve created this dependency relationship declaratively:

the computed getter function has no side effects, which makes it easier to test and understand.

#### Computed Caching vs Methods

You may have noticed we can achieve the same result by invoking a method in the expression:

```js
<p>Reversed message: "{{ reverseMessage() }}"</p>
// in component
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

Instead of a **computed** property, we can define the same function as a **method**. 
For the end result, the two approaches are indeed exactly the same. 

However, the difference between methods and the computed properties is that the **computed properties are cached based on their reactive dependencies**. 

**A computed property will only re-evaluate when some of its reactive dependencies have changed**. 

This means as long as message has not changed, multiple access to the `reversedMessage` computed property will immediately return the previously computed result without having to run the function again.

This also means the following computed property will never update, because `Date.now()` is not a reactive dependency:

```js
computed: {
  now: function () {
    return Date.now()
  }
}
```

In comparison, **a method invocation will always run the function whenever a re-render happens**.

#### Why do we need caching? 

Imagine we have an expensive computed property `A`, which requires looping through a huge Array and doing a lot of computations. 

Then we may have other computed properties that in turn depend on `A`. 

Without caching, we would be executing `A`’s getter many more times than necessary! In cases where you do not want caching, use a method instead.

#### Computed vs Watched Property

Vue does provide a more generic way to observe and react to data changes on a Vue instance: **watch properties**. 

When you have some data that needs to change based on some other data, it is tempting to overuse watch. However, it is often a better idea to use a computed property rather than an imperative watch callback. Consider this example:

```html
<div id="demo">{{ fullName }}</div>
```
```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

The above code is imperative and repetitive. Compare it with a computed property version:

```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

Much better, isn’t it?

#### Computed Setter

Computed properties are by default getter-only, but you can also provide a setter when you need it:

```js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

Now when you run `vm.fullName = 'John Doe'`, the setter will be invoked and `vm.firstName` and `vm.lastName` will be updated accordingly.

### Watchers

While computed properties are more appropriate in most cases, there are times when a custom watcher is necessary. 

That’s why Vue provides a more generic way to react to data changes through the `watch` option. 

This is most useful when you want to perform asynchronous or expensive operations in response to changing data.

The following example taken from [@macrae2018vue] shows a Vue app where we have an `input` where a user can type in and displays 
on the page what was the value of the input 5 seconds ago.


```html
<div id="watch-5-seconds-ago" class="execution">
 <input type="text" v-model="inputValue">
 <p>Five seconds ago, the input was: "{{ oldInputValue }}"</p>
</div>

<script>
new Vue({
  el: "#watch-5-seconds-ago",
  data: {
    inputValue: '',
    oldInputValue: ''
  },
  watch: {
    inputValue(newValue, oldValue) {
      const store = newValue;
      setTimeout(() => {
        this.oldInputValue = store;
      }, 5000);
    }
  }
})
</script>
```

<div id="watch-5-seconds-ago" class="execution">
 <input type="text" v-model="inputValue">
 <p>Five seconds ago, the input was: "{{ oldInputValue }}"</p>
</div>

<script>
new Vue({
  el: "#watch-5-seconds-ago",
  data: {
    inputValue: '',
    oldInputValue: ''
  },
  watch: {
    inputValue(newValue, oldValue) {
      const store = newValue;
      setTimeout(() => {
        this.oldInputValue = store;
      }, 5000);
    }
  }
})
</script>

Remember that `v-model` is a two-way binding for form inputs. 
It combines `v-bind`, which brings a JavaScript value from `.data` into the template, and `v-on:input` to update the JavaScript value. 

The `v-model` directive works with all the basic HTML input types (`text`, `textarea`, `number`, `radio`, `checkbox`, `select`).

The `watch` entry of the Vue instance has to be an object where 

1. **keys** are expressions to watch (`inputValue`) and 
2. **values** are the corresponding callbacks `function(newValue, oldValue) { ... }`
3. Callbacks are called with two arguments when their watched property is changed: the new value and the old value
4. The value can also be a string of a method name, or an Object that contains additional options. 
5. The Vue instance will call [$watch()](https://vuejs.org/v2/api/#vm-watch) for each entry in the object at instantiation.


#### Answering your Question with the yesno.wtf API

The following examples has an input where you can formulate a question. 

The Vue app binds the `input` value to the `question` data variable and watches for changes 
of `question`. 

The question will be sent using `axios.get` for an answer to https://yesno.wtf/api.

The simple Yes or No API is made for automating boolean decision making within mobile environments. API calls will return a "Yes", "No" or "Maybe." Returns include fun links to comical Gifs.

We use lodash method `_.debounce` to limit how often 
we access https://yesno.wtf/api, waiting until the user has completely
finished typing before making the ajax request. 
(To learn more about the `_.debounce` function (and its cousin `_.throttle`), visit: <https://lodash.com/docs#debounce>

```html
<div id="watch-example" class="execution">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
<!-- Since there is already a rich ecosystem of ajax libraries    -->
<!-- and collections of general-purpose utility methods, Vue core -->
<!-- is able to remain small by not reinventing them. This also   -->
<!-- gives you the freedom to use what you're familiar with.      -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // whenever question changes, this function will run
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // _.debounce is a function provided by lodash to limit how
    // often a particularly expensive operation can be run.
    // In this case, we want to limit how often we access
    // yesno.wtf/api, waiting until the user has completely
    // finished typing before making the ajax request. To learn
    // more about the _.debounce function (and its cousin
    // _.throttle), visit: https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: async function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      try {
        let response = await axios.get('https://yesno.wtf/api')
        vm.answer = _.capitalize(response.data.answer)
      }
      catch (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
      }
    }
  }
})
</script>
```

Result:


<div id="watch-example" class="execution">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
<!-- Since there is already a rich ecosystem of ajax libraries    -->
<!-- and collections of general-purpose utility methods, Vue core -->
<!-- is able to remain small by not reinventing them. This also   -->
<!-- gives you the freedom to use what you're familiar with.      -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // whenever question changes, this function will run
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // _.debounce is a function provided by lodash to limit how
    // often a particularly expensive operation can be run.
    // In this case, we want to limit how often we access
    // yesno.wtf/api, waiting until the user has completely
    // finished typing before making the ajax request. To learn
    // more about the _.debounce function (and its cousin
    // _.throttle), visit: https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: async function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      try {
        let response = await axios.get('https://yesno.wtf/api')
        vm.answer = _.capitalize(response.data.answer)
      }
      catch (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
      }
    }
  }
})
</script>

In this example this bidirectional behavior of `v-model` constitutes a problem  😢 since each time the user press a key the input changes producing a call to the [watch handler function](https://vuejs.org/v2/api/#watch) associated to changes in `question`.

```js
  watch: {
    // whenever question changes, this function will run
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  ```

If we instead were calling directly `this.getAnwser()` then there is the risk of  
calling `axios.get('https://yesno.wtf/api')` for each pressed key.
Something has to be done regarding this problem.

This is an example of how to use a lifecycle hook seen in section [Lifecycle Diagram](#lifecycle-diagram). When the Vue instance is created we use the `created` hook to create the `debouncedGetAnswer` method from the `getAnswer` method:

```js
  created: function () {
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
```

The call to the Lodash method [`_.debounce(this.getAnswer, 500)`](https://lodash.com/docs/4.17.15#debounce) 

```js
_.debounce(func, [wait=0], [options={}])
```

creates a debounced function that delays invoking `func` until after `wait` milliseconds have elapsed since the last time the debounced function was invoked.

That alleviates the risk to throttle the network. Furthermore, inside `getAnswer` we check for the presence of a `?` character:

```js
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
```
Doing nothing until the question mark appears.

In this case, using the `watch` option allows us 

1. To perform an asynchronous operation (accessing an API), 
2. With additional effort, to limit how often we perform that operation, and 
3. Set intermediary states until we get a final answer. 

None of that would be possible with a computed property.


::: tip
Note that you should not use an arrow function to define a watcher (e.g. `searchQuery: newValue => this.updateAutocomplete(newValue))`. 

The reason is arrow functions bind the parent context, so this will not be the Vue instance as you expect and `this.updateAutocomplete` will be undefined.
:::

In addition to the `watch` option, you can also use the imperative 
[`vm.$watch` API](https://vuejs.org/v2/api/#vm-watch).


### Exercise: Examining Update Events with Computed Properties in Vue.js

Read *Examining Update Events with Computed Properties in Vue.js* [@computedproperties], reproduce the examples and make a report in folder 
`examining-update-events-with-computed-properties` of the assignment repo.


### Filters (Not supported in 3.x)

As other template languages, Vue allows the use of *filters*.

Filters are usable in two places: mustache interpolations and `v-bind` expressions 
(the latter supported in 2.1.0+). 

Filters should be appended to the end of the JavaScript expression, denoted by the *pipe* symbol:

```html
<div id="appfilter" class="execution">
  <p>Product one cost: {{ productOneCost | formatCost }}</p> 
  <p>Product two cost: {{ productTwoCost | formatCost }}</p> 
  <p>Product three cost: {{ productThreeCost | formatCost }}</p> 
</div>
<script>
  new Vue({
    el: "#appfilter",
    data: {
      productOneCost: 998,
      productTwoCost: 2399,
      productThreeCost: 5300
    },
    filters: {
      formatCost(value) {
        return (value/100).toFixed(2)+"€";
      }
    }
  })
</script>
``` 

<div id="appfilter" class="execution">
  <p>Product one cost: {{ productOneCost | formatCost }}</p> 
  <p>Product two cost: {{ productTwoCost | formatCost }}</p> 
  <p>Product three cost: {{ productThreeCost | formatCost }}</p> 
</div>
<script>
  new Vue({
    el: "#appfilter",
    data: {
      productOneCost: 998,
      productTwoCost: 2399,
      productThreeCost: 5300
    },
    filters: {
      formatCost(value) {
        return (value/100).toFixed(2)+"€";
      }
    }
  })
</script>

In Vue 3.x, filters are removed and no longer supported. 
Instead, the Vue authors recommend replacing them with method calls or computed properties.

At the end, a filter is only a function applied to the resulting value and thus we can use a method instead:

```js
<div id="appFilterTransformed" class="execution">
  <p>Product one cost:   {{ formatCost(productOneCost) }}</p> 
  <p>Product two cost:   {{ formatCost(productTwoCost) }}</p> 
  <p>Product three cost: {{ formatCost(productThreeCost) }}</p> 
</div>
<script>
  new Vue({
    el: "#appFilterTransformed",
    data: {
      productOneCost: 998,
      productTwoCost: 2399,
      productThreeCost: 5300
    },
    methods: {
      formatCost(value) {
        return (value/100).toFixed(2)+"€";
      }
    }
  })
</script>
```

<div id="appFilterTransformed" class="execution">
  <p>Product one cost:   {{ formatCost(productOneCost) }}</p> 
  <p>Product two cost:   {{ formatCost(productTwoCost) }}</p> 
  <p>Product three cost: {{ formatCost(productThreeCost) }}</p> 
</div>
<script>
  new Vue({
    el: "#appFilterTransformed",
    data: {
      productOneCost: 998,
      productTwoCost: 2399,
      productThreeCost: 5300
    },
    methods: {
      formatCost(value) {
        return (value/100).toFixed(2)+"€";
      }
    }
  })
</script>
