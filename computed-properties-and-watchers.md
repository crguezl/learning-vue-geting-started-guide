## Computed Properties and Watchers

In-template expressions are very convenient, but they are meant for simple operations. Putting too much logic in your templates can make them bloated and hard to maintain. For example:

```html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

At this point, the template is no longer simple and declarative. You have to look at it for a second before realizing that it displays message in reverse. The problem is made worse when you want to include the reversed message in your template more than once.

That’s why for any complex logic, you should use a computed property.

### Basic Example

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


Here we have declared a computed property `reversedMessage`. 

The function we provided will be used as the getter function for the property `vm.reversedMessage`:

```js
console.log(vm.reversedMessage) // => 'olleH'
vm.message = 'Goodbye'
console.log(vm.reversedMessage) // => 'eybdooG'
```

You can open the console and play with the example vm yourself. The value of `vm.reversedMessage` is always dependent on the value of `vm.message`.

You can data-bind to computed properties in templates just like a normal property.

Vue is aware that `vm.reversedMessage` depends on `vm.message`, so it will update any bindings that depend on `vm.reversedMessage` when `vm.message` changes. 

And the best part is that we’ve created this dependency relationship declaratively: the computed getter function has no side effects, which makes it easier to test and understand.

### Computed Caching vs Methods

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

Instead of a computed property, we can define the same function as a method. 
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

