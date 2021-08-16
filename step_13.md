# Async

## Timers
자바 스크립트에서 가장 기초적이고 기본적인 Async가 있는데, 바로 Timer 이다.<p>
Timer는 `setTimeout()`으로 함수 호출이 가능하고, 해당 Timer가 실행되면 프로그램 코드는 비동기적으로 실행된다.<p>
```javascript
let updateIntervalId = setInterval(checkForUpdates, 60000);
// setInterval() returns a value that we can use to stop the repeated
// invocations by calling clearInterval(). (Similarly, setTimeout()
// returns a value that you can pass to clearTimeout())
function stopCheckingForUpdates() {
    clearInterval(updateIntervalId);
}
```
* setTimeout : 일정시간 후 실행
* setInterval : 일정시간 마다 실행
* clearInterval : setInterval을 정시시킴.

## Events
javasript는 주로 웹에서 사용이 되며, 보편적으로 이벤트 기반이다.<p>
일반적으로 사용자가 무언가를 할 때까지 기다린 다음,<p>
사용자의 행동. 웹 브라우저는 사용자가 이벤트를 생성할 때 (키보드의 키 누르기, 마우스 이동, 마우스 클릭)<p>
이벤트 기반 자바스크립트로 프로그램은 이벤트의 지정된 유형에 대한 콜백 함수를 등록합니다.<p>
이러한 콜백 함수는 이벤트 핸들러 또는 이벤트 리스너라고 하며 `addEventListener()` 로 사용된다.

Fetch API는 네트워크 통신을 포함한 리소스 취득을 위한 인터페이스가 정의되어 있습니다.
fetch()를 불러들이는 경우, 취득할 리소스를 반드시 인수로 지정하지 않으면 안됩니다. 읽어들인 뒤,  fetch()는 Promise객체를 반환합니다.

  promise는 3가지 상태가 존재한다.
  
  Pending(대기) : 비동기 처리 로직이 아직 완료되지 않은 상태
Fulfilled(이행) : 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태
Rejected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태
  
  then을 사용하여 체이닝 식의 코드를 짜는것이 가능하다.
  pending상태를 하여 비동기가 처리를 끝낼때 까지 기다릴 수 있다.
  then을 사용할 때에는 promise객체를 반환해 줘야한다. 이것이 약속이다.

Promise는 인자로 resolve와 reject을 인자로 받는다.
resolve는 완료, reject은 거부를 의미하며, catch문을 사용하여 에러를 처리할 수 있다.
finally는 거부든 완료든 상관 없이 무조건 수행한다.

promise.all은 다른 promise함수들이 여러게 있을 때, 이를 join 상태를 만들 수 있다.

```javascript
Promise.allSettled([Promise.resolve(1), Promise.reject(2),
3]).then(results => {
results[0] // => { status: "fulfilled", value: 1 }
results[1] // => { status: "rejected", reason: 2 }
results[2] // => { status: "fulfilled", value: 3 }
})
```

es2017에서 새로운 async와 await라는 두 가지 키워드를 도입했습니다.
function 앞에 async라는 키워드를 붙인다.
promise로 반환하는 것들 앞에 await을 붙인다.

기존 promise를 사용하면 다음과 같이 해야 했다.

```javascript
// ES6
let num = 0
const f = _ => new Promise(resolve => {
  setTimeout(_ => {
    console.log(`${++num} 번째 실행`)
    resolve()
  }, 500)
})
f().then(f).then(f).then(f).then(f).then(f).then(f).then(f).then(f)
```

async/await.


```javascript
// ES6
let num = 0
const f = _ => new Promise(resolve => {
  setTimeout(_ => {
    console.log(`${++num} 번째 실행`)
    resolve()
  }, 500)
});

(async _ => { 
  await f() 
  console.log('test1')
  await f()
  console.log('test2')
  await f()
  console.log('test3')
})();
```

다른 예시

```javascript
async function showAvatar() {

  // JSON 읽기
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();

  // github 사용자 정보 읽기
  let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
  let githubUser = await githubResponse.json();

  // 아바타 보여주기
  let img = document.createElement('img');
  img.src = githubUser.avatar_url;
  img.className = "promise-avatar-example";
  document.body.append(img);

  // 3초 대기
  await new Promise((resolve, reject) => setTimeout(resolve, 3000));

  img.remove();

  return githubUser;
}

showAvatar();
```

