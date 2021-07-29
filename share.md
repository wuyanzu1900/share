## Vue 3.0 VS React-Hook
&#160;&#160;&#160;目前三大主流前端框架之中，Angular作为单页应用的先行者，虽然目前市场占有率正在被Vue和React慢慢蚕食，但是不可否认的是，Angular为后来的React和Vue提供了很多借鉴经验。比如双向绑定，组件解耦，响应式开发，包括配合TS的使用等等，都给后来的两个框架提供了一个很好的发展方向。但是今天主角是Vue和React，准确的来说是Vue3.0与React-Hook，作为两个框架功能迭代的新版本，都与其之前版本有很大的改变，并且也争议不断，那么为什么会有这么多争议呢？就让我们共同探究一下吧。 <br><br>

### 设计动机
  其实每个框架的版本更新目的只有两个:解决问题和解决更多问题。也只有这样，才能让框架不断保持活力与生命力。
- Vue 3.0
  <br>
  在Vue2.0的代码模式之中，随着功能的不断增长，复杂的代码变得越来越难维护，这种情况在接受别人代码时尤为显著。这种情况出现的根本原因是 Vue 的现有 API 通过选项组织代码,但是在实际的业务场景之中我们通常优先选择使用逻辑来组织代码。除此之外，也缺乏一种较为纯净的组件之间的复用逻辑机制，同样的类型推断的不友好，这些问题的出些都让Vue需要提出一种更优的方案，于是，Vue3.0的设计开始了。

- React-Hook
  <br>
  传统的class类组件之中，组件之间的复用状态逻辑很难，由 providers，consumers，高阶组件，render props 等其他抽象层组成的组件会形成“嵌套地狱”，导致代码的复用性变差，同样可读性也变得更差，而这样最终导致这些复杂组件变得难以理解，项目越到后期开发维护变得越来越艰难。而hook的设计初衷就是为了解决这些问题。

### 新特性
  通过对两个框架设计动机的比较，我们很容易发现，两个框架需要解决的都是要提高代码的复用性与可读性等问题。所以这两个框架的更新特性与模式就好像两辆从不同起点出发的汽车，要开向相同的终点，所以有些路肯定是一样的。那接下来让我们一起看看究竟有哪些相同与不相同的地方吧。
