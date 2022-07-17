# 영상요약

## top level await
async문 밖에서도 await을 쓸 수 있다.  -> node 16.15.0(2022-04-26 출시)에서 사용 가능함을 확인하였다.
![image](https://user-images.githubusercontent.com/102134003/179396250-f228fdcd-bd89-4ce9-9372-86bcf2133200.png)



## error cause
에러 메시지와 별개로, 에러의 원인도 작성해줄 수 있다.
[error cause에 대한 설명](https://github.com/tc39/proposal-error-cause)
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
![image](https://user-images.githubusercontent.com/102134003/179395563-05364a59-591b-4e49-92bd-1710cdd6a1eb.png)

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
조사 방법은 우선 로컬에서 node를 돌리며 실험해보았고, 필요한 경우 [node green](https://node.green/) 사이트도 이용하였다.
<br><br><br>
private/static과 관하여, 2022-06-01 출시인 17.9.1에서 새로 추가된 내용이 있기는 하였다.
바로 https://github.com/tc39/proposal-class-static-block 의 내용이다.
정적 초기화 블럭, static initialization block 등으로 검색해보면 다른 언어의 구현 사례도 찾아볼 수 있다.
아래는 그에 대한 문법과 간략한 예시이다.
```JavaScript
// Syntax - 위의 깃허브로부터 가져옴
class C {
  static {
    // statements
  }
}
```

```JavaScript
// "friend" access (same module)
let A, B;
{
  let friendA;

  A = class A {
    #x;

    static {
        friendA = {
          getX(obj) { return obj.#x },
          setX(obj, value) { obj.#x = value }
        };
    }
  };

  B = class B {
    constructor(a) {
      const x = friendA.getX(a); // ok
      friendA.setX(a, x); // ok
    }
  };
}
```
<br><br><br>
top-level await의 경우, node-green에서는 찾아볼 수 없었다. 다만 [v8의 개발 블로그](https://v8.dev/features/top-level-await)에 따르면, 한 때는 node.js 등의 환경에서 있었다가 퇴출되었다.
<br><br><br>
error cause는, 2022-06-01 출시인 17.9.1에서 최초 적용이라고 node-green 상에 나와 있다. (정확히는 이전 버전부터 포함은 되어 있으나, 완전히 구현이 된 시점은 그 이후인 17.9.1 정도로 보인다)
