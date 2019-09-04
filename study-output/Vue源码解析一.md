# 准备工作

## Flow

Vue.js 在做 2.0 重构的时候，在 ES2015 的基础上，除了 ESLint 保证代码风格之外，也**引入了 Flow 做静态类型检查**。之所以选择 Flow，主要是因为 Babel 和 ESLint 都有对应的 Flow 插件以支持语法，可以完全沿用现有的构建配置，非常小成本的改动就可以拥有静态类型检查的能力。

### Flow 的工作方式

通常类型检查分成 2 种方式：

- **类型推断**：通过变量的使用上下文来推断出变量类型，然后根据这些推断来检查类型。
- **类型注释**：事先注释好我们期待的类型，Flow 会基于这些注释来判断。

### 全局安装

```bash
npm install -g flow-bin

mkdir flow-test
cd flow-test
flow init  # 初始化文件项目 生成 .flowconfig
```

### 类型注释类型

* 需要检查的文件前 `/*@flow*/`，则该文件开启flow检查
  
* **数组类型** 注释的格式是 `Array<T>`，`T` 表示数组中每项的数据类型。
  
  ```javascript
  var arr: Array<number> = [1, 2, 3]
  ```

* **类和对象**

  ```javascript
  class Bar {
    x: string;           // x 是字符串
    y: string | number;  // y 可以是字符串或者数字
    z: boolean;
  
    constructor(x: string, y: string | number) {
      this.x = x
      this.y = y
      this.z = false
    }
  }
  ```

* **Null**   若想任意类型 `T` 可以为 `null` 或者 `undefined`，只需类似如下写成 `?T` 的格式即可

  ```javascript
  var foo: ?string = null
  ```

### 在`Vue.js`源码中的应用

Flow 提出了一个 `libdef` 的概念，可以用来识别这些第三方库或者是自定义类型，而 Vue.js 也利用了这一特性。

### Runtime Only VS Runtime + Compiler

通常我们利用 vue-cli 去初始化我们的 Vue.js 项目的时候会询问我们用 Runtime Only 版本的还是 Runtime + Compiler 版本。下面我们来对比这两个版本。

Runtime Only
我们在使用 Runtime Only 版本的 Vue.js 的时候，通常需要借助如 webpack 的 vue-loader 工具把 .vue 文件编译成 JavaScript，因为是在编译阶段做的，所以它只包含运行时的 Vue.js 代码，因此代码体积也会更轻量。

Runtime + Compiler
我们如果没有对代码做预编译，但又使用了 Vue 的 template 属性并传入一个字符串，则需要在客户端编译模板，如下所示：

```javascript
// 需要编译器的版本
new Vue({
  template: '<div>{{ hi }}</div>'
})

// 这种情况不需要
new Vue({
  render (h) {
    return h('div', this.hi)
  }
})

```

因为在 Vue.js 2.0 中，最终渲染都是通过 render 函数，如果写 template 属性，则需要编译成 render 函数，那么这个编译过程会发生运行时，所以需要带有编译器的版本。

很显然，这个编译过程对性能会有一定损耗，所以通常我们**更推荐使用 Runtime-Only 的 Vue.js**。

### Vue 初始化

找到vue的定义 `...\vue\src\core\instance\index.js`，通过执行`Mixin` 方法给Vue 原型上挂载了很多的原型方法；

通过`...\vue\src\core\global-api`的`initGlobalAPI`给Vue原型上挂载若干静态方法；