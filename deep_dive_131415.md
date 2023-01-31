## 스코프

Javascript는 var, let, const 키워드로 변수를 선언할 수 있습니다.  
키워드들은 스코프 `{}` 에 따라서 다르게 동작하게 됩니다.  

```javascript
var var1 = 1;

if (true) {
	var var2 = 2;

	if (true) {
		var var3 = 3;
	}
}

function foo() {
	var var4 = 4;

	function bar() {
		var var5 = 5;
	}
    console.log(var5) //Error
}
foo()
console.log(var1)
console.log(var2)
console.log(var3)
console.log(var4) // Error
console.log(var5) // Error
```

```javascript

var x = 'hwan'

function foo() {
	var x = 1
	console.log(x)
}

console.log(x)
foo()
console.log(x)
```
Output:
```
hwan
1
hwan
```

이 처럼 스코프에 따라서 같은 이름이라 하더라도 변수는 다르게 인식합니다.  
하지만 var같은 경우에는 같은 스코프 내에서 중복 선언이 가능합니다.  

```javascript

var x = 1
var x = 2

console.log(x) // output: 2

let a = 1;
let a = 2; // SyntextError!!
```

여기서 특이한점이 있는데 javascript, python은 스크립트 형태의 언어여서 스코프의 범위를  
global, class, function으로 한정하고 있습니다. 
이를 Function level scope라고 합니다.  

하지만 Javascript에서 let, const는 Block level scope 라고하며,  
C++, Java, Go 언어도 같은 Block level scope입니다.  
```javascript
if (true) {
	let a = 1;
}

console.log(a) // Error
```

```python
a = 1

if 1:
    b = 2

print(a)
print(b) # Successs
```


```c++
#include <iostream>

using namespace std;

int main()
{
    
    int a = 1;
    
    if(true){
        int b = 2;
    }
    cout << a << endl;
    cout << b << endl; // Error
    return 0;
}
```

```java
public class Hwan{

     public static void main(String []args){
        int a = 1;
        
        if(true) {
            int b = 2;
        }
        System.out.println(a);
        System.out.println(b); // Error
     }
}
```

```go
package main

import "fmt"

func main() {
	var a int = 1

	if true {
		var b int = 2
		fmt.Println(b)
	}
	fmt.Println(a)
	fmt.Println(b) // Error
}
```

## 체인 스코프
javascript에서는 global 스코프, outer 스코프, inner 스코프가 있을 때,  
inner -> outer -> global 스코프로 값을 찾아갑니다.  
이러한 형태를 체인 스코프라고 부릅니다.

```javascript
const x = 1

function foo() {
	console.log('global')
}

function bar() {
	const x = 2;
	function foo() {
		console.log('local')
	}
	console.log(x)
	foo()
}

bar() // output : local 2
console.log(x) // output : 1
```

여기서 bar()내에서 선언된 변수와 함수는 bar스코프 내에서 선언되었기 때문에,  
global로 가지않고 지역에서 찾아서 실행하는 것입니다.  
이러한 실행구조를 렉시컬 환경(Lexical Environment)라고 부릅니다.  
렉시컬 환경은 실행 컨텍스트(Execution context)라는 V8 engine자료구조에 의해 생성되고 관리됩니다.(23장 에서...)   


```javascript
const x = 1

function bar() {
	console.log(x) // undefined
	var x = 2;
	console.log(x) // 2

	console.log(b) // Refference Error
	let b = 3;
}

bar()
console.log(x) // 1

console.log(z) // undefined
var z = 1
```
위와 같이 var로 변수를 나중에 선언해도 스코프 내에서 해당 변수를 찾으면 찾을 수 있습니다.  
실행 컨텍스트에 의해서 찾을 수 있게 되지만, 정의되지 않았기 때문에 undefined로 출력되는 것을 확인할 수 있습니다.  
그 후 변수 할당이 실행되면 정상적으로 출력됩니다. 이를 호이스팅이라고 합니다.  
ES6부터 let과 const가 도입이 되었는데, 이 키워드들은 호이스팅을 지원하지 않습니다.  
정확히는 호이스팅을 지원하지만, 호이스팅이 발생하지 않는 것 처럼 동작합니다.  
때문에 에러를 확인해보면 "undefined Error"가 아니라 "Reference Error"가 나온 것을 확인할 수 있습니다.  

### const
const키워드로 된 변수들은 기본적으로 값을 변경할 수 없습니다.  
```javascript
const x = 1

x = 2 //Error

const obj = {};
obj.name = "hwan";
console.log(obj) // hwan
obj = {} // Error

const num = new Number(123123123)
num = Number(456456) // Error
num = 5656878 // Error
```
Javascript에서의 const는 참조하는 곳을 변경할 순 없지만, 참조의 참조 변수의 값은 변경할 수 있습니다.  

때문에 책에서는 var를 사용하지 말라고 권장하고 있습니다.  

## 변수의 생명 주기
Javascript는 가비지 컬렉터가 있으며, 이걸로 메모리 관리를 하게 됩니다.  
다른 언어들의 가비지 컬렉터와 마찬가지로 변수의 사용 여부를 "참조" 기준으로 하고 있으며,  
아무도 해당 변수를 참조하지 않으면 release시켜 버립니다.  
