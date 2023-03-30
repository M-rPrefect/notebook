```mysql
-- alter

alter table teacher rename as teachers;

alter table teachers add friend varchar(30) default null;

alter table teachers modify friend int(20);

alter table teachers change friend friends int(20);

alter table teachers drop friends;

drop table teachers;


-- insert

insert into `student`(`stuid`,`stuname`,`age`) values (`1`,`chhd`,`18`);

insert into `student` values (`2`,`sdd`,`19`)

-- update

update teacher set `stuname` = 'chb' where  name 'lihua'

update `teacher` set age = 23 where id > 3;

update `teacher` set stuname where id between 2 and 5;

-- select

select stuname from student where stuname like 'chen_';

select stuid from student where stuname in 'li'

-- 联表查询 
-- student teacher

select stuid,stuname,teacherid,teachername 
from student 
left join teacher
on student.stuid = teacher.suid

select stuid,stuname 
from student 
where stuid in( select stuid from class where class  )
```

```mysql
create table teachers(
     id int(50) not null auto_increment,
     name varchar(100) not null default 'Jetom',
     sex varchar(2) not null default '女',
     type varchar(60) not null default '语文',
     age int(20) not null default 18,
     birthday datetime default null,
     primary key(id)
     )engine=innodb default charset=utf8;
```

```mysql
create table student(
     id int(20) not null auto_increment,
     name varchar(100) not null default '学生胖仔',
     class_teacher varchar(100) not null default 'Jetom',
     class_group int(10) not null default 1,
     age int(20) not null default 21,
     join_time datetime default null,
     primary key(id)
     )engine=innodb default charset=utf8;
```

