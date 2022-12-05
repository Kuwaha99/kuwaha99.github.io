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
**ㅁ sqldeveloper 설치 및 셋팅하기**

oracle사이트에서 sql developer 64bit용 다운을 받은후 
sqldeveloper.exe파일 관리자 권한으로 실행 후 설치


명령 프롬포트 창에서 lsnrctl status 명령으로 리스너의 호스트와 포트 번호를 확인

HOST=DESKTOP-B8KQDRM
포트번호 = 1521

** 호스트 이름: 건물의 주소, localhost 라고 해도 됩니다. 
   포트 :  건물 내의  통로 번호, 1521
   sid :  회사명, xe ( 데이터 베이스 이름)

Develoer에 접속 후
오른쪽 상단에 +누른 후 명령 프롬포트창에서 확인한 사항 입력 
이름 및 아이디 비밀번호 입력 후 테스트 하여 접속이 잘 된다면 
생성하여 테이블창을 만들어준다.

* 명령 프롬포트와 동일하게 사용이 가능하며 좀더 깔끔하게 테이블이 생성된다는 장점이 있음.
   develoer는 실행시 ctl + enter로 실행 

*** sqlplus에서의 실행순서**
select문 <---2
from 문  <---1
order by 문 <---3

select문에서 컬럼 별칭을 인식하고 order by문으로 넘어가기 때문에 
select 문에서 job as 직업 작성후 order by 직업 asc; 가능하다
order by 숫자로 asc; 이런식으로도 가능하다 이 숫자는 select 문에서 순서를 나타냄 2번이면 job,ename,sal 에서 ename으로 인식

***order by 문은 1가지 정렬 뿐 아니라 동시에 정렬이 가능한데 order by a asc , b desc; 가능** 
---------------------------------------------------------------------------------------------------------------------
**ㅁ 테이블 생성**
create  table  emp16     <---테이블 생성 테이블명(emp16)

( empno       number(10) ,    <---사원 번호 숫자(10)
  ename         varchar2(20),   <---이름       문자(10)
  age             number(10),   <--- 나이       숫자(10)
  birth            date,           <--- 생일       날짜
  telecom        varchar2(10),    <---통신사   문자(10)
  major           varchar2(50),   <---전공       문자(10)
  email           varchar2(60),   <---이메일     문자(10)
  address        varchar2(100) );   <---주소    문자(10)

insert into emp16 values(     )  <<<-emp 16테이블에 정보(위의 순서대로 입력 사원 번호,이름 ...); 입력 후
commit; <---커밋으로 저장 하여줌
---------------------------------------------------------------------------------------------------------------------

**ㅁwhere문**
 where 검색조건을 주면 특정 행들 선택
 
where문의 실행순서
 ex) select             <---3 
     from               <---1  
     where a >333;   <---2                  
                           (순서)

select ename, sal 
from emp 
where ename = 'SCOTT';  <---' '대소문자 구분, 문자와 날짜의경우는 숫자와는 달리 양쪽에 싱글 쿼테이션 마크를 둘러야함
                        
       * 데이터가 대문자로 되어 있으면 대문자로 검색해야하며 소문자일경우 소문자로 검색해야한다.
       * 숫자의 조건의 경우 ' ' 을 사용 할필요 x 사용시 문자로 인식된 숫자가 숫자로 변환하면서 오래걸림
---------------------------------------------------------------------------------------------------------------------
*********************************************************************************************************************
order by 문에서는 as를 사용한 명칭이나 숫자 등으로 간단하게 검색이 가능했지만 where문에서는 검색이 되지 않는걸 확인할수 있다
where 문에서 조건 후 = 대문자 데이터를 소문자로 검색 시 검색이 안되는 걸 볼 수 있다 order by문과 달리 실행 순서가 다르기 때문
         데이터는 대,소문자를 구분하고 sql은 대소문자를 구분하지 않습니다

데이터 = select *
            from emp
            where job = 'SALESMAN'       명령문 select.from,where (대소문자 구분x) ,SALESMAN(데이터)

SQL은 대소문자를 구분하지 않고 가독성을 위해서 들여쓰기 권장
절과 절은 따로 분리해서 쓰기를 권장
**********************************************************************************************************************
---------------------------------------------------------------------------------------------------------------------
**ㅁorder by , where문 혼합형으로 사용 했을때**

 혼합해서 실행 했을때 실행 순서

  select ename 이름,sal 월급,job 직업 <<<<-3
   from emp                                <<<<-1
   where job = 'SALESMAN'             <<<<-2
   order by sal asc;                        <<<<-4
---------------------------------------------------------------------------------------------------------------------
** ㅁ 산술 연산자**

*, / ,+, - 기능등을 사용 할 수 있다.

기존의 생선된 테이블에서 계산하면 그 테이블 수 많큼 출력이 되기 때문에
 단순 계산을 할때는 가상의 테이블을 이용 하여 계산을 이용 할 수 있습니다. 
 가상의 테이블은 from dual;로 임시 생성이 가능합니다.
Ex) select 3000 + 2000
     from dual;

*계산식 사용하는 sql에서는 컬럼 별칭을 사용하는걸 권장 하고 있습니다 
(컬럼 별칭으로 간결하게 보이기 때문이라고 생각 하고 있습니다. (해깔리지 않고))
---------------------------------------------------------------------------------------------------------------------
 **ㅁ 비교 연산자**
          >   : 크다
          <   : 작다 
        >=   :크거나 같다
        <=   :작거나 같다
          =   :같다
!=, <> ,^= :같지않다.
---------------------------------------------------------------------------------------------------------------------
 **ㅁ 비교 연산자2**

검색 조건은 where절에서 쓰이며
같은 비교 연산자이다 하지만 새부 조건을 걸려면 
and 조건으로 미만 혹은 초과를 사용 하려면 and조건이 필요

Between : where sal between 1000 and 3000
                                      (낮은값)  (높은값) 이상 이하로잡혀 둘다 포함
And      :  where sal >= 1000 and sal <= 3000

---------------------------------------------------------------------------------------------------------------------
 **ㅁ 실습 예제**
문제13.  이름과 월급을 출력하는데 이름을 ABCD 순서데로 출력되게하시오!

정 답:
2 select  ename, sal
1   from   emp
3   order   by   ename   asc;

설명:   order   by  컬럼명   옵션
        order by 절은 코딩 순서도 맨 마지막이고 
        오라클이 내부적으로 수행하는 실행 순서도 맨 마지막에 실행됩니다.  


문제14.   이름과 직업을 출력하는데 직업을 ABCD 순서데로 출력하시오 !

정 답:
select  ename, job
  from   emp
  order   by   job  asc;

문제15.(복습문제) 위의 결과를 다시 출력하는데 컬럼명이 한글로  이름, 직업
            으로 출력되게하시오 !

정 답:
select  ename   as  이름,  job   as  직업
  from   emp
  order   by   직업  asc; 


문제16.  우리반 테이블에서  이름과 나이를 출력하는데 나이가 높은 학생부터
             출력하시오 !

정 답:
select   ename,  age
  from  emp16
  order  by  age  desc;

문제17.  위의 결과를 다시 출력하는데  이름과 나이와 생일을 출력하고 나이가 높은 학생부터 출력하면서 생일이 빠른 순으로 출력되게하시오

정 답:
select  ename, age, birth
   from   emp16
   order  by    age  desc ,  birth  asc ;

설명: 나이가 높은 순서데로 정렬된것을 기준으로 삼고
         생일이 빠른 순서로 정렬합니다.

문제18.  이름, 직업, 월급을 출력하는데 직업을 ABCD 순서데로 출력되게하고 직업이 정렬된것을 기준으로 삼고
        월급이 높은 순서데로 정렬해서 출력되게하시오 !

정 답:
 select ename, job, sal
   from  emp
   order  by  job asc, sal desc;

문제19.  우리반 테이블에서 모든 컬럼들을 다 출력하는데 주소가 가나다라 순서데로 정렬되어서 출력되게하시오 !

정 답:
select  *
  from  emp16
  order  by  address  asc ;

문제20.   우리반 테이블에서 모든 컬럼을 다 출력하는데  통신사를 abcd 순으로 정렬해서 출력하시오 !

정 답:
select  *
  from  emp16
  order  by  telecom  asc ;

문제21.  우리반 테이블에서 모든 컬럼을 다 출력하는데 주소를 가나다라 순서데로 출력하고 주소를 정렬한것을 기준으로 
         통신사를 ABCD 순으로 정렬해서 출력하시오 !

정 답:
select  *
  from  emp16
  order  by  address asc, telecom asc;

문제22.  우리반 테이블에서  이름과  나이와 주소를 출력하는데 성씨가 가나다라 순서로 출력되게하시오 !

정 답:               
select  ename, age, address
 from emp16
 order  by ename asc;


문제23.  우리반 테이블에서 나이가 31살인 학생들의 모든 컬럼을 출력하시오 !

정 답:
select   *
  from   emp16
  where  age = 31; 

문제24. (점심시간 문제)  부서번호가 20번인 사원들의 이름과 월급과 부서번호를
             출력하시오 

