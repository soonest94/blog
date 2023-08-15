---
title: "[JS] 모듈"
date: 2023-08-15T11:40:44Z
draft: false

tags: [JavaScript, Node.js]
categories: [JavaScript]
---

## 모듈(module)의 개념과 특성
모듈은 여러 기능들에 관한 코드가 모여있는 하나의 파일이다.  
모듈을 사용하면, 하나의 거대한 코드 덩어리를 여러개의 파일로 나눌 수가 있다.  
그로 인해 이하의 특성을 이용할 수 있다.  
- **유지보수성** : 가독성을 높임으로 코드의 유지 및 보수를 용이하게 할 수 있다.  
- **네임스페이스화** : JavaScript는 전역변수를 기초로하므로, 하나의 파일로 관리할 경우 겹치는 네임스페이스가 많아질 수 있다.
하지만 모듈은 모듈만의 네임스페이스를 가지므로, 위 문제를 해결할 수 있다.  

---

## 모듈 시스템
스크립트의 크기가 작을 경우에는 위의 방법으로 충분했으나, 점차 스크립트의 크기가 커지고 기능도 고도화되어감에 따라 자바스크립트 커뮤니티는 다양한 시도를 했다.  
- **ES Module System** : ES6(ES2015)에서 도입된 모듈 시스템이다.
- **CommonJS** : Node.js서버를 위해 만들어진 모듈 시스템이다.
- **AMD** : 가장 오래된 모듈 시스템 중 하나로, require.js라는 라이브러리를 통해 처음 개발되었다.
- **UMD** : AMD와 CommonJS와 같은 다양한 모듈 시스템을 함께 사용하기 위해 만들어졌다.

---

### ES Module System
ES6(ES2015)에서 도입된 모듈 시스템이다.  
html파일에서 JavaScript파일을 모듈로 변경하는 방법은, `<script>`에 `type="module"`속성을 추가하면 된다.  
```html
<script type="module" src="./module/index.js"></script>
```

- **Export**  
함수 및 변수 등을 모듈로서 외부 파일에 export하기위해, 이하 2가지 방법을 사용할 수 있다.  
    - **Default exports**  
        파일에 하나의 default export를 설정할 수 있다.  
        만일 `default_exports.js`라는 파일을 외부 파일에 default export할 경우 아래와 같다.  
        ```js
        const message = () => {
            const name = "Jesse";
            const age = 40;
            return name + ' is ' + age + 'years old.';
        };

        export default message;
        ```
    - **Named exports**  
        Named exports는 해당하는 각각의 행을 export하는 방법과, 파일 최하단에 한번에 export하는 방법이 있다.  
        이때, 각각의 행을 export하는 방법은 최상위 항목만 해당하며, 함수 안에서는 export할 수 없다.  
        만일 `named_exports.js`라는 파일을 외부 파일에 default export할 경우 아래와 같다.  
        ```js
        // 각각의 행을 export하는 방법
        // functions, var, let, const, class등 을 export할 수 있지만, 최상위 항목이어야함.
        export const name = "Jesse";
        export const age = 40;
        ```
        ```js
        // 한번에 export하는 방법
        // export할 기능을 쉼표(,)로 구분하여 중괄호{}로 묶음.
        const name = "Jesse";
        const age = 40;

        export {name, age};
        ```

- **Import**  
상기 방법으로 export한 모듈을 export한 방법에 따라 다음 방법으로 import할 수 있다.  
    ```js
    // default export한 모듈은 하나뿐이므로, 그 자체로 import할 수 있다.
    import message from "./default_exports.js";
    ```
    ```js
    // named export한 모듈은, import할 기능을 쉼표(,)로 구분하여 중괄호{}로 묶어서 import한다.
    import { name, age } from "./named_exports.js";
    ```
위의 예문처럼 찾고자하는 export 파일의 경로를 상대 경로로 나타낼 수 있다.(물론, 절대 경로로도 가능함)  

---

### CommonJS
JavaScript의 태생이 브라우저만을 지원하는 것이었지만, 서버사이드 및 데스크탑 어플리케이션에서도 지원하기 위해서 만들어졌다.  
즉, `node.js`를 위해 만들어진 방식이다.  
다른 모듈을 import할 때는 `require`를, 모듈을 해당 스코프 밖으로 export할 때에는 `module.exports`를 사용한다.  

다음 `export_module.js`를 `import_module.js`에 import 할 경우,  
```js
/** export_module.js */
const printHelloWorld = () => {
  console.log('Hello Wolrd');
};

module.exports = {
  printHelloWorld
};
```
```js
/** import_module.js */
const func = require('./export_module.js'); // 같은 디렉토리에 있다고 가정
func.printHelloWorld();
```
여기서 `module.exports`의 `module`은 현재 모듈에 대한 정보를 갖고 있는 객체이다.  
이는 예약어이며 그 안에 `id`, `path`, `parent`등의 속성이 있고 `exports`객체를 가지고 있다.  

