---
sort: 1
---

# 오라클 데이터베이스

Oracle Database를 이용한 공부

# 오라클 1일차

** SQL 이란 ?**
(database 의 데이터를 검색하거나 조작하는 프로그램 언어)  
 Structure  Query  Language     
   구조적   질의   언어   

** SQL 의 종류**
1. Query   --> 데이터 검색 언어 , select 문의 6가지절 
2. DML 문 --> Data Manipulation Language (데이터 조작 언어) : insert, update, delete, merge
3. DDL 문  --> Data Definition Language(데이터 정의 언어)  :  create, alter, drop, truncate, rename
4. DCL 문  --> Data Control Language(데이터 제어 언어) :  grant, revoke 
5. TCL 문 --> Transaction Control  Language(트랜잭션 제어 언어) : commit, rollback, savepoint
 

ㅁ**접속**
sqlplus "/ as sysdba"

**ㅁ계정확인**
show user
USER은 "SYS"입니다 <--- 최상위 계정 위에 sysdba로 접속해서 sys 계정으로 접속

*최상위 계정은 database에서 최고위 권한을 가짐
주로 데이터베이스 관리 작업용이며 데이터 분석이나 검색은 이 계정에서 하지 않는게 좋다

**ㅁ계정 생성**
create c##user (만들 user ID)  <--c##은 보안을 위해서 생성시 붙여야함 (해킹 방지)
2 identified by (비밀번호);  ;<-- 여기까지 명령어다 라는 표기

**ㅁ권한 부여**
grant dba to c##(부여할 user ID);  <--- database에 있는 모든 데이터를 보고 수정할 권한
                                                    (관리자 권한 부여)

**ㅁc##user 로 접속**
connect c##(user ID)/(비밀번호)
sqlplus 접속후 다른 아이디로 접속하는 방법은 
sqlplus "/ as sysdba" 후 connect(conn) c##(user ID)/(비밀번호)
sqlplus c##(user ID)/(비밀번호)로 sqlplus 안들어가고 바로 접속하는 방법도 있음.

**ㅁc##user 사용할 실습 테이블 생성하기**
CREATE TABLE DEPT
       (DEPTNO number(10),
        DNAME VARCHAR2(14),
        LOC VARCHAR2(13) );

INSERT INTO DEPT VALUES (10, 'ACCOUNTING', 'NEW YORK'); <- 테이블 생성시 deptno,dname,loc  순서대로 입력됨
INSERT INTO DEPT VALUES (20, 'RESEARCH',   'DALLAS');
INSERT INTO DEPT VALUES (30, 'SALES',      'CHICAGO');
INSERT INTO DEPT VALUES (40, 'OPERATIONS', 'BOSTON');

CREATE TABLE EMP (
 EMPNO               NUMBER(4) NOT NULL,
 ENAME               VARCHAR2(10),
 JOB                 VARCHAR2(9),
 MGR                 NUMBER(4) ,
 HIREDATE            DATE,
 SAL                 NUMBER(7,2),
 COMM                NUMBER(7,2),
 DEPTNO              NUMBER(2) );

INSERT INTO EMP VALUES (7839,'KING','PRESIDENT',NULL,'81-11-17',5000,NULL,10);  <-테이블 생성시 empno,ename,job,mgr,hiredate,sal,comm,deptno 순서대로 입력
.....
commit;

**ㅁ오라클 c##scott 접속법** <--아까 scott로 생성했음
sqlplus c##scott/tiger

**ㅁemp테이블 소개**
empno = 사원번호
ename = 사원이름 
sal = 월급 
job = 직업 
hiredate = 입사일 
comm  = 커미션 
mgr  = 직속상사의 사원번호 
deptno = 부서번호

**ㅁ테이블 특정 컬럼(열)선택**
select empno,ename,sal
from emp;                   <--- emp테이블에서 empno,ename,sal 열불러오기

*화면에 컬럼이 ----치면서 한번더 나오는걸 방지하려면 set pages 100
  set pages 100으로 페이지길이(세로)를 100크기로 늘려줌
  set lines 100는 가로를 100크기로 넓혀주는 명령어                          

**ㅁdept 테이블**
부서테이블 , dptno -부서 번호 , dname-부서명, loc - 부서 위치

