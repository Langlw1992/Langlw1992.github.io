## Jest测试框架初探

> Jest是一个Javascript测试框架，由Facebook开源，致力于简化测试，降低前端测试成本，已被`create-react-app`、`@vue/cli`等脚手架工具默认集成。Jest主打开箱即用、快照功能、独立并行测试以及良好的文档和Api.

#### 安装
```shell
//#初始化一个项目
mkdir jest-test&&cd jest-test
yarn init -y

//#使用 yarn 安装
yarn add -D jest

//#在package.json的scripts中添加
 "scripts": {
    "test": "jest"
  }
```
    
#### 编写测试
```javascript
// 新建一个目标文件 index.js
export function sum(a, b) {
      return a + b;
}
//新建测试文件 index.test.js
import {sum} from './target'

test('1 加 2 等于 3',()=>{
expect(sum(1,2)).toBe(3)
})
```
运行 `yarn test`

    翻车了。。。
    
    ● Test suite failed to run
        Jest encountered an unexpected token
        This usually means that you are trying to import a file which Jest cannot parse, e.g. it's not plain JavaScript.
        ...
        
再去翻文档

> ### Using with ES module imports
> If you're using ES module imports then you'll normally be inclined to put your import statements at the top of the test file. But often you need to instruct Jest to use a mock before modules use it. For this reason, Jest will automatically hoist jest.mock calls to the top of the module (before any imports). To learn more about this and see it in action, see this repo.
> 
Jest运行基于node，而至今`ES Module`仍然是Experimental支持。。。

    #利用babel将代码转译为es5
    yarn add -D babel-jest @babel/core @babel/preset-env
    
    #编辑babel配置文件 .babelrc
    {
      "presets": [
        [
          "@babel/env",
          {
            "targets": {
              "node": "current"
            }
          }
        ]
      ]
    } 
    
再次运行`yarn test`
    
    #通过测试
    PASS  ./index.test.js
    ✓ 1 加 2 等于 3 (3ms)
    
    Test Suites: 1 passed, 1 total
    Tests:       1 passed, 1 total
    Snapshots:   0 total
    Time:        1.128s
    Ran all test suites.
    

#### Matchers 匹配器

Jest拥有众多的[Matchers](https://jestjs.io/docs/en/expect),最为常用的`expect(value)`和`toBe(value)`函数，前者返回用例的的执行结果，匹配后者，参数为执行的期望值，与`toBe`类似的还有`toEqual`。前者基于`Object.is`，为绝对相等，后者则是递归的比较两者对象中字段的value，非严格相等。

#### 异步测试
```javascript
//#测试异步函数
    
// #由于node中不支持 xhr 和fetch API，暂且用 node-fetch 代替

const fetch = require('node-fetch')

test('async function', async () => {
  let res = await fetch('http://localhost:8080/dick')
  let {dick} = await res.json()
  expect(dick).toBe(123)
})

// #也可以在代码块中使用 try catch 捕获异常用于 toThrow Matcher 匹配
```    
#### 测试hooks

Jest提供了`beforeEach`、`afterEach`、`beforeAll`、`afterAll`等钩子，主要用于一些测试之前等状态预设和测试完成后等重置、清理工作.
其中`beforeEach`和`afterEach`会分别在每个单元测试的运行前和运行结束后执行，`beforeAll`和`afterAll`则是在所有单元测试的执行前和执行完成后运行
```javascript
// #分别定义两个测试目标 index.test.js
    function sum(a, b) {
      return a + b
    }
    
    function co(a, b) {
      return a * b
    }
    
    // #hook beforeEach
    beforeEach(() => {
      console.log('beforeEach call')
    })
    
    // #hook afterEach
    afterEach(() => {
      console.log('afterEach call')
    })
    
    // #hook beforeAll
    beforeAll(() => {
      console.log('beforeAll call')
    })
    
    // #hook afterAll
    afterAll(() => {
      console.log('afterAll call')
    })
    
    test('test sum', () => {
      expect(sum(1, 2)).toBe(3)
    })
    
    test('test co', () => {
      expect(co(1, 2)).toBe(2)
    })
```
    
输出测试结果
```shell
PASS  ./index.test.js
      ✓ test sum (5ms)
      ✓ test co (2ms)
    
      console.log index.test.js:18
        beforeAll call
    
      console.log index.test.js:10
        beforeEach call
    
      console.log index.test.js:14
        afterEach call
    
      console.log index.test.js:10
        beforeEach call
    
      console.log index.test.js:14
        afterEach call
    
      console.log index.test.js:22
        afterAll call
```
#### Mock Functions

Jest主要有创建mock函数和重写原来的依赖的模块
mock函数主要提供了三个功能用于测试

- 调用捕获
- 返回值设定
- 改变函数实现

使用`jest.fn`是最简单的实现一个mock函数的方法
```javascript
test("mock test", () => {
 const mock = jest.fn(()=> 'jest.fn test');
 expect(mock()).toBe('jest.fn test'); //函数返回结果
 expect(mock).toHaveBeenCalled(); //函数被调用
 expect(mock).toHaveBeenCalledTimes(1); //调用1次
});

test("mock 返回值", () => {
  const mock = jest.fn();
  mock.mockReturnValue("return value"); //mock 返回值
  expect(mock()).toBe("return value");
});

test("mock promise", () => {
  const mock = jest.fn();
  mock.mockResolvedValue("promise resolve"); // mock promise

  expect(mock("promise")).resolves.toBe("promise resolve");
  expect(mock).toHaveBeenCalledWith("promise"); // 调用参数检验
});

//或者使用赋值的形式 
function add(v1,v2){
  return v1 + v2
}

add = jest.fn()

test("mock dependency", () => {
  add(1,2)
  expect(add).toHaveBeenCalledWith(1,2)
});
```
除了`jest.fn`以为，Jest 还有`jest.mock`、`jest.spyOn`

#### jest.mock
```javascript
//jest.mock mock整个module

//mod.js
export function modTest(v1, v2) {
  return v1 * v2
}

//index.test.js
import {modTest} from './mod'

jest.mock('./mod')

test('jest.mock test', () => {
  modTest(1,2)
  expect(modTest).toHaveBeenCalledWith(1,2)
})
```
    
#### jest.spyOn
```javascript
import * as mod from './mod'
    
    test('jest.spyOn test', () => {
      const modMock = jest.spyOn(mod,'modTest')
      expect(mod.modTest(1, 2)).toBe(2);
    
      // and the spy stores the calls to add
      expect(modMock).toHaveBeenCalledWith(1, 2);
    })
```
    
具体的[jest.spyOn实现](https://github.com/facebook/jest/blob/e9aa321e0587d0990bd2b5ca5065e84a1aecb2fa/packages/jest-mock/src/index.js#L674-L708)



