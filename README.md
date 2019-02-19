# web开发中的学习，实践和思考

在这里记录并分享我的在web开发中的学习，实践和思考。

React现在已经是大家都想要学习的框架，关于React的高质量文章也挺多的，在这里分享一下我的学习笔记，写得不对的地方请指正。

React只是View层，如果要做大型的项目，会搭配其他js库，普遍的选择是
- Redux： 统一管理数据，将所有的数据放到store这个对象里
- React-Redux： 数据绑定，将Redux store里的数据绑定到React 组件中
- react-router: 路由

React的社区很繁荣，有许多优质的组件，可以满足项目里的各种需要，比如react-helmet, react-modal等等，更多精选组件推荐请见[antDesign](https://ant.design/docs/react/recommendation-cn)


React使用jsx创建组件，jsx语法并不神秘，感觉和写普通的html标签差不多，但本质是javascript的扩展语法， 需要babel-preset-react编译，babel-preset-react会将jsx语法编译成为React.createElement(component, props, …children)，
你用jsx语法写的都可以用React.createElement()这个API来写，但是不直观，写起来也不太方便。

比如jsx的方式是:
```javascript
const element = <h1>Hello, world!</h1>;

```
Babel的babel-preset-react编译后的js就是:

```javascript
const element = React.createElement(
  'h1',
  null,
  'Hello, world!'
);
```
啰嗦一句，写react是需要安装并配置Babel的，因为实际项目是用React组件开发的，组件打包相关的还需要webpack，可以自己一步步配置，也可以使用facebook的**create-react-app**，按照**create-react-app**的官方文档的步骤运行，就可以忽略繁琐的工程配置，直接上手react 开发。

如果我们写了一个组件A，然后通过ReactDOM.render(<A …props />， domElement)方法开始渲染。<A …props />通过Babel编译后实际上一个element 对象，React通过ReactDOM.render这个入口函数拿到你写的组件A的相关信息即一个element对象，开始了渲染的神奇之旅。

React组件的类型基本分为**function component**和**class component**2种，两者的不同就是function component是没有state和生命周期函数，同时这个function component也不会有对应的instance。class component有state， 有组件生命周期函数，在react的渲染机制中会有对应的instance，这个instance就是根据我们写的class component new的对象。

在写组件的时候，生命周期函数是很重要的，一般会将业务逻辑放在生命周期函数里，生命周期函数分成mounting， updating,  unmounting阶段，现在的react是V16+的版本，和之前的V15+的版本在生命周期函数上是有些不同的，在写组件生命周期函数的时候要注意react版本。

现在的v16.3+版本的组件生命周期函数有：

* **mounting阶段有**

  constructor：
  在构造函数中一般初始化state, 绑定方法。在这里不要调用setState，也不要做任何引起副作用的工作。

  static getDerivedStateFromProps, 


  render: render函数要保持纯净。


  componentDidMount：
  当一个组件mounting后，这个函数会被调用，涉及到dom的初始化工作，从服务器获取数据，以及其它数据订阅都可以放在这里，
  如果在这里做了任何数据订阅，在componentWillUnmount中都要解除订阅。

  如果在这里又调用setState的话，会触发新的渲染，比如你需要根据dom node的位置和大小来渲染提示框，
  但是这个渲染会发生在浏览器更新屏幕前，这保证了即使这种情况调用了2次render, 一次在componentDidMount()前，一次在setState()后，用户也不会看到中间的状   态。


* **updating阶段有**：

  static getDerivedStateFromProps(props, state):

  在render前被调用，应该返回一个对象来更新state， 或者null表示不更新。这个函数用得比较少，并且react建议用其它方式替代。
  并且这个函数和旧版本的componentWillReceiveProps(nextProps)只会在父组件引起的re-render被调用不同，getDerivedStateFromProps(props, state)在本地   setstate引起的re-render中也会被调用。

  shouldComponentUpdate(nextProps, nextState)：
  返回false的话之后的updating阶段的生命周期函数都不会执行。

  render

  getSnapshotBeforeUpdate(prevProps, prevState)：
  在更新的结果提交到dom前调用，让你的组件可以在dom更新前获取dom的一些信息，这个方法返回的任何值都会作为componentDidUpdate的第3个参数传入。


  componentDidUpdate(prevProps,  prevState，[return from getSnapshotBeforeUpdate]):
  在更新发生后立即被调用。在这个函数里,可以当组件更新的时候去操作dom，或者根据prevProps和当前的props的不同去请求数据。
 在这个函数里调用setState后会引起无穷的循环，会引起新的的渲染，虽然用户看不到，但是会对组件性能产生影响，不要调用setState()

* **unmounting阶段**：
    componentWillUnmount, 在这里不要再setState()

更详细的组件生命周期函数请参考官方文档[React Component](https://reactjs.org/docs/react-component.html)

react上手相对简单，但要发挥出它的特点和威力需要更深入地学习。
