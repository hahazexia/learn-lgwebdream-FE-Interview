# react

1. React 项目中有哪些细节可以优化？实际开发中都做过哪些性能优化？[链接](https://github.com/lgwebdream/FE-Interview/issues/12)

<details>
<summary>答案</summary>

对于正常的项目优化，一般都涉及到几个方面，开发过程中、上线之后的首屏、运行过程的状态 <br>

* 来聊聊上线之后的首屏及运行状态：

* 首屏优化一般涉及到几个指标FP、FCP、FMP；要有一个良好的体验是尽可能的把FCP提前，需要做一些工程化的处理，去优化资源的加载方式及分包策略，资源的减少是最有效的加快首屏打开的方式；

* 对于CSR的应用，FCP的过程一般是首先加载js与css资源，js在本地执行完成，然后加载数据回来，做内容初始化渲染，这中间就有几次的网络反复请求的过程；所以CSR可以考虑使用骨架屏及预渲染（部分结构预渲染）、suspence与lazy做懒加载动态组件的方式。

* 当然还有另外一种方式就是SSR的方式，SSR对于首屏的优化有一定的优势，但是这种瓶颈一般在Node服务端的处理，建议使用stream流的方式来处理，对于体验与node端的内存管理等，都有优势；

* 不管对于CSR或者SSR，都建议配合使用Service worker，来控制资源的调配及骨架屏秒开的体验

* react项目上线之后，首先需要保障的是可用性，所以可以通过React.Profiler分析组件的渲染次数及耗时的一些任务，但是Profile记录的是commit阶段的数据，所以对于react的调和阶段就需要结合performance API一起分析；

* 由于React是父级props改变之后，所有与props不相关子组件在没有添加条件控制的情况之下，也会触发render渲染，这是没有必要的，可以结合React的PureComponent以及React.memo等做浅比较处理，这中间有涉及到不可变数据的处理，当然也可以结合使用ShouldComponentUpdate做深比较处理；

* 所有的运行状态优化，都是减少不必要的render，React.useMemo与React.useCallback也是可以做很多优化的地方；

* 在很多应用中，都会涉及到使用redux以及使用context，这两个都可能造成许多不必要的render，所以在使用的时候，也需要谨慎的处理一些数据；

* 最后就是保证整个应用的可用性，为组件创建错误边界，可以使用componentDidCatch来处理；


* 实际项目中开发过程中还有很多其他的优化点：

1. 保证数据的不可变性
2. 使用唯一的键值迭代
3. 使用web worker做密集型的任务处理
4. 不在render中处理数据
5. 不必要的标签，使用React.Fragments

</details>
<br><br>

2. react 最新版本解决了什么问题？加了哪些东西？[链接](https://github.com/lgwebdream/FE-Interview/issues/13)

<details>
<summary>答案</summary>

1. React 16.x的三大新特性 Time Slicing, Suspense，hooks

* Time Slicing（解决CPU速度问题）使得在执行任务的期间可以随时暂停，跑去干别的事情，这个特性使得react能在性能极其差的机器跑时，仍然保持有良好的性能
* Suspense （解决网络IO问题）和lazy配合，实现异步加载组件。 能暂停当前组件的渲染, 当完成某件事以后再继续渲染，解决从react出生到现在都存在的「异步副作用」的问题，而且解决得非常的优雅，使用的是「异步但是同步的写法」，我个人认为，这是最好的解决异步问题的方式
*  此外，还提供了一个内置函数 componentDidCatch，当有错误发生时, 我们可以友好地展示 fallback 组件；可以捕捉到它的子元素（包括嵌套子元素）抛出的异常；可以复用错误组件。

2. React16.8

* 加入hooks，让React函数式组件更加灵活

hooks之前，React存在很多问题

在组件间复用状态逻辑很难<br>
复杂组件变得难以理解，高阶组件和函数组件的嵌套过深。<br>
class组件的this指向问题<br>
难以记忆的生命周期<br>

* hooks很好的解决了上述问题，hooks提供了很多方法

useState 返回有状态值，以及更新这个状态值的函数<br>
useEffect 接受包含命令式，可能有副作用代码的函数。<br>
useContext 接受上下文对象（从React.createContext返回的值）并返回当前上下文值，<br>
useReducer useState的替代方案。接受类型为(state，action) => newState的reducer，并返回与dispatch方法配对的当前状态。<br>
useCallback 返回一个回忆的memoized版本，该版本仅在其中一个输入发生更改时才会更改。纯函数的输入输出确定性<br>
useMemo 纯的一个记忆函数<br>
useRef 返回一个可变的ref对象，其.current属性被初始化为传递的参数，返回的 ref 对象在组件的整个生命周期内保持不变。<br>
useImperativeMethods 自定义使用ref时公开给父组件的实例值<br>
useMutationEffect 更新兄弟组件之前，它在React执行其DOM改变的同一阶段同步触发<br>
useLayoutEffect DOM改变后同步触发。使用它来从DOM读取布局并同步重新渲染<br>

3. React16.9

* 重命名 Unsafe 的生命周期方法。新的 UNSAFE_ 前缀将有助于在代码 review 和 debug 期间，使这些有问题的字样更突出
* 废弃 javascript: 形式的 URL。以 javascript: 开头的 URL 非常容易遭受攻击，造成安全漏洞。
* 废弃 “Factory” 组件。 工厂组件会导致 React 变大且变慢。
* act() 也支持异步函数，并且你可以在调用它时使用 await。
* 使用 `<React.Profiler>` 进行性能评估。 在较大的应用中追踪性能回归可能会很方便

4. React16.13.0

* 支持在渲染期间调用setState，但仅适用于同一组件
* 可检测冲突的样式规则并记录警告
* 废弃unstable_createPortal，使用createPortal
* 将组件堆栈添加到其开发警告中，使开发人员能够隔离bug并调试其程序，这可以清楚地说明问题所在，并更快地定位和修复错误。

</details>
<br><br>