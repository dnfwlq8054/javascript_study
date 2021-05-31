<h1> JavaScript 탄생</h1>

첫 탄생은 브랜든 아이크가 10일만에 설계한 것으로부터 시작한다. 

처음에는 Mocha라는 이름이었지만 4달 만에 LiveScript라는 이름으로 개명하고 다시 3달 후에는 JavaScript라는 이름이 되어 오늘날까지 이어지고 있다. 

JavaScript라고 지은 이유는 Java의 유명세를 타서 묻어가려고 의도적으로 만들었다고 한다. ㅋ

<h1> ECMAScript </h1>
ECMA International 이라는 국제 표준화 기구가 있다.

표준화 시킨 대표적인 것들은 JSON, C#언어 규격, C++/CLI언어 규격, CD-ROM 볼륨 규격.(이 정도만 알아보자...)

ECMA에서 만든 표준화중에는 ECMA-262라는 것이 있다. 해당 규격을 JavaScript가 사용한다.

따라서 해당 규격에 맞춰 매년 ES6 ~ ES12까지 나오고 있으며, 나올때마다 조금씩 문법이나 새로운 자료형들이 추가되었다.

<h1>JavaScript Engine</h1>
ECMA-262규격에 맞춰 문법들이 다양해진다.

하지만 개발자들은 ES6문법을 사용하거나 ES9문법을 사용하거나, 래거시코드(var)를 사용하기도 한다.

그렇다면 이렇게 각기다른 문법으로 쓰여진 JavaScript는 어떻게 컴파일되는걸까??

의 의문점에서 출발한다.

JavaScript는 웹 언어이다. 때문에 웹브라우저에서 동작하게된다.

다시말해 웹브라우저에 컴파일러가 있고 이를 엔진이라고 표현한다.

크롬에서는 Google이 만드 V8 Engine이 있고, MS Edge에는 Chakra라는 엔진이 있다.

그 외 웹브라우저마다 사용하는 엔진이 각기 다르다.

해당 엔진들이 문법을 해석해 하나의 바이트코드로 만들어 컴파일한다고 한다. (이에 대해선 나중에 좀 더 자세히 알아보자)

<h1>JavaScript Value</h1>
자바스크립트의 기본 자료형들은 다음과 같이 있다.

원시 값 : number, string, boolean, null, undefined

참조 값 : array, function, object

문자열을 나타내는 방법은 총 3가지가 있다. 

", ', \` (큰따옴표, 작은 따옴표, 백틱)

JS에서 권장하는 바는 문자열을 표현할 때는 '와 \` 사용을 권장한다.(HTML 문법과 맞추기 위한??)

<h1>JavaScript Compare</h1>
자바스크립트는 비교하는 방식이 2가지가 있다.

== 과 === 이다.

==는 기본적으로 "값" 만을 비교한다.

ex)1 == "1" (true), 0 == 0n (true)

===는 "값" + "자료형" 두가지를 비교하며 엄격한 비교라고 한다.

ex)1 === "1" (false), 0 === 0n (false)

<h1>JavaScript Simbol</h1>
Simbol이란 개념은 독립적인 Key라고 보면 된다.

```
let key1 = Simbol("a");
let key2 = Simbol("a");
key1 == key2 (false) 
```

즉, 서로 영향을 주지 않는다. 이런 Simbol을 공유할 수 있다.

```
let key1 = Simbol.for("a");
let key2 = Simbol.for("a");
key1 == key2 (true) 
```

이렇게 말이다.

<h1> 옵셔널 체이닝 </h1>
옵셔널 체이닝을 사용하면 객체에 안전하게 접근할 수 있다.

사용방식은 다음과 같다.

```
console.log(user?.address);

```

세션을 맺거나 웹통신을 할 때 클라이언트 측에서 접속이 끊기거나 해당 객체 리소스가 비어있을 경우,
위와 같은 방식으로 작성하지않고,

```
console.log(user.address);  //error!!

```

위 예시대로 작성하면 애러를 발생시켰다.

때문에 새롭게 나온것이 옵셔널 체이닝!!

<code>.?</code>를 사용함으로서 해당객체에 안전하게 접근해, 애러를 발생시키지않고 <code>undefined</code>를 호출하게 해준다.

