## 非Prop的Attribute



一个非prop的attribute是指传向一个组件，但是该组件并没有相应props或emits定义的attribute。常见实例包括`class`,`style`,`id`等等。

#### Attribute继承

当组件返回单个根节点时，非prop attribute将自动添加到根节点的attribute中。例如在`<date-picker>`组件的实例中：

```javascript
app.component('data-picker',{
  template:`
  	<div class="date-picker">
      <input type="datetime-local" />
    </div>
  `
})
```

如果我们需要通过 `data-status` attribute 定义 `<date-picker>` 组件的状态，它将应用于根节点 (即 `div.date-picker`)。

```html
<!-- 具有非prop attribute的Date-picker组件-->
<date-picker data-status="activated"></date-picker>

<!-- 渲染 date-picker 组件 -->
<div class="date-picker" data-status="activated">
  <input type="datetime-local" />
</div>
```

同样的规则也适用于事件监听器：

```html
<data-picker @change="submitChange"></data-picker>
```

```html
app.component('date-picker',{
create(){
console.log(this.$attr)  // => {onChange:()=>{}}
}
})
```

#### 禁用Attribute继承

如果你不希望组件的跟元素继承attribute，你可以在组件的选项中设置`inheritAttrs:false`。例如