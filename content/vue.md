---
title: Vue
description: How to use Vue.js with Meteor.
---
<h2 id="introduction">Introduction</h2>

Vue has an excellent [guide and documentation](https://vuejs.org/v2/guide/). 
This guide is about integrating it with Meteor. That's why we take a full-stack approach 
where Meteor serves as the main platform with Vue as its render engine.

<h3 id="why-vue">Vue with Meteor?</h3>

[Vue](https://vuejs.org/v2/guide/) (pronounced /vjuː/, like view) is a progressive framework 
for building user interfaces. Some really nice Vue frameworks like [Nuxt.js](https://nuxtjs.org) 
and [Gridsome](https://gridsome.org/) exist already. So why use Vue with Meteor? 




Meteor is a platform that can be used as BFF (Backend For Frontend API) that supports one or more 
external Vue applications which are built for example with Nuxt or Gridsome. It could also 
be used as a full-stack application where the API and app work on 1 container. Both approaches 
have pro's and con's. In this guide both options will be described. 

integrations for all parts of the application. 
Its default is a nicely integrated full-stack application that works without boilerplate or 
configuration. With one command you will have a fully functional Vue application with an API.

You could also go

For example, you could use Vue on top of Meteor as one stack. Meteor will take care of 
building, HMR, the API and guidance on how to fit it all together. No webpack configuration, 
no building of Rest endpoints, no figuring out which API framework to use and how to sync 
clientside data with the server.

You could also run a separate Vue app with for example Nuxt or Gridsome and have Meteor run 
as a BFF (Backend For Frontend) with an optimized API.

<h2 id="getting-started">Getting Started</h2>

Create your new Vue project

```sh
meteor create --vue my-vue-project
```

Start Meteor 

```sh
meteor
```

A small boilerplate has now been set-up to get you going. If you've read some basic tutorials about Vue.js, you are good to go. 

<h3 id="manual-setup">Manual setup</h3>

You might want a manual setup if you deal with an existing project or if want to use 
variations of the existing packages. Essentially the whole default setup relies on 
packages from the [Vue Meteor](https://github.com/meteor-vue/vue-meteor) repository.

**Step 1**. Install tooling that allows loading and reloading Vue components.

```sh
meteor add akryum:vue-component
```

The above package gives HMR (Hot Module Reloading) and allows loading of `.vue` files.
Here's the [documentation of this package](https://github.com/meteor-vue/vue-meteor/tree/master/packages/vue-component).

**Step 2**. Install Vue Meteor Tracker

In order to support Meteor functionality in Vue components, you will need to add another 
package called [Vue Meteor Tracker](https://github.com/meteor-vue/vue-meteor-tracker). 
This package integrates Meteor's [Tracker](https://docs.meteor.com/api/tracker.html) and 
grants the ability to use all the reactive Meteor tools in Vue components. Some of these tools are 
like: [Minimongo collections](https://docs.meteor.com/api/collections.html), 
[Subscriptions](https://docs.meteor.com/api/pubsub.html#Meteor-subscribe), 
[ReactiveVar](https://docs.meteor.com/api/reactive-var.html), 
[Session](https://docs.meteor.com/api/session.html) and
[Meteor.user()](https://docs.meteor.com/api/accounts.html#Meteor-user).

```sh
meteor npm install --save vue-meteor-tracker
```

Now connect the plugin to Vue like below example. Assuming a basic setup, the 
recommended place to put this is in a separate file called plugins.js.

```js
import VueMeteorTracker from 'vue-meteor-tracker'

Vue.use(VueMeteorTracker)
```

<h3 id="routing">Routing</h3>

Routing for Vue with Meteor is best done simply with the default 
[Vue Router](https://router.vuejs.org/) package. The below illustrates this:

```sh
meteor npm install vue-router
```
Simply create a routes.js file and add the routes.

```js
import VueRouter from 'vue-router'

import Home from './pages/Home'
import Forum from './pages/Forum'
import NotFound from './pages/NotFound'

const routes = [
  {
    path: '/',
    name: 'home',
    component: Home
  },
  {
    path: '/forum',
    name: 'forum',
    component: Forum
  },
  {
    path: '*',
    name: 'notFound',
    component: NotFound
  },
];

export default new VueRouter({
  mode: 'history',
  routes
})
```

Add the router to the Vue instance like below:

```js
import router from './router'

import App from './App.vue'

Meteor.startup(() => {
  new Vue({
    router,
    render: h => h(App),
  }).$mount('#app');
})

```

That's it. You're set. No special stuff. Just use the router as you always do in other
 Vue apps: `<router-view>` and `<router-link>`.

<h2>Integrating API's</h2>

By default Meteor uses Minimongo + DDP to communicate data from server to clientside. 
It's a very powerful feature, but very often, you might want to use a different tool like 
ApolloJS or a simple Rest API.

This section will explain the practices of how to integrate these tools with Meteor + Vue.

<h3 id="meteor-methods">Meteor Methods</h3>

Meteor Methods are Meteor's alternative to Rest API methods. Methods are much 
simpler and have a lot of powerful features that no other framework provides with zero 
boilerplate. Things that are never worth time-wise and complexity wise, but greatly 
improve user experience. To mention one feature: Its integration with Minimongo to provide 
[Optimistic UI](https://blog.meteor.com/optimistic-ui-with-meteor-67b5a78c3fcf).

So how do Meteor methods work in Vue? Simple:

Lets assume you have the below method. That's essentially your API "endpoint":

```js
Meteor.methods({
  createArticle(article) {
    schema.validate(article)
    ArticlesCollection.insert(article)
  }
})
```

Here's a link to the full guide on [Meteor Methods](/methods.html)
for a full understanding on how Meteor methods work

In Vue, you could just call it in a Vue method like below:
```html
<script>
 import { Meteor } from 'meteor/meteor'

 export default {
  data() {
    return {
      title: '',
      body: ''
    }
  },
  methods: {
    handleSubmit() {
      Meteor.call('createArticle', {
        title: this.title,
        body: this.body
      })
    }
  }
 }
</script>
```

That's it. No determining the JSON structure, figuring out headers, implementing clients. 
It just works.

<h3 id="minimongo-and-ddp">Minimongo and DDP</h3>

- **Minimongo** is Meteor's Realtime client- and server-side 'cache'. It works seamlessly 
with [MongoDB](https://www.mongodb.com/), but its not a requirement. 
It also works nicely without it.

- **DDP** is an API protocol which is used by Minimongo to send data in realtime to and from the client. 

Minimongo and DDP work together to provide the whole back to front realtime API - 
allowing for full-stack reactivity. Essentially you subscribe to data with parameters. On the 
server you can use a publication function with these parameters to query Minimongo, 
by returning the result, it 'magically' fills Minimongo on the client. 
Since Minimongo is reactive, you can just connect it to your components.

Minimongo + Methods and DDP provide a powerful feature called 
[Optimistic UI](https://blog.meteor.com/optimistic-ui-with-meteor-67b5a78c3fcf).
Check it out. It greatly enhances user experience and the perception of "instant updates". 
You are getting this without having to do anything!


Here's a short example of an endpoint / publication on the server:

```js
import ProductsCollection from './collections/products'

Meteor.publish('top20Products', function(params) {
  return ProductsCollection.find({}, { limit: 20, sort: { rank: -1 } })
})
```

In your Vue component you can now do this:

```html
<template>
<ul>
  <li v-for="item in items" :key="item.id">{{ item.title }}</li>
</ul>
</template>

<script>
import ProductsCollection from './collections/products'
export default {
  meteor: {
    $subscribe: {
      'top20Products': [],
    },
    items () {
      return ProductsCollection.find({})
    },
  },
}
</script>
```

The above Vue component subscribes to the `top20Products` publication. This 'magically' puts 
products from the server into the client products collection. The component returns the result 
from the clientside `ProductsCollection` as `items` to the template.

<h3>Apollo Server / Client</h3>

-- coming up --

<h3>Rest API's</h3>

-- coming up --

<h2 id="best-practices">Best Practices</h2>

Meteor nor Vue enforces you to structure your project and code a certain way. However, 
there are practices that really work well in most cases. 

This section highlights some of these best practices for Meteor in combination with Vue. 
Vue has a strong [Vue Style Guide](https://vuejs.org/v2/style-guide/). It's a 
recommended read, because the rules in that guide form the base of this section.

> If you spot any inconsistencies between this guide and the Vue guide, then please consider 
> contributing to this guide or to create an issue on [this guide's repository](https://github.com/meteor/guide)

<h3 id="smart-vs-reusable-components">Smart vs Reusable components</h3>

Meteor has a [very nice guide on smart vs reusable components](/ui-ux.html#components).
It follows the principles of SOLID and DRY, where functions, classes and components should 
ideally just do 1 thing. For components that means that some of them 
should just be responsible for providing design with html (the Design System Implementation) 
and others to connect the environment, app and 
[domain state](https://medium.com/@abhiaiyer/domain-state-vs-ui-state-768c1271a41d). 

Lets refer to them as: 

- **Domain component**: Domain + App integration
- **Design component**: Design System Implementation 

This component contains both Design and Domain stuff. 

**Don't do this!**
```html
<template>
  <div>
    <ul v-if="products.length">
      <li v-for="product in products" :key="product.id">{{ product.title }}</li>
    </ul>
  </div>
</template>

<script>
  import ProductsCollection from '../collections/products'

 export default {
  meteor: {
    $subscribe: {
      products: []
    },
    products() {
      return ProductsCollection.find()
    }
  }
 }
</script>

<style scoped>
ul {
  padding: 0;
  list-style: none;
}
</style>
```

It might be tempting to structure components like above, but there are downsides. 
Consider splitting up these components based on if they are Design or Domain types:

*Domain Component / Container. Takes care of connecting Meteor and composes Design components* 
```html
<template>
  <overview-wrapper>
    <product-list v-if="products.length">
      <products-list-item v-for="product in products" :title="product.title">
    </product-list>
  </overview-wrapper>
</template>

<script>
  import ProductsCollection from '../collections/products'

 export default {
  meteor: {
    $subscribe: {
      products: []
    },
    products() {
      // Ensure that the products have the properties to fit into a standard list component
      return ProductsCollection.find().map(product => ({
        ...product,
        title: product.name
      }))
    }
  }
 }
</script>
```

Design components. Purely style and markup (no Meteor):

*overview-wrapper*
```html
<template>
  <section class="overview">
    <slot />
  </section>
</template>

<style scoped>
.overview {
  margin: 20px auto;
}
</style>
```

*product-list*
```html
<template>
  <ul>
    <slot />
  </ul>
</template>

<style scoped>
ul {
  padding: 0;
  list-style: none;
}
</style>
```

*product-list-item*
```html
<template>
  <li>
    {{ title }}
  </li>
</template>

<script>
export default {
  props: {
    title: String
  }
}
</script>

<style scoped>
li {
  position: relative;
  background-color: #cccccc;
}
</style>
```

By splitting design from domain state, you will be able to easily reuse your Vue design implementation on other applications 
in the form of a design system like [Vuetify](https://vuetifyjs.com/en/) and [Bootstrap Vue](https://bootstrap-vue.org/). 

Another reason is reducing dependency specifically on a backend (like Meteor). 
You can now switch API tooling just by changing the containers part, but the components will 
remain untouched. If you nail this, your project will become highly scalable and relatively 
easy to refactor and maintain.

<h3>Naming conventions</h3>

As described in the Vue Style guide - Use multi-word component names. This will not only 
make it easy to distinguish components from html elements, but also helps you structure 
files and folders based on clear 'categories' of components.


<h3>Folder structure</h3>

<h3>Vue + Minimongo</h3>

<h2>SSR and Hydration</h2>

<h2>Meteor as API only</h2>

<h3>Integration in Nuxt</h3>

```sh
npm i @nuxtjs/meteor
```

<h2>Boilerplates</h2>

<h3>Meteor + Vue + Minimongo</h3>

<h3>Meteor + Vue + Apollo</h3>

<h3>Meteor + Vue + Rest</h3>