때문에 비어있는 프로퍼티가 있더라도 계속 진행할 수 있다.

다른 예제를 보자.

```
let f = null
let x = 0;
f(x++)  //error

let f = null
let x = 0;
f?.(x++)  //undefined

```




<h1> Object </h1>
객체를 생성할때는 <code> new </code> 키워드를 사용해 호출한다.

```

let oj = new Object();
let oj2 = new Date();

```

객체안에 배열이 들어갈 수 있고, 배열안에 객체가 들어갈 수 있다.

```
let a = [1, 4, [5, 6]]; 
let b = {a, y: 3};
b.a[0]  //output : 1

let a = {x: 1, y: 2}
let b = [a, 1, [2, 3]];
b[0].x  //output : 1
b[2][1] //output : 2
b[2]    //output : (2) [2, 3]

```

<h1> Operator Side Effects (부작용) </h1>
자바스크립트는 자료형이 없기 때문에 문자열 계산 과정에서 예기치 못한 상황이 벌어질 수 있다.

때문에 문자열의 계산은 대부분 제공해주는 내장 함수로 해결해보자!!

간단한 예를 들보자.

```
let v = "5" + "5"   //output : 55
let a = "5" * "5"   //output : 25
let c = "5" + 5     //output : 55
let z = "5";
z++
console.log(z)      //output : 6

```

위 예제에서 보듯이 문자열 연산은 상당히 위험하니 되도록 지양하다록 하자.

<h1> typeof </h1>
typeof는 해당 변수의 자료형을 반환해준다.

```
let x = 1
typeof(x)      //output : number

let v = "1"   
typeof(v)      //output : string

let n = null
typeof(n)      //output : object

let un = undefined
typeof(un)     //output : undefined

let bool = true
typeof(bool)   //output : boolean

let sym = Sysbol()
typeof(sym)    //output : symbol

let arr = [1, 2, 3, 4]
typeof(arr)    //output : object

let obj = {x: 1, y: 2}
typeof(obj)    //output : object

function f(){}
typeof(f)      //output : function

```
여기서 주목해야할 점은 배열과 null이다.

배열과 null은 object로 동작하게 된다.

<h1> for in, for of </h1>
javascript에는 2가지 for문이 존재한다.

of와 in.....

for of 반복문은 ES6에 추가된 새로운 컬렉션 전용 반복 구문이다.

for of에서 사용할 수 있는 것은 <code>Symbol.iterator</code> 객체 이다.

때문에 for of에서 반환되는 값들은 배열의 value 값들이다.

for in 반복문은 모든 객체들이 사용할 수 있는 for loop 이다.

for in은 value가 아닌 key값에 접근하게 된다. 즉, 배열기준 index로 접근을 하게 된다.

때문에 for in을 돌리게되면 key 뿐만 아니라 다른 부가적인 요소들을 포함한 모든 것들이 출력되는것을 확인할 수 있다.

<h1> try / catch / finally /throw </h1>
javascript에도 해당 오류 검출 구문이 존재한다.

JAVA와 똑같다. try는 실행, catch는 try실행시 나는 에러, finally는 실행이 보장되는 구문.

throw는 `throw new Error("String")` 으로 만들 수 있다.

Error클래스를 사용한다고 한다.

<h1> yield </h1>
yield는 Javascript에서 비동기식으로 데이터를 처리할 때 사용한다고 한다. 

iteartor를 구현하고 사용할 때 편하다는 장점이 있다. ex) `gen.next()`

이것 또한 JAVA의 iterator와 유사하다.

사용 방법은 다음과 같다.

```

function *call() {
  yield 10;
  yield 20;
  yield 30;
}

for(let value of call()){
    console.log(value);
}

//output : 10, 20 ,30

```
이렇게 쓰일 수 있다. 하지만 이것 외에도 Jvavscript는 async / await이 잘 구현되어 있다고 하니 굳이??

사용할 일은 많지는 않을 것 같다.

<h1> with </h1>
쓰지마!!

<h1> use strict </h1>
엄격모드라고 한다.

코드를 보다 안정성 있게 만들어 준다. (선언하는게 좋아보인다.)

사용법은 다음과 같다.

`"use strict"`

이걸 사용했을 때 효과는 다음과 같다.

