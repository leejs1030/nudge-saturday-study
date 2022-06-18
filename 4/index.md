## 재귀
![image](https://user-images.githubusercontent.com/102134003/174430717-88a805f3-2c4e-41f9-9bbb-51190b6f1ceb.png)
선행 과목을 구하는 예제

CS-347의 선행 과목은 CS-319이다. CS-319의 선행 과목은 CS-315이다.
이러한 경우, CS-347의 선행 과목으로 테이블에 CS-315가 직접적으로 명시되어있지는 않지만, 실제로는 선행 과목이라고 할 수 있다.
이러한 간접적인 선행과목까지 모두 구하는 것을 SQL 쿼리를 통해 구현하는 것은 그리 간단하지 않을 수 있다. 다음은 반복을 통해 이를 구현한 내용이다.
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

이러한 반복에 비해서, 더욱 간단한 방법은 재귀를 사용하는 것이다. sql에서는 재귀 질의도 제공한다.
```SQL
with recursive rec prereq(course_id, prereq_id) as (
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