문제25. 우리반 테이블에서 이름이 박용훈 인 학생의 이름과 나이와 전공을 출력하시오!

정 답:
select  ename, age, major 
  from    emp16
 where  ename = '박용훈' ;

설명:  싱글 쿼테이션 마크로 둘러주면은 싱글 쿼테이션 마크 안에 있는 것은
          숫자가 아니라 문자다 라고 알려주는 것입니다. 

select   내가 보고 싶은 컬럼명
 from   내가 보고 싶은 데이터가 있는 테이블명 
 where   검색 조건  ;  

문제26. 직업이 SALESMAN 인 사원들의 이름과 월급과 직업을 출력하시오 !

정 답:
select  ename, sal, job
  from   emp
  where   job ='SALESMAN';

설명:  다음과 같이 salesman을 소문자로 검색하면 데이터가 출력이 안됩니다.

문제27.  우리반에서  통신사가 kt 인 학생들의 이름과 나이과 통신사를 출력하시오!

정 답:
select   ename, age, telecom
  from   emp16
  where  telecom='kt';

문제28.  나이가 31살인 학생들의 이름과 나이를 출력하시오 !

정 답:
select  ename, age
  from   emp16
 where  age=31; 

설명:  숫자이므로 양쪽에 싱글 쿼테이션 마크를 둘러줄 필요가 없습니다.
         그런데 다음과 같이 싱글 쿼테이션 마크를 둘러주면 실행될까요 ? 

문제29.   사원 테이블에서 직업이 ANALYST 인 사원들의 이름과 직업을 출력하시오 !

정 답:
select  ename, job
   from   emp
   where   job= 'ANALYST' ; 

문제30.  (복습문제)  위의 결과를 다시 출력하는데 컬럼명이 한글로 이름, 직업으로
             출력되게하시오 !
정 답:
select  ename  as  이름,  job  as  직업
   from   emp
   where   job= 'ANALYST' ; 

문제31. (복습문제) 이름과 월급을 출력하는데 월급이 높은 사원부터 출력하시오 !

정 답:
select  ename, sal
  from  emp
 order  by  sal  desc; 

문제32. (복습문제)  이름과 월급과 직업을 출력하는데 이번에는 월급이 낮은 사원부터
            출력하시오 !

정 답:
select  ename, sal, job
  from   emp
  order   by   sal   asc ;

문제33.  위의 결과를 다시 출력하는데 직업이 SALESMAN 인 사원들만 출력하시오 !

정 답:
select  ename, sal, job
  from   emp
  where   job='SALESMAN'
  order   by   sal   asc ;

문제34.  사원 테이블에서 부서번호가 20번인 사원들의 이름과 월급과 부서번호를
             출력하는데 월급이 높은 사원부터 낮은 사원순으로 출력하시오 !

정 답:
select   ename, sal, deptno
  from  emp
  where  deptno = 20 
  order   by  sal   desc; 

문제35.  통신사가  kt 인 학생들의  이름과 나이와 통신사를 출력하는데 나이가 적은 학생부터 높은 학생순으로 출력하시오 !

정 답:
select   ename, age, telecom 
  from   emp16
  where   telecom = 'kt'
  order   by  age   asc; 

문제36.  위의 결과를 다시 출력하는데 컬럼명을 한글로 이름, 나이, 통신사로 
             출력되게하시오 !

정 답:
select   ename  as  이름, age  as  나이, telecom  as   통신사
  from   emp16
  where   telecom = 'kt'
  order   by  age   asc; 

* 날짜를 검색하는 방법


예제.  사원 테이블에서 이름과 입사일을 출력하시오 !

정 답:
select  ename, hiredate
  from  emp;

예제. 82/12/22 에 입사한 사원의 이름과 입사일을 출력하시오 !

정 답:
select  ename, hiredate
  from  emp
  where   hiredate  = '82/12/22'  ;  

설명: 날짜도 양쪽에 싱글 쿼테이션 마크로 둘러줘야 합니다. 

문제37.   88/07/03 에 태어난 학생의 이름과 생일을 출력하시오 !

정 답:
select  ename,  birth 
  from   emp16
  where  birth =  '88/07/03'  ;

설명: 날짜 검색할 때는 날짜 양쪽에 싱글 쿼테이션 마크를 둘러줘야 합니다.

문제38. 우리반에 생일이 같은 학생이 있는지 지금 까지 배운 SQL을 이용해서
             어떻게든 확인하시오 !

정 답:
select   ename,  birth
  from   emp16
  order  by   birth  asc; 


문제39.  아래의 식을 SQL 로 작성해서 값을 출력하시오 !

   200 + 3  *  2  

정 답:
select  200 + 3  *  2 
  from   dual; 

설명:  위의 식은 곱하기 부터 수행했습니다.  

문제40.  위의 식을 더하기 부터 수행되게 해보세요 !

정 답:
select  (200 + 3)  *  2 
  from   dual; 

설명:  금융권에서 SQL 작성할 때는 정말 조심히 잘 작성해줘야합니다. 
          금액이 확 달라지기 때문에 조심히 작성해야합니다. 

문제41.  이름, 월급,  월급 + 200 을 출력하는데 컬럼명이 한글로 다음과 같이 
             출력되게하시오.   이름, 월급, 보너스 

정 답:
 select   ename as  이름,  sal  as  월급,  sal + 200  as  보너스
       from   emp; 

설명: 계산식을 사용하는 SQL 에서는 컬럼별칭을 사용하는것을 권장합니다. 

문제42.  우리반 테이블에서 30살 이상인 학생들의 이름과 나이와 주소를 출력하시오

정 답:
select  ename, age, address
  from  emp16
  where  age >= 30;

문제43. 위의 결과를 다시 출력하는데 나이가 높은 학생부터 출력하시오 !

정 답:
select  ename, age, address
  from  emp16
  where  age >= 30
  order   by  age   desc;

문제44. 직업이 SALESMAN 이 아닌 사원들의 이름과 직업을 출력하시오 !

정 답:
select  ename, job
 from  emp
 where  job != 'SALESMAN';

설명: 같지 않다는 3가지가 있습니다.  !=, <>, ^= 입니다. 

문제45. (복습문제)   직업을 출력하는데 중복을 제거해서 출력하시오 ~

정 답:
select  distinct job
  from  emp; 

문제46. (복습문제)  우리반 테이블에서 통신사를 출력하는데 중복을 제거해서
            출력하시오 !

정 답:
select  distinct  telecom
  from  emp16; 

문제47.  월급이 1000 에서 3000 사이인 사원들의 이름과 월급을 출력하는데
              1000과 3000은 포함하지 말고 검색되게 하시오 !

정 답:
select   ename, sal
  from   emp
  where   sal > 1000     and     sal < 3000; 

설명: 이 문제는 between ... and 연산자로는 검색할 수 없습니다. 

문제48. 81/01/01 부터 81/12/31  사이에 입사한 사원들의 이름과 입사일을 출력하시오
            (81/01/01 과 81/12/31 을 포함해서 검색되게 하세요)

정 답:
select  ename,  hiredate
  from   emp
  where  hiredate  between  '81/01/01'   and  '81/12/31'; 

문제49. 82년도에 입사한 사원들의 이름과 입사일을 출력하시오 !

정 답:
select  ename,  hiredate
  from   emp
  where  hiredate  between  '82/01/01'   and  '82/12/31'; 

문제50. 97년도에 태어난 학생들의 이름과 생일을 출력하시오 !

정 답:
select   ename,  birth
 from  emp16
 where  birth   between  '97/01/01'   and   '97/12/31'; 

문제51. 나이가 26살부터 29살 사이의 학생들의 이름과 생일과 나이를  출력하시오 !

정 답:
select  ename, birth, age
  from emp16
  where  age  between 26  and 29 ;

문제52. (복습문제)  직업이 CLERK 인 사원들의 이름과 월급을 출력하는데 월급이 높은 사원부터 출력하시오 !
정 답:
 select  ename, sal
   from  emp
   where  job='CLERK'
   order  by  sal  desc;

문제53. (복습문제)  부서번호가 20번인 사원들의 직업만 출력하시오 !

정 답:
select   job 
  from   emp
  where  deptno = 20 ; 

문제54. (복습문제)  위의 결과를 다시 출력하는데 중복을 제거해서 출력하시오!

정 답:
select  distinct  job 
  from   emp
  where  deptno = 20 ; 

문제55. (복습문제)  우리반 테이블에서 나이대가 30대인 학생들의 이름과 나이를
            출력하시오 !

정 답:
select  ename,  age
  from   emp16
  where   age  between  30   and   39; 

문제56. (복습문제)  우리반 테이블에서 나이가 20대인 학생들의 이름과 통신사와
            나이를 출력하는데 통신사를 abcd 순으로 정렬해서 출력하시오 !

정 답:
select  ename, telecom, age
  from  emp16
  where   age  between   20   and   29 
  order  by  telecom  asc; 

