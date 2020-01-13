---
date: 2019-10-01
tag: 
  - JavaScript
  - JQuery
  - 观察者
author: NeoYo
location: Shenzhen
---

### Callbacks 源码

源码地址 [github - jquery - callbacks ](https://github.com/jquery/jquery/blob/1b74660f730d34bf728094c33080ff406427f41e/src/callbacks.js#L15)

#### 3.1 功能点拆分

##### 1. callbacks.add 和 callbacks.fire

- 闭包封装
    ```js
    function() {
        var self = {
            add: () => {},
            fire: () => {}
        };
        return self;
    }
    ```
- 一个数组作为容器
    ```js
    Array.prototype.push()
    Array[index] // for 遍历
    ```

##### 2. stopOnfalse

- 数组容器不能用 forEach， 因为无法中断
- Array[index] 和 `for...of` 是可以的
    ```
    for(;index < length;index++){
        if(list[index].apply(data[0], data[1]) === false && options.stopOnfalse){
    		break;
        }
    }
    ```

##### 3. once

- 想要实现只 fire 一次，用一个标志位即可 `onceBefore`

    ```js
    var onceBefore = false;
    if(options.once && onceBefore){
        // 不执行了	    
	} else {
	    fire(args);
	    onceBefore = true;
	}
    ```

##### 4. memory

- 需求：add 的时候自动 fire 容器中最后一个
- 方案步骤

1. 变量 memory 存储之前的 `this` 和 arguments
2. 标志变量 isMemoryFire, 修改遍历 index
    ```js
    // 1. add 函数中
    function add() {
    	if(memory){
			 isMemoryFire = true;
			 fire(memory); // 使用缓存的 this 和 arguments
			 isMemoryFire = false;
		}
	}
    // 2. 真正 fire 函数中
    /**
     * data [this, arguments]
     **/
    function fire(data) {
        // 2.1 缓存 this 和 arguments 给 add-fire 使用
        memory = options.memory && data;
        // 2.2 只调用最后一个
        index = isMemoryFire ? list.length - 1 : 0;    
    }
    ```
