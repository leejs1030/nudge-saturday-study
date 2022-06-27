# 물리적 저장 장치 시스템

## RAID
디스크 드라이브의 용량은 빠르게 증가하나, 데이터 저장 요구량도 매우 빠르게 증가하고 있다.
따라서 데이터를 저장하는 데에 많은 수의 디스크가 필요하다.



많은 디스크를 갖고, 병렬로 운영한다면 데이터의 읽기/쓰기 속도를 향상시킬 수 있다.
독립적 읽기/쓰기도 병렬로 수행할 수 있다.
중복된 정보를 여러 곳에 저장하면 신뢰성을 향상시킬 수 있다.



디스크의 성능과 신뢰성 향상을 위해, 통칭하여 RAID라 불리는 다양한 기법이 제안되었다.
RAID: Redundant Arrays of Independent Disk



이러한 장점들로, RAID를 사용한다. 경제적인 이유보다는 높은 신뢰성과 성능때문이다. 또한 관리/작업도 쉬워진다.



RAID는 0\~6까지 7개의 수준이 있다.
하지만 2\~4는 실제로 더 사용하지 않기에, 교재에서는 다루지 않는다고 하여, 별도 조사를 통해 간단하게만 알아볼 것이다.
0\~6까지의 수준이 있지만, 이 중에서 한 번에 여러 개를 같이 쓸 수도 있기에 실제 선택지는 더욱 넓다.


### 중복에 의한 신뢰성 향상

MTTF = Mean Time To Fail: 디스크가 실패할 때까지 걸리는 평균 시간   
HDD 제조 업체의 주장에 따르면, 오늘날 디스크의 MTTF범위는 500,000 시간 ~ 1,200,000 시간(약 57년 ~ 136년)이다.
신품을 기준으로 측정되었으며, 사용할수록 MTTF는 낮아진다. MTTF가 디스크의 실제 수명을 나타내지는 않는다.
디스크의 대부분은 기대 수명이 5년 정도에 불과하다.



여기서, 디스크의 MTTF가 100,000 시간(약 11년)이라고 가정하자. 그리고, 100개의 디스크로 된 하나의 배열이 있다고 하자.
이 때, 이 배열에서 일부 디스크가 실패할 MTTF는 1,000시간(약 42일)이다.
각 데이터를 한 번씩만 저장한다면, 굉장히 잦은 손실이 일어날 것이다.
이 정도의 손실은 문제가 있는 수준이다.



중복을 사용하면 이를 해결할 수 있다. 디스크가 실패하더라도 복원할 수 있도록, 여분의 정보를 저장한다.
이 방법을 사용하면 디스크가 실패해도 데이터는 날아가지 않는다.
따라서 실제 데이터를 못 쓰게 되는 경우로 MTTF를 계산할 경우, MTTF는 늘어나게 된다.


가장 간단한 방식의 중복은 미러링으로, 디스크 전체를 복사하는 것이다.
논리 디스크는 두 개의 물리 디스크로 구성되며, 두 디스크 모두에 쓰게 된다.
둘 중 하나가 실패해도, 다른 하나로부터 데이터를 읽어올 수 있다.
따라서 둘 다 실패하는 것(정확히는 복구가 일어나기 전에 둘 다 실패하는 것)이 진짜 실패이며, 하나만 실패하는 것은 신경쓸 필요가 없다.



이 논리 디스크의 MTTF는 당연히 개별 물리 디스크의 MTTF의 영향을 받는다. 또, 데이터 복구 평균 시간에도 영향을 받는다.
- 데이터 복구 평균 시간: 실패한 디스크를 교체해서 데이터를 다시 저장하는데 걸리는 평균 시간

MTTF가 100,000 시간이며, 데이터 복구 평균 시간이 10시간이라면 데이터 손실 평균시간은 57,000년이 된다고 한다.
단, 이는 실패가 독립적이라는 가정 하에서 성립한다.
자연재해/정전 등의 경우는 주로 독립적이지 않기에, 이를 그대로 적용하기는 어렵다.
특히 자주 일어날 수 있는 정전의 경우는 더 신경써서 대비해야 한다.
하나의 디스크에 먼저 쓰고, 그 다음에 다른 하나의 디스크에 쓰는 방식을 택한다면 두 복사본 중 하나는 항상 완전하다.
따라서 정전이 일어나도 복구할 수 있다. (물론 정전이 끝나고 나서 재시작 할 때의 추가적인 처리가 필요하긴 하다)


이러한 미러링 방식은, RAID 1이라고 불리운다.



- 정전 이후 추가적인 처리에 대하여