문제57. 우리반 테이블에서 나이가 30대인 학생들의 이름과 나이와 통신사를 출력하는데 다음과 같이 출력되게 하시오 !

정 답:
select ename||'의 나이는'||age||' 이고 통신사는'||telecom||' 입니다'
 from emp16
 where age BETWEEN 30 and 39
 order by telecom asc;


### 오라클 3일차

**ㅁ 부정문**
not을 사용하여 부정문을 사용하여 반대 결과를 출력하기 가능합니다.
not은 

  select ename,sal
   from emp
   where sal BETWEEN 1000 and 3000; 

  1000~3000의  sal값

  select ename,sal
   from emp
   where sal not BETWEEN  1000 and 3000;

   1000~3000의 sal값을 제외한 나머지 sal값을 나타내 줍니다.

---------------------------------------------------------------------------------------

**ㅁ 이론**

 ※ 오라클 SQL 연산자의 종류 3가지
   1. 산술 연산자 : +,  -,  *,  / 
   2. 비교 연산자 : >, <, >=, <=, [!=, <>, ^=]
   3. 논리 연산자 : AND, OR, NOT


 ※ 기타 비교 연산자 4가지
   1.between A and b     <----->  not between A and B
   2.like                       <----->  not like
   3.in                         <----->  not in
   4.is null                    <----->  is not null


   *between = 사이의 포함된값

    like       = 시작점~?

    in         = 안의 모든 값을 비교 할수 있다  =과 다르게 여러값이고 =은 하나의 값을 비교할 때 사용한다. 
                  where A in ( ~,~,~);로 여러 값을 검색조건으로 가능하다
 
    is null    = null은 비어있는 값 혹은 알수 없는 unknown을 나타냅니다. 
                  where 문에 null값은 은 알수 없는 값이기 때문에 부등호 (=,!=)
                  등을 사용해서는 검색이 안되며 where A is null; 로 해야 검색가능 합니다.

 *like 연산자의 키워드 설명
       %  = 와일드카드라고 불리며 와일드 카드 자리에 무엇이든 몇개가와도 상관없습니다.
       _   = 언더바라고 불리며 이자리에 뭐가 와도 관계없지만 갯수는 한개여야 합니다.
 escape  = %와 _를 트수문자 %와 _로 검색하게 하는 키워드 입니다.



***ㅁ count***
   count는 개수를 세어주는 명령어로 
   select count(*) 셀렉트문에 사용하여 해당하는 항목의 개수를 세어 나타내어 줍니다.

 ***논리 연산자의 우선 순위에 따른 차이**

select ename,sal,job
  from emp
  where job = 'SALESMAN' or job = 'ANALYST' and sal >= 2000;
  
 select ename,sal,job
  from emp
  where (job = 'SALESMAN' or job = 'ANALYST') and sal >= 2000;


위의 두 가지는 언뜻 같은 결과값을 가질것 같지만 다른 결과 값을 보여줍니다.
논리 연산자의 우선순위로 인해서 생기는 차이인데 AND와 OR가 같이있으면 
AND의 순서가 먼저 이기 때문에 앞에 적혀있는 OR보다 AND의 논리의 순이 먼저이다

첫번째는 직업이 ANALYST이면서 연봉 2000 이상이 잡히게되고 
직업이 SALESMAN의 결과 값이 나온다 이때 SALESMAN은 연봉과 관련 없이 나온다

두번째는 직업이 SALESMAN과 ANALYST 두 직업 중에 연봉 2천이상만 검색되게됩니다.

---------------------------------------------------------------------------------------

 ◆like 예제. 이름의 첫글자가 S로 시작하는 사원들의 이름과 월급을 출력하시오.

 정 답:
      select ename,sal
       from emp
       where ename like 'S%';

설 명: %는 Wild Card라고 하며 %자리에 무엇이든 올 수 있다는 뜻으로
        S뒤에 뭐가와도 상관없다라는 뜻으로 쓰인다. 
        ename의 S로 시작하는 테이블을 출력.

 ◆like 예제2. 이름의 두번째 철자가 M로 시작하는 사원들의 이름과 월급을 출력하시오.

 정 답:
      select ename
       from emp
       where ename like '_M%';

설 명: _는 Under Bar라고 하며 _자리에 한가지만 무엇이든 올수 있다는 뜻으로
        _자리에 한가지 어떤것이든 상관없다는 뜻으로 쓰인다.


  ◆like 예제3. 이름의 두번째 철자가 %인 사원의 이름을 출력하시오.

 정 답
       select ename
        from emp
        where ename like '_a%%' escape 'a';

 설 명: a다음에 나오는%는 와일드카드가 아닌 특수 문자라는걸 escape가
         문자라고 인식하게 만들어 검색 a뿐만 아니라 어떤 문자로 해도 상관 없다.
         %와 _ 둘다 문자로 인식하게 가능 하게 해주는 명령어

---------------------------------------------------------------------------------------
**ㅁ 엑셀파일을 활용한 테이블 생성 및 테이블 활용**

 소상공인시장진흥공단_상가(상권)정보 파일을 활용하려고 한다 
 공공데이터 포탈에서 상권정보 파일을 다운 받아준후

CREATE TABLE  market_2022                   <<--------market_2022테이블 생성
( "상가업소번호" VARCHAR2(200 BYTE),                             .
"상호명" VARCHAR2(200 BYTE),                                       .
"지점명" VARCHAR2(200 BYTE),                                       .
"상권업종대분류코드" VARCHAR2(200 BYTE), 
"상권업종대분류명" VARCHAR2(200 BYTE), 
"상권업종중분류코드" VARCHAR2(200 BYTE), 
"상권업종중분류명" VARCHAR2(200 BYTE), 
"상권업종소분류코드" VARCHAR2(200 BYTE), 
"상권업종소분류명" VARCHAR2(200 BYTE), 
"표준산업분류코드" VARCHAR2(200 BYTE), 
"표준산업분류명" VARCHAR2(200 BYTE), 
"시도코드" VARCHAR2(200 BYTE), 
"시도명" VARCHAR2(200 BYTE), 
"시군구코드" VARCHAR2(200 BYTE), 
"시군구명" VARCHAR2(200 BYTE), 
"행정동코드" VARCHAR2(200 BYTE), 
"행정동명" VARCHAR2(200 BYTE), 
"법정동코드" VARCHAR2(200 BYTE), 
"법정동명" VARCHAR2(200 BYTE), 
"지번코드" VARCHAR2(200 BYTE), 
"대지구분코드" VARCHAR2(200 BYTE), 
"대지구분명" VARCHAR2(200 BYTE), 
"지번본번지" VARCHAR2(200 BYTE), 
"지번부번지" VARCHAR2(200 BYTE), 
"지번주소" VARCHAR2(200 BYTE), 
"도로명코드" VARCHAR2(200 BYTE), 
"도로명" VARCHAR2(200 BYTE), 
"건물본번지" VARCHAR2(200 BYTE), 
"건물부번지" VARCHAR2(200 BYTE), 
"건물관리번호" VARCHAR2(200 BYTE), 
"건물명" VARCHAR2(200 BYTE), 
"도로명주소" VARCHAR2(200 BYTE), 
"구우편번호" VARCHAR2(200 BYTE), 
"신우편번호" VARCHAR2(200 BYTE), 
"동정보" VARCHAR2(200 BYTE), 
"층정보" VARCHAR2(200 BYTE), 
"호정보" VARCHAR2(200 BYTE), 
"경도" VARCHAR2(200 BYTE), 
"위도" VARCHAR2(200 BYTE)
   ) ;


 테이블이 완성이되면 
