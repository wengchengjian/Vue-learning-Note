##  Data Property 和方法



### Data Property

组件的`data`选项是一个函数。Vue会在创建新组建实例的过程中调用此函数。此时它应该返回一个对象，然后Vue通过响应式系统将其包裹起来，并以`$data`的形式存储在组件实例中。为了方便起见，任何顶级的property也直接通过组件实例暴露出来。

```javascript
const app = Vue.createApp({
	data(){
    return {count:4}
  }  
})

const vm = app.mount("#app");

console.log(vm.$data.count); // =>4
console.log(vm.count); // => 4
//修改count 也会同时修改$data.count
vm.count = 5;
console.log(vm.$data.count) // =>5

//反之依然
vm.$data.count = 6;
console.log(vm.count) // =>count
```



Vue 使用`$`前缀通过组件实例暴露自己的内置API,他还为内部property保留`_`前缀。所以应该避免使用这两个前缀来命名顶级property。



### 方法

对于方法，我门使用 `methods` 选项向组件实例中添加方法，他应该是包含所需方法的对象。

```javascript
const app = Vue.createApp({
  data(){
    return {
      count:4
    }
  },
  methods:{
    increment(){
      this.count++;
    }
  }
})

const vm = app.mount("#app");

console.log(vm.count) // =>4

vm.increment()

console.log(vm.count) // =>5

```



Vue 自动为	`methods`绑定this,以便他始终指向组件实例。这样能确保事件监听和回调时保持正确的this指向。在定义methods时应该避免使用箭头函数，因为这会阻止Vue绑定恰当的this指向。

这些`methods`和组件实例的其他所有property一样可以在组件的模版中被访问。在模版中，他们通常被当作事件监听使用

`<button @click="increment">UpDate<button/>`

上面这个例子，点击这个按钮时回调用`increment`方法 





