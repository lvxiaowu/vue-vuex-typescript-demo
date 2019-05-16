# 自己搭建Vue + Vuex + Typescript 项目的使用



## 为什么使用TypeScript（主要原因）
    
### 1. JavaScript的超集
支持所有原生JavaScript的语法
### 2. 强类型语言
现在很多主流语言都是强类型的，而这点也一直是JavaScript所被人诟病的地方。使用TypeScript之后，将会在代码调试、重构等步骤节省很多时间。

> 比如说：函数在返回值的时候可能经过复杂的操作，那我们如果想要知道这个值的结构就需要去仔细阅读这段代码。那如果有了TypeScript之后，直接就可以看到函数的返回值结构，将会非常的方便

## TypeScript配置

### 1. Webpack
* 首先需要安装`ts-loader`，这是TypeScript为Webpack提供的编译器，类似于`babel-loader`
```bash
npm i ts-loader -D
```
* 配置rules
接着在Webpack的`module.rules`里面添加对ts的支持(我这里的webpack版本是2.x)：

```javascript
{
    test: /\.vue$/,
    loader: 'vue-loader',
    options: vueLoaderConfig
},
{
    test: /\.ts$/,
    loader: 'ts-loader',
    options: {
      appendTsSuffixTo: [/\.vue$/],
    }
}
```
* 配置extensions
添加可识别文件后缀对ts的支持，如：

```javascript
extensions: ['.js', '.vue', '.json', '.ts']
```
### 2. tsconfig.json
创建tsconfig.json文件，放在根目录下，和`package.json`同级

配置内容主要也看个人需求，具体可以去typescript的官网查看，但是有一点需要注意：
> 在Vue中，你需要引入 strict: true (或者至少 noImplicitThis: true，这是 strict 模式的一部分) 以利用组件方法中 this 的类型检查，否则它会始终被看作 any 类型。

```javascript
{
  "include": [
    "src/*",
    "src/**/*"
  ],
  "exclude": [
    "node_modules"
  ],
  "compilerOptions": {
    // types option has been previously configured
    "types": [
      // add node as an option
      "node"
    ],
    // typeRoots option has been previously configured
    "typeRoots": [
      // add path to @types
      "node_modules/@types"
    ],
    // 以严格模式解析
    "strict": true,
    // 在.tsx文件里支持JSX
    "jsx": "preserve",
    // 使用的JSX工厂函数
    "jsxFactory": "h",
    // 允许从没有设置默认导出的模块中默认导入
    "allowSyntheticDefaultImports": true,
    // 启用装饰器
    "experimentalDecorators": true,
    "strictFunctionTypes": false,
    // 允许编译javascript文件
    "allowJs": true,
    // 采用的模块系统
    "module": "esnext",
    // 编译输出目标 ES 版本
    "target": "es5",
    // 如何处理模块
    "moduleResolution": "node",
    // 在表达式和声明上有隐含的any类型时报错
    "noImplicitAny": true,
    "lib": [
      "dom",
      "es5",
      "es6",
      "es7",
      "es2015.promise"
    ],
    "sourceMap": true,
    "pretty": true
  }
}
```

### 3. 修改main.js
*  把项目主文件`main.js`修改成`main.ts`，里面的写法基本不变，但是有一点需要注意：
引入Vue文件的时候需要加上`.vue`后缀,否则编辑器识别不到
* 把webpack的entry文件也修改成`main.ts`


## 4. vue-shims.d.ts
  TypeScript并不支持Vue文件，所以需要告诉TypeScript`*.vue`文件交给vue编辑器来处理。解决方案就是在创建一个vue-shims.d.ts文件，
    创建vue-shims.d.ts文件，可放在新建文件夹tsconf下
> *.d.ts类型文件不需要手动引入，TypeScript会自动加载

```javascript
declare module '*.vue' {
  import Vue from 'vue';
  export default Vue;
}

```

## 5. 全局变量的使用 
你可以通过 declare 关键字，来告诉 TypeScript，你正在试图表述一个其他地方已经存在的代码
创建vue-shims.global.ts文件，可放在新建文件夹tsconf下