![1번 사진](https://user-images.githubusercontent.com/119403834/205190505-6fa4d80b-fc0c-4c04-a30f-9fcffe69fac7.jpg)

사진1 처럼 테이블의 -부분을 클릭해서 확장해주어야 밑에 확장자가 나옵니다.
![2번 사진](https://user-images.githubusercontent.com/119403834/205190525-400065ac-ae4f-4f7d-9f19-e902907f8ac9.jpg)

사진 2 처럼 market_2022를 확인하고 우측 마우스 클릭후 데이터 임포트를 들어가서
              입력할 파일을 선택후 파일 내용이 개진다면 깨지지않도록 해주기 위해서는 
              파일형식의 인코딩을 utf8 or utf-8등으로 인코딩눌러 바꿔주고 깨지는지 확인 후
              다음으로 넘어가 줍니다.
              열 정의에서 일치기준을 위치로 바꿔준후 넘어가서 완료를 진행해 줍니다.
 
select * from market_2022로 입력이잘 들어 갔는지 확인해 줍니다. 

---------------------------------------------------------------------------------------
**문제**

문제61. 우리반에서 성씨가  김씨인 사원들의 이름과 나이를 출력하시오 !

정 답:  
   select ename, age
    from emp16
    where ename like '김%';

문제62. 서울에서 사는 학생들의  이름과 주소를 출력하시오 !

정 답:  
   select ename,  address
    from emp16
    where address like '서울%';

문제63. 통신사가 sk 인 학생들의 이름과 통신사를 출력하시오 ! 

정 답:
   select ename, telecom
    from emp16
    where telecom like 'sk%';

문제64. 이름의 끝글자가 T 로 끝나는 사원들의 이름을 출력하시오 !

정 답:
   select ename
    from emp
    where ename like '%T'; 

설명: 이름의 끝자리가 T 이기만 하면 되고 앞에는 뭐가 와도 관계없다 입니다. 

문제66. 이름의 세번째 철자가  L 인 사원들의 이름을 출력하시오 !

정 답:
   select ename
    from emp
    where ename like '__L%';

문제67. 우리반에서 naver 메일을 사용하는 학생들의 이름과 이메일을 출력하시오!

정 답:   
   select ename, email
    from emp16
    where email like '%naver%';

설명: 이메일에 naver 가 포함되어 있으면 가져와라 !

문제69. 사원 테이블에서 이름의 세번째 철자가 % 인 사원의 이름을 출력하시오 !

정 답:
   select ename
    from emp
    where ename like '__m%' escape  'm';

문제70. 전공이 '경영' 인 학생들의 이름과 전공을 출력하시오 !

정 답:   
   select ename, major
    from emp16
    where major like '%경영%'; 

문제71. (복습문제)  경기도에서 사는 학생들의 이름과 나이와 주소를 출력하는데
        나이가 높은 학생부터 출력하시오 !

정 답:
   select ename, age, address
    from emp16
    where address like '경기도%'
    order by age desc; 

문제72. 경기도에서 살지 않는 학생들의 이름과 나이와 주소를 출력하시오 !

정 답:
   select ename, age, address
    from  emp16
    where address not like '경기도%' ;

설명:  not 을 like 앞에 쓰면 됩니다. 

문제73. 나이대가 30대인 학생들의 이름과 나이를 출력하는데  like 로 검색해보세요.

정 답:
   select ename, age
    from  emp16
    where age like '3%';

문제74. naver 메일을 사용하지 않는 학생들의 이름과 이메일을 출력하시오 !

정 답:
   select ename, email
    from emp16
    where email not like '%naver%'; 

문제75. 우리반 테이블에서 이메일의 끝글자가 .com 으로 끝나지않는 이메일을 갖는
        학생의 이름과 나이와 이메일을 출력하시오 !
정 답:
   select *
    from emp16
    where email not like '%.com';

문제76. 상호명에 스타벅스가 포함되어진 데이터를 검색하는데 모든 컬럼을 다 출력하시오 ! 

정 답:
   select  *
    from market_2022
    where 상호명 like '%스타벅스%';

문제77. 상호명에 '스타벅스' 가 포함되어져 있고 그리고 도로명주소에 '강남구' 가 포함된
        모든 데이터를 출력하시오 !

정 답:
   select  *
    from  market_2022
    where 상호명 like '%스타벅스%' and 도로명주소 like '%강남구%';

문제78. 위의 결과를 다시 출력하는데 상가업소번호가 낮은것부터 높은 순으로 
       출력되게하시오 !
정 답:
   select  *
    from  market_2022
    where 상호명 like %스타벅스%' and 도로명주소 like '%강남구%'
    order by 상가업소번호 asc; 

문제79. 상호명에 '동대문엽기' 를 포함하는 모든 데이터를 출력하시오 !

정 답:
   select  *
    from  market_2022
    where  상호명  like  '%동대문엽기%';

   select  *
    from  market_2022
    where  상호명 like '%동대문%엽기%';

문제80. 서울시에 신전떡볶이 매장이 몇개가 있는지 조회하시오 !

정 답:
   select *
    from  market_2022
    where 상호명 like '%신전떡볶이%';

설명: '공공 데이터 포털'사이트에서 원하는 데이터를 csv 파일로 내려받아서 데이터 베이스에
      테이블로 구성하고 데이터 분석을 할 수 있습니다. 


문제81. 커미션이 null 인 사원들의 이름과 커미션을 출력하시오 !

정 답:
   select ename, comm
    from emp
    where comm = null;

설명: null 값은 알 수 없는 값이기 때문에 이퀄(=) 로 비교 할 수 없습니다. 

정 답:
   select ename, comm
    from emp
    where comm is null ; 

설명: null 값을 검색할 때는 is  null  비교 연산자를 사용해야 합니다. 

문제82.  market_2022 테이블에서 지점명이 null 인 데이터의 모든 데이터를 출력하시오

정 답:
   select  *
    from market_2022
    where 지점명 is null;  

문제83. (복습문제) 서울시에서 스타벅스가 1층 부터  3층까지 쓰는 곳이 어디인지 출력하시오! (층정보 컬럼)

정 답:
    select  *
     from  market_2022
     where  상호명 like '%스타벅스%' and  층정보= 3;

문제84. 사원 테이블에서 커미션이 null 이 아닌 사원들의 이름과 커미션을 출력하시오

정 답:
    select ename, comm
     from emp
     where comm is not null; 

문제85. 관리자의 사원번호(mgr) 이 null 사원의 이름과 직업을 출력하시오 !

정 답:
    select ename,  job
     from emp
     where mgr is null;

문제86. 우리반 테이블에서 나이가 30 살과 33살인 학생들의 이름과 나이를 출력하시오

정 답:
    select ename, age
     from emp16
     where age   in  ( 30,  33 );
또는
    select ename,  age
     from emp16
     where age  = 30 or age = 33; 

문제87. 직업이 SALESMAN, ANALYST 인 사원들의 이름과 직업을 출력하시오 !

정 답:
     select ename, job
       from emp
       where job in('SALESMAN', 'ANALYST');

문제88. 직업이 SALESMAN, ANALYST 가 아닌 사원들의 이름과 직업을 출력하시오

정 답:     
     select ename, job
      from emp
      where job not in ('SALESMAN', 'ANALYST');
 
문제89. 아래의 SQL은 2개의 결과가 출력되고 있습니다. 아래의 SQL을 in  연산자로
        변경해서 수행 하세요 !

정 답:
     select ename, sal, job
      from emp
      where ( job ='SALESMAN' or job='ANALYST') and sal > 2000;

     select ename,  sal,  job
      from emp
      where job in ('SALESMAN', 'ANALYST') and  sal > 2000;

문제90.  아래의 SQL을 SQLD 시험 35회 문제35번 3번 보기처럼 변경해서 수행하시오

정 답:
     select ename, sal, job
      from emp
      where (job ='SALESMAN' or job='ANALYST') and sal > 2000;

     select ename, sal, job
      from emp
      where (job ='SALESMAN' and sal > 2000)  or
             (job ='ANALYST' and sal > 2000) ;

문제91. 우리반에서 나이가 30대이고  통신사가 sk 인 학생들의 이름과 나이와 통신사를 출력하시오 !

정 답:
     select ename, age, telecom 
      from emp16
      where  age like '3%' and telecom like 'sk%'; 

문제92. 아래의 SQL을  and  논리 연산자를 쓴 SQL 로 변경하시오 !

정 답:
     select ename,  age
      from emp16
      where age between 30 and 37;

     select ename,  age
      from emp16
      where age  >= 30   and   age <= 37;

문제93. 상호명에 '도미노피자' 를 포함하고 그리고 지역이 강남인 곳의 모든 데이터를 출력하시오 !

정 답:
     select *
      from market_2022
      where 상호명 like '%도미노피자%' and 시군구명='강남구';

문제94. 상호명에 '피자헛' 을 포함하고 그리고 지역이 강남인 곳의 모든 데이터를
         출력하는데 상가업소번호가 낮은값부터 높은순으로 출력하시오 !

정 답:
     select *
      from market_2022
      where 상호명 like '%피자헛%' and 시군구명='강남구'
      order by 상가업소번호  asc;

문제95. 코로나 이전의 서울의 스타벅스 매장의 갯수는 코로나 이후에 더 늘어났는지
        줄어들었는지 확인하시오 !  

정 답:
     select count(*)
      from market_2019
      where (상호명 like '%스타%벅스%') and (시도명 like '%서울%');

     select count(*)
      from market_2022
      where (상호명 like '%스타%벅스%') and (시도명 like '%서울%');

코로나이전에는 501
코로나 이후에는 463 의매장으로 줄어들었습니다.

#### 오라클 4일차

*** ※ null 값***

null값은 비교 연산자로 비교 할 수 없습니다.
null을 ' '으로 표현 가능하지만 
기타 비교 연산자인 is null or is not null로만 비교가 가능합니다

ex ) <> ''; -> X     /    =  '';  -> X

------------------------------------------------------------------------------------------

***ㅁ 대소문자 변환 함수***

  함수(function)이란 : 
    값을 입력하면 어떤 처리를 해서 결과를 출력해주는 코드의 모음 
    SQL에서 함수를 사용하여 상세하게 데이터를 검색하기위해 필요합니다.

    * upper : 대문자로 작성
      lower  : 소문자로 작성
      initcap : 맨 앞에만 대문자 나머지 소문자로 변환


Ex) 검색조건 where문의 변환을 하면 upper , lower등을 사용 하여 
    테이블안의 데이터를 대문자,소문자 등을 임시로 대,소문자로 변경하여 
    검색조건을 원하는 조건으로 바꿔 줄 수 있음

     select ename,sal
      from emp
      where lower(ename)  = 'scott';  <---테이블안의 ename을 소문자로 검색하여 
                                                    scott으로도 검색가능 이때 ename은
                                                    대소문자가 섞여있을 수도 있다.
                                               (대소문자 모른다는 가정 하에 진행하여 검색 가능)
                                          

