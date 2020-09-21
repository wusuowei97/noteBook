# js

### 1. delete 运算符

1. delete 0 中的这个0是一个表达式求值；
2. delete x 中的x是一个引用；
3. delete obj.x 中obj.x 是一组表达式连续运算的结果（Result/引用）

```js

```

# vue

## 1. watch的高级用法

```html
<div>
      <p>FullName: {{fullName}}</p>
      <p>FirstName: <input type="text" v-model="firstName"></p>
</div>
<script>
new Vue({
  el: '#root',
  data: {
    firstName: 'Dawei',
    lastName: 'Lou',
    fullName: ''
  },
  watch: {
    firstName(newName, oldName) {
      this.fullName = newName + ' ' + this.lastName;
    }
  } 
})
</script>
```



### 1.1 handler方法和immediate属性

watch的一个特点，最初绑定的时候是不会只想的，要等到 `firstName` 改变时才执行监听计算。

那我们想要一开始就让它最初绑定的时候就执行该怎么办呢？我们需要修改下watch写法，修改后的watch代码如下：

```js
watch: {
    firstName: {
        handler(newName, oldName) {
            this.fullName = newName + ' ' + this.lastName;
        },
        // 代表在watch里声明了firstName这个方法之后立即先去执行handler方法
        imediate: true,
    }
}
```

注意 我们 给firstName绑定了一个 `handler` 方法，之前我们写的watch方法其实默认写的就是这个 `handler`，Vue.js 会去处理这个逻辑，最终编译出来其实就是这个 `handler`



而 `immediate:true` 代表如果在watch里声明了  firstName 之后，就会立即先去执行里面的 handler方法，如果为 `false` 就跟我们以前的效果一样，不会在绑定的时候就执行。

### 1.2 deep属性 

watch里面的 `deep` 属性，默认值是 `false`，代表是否深度监听，比如data中有一个 obj 属性：

```html
<div>
      <p>obj.a: {{obj.a}}</p>
      <p>obj.a: <input type="text" v-model="obj.a"></p>
</div>
<script>
new Vue({
  el: '#root',
  data: {
    obj: {
      a: 123
    }
  },
  watch: {
    obj: {
      handler(newName, oldName) {
         console.log('obj.a changed');
      },
      immediate: true
    }
  } 
})
</script>
```

当我们在在输入框中输入数据视图改变`obj.a`的值时，我们发现是无效的。受现代 JavaScript 的限制 (以及废弃 `Object.observe`)，Vue 不能检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 `getter/setter` 转化过程，所以属性必须在 `data` 对象上存在才能让 Vue 转换它，这样才能让它是响应的。

默认情况下 handler 只监听`obj`这个属性它的引用的变化，我们只有给`obj`赋值的时候它才会监听到，比如我们在 mounted事件钩子函数中对`obj`进行重新赋值：

```js
mounted: {
    this.obj = {
        a: '456'
    }
}
```

这样我们的 handler 才会执行，打印 `obj.a changed`

如果我们需要监听 obj里的属性a的值，这时候 deep 属性就派上用处了：

```js
watch: {
    obj: {
        handler(newName, oldName) {
            console.log('obj.a changed');
        },
        immediate: true,
        deep: true,
    }
}
```

