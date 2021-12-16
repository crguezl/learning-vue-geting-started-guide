## Component Props

### Prop Casing (camelCase vs kebab-case)

HTML attribute names are case-insensitive, so browsers will interpret any uppercase characters as lowercase. 

That means when you’re using **in-DOM templates**, *camelCased* prop names need to use their *kebab-cased* (hyphen-delimited) equivalents:

```js 
Vue.component('blog-post', {
  // camelCase in JavaScript
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
<!-- kebab-case in HTML -->
<blog-post post-title="hello!"></blog-post>
```

Again, if you’re using **string templates**, this limitation does not apply.
