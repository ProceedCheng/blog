### Is that any way to shared a global var and function of parent to all child components? #1309



There are several ways:

1. Like what you did, import `helper.js` when you need it. This is actually the recommended approach - although repetitive, it clearly shows where the method comes from.
2. Just add it to `Vue.prototype`. For example, `Vue.prototype.$onPromiseError = ...` so all instances can just `this.$onPromiseError(err)`. Better yet, group all your global helpers into a mixin, so you know where to look for them and maintain them.
3. Make it a separate global, like you did. This is fine when the scope is small and you put everything under your own namespace object.



[Is that any way to shared a global var and function of parent to all child components?](https://github.com/vuejs/vue/issues/1309)