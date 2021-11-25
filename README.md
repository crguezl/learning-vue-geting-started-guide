# Introduction to Vue.js

This are my notes from reading of the [Getting Started](https://vuejs.org/v2/guide/#Getting-Started) guide of Vue.js (v2)

Open the deployment in GitHub pages <https://crguezl.github.io/learning-vue-geting-started-guide/> of this `README.md`

## Simple example

```html
<!-- development version, includes helpful console warnings -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>


<h2>
  <div id="app">
    {{ message }}
  </div>
</h2>

<script>
  var app = new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue!'
    }
  })
</script>
```

<!-- development version, includes helpful console warnings -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>


<h2>
  <div id="app">
    {{ message }}
  </div>
</h2>

<script>
  var app = new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue!'
    }
  })
</script>

We have already created our very first Vue app! 

This looks pretty similar to rendering a string template, but Vue has
done a lot of work under the hood. <strong>The data and the DOM are now linked</strong>, 
and everything is now <strong>reactive</strong>!.

### Check in the developer's tools

<p>
  How do we know?
<ul>
  <li>Open your browser’s JavaScript console (right now, 
  <a href="https://crguezl.github.io/learning-vue-geting-started-guide/" target="_blank">on the GH page</a>) and </li>
  <li> set <code>app.message</code> to a
    different value. </li>
</ul>

You should see the rendered example above update accordingly.
</p>


## No interpolation occurs outside the Vue app entry point


<h3>{{ message }}</h3>


## The v-bind directive

Here we define a second entry point for a second Vue app object:

```html
<div id="app-2">
  <span v-bind:title="message">
    <strong>
      Hover your mouse over me for a few seconds
      to see my dynamically bound title!
    </strong>
  </span>
</div>


<script>
  var app2 = new Vue({
    el: '#app-2',
    data: {
      message: 'You loaded this page on ' + new Date().toLocaleString()
    }
  })
</script>
```

<div id="app-2">
  <span v-bind:title="message">
    <strong>
      Hover your mouse over me for a few seconds
      to see my dynamically bound title!
    </strong>
  </span>
</div>

<script>
  var app2 = new Vue({
    el: '#app-2',
    data: {
      message: 'You loaded this page on ' + new Date().toLocaleString()
    }
  })
</script>

Here we are encountering something new. 
  
1. The <code>v-bind</code> attribute you are seeing is called a
  <strong>directive</strong>. 
2. Directives are prefixed with <code>v-</code> to indicate that they are special attributes
  provided by Vue, and as you may have guessed, they apply special reactive behavior to the rendered DOM. 
3. Here, it is basically saying **keep this element’s <code>title</code> attribute up-to-date with the <code>message</code> propert on the Vue instance**.