<li> with을 허용하지 않는다. </li>
<li> 선언하지 않고 전역변수를 만들 수 없다. </li>
<li> 읽기 전용 객체는 쓰기가 불가능 하다.(writable이 false인 경우)</li>
<li> get 으로 선언된 객체는 수정 불가능하다. </li>
<li> Object.preventExtensions(testObj); 수정이 불가능 하다. </li>
<li> delete를 호출할 수 없다. </li>
<li> 동일한 매개 변수를 선언하는 함수를 2개 이상 만들 수 없다. </li>
<li> primitive values의 속성 설정이 불가능. </li>
<li> eval의 새로운 변수를 스코프에 추가하지 않는다. </li>
<li> arguments 객체가 생성한 property를 alias하지 않음 </li>
<li> arguments.callee 지원하지 않음 </li>

엄격 모드는 함수별로, 부분별로 선언할 수 있으며 비 엄격모드와 공존이 가능하다.

코드 전체를 엄격 모드로 바꾸게 된다면, 기존에 있던 레거시 코드들이 작동 안할 수 있으니 주의하자!!

<h1> export </h1>
export는 Javascript에서 모듈 밖으로 값을 내보낼때 사용하는 것이다.

반대로 export한 것을 받을 때는 import를 사용하면 된다.

```
//배열 내보내기
export let months = ['Jan', 'Feb', 'Mar','Apr', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];

// 상수 내보내기
export const MODULES_BECAME_STANDARD_YEAR = 2015;

// 클래스 내보내기
export class User {
  constructor(name) {
    this.name = name;
  }
}
```

함수도 내보낼 수 있다.
```
//say.js 파일

function sayHi(user) {
  alert(`Hello, ${user}!`);
}

function sayBye(user) {
  alert(`Bye, ${user}!`);
}

export {sayHi, sayBye}; // 두 함수를 내보냄
```

import하려면 다음과 같이 하면 된다.
```
// 📁 main.js
import {sayHi, sayBye} from './say.js';

sayHi('John'); // Hello, John!
sayBye('John'); // Bye, John!

```

가져올 것이 많으면 import * as <obj> 처럼 객체 형태로 원하는 것들을 가지고 올 수 있습니다. 예시를 살펴보겠습니다.
  
```
  
// 📁 main.js
import * as say from './say.js';

say.sayHi('John');
say.sayBye('John');
  
```
  
<h1> Objcet </h1>
Object의 접근 방법은 2가지가 있다.
  
.(dat)과 [](대괄호) 이다.

이 둘의 차이는 보이는 그대로 구조체의 this형태로 접근할 것이냐, 인덱스 형태로 접근할 것이냐의 차이이다.

이 차이에서 문자열 인덱스 접근시(띄어쓰기 포함) 대괄호가 좀 더 유연하게 동작할 수 있다.

.과 [] 둘다 속성(일종의 property들)을 추가하거나 변경이 가능하다.

해당 값이 없으면 추가하고, 있으면 수정한다.

delete를 통해 속성을 제거할 수 있지만 굳이?? 라는 생각이 든다.

  <h1> v8 engine memory </h1>
  v8 engine은 stack과 heap으로 메모리를 관리한다.
  
  stack에는 전역 스코프가 들어가며, 객체 생성, 함수 호출시 스택에 적재 후 heap으로 데이터를 전달, 그 후 스택에서 pop된다.
  
  스택에는 frame이라는 것으로 감싸서 관리하게 된다. frame은 전역 스코프를 넣는 global frame이 제일 밑에 깔리며, 함수, 객체 생성시 해당 객체애 대한 frame이 생성된다.
  
  물론 pop되는 순간 모두 사라지지만 말이다....
  
  그외 async / await을 사용하기 위해 web api를 사용한다.
  
  자세한 내용은 여기를 참고하자.
  
  https://deepu.tech/memory-management-in-v8/
  
  
  <h1> this </h1>
  javascript에서의 this는 일반적인 java에서 사용된는 this와 차이점이 있다.

총 4가지의 방법을 제시한다.

1. this자체는 실행 환경의 전역 객체가 된다.
chrome 기준 객체는 window가 된다.

2. this 객체를 통해 등록한 변수들은 지역 스코프에서 정의해도 전역 스코프에서 선언한 것과 같은 결과가 된다.

