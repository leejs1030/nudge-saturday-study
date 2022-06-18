## 재귀
![image](https://user-images.githubusercontent.com/102134003/174430717-88a805f3-2c4e-41f9-9bbb-51190b6f1ceb.png)
선행 과목을 구하는 예제

CS-347의 선행 과목은 CS-319이다. CS-319의 선행 과목은 CS-315이다.
이러한 경우, CS-347의 선행 과목으로 테이블에 CS-315가 직접적으로 명시되어있지는 않지만, 실제로는 선행 과목이라고 할 수 있다. 이러한 간접적인 선행 과목을 구하는 과정은 그래프 이론에서의 이행 폐포(transitive closure)를 구하는 것과 같다고 할 수 있다.
그리고 이행 폐포를 구하는 것을 SQL 쿼리를 통해 구현하는 것은 그리 간단하지 않을 수 있다. 다음은 반복을 통해 이를 구현한 함수, findAllPrereqs이다. 이 함수는 cid를 받아서, 해당 과목의 모든 직간접적 선행 과목의 집합을 계산하여 반환한다.
```SQL
create function findAllPrereqs(cid varchar(8))
  – – Finds all courses that are prerequisite (directly or indirectly) for cid
returns table (course_id varchar(8))
  – – The relation prereq(course id, prereq id) specifies which course is
  – – directly a prerequisite for another course.
begin
  create temporary table c_prereq (course_id varchar(8));
    – – table c prereq stores the set of courses to be returned
  create temporary table new_c_prereq (course_id varchar(8));
    – – table new c prereq contains courses found in the previous iteration
  create temporary table temp (course_id varchar(8));
    – – table temp is used to store intermediate results
  insert into new_c_prereq
    select prereq_id
    from prereq
    where course_id = cid;
  repeat
    insert into c_prereq
      select course_id
      from new_c_prereq;
      
    insert into temp
      (select prereq.prereq_id
        from new_c_prereq, prereq
        where new_c_prereq.course_id = prereq.course_id
      )
      except (
        select course_id
        from c_prereq
      );
    delete from new_c_prereq;
    insert into new_c_prereq
      select *
      from temp;
    delete from temp;
    
  until not exists (select * from new_c_prereq)
  end repeat;
  return table c_prereq;
end
```
사용한 임시 테이블
- c_prereq: 반환되는 튜플 집합 저장.
- new_c_prereq: 이전의 반복에서 발견된 과목을 저장.
- temp: 과목들의 집합을 유지하기 위한 임시 저장소로 사용됨.


진행되는 과정
1. 시작할 때, 반복이 시작되기 이전에 new_c_prereq에 직접적 선행 과목을 추가한다.
2. repeat 반복에서는 먼저 new_c_prereq의 모든 과목을 c_prereq에 추가한다.
3. 이미 발견된 과목을 제외한 new_c_prereq의 모든 과목의 선행 과목을 찾고 이를 temp에 저장한다.
4. new_c_prereq의 내용을 temp의 내용으로 교체한다.
5. 새로운 선행 과목을 찾을 수 없을 때까지 2~4를 반복한다.

except절은 순환이 있을 경우에도 잘 처리될 수 있도록 해준다. 선행 과목을 구하는 경우에는 정상적이라면 사이클이 생기지 않겠지만, 만약 다른 예제에서도 응용해야할 경우 필요한 부분이다.




하지만 이처럼 반복을 사용한 내용은 너무나도 길고, 복잡하다. 이러한 반복에 비해서, 더욱 간단한 방법은 재귀를 사용하는 것이다. 주어진 문제 자체도 재귀적인 사고를 통해 더 쉽게 해결할 수 있을 것으로 보인다. 따라서, 재귀를 통해 이를 해결하고자 하는데, sql에서는 재귀 질의도 제공한다. 바로 재귀적 뷰 정의이다.
```SQL
with recursive rec_prereq(course_id, prereq_id) as (
    select course_id, prereq_id
    from prereq
  union
    select rec_prereq.course_id, prereq.prereq_id
    from rec_prereq, prereq
    where rec_prereq.prereq_id = prereq.course_id
)
select ∗
from rec_prereq;
```
위의 예시는 다음과 같이 해석할 수 있다.
with recursive를 통해 임시적인 재귀젹 뷰를 생성한다.
재귀적 뷰는 반드시 두 개의 하위 질이의 합으로 정의되어야 한다. 기본 질의는 재귀적이어서는 안 된다. 재귀 질의는 재귀적 뷰를 사용한다.
위 예제에서 첫 번째 select문은 기본 질의이다. 두 번째 select문은 재귀 질의이며, 재귀젹 뷰인 rec_prereq와 prereq의 조인을 사용하고 있다.

재귀적 뷰의 의미는 다음과 같이 이해해야 한다. 기본 질의를 계산하고, 결과의 모든 튜플을 rec_prereq에 추가한다. (처음에 rec_prereq는 비어 있는 상태이다.)
그 다음 현재 내용들을 사용하여 재귀 질의를 계산하고, 모든 결과들을 다시 뷰 릴레이션에 추가한다. 그리고 결과 뷰 릴레이션에 변화가 없을 때까지 반복한다.
재귀를 도는 과정에서 계속해서 뷰 릴레이션에 변화가 생길 것이다. 반면, 마지막으로 나온 뷰 릴레이션의 인스턴스는 더 이상 바뀌지 않는 상태가 된 것이며, 이를 "고정점"이라고 부른다.


이러한 해석적인 부분 외에, 재귀 질의에는 몇 가지 추가적인 제한이 있을 수도 있다. 대표적으로 "재귀 질의는 단조로워야 한다"는 조건이다. 단조성에 대한 것은 [단조 함수](https://www.scienceall.com/%EB%8B%A8%EC%A1%B0%ED%95%A8%EC%88%98monotonemonotonic-function/)와 의미가 비슷하다고 생각해볼 수 있다. 단조 증가 함수 f에 대해서, x_1<=x_2라면 f(x_1)<=f(x_2)이다. 이와 비슷하게, 뷰 릴레이션 인스턴스 V_1과 V_2가 존재한다고 하자. 이 때, V_1 ⊂ V_2이라면, 재귀 g에 대해서 g(V_1) ⊂ g(V_2)이어야 한다는 뜻이다.

또, 재귀 질의에서 사용이 불가능한 것들도 있다.
- 재귀젹 뷰에서 집계
- 재귀적 뷰를 사용하는 하위 질의에서 not exists
- 오른편에서 재귀적 뷰를 사용하는 것을 제외한 차집합

사용해서 안 되는 이유는 위의 구문들이 쿼리를 단조롭지 않게 만들기 때문이다.


단조로운 재귀 질의는 반복 프로시저에 의해 나타내질 수 있다. 재귀 질의가 단조롭지 않다면, 뷰의 의미를 정의하기 힘들어진다. 따라서 SQL은 질의가 단조로워야 한다.
