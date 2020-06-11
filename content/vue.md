---
title: Vue
description: How to use Vue.js with Meteor.
---
<h2 id="introduction">Introduction</h2>
[Vue](https://vuejs.org/v2/guide/) (pronounced /vjuː/, like view) is a progressive framework for building user interfaces. 
Unlike other monolithic frameworks, Vue is designed from the ground up to be incrementally adoptable. 
The core library is focused on the view layer only, and is easy to pick up and integrate 
with other libraries or existing projects. On the other hand, Vue is also perfectly 
capable of powering sophisticated Single-Page Applications when used in combination 
with [modern tooling](https://vuejs.org/v2/guide/single-file-components.html) 
and [supporting libraries](https://github.com/vuejs/awesome-vue#components--libraries).

Vue has an excellent [guide and documentation](https://vuejs.org/v2/guide/). This guide is about integrating it with Meteor.

<h3 id="why-use-vue-with-meteor">Why use Vue with Meteor</h3>

Vue is a frontend library, like React, Blaze and Angular. 

Some really nice frameworks exist already for Vue. So why use Meteor? Lets take 
[Nuxt.js](https://nuxtjs.org) for example. Nuxt aims to create a UI framework flexible 
enough for use in any scenario, but rigid enough to provide solid best practices. 
Nuxt is just one piece of the puzzle though! We still need a data-layer. A proper BFF (Backend 
For Frontend). This is where Meteor comes in. Vue and Meteor is magic. Even Nuxt + Meteor is 
a very good option if you want to have your render layer and API strictly separate!

Meteor's build tool and Pub/Sub API (or Apollo) provides Vue with an API that you 
would normally have to integrate and discover for yourself. Meteor greatly reduces the amount 
of code you have to write and provides best practices for almost any use-case you can think of.

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

<h2 id="design-systems">Design Systems</h2>
 
<h3>Vuetify</h3>

<h3>Bootstrap Vue</h3>
 
<h2>Integrating API's</h2>

<h3>Minimongo, Methods and DDP</h3>

<h3>Apollo Server / Client</h3>

<h3>Rest API's</h3>

Meteor supports multiple types of API. For example, you can use it with Apollo Server + Client, 
or as a Rest API. However, by default, Meteor also uses its own protocol called DDP.

<h2>Best Practices</h2>

There are many ways to structure any app. That also counts for Meteor and for Vue. But over time 
we did figure out some practices that work well for any project.

<h3></h3>

As you can see the routes are being imported

<h3>Naming conventions</h3>

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