3. 어떤 객체를 통해 함수가 호출되면 this는 호출한 객체가 된다.
```
function test() {
  console.log(this.a);
}

var obj = {
  a: 20,
  func1: test,
  func2: function() {
    console.log(this.a);
  }
};

obj.func1(); // 20
obj.func2(); // 20
```

그럼 다음 예제를 보자.

  ```
var b = 100;

function test() {
  console.log(this.b);
}

var obj = {
  a: 20,
  func1: test,
  func2: function() {
    console.log(this.b);
  }
};

obj.func1(); // undefined
obj.func2(); // undefined

var gFunc1 = obj.func1;
gFunc1(); // 100
```
  
obj 까지는 이해가 될 것이다.
문제는 그 다음인데, obj객체를 gFunc1로 전달해주고 있다.
this.b는 b를 가르키므로 100이 된다.
즉, 실행하는 객체의 위치에 따라 this가 참조하는 객체는 달라진다.

4.명시적 바인딩
함수 뒤에 .call() 함수를 실행하여 this 를 명시적으로 지정해 줄 수 있다.

5. new 바인딩

let a = new fnc(10)
이런식으로 new를 통해 객체를 반환해주면 this는 고정된다.
여기서 고정의 의미는 fnc에 등록된 매개변수를 기준으로 고정된다는 뜻이다.


  <h1> promise </h1>
  promise는 3가지 상태가 존재한다.
  
  Pending(대기) : 비동기 처리 로직이 아직 완료되지 않은 상태
Fulfilled(이행) : 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태
Rejected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태
  
  then을 사용하여 체이닝 식의 코드를 짜는것이 가능하다.
  또한 비동기시 다른 언어의 join인과 비슷하게 pending상태를 하여 비동기가 처리를 끝낼때 까지 기다릴 수 있다.
  
  <h1> Array </h1>
  JavaScript에서 배열은 0비트 ~ 32비트까지 사용할 수 있다고 한다.
  
  즉, 4294967295개의 원소를 가질 수 있는 배열을 만들 수 있다.(이것이 최대이다.)
  
  따라서 배열의 최대 인덱스는 4294967294 입니다.
  
  let arr = [1, true, "a"] 를 입력한다면, 각 자료형이 들어갈 수 있다.
  
  따라서 배열은 모든 타입을 담을 수 있다.
  
  스프레드(...)를 사용하면 모든 각 요소들이 들어간다.
  
  ex)
  
  ```
  let a = [1, 2, 3]
  let b = [1, ...a, 5] // 1, 1, 2, 3, 5
  let c = [1, a, 5] // 1, Array(3), 5 
  ```
또한 문자열을 담았을 때, 스프레드를 사용하면 문자로 분리시킬 수 있다.
  ```
let digits = [..."0123456789ABCDEF"];
digits // ["0","1","2","3","4","5","6","7","8","9","A","B","C","D","E", "F"]
```
  배열 생성자를 통해 범위를 설정해 줄 수 있지만 별로 의미는 없는 것 같다.
```
let q = new Array(10) //길이 = 10
q[14] = 1
console.log(q)  //길이 = 14

let a = new Array(1, 2, 3, 4) // 길이 4(초기화)
  ```
  JavaScript의 배열의 인덱스를 숫자가 아닌 문자열이나 그 외의 값으로 지정할 수 있다. (그렇게되면 객체를 넣는 것과 같은것이 된다.)
  ```
let z = []
z["name"] = 1
console.log(z[0]) //undefined
console.log(z.name) // 1
console.log(z["name"]) // 1
  ```

  배열에 length로 길이를 반환할수도, 길이를 설정할 수도 있다.
  ```
  let a = [1, 2, 3] // a.length = 3
  a.length = 0 // a.length = 0, 모든 요소  delete
  a.length = 1000 // a.length = 1000
  ```
  
  delete는 속성의 연결만 끊을 뿐 힙 메모리에서 데이터를 직접적으로 삭제해주진 않는다.
  
  따라서 delete보단 undefined나 null 값을 넣어주는 것이 좋다.
  
  배열 길이자체를 줄이고 싶다면, pop, shift, filter, reduce 등을 사용하자.
  
  string은 배열 인덱스 형식으로 접근이 가능하지만, 읽기전용이라서 수정이 불가능 하다.
  
  