-----------------------------------------------------------------------------------------

 ***※몇개인지 카운트해주는 함수 ***
 ■ count = 특정 조건의 개수 확인
 ■ regexp_cout  : reguler_expression(정규 표현식) 함수가 크게 5가지 있는데
                      이 함수는 특정단어가 몇개나 포함되어있는지 확인

-----------------------------------------------------------------------------------------
*** ㅁ 문자에서 특정 철자 추출***
      substr을 활용하여 특정 철자를 잘라 낼 수 있습니다. 
      잘라내기의 활용은 특정 텍스트를 잘라내서 사용가능
      ex) 한달입니다 -> 한달
 
 문 법                              | 
     select substr('smith',1,3)   |  substr을 활용(1번째 철자 위치,선택위치부터 잘라낼 개수)
       from dual;                  |
                           
 결 과 : smi이 출력 됩니다.

     select ename
      from emp16
      where substr(ename,1,) = '김';
     =김씨성인 사람이 출력
-----------------------------------------------------------------------------------------
** ㅁ문자열의 ****길이****를 출력하기 **
   문자의 길이는  length를 통해서 길이의 수를 출력이 가능합니다.
 
        select ename ,email,length(email)
          from emp16
          order by length(email) desc;

        select ename ,email,length(email)
          from emp16
          order by length(email) desc fetch first 1 rows only;

    *fetch first 숫자 rows only 를 order by 절에 사용하면 정렬과정에서 숫자만큼 출력
     이메일중 가장 긴 이메일 순으로 나열 하는데 첫번째에 있는 것만 가져오겠다.

    *instr 활용하기
     instr은 지정한 문자가 몇번째에 있는지 알려주는 문자
   
   Ex) select eamil, instr(email,'@')   <----이메일,이메일안에 @가 몇번째 자리에 있나
       from emp16
 
-----------------------------------------------------------------------------------------
** ㅁ특정 철자를 다른 철자로 변경 **

   ▲REPLACE
     Replace는 특정 철자를 다른 철자로 치환 하는 함수 입니다.
     ex) 병원에서 환자 대기 전공판에 홍O동과 같이 변경해주는 함수
          select ename , replace(ename,'S','K')
              -> 철자를 변경해라(이름중에 모든 S철자를 K로 변경해라)
          select ename,replace(ename,substr(ename,2,1),'o')
             -> 철자를 변경해라(이름중에,2번째부터 2번째까지(1개만)) o로 변경
      
-----------------------------------------------------------------------------------------
** ㅁ특정 철자를  N개만큼 채우기 **

   ▲lpad 
            원하는 철자를 왼쪽에 채워넣는 함수
            lpad(컬럼명,자릿수,채워넣을 키워드)
       
     select ename,lpad(sal,10,'*') <<--
      from emp;

   ▲rpad
           원하는 철자를 오른쪽에 채워넣는 함수
           rpad(컬럼명,자릿수,채워넣을 키워드)

*주의할점은 한글은 1글자에 2byte이기때문에 자릿수에 2자리씩 차지하게됩니다.
-----------------------------------------------------------------------------------------

*** ㅁ특정 철자를  잘라내기 ***

   ▲rtrim : rtrim(컬럼명,오른쪽에 있는 잘라낼 철자나 단어) 
   
   ▲ltrim ltrim(컬럼명,왼쪽에 있는 잘라낼 철자나 단어) 
   
   ▲trim trim(컬럼명)공백제거

-----------------------------------------------------------------------------------------
***◆대소문자 예제◆***
    


예제.   사원 이름을 대문자, 소문자, 첫번째 철자 대문자 나머지 소문자로 출력하시오

 select  upper(ename),  lower(ename), initcap(ename)
   from   emp; 

예제.  사원이름이 scott 인 사원의 이름과 월급을 출력하시오

select  ename,  sal
  from    emp
  where   ename = 'scott'; 

위와 같이 소문자로 검색하면 데이터가 출력 안됩니다. 
SCOTT 을 대문자로 검색해야 합니다. 

select  ename, sal
  from  emp
  where  ename='SCOTT';

emp 테이블이 1억건이 넘는 대용량 테이블이라고 가정하고 테이블의 데이터가
대문자로 되어있는지 소문자로 되어있는지 아니면 대소문자가 섞여있는지 모른다고
가정하고 이름이 scott 인 사원의 이름과 월급을 검색하려면 ?

select  ename, sal
  from  emp
  where  lower(ename)='scott'; 
                    ↓
설명:   emp 테이블의 ename 의 데이터를 전부 소문자로 변경하고  
          그리고서 이름이 scott 인 사원을 조회하겠다.

select  ename, sal
  from  emp
  where  upper(ename)='SCOTT';
                   ↓
   이렇게 하면 ename 의 데이터가 전부 대문자로 변경되므로 SCOTT 을 조회할 때
   대문자로 비교해줘야합니다.             

-----------------------------------------------------------------------------------------
* 함수 concat = ||와 동일하게 붙일 수 있지만 ||와 달리 ' 한글' 을 추가할수 없다.

-----------------------------------------------------------------------------------------
***ㅁ 연설문을 이용한 데이터 활용***

테이블명은 speech로 생성하고 어제와 같은 방법으로 연설문을 다운후
임포트하여 데이터를 테이블에 집어 넣어준다.
create table speech(s_text varchar2(2000)); 문장이 길이때문에 최대한길게 2000바이트로설정

-----------------------------------------------------------------------------------------
문제96. (복습문제)  커미션이 null 이 아닌 사원들의 이름과 월급과 커미션을
              출력하시오 ! 

     select  ename, sal, comm
      from emp
      where comm  is  not  null;

설명: null 값은 비교 연산자로 비교 할 수 없습니다 .기타 비교연산자인 is null 로 
        비교 해야합니다.


문제97.(복습문제)  직업이 SALESMAN, ANALYST 가 아닌 사원들의 이름과 월급과 
             직업을 출력하시오 ! 

     select  ename,  sal, job
      from  emp
      where   job   not   in  ('SALESMAN', 'ANALYST');

                        또는

    select  ename, sal, job
      from   emp
      where   job  !='SALESMAN'   and  job !='ANALYST'; 

설명: 직업이 SALESMAN 이 아니고 그리고 직업이 ANALYST 가 아닌 사원들의 
이름과 월급과 직업을 출력하시오 !

문제98. (복습문제)  부서번호가 10번, 20번인 사원들의 사원이름과 월급과 부서번호를
            출력하시오 ! 

    select   ename, sal, deptno
      from  emp
      where   deptno   in   ( 10, 20 );

             또는  논리 연산자로 수행하면 ?

    select  ename, sal, deptno
      from   emp
      where  deptno   = 10      or      deptno = 20 ; 

문제99.  우리반 테이블에서   임민화 학생의 이메일의 naver 를 대문자로 변경하시오

    update   emp16
     set  email = 'minhwa518@NAVER.com'
     where  ename='임민화';

commit;

    select  ename, email 
     from emp16;

문제100. 우리반에  naver 메일을 사용하는 학생들의 이름과 이메일을 출력하시오!

    select  ename,  email
     from   emp16
     where   lower(email)   like  '%naver%';
                   ↓
설명: email 을 전부 소문자로 변경하고 나서  naver 를 검색합니다.

문제101.   상호명이 GS25   편의점이 서울에 몇개가 있는지 출력하시오 !
               ( 테이블명 : market_2022 에서 조회하세요)

    select  count(*)         <-----  전체 건수 세기 
     from   market_2022
     where   상호명  like  '%GS25%'; 
  
    select  count(*)       
      from   market_2022
      where   lower(상호명)  like  '%gs25%'; 

문제102.  서울에 cu 편의점에 몇개가 있는지 조회하시오 !

    select count(*) 
     from market_2022
     where   lower(상호명) like '%cu%'; 


문제103.  조지부시 전 대통령 연설문에는 today 라는 단어가 몇번 나오는지
                나오는지 확인하시오 !

   select   regexp_count( s_text,  'today'),  s_text 
   from   speech; 

설명:   s_text 에서  'today' 를 행마다 검색해서 'today' 가 몇건 나오는지 확인합니다. 

  reguler   expression  ( 정규 표현식 )  함수가 크게 5가지가 있고 뒤에서 배웁니다.
  정규 표현식 함수중에 regexp_count 함수는 문장에서 특정 단어가 몇개나 포함되었는지
  확인하는 함수입니다. 

   select   regexp_count( lower(s_text) ,  'today'),  s_text 
   from   speech; 


문제104.  우리반에서 이름의 성씨만 출력하시오 !

    select    substr(  ename,  1,  1 )
     from  emp16;	

