#  准备工作

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

