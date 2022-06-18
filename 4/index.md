재귀
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
