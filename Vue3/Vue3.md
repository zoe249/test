## Vue3 ref与reactive对比

* 从定义数据角度对比
  * ref 用来定义：基本数据类型
  * reactive 用来定义：对象（数组）类型数据
  * ref也可以定义对象（数组）类型数据，内部自动通过reactive编译转为代理对象
* 从原理角度对比
  * ref通过Object.defineProerty 的 get set 来实现响应式
  * reactive通过使用Proxy来实现响应式，并通过Reflect操作源对象内部数据 
* 从使用角度对比
  * ref定义的数据：操作数据需要.value，模板中不需要 .value
  * reactive 定义的数据：操作数据与读写数据均不需要 .value

|          | ref                                  | reactive                                      |
| -------- | ------------------------------------ | --------------------------------------------- |
| 数据类型 | 基本类型数据                         | 对象（或数组）类型数据                        |
| 原理     | 通过Object.defineProerty实现数据劫持 | 通过Proxy来实现响应式,并通过Reflect操作源对象 |
| 使用     | 操作数据需要，.value(模板内不需要)   | 操作与读取数据不需要 .value                   |



# 一、Vue3常用API

## 1、computed 计算属性

* 与 Vue2 中的computed配置功能一致

* 需要 引入  import {computed} from 'vue'

* 写法

  ```ts
  import {reactive,computed} from 'vue'
  setup(){
      let person = reactive({
        firstname:'张',
        lastname:'三'
      })
  
      // 计算属性，简写，没有考虑计算属性被修改的情况
      person.fullname = computed(()=>{
        return person.firstname+person.lastname
      })
  
      // 计算属性，完整写法 考虑读和写
      person.fullname = computed({
        get(){
          return person.firstname+'-'+person.lastname
        },
        set(value){
          const nameArr = value.split('-')
          person.firstname = nameArr[0]
          person.lastname = nameArr[1]
        }
      })
      return {
        person,
        // fullname
      }
    }
  ```

## 2、watch 监听

实例

```js
setup(){
	let person = reactive({
      name:'张三',
      age:15,
      job:{
        j1:{
          money:14,
        }
      }
    })
    let num = ref(0)
    let msg = ref('hello')
    // 情况一 监视ref所定义的一个响应式数据
    // watch(num,(newVal,oldVal)=>{
    //   console.log(newVal,oldVal)
    // })

    // 情况二 监听ref定义的多个响应式数据
    // watch([num,msg],(newVal,oldVal)=>{
    //   console.log('num或msg改变了',newVal,oldVal)
    // },{immediate:true})   // immediate:true  页面加载完成立即初始化一次
	
    // 情况三 如果reactive 所定义的一个响应式数据全部属性，此处无法正确获得oldVal
    // 强制开启了深度监听
    // watch(person,(newVal,oldVal)=>{
    //   console.log('person变化了',newVal,oldVal)
    // })

    // 情况四 监听 reactive 所定义的一个响应式数据中的某个属性
    // watch(()=>person.age,(newVal,oldVal)=>{
    //   console.log('person变化了',newVal,oldVal)
    // })
    watch(()=>person.age,(newVal,oldVal))

    // 情况五 监听 reactive 所定义的一个响应式数据中的某些属性
    watch([()=>person.age,()=>person.name],(newVal,oldVal)=>{
      console.log('person变化了',newVal,oldVal)
    })

    watch(()=>person.job,(newVal,oldVal)=>{
      console.log('person变化了',newVal,oldVal)
    },{deep:true})  // 此处监视reactive定义的某个属性的值，所以deep配置有效 
    // deep:true,开启深度监听
}
return {
      person,
      num,
      msg
      // fullname
    }
```



* ​	情况一：监视ref所定义的一个响应式数据

  ```js
  watch(num,(newVal,oldVal)=>{
      consolo,.log(newVal,oldVal)
  })
  ```

*    情况二：监视ref定义的多个响应式数据

  ```js
  watch([num,msg],(newVal,oldVal)=>{
        console.log('num或msg改变了',newVal,oldVal)
      },{immediate:true})
  ```