- Vue
    <br>Vue3.0的版本发布在社区里引起了巨大的轰动，社区里也出现大片的批评。其中声音最多的莫过于说Vue3.0是抄袭React-Hook，新特性的加入使得Vue越来越像React了，没有了自己的特征。其实尤大大自己也说过，Vue3.0的确有些借鉴了React-Hook的设计思想，因为这些特性React的确做的很不错。其实作为一个开源框架设计者而言，愿意去借鉴别人的优点，并且认真去学习，这才是能是框架不断成熟的根本。再者说，框架其实只是使用工具，我们使用它们是提效的，也没必要去吹毛求疵。好了，说了这么多，让我们来看看Vue3.0到底有哪些新特性吧。<br><br>
  **1. setup 函数**

    在新增的Composition Api的新特性之中，setup函数就是其统一的入口，setup 函数会在 beforeCreate 、created 之前执行。而在vue3.0之中，也没有了beforeCreate 、created这两个生命周期，所以我们也可以将setup当作一个生命周期函数来看。为什么说这是入口呢？因为在Vue3.0之中，我们不再单独写data，methods,watch，computed等方法，这些方法全部放在setup函数之中，同样的也不再需要使用this去拿取数据，通过setup函数之中return出去即可使用。具体用例如下:
    ```
    <template>
      <div class="hello">
        <div class="content">
          <span>{{ count }}</span>
          <span>{{ pushNumber }}</span>
        </div>
        <div class="action">
          <button @click="countNumber()">
            增加
          </button>
          <button @click="reduce()">
            减少
          </button>
          <button @click="push()">
            添加数组
          </button>
          <button @click="resetArray()">
            重置数组
          </button>
        </div>
      </div>
    </template>
    <script lang="ts">
    import {  reactive, ref,toRefs } from 'vue';

    export default {
  props: {
    value: String,
  },
  setup() {
    const count = ref<number>(0);
    const array:any = reactive({
      pushNumber:[]
    });
    const countNumber = () => {
      console.log(this,'this')
      count.value ++;
    };
    const reduce = () => count.value--;
    
    const push = () => {
      array.pushNumber.push(1);
    };
    const resetArray = () => {
      array.pushNumber.splice(0, array.length);
    };
    return {
      count,
      ...toRefs(array),
      countNumber,
      reduce,
      push,
      resetArray,
    };
    },
  };
  </script>

<br>在上述代码的样例之中，我们可以看到，在Vue3.0之中已经没有了单独的data，method，computed等方法，而是全部整合在了setup函数之中，并且眼尖的朋友会立马发现，$this$ 不见了，在setup之中将数据返回之后便能直接调用，而且为了让大家不再混淆this的用法，Vue3.0之中直接将$this$置为了$undefined$。看到setup的这个函数，要是熟悉React-Hook的朋友会立马说，这不就是UseEffect函数吗？$ref$和$reactive$不就和setState差不多嘛？可以说Vue3.0的这部分吧的确借鉴了React-Hook的模式，但是又有些不同，具体的我们稍后可以通过React-Hook的代码进行比对。通过上述代码，可能大家会不明白，这个$ref$和$Reactive$又是个啥？那就让我们来共同探讨一下吧。
  <br>
  - ref(响应式变量)<br>

    在Vue3.0之中，没有了data声明数据，多了一个ref，很多人会一脸懵逼，这是个什么玩意儿，这还是Vue吗？但是用了之后就会发现真香。我们来看看官方文档是怎么解释ref的:`ref 接收参数并将其包裹在一个带有 value property 的对象中返回，然后可以使用该 property 访问或更改响应式变量的值。`再来看个例子:
    ```
    import { ref } from 'vue'
    const counter = ref(0)
    console.log(counter) // { value: 0 }
    console.log(counter.value) // 0
    counter.value++
    console.log(counter.value) // 1
    ``` 
    从例子里可以很清楚的看到，counter是一个对象，ref将值封装在了一个对象里面，通过counter.value的方法拿到值，那么这就产生疑问了，为什么需要放置在对象里面呢?因为在JavaScript之中，简单数据类型(如String和Number)是通过值而非引用传递的，在值的外面封装一个对象，就可以保证其在传递时不会再某个时刻失去其响应性。我们可以通过下面的图来理解一下:

    ![Alt text](https://blog.penjee.com/wp-content/uploads/2015/02/pass-by-reference-vs-pass-by-value-animation.gif )<br>

    总的来说，ref的作用就是让视图层随时能知道数据的更改，并且做出相应的渲染，达到响应式的引用。

    - reactive(可监听所有变量)<br>
      上面说到ref是监听简单数据类型，那么reactive就是用来监听引用数据类型，传入一个对象并返回一个基于原对象的响应式代理，即返回一个 Proxy，相当于 Vue2x 版本中的 Vue.observer。但是在以往的Vue2x之中，它只会侦听对象的属性，并不能侦听对象。所以reactive基于 ES2015 Proxy 实现对数据对象的响应式处理，即在 Vue3.0 可以往对象中添加属性，并且这个属性也会具有响应式的效果，同样的我们用个例子来看看如何使用reactive:

      ``` 
      import { reactive，toRefs } from 'vue'
      export default {
      const state = reactive({
        newArr:[1,23],
        newStr:{ a:1, b:2 }
      })
      console.log(state) // { newArr:[1,23],newStr:{ a:1, b:2 } }
      console.log(state.newArr) // [1,23]
      state.newArr.splice(0,1)
      console.log(state.newArr) // [23]
      return{
        ...toRefs(state)
      }
      }

      ```
      但是上述代码之中又出现了一个toRefs,这又是什么呢?让我们来看一下toRefs的源码:
      ```
      function toRefs(object) {
          if ((process.env.NODE_ENV !== 'production') && !isReactive(object)) {
              console.warn(`toRefs() expects a reactive object but received a plain one.`);
          }
          const ret = {};
          for (const key in object) {
              ret[key] = toProxyRef(object, key);
          }
          return ret;
      }
      function toProxyRef(object, key) {
          return {
              _isRef: true,
              get value() {
                  return object[key];
              },
              set value(newVal) {
                  object[key] = newVal;
              }
          };
      }
      ```
      从源码之中可以清楚的看到，toRefs的作用是在原有 Proxy 对象的基础上，返回了一个普通的带有 get 和 set 的对象。这样就解决了 Proxy 对象遇到解构和展开运算符后，失去响应的情况的问题。

      #### 注意: setup函数只会执行一次，因为 ref 和 reactive 可以监听响应式对象，所以每次值变化时，会触发由 template 编译而成的 render 函数的重新执行，这个函数会收集这些值作为依赖，下一次值更新时，函数就会重新执行。

  **2. 生命周期函数**
  
  
- React
    <br>
    react整体上是函数式的思想，组件使用jsx语法，将html与css全都融入javaScript，jsx语法相对来说更加灵活，写react应用的时候会让人感觉就像是在写javaScript。当组件调用setState或props变化的时候，组件内部render会重新渲染，子组件也会随之重新渲染，可以通过shouldComponentUpdate或者PureComponent可以避免不必要的重新渲染，这一点Vue做的要好很多。

### 编译过程
- Vue
  
![Alt text](https://user-gold-cdn.xitu.io/2020/6/26/172eec4c38c54c96?imageView2/0/w/1280/h/960/format/webp/ignore-error/1 )

- React
  
![Alt text](https://user-gold-cdn.xitu.io/2020/6/26/172eeb528af1b162?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
图片来源：[]