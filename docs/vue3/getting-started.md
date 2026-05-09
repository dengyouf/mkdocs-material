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
