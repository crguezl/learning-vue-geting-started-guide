---
title: Introduction to Vue.js
subtitle: Course 21/22
author: Casiano
date:  \today
css: index.css
link-citations: true
#urlcolor: "blue"
# csl: "./acm.csl"
# output: powerpoint_presentation
references:
- type: article-journal
  id: WatsonCrick1953
  author:
  - family: Watson
    given: J. D.
  - family: Crick
    given: F. H. C.
  issued:
    date-parts:
    - - 1953
      - 4
      - 25
  title: 'Molecular structure of nucleic acids: a structure for
    deoxyribose nucleic acid'
  title-short: Molecular structure of nucleic acids
  container-title: Nature
  volume: 171
  issue: 4356
  page: 737-738
  DOI: 10.1038/171737a0
  URL: https://www.nature.com/articles/171737a0
  language: en-GB
nocite: |
    @djirdeh2018fullstack
---

---
<!--
https://stackoverflow.com/questions/48429998/data-prefix-is-added-to-custom-attributes-how-to-prevent-this
-->
This are my notes written in markdown from reading of the [Getting Started](https://vuejs.org/v2/guide/#Getting-Started) guide of Vue.js (v2)

They have been ellaborated using pandoc.

To see the result, you can open the deployment in the  GitHub pages <https://crguezl.github.io/learning-vue-geting-started-guide/> of the repo containing the notes.

## Simple example

We have inserted this HTML code into this markdown page and then used [pandoc](https://pandpc.org) to translate it to HTML:

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


<h2 class="execution">
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

This message appears verbatim:

```html
<h3>{{ message }}</h3>
```

because it is outside the element to wich Vue has been anchored.

<h3 class="execution">
{{ message }}
</h3>

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

<div id="app-2" class="execution">
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
3. Here, it is basically saying **keep this element’s <code>title</code> attribute** 
   up-to-date with the <code>message</code> property on the Vue instance.

If you open up your JavaScript console again and enter 

<code>app2.message = 'some new message'</code>, 

you’ll once again see that the bound HTML - in this case the <code>title</code> attribute - has been updated.


## Conditionals 

It’s easy to toggle the presence of an element, too:

```html
<div id="app-3">
  <span v-if="seen">Now you see me</span>
</div>

<script>
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
</script>
```

This example doesn't work due to `pandoc`modifying the directive `v-if`. 
Go to the HTML and change it.



<div id="app-3" class="execution">
  <span v-if="seen">Now you see me</span>
</div>

<script>
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
</script>



## Loops: v-for

There are quite a few other directives, each with its own special functionality. 

For example, the <code>v-for</code> directive can be used for displaying a list of items using the data from an Array:

```html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>

<script>
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
})
</script>
```

<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>

<script>
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
})
</script>


## Handling User Input


```html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>

<script>
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
</script>
```

<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>

<script>
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
</script>


## v-model


Vue also provides the v-model directive that makes **two-way binding** between 
**form input** and **app state** a breeze:

```html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>

<script>
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
```

<div id="app-6" class="execution">
  <p>{{ message }}</p>
  <input v-model="message">
</div>

<script>
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
</script>

## Composing with Components

## References
