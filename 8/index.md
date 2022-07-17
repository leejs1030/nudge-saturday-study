# 영상요약

## top level await
async문 밖에서도 await을 쓸 수 있다.  -> node 16.15.0(2022-04-26 출시)에서 사용 가능함을 확인하였다.


## error cause
에러 메시지와 별개로, 에러의 원인도 작성해줄 수 있다.
```JavaScript
const err = new Error('msg', { cause: 'cause' });

err.message;
err.cause;
```


## .at()
어레이의 임의의 원소에 접근. arr[1]처럼, arr.at(1) 가능.
arr.at(-1)은 arr의 맨 마지막 원소에 접근. -> node 16.15.0(2022-04-26 출시)에서 사용 가능함을 확인하였다.


## 클래스 관련
private 사용 가능 -> node 14.12.0(2020-09-22에서 출시)에서 사용 가능함을 확인하였다.
static 사용 가능 -> node 14.12.0(2020-09-22에서 출시)에서 사용 가능함을 확인하였다.
