## react 基本语法
### 组件内部状态 state

### 单向数据流

基于 es6的语法
1. 组件化
2. 函数定义 使用箭头函数 自动绑定到this 
```
dosomething = (参数) => {
    do...
}
```


### 事件处理

```
正确：
<button
  onClick={() => this.onDismiss(item.objectID)}
  type="button"
>
  点击
</button>
```
因为你必须传递一个参数到类的方法，因此你需要将它封装到另一个（箭头）函数中，基本上，由于要传递给事件处理器使用，因此它必须是一个函数。下面的代码不会工作，因为类方法会在浏览器中打开程序时立即执行。

```
错误：
<button
  onClick={
  this.onDismiss(item.objectID)}
  type="button"
>
  点击
</button>
```
**demo:**
```

filter 函数

class App extends Component {
  ...
    onDismiss = id => {
      const isNotId = item => item.objectID !== id;
      const updatedList = this.state.list.filter(isNotId);
    # leanpub-start-insert
      this.setState({ list: updatedList });
    # leanpub-end-insert
    };

  render() {
    return (
      <div className="App">
        {this.state.list.map(item =>
          <div key={item.objectID}>
            ...
            <span>
# leanpub-start-insert
              <button
                onClick={() => this.onDismiss(item.objectID)}
                type="button"
              >
                Dismiss
              </button>
# leanpub-end-insert
            </span>
          </div>
        )}
      </div>
    );
  }
}

```

### 表达交互

```
event 对象的 target 属性中带有输入框的值，因此你可以使用 this.setState()来更新本地的搜索词的状态了。

includes 函数
  
  class App extends Component {

  ...
    onSearchChange = event => {
    
        this.setState({ searchTerm: event.target.value });
   
      }
      
  render() {
  const isSearched = searchTerm => item =>
  item.title.toLowerCase().includes(searchTerm.toLowerCase());
  
    return (
      <div className="App">
        <form>
          <input
            type="text"
            onChange={this.onSearchChange}
          />
        </form>

        {this.state.list.filter(isSearched(this.state.searchTerm)).map(item =>

          ...
        )}
      </div>
    );
  }
}

```

### 拆分组件
```

class App extends Component {

  ...

  render() {
    const { searchTerm, list } = this.state;
    return (
      <div className="App">

        <Search
          value={searchTerm}
          onChange={this.onSearchChange}
        />
        <Table
          list={list}
          pattern={searchTerm}
          onDismiss={this.onDismiss}
        />

      </div>
    );
  }
}

class Search extends Component {
  render() {
    const { value, onChange } = this.props;
    return (
      <form>
        <input
          type="text"
          value={value}
          onChange={onChange}
        />
      </form>
    );
  }
}

class Table extends Component {
  render() {
    const { list, pattern, onDismiss } = this.props;
    return (
      <div>
        {list.filter(isSearched(pattern)).map(item =>
          <div key={item.objectID}>
            <span>
              <a href={item.url}>{item.title}</a>
            </span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
            <span>
              <button
                onClick={() => onDismiss(item.objectID)}
                type="button"
              >
                Dismiss
              </button>
            </span>
          </div>
        )}
      </div>
    );
  }
}
```

### 可组合组件
在 props 对象中还有一个小小的属性可供使用: children 属性。通过它你可以将元素从上层传递到你的组件中，这些元素对你的组件来说是未知的，但是却为组件相互组合提供了可能性。让我们来看一看，当你只将一个文本（字符串）作为子元素传递到 Search 组件中会怎样

```
class App extends Component {

  ...

  render() {
    const { searchTerm, list } = this.state;
    return (
      <div className="App">
# leanpub-start-insert
        <Search
          value={searchTerm}
          onChange={this.onSearchChange}
        >
          Search
        </Search>
# leanpub-end-insert
        <Table
          list={list}
          pattern={searchTerm}
          onDismiss={this.onDismiss}
        />
      </div>
    );
  }
}

class Search extends Component {
  render() {
# leanpub-start-insert
    const { value, onChange, children } = this.props;
# leanpub-end-insert
    return (
      <form>
# leanpub-start-insert
        {children} <input
# leanpub-end-insert
          type="text"
          value={value}
          onChange={onChange}
        />
      </form>
    );
  }
}

```

### 可复用组件

可复用和可组合组件让你能够思考合理的组件分层，它们是 React 视图层的基础。前面几章提到了可重用性的术语。现在你可以复用 Search 和 Table 组件了。甚至 App 组件都是可复用的了，因为你可以在别的地方重新实例化它。

```
class Button extends Component {
  render() {
    const {
      onClick,
      className,
      children,
    } = this.props;

    return (
      <button
        onClick={onClick}
        className={className}
        type="button"
      >
        {children}
      </button>
    );
  }
}
```