## Template Syntax

Vue.js uses an HTML-based template syntax that allows you to declaratively bind the rendered DOM to the underlying Vue instance’s data. 

All Vue.js templates are valid HTML that can be parsed by spec-compliant browsers and HTML parsers.

Under the hood, Vue compiles the templates into Virtual DOM[^virtualDom] render functions. 

[^virtualDom]: The virtual DOM (VDOM) is a programming concept where an ideal, or *virtual*, representation of a UI is kept in memory and synced with the “real” DOM by a library like Vue or React

Combined with the reactivity system, Vue is able to intelligently figure out the minimal number of components to re-render and apply the minimal amount of DOM manipulations when the app state changes.

If you are familiar with Virtual DOM concepts and prefer the raw power of JavaScript, [you can also directly write render functions instead of templates](https://vuejs.org/v2/guide/render-function.html), with optional JSX support.