**ㅁ컬럼명 변경하기**
ex ) select ename  as 이름 , sal as 월급  <= 월 급 이렇게바꾸려면 "월 급"으로 띄어서 사용
                                               ""는 띄어쓰기 특수문자(_,$,#),영문 대소문자 구분시에 사용
 from emp; <-- 컬럼에 ename과 sal은 이름과 월급으로 바뀜

ㅁselect ename | | '의 월급은' | | sal | |' 입니다' <---''은 문자를 더하기 위해서 띄어 쓰기는 안쪽에해야 '안쪽'에 띄어쓰기가능

***컬럼명 변경(별칭 사용하면서) 문자 이어줄때 주의***

컬럼명 뒤에 as 를 붙여 병칭으로 바꾸어 줄 수 있지만 ||와 같이 쓸때 유의 해야하는점 

select ename as '이름'||'의 월급은'||sal as '월급'||'입니다<--- 이렇게 하나하나 바꿔 줄 수 없으며
 
select ename||'의 월급은 '||sal||' 입니다.'as "        월      급     " 이렇게하게되면 ename and sal이 합쳐 "월   급" 으로 변경된다
||으로 이어준 것은 밑에 테이블에 변경으로 이어진다. 
       월      급
--------------------------
SMITH의 월급은 800 입니다.
JAMES의 월급은 950 입니다.
ADAMS의 월급은 1100 입니다.
WARD의 월급은 1250 입니다.
MARTIN의 월급은 1250 입니다.
MILLER의 월급은 1300 입니다.
TURNER의 월급은 1500 입니다.
ALLEN의 월급은 1600 입니다.
CLARK의 월급은 2450 입니다.
BLAKE의 월급은 2850 입니다.
JONES의 월급은 2975 입니다.
FORD의 월급은 3000 입니다.
SCOTT의 월급은 3000 입니다.
KING의 월급은 5000 입니다.

이런식으로 표현 가능하다.


**ㅁ중복 데이터 제거**
Distinct사용 ex) select distinct job from emp; 

**ㅁ정렬 방법**
ex)select ename, sal from emp order by sal asc;
order by ~ asc (오름차순(ascending)) 낮은순
order by ~ asc (내림차순(descending)) 높은순

-----------------------------------------------------------------------------------------------------------------------------------------------------
▩ 문제1.  사원이름, 월급, 직업을 출력하시오 !
           select ename, sal, job
           from emp; 

문제2.  사원번호, 사원이름, 월급, 직업, 부서번호를 출력하세요 !

정 답 select empno, ename, sal, job, deptno
      from  emp;

▩ 002 테이블에서 모든 열(COLUMN) 출력하기
        select   *
        from  dept;
  
  설 명 * (별표) 는 asterisk 라고 칭하며. 모든 컬럼을 다 검색해라 라는 뜻입니다. 
          
* dept 테이블의 컬럼 3개 소개 
  dept 는 부서 테이블 입니다.  부서에 대한 정보를 가지고 있는 테이블입니다.
deptno : 부서번호
dname : 부서명
loc : 부서위치 

문제3. 부서 테이블에서 부서번호, 부서명을 출력하시오 !

정 답 select  deptno,  dname
      from  dept ;

문제4. 사원 테이블에서 모든 컬럼을 다 출력하시오 !

정 답 select   *
      from emp;

▩ 003 컬럼 별칭을 사용하여 출력되는 컬럼명 변경하기

   select  ename  as  이름,  sal  as  월급
   from   emp; 
   설명  컬럼명 옆에 as 를 쓰고 출력하고 싶은 컬럼명을 기술 as 는 생략 가능합니다. 

문제5. 이름과 직업과 부서번호를 출력하는데 컬럼 별칭을 써서 이름과 직업은 한글로 컬럼명이 출력되게하시오 ! 

정 답 select ename as 이름, job as 직업, deptno
      from emp;

문제6.  이름과 직업과 부서번호를 출력하는데 컬럼명이 전부 한글로 출력되게 하시오 !

정 답 select ename as 이름, job as 직업, deptno as 부서번호
      from emp ;


*select  ename  as  이름, job  as 직업, deptno  as  "부서 번호"
 from  emp; 
