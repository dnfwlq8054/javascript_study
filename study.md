<h1> JavaScript 탄생</h1>

첫 탄생은 브랜든 아이크가 10일만에 설계한 것으로부터 시작한다. 

처음에는 Mocha라는 이름이었지만 4달 만에 LiveScript라는 이름으로 개명하고 다시 3달 후에는 JavaScript라는 이름이 되어 오늘날까지 이어지고 있다. 

JavaScript라고 짛은 이유는 Java의 유명세를 타서 묻어가려고 의도적으로 만들었다고 한다. ㅋ

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

이중에 참조값인 funcion과 object는 객체로 표현된다.(JS의 특성이다.)

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
