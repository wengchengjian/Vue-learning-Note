## Class与Style绑定

操作元素的class列表与内联样式是数据绑定的一个常见需求。因为他们都是attribute，所以我们可以用`v-bind`处理他们：只需要通过表达式计算出字符串结果即可。不过，字符串拼接麻烦且易错。因此，在将`v-bind`用于`class` 和`style`时，Vue做了专门的增强。表达式结果的类型除了字符串之外，还可以时对象和数组。

### 绑定HTML Class



#### 对象语法

我们可以传给`:class`(`v-bind:class`的简写)一个对象，以便动态地切换class：

```javascript
<div :class="{ active:isActive }"><div/>
```

上面的语法表示`active`这个class存在与否取决于数据property`isActive`

还可以向对象中传入更多的字段来动态切换class,此外，`:class`指令也可以于普通的`class`attribute共存。例子如下：

模版：

```javascript
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
```

data：

```javascript
data() {
  return {
    isActive: true,
    hasError: false
  }
}
```

渲染的结果为：

```javascript
<div class="static active"></div>
```

绑定的对象不一定内联定义在模版内：

```html
<div :class="classObject">
  
</div>
```

```javascript
data(){
  return {
    classObject: {
      active:true,
      "text-danger":false
    }
  }
}
```

渲染的结果和上面一样。

我们甚至可以在这里绑定一个返回对象的计算属性。这是一个常用且强大的模式：

```html
<div :class="classObejct">
  
</div>
```

```javascript
data(){
  return {
    isActive:true,
    error:null,
  }
},
computed:{
  classObejct(){
    return {
      acitve:this.isActive && !this.error,
      "text-danger":this.error && this.error.type === "fatal"
    }
  }
}
```

#### 数组语法

我们可以把一个数组传给`:class`，以应用一个class列表：

```html
<div :class="[activeClass,errorClass]">
  
</div>
```



```javascript
data(){
  return {
    activeClass:"active",
    errorClass:"text-danger",
  }
}
```

渲染的结果为：

```html
<div class="active text-danger">
  
</div>
```

当存在条件class时，可以使用三元表达式。但存在多个条件时这样写比较繁琐。所以在数组语法中也可以使用对象语法。

### 绑定内联样式

#### 对象语法

`:style`的对象语法十分直观 ----看着非常像css，但其实是一个JavaScript对象。CSS property名可以使用驼峰式或短横线命名：

```html
<div :style="{color:activeColor,fontSize:fontSize+'px'}">
  
</div>
```

```javascript
data(){
  return {
    activeColor:"red",
    fontSize:30,
  }
}
```

或者直接绑定一个样式对象更加直观

```html
<div :style="styleObject">
  
</div>
```

```javascript
data(){
  return {
    styleObejct:{
      color:"red",
      fontSize:"13px",
    }
  }
}
```

于绑定class相同，绑定style 的对象语法常常结合对象的计算属性使用。

#### 数组语法

`:style`的数组语法可以将多个样式对象应用到相同的元素上：

```html
<div :style="[baseStyles,overrideStyles]">
  
</div>
```

#### 自动添加前缀

在`:style`中使用需要一个浏览器引擎前缀的css property时，Vue将自动侦测并添加相应的前缀。Vue是通过运行时检测来确定哪些样式的property是被当前浏览器支持的。如果的浏览器不支持某个property，Vue会进行多次测试以找到支持它的前缀。



#### 多重值

可以为style绑定中的property提供一个包含多个值的数组，常用于提供多个带前缀的值，例如：

```html
<div :style="{display:['-webkit-box','-ms-flexbox','flex']}">
 
</div>
```

这么写只会渲染数组中最后一个被浏览器支持的值。