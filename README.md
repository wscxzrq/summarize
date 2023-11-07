# 总结
# vue2Mixin

当组件对象与混入对象存在同名选项时，这些选项将会以恰当的方式进行```合并```

- 数据对象在内部会进行```递归合并```，并在发生冲突时以组件数据优先

  ```js
  var mixin = {
    data: function () {
      return {
        message: 'hello',
        foo: 'abc'
      }
    }
  }
  
  new Vue({
    mixins: [mixin],
    data: function () {
      return {
        message: 'goodbye',
        bar: 'def'
      }
    },
    created: function () {
      console.log(this.$data)
      // => { message: "goodbye", foo: "abc", bar: "def" }
    }
  })
  ```

- 同名的钩子函数将合并为一个```数组```，因此都将被调用，``混入对象的钩子函数会在组件自身钩子函数之前调用``

  ```JS
  var mixin = {
    created: function () {
      console.log('混入对象的钩子被调用')
    }
  }
  
  new Vue({
    mixins: [mixin],
    created: function () {
      console.log('组件钩子被调用')
    }
  })
  
  // => "混入对象的钩子被调用"
  // => "组件钩子被调用"
  ```

- 值为对象的选项，例如 methods、components 和 directives 将被合并为同一个对象，两个对象的键名发生冲突时，取组件对象的键值对

  ```JS
  var mixin = {
    methods: {
      foo: function () {
        console.log('foo')
      },
      conflicting: function () {
        console.log('from mixin')
      }
    }
  }
  
  var vm = new Vue({
    mixins: [mixin],
    methods: {
      bar: function () {
        console.log('bar')
      },
      conflicting: function () {
        console.log('from self')
      }
    }
  })
  
  vm.foo() // => "foo"
  vm.bar() // => "bar"
  vm.conflicting() // => "from self"
  ```

- [Vue.extend()](https://juejin.cn/post/6982558712149835790) 也使用相同的策略进行合并
