## 计算属性和侦听器



### 计算属性

模版内的表达式非常便利，但设计他们的初衷是用于简单运算的。在模版内放入太多的逻辑的会让模版过于繁重且难以维护。例如，又一个嵌套数组

```javascript
Vue.createApp({
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  }
})
```



我们想根据`author`是否已经有一些书来显示不同的消息

```javascript
<div id="computed-basic">
	<p>已经出版书籍<p/>
  <span>{{author.books.length > 0 ? "Yes" : "No"}}<span/>
<div/>
```

这个时候模版不在是简单的和声明性的。你必须先看一下它，然后才能意识到它执行的计算取决于`author.books`。如果要在模版中多次包含此计算，则问题会变得更加糟糕。所以，对于任何包含响应式数据的复杂逻辑，你都应该使用**计算属性**



#### 基本例子

```javascript
<div id="computed-basics">
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</div>
```

```javascript
Vue.createApp({
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  },
 computed:{
   publishedBooksMessage(){
     return this.author.books.length > 0 ? "Yes":"No";
   }
 }
}).mount('#computed-basics')
```



这里声明了一个计算属性`publishedBooksMessage`。

改变`data`中的`books`，computed中的`publishedBooksMessage`如何相应地改变。

计算属性可以像普通属性一样将属性绑定到模版中的计算属性。Vue知道`vm.publishedBooksMessage` 依赖于 `vm.author.books`，因此当`vm.author.books`发生改变时，所以依赖`vm.publishedBooksMessage`的绑定也会更新。

### 计算属性的Setter



```javascript
//...
computed:{
  fullName:{
    get(){
      return this.firstName + " " + this.lastName;
    },
    set(newValue){
      const names = newValue.split(" ");
      this.firstName = names[0];
      this.lastName = names[names.length -1];
    }
  }
}
```

运行`vm.fullName = "Weng Chengjian"`时setter会被调用，`vm.firstName`和`vm.lastName`也会相应的被更新。



### 侦听器

虽然计算属性在大部分情况下更适合，但有时也需要一个自定义的侦听器。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。

例如：

```javascript
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</div>
```

```javascript
<!-- 因为 AJAX 库和通用工具的生态已经相当丰富，Vue 核心代码没有重复 -->
<!-- 提供这些功能以保持精简。这也可以让你自由选择自己更熟悉的工具。 -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script>
  const watchExampleVM = Vue.createApp({
    data() {
      return {
        question: '',
        answer: 'Questions usually contain a question mark. ;-)'
      }
    },
    watch: {
      // whenever question changes, this function will run
      question(newQuestion, oldQuestion) {
        if (newQuestion.indexOf('?') > -1) {
          this.getAnswer()
        }
      }
    },
    methods: {
      getAnswer() {
        this.answer = 'Thinking...'
        axios
          .get('https://yesno.wtf/api')
          .then(response => {
            this.answer = response.data.answer
          })
          .catch(error => {
            this.answer = 'Error! Could not reach the API. ' + error
          })
      }
    }
  }).mount('#watch-example')
</script>
```



在这个例子中，使用`watch`选项允许我们执行异步操作，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

除了watch选项之外，还可以使用命令式的`vm.$watch` API。