## Props


### Prop类型

到这里为止，我们只看到了以字符串数组形式列出的prop：

```javascript
props:['title','likes','isPublished']
```

但是，通常你希望每一个prop都有指定的值和类型。这是，你可以以对象的形式列出prop，这些property的名称和值分别是prop各自的名称和类型:

```javascript
props:{
  title:String,
  likes:Number,
  isPublished:Boolean,
  commentIds:Array,
}
```

### 传递静态或动态的Prop

你已经知道如何传递静态的值：

```html
<blog-post title="标题"></blog-post>
```

也可以通过`v-bind`或`:`动态赋值，例如：

```html

<blog-post :title="post.title"></blog-post>

<blog-post :title="post.title+'by' + post.author.name"></blog-post>
```

#### 传入一个数字

```html
<blog-post :likes="42"></blog-post>

<blog-post :likes="post.likes"></blog-post>
```



#### 传入一个布尔值



```html
<!-- 包含该 prop 没有值的情况在内，都意味着 `true`。          -->
<!-- 如果没有在 props 中把 is-published 的类型设置为 Boolean，则这里的值为空字符串，而不是“true” 。 -->
<blog-post is-published></blog-post>

<!-- 即便 `false` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue  -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。             -->
<blog-post :is-published="false"></blog-post>

<!-- 用一个变量进行动态赋值。                                -->
<blog-post :is-published="post.isPublished"></blog-post>
```

#### 传入一个数组



```html
<!-- 即便数组是静态的，我们仍然需要 `v-bind` 来告诉 Vue        -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。             -->
<blog-post :comment-ids="[234, 266, 273]"></blog-post>

<!-- 用一个变量进行动态赋值。                                -->
<blog-post :comment-ids="post.commentIds"></blog-post>

```



#### 传入一个对象

```html
<!-- 即便对象是静态的，我们仍然需要 `v-bind` 来告诉 Vue        -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。             -->
<blog-post
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
></blog-post>

<!-- 用一个变量进行动态赋值。                                 -->
<blog-post :author="post.author"></blog-post>
```

#### 传入一个对象的所有property

```html
<blog-post v-bind="post"></blog-post>
```

等价于

```html
<blog-post :id="post.id" :title="post.title"></blog-post>
```



### 单向数据流

所有的prop都使得父子prop之间形成了单向下行绑定：父级prop的更新会向下流动到子组件中，反过来则不行。这样会防止从子组件意外变更父级组件的状态，从而导致你的应用的数据流难以理解。

另外，每次父级组件发生变更时，子组件中所有的prop都将刷新为最新的值。这意味着，你不能在子组件内部改变prop。如果这样做了，Vue会在浏览器的控制台中发出警告。

例子：

1. 这个prop用来传递一个初始值；这个子组件接下来希望将其作为本地一个data property来使用。在这样的情况下，最好定义一个本地的data property来保存prop作为初始值：

```javascript
props:['initialCounter'],
data(){
  return {
    counter:this.initialCounter,
  }
}
```

2. 这个prop 以一种原始值传入并且需要转换。这种情况下，最好使用这个prop的值来定一个计算属性：

```javascript
props:['size']
computed:{
  normalizedSize(){
    return this.size.trim().toLowerCase();
  }
}
```

