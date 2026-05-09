# Getting started


## vite 构建

```
npm  config set "strict-ssl" false -g
npm create vite
Need to install the following packages:
create-vite@9.0.6
Ok to proceed? (y) y


> npx
> create-vite

│
◇  Project name:
│  startvue
│
◇  Select a framework:
│  Vue
│
◇  Select a variant:
│  JavaScript
│
◇  Install with npm and start now?
│  No
│
◇  Scaffolding project in /home/dengyouf/PycharmProjects/startvue...
│
└  Done. Now run:

  cd startvue
  npm install
  npm run dev
```
## 项目结构

```shell
tree startvue/
startvue/
├── index.html            # 应用的入口 HTML 文件
├── package.json          # 项目的依赖管理文件
├── public                # 静态资源目录（不会被 Vite 处理，直接复制到构建输出）
│   ├── favicon.svg
│   └── icons.svg
├── README.md             # 项目的说明文档，通常包含项目介绍、安装运行方法、使用说明等
├── src
│   ├── App.vue          # 根组件，所有其他组件的父组件，通常包含路由出口、全局布局等
│   ├── assets           # 需要构建处理的资源目录，这些资源会被 Vite 进行优化、哈希命名等处理
│   │   ├── hero.png
│   │   ├── vite.svg
│   │   └── vue.svg
│   ├── components       # 可复用的 Vue 组件
│   │   └── HelloWorld.vue
│   ├── main.js          # 应用的JavaScript 入口文件，创建 Vue 应用实例，导入全局样式、注册全局组件、挂载应用
│   └── style.css        # 全局样式文件，在 main.js 中导入，影响整个应用
└── vite.config.js        # Vite 配置文件配置开发服务器、插件、构建选项等，例如：配置代理、别名、插件使用等
```

## 选项式 API


=== "components/Options.vue"

    ``` vue
    <template>
        <div>
            <button @click="onUpdateCount">计数器{{ count }}</button>
        </div>
    </template>

    <script>

    export default {
        name: 'Options',
        data() {
            return {
                count: 0
            }
        },
        methods: {
            onUpdateCount() {
                this.count++
            }
        }
    }
    </script>

    <style scoped>

    </style>
    ```
=== "src/App.vue"

    ```vue
    <script setup>
    import Options from './components/Options.vue'
    </script>

    <template>
      <Options />
    </template>
    ```

## 组合式 API


=== "components/Compositions.vue"

    ```vue
    <template>
        <button @click="increment">计数器{{ count }}</button>
    </template>

    <script setup>
    import { ref } from 'vue';

    const count=ref(0)

    function increment(){
        count.value++
    }
    </script>

    <style lang="scss" scoped>

    </style>
    ```
=== "App.vue"

    ```
    <script setup>
    import Options from './components/Options.vue'
    import Compositions from './components/Compositions.vue';
    </script>

    <template>
      <Options />
      <hr>
      <Compositions />
    </template>
    ```

