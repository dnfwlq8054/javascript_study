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

