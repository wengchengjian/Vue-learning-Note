## 组件基础



例如：

```javascript
const app = Vue.createApp({});

app.component('button-counter',{
  data(){
    return {
      count:0
    }
  },
  template:`
  <button @click="count++">已经点击了{{count}}次<button/>
  `
})
```



组件是带有名称的可复用实例，在上面这个例子中是`button-counter`。我们可以把这个组件作为一个根实例中的自定义元素来使用:

```html
<div>
  <button-counter></button-counter>
</div>
```

因为组件是可复用的组件实例，所以它们与根实例接受相同的选项，例如`data`,`computed`,`watch`,`methods`以及声明周期钩子。

### 组件的复用

可以将组件进行任意次的复用：

```html
<div>
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

### 通过Prop向子组件传递数据

Prop是我们在组件上注册的一些自定义attribute。为了给博文组件传递一个标题，我们可以用`props`选项将其包含在该组件可接受的prop列表中：

```javascript
const app = Vue.createApp({});

app.component('blog-post',{
  props:['title'],
  template:`<h4>{{title}}<h4/>`
})
app.mount("#app");
```



当一个值被传递给组件的props时，它将成为该组件实例中的一个property。该property的值可以在模版中访问，就像其他property一样。

### 监听子组件事件

我们在开发`<blog-post>`组件时,它的一些功能可能需要与父级组件沟通。例如我们可能会引入一个辅助功能来放大博文的字号，同时让页面的其他部分保持默认的字号。

在其父组件中，我们可以添加一个`postFontSize`数据的property来支持这一功能：

```javascript
const app = {
  data(){
    return {
      posts:[],
      postFontSize:1
    }
  }
}
```

他可以在模版中用来控制所有博文的字号：

```html
<div>
  <div :style="{fontSize:postFontSize+'em'}">
    <blog-post>
    	v-for="post in posts"
      :key="post.id"
      :title="post.title"
    </blog-post>
  </div>
</div>
```

现在我们在每篇博文正文前添加一个按钮来放大字号：

```javascript
app.component('blog-post',{
  props:['title'],
  template: `
    <div class="blog-post">
      <h4>{{ title }}</h4>
      <button>
        Enlarge text
      </button>
    </div>
  `
})
```

但是点击这个按钮不会做任何事：

```html
<button>
  放大字号
</button>
```

当我们点击这个按钮时，我们需要告诉父级组件放大所有博文的文本。而组件实例提供了一个自定义事件的系统来解决这个问题。父级组件可以像处理原生DOM事件一样通过`v-on`或`@`来监听子组件实例的任意事件：

```html
<blog-post @enlarge-text="postFontSize+0.1"></blog-post>
```

同时子组件可以通过调用内建的`$emit`方法并传入事件名称来触发一个事件：

```html
<button @click="$emit('enlargeText')">
  放大字号
</button>
```



我们还可以在组件的`emits`选项中列出已抛出的事件：

```javascript
app.component('blog-post',{
  props:['title'],
  emits:['enlargeText']
})
```

这将允许我们检查组件抛出的所有事件，还可以选择验证它们



#### 使用事件抛出一个值



有时候使用事件抛出一个值是非常有用的，这时我们可以使用`$emit`第二个参数来提供这个值：

```html
<button @click="$emit('enlargeText',0.1)">
  放大字号
</button>
```

然后在父级组件去监听这个事件时，我们可以通过`$event`访问到被抛出的值：

```html
<blog-post @enlarge-text="postFontSize +=$event"></blog-post>
```

或者这个事件处理函数是一个方法：

```html
<blog-post @enlarge-text="onEnlargeText"></blog-post>
```

那么这个值将会作为第一个参数传入这个方法：

```javascript
methods:{
  onEnlargeText(enlargeNumber){
    this.postFontSize +=enlargeNumber;
  }
}
```

#### 在组件上使用`v-model`

自定义事件也可以用于创建支持`v-model`的自定义输入组件。记住：

```html
<input v-model="searchText" />
```

等价于：

```html
<input :value="searchText" @input="searchText = $event.target.value" />
```



当用在组件上时，`v-model`则会这样：

```html
<custom-input :model-value="searchText" @update:model-value="searchText = $event">
</custom-input>
```



```javascript
app.component("custom-input",{
  props:['modelValue'],
  emits:['update:modelValue'],
  template:`
  	<input :value="modelValue" @input="$emit('update:modelValue',$event.target.value)" />
  `
})
```

现在`v-model`就可以在这个组件上完美地工作起来了：

```html
<custom-input v-model="seaechText"></custom-input>
```

在组件中实现`v-model`的另一种方法是使用`computed`property的功能来定义getter和setter。`get`方法返回`modelValue` property,`set`方法触发相应的事件。



```javascript

app.component('custom-input',{
  props:['modelValue'],
  emits:['update:modelValue'],
  template:`
  <input v-model="value" />
  `,
  computed:{
    value:{
      get(){
        return this.modelValue;
      },
      set(value){
        this.$emit('update:modelValue',value);
      }
    }
  }
})
```



### 通过插槽分发内容

和html元素一样，我们经常需要向一个组件传递内容，像这样：

```html
<alert-box>
	Something bad happened
</alert-box>
```

这可以通过使用Vue的自定义`slot`来实现

```javascript
app.component('alert-box',{
  template:`
  <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```

### 动态组件

有时候需要在不同组件之间进行动态切换是非常有用的，比如在一个多标签的界面里。这可以通过Vue的`<component>`元素加一个特殊的`is`attribute来实现

```html
<component :is="currentTabComponent"></component>
```

在上述例子中，`currentTabComponent`可以包括

+ 已经注册组件的名字
+ 一个组件的选项对象