문제105.  우리반에서 성씨가 김씨인 학생들의 이름을 출력하시오 ! 
              ( like 이용하지 말고 substr 로 하세요 )

     select   ename
      from  emp16
      where  substr( ename, 1, 1 ) ='김'; 

문제107.  위의 결과를 다시 출력하는데 이름의 철자의 갯수가 5개 이상인 사원들만
               출력하시오! 

     select  ename , length(ename)
      from   emp
      where   length(ename) >= 5; 

문제108.  우리반 테이블에서 이메일의 철자의 길이가 가장 긴 학생이 누구이고
               이메일이 어떻게 되는지 출력하시오 !

      select  ename,  email,  length(email)   
       from   emp16
       order  by    3   desc  fetch   first   1  rows  only ;

설명:   fetch  first  숫자  rows  only 를 order by 절에 같이 쓰게 되면 정렬된 결과에서
          위의 행들을 숫자만큼 출력합니다. 

문제109.  서울시에서 상호명이 제일 긴 상호명이 무엇인지 출력하시오 !

     select   상호명,  length(상호명) 
      from   market_2022
      order  by   2  desc   fetch   first  1  rows  only;  

문제110. (복습문제) 우리반 테이블에서 서울에서 살지 않는 학생들의 이름과 이메일과
              전공과 나이를 출력하는데 이름을 ㄱ ㄴ ㄷ ㄹ 순서로 출력하시오

    select  ename, email, major , age 
     from   emp16
     where  address   not   like   '서울%'
     order  by   ename  asc ;

문제111.  우리반 테이블에서 이름을 출력하고 그 옆에 이름에 '진' 이라는 철자가
              몇번째 자리에 있는지 출력하시오 !

     select   ename,  instr( ename,  '진' )
      from   emp16;

문제112.   우리반 테이블에서 이름에 '진' 이 포함된 학생들의 이름을 출력하시오 !
               ( like 사용하지 말고 insert 로 하세요 )

     select   ename,  instr( ename,  '진' )
      from   emp16
      where   instr( ename, '진')  > 0 ;

                        또는

     select   ename,  instr( ename,  '진' )
      from   emp16
      where   instr( ename, '진')  != 0 ;

문제113. (복습문제)   우리반 테이블에서 이메일이 gmail 이 아니고  나이가 30 살 이상인
             학생들의 이름과 나이와 이메일을 출력하시오 !

     select  ename,  age,  email  
      from   emp16
      where   email   not   like  '%gmail%'    and   age >= 30;


-----------------------------------------------------------------------------------------


문제114. (복습문제)  전공에  '경영'  또는  '건축'   또는  '정보' 를 포함하는 
              학생들의 이름과 전공을 출력하시오 ! 

     select  ename,  major 
       from   emp16
       where   major   like  '%경영%'   or
               major  like  '%건축%'    or
               major  like  '%정보%'   ;

문제115. (복습문제)  우리반에서 성씨가 무엇무엇이 있는지 출력하시오 !

      select  distinct  substr(ename, 1, 1 )
       from  emp16;

문제116.  위의 결과를 다시 출력하는데 ㄱ ㄴ ㄷ ㄹ 순서로 정렬해서 출력하시오

      select  distinct  substr(ename, 1, 1 )   as  성씨
       from  emp16
       order  by  성씨 asc;
   
문제117. 위의 결과에서 .net 도 잘라내시오 ! 

     select   rtrim ( rtrim( email,  '.com') , '.net' )  
       from  emp16;

설명:  함수를 위와 같이 중첩 시킬 수 있습니다. 

문제118. 이름이  JACK 인 사원 이름과 월급을 출력하시오 

     select  ename, sal
       from   emp
       where  ename  like  'JACK%'; 

문제119. (오늘의 마지막 문제) 우리반 테이블에서 이메일을 출력하는데 
               도메인만 출력하시오 

                  hanmail
                  naver
                  naver
                     :

문제120. (복습문제)  우리반 테이블을 가지고 다음과 같이 결과를 출력하시오
           ( 연결 연산자 문제) 

최수진 학생은 나이가 31살 입니다
김연재 학생은 나이가 28살 입니다
                  .
                  .
                  .
    
       select  ename || '  학생은 나이가  ' ||  age || ' 입니다'
         from  emp16; 

문제121. (복습문제)  우리반 테이블을 가지고 개인 정보 보호를 위해서 
             이름과 나이를 출력할 때 다음과 같이 출력하시오 !

최수진   30대
김연재   20대
최문정   20대
   :           :

        select ename, substr(age, 1, 1 )  || '0대'
         from  emp16;


문제122.  우리반에서 이름과  주소를 출력하는데  개인정보 보호를 위해서
               주소가 다음과 같이 출력되게 하시오 !

최수진	인천시 ***
   :               :   
심형준	서울시 ***
   :               :

        select ename, substr(address, 1, 3 )  || ' ***'
         from  emp16;

문제123. (복습문제)  직업이 SALESMAN 이 아닌 사원들의 이름과 월급과 
               직업을 출력하는데 월급이 높은 사원부터 출력하시오 ! 

          select   ename,  sal,  job
             from   emp
             where   job  != 'SALESMAN'
             order  by  sal   desc; 

문제124. (복습문제)  월급이 1000 에서 3000 사이인 사원들의 이름과 월급과
              입사일을 출력하는데 최근에 입사한 사원부터 출력하시오 !
             (1000과 3000이 포함되어지게 SQL을 작성하세요)

         select  ename, sal, hiredate
           from   emp
           where   sal  between  1000  and  3000
           order  by   hiredate   desc;

문제125. (복습문제)  이름의 첫글자가 M 으로 시작하는 사원들의 이름과 월급을
               출력하는데 컬럼명을 한글로 이름, 월급으로 출력되게 하시오 !

         select  ename  as "이름" , sal  as "월급" 
           from   emp
           where   ename   like  'M%'; 

문제126.  (복습문제)  market_2022 테이블에서 상호명에 떡볶이가 포함된
              상호명을 출력하시오 !

        select  상호명
         from  market_2022
         where  상호명 like '%떡볶이%';

문제127. (복습문제) 위의 결과를 다시 출력하는데 상호명이 가장 긴것 부터 
             작은순으로 출력되게하시오 !

        select  상호명, length(상호명)
         from  market_2022
          where  상호명 like '%떡볶이%'
          order   by    2 desc; 

문제128. (복습문제)  사원 테이블에서 부서번호가 10번, 30번이 아닌 사원들의
               이름과 월급과 부서번호를 출력하시오 !

        select  ename, sal, deptno
          from   emp
          where   deptno   not   in   ( 10 , 30 ) ; 

문제129.  우리반 테이블에서 이메일을 출력하는데 오른쪽에 있는 .com 을
                잘라내서 출력하시오  !

        select   rtrim( email, '.com' )
           from   emp16;

문제130.  위의 결과에서 .net 도 잘라내시오 !

        select   rtrim ( rtrim( email, '.com' ) , '.net' ) 
           from   emp16;

문제131. 사원 이름을 출력하는데 이름의 첫번째 철자는 대문자로 출력하고
             나머지를 소문자로 출력하시오 !

        select   initcap(ename)
         from emp;

문제132. 위의 결과를 다시 출력하는데  initcap 을 사용하지 않고 
            upper, lower,  substr 과 연결 연산자( || ) 를 사용해서 위의 결과를  출력하시오 !
 
        Jack   
          .
          .
          .
        Miller
        
         select upper(substr(ename,1,1))||lower(substr(ename,2,length(ename)))
          from emp;
#### 오라클 5일차
4일차 전까지 복습 
       오라클 복습문  
     
       기본 select문 select 
                         from
                         where
                         order by
       
        함수  [ upper, lower, initcap, substr, length, instr, replace]


***SQL에서 함수를 사용하는 이유**
    함수를 사용해여 더 상세한 데이터 검색이 가능 하기 때문입니다.

-----------------------------------------------------------------------------------------------