*설명 다음과 같이 컬럼 별칭을 사용할 때 '부서' 와 '번호' 사이에 공백을 넣으려면 양쪽에 더블 쿼테이션 마크를 둘러줘야 합니다. 
양쪽에 더블 쿼테이션 마크를 사용하는 경우는 오라클 전체를 통틀어서이 경우밖에 없습니다. 

 1. 컬럼 별칭 사용시  공백문자를 사용할 때
 2. 컬럼 별칭 사용시 특수문자를 사용할 때 ( _, $ , # )
 3. 컬럼명을 영문으로 출력하는데 무조건 대문자로 출력하지 않고
     대소문자를 구분해서 출력하고 싶을때 
ex)select  ename as  "ename", sal  as  "sal"
   from emp;



▩  004 연결 연산자 사용하기(||)
연결 연산자는 키보드의 엔터키 위에 있는 수직바를 두번 첨부,이 연결 연산자로  두개의 컬럼을 연결해서 출력을 합니다.

select ename  ||  sal
from emp;  

select ename || '  의 월급은  ' ||  sal
from  emp; 

문제7.  아래의 결과를 출력하세요 !

KING 의 월급은  5000 입니다
BLAKE 의 월급은  2850 입니다
CLARK 의 월급은  2450 입니다
JONES 의 월급은  2975 입니다
MARTIN 의 월급은  1250 입니다
ALLEN 의 월급은  1600 입니다
TURNER 의 월급은  1500 입니다
JAMES 의 월급은  950 입니다
WARD 의 월급은  1250 입니다
FORD 의 월급은  3000 입니다
SMITH 의 월급은  800 입니다
SCOTT 의 월급은  3000 입니다
ADAMS 의 월급은  1100 입니다
MILLER 의 월급은  1300 입니다

정 답 select  ename ||  ' 의 월급은  '  ||  sal  ||  ' 입니다 '
      from  emp ;

*설명 싱글 쿼테이션 마크를 둘러줘야하는 이유는 싱글 쿼테이션 마크안에 있는것이 '문자' 다라는 것을 오라클에게 알려주는 것 입니다. 

▩ 005 중복된 데이터를 제거해서 출력하기(DISTINCT)

예제. 직업을 출력하는데 중복을 제거해서 출력하시오 

  select  distinct  job
  from   emp;

*설명 distinct 키워드는 중복을 제거하는 키워드입니다,출력되는 데이터의 중복을 제거하고 싶은 컬럼명 앞에 사용 

문제8. 부서번호를 출력하는데 중복을 제거해서 출력하시오 !

정 답 select  distinct  deptno
      from emp;

문제9. 우리회사에 관리자인 사원들의 사원번호를 출력하시오 ! 
           ( 관리자인 사원의 사원번호는 mgr  입니다 ) 

정 답 select  distinct   mgr
     from   emp;

▩  006 데이터를 정렬해서 출력하기(ORDER BY)

예제.  이름과 월급을 출력하는데 월급이 낮은 사원부터 높은 사원순으로 출력하시오 !

정 답select  ename, sal
     from emp
     order  by  sal   asc ;

*설명 order by 절은 맨 아래에 기술하여 사용

1.  컬럼명의 데이터가 낮은 값부터 출력하고 싶다면 ? 
    order  by  컬럼명  ascending   
    order  by  컬럼명  asc 
  
2.  컬럼명의 데이터가 높은 값부터 출력하고 싶다면 ?

order  by  컬럼명  descending
order by  컬럼명  desc 

문제10. 이름과 월급을 출력하는데 월급이 높은 데이터 부터 낮은 데이터 순으로
           출력하시오 !

정 답select  ename, sal
     from  emp
     order   by   sal  desc; 

문제11. 이름, 월급, 입사일을 출력하는데  최근에 입사한 사원부터 출력되게 하시오 !  

정 답select ename,job,hiredate
     from emp
     order by hiredate desc;

## 오라클 2일차

> This is a blockquote following a header. Bacon ipsum dolor sit amet t-bone doner shank drumstick, pork belly porchetta chuck sausage brisket ham hock rump pig. Chuck kielbasa leberkas, pork bresaola ham hock filet mignon cow shoulder short ribs biltong.

### 오라클 3일차

```
This is a code block following a header.
```

#### 오라클 4일차

