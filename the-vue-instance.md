---
title: The Vue Instance
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
    @djirdeh2018fullstacks
--- 

Every Vue application starts by creating a new Vue instance with the Vue function:

```js
var vm = new Vue({
  // options
})
```

Although not strictly associated with the MVVM pattern, Vue’s design was partly inspired by it. As a convention, we often use the variable vm (short for ViewModel) to refer to our Vue instance.

When you create a Vue instance, you pass in an options object. See the [API]. 

[API]: https://vuejs.org/v2/api/#Options-Data

These are the main proeprties of the options object:

-   [data](https://vuejs.org/v2/api/#data){.section-link}
-   [props](https://vuejs.org/v2/api/#props){.section-link}
-   [propsData](https://vuejs.org/v2/api/#propsData){.section-link}
-   [computed](https://vuejs.org/v2/api/#computed){.section-link}
-   [methods](https://vuejs.org/v2/api/#methods){.section-link}
-   [watch](https://vuejs.org/v2/api/#watch){.section-link}


A Vue application consists of a root Vue instance created with `new Vue`, optionally organized into a tree of nested, reusable components. 

For example, a todo app’s component tree might look like this:

```
Root Instance
└─ TodoList
   ├─ TodoItem
   │  ├─ TodoButtonDelete
   │  └─ TodoButtonEdit
   └─ TodoListFooter
      ├─ TodosButtonClear
      └─ TodoListStatistics
```