1. 단일행 함수   :    하나의 값 ---> 함수 ---> 하나의 값
2. 복수행 함수  :    여러개의 값 ---> 함수 ---> 하나의 값


    단일행 함수의 종류  문자 함수 : uppper, lower, initcap, substr, instr, replace, length 
                             숫자 함수 : round , trunc,mod,power
                             날짜 함수 : months_between,  add,_months,  next_day,  last_day
                             변환 함수 : to_chr, to_nuber, to_date
                             일반 함수 : nvl, decode, case

    
       ※ round : 반올림해주는 함수로 
                    select round(12.555,2) 
                                    소수 2번째 짜리까지 나타내주고 
                                    3번째에서그전에껄 반올림 ,0을쓰면 1번째에서 반올림
                      1 2   . 5 5 5 
                    [-2.-1.,0,1,2,3]


       ※ trunc : 반올림 하지 않고 반내림 함수
                   select trunc(12.555,2) 
                                   2번째 자리를 기준으로 그전의 숫자 반내림            

       ※ MOD : 나머지 값을 출력합니다.
                   lect mod (10,3) from dual;   --->  결과값 1

       ※ power : 값을 제곱 해주는 함수 입니다.
                     select power(10,5) from dual; --->10의 5제곱

     ※ 날짜 함수 4가지 

     1. months_between  : 날짜와 날짜 사이의 개월수를 출력하는 함수
     2. add_months  :  날짜에서 개월수를 더한 날짜를 출력하는 함수 
     3. next_day  :  특정날짜 뒤에 돌아오는 특정 요일의 날짜를 출력하는 함수
     4. last_day  :  특정 날짜의 달에 마지막 날짜를 출력하는 함수 
                 
       ※ months_between :  날짜 관련된 함수로서,달을 계산할때 사용합니다.
                                   sysdate로 오늘자 확인가능 
                                  select  ename,months_between(sysdate,birth) 
                                  from emp16
                                  where ename = 'aaa';
        
         *select systimestamp, systimestamp + (interval '02:10' hour to minute) from dual;
                     -현재 날짜와 시분초, 현재 날짜와 시분초 +2시간10분
                     ( systimestamp 현재날짜와 시분초 )
                      
          ※ add_months( 특정날짜, 개월수 ):  특정날짜에서 개월수를 더한 날짜 출력
               select   sysdate ,  add_months( sysdate,  100 )
                from dual;
                (현재시간 ,달을 더하다 (현시간에서 ,100개월)

              *interval 년,달,일,시간,분,초를 더하여 늘리수 있다.
               select sysdate + (interval '10'year)  ---> 현재시간 + 10년 
          
                    * interval '숫자' year
                      interval '숫자' month                     
                      interval '숫자' day
                      interval '숫자' hour
                      interval '숫자' minute
                      interval '숫자' second
           
          ※  next_day  : 오늘부터 돌아오는 다음 요일 
                        select sysdate , next_day(sysdate,'금요일') 
                        from dual;
                        현재 날짜부터 다음 금요일은 언제인가.   

          ※  last_day  :  특정 날짜의 달에 마지막 날짜를 출력하는 함수 
                          select sysdate, last_day(sysdate) from dual;
                          (현재 날짜부터 ,마지막날자(현재날짜에서)
                       

      복습행 함수의 종류 count : 숫자
                              max   : 큰 값
                              min   : 작은 값
                              avg   : 평균
                              sum  : 전체 값


-----------------------------------------------------------------------------------------------

     ※ 변환함수 3가지
         1.  to_char : 숫자나 날짜를 문자로 데이터 유형을 변환하는 함수
             to_char (문자,포맷) 그냥 문자란에 넣으면 문자로 변환되어주고 
                                     추가 조건 포맷을 줄수있습니다
               *포맷 : 년도:  RRRR,YYYY,RR,YY
                           월:  MM
                         요일: DAY(요일),Dy(일(월,화),D(일자 1일 2월...)
                           초: SS

         2.  to_number : 문자를 숫자로 형 변환하는 함수
         3.  to_date : 문자를 날짜로 형 변환하는 함수
         
                (to_char)     (to_char)               (to_char)      (to_char)
         숫자       <     -      >           문자         <      -      >       날짜
              (to_number)   (to_char)              (to_date)   (to_char)

         한번에 숫자에서 날짜로 바로 못넘어감
 
  to_char와 extract의 차이
* 기존
         elect  hiredate,  to_char(hiredate, 'RRRR') as 년도,
                                            to_char(hiredate, 'MM')  as  달,
                                            to_char(hiredate, 'DD') as  일
          from   emp;

* NEW
           select hiredate,extract(year from hiredate) as  년도,
                 extract(month from hiredate) 월,
                 extract(day from hiredate) 일
            from emp;

          to_char의 경우에는 입사일을 문자로 변환이 되지만 extract는 숫자로 인식이 된다.

-----------------------------------------------------------------------------------------------

select ename,sal,to_char(sal,'999,999') from emp;

위에서 사용된 포멧은  999,999는 이 자리에 0~9까지 뭐가 와도 상관없다

select ename,sal,to_char(sal,'L999,999') from emp
                           L999,999 L이 로컬 화폐단위를 표시 해줍니다.

select ename,sal,to_char(sal,'$999,999') from emp;
                           $999,999 $표시가 앞에 출력됩니다.
-----------------------------------------------------------------------------------------------
예제 . 지난 토요일 중앙 sunday기사의 데이터를 테이블로 생성하기

create table survey_1 <---테이블명
(s_text varchar2(50),   <---컬럼 명 및 문자형(50바이트)
 s_core number(10,2));  <---컬럼 명 및 숫자형 (10자리 저장, 소숫점 이후 2자리로 저장)

insert into survey_1 values('취업 성공률이 높아서',89.5); <---s_text ,s_core 순으로 넣을순 대로
insert into survey_1 values('유망한 직무여서',59.5);
insert into survey_1 values('인력 수요가 많아서',49.9);
insert into survey_1 values('타직무보다 처우가 좋아서',42.4);

commit;

문제133.  survey_1 테이블에서 s_score 를 출력하시오 !

select  s_score
  from  survey_1;

문제134.  위의 결과를 다시 출력하는데 소수점 이후 첫번째 자리에서 반올림해서
               출력하시오 !

select round( s_score, 0 )
  from  survey_1;

select  round( s_score )
  from  surver_1;

설명:  그냥 0  안쓰고 바로 괄호를 닫게 되면 0 쓴것과 결과가 같습니다. 
-----------------------------------------------------------------------------------------------
ㅁ 달러테이블 생성 하여 데이터 예제풀어보기

https://kr.investing.com/currencies/usd-krw-historical-data 
위의 사이트에서 달러 환율 2022-01 ~ 현재까지 다운 받아서 
테이블 만든 후 임포트

drop table dollar;

create table dollar
(날짜   date,	
종가    number(10,2),
오픈    number(10,2),
고가    number(10,2),	
저가    number(10,2),
거래량  varchar2 (10),	
변동     varchar2 (10) );

select * from dollar;

예제. 문제164. 달러의 종가가 높은것부터 낮은 순으로 모든 컬럼을 출력하는데  상위 10개만
                  출력하시오 ! 
  select *
   from dollar
   order by 종가 desc fetch first 10 row only;
-----------------------------------------------------------------------------------------------

문제
문제135.  중앙일보 선데이 기사 테이블의 s_score 를 일의 자리에서 반올림해서
              출력하시오 ! 

select  s_text, round(s_score,-1)
 from  survey_1;


문제136.  중앙일보 선데이 기사 테이블에서 s_score 를 출력하는데
               소수점 이하는 다 버려버리고 출력되게하시오 !

select  s_text, round(s_score, 0)
 from survey_1;


문제137. (빅데이터 기사문제에 개인정보보호법에 관련한 문제)
              우리반 테이블에서 이름과 나이대를 다음과 같이 출력하시오 !
             ( trunc 를 이용해서)

select ename, trunc(age,-1) || '대'  as 나이대
 from emp16;

최수진	30대
        :
전진현	20대


문제138.  우리반 테이블에서 이름, 나이, 나이를 2로 나눈 나머지값을 출력하시오

select   ename,  age,  mod( age, 2 )
  from  emp16;

문제139. (응용문제)  우리반 테이블에서 이름과 나이를 출력하는데 나이가
                홀수대인 학생들의 이름과 나이를 출력하시오 !

select  ename,  age
 from   emp16
 where  mod( age, 2 ) = 1 ;

문제140. (복습문제) 위의 결과를 다시 출력하는데 ename 을 출력할 때
              ㄱ ㄴ ㄷ ㄹ 순서로 출력하시오 !

select  ename,  age
 from   emp16
 where  mod( age, 2 ) = 1 
 order  by   ename  asc ;

* 숫자함수  :  round, trunc, mod, power

 select  power(2, 3) 
    from  dual;  

문제141.  234의 7승을 출력하시오 !

select  power(234, 7 )
 from dual;

38415899085692544

문제142. (복습문제) 우리반 테이블에서 서울과 경기에서 거주하지 않는 
             학생들의 이름과 주소를 출력하시오 !

select   ename,  address
  from   emp16
  where    address   not   like  '%서울%'   
     and     address   not   like  '%경기%'
     and    address    not  like '%성남%'
     and    address    not  like  '%안산%'; 

▩ 026 날짜 간 개월 수 출력하기(MONTHS_BETWEN)

예제.  오늘날짜를 출력하시오 !

select   sysdate
 from  dual;  

예제. 내가 태어난 날짜부터 오늘까지 총 몇일 살았는지 출력하시오 !

날짜 - 날짜 = 숫자 
날짜 + 숫자 = 날짜
날짜 - 숫자 = 날짜 

select    trunc(sysdate  -  birth, 0 ) 
   from   emp16
  where  ename='최수진';

문제143. 내가 태어난 날짜부터 오늘까지 총 몇주를 살았는지 출력하시오 !

select    trunc(sysdate  -  birth, 0 )  /  7  
   from   emp16
  where  ename='최수진';

또는

select   sysdate  -  to_date('90/05/19', 'RR/MM/DD')
   from dual; 

설명: 변환함수인 to_date 배울때 설명 드리겠습니다. 

select  sysdate -  '90/05/19'
  from  dual; 

설명: 이렇게 하면 에러가 나는데 에러가 나는 이유는   ?
  
       sysdate   -  '90/05/19' 

          날짜 - 문자  로 수행되었습니다. 날짜 - 날짜는 가능한데 
          날짜 - 문자는 수행되지 않습니다.
          그래서 다음과 같이 문자를 날짜로 변환해줘야합니다.  

select   sysdate  -  to_date('90/05/19', 'RR/MM/DD')
   from dual; 

문제144. 내가 태어난 날짜부터 오늘까지 총 몇 달 살았는지 출력하시오 !

select    trunc(sysdate  -  birth, 0 )  /   ?   <----- 이 자리에 어떤 숫자 ? 
   from   emp16
  where  ename='최수진';

위의 SQL로 작성할 수 는 없고 오라클에서 정확하게 개월수를 출력할 수 있도록
함수를 제공합니다.  그게 바로 months_between 입니다. 

select   ename, months_between( sysdate,  birth )
         from   emp16
        where  ename='최수진';

최수진	366.8870120221027479091995221027479092

설명:  months_between( 최신날짜, 예전날짜 )  의 결과는 두 날짜 사이의 
         개월수가 출력됨 

문제145.  위의 결과를 다음과 같이 출력되게하시오 !  (trunc 로 소수점이후는 
                지우세요)

 최수진   366개월 

select   ename,   trunc( months_between( sysdate,  birth ), 0 )  || '개월'
         from   emp16
        where  ename='최수진';

문제146.   사원 테이블에서 이름과 해당 사원이 입사한 날짜부터 오늘까지
                  총 근무한 개월수를 출력하시오 !

select   ename,  trunc( months_between( sysdate,  hiredate ), 0 ) 
        from   emp; 

문제147. 위의 결과를 다시 출력하는데 그동안 임금이 동결되었다고 하고
               이름을 출력하고 그 옆에 그동안 받은 총 급여를 출력하시오 !
               ( 입사한 날짜 부터 지금까지 받은 총 급여 )

select   ename,  trunc( months_between( sysdate,  hiredate ), 0 )  * sal 
        from   emp; 

문제148.  위의 결과를 다시 출력하는데 아래와 같이 문자열로 출력하시오 !

KING 이 그동안 받은 총 급여는 2460000 입니다.  
BLAKE 이 그동안 받은 총 급여는 1422150 입니다. 
                       :

정 답:
select   ename || '이 그동안 받은 총 급여는 ' || 
          trunc( months_between( sysdate,  hiredate ), 0 )  * sal   || ' 입니다.'
        from   emp; 

문제149. 중앙일보 선데이 기사 테이블을 다음과 같이 결과로 출력하시오 ! 

IT 비전공생에게 물었더니     <--------------- 컬럼명 
취업 성공률이 높아서	89.5%
유망한 직무여서	               59.5%
인력 수요가 많아서	               49.9%
타직무보다 처우가 좋아서	42.4%


select S_ TEXT || S_SCORE || '%' || as "IT 비전공생에게 물었더니"
from SURVEY_1

문제150.   오늘날짜에서 10년뒤의 날짜를 출력하시오 !

select   add_months( sysdate, 120 )
  from  dual; 

설명:  add_year 라는 함수는 없습니다.  그래서 위의 SQL 처럼 add_months 를
         이용했습니다. 

select   sysdate  +  ( interval  '10'  year )
    from  dual;

select systimestamp ,  systimestamp + ( interval  '02:10'  hour  to minute )
    from  dual;

설명: 현재 날짜와 시분초, 현재 날짜와 시분초 + 2시간 10분 

22/12/05 13:58:26.123000000 +09:00	22/12/05 16:08:26.123000000 +09:00


문제151. 오늘부터 바로 돌아오는 월요일의 날짜를 출력하시오 !

select   sysdate,  next_day( sysdate, '월요일')
 from dual; 

22/12/05	  22/12/12

문제152. (난이도 중) 함수는 함수안에 중첩해서 사용할 수 있다는것을 생각하고
             문제를 풀어주세요.  오늘부터 100 달 뒤에 돌아오는 월요일의 날짜를
             출력하시오 !

select    next_day( add_months( sysdate, 100 ) ,  '월요일' )
  from  dual; 

설명: 함수는 위와 같이 중첩될 수 있습니다. 

문제153. (복습문제) 직업이 SALESMAN 이 아닌 사원이고 월급이 1200 이상인
               사원들의 이름과 월급과 직업을 출력하는데 월급이 높은 사원부터
               출력하시오 !

select  ename, sal, job
  from   emp
  where   job !='SALESMAN'   and  sal >= 1200 
  order   by  sal   desc; 


문제154.   다음과 같이 결과를 출력하시오 !

 오늘 부터 요번 달 말일까지는 총 26 일이 남았습니다.  

select  '오늘 부터 요번 달 말일까지는 총 ' ||  to_char( last_day(sysdate) - sysdate ) 
           ||  ' 일이 남았습니다. '
  from   dual; 

설명:   '오늘 부터 요번달 말일까지는 총'  은 '문자' 입니다.
        문자 + 숫자로 연결할 수 없어서 to_char 를 사용하지 않으면 에러가 납니다. 
         to_char 는 '문자' 로 데이터 유형을 변환하는 변환함수입니다. 
          to_char쓰지 않아도 나오긴하지만 이는 오라클내부에서 암시적 형변환으로
          자동변환으로 변환되어서 이기 때문입니다.

문제155.  사원 테이블에서 1981년도에 입사한 사원들의 이름과 입사일을
               출력하시오 ( between..and 와 like 사용하지 말고 to_char 로 하세요)

 select  ename, hiredate
  from  emp
  where  to_char(hiredate,'RRRR') = '1981'; 

설명:  to_char(hiredate, 'RRRR') 이 입사일에서 년도만 4자리로 추출합니다.
        그래서 = (이퀄) 하고 1981을 써주고 비교하면 되는데 양쪽에 싱글 쿼테이션
         마크를 둘러줘야 합니다. 

 select  ename, hiredate
  from  emp
  where  to_char(hiredate,'RRRR') = 1981; 
                        문자형               =       숫자형
                                                 ↓
                         문자형              =  문자형 

  where  to_char(hiredate,'RRRR') = '1981' ; 

문제156.  우리반 테이블에서 이름, 생일, 태어난 요일을 출력하시오 !

select  ename,  birth,  to_char( birth, 'day')
  from   emp16; 

문제157.  위의 결과를 다시 출력하는데 월요일에 태어난 학생들만 출력하시오!

select  ename,  birth, to_char(birth, 'day')
  from  emp16
  where  to_char( birth, 'day') ='월요일';

설명:  요일을 출력하는 포멧이  3가지가 있습니다. day,  dy,  d  입니다.

select  ename, to_char(birth, 'day'), to_char(birth, 'dy'), to_char( birth, 'd')
   from   emp16;

최수진	토요일	토	7
                    :
심형준	수요일	수	4

문제158. (난이도 상) 이름,  태어난 요일을 출력하는데 태어난 요일이
               월화수목금토일 순으로 정렬해서 출력하시오 !
select ename,birth,to_char(birth,'dy') 
 from emp16  
 order by replace(to_char(birth,'d'),'1','8') asc;
                           
                             OR

select ename,birth,to_char(birth,'dy') 
 from emp16  
 order by to_char(birth-1,'d') asc;
                           


문제159.  우리반에서 태어난 요일이  월요일, 토요일, 금요일인 학생들의 
               이름과 생일과 태어난 요일을 출력하시오 !

select  ename, birth,  to_char(birth, 'day')
   from  emp16
   where  to_char(birth, 'day')  in  ( '월요일', '토요일', '금요일');


문제160.  이름, 월급 * 12000000  을  출력하는데 금액 단위를 읽기 편하도록
                출력하시오 !

select  ename, to_char( sal*12000000,  '999,999,999,999') 
   from  emp;

설명:  출력되는 자리의 갯수가 포멧에서 충분히 길어야합니다. 안그러면
           # 이 출력됩니다. 

select  ename, to_char( sal*12000000,  '999,999') 
   from  emp;

문제161.  문제160번 SQL 을 다시 수행하는데  출력되는 2번째 컬럼의 값이
               큰 것부터 출력하시오 ! 

select  ename, to_char( sal*12000000,  '999,999,999,999') 
   from  emp
   order  by   2  desc; 

문제162.  이자율이 4% 일때 3억을 예금했을때 받을 수 있는 이자 금액을 
               출력하시오 ! 

select   to_char( 300000000 * 0.04, 'L999,999,999')
  from dual;
                                             
문제163. (복습문제)  이름의 두번째 철자가 M 인 사원의 이름과 월급을 출력
            하는데 월급을 출력할 때 단위를 표시하시오 !

select  ename, to_char( sal, '$999,999')
  from  emp
  where  ename  like  '_M%';  




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