- This is an unordered list following a header.
- This is an unordered list following a header.
- This is an unordered list following a header.

##### Header 5

1. This is an ordered list following a header.
2. This is an ordered list following a header.
3. This is an ordered list following a header.

###### Header 6

| What    | Follows  |
| ------- | -------- |
| A table | A header |
| A table | A header |
| A table | A header |

---

There's a horizontal rule above and below this.

---

Here is an unordered list:

- Salt-n-Pepa
- Bel Biv DeVoe
- Kid 'N Play

And an ordered list:

1. Michael Jackson
2. Michael Bolton
3. Michael Bublé

And an unordered task list:

- [x] Create a sample markdown document
- [x] Add task lists to it
- [ ] Take a vacation

And a "mixed" task list:

- [ ] Steal underpants
- ?
- [ ] Profit!

And a nested list:

- Jackson 5
  - Michael
  - Tito
  - Jackie
  - Marlon
  - Jermaine
- TMNT
  - Leonardo
  - Michelangelo
  - Donatello
  - Raphael

Definition lists can be used with HTML syntax. Definition terms are bold and italic.

<dl>
    <dt>Name</dt>
    <dd>Godzilla</dd>
    <dt>Born</dt>
    <dd>1952</dd>
    <dt>Birthplace</dt>
    <dd>Japan</dd>
    <dt>Color</dt>
    <dd>Green</dd>
</dl>

---

Tables should have bold headings and alternating shaded rows.

| Artist          | Album          | Year |
| --------------- | -------------- | ---- |
| Michael Jackson | Thriller       | 1982 |
| Prince          | Purple Rain    | 1984 |
| Beastie Boys    | License to Ill | 1986 |

If a table is too wide, it should condense down and/or scroll horizontally.

<!-- prettier-ignore-start -->

| Artist            | Album           | Year | Label       | Awards   | Songs     |
|-------------------|-----------------|------|-------------|----------|-----------|
| Michael Jackson   | Thriller        | 1982 | Epic Records | Grammy Award for Album of the Year, American Music Award for Favorite Pop/Rock Album, American Music Award for Favorite Soul/R&B Album, Brit Award for Best Selling Album, Grammy Award for Best Engineered Album, Non-Classical | Wanna Be Startin' Somethin', Baby Be Mine, The Girl Is Mine, Thriller, Beat It, Billie Jean, Human Nature, P.Y.T. (Pretty Young Thing), The Lady in My Life |
| Prince            | Purple Rain     | 1984 | Warner Brothers Records | Grammy Award for Best Score Soundtrack for Visual Media, American Music Award for Favorite Pop/Rock Album, American Music Award for Favorite Soul/R&B Album, Brit Award for Best Soundtrack/Cast Recording, Grammy Award for Best Rock Performance by a Duo or Group with Vocal | Let's Go Crazy, Take Me With U, The Beautiful Ones, Computer Blue, Darling Nikki, When Doves Cry, I Would Die 4 U, Baby I'm a Star, Purple Rain |
| Beastie Boys      | License to Ill  | 1986 | Mercury Records | noawardsbutthistablecelliswide | Rhymin & Stealin, The New Style, She's Crafty, Posse in Effect, Slow Ride, Girls, (You Gotta) Fight for Your Right, No Sleep Till Brooklyn, Paul Revere, Hold It Now, Hit It, Brass Monkey, Slow and Low, Time to Get Ill |

<!-- prettier-ignore-end -->

---

Code snippets like `var foo = "bar";` can be shown inline.

Also, `this should vertically align` ~~`with this`~~ ~~and this~~.

Code can also be shown in a block element.

```
var foo = "bar";
```

Code can also use syntax highlighting.

```javascript
var foo = "bar";
```

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```javascript
var foo =
  "The same thing is true for code with syntax highlighting. A single line of code should horizontally scroll if it is really long.";
```

Inline code inside table cells should still be distinguishable.

| Language   | Code               |
| ---------- | ------------------ |
| Javascript | `var foo = "bar";` |
| Ruby       | `foo = "bar"`      |

---

Small images should be shown at their actual size.

![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png)

Large images should always scale down and fit in the content container.

![Branching](https://guides.github.com/activities/hello-world/branching.png)

```
This is the final element on the page and there should be no margin below this.
```
