# 영상요약

## top level await
async문 밖에서도 await을 쓸 수 있다.  -> node 16.15.0(2022-04-26 출시)에서 사용 가능함을 확인하였다.


## error cause
에러 메시지와 별개로, 에러의 원인도 작성해줄 수 있다.
```JavaScript
const err = new Error('msg', { cause: 'cause' });

err.message; // msg
err.cause; // cause
```


## .at()
어레이의 임의의 원소에 접근. arr[1]처럼, arr.at(1) 가능.
arr.at(-1)은 arr의 맨 마지막 원소에 접근. -> node 16.15.0(2022-04-26 출시)에서 사용 가능함을 확인하였다.
```JavaScript
const arr = [6, 2, 8, 5, 3];

arr[0] // 6
arr.at(0) // 6

arr[4] // 3
arr.at(-1) // 3
```


## 클래스 관련
private 사용 가능 -> node 14.12.0(2020-09-22 출시)에서 사용 가능함을 확인하였다.   
static 사용 가능 -> node 14.12.0(2020-09-22 출시)에서 사용 가능함을 확인하였다.
```JavaScript
class A{
   #privateFoo() {return 1; }
   static staticFoo() { return 1; }
   foo() { return 1; }
 }
```


## 2022-06?
2022-06 신규 자바스크립트 기능이라고 했지만, #을 붙이는 방식의 private를 이미 예전에 본 기억이 있어 추가로 찾아 보았다.
실제로 이미 구현이 되었던 기능이며, 이에 미심쩍어서 추가로 다른 것들조 조사를 해 보았고, at도 이미 구현이 되어 있었다.
조사에는 nvm을 사용해서 직접 찾아본 것과, [node green](https://node.green/) 사이트를 이용한 것이 있다.
<br>
private/static과 관련해, 2022-06-01 출시인 17.9.1에서 새로 추가된 내용이 있기는 하였다. 바로 https://github.com/tc39/proposal-class-static-block 의 내용이다.