*    情况三： reactive 所定义的一个响应式数据，此处无法正确获取oldVal  强制开启了深度监听

  ```js
  // 深度监听强行开启
  watch(person,(newVal,oldVal)=>{
        console.log('person变化了',newVal,oldVal)
      })
  ```

*    情况四：监听reactive 所定义的一个响应式数据中的某个属性

  ```js
  watch(()=>person.age,(newVal,oldVal)=>{
        console.log('person变化了',newVal,oldVal)
      },{deep:true}) // 此处监听 reactive 定义的某个属性的值，所以deep有效
  ```

*    情况五：监听 reactie 所定义的 一个响应式数据中的某些属性

  ```js
  watch([()=>person.age,()=>person.name],(newVal,oldVal)=>{
        console.log('person变化了',newVal,oldVal)
      })
  ```

## 3、watchEffect

​	类似于 computed

​	自动检测内部使用的对象        

* computed 更注重 计算出来的结果，必须要有返回值

* watcheEffect 更注重 过程，不需要写返回值

* ```ts
  // 计算属性
  person.fullname = computed(()=>{
        return person.firstname+person.lastname
      })
  
  watchEffect(()=>{
          console.log('watchEffect执行')
      })
  ```

  

## 4、生命周期

![生命周期](https://v3.cn.vuejs.org/images/lifecycle.svg)



### 组合式api 

```ts
setup(){
    let num = ref(0)
    console.log('------setup------')
    // 组合式 api 生命周期钩子
    onBeforeMount(()=>{
      console.log('------onBeforeMount-------')
    })
    onMounted(()=>{
      console.log('------onMounted-------')
    })
    onBeforeUpdate(()=>{
      console.log('------onBeforeUpdate-------')
    })
    onUpdated(()=>{
      console.log('------onUpdated-------')
    })
    onBeforeUnmount(()=>{
      console.log('------onBeforeUnmount-------')
    })
    onUnmounted(()=>{
      console.log('------onUnmounted-------')
    })
    return {
      num,
    }
  },
```

### optionsApi生命周期

```ts
beforeCreate(){
    console.log('--------------beforeCreate-------------')
  },
  created(){
    console.log('--------------create-------------')
  },
  beforeMount(){
    console.log('--------------beforeMount-------------')
  },
  mounted(){
    console.log('--------------mounte-------------')
  },
  beforeUpdate(){
    console.log('--------------beforeUpdate-------------')
  },
  updated(){
    console.log('--------------update-------------')
  },
  beforeUnmount(){
    console.log('--------------beforeUnmount-------------')
  },
  unmounted(){
    console.log('--------------unmount-------------')
  }
```

## 5、toRef和toRefs

* toRef

  ```vue
  <template>
    <h2>年龄：{{ age }}</h2>
    <h2>年龄：{{ money }}</h2>
    <h2>姓名：{{ name }}</h2>
  
    <button @click="name += '~'">修改年龄</button>
    <button @click="age++">修改姓名</button>
    <button @click="money++">修改薪资</button>
  </template>
  
  <script>
  import { defineAsyncComponent, toRefs, toRef, ref, reactive } from "vue";
  export default {
    setup() {
      let person = reactive({
        name: "张三",
        age: 15,
        job: {
          j1: {
            money: 14,
          },
        },
      });
      return {
        name: toRef(person, "name"),
        age: toRef(person, "age"),
        money: toRef(person.job.j1, "money"),
      };
    },
  };
  </script>
  ```

  

* 作用：创建一个ref对象，其value值指向另一个对象中的某个属性

* 语法：`const name = toRef(pseron,'name')`

* 应用：将响应式对象中的某个属性单独提供给外部使用

* toRefs：toRefs可以批量创建多个ref应用 `toRefs(person)`

  ```vue
  <template>
    <h2>年龄：{{ age }}</h2>
    <h2>薪水：{{ job.j1.money }}</h2>
    <h2>姓名：{{ name }}</h2>
  
    <button @click="name += '~'">修改年龄</button>
    <button @click="age++">修改姓名</button>
    <button @click="job.j1.money++">修改薪资</button>
  </template>
  
  <script>
  import {  toRefs, toRef, ref, reactive } from "vue";
  export default {
    setup() {
      let person = reactive({
        name: "张三",
        age: 15,
        job: {
          j1: {
            money: 14,
          },
        },
      });
        // 一次性导出多个
      return {
        ...toRefs(person),
      };
    },
  };
  </script>
  
  <style>
  </style>
  ```

  

# 二、Vue3 其他composition API 

## 1、shallowReactive 与 shallowRef

* shallowReactive：只处理对象最外层属性的响应式

* shallowRef： 只处理基本数据类型的响应式，不进行对象的响应式处理

* 场景
  * 如果一个对象结构较深，但只变化外层属性===》shallowReactive
  * 如果一个对象数据，后续功能不会修改该对象中的属性，二十生成新的对象来代替===》shallowRef
  
* ```vue
  <template>
    <h2>App</h2>
  
    <h3>m1: {{m1}}</h3>
    <h3>m2: {{m2}}</h3>
    <h3>m3: {{m3}}</h3>
    <h3>m4: {{m4}}</h3>
  
    <button @click="update">更新</button>
  </template>
  
  <script lang="ts">
  import { reactive, ref, shallowReactive, shallowRef } from 'vue'
  /* 
  shallowReactive与shallowRef
    shallowReactive: 只处理了对象内最外层属性的响应式(也就是浅响应式)
    shallowRef: 只处理了value的响应式, 不进行对象的reactive处理
  总结:
    reactive与ref实现的是深度响应式, 而shallowReactive与shallowRef是浅响应式
    什么时候用浅响应式呢?
      一般情况下使用ref和reactive即可,
      如果有一个对象数据, 结构比较深, 但变化时只是外层属性变化 ===> shallowReactive
      如果有一个对象数据, 后面会产生新的对象来替换 ===> shallowRef
  */
  
  export default {
  
    setup () {
  
      const m1 = reactive({a: 1, b: {c: 2}})
      const m2 = shallowReactive({a: 1, b: {c: 2}})
  
      const m3 = ref({a: 1, b: {c: 2}})
      const m4 = shallowRef({a: 1, b: {c: 2}})
  
      const update = () => {
        // m1.b.c += 1
        // m2.b.c += 1
  
        // m3.value.a += 1
        m4.value.a += 1
      }
  
      return {
        m1,
        m2,
        m3,
        m4,
        update,
      }
    }
  }
  </script>
  ```

## 2、readonly 与 shallowReadonly

* readonly: 让一个响应式数据变为只读（深只读）

* shallowReadonly：让一个响应式数据变为只读（浅只读）

* 语法 `let person = readonly()`

* ```vue
  <template>
    <h2>App</h2>
    <h3>{{state}}</h3>
    <button @click="update">更新</button>
  </template>
  
  <script lang="ts">
  import { reactive, readonly, shallowReadonly } from 'vue'
  /*
  readonly: 深度只读数据
    获取一个对象 (响应式或纯对象) 或 ref 并返回原始代理的只读代理。
    只读代理是深层的：访问的任何嵌套 property 也是只读的。
  shallowReadonly: 浅只读数据
    创建一个代理，使其自身的 property 为只读，但不执行嵌套对象的深度只读转换 
  应用场景: 
    在某些特定情况下, 我们可能不希望对数据进行更新的操作, 那就可以包装生成一个只读代理对象来读取数据, 而不能修改或删除
  */
  
  export default {
  
    setup () {
  
      const state = reactive({
        a: 1,
        b: {
          c: 2
        }
      })
  
      // const rState1 = readonly(state)
      const rState2 = shallowReadonly(state)
  
      const update = () => {
        // rState1.a++ // error
        // rState1.b.c++ // error
  
        // rState2.a++ // error
        rState2.b.c++
      }
      
      return {
        state,
        update
      }
    }
  }
  </script>
  ```

## 3、toRaw  与 markRaw

* toRaw：
  * 作用：将一个由`reactive`生成的<font color='red'>响应式对象</font>转为<font color="red">普通对象</font>
  * 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面的更新
  
  `const p = toRaw(person);`
  
* markRaw
  * 作用：标记一个对象，使其永远不会再成为响应式对象
  * 应用场景
    * 有些值不应该被设置为响应式的，例如复杂的第三方库
    * 当渲染具有不可变数据源的列表时，跳过响应式转换可以提高性能
  
  ```js
  <template>
    <h2>{{state}}</h2>
    <button @click="testToRaw">测试toRaw</button>
    <button @click="testMarkRaw">测试markRaw</button>
  </template>
  
  <script lang="ts">
  /* 
  toRaw: 得到reactive代理对象的目标数据对象
  */
  import {
    markRaw,
    reactive, toRaw,
  } from 'vue'
  export default {
    setup () {
      const state = reactive<any>({
        name: 'tom',
        age: 25,
      })
  
      const testToRaw = () => {
        const user = toRaw(state)
        user.age++  // 界面不会更新
  
      }
  
      const testMarkRaw = () => {
        const likes = ['a', 'b']
        // state.likes = likes
        state.likes = markRaw(likes) // likes数组就不再是响应式的了
        setTimeout(() => {
          state.likes[0] += '--'
        }, 1000)
      }
  
      return {
        state,
        testToRaw,
        testMarkRaw,
      }
    }
  }
  </script>
  // 对象不再是响应式对象，修改属性页面不会再更新
  // 注意：通过其他方式引起的页面更新，获取到的仍是最新的
  ```
  
  

## <font color="red">4、自定义customRef</font>

​	作用：创建一个自定义的ref，并对其依赖项跟踪和更新出发进行显示控制

* 实例 

  ```vue
  <template>
    <input type="text" v-model="keyWord">
    <h2>{{keyWord}}</h2>
  </template>
  
  <script>
  import {ref,customRef} from 'vue'
  export default {
    name:'App',
    
    setup(){
      function myRef(value) {  // 自定义 ref
        let timer;
        return  customRef((track,tigger)=>{
          return {
            get(){
              console.log('从myRef读取数据')
              track()
              return value
            },
            set(val)
              console.log(val+'修改')
              clearTimeout(timer)
              timer = setTimeout(()=>{
                value = val   
                tigger() //  重新解析模板
              },500)
            }
          }
        })
      }
      // let keyWord = ref('keyWord')  // 使用 Vue 内置ref
      let keyWord = myRef('keyWord')
      return{
        keyWord
      }
    }
  }
  </script>
  
  <style>
  
  </style>
  ```

## 5、provide与inject

作用：父组件与后代组件通信

![图片](https://v3.cn.vuejs.org/images/components_provide.png)

父组件使用 `provide`选项来提供数据，后代组件使用`inject`选项来接受使用这些数据

具体方法

* 父组件

  ```js
  <script>
  import {provide} from 'vue'
  import Son from './sonView.vue'
  export default {
      components:{
          Son
      },
      setup(){
        provide('car',car)
  
        return {car}
      }
  }
  </script>
  ```

* 子组件或后代组件

  ```js
  <script>
  import {inject} from 'vue'
  export default {
    setup(){
      let car = inject('car')
      console.log(car)
      return {car}
    }
  }
  </script>
  ```

  

##  6、响应式数据的判断

* isRef：检查一个值是否为`ref` 对象
* isReactive：检查一个对象是否由`reactive`创建的响应式原理
* isReadonly：检查一个对象是否由`readonly`创建的只读
* isProxy：检查一个对象是否由`reactive`或者`readonly`方法创建

# 三、Vue3新增组件

 ### 1、teleport 瞬移

​	Teleport 提供了一种干净的方法, 让组件的html在父组件界面外的特定标签(很可能是body)下插入显示

​	可以将元素定位到指定元素下

```html
<template>
  <div>
      <button @click="isShow = true">弹窗</button>   
  </div>
    <!-- 将元素指定到body元素下 -->
  <teleport to="body">  
    <div class="mask" v-if="isShow">
        <button @click="isShow = false">关闭</button>
        <div class="dialog" >
            <h1>内容1</h1>
            <h1>内容1</h1>
            <h1>内容1</h1>
        </div>
    </div>
  </teleport>
</template>
```

# 四、其他

## 1、setup()中使用vuex

​	引入`import { useStore } from 'vuex'`

​	使用

```vue
<template>
  <h1>{{msg}}</h1>
</template>

<script>
import {computed} from 'vue'
import { useStore } from 'vuex'
export default {
  setup(){
    const store = useStore()
    const msg = computed(()=> {return store.state.message})
    return {
      msg
    }
  }
}
</script>

<style>

</style>
```

## 2、Vue3 hooks 使用

* 在src目录下创建hooks文件夹

* 申明一个我们要复用的方法的名字.ts(或js) 文件，一般为use开头

  ![](https://img-blog.csdnimg.cn/20210223155148135.png)

* useMousePositions.ts文件代码

  ```tsx
  import {onBeforeUnmount, onMounted, ref} from 'vue'
  export default function () {
  	const x = ref(-1) ; // x 绑定为响应式数据
  	const y = ref(-1);
  	const clickHandler=(event:MouseEvent)=>{
  		x.value = event.pageX
  		y.value = event.pageY
  	} 
  	onMounted(()=>{
  		window.addEventListener('click', clickHandler)
  	})
  	onBeforeUnmount(()=>{
  		window.removeEventListener('click', clickHandler)
  	})
      // 只返回x,y  不返回具体函数
  	return {
  		x,
  		y,
  
  	}
  }
  ```

* 在 vue 文件 setup中使用

* ```vue
  <template>
    <div class="hello">
      <h1>{{x}}</h1>
      <h1>{{y}}</h1>
    </div>
  </template>
  
  <script lang="ts">
  import useMousePosition from '../hooks/useMousePosition'
  export default ({
    setup(){
      const {x,y} = useMousePosition()
      return{
        x,y,
      }
    },
  });
  </script>
  
  <style scoped>
  </style>
  
  ```

## 3、获取操作dom

* ref 获取dom

  ```ts
  <template>
    <img  ref="img" src="../assets/logo.png" alt="">
  </template>
  <script>
    import { ref, onMounted } from 'vue'
  
    export default {
      setup() {
        const img = ref(null)
  
        onMounted(() => {
          // DOM 元素将在初始渲染后分配给 ref
          console.log(img.value) // <div>This is a root element</div>
        })
  
        return {
          img
        }
      }
    }
  </script>
  ```

  

* internalInstance 获取dom

  ```ts
  <template>
    <img  ref="img" src="../assets/logo.png" alt="">
  </template>
  //首先引入
  import { getCurrentInstance} from 'vue';
   setup() {
       const internalInstance = getCurrentInstance();
       //然后我们在一些方法或者生命周期中就可以去访问我们需要访问的dom了
       const save = () => {
           internalInstance.refs.img // 这个就可以访问的到了
      }
  }
  ```

* Vue3 中 摈弃了 this，要获取当前 Vue 示例的上下文 需要用  getCurrentInstance 

  * **`getCurrentInstance` **只能**在 [setup](https://v3.cn.vuejs.org/api/composition-api.html#setup) 或[生命周期钩子](https://v3.cn.vuejs.org/api/composition-api.html#生命周期钩子)中调用。**

  ```vue
  <script>
  //getCurrentInstance
  import {getCurrentInstance} from 'vue'
  
  setup(){
  	const _this = getCurrentInstance(); // 通过getCurrentInstance得到我们想要的this，并把this传入createIntersectionObserver中
  }
  
  </script>
  ```

  
