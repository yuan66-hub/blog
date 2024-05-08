title: js如何管理内存
author: jianmingYuan
date: 2024-03-01 17:27:53
tags:
---
# 引言

JS的内存空间分为栈(stack)、堆(heap)、池(一般也会归类为栈中)。
其中栈存放变量，堆存放复杂对象，池存放常量，所以也叫常量池。

# 栈数据结构

1. 栈是一种特殊的列表，栈内的元素只能通过列表的一端访问，这一端称为栈顶。
2. 栈被称为是一种后入先出（LIFO，last-in-first-out）的数据结构。
3. 由于栈具有后入先出的特点，所以任何不在栈顶的元素都无法访问。
4. 为了得到栈底的元素，必须先拿掉上面的元素。

# 堆数据结构

1. 堆是一种经过排序的树形数据结构，每个结点都有一个值。
2. 通常我们所说的堆的数据结构，是指二叉堆。
3. 堆的特点是根结点的值最小（或最大），且根结点的两个子树也是一个堆。
4. 由于堆的这个特性，常用来实现优先队列，堆的存取是随意

这就如同我们在图书馆的书架上取书，虽然书的摆放是有顺序的，但是我们想取任意一本时不必像栈一样，先取出前面所有的书，我们只需要关心书的名字。

# 变量类型与内存的关系

## 基本数据类型
基本数据类型共有6种：
1. Sting
2. Number
3. Boolean
4. null
5. undefined
6. Symbol

> 基本数据类型保存在栈内存中，因为基本数据类型占用空间小、大小固定，通过按值来访问，属于被频繁使用的数据。

## 引用数据类型
1. Array
2. Function
3. Object

> 引用数据类型存储在堆内存中，因为引用数据类型占据空间大、大小不固定。 如果存储在栈中，将会影响程序运行的性能； 引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。 当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体

## 栈内存和堆内存的优缺点

- 在JS中，基本数据类型变量大小固定，并且操作简单容易，所以把它们放入栈中存储。
- 引用类型变量大小不固定，所以把它们分配给堆中，让他们申请空间的时候自己确定大小，这样把它们分开存储能够使得程序运行起来占用的内存最小。
- 栈内存由于它的特点，所以它的系统效率较高。
- 堆内存需要分配空间和地址，还要把地址存到栈中，所以效率低于栈。

## 栈内存和堆内存的垃圾回收
栈内存中变量一般在它的当前执行环境结束就会被销毁被垃圾回收制回收， 而堆内存中的变量则不会，因为不确定其他的地方是不是还有一些对它的引用。 堆内存中的变量只有在所有对它的引用都结束的时候才会被回收。

## 闭包与堆内存

闭包中的变量并不保存中栈内存中，而是保存在堆内存中。 这也就解释了函数调用之后之后为什么闭包还能引用到函数内的变量。举一个例子：

```js
function A() {
  let a = 1;
  function B() {
      console.log(a);
  }
  return B;
}
let res = A();

```
函数 A 弹出调用栈后，函数 A 中的变量这时候是存储在堆上的，所以函数B依旧能引用到函数A中的变量。 现在的 JS 引擎可以通过逃逸分析辨别出哪些变量需要存储在堆上，哪些需要存储在栈上。

# 深克隆和浅克隆
## 浅克隆

1. 浅拷贝只复制对象的第一层属性。如果对象的属性值是基本数据类型（如String、Number、Boolean等），则直接复制值；如果属性值是引用类型（如Array、Object等），则复制其内存地址，而不是复制实际的值或嵌套的对象。以下都是浅拷贝的实现：
  - Object.assign()
  
    ```js
      let origin = { a: '原始字符串', b: { c: 1 } };
      let copy = Object.assign({}, origin);
      origin.a = '改变后的字符串';
      copy.b.c = 2;
      console.log(origin); // {a: '改变后的字符串', b: {c: 2}}
      console.log(copy); // {a: '原始字符串', b: {c: 2}}
    ```
  - 扩展运算符
  
    ```js
        let origin = { a: '原始字符串', b: { c: 1 } };
        let copy = { ...origin };
        origin.a = '改变后的字符串';
        copy.b.c = 2;
        console.log(origin); // {a: '改变后的字符串', b: {c: 2}}
        console.log(copy); // {a: '原始字符串', b: {c: 2}}
      ```
   - slice(数组)
   
     ```js
      let origin = [1, 2, 3, 4];
      let copy = origin.slice();
      console.log(copy);
     ```
   - concat
     ```js
       let original = [1, 2, 3, 4];
       let copy = original.concat();
     ```
  
## 深克隆
2. 深拷贝相对于浅拷贝来说，不仅复制了对象本身及其包含的原始类型的值，还复制了所有引用类型的实际值。这意味着，如果你修改拷贝对象中的一个引用类型的值，原始对象中相应的值不会发生变化，因为它们指向了不同的内存地址。深拷贝不仅复制对象的第一层属性，还递归复制所有的嵌套对象。这意味着，无论对象有多少层嵌套，深拷贝都会创建所有层次的副本。因此，原始对象和拷贝对象之间不会相互影响。
 - JSON.parse(JSON.stringify(object))
 ```js
   let original = { a: 1, b: { c: 2 } };
   let copy = JSON.parse(JSON.stringify(original));
   console.log(copy); // { a: 1, b: { c: 2 } }
   JSON.parse(JSON.stringify(NaN))  // null
 ```
 > 使用 JSON.stringify() 将对象序列化（转换为JSON字符串），然后使用 JSON.parse() 将字符串解析为新的对象。这种方法不能复制函数和循环引用的对象,undefine,Symbol,正则表达式
 
- 递归拷贝
   ```js
    function deepCopy(obj) {
    if (typeof obj !== 'object' || obj === null) {
        return obj;
    }

    let copy;
    if (Array.isArray(obj)) {
        copy = [];
        for (let i = 0; i < obj.length; i++) {
            copy[i] = deepCopy(obj[i]);
        }
    } else {
        copy = {};
        for (let key in obj) {
                if (obj.hasOwnProperty(key)) { // 检查是否是对象自身具有的属性，因为in会包含原型链上的属性，这里只拷贝自身的！
                    copy[key] = deepCopy(obj[key]);
                }
            }
        }
        return copy;
    }

   ```
- 使用第三方库：如Lodash的 _.cloneDeep()
 ```js
    let original = { a: 1, b: { c: 2 } };
    let copy = _.cloneDeep(original);
 ```