#### `exports`와 `module.exports`
위 예문의 `module.exports`외에도 `exports`를 사용하기도 하는데, 다음과 같은 차이가 있다.  
- `exports`
    > 여러개의 객체를 따로 export할 때 사용
    - `exports`는 `module.exports`를 참조한다.
        ```js
        exports.add = function(a, b) {
            return a + b;
        };

        exports.substract = function(a, b) {
            return a - b;
        };
        ```
- `module.exports`
    > 여러개의 함수를 한번에 넣기에 용이함
    - `module.exports`는 빈 객체를 참조한다.
    - `require`는 항상 `module.exports`를 리턴받는다.
        ```js
        function add(a, b) {
            return a + b;
        }

        function substract(a, b) {
            return a - b;
        }

        module.exports = {
            add: add,
            substract: substract
        };
        ```
그렇다면 왜 2가지를 설정해놓았을까?  
그 이유는 `exports`는 항상 `module.exports`를 참조하기 때문에, `exports`를 사용하면 직접 `module.exports`를 수정하지 않고 객체의 멤버를 만들거나 수정하는 방식으로 사용한다. 따라서, `exports`에 어떤 값을 할당하거나 새로운 객체를 할당했다고 하더라도 결국 `require`는 `module.exports`를 리턴받기 때문에 잠재적인 버그를 피할 수가 있다.

---

### AMD(Asynchronous Module Definition)
비동기 모듈에 대한 표준안을 다룬다.  
CommonJS가 서버쪽에서 장점이 많은 반면 AMD는 브라우저 쪽에서 더 장점이 많다.  
이 방식에서 사용하는 함수는 `define()`과 `require()`이며, AMD스펙을 가장 잘 구현한 모듈로더는 RequireJS 이다.  
```js
/** multiply.js */
define("multiply", [], function() {
//     ^^^^^^^^^^  ^^-- 의존관계
//              \------ 모듈 이름
    return {
        multiply: function(x, y) {
            return x * y;
        },
    };
});
```
```js
/** square.js */
define("square", ["multiply"], function(multiply) {
//     ^^^^^^^^  ^^^^^^^^^^^^           ^^^^^^^^- 로드된 의존관계 (순서대로 넘겨짐)
//            \             \-------------------- 의존관계
//             \--------------------------------- 모듈 이름
    return {
        square: function(x) {
            return multiply.multiply(x, x);
        },
    }
});
```
```js
/** main.js */
define("main", ["square"], function(square) {
    console.log(square.square(42));
    return {};
});
```
첫번째 매개변수(모듈 이름)과 두번째 매개변수(의존관계)는 존재하지않는 경우, 생략할 수 있다.  
AMD는 CommonJS와는 달리, 필요한 의존관계가 무엇인지, 필요한 의존관계가 먼저 읽혀졌는지가 반드시 필요하다.  
```html
<!-- /** index.html */ -->
<!doctype html>
<html>
    <head>
        <script data-main="main.js" src="require.js"></script>
    </head>
</html>
```
위의 html을 실행하면, 브라우저가 `require.js`를 로드하고, RequireJS에 의해 `main.js`가 로드된다.  

---

### UMD(Universal Module Definition)
모듈 구현방식이 CommonJS와 AMD로 나뉘기 때문에 그걸 통합하기 위한 하나의 패턴으로, 스스로 모듈의 종류를 판별하는 라이브러리를 패턴화한것이다.  
상세한 정보는 [UMD 깃허브](https://github.com/umdjs/umd)참조.    

---
---

### Reference
[Mozilla, MDN Web Docs - JavaScript modules (View 23.08.15)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)  
[W3Schools, JavaScript Modules (View 23.08.15)](https://www.w3schools.com/js/js_modules.asp)  
[JAVASCRIPT.INFO, Modules, introduction (22.10.14)](https://javascript.info/modules-intro)  
[즐거운인생 (미련없이 하자), [javascript] module 시스템 (22. 4. 29)](https://doitnow-man.tistory.com/entry/javascript-module-%EC%8B%9C%EC%8A%A4%ED%85%9C)  
[배하람, [JS] 모듈에 대한 이해와 사용법 (20.01.10)](https://baeharam.netlify.app/posts/javascript/module)  
[팔만코딩경, [JavaScript] 모듈이란? (22.02.18)](https://80000coding.oopy.io/a9e13d5e-e858-40f5-a80f-dbb50d0b65f4)  
[@qnighy, JSエコシステムぶらり探訪(6): AMDとモジュールローダー (20.11.15)](https://qiita.com/qnighy/items/0c3fd208e0356fa19cda)  