문제: 디스크 블록을 쓰는 동안 정전이 발생하면 블록 일부분만 쓰이게 된다.
부분적으로만 쓰인 블록을 감지할 수 있다고 가정하자.
원자 블록 쓰기(atomic block write)는 디스크 블록을 완전히 쓰거나 아예 아무것도 쓰이지 않게 하거나 둘 중 하나인데, 부분 쓰기는 전혀 아니다.
RAID1 을 사용하여 원자적으로 쓰는 법 원자 블록 쓰기의 효과를 얻는 방법을 제안해 보라.
제안하는 방법이 실패로부터 복구하는 작업과 관련 있어야 한다.

Redundant Array of Independent Disks (RAID) level 1 scheme:
RAID’s are a number of hard disks which store exact same data. Different RAID levels are available from 1 to 10, each with different significance.
The scheme for getting the effect of atomic block writes with the RAID level 1 (mirroring) is shown below:
- A block write operation is performed as follows, to secure atomicity.
  - Write the information onto the physical block which is located first.
  - Write the similar information onto the physical block located at the second, when the first write accomplishes successfully.
  - Only after the second write accomplishes successfully, the output is announced.
- Each set of “2” physical blocks is tested during recovery.
- Further process is not required if both are same and there is unnoticeable partial-write...




### 병렬화에 의한 성능 향상
이전에 소개한 미러링 방식도 병렬화에 의한 성능 향상을 누릴 수 있다.
디스크 미러링을 사용했으므로, 디스크가 두 개이다. 각 디스크에 요청을 보낼 수 있으므로, 읽기 요청 처리 속도는 2배가 된다.
[(단, latency 는 변하지 않고, throughput 만 변할 것으로 생각했지만.. [latency 도 이득을 볼 수 있다!](https://www.usenix.org/legacy/publications/library/proceedings/fast02/full_papers/zhang/zhang_html/node4.html))

RAID 0 방식은 분산(striping) 방식이다. 디스크 단위로 분산할 경우, disk striping이 된다.
하나의 데이터를 여러 개의 디스크에 분산해서 저장하는 방식이다.
한 번 요청할 경우, 여러 디스크에서 데이터를 불러오기에, throughput 이 향상되는 효과를 얻을 수 있다.
이 방식은 속도적인 측면에서 장점을 가지게 되지만, 하나의 데이터가 여러 개의 디스크로 나뉘어 있으므로, 하나의 디스크에만 문제가 생겨도 데이터를 읽지 못한다는 문제점도 존재한다.


<br><br>
latency vs throughput   
인터넷으로 따지자면, latency는 핑에 해당한다. (ms 단위) cpu의 클럭은 latency에 직결되는 요소이다. 주로 (작업 처리 시작 시간) ~ (작업 처리 완료 시간)을 의미한다.   
인터넷으로 따지자면, throughput은 인터넷의 초당 전송 속도에 해당한다. (GB/s, MB/s 단위). cpu의 코어 수는 latency에는 영향이 없으나, throughput에는 큰 영향을 미치는 요소이다. 주로 시간당 총 작업 수행량을 의미한다.

대용량 파일을 주로 다운 받는 사람이라면, latency(ping)이 높더라도, throughput(전송 속도)이 높은 것을 택하는 쪽이 좋을 것이다.
다운로드 요청을 보내고, 시작하는 시점은 느리더라도, throughput이 높기에 대용량 파일을 받는 작업 자체는 금방이기 때문이다.

다만 실시간으로 반응이 중요한 FPS게임을 하는 사용자라면, throughput보다는 latency가 중요해질 것이다.
게임에서 요구되는 데이터의 양은 많지 않지만, 사용자는 내가 총을 쏘면 실제로 서버에서도 바로 총이 날라가는 환경을 원할 것이다.
그러기 위해서는 나의 요청을 바로 인식할 수 있는, latency가 짧은 인터넷을 선택하는 편이 좋을 것이다.   

cpu는 연산을 사이클단위로 진행한다.
그리고 한 사이클을 도는 데 걸리는 시간은 클럭에 반비례한다.
따라서 클럭이 증가하면, 한 사이클을 도는 데 걸리는 시간이 감소하므로, 하나의 연산을 수행하는 시간이 짧아진다.
따라서 cpu의 클럭은 latency에 직결된다.

반대로 코어 수는 throughput에 있어 굉장히 중요한 요소이다.
싱글 코어에서 듀얼 코어가 되었을 때, throughput은 (이론상으로) 2배가 될 수 있고, latency는 변하지 않는다.
하나의 연산은 결국 하나의 코어 위에서 돌아가기에, cpu가 두 개가 되었다고 해서 연산 하나하나가 빨라지지는 않는다.
하지만 두 개의 코어가 동시에 일하기에, 실제로는 두 배의 연산을 처리할 수 있을 것이다.