```javascript
declare let $:(select:string) => any
declare let wx: any
interface Window {
    Bridge: any,
    _XT: any
}
```

## 6.Vue 的全局/实例属性和方法、组件
创建vue-shims.option.ts文件，可放在新建文件夹tsconf下
```javascript
import Vue from 'vue'
declare module 'vue/types/vue' {
    // 可以使用 `VueConstructor` 接口
    // 来声明全局属性
    interface Vue {
        $axios: any,
        Login: (options?:any):void
    }
}
```
## 7.引入第三方库的时候需要额外声明文件
创建vue-shims.package.ts文件，可放在新建文件夹tsconf下
```javascript
declare module 'vue-awesome-swiper' {
  export const swiper: any
  export const swiperSlide: any
}

declare module 'vue-lazyload'
```

##  第三方插件库

### vue-class-component
 [vue-class-component](https://github.com/vuejs/vue-class-component)  是官方维护的TypeScript装饰器，写法比较扁平化
### Vue-Property-Decorator
[vue-property-decorator](https://github.com/kaorun343/vue-property-decorator) 是在 vue-class-component 上增强了更多的结合 Vue 特性的装饰器，新增了这 7 个装饰器

- `@Emit`
- `@Inject`
- `@Model`
- `@Prop`
- `@Provide`
- `@Watch`
- `@Component` (从 vue-class-component 继承)

```javascript
import  { Component, Vue, Watch, Prop  }  from 'Vue-Property-Decorator'
import Header from '@/component/header.vue'
import MyMixin from './mixin.js'

Vue.mixin(MyMixin);

@Component({
    // component
    components:{ Header }
})
export default class App extends Vue {
    // initial data
    name:string = 'xiaowu'

    // computed
    get getMyName():string {
        return `my name is ${this.name}`
    }

    // props
    @Prop({
        default: '',
        type: [String, Number]
    })
    phone: [string, number];

    // method
    jumpPage():void{
        console.log('jump page')
    }

    // lifecycle hook
    mounted(){
        console.log('mounted !!')
    }

    // watch 
    @Watch('state', { deep: true })
    onStateChange(n: any, o: any) {
        console.log('state is change ')   
    }
}

```
####  如何在组件内使用路由钩子
* 新建class-component-hooks.ts文件，在入口文件main.ts 引入(放在路由引入之前)，就可以正常使用

```javascript
// Register the router hooks with their names
Component.registerHooks([
  'beforeRouteEnter',
  'beforeRouteLeave',
  'beforeRouteUpdate' // for vue-router 2.2+
])  
```

### vuex-class
[vuex-class](https://github.com/ktsn/vuex-class)是基于基于`vue-class-component`对Vuex提供的装饰器。它的作者同时也是`vue-class-component`的主要贡献者，质量还是有保证的。

```javascript
import Vue from 'vue'
import Component from 'vue-class-component'
import {
  State,
  Getter,
  Action,
  Mutation,
  namespace
} from 'vuex-class'
 
const someModule = namespace('path/to/module')
 
@Component
export class MyComp extends Vue {
  @State('foo') stateFoo
  @State(state => state.bar) stateBar
  @Getter('foo') getterFoo
  @Action('foo') actionFoo
  @Mutation('foo') mutationFoo
  @someModule.Getter('foo') moduleGetterFoo
 
  // If the argument is omitted, use the property name
  // for each state/getter/action/mutation type
  @State foo
  @Getter bar
  @Action baz
  @Mutation qux
 
  created () {
    this.stateFoo // -> store.state.foo
    this.stateBar // -> store.state.bar
    this.getterFoo // -> store.getters.foo
    this.actionFoo({ value: true }) // -> store.dispatch('foo', { value: true })
    this.mutationFoo({ value: true }) // -> store.commit('foo', { value: true })
    this.moduleGetterFoo // -> store.getters['path/to/module/foo']
  }
}

```