[//]: # (## 组件定义名称)

[//]: # ()
[//]: # (- 安装vite-plugin-vue-setup-extend)

[//]: # ()
[//]: # (vite-plugin-vue-setup-extend 是一个 Vite 插件，主要作用就是解决使用 <script setup> 语法糖时，无法直接为组件设置 name 属性的痛点)

[//]: # ()
[//]: # (```shell)

[//]: # (npm install vite-plugin-vue-setup-extend --save-dev)

[//]: # (```)

[//]: # ()
[//]: # (- 配置Vite)

[//]: # ()
[//]: # (=== "vite.config.js")

[//]: # ()
[//]: # (    ```js)

[//]: # (    import { defineConfig } from 'vite')

[//]: # (    import vue from '@vitejs/plugin-vue')

[//]: # (    import VueSetupExtend from 'vite-plugin-vue-setup-extend')

[//]: # ()
[//]: # (    // https://vite.dev/config/)

[//]: # (    export default defineConfig&#40;{)

[//]: # (      plugins: [)

[//]: # (        vue&#40;&#41;,)

[//]: # (        VueSetupExtend&#40;&#41;)

[//]: # (      ],)

[//]: # (    }&#41;)

[//]: # (    ```)

[//]: # ()
[//]: # (=== "src/components/Compositions.vue")

[//]: # ()
[//]: # (    ```vue)

[//]: # (    <template>)

[//]: # (        <button @click="increment">计数器{{ count }}</button>)

[//]: # (    </template>)

[//]: # ()
[//]: # (    <script setup>)

[//]: # (    import { ref } from 'vue';)

[//]: # ()
[//]: # (    const count=ref&#40;0&#41;)

[//]: # ()
[//]: # (    function increment&#40;&#41;{)

[//]: # (        count.value++)

[//]: # (    })

[//]: # (    </script>)

[//]: # ()
[//]: # (    <style lang="scss" scoped>)

[//]: # ()
[//]: # (    </style>)

[//]: # (    ```)

[//]: # (=== "src/App.vue")

[//]: # ()
[//]: # (    ```)

[//]: # (    <script setup>)

[//]: # (    import Options from './components/Options.vue')

[//]: # (    import Compositions from './components/Compositions.vue';)

[//]: # (    </script>)

[//]: # ()
[//]: # (    <template>)

[//]: # (      <Options />)

[//]: # (      <hr>)

[//]: # (      <Compositions />)

[//]: # (    </template>)

[//]: # (    ```)

## 响应式变量

!!! tip

    - 如果要定义一个基本数据类型，必须使用ref
    - 如果定义一个层级不是很深的复合类型可以使用ref也可以使用reactive
    - 如果定义一个层级很深的复合类型强烈推荐使用reeactive

### ref函数

ref函数即可以定义基本数据类型(数值类型，字符串，布尔类型)，还可以定义复合的数据类型(对象，数组)

=== "components/Person.vue"

    ```vue
    <template>
        <p>用户名: {{ username }}</p>
        <button @click="changeUsername">修改用户名</button>
    </template>

    <script setup>
    import { ref } from 'vue';

    const username = ref('张三')

    const changeUsername = () => {
        username.value = 'zhanshan'
    }
    </script>

    <style lang="scss" scoped>

    </style>
    ```

=== "App.vue"

    ```vue
    <script setup>
    import Person from './components/Person.vue';
    </script>

    <template>
        <Person />
    </template>

    <style scoped>
    </style>
    ```

### reactive函数

reactive 不能用来定义基本数据类型，可以用于定义复合类型(数组，对象)的响应式变量

=== "components/Book.vue"

    ```vue
    <template>
        <p>作者：{{bookinfo.author}}</p>
        <p>书籍：{{bookinfo.book_name}}</p>

        <button @click="changeBookInfo">更改📖信息</button>
    </template>

    <script setup>
    import { reactive } from 'vue';

    const bookinfo = reactive({
        author: "吴承恩",
        book_name: "西游记"
    })

    const changeBookInfo = () =>{
        // 修改单个值不需要.value
        // bookinfo.author ="罗贯中"
        // bookinfo.book_name ="三国演义"
        // 重新给响应式变量赋值，需要使用Object.assign方法
        Object.assign(bookinfo, {'author': '曹雪芹', 'book_name': '红楼梦'})
    }

    </script>

    <style scoped>

    </style>
    ```

## 模板语法

### 文本`{{}}`

在html中使用 `{{ }}`

=== "components/Syntax.vue"

    ```vue
    <template>
        <p>{{ username }}</p>
        <!--  v-once 使用默认值，不会被修改-->
        <p v-once> {{username}}</p>
        <button @click="changeUsername">修改名字</button>
    </template>

    <script setup>
    import { ref } from 'vue';


    const username = ref("张三")

    const changeUsername = () => {
        username.value = "zhanshan"
    }
    </script>

    <style scoped>

    </style>
    ```

=== "App.vue"

    ```vue
    <script setup>
    import Syntax from './components/Syntax.vue';
    </script>

    <template>
        <Syntax />
    </template>

    <style scoped>
    </style>
    ```

### v-html

Vue 默认会将数据中的 HTML 标签当作纯文本处理，以防止跨站脚本攻击（XSS）， 如果要渲染需要使用 v-html 指令。

=== "components/Syntax.vue"

    ```vue
    <template>
         <div v-html="html_code"></div>
    </template>

    <script setup>
    import { ref } from 'vue';

    const html_code = ref("<h1>hello world</h1>")
    </script>

    <style scoped>

    </style>
    ```

### v-model

=== "components/Syntax.vue"

    ```vue
    <template>
         <p>Message is: {{ message }}</p>
         <!-- <input v-model="message" placeholder="edit me" /> -->
         <input :value="message" @input="message = $event.target.value" />
    </template>

    <script setup>
    import { ref } from 'vue';
    const message=ref("")

    </script>

    <style scoped>

    </style>
    ```

=== "components/Syntax.vue 案例2"

    ```VUE
    <template>
      <form @submit.prevent="handleSubmit">
        <input type="text" v-model="form.username" placeholder="用户名" />
        <input type="password" v-model="form.password" placeholder="密码" />
        <button type="submit">提交</button>
      </form>
      <button @click="getInput">获取Input</button>
    </template>

    <script setup>
    import { reactive } from 'vue';

    const form = reactive({
      username: "",
      password: ""
    })

    const getInput = () => {
      console.log("用户名:", form.username)
      console.log("密码:", form.password)
    }

    const handleSubmit = () => {
      console.log("提交表单:", form)
    }
    </script>
    ```


### javascript

=== "components/Syntax.vue"

    ```vue
    <template>
         <p>年龄: {{ age }} - {{ age > 18 ? "成年人" : "未成年" }}</p>
         <button @click="age++">年龄+</button>
    </template>

    <script setup>
    import { ref } from 'vue';
    const age=ref(17)


    </script>

    <style scoped>

    </style>
    ```

### v-if

=== "components/Syntax.vue"

    ```vue
    <template>
       <p>{{age}}</p>
       <p v-if="age >= 18">成年人</p>
       <p v-else-if="age >= 0 && age < 18">未成年人</p>
       <!-- v-else 永远不能带条件，-->
       <p v-else> {{age}} 非法年龄</p>
       <button @click="age++">年龄+</button>
       <button @click="age--">年龄-</button>
    </template>

    <script setup>
    import { ref } from 'vue';
    const age=ref(17)


    </script>

    <style scoped>

    </style>
    ```

### v-show

=== "components/Syntax.vue"

    ```vue

    <template>
       <h1 v-show="ok">Hello!</h1>
       <button @click="ok = !ok">Toggler</button>
    </template>

    <script setup>
    import { ref } from 'vue';

    const ok = ref(true)


    </script>

    <style scoped>

    </style>
    ```

!!! Tip

    v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要频繁切换，则使用 v-show 较好；如果在运行时绑定条件很少改变，则 v-if 会更合适

### v-for

=== "components/Syntax.vue"

    ```vue
    <template>
       <table border="1" style="border-collapse: collapse">
          <thead>
             <tr>
                <td>序号</td>
                <td>作者</td>
                <td>书籍</td>
             </tr>
          </thead>
          <tbody>
             <!-- 循环books.items -->
             <tr v-for="(book, index) in books.items" :key="index">
                <td>{{ index +1  }}</td>
                <td>{{ book.author }}</td>
                <td>{{ book.book_name }}</td>
             </tr>
          </tbody>
       </table>
       <button @click="changeBooks">书籍变更</button>
    </template>

    <script setup>
    import { reactive } from 'vue';
    // 更符合实际使用场景
    const books = reactive({
       items:[
       {"author": "罗贯中", "book_name":"三国演义"},
       {"author": "吴承恩", "book_name":"西游记"},
       {"author": "曹雪芹", "book_name":"红楼梦"},
       {"author": "施耐庵", "book_name":"水浒传"},
       ]
    })

    const changeBooks= ()=> {
       // // 1. 添加元素
       // // 第1个参数: 起始位置：从索引 1 开始添加
       // // 第2个参数: 删除个数：删除 0 个元素
       // // 第3个参数: 要插入的元素
       // books.items.splice(1, 0, {author: "dengyouf", book_name: "Vue从入门到放弃"})

       // // 2. 删除元素,从索引1开始，删除2个元素，也就是删除索引为1 2 一共两个元素
       // books.items.splice(1, 2)

       // 3. 替换元素， 从索引1开始，删除1个元素, 然后插入新值
       books.items.splice(1, 1,  {author: "dengyouf", book_name: "Vue从入门到放弃"})
    }
    </script>

    <style scoped>

    </style>
    ```
## 事件绑定

### v-on

=== "components/Syntax.vue"
    ```vue
    <template>
       <!-- <a href="http://www.baidu.com" v-on:click.prevent="goto360($event)">百度</a> -->
          <a href="http://www.baidu.com" @click.prevent="goto360($event)">百度</a>
    </template>

    <script setup>
    const goto360 = (event) =>{
       // event.preventDefault()
       window.location ="https://www.qq.com"
    }
    </script>

    <style  scoped>

    </style>
    ```

### ref属性
=== "components/Syntax.vue 案例1"

    ```vue
    <template>
      <input type="text" ref="usernameref">
      <button @click="getUsername">获取username</button>
    </template>

    <script setup>
    import { ref } from 'vue';

    const usernameref=ref("")
    const getUsername=() => {
       console.log(usernameref.value.value)
    }
    </script>

    <style  scoped>

    </style>
    ```


## 计算属性

### computed

=== "components/Syntax.vue"

    ```vue
    <template>
       <label for="width">长</label>
       <input type="number" name="width" v-model="width">
       <label for="height">宽</label>
       <input type="number" name="height" v-model="height">
        <label for="height">面积</label>
       <input type="number" name="area" :value="area" readonly="">
    </template>

    <script setup>
    import { computed, ref } from 'vue';

    const width = ref(0)
    const height = ref(0)

    const area = computed(() => {
       return width.value*height.value
    })
    </script>

    <style scoped>

    </style>
    ```

## 属性监听

=== "components/Syntax.vue (ref)"

    ```vue
    <template>
       <p>width：{{ width }}</p>
       <div>
          <p>用户名:{{ person.username }}</p>
          <p>年龄:{{ person.age }}</p>
       </div>
       <button @click="changeWidth">改变</button>
    </template>

    <script setup>
    import { ref, watch } from 'vue';

    const width = ref(0)
    const person = ref({
       username: "dengyouf",
       age: 18
    })

    const changeWidth = () => {
       width.value++
       person.value ={"username": "DENGYOUF", "age": 32}
    }

    // 监听基本数据类型
    watch(width, (newValue, oldValue)=>{
       console.log(`new: ${newValue}, old: ${oldValue}`)
    })

    // 监听整个对象的改变
    watch(person, (newValue, oldValue)=>{
       console.log("new person: ", newValue, "_",oldValue)
    })

    // 监听对象的某个属性改变，采用getter函数形式person.value.username
    // watch(()=>{return person.value.username},  (newValue, oldValue) =>{})
    watch(()=> person.value.username, (newValue, oldValue) => {
       console.log("用户名发生变化", newValue)
    })

    // 监听对象的所有属性改变，开启深度deep监听
    watch(person, (newValue, oldValue)=>{
       console.log("3. new person: ", newValue, "_",oldValue)
    }, {deep: true})


    </script>

    <style  scoped>

    </style>
    ```


=== "components/Syntax.vue (reactive默认开启深度监听)"

    ```vue
    <template>
       <div>
          <p>用户名:{{ person.username }}</p>
          <p>年龄:{{ person.age }}</p>
       </div>
       <button @click="changePerson">改变</button>
    </template>

    <script setup>
    import { reactive, watch } from 'vue';

    const person = reactive({
       username: "dengyouf",
       age: 18
    })

    const changePerson = () => {
       person.username = "HELLO"
       // 如果需要整体替换，使用 Object.assign
       // Object.assign(person, {username: "DENGYOUF", age: 34})
    }

    // ✅ 方式1：监听特定属性的变化
    watch(() => person.username, (newValue, oldValue) => {
       console.log("username 变化: ", newValue, "→", oldValue)
    })

    // ✅ 方式2：监听整个对象（deep 监听）
    watch(person, (newValue, oldValue) => {
       console.log("person 对象变化:", {
          username: newValue.username,
          age: newValue.age
       })
    }, { deep: true })

    // ✅ 方式3：同时监听多个属性
    watch(
       [() => person.username, () => person.age],
       ([newName, newAge], [oldName, oldAge]) => {
          console.log("多个属性变化:", { newName, newAge }, { oldName, oldAge })
       }
    )
    </script>
    ```

## 生命周期

=== "components/LifeCycle.vue"

    ```vue
    <template>
        {{  count  }}
        <button @click="updateCount">更新count</button>
    </template>

    <script setup>
    import { onBeforeMount, onMounted,onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted,  ref } from 'vue';

    const count = ref(0)

    const updateCount = () => {
        count.value++
    }
    // 1. 创建阶段
    console.log("setup")

    // 2. 挂载阶段
    onBeforeMount (() => {
        console.log("before mounted")
    })

    onMounted(() => {
        console.log("onmounted")
    })
    // 3.更新阶段
    onBeforeUpdate( () =>{
        console.log("before update")
    })
    onUpdated (() => {
        console.log("update")
    })

    // 4.卸载阶段
    onBeforeUnmount( () => {
        console.log("before unmounted")
    })
    onUnmounted ( () => {
        console.log("unmounted")
    })
    </script>

    <style scoped>

    </style>
    ```
=== "App.vue"

    ```vue
    <script setup>
    import { ref } from 'vue';
    import LifeCycle from './components/LifeCycle.vue';
    const show = ref(true)
    const togglerHandler =()=>{
        console.log("toggler")
        show.value = !show.value
    }
    </script>

    <template>
        <LifeCycle v-if="show"></LifeCycle>
        <button @click="togglerHandler">toggler</button>
    </template>

    <style scoped>
    </style>
    ```

## 自定义组件

### 定义属性defineProps

将父组件的变量传递给子组件

=== "components/ChildCom.vue"

    ```vue
    <template>
       <div>我是子组件</div>
       <!-- 使用数组 -->
       <!-- username 由调用者指定也就是父组件
       <p>用户名：{{ props.username }}</p> -->

       <!-- 使用对象 -->

       <p>用户名：{{ props.username }}</p>
       <p>性别: {{ props.gender }}</p>
       <p>身高：{{ props.body.height }}</p>
       <p>体重: {{ props.body.weight }}</p>
    </template>

    <script setup>
    import { defineProps } from 'vue';

    // 使用数组指定属性的名称
    // const props = defineProps(["username"])

    // 使用对象指定属性
    const props = defineProps({
        username: String,
        gender: {
            type: String,
            default: "男"
        },
        body: {
            height: Number,
            weight: Number
        }
    })
    </script>


    <style scoped>

    </style>
    ```

=== "components/ParentCom.vue"

    ```vue
    <template>
        <div>我是父组件</div>
        <hr>
        <ChildCom :username="name" :body="body"/>
    </template>

    <script setup>
    import { reactive, ref } from 'vue';
    import ChildCom from './ChildCom.vue';

    const name = ref("张三")
    const body = reactive({height: 180, weight: 80})

    </script>

    <style lang="scss" scoped>

    </style>
    ```

=== "App.vue"

    ```vue
    <script setup>
    import ParentCom from './components/ParentCom.vue';
    </script>

    <template>
        <ParentCom />
    </template>

    <style scoped>
    </style>
    ```

### 定义事件defineEmits

通过定义事件将子组件的值传递给父组件

=== "components/ChildCom.vue"

    ```vue
    <template>
       <div>我是子组件</div>
       <p>步数 {{ steps }}</p>
       <button @click="updateSteps">走路</button>
    </template>

    <script setup>
    import { ref } from 'vue';

    const steps = ref(0)

    // ✅ 推荐写法1：使用数组语法
    const emit = defineEmits(['walk'])

    // ✅ 推荐写法2：使用对象语法（支持事件验证）
    // const emit = defineEmits({
    //     walk: (steps) => {
    //         if (typeof steps !== 'number') {
    //             console.warn('walk 事件参数必须是数字')
    //             return false
    //         }
    //         return true
    //     }
    // })

    const updateSteps = () => {
        steps.value += 10
        emit("walk", steps.value)
    }
    </script>
    ```

=== "components/ParentCom.vue"

    ```vue
    <template>
        <div>我是父组件</div>
        <hr>
        <ChildCom @walk="personWalk"></ChildCom>
    </template>

    <script setup>
    import ChildCom from './ChildCom.vue';

    const personWalk = (step) => {
        console.log(`子组件走了 ${step} 步`)  // 输出：子组件走了 10 步、20 步...
    }
    </script>
    ```

=== "App.vue"

    ```vue
    <script setup>
    import ParentCom from './components/ParentCom.vue';
    </script>

    <template>
        <ParentCom />
    </template>

    <style scoped>
    </style>
    ```

### 双向绑定defineModel


=== "components/ChildCom.vue"

    ```vue
    <template>
       <div>我是子组件</div>
       <div>子组件步数: {{ steps }}</div>
       <button @click="updateSteps">子组件+10</button>
    </template>

    <script setup>
    import { defineModel, watch } from 'vue';

    const steps = defineModel()

    watch(steps, (newVal, oldVal) => {
        console.log(`Child: ${oldVal} → ${newVal}`)
    })

    const updateSteps = () => {
        steps.value += 10
    }
    </script>
    ```
=== "components/ParentCom.vue"

    ```vue
    <template>
        <div>我是父组件</div>
        <div>父组件步数: {{ steps }}</div>
        <button @click="steps += 5">父组件+5</button>  <!-- 新增 -->
        <hr>
        <ChildCom v-model="steps"></ChildCom>
    </template>

    <script setup>
    import { ref, watch } from 'vue';
    import ChildCom from './ChildCom.vue';

    const steps = ref(0)

    watch(steps, (newVal, oldVal) => {
        console.log(`Parent: ${oldVal} → ${newVal}`)
    })
    </script>
    ```

=== "App.vue"

    ```vue
    <script setup>
    import ParentCom from './components/ParentCom.vue';
    </script>

    <template>
        <ParentCom />
    </template>

    <style scoped>
    </style>
    ```
### 插槽Slot

=== "components/ChildCom.vue"

    ```vue
    <template>
        <button type="submit">
            <slot></slot>
        </button>
    </template>

    <script setup>

    </script>

    <style  scoped>

    </style>
    ```

=== "components/ParentCom.vue"

    ```vue
    <template>
        <ChildCom>登录</ChildCom>
    </template>

    <script setup>
    import ChildCom from './ChildCom.vue';

    </script>

    <style  scoped>

    </style>
    ```

=== "App.vue"

    ```vue
    <script setup>
    import ParentCom from './components/ParentCom.vue';
    </script>

    <template>
        <ParentCom />
    </template>

    <style scoped>
    </style>
    ```

### 具名插槽Slot

=== "components/ChildCom.vue"

    ```vue
    <template>
        <header>
            <slot name="header"></slot>
        </header>
        <main>
            <slot name="main"></slot>
        </main>
        <footer>
            <slot name="footer"></slot>
        </footer>
        <!-- 默认插槽 -->
        <slot></slot>
    </template>

    <script setup>

    </script>

    <style scoped>

    </style>
    ```

=== "components/ParentCom.vue"

    ```vue
    <template>
        <ChildCom>

            <!-- header -->
            <template #header>
                这是首部
            </template>
            <!--main-->
            <template #main>
                这是主体
            </template>
            <!--footer-->
            <template #footer>
                这是底部
            </template>


            <!-- <template #default>
                <h1>默认插槽</h1>
            </template> -->
           <h1>默认插槽</h1>
        </ChildCom>
    </template>

    <script setup>
    import ChildCom from './ChildCom.vue';

    </script>

    <style  scoped>

    </style>
    ```

=== "App.vue"

    ```vue
    <script setup>
    import ParentCom from './components/ParentCom.vue';
    </script>

    <template>
        <ParentCom />
    </template>

    <style scoped>
    </style>
    ```

### 插槽作用域

=== "components/ChildCom.vue"

    ```vue
    <template>
        <header>
            <slot name="header" :person="p1"></slot>
        </header>
        <main>
            <slot name="main" :person="p2"></slot>
        </main>
        <footer>
            <slot name="footer" :person="p3"></slot>
        </footer>
        <!-- 默认插槽 -->
        <slot :person="p4"></slot>
    </template>

    <script setup>
    import { ref } from 'vue';


    const p1 = ref("张三")
    const p2 = ref("李四")
    const p3 = ref("王五")
    const p4 = ref("赵六")
    </script>

    <style scoped>

    </style>
    ```

=== "components/ParentCom.vue"

    ```vue
    <template>
        <ChildCom>

            <!-- header -->
            <template #header="scoped">
                这是首部 - {{ scoped.person}}
            </template>
            <!--main-->
            <template #main="scoped">
                这是主体 - {{ scoped.person}}
            </template>
            <!--footer-->
            <template #footer="scoped">
                这是底部 - {{ scoped.person}}
            </template>


            <template #default="scoped">
                <h1>默认插槽- {{ scoped.person}}</h1>
            </template>
           <!-- <h1>默认插槽</h1> -->
        </ChildCom>
    </template>

    <script setup>
    import ChildCom from './ChildCom.vue';

    </script>

    <style  scoped>

    </style>
    ```
=== "App.vue"

    ```vue
    <script setup>
    import ParentCom from './components/ParentCom.vue';
    </script>

    <template>
        <ParentCom />
    </template>

    <style scoped>
    </style>
    ```

## VueRouter

### 基本使用


=== "views/HomeView.vue"

    ```vue
    <template>
        <h1>Home</h1>
    </template>

    <script setup>

    </script>

    <style scoped>

    </style>
    ```

=== "views/AboutView.vue"

    ```vue
    <template>
        <h1>About</h1>
    </template>

    <script setup>

    </script>

    <style  scoped>

    </style>
    ```

=== "router/index.js"

    ```js
    // 定义路由
    import { createRouter, createWebHistory } from "vue-router";
    // 直接导入
    import HomeView from "../views/HomeView.vue"
    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: [
            {
                path: "/", name: "home", component: HomeView
            },
            {   path: "/home", redirect: "/"},
            {
                path: "/about", name: "about", component: ()=> import("../views/AboutView.vue") // 动态导入
            }
        ]
    })

    export default router
    ```

=== "main.js"

    ```js
    import { createApp } from 'vue'
    import './style.css'
    import App from './App.vue'
    // 加载路由
    import  router  from "./router/index.js"
    const app = createApp(App)
    // 加载路由
    app.use(router)
    app.mount('#app')
    ```

=== "App.vue"

    ```vue
    <script setup>
    import { RouterLink, RouterView } from 'vue-router';
    </script>

    <template>
        <header>
            <div class="wrapper">
                <nav>
                    <RouterLink to="/">Home</RouterLink>
                    <br>
                    <RouterLink to="/about">About</RouterLink>
                </nav>
            </div>
        </header>
        <!-- 这里是动态内容 -->
        <!-- <router-view></router-view> -->
         <!-- <RouterView></RouterView> -->
          <RouterView/>
    </template>

    <style scoped>
    .wrapper {
        display: block;
        margin: 18px;
    }
    </style>
    ```

### 子路由

=== "router/index.js"

    ```js
    import { createRouter, createWebHistory } from "vue-router";

    import HomeView from "../views/HomeView.vue"
    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: [
            {
                path: "/home",
                name: "home",
                component: HomeView ,
                children:[
                    {
                        path: "sub1", //不以 / 开头，相对于 /home/sub1
                        name: "sub1",
                        component: ()=> import( "../views/Sub1View.vue")
                    },
                    {
                        path: "/home/sub2", // 完整路径
                        name: "sub2",
                        component: ()=> import( "../views/Sub2View.vue")
                    },
                ]
            },
            { path: "/", redirect: "/home" },  // 重定向放在后面也可以
            { path: "/about", name: "about", component: () => import("../views/AboutView.vue") }
        ]
    })

    export default router
    ```

=== "views/HomeView.vue"

    ```vue
    <template>
        <h1>Home</h1>
        <RouterLink to="/home/sub1">子页面1</RouterLink> &nbsp;
         <RouterLink :to="{ name: 'sub2' }">子页面2</RouterLink>
        <hr>
        <!-- 子路由显示在这里  -->
        <RouterView />
    </template>

    <script setup>
    import { RouterLink } from 'vue-router';

    </script>

    <style scoped>

    </style>
    ```

=== "views/Sub1View.vue"

    ```vue
    <template>
        <p>子页面1</p>
    </template>

    <script setup>

    </script>

    <style scoped>

    </style>
    ```

=== "views/Sub2View.vue"

    ```vue
    <template>
        <p>子页面2</p>
    </template>

    <script setup>

    </script>

    <style scoped>

    </style>
    ```

### 路由传参


=== "router/index.js"

    ```js
    import { createRouter, createWebHistory } from "vue-router";

    import HomeView from "../views/HomeView.vue"
    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: [
            {
                path: "/home",
                name: "home",
                component: HomeView ,
                children:[
                    {
                        // http://localhost:5173/home/sub1/1
                        path: "sub1/:pk", //不以 / 开头，相对于 /home/sub1
                        name: "sub1",
                        component: ()=> import( "../views/Sub1View.vue")
                    },
                    {
                        path: "/home/sub2",
                        name: "sub2",
                        component: ()=> import( "../views/Sub2View.vue")
                    },
                ]
            },
            { path: "/", redirect: "/home" },  // 重定向放在后面也可以
            { path: "/about", name: "about", component: () => import("../views/AboutView.vue") }
        ]
    })

    export default router
    ```

=== "views/HomeView.vue"

    ```vue
    <template>
        <h1>Home</h1>
        <!-- 路径参数 (params)  -->
        <!-- http://localhost:5173/home/sub1/1 -->
         <RouterLink to="/home/sub1/1">子页面1-1</RouterLink>
         <RouterLink :to="{ name: 'sub1', params:{pk: 2} }">子页面1-2</RouterLink>

         <!-- 查询参数 (query) -->
          <!-- http://localhost:5173/home/sub2/?page=1 -->
         <RouterLink to="/home/sub2/?page=1">子页面2-1</RouterLink>
         <RouterLink :to="{ name: 'sub2', query:{page: 2} }">子页面2-2</RouterLink>
        <hr>
        <!-- 子路由显示在这里  -->
        <RouterView />
    </template>

    <script setup>
    import { RouterLink } from 'vue-router';

    </script>

    <style scoped>

    </style>
    ```

=== "views/Sub1View.vue"

    ```vue
    <template>
        <p>子页面1</p>
        {{ pk }}
    </template>

    <script setup>
    // 获取参数
    import { onMounted, onUpdated ,computed } from 'vue';
    import { useRoute } from 'vue-router';

    const route = useRoute()

    onMounted(()=>{
        const params = route.params
        console.log(params)
    })
    // onMounted 只在组件创建时执行一次，而 onUpdated 会在每次参数变化时执行
    onUpdated(()=>{
        const params = route.params
        console.log(params)
    })

    // 自动追踪并缓存 route.params.pk
    const pk = computed(() => route.params.pk)
    </script>

    <style scoped>

    </style>
    ```

=== "views/Sub2View.vue"

    ```vue
    <template>
        <p>子页面2</p>
        {{ page }}
    </template>

    <script setup>
    import { onMounted, onUpdated ,computed } from 'vue';
    import { useRoute } from 'vue-router';

    const route = useRoute()

    onMounted(()=>{
        const query = route.query
        console.log(query)
    })
    // onMounted 只在组件创建时执行一次，而 onUpdated 会在每次参数变化时执行
    onUpdated(()=>{
        const query = route.query
        console.log(query)
    })

    // 自动追踪并缓存 route.params.pk
    const page = computed(() => route.query.page)

    </script>

    <style scoped>

    </style>
    ```
### 路由导航

=== "router/index.js"

    ```vue
    import { createRouter, createWebHistory } from "vue-router";

    import HomeView from "../views/HomeView.vue"
    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: [
            {
                path: "/home",
                name: "home",
                component: HomeView ,
            },
            { path: "/", redirect: "/home" },  // 重定向放在后面也可以
            { path: "/login", name: "login", component: () => import("../views/LoginView.vue") }
        ]
    })

    export default router
    ```

=== "App.vue"

    ```vue
    <script setup>
    import { useRouter } from 'vue-router';

    const router = useRouter()
    const btnLogin = () => {
        console.log("跳转到登录页面")
        // push 会添加历史记录
        // router.push("/login")
        router.push({ name: "login" })
    }
    const btnHome = () => {
        console.log("跳转到主页面")
        // replace 会替换当前历史记录
        // router.replace("/home")
        router.replace({ name: "home" })
    }
    </script>

    <template>
        <header>
            <div class="wrapper">
                <nav>
                    <RouterLink to="/">Home</RouterLink> |
                    <RouterLink :to="{name: 'login'}">login</RouterLink>
                </nav>
            </div>
            <button @click="btnLogin">跳转到登录页</button>
            <button @click="btnHome">跳转到主页</button>
        </header>
          <RouterView/>
    </template>

    <style scoped>
    .wrapper {
        display: block;
        margin: 18px;
    }
    </style>

    ```

=== "views/HomeView.vue"

    ```vue
    <template>
        <h1>Home</h1>
    </template>

    <script setup>

    </script>

    <style scoped>

    </style>
    ```
=== "views/LoginView.vue"

    ```vue
    <template>
        <h1>登录页</h1>
    </template>

    <script setup>

    </script>

    <style scoped>

    </style>
    ```

### 路由守卫

=== "router/index.js"

    ```vue
    import { createRouter, createWebHistory } from "vue-router";

    import HomeView from "../views/HomeView.vue"
    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: [
            {
                path: "/home",
                name: "home",
                component: HomeView ,
            },
            { path: "/", redirect: "/home" },  // 重定向放在后面也可以
            { path: "/login", name: "login", component: () => import("../views/LoginView.vue") },
            {
                path: "/about",
                name: "about",
                component: () => import("../views/AboutView.vue") ,
                // 路由导航守卫
                beforeEnter: (to, from) => {
                    console.log("beforeEnter", to, from)
                    return false
                }
            }
        ]
    })

    // 全局守卫
    router.beforeEach((to, from) => {
        console.log(to.path, from.path)
        if ( to.path !== "/login" ) {
            // 如果获取不到token,则返回的登录页
            const token = localStorage.getItem("token")
            console.log("token:", token)
            if (!token) {
                return "/login"
            }
        }
        return true
    })
    export default router
    ```

=== "views/HomeView.vue"

    ```vue
    <template>
        <h1>Home</h1>
        <button @click="btnLogout">登出</button>
    </template>

    <script setup>

    import { onBeforeRouteLeave, onBeforeRouteUpdate, useRouter } from 'vue-router';

    const router = useRouter()
    const btnLogout=()=>{
        // 模拟登出
        localStorage.removeItem("token")
        router.push("login")
    }
    // 组件内导航守卫，离开守卫：防止未保存的内容丢失
    onBeforeRouteLeave((to, from) => {
        console.log("onBeforeRouteLeave",to, from)
        const answer = window.confirm("是否确认离开？")
        if  (!answer) {
            return false
        }
    })
    // 组件内导航守卫， 更新守卫：当路由参数变化时
    onBeforeRouteUpdate((to, from)=>{
        console.log("onBeforeRouteUpdate", to, from)
    })
    </script>

    <style scoped>

    </style>
    ```

=== "views/LoginView.vue"

    ```vue
    <template>
        <h1>登录页</h1>
        <button @click="btnLogin">登录</button>
    </template>

    <script setup>
    import { useRouter } from 'vue-router';

    const router = useRouter()
    const btnLogin=()=>{
        // 模拟登录
        localStorage.setItem("token", "helloworld")
        router.push("/home")
    }
    </script>

    <style scoped>

    </style>
    ```

=== "App.vue"

    ```vue
    <script setup>
    import { useRouter } from 'vue-router';

    const router = useRouter()
    const btnLogin = () => {
        console.log("跳转到登录页面")
        // router.push("/login")
        router.push({ name: "login" })
    }
    const btnHome = () => {
        console.log("跳转到主页面")
        // router.replace("/home")
        router.replace({ name: "home" })
    }
    </script>

    <template>
        <header>
            <div class="wrapper">
                <nav>
                    <RouterLink to="/">Home</RouterLink> |
                    <RouterLink :to="{name: 'login'}">Login</RouterLink> |
                    <RouterLink :to="{name: 'about'}">About</RouterLink>
                </nav>
            </div>
            <button @click="btnLogin">跳转到登录页</button>
            <button @click="btnHome">跳转到主页</button>
        </header>
          <RouterView/>
    </template>

    <style scoped>
    .wrapper {
        display: block;
        margin: 18px;
    }
    </style>
    ```

### 路由元信息

=== "router/index.js"

    ```vue
    import { createRouter, createWebHistory } from "vue-router";

    import HomeView from "../views/HomeView.vue"
    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: [
            {
                path: "/home",
                name: "home",
                component: HomeView ,
                // 通过 meta设置 /home 设置为公开的页面了
                meta: {requireAuth: false}
            },
            { path: "/", redirect: "/home" },
            { path: "/login", name: "login", component: () => import("../views/LoginView.vue") },
            {
                path: "/about",
                name: "about",
                component: () => import("../views/AboutView.vue") ,
                meta: {requireAuth: true}
            }
        ]
    })

    // 全局守卫
    router.beforeEach((to, from) => {
        console.log(to.path, from.path)
        if ( to.path !== "/login" && to.meta.requireAuth ==true) {
            const token = localStorage.getItem("token")
            console.log("token:", token)
            if (!token) {
                return "/login"
            }
        }
        return true
    })
    export default router
    ```

=== "views/HomeView.vue"

    ```vue
    <template>
        <h1>Home</h1>
        <button @click="btnLogout">登出</button>
    </template>

    <script setup>

    import { onBeforeRouteLeave, onBeforeRouteUpdate, useRouter } from 'vue-router';

    const router = useRouter()
    const btnLogout=()=>{
        // 模拟登出
        localStorage.removeItem("token")
        router.push("login")
    }
    // 组件内导航守卫，离开守卫：防止未保存的内容丢失
    onBeforeRouteLeave((to, from) => {
        console.log("onBeforeRouteLeave",to, from)
        const answer = window.confirm("是否确认离开？")
        if  (!answer) {
            return false
        }
    })
    // 组件内导航守卫， 更新守卫：当路由参数变化时
    onBeforeRouteUpdate((to, from)=>{
        console.log("onBeforeRouteUpdate", to, from)
    })
    </script>

    <style scoped>

    </style>
    ```

=== "views/LoginView.vue"

    ```vue
    <template>
        <h1>登录页</h1>
        <button @click="btnLogin">登录</button>
    </template>

    <script setup>
    import { useRouter } from 'vue-router';

    const router = useRouter()
    const btnLogin=()=>{
        // 模拟登录
        localStorage.setItem("token", "helloworld")
        router.push("/home")
    }
    </script>

    <style scoped>

    </style>
    ```

=== "App.vue"

    ```vue
    <script setup>
    import { useRouter } from 'vue-router';

    const router = useRouter()
    const btnLogin = () => {
        console.log("跳转到登录页面")
        // router.push("/login")
        router.push({ name: "login" })
    }
    const btnHome = () => {
        console.log("跳转到主页面")
        // router.replace("/home")
        router.replace({ name: "home" })
    }
    </script>

    <template>
        <header>
            <div class="wrapper">
                <nav>
                    <RouterLink to="/">Home</RouterLink> |
                    <RouterLink :to="{name: 'login'}">Login</RouterLink> |
                    <RouterLink :to="{name: 'about'}">About</RouterLink>
                </nav>
            </div>
            <button @click="btnLogin">跳转到登录页</button>
            <button @click="btnHome">跳转到主页</button>
        </header>
          <RouterView/>
    </template>

    <style scoped>
    .wrapper {
        display: block;
        margin: 18px;
    }
    </style>
    ```
## Pinia

=== "main.js"

    ```js
    import { createApp } from 'vue'
    import './style.css'
    import App from './App.vue'
    // 加载路由
    import  router  from "./router/index.js"
    // 导入 Pinia
    import { createPinia } from 'pinia'
    const app = createApp(App)

    app.use(router)
    app.use(createPinia())
    app.mount('#app')
    ```

=== "stores/counter.js 组合式"

    ```js
    import { defineStore } from "pinia";
    import { computed, ref } from "vue";

    // // 组合式
    // export const useCounterStore = defineStore("counter", ()=> {
    //     // setup
    //     const count = ref(0)
    //     const game = ref("篮球")
    //     const doubleCount = computed(()=> count.value*2)

    //     function increment () {
    //         count.value ++
    //     }

    //     // 如果使用setup(组合式API),那么必须要return 变量,计算属性和方法，如果不返回则无法在外面是使用
    //     return { count, doubleCount, increment, game}
    // })

    // 选项式
    export const useCounterStore = defineStore("counter", {
        // state, getters, actions
        state() {
            return {
                count: 0,
                game: "篮球"
            }
        },
        getters: {
            doubleCount() {
                return this.count*2
            }
        },
        actions: {
            increment(){
                this.count++
            }
        }
    })
    ```

=== "views/AboutView.vue"

    ```vue
    <template>
        {{ counterStore.count }}
        {{ counterStore.doubleCount }}
        {{counterStore.game }}
        <button @click="updateCount">更新</button>
    </template>

    <script setup>
    import { useCounterStore } from '../components/stores/counter';

    const counterStore = useCounterStore()

    const updateCount= () => {
        // 1. 直接更新
        // counterStore.count+=1
        // 2. 批量更新
        // counterStore.$patch({
        //     count: counterStore.count+2,
        //     game: "足球"
        // })
        // 3. 通过方法counterStore.increment
        counterStore.increment()
    }

    </script>

    <style  scoped>

    </style>
    ```


