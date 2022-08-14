// 보호 시작당시, 젊은 동물들을 대상으로 한 ANIMAL_INS목록을 작성하세요
SELECT ANIMAL_ID, NAME
FROM ANIMAL_INS
WHERE INTAKE_CONDITION != 'Aged'
GROUP BY ANIMAL_ID;

// 조건이 2개(이름이 같다면 보호를 나중에 시작한 순으로 출력) 
SELECT ANIMAL_ID, NAME, DATETIME
FROM ANIMAL_INS
ORDER BY NAME, DATETIME DESC; 

// 조건문 (부속질의) - MIN
SELECT NAME
FROM ANIMAL_INS
WHERE DATETIME = (SELECT MIN(DATETIME) FROM ANIMAL_INS);

// 조건문(부속질의 ) - MAX 
SELECT DATETIME
FROM ANIMAL_INS
WHERE DATETIME
 = (SELECT MAX(DATETIME) FROM ANIMAL_INS);

// 해당하는 조건의 숫자 세기 - COUNT , AS
SELECT COUNT(*) AS COUNT FROM ANIMAL_INS;

// 중복되는 셀을 제외한 숫자 구하기 - DISTINCT / GROUP BY (정렬기능 추가) /AS
// DISTINCT - SELECT ((count)) DISTINCT {컬럼명} FROM {테이블명} {조건절}
// GROUP BY - SELECT ((COUNT)) {컬럼명} FROM {테이블명} {조건절} GROUP BY {테이블명}
// SQL AS 사용 : 필드명이나 테이블의 이름을 다시 지을 때 씀
sol1. GROUP_BY
SELECT COUNT (a.name) FROM (SELECT name FROM ANIMAL_INS GROUP BY name) a 

sol2. 서브쿼리 이용 
SELECT COUNT(DISTINCT(NAME)) AS 'COUNT'
FROM ANIMAL_INS
WHERE NAME IS NOT NULL

** mysol. 오답 : 
// COUNT사용 시, null은 비교연산으로 비교할 수 없음 
// COUNT는 NULL을 포함(GROUP 함수일때)
// null값을 제외하고 구하고자 한다면 COUNT(NAME)을 통해 NULL값 제외
SELECT COUNT (DISTINCT NAME) FROM ANIMAL_INS


// COUNT절, 여러 필드의 개수 구하기 - GTOUP BY / ORDER BY 차이 
// GROUP BY - 데이터를 특정 컬럼 기준으로 그룹화 (그룹을 나누고 세부그룹 나누기 가능, 다양한 함수 사용 가능, SELECT절에도 있어야 함)
// ORDER BY - 테이블을 특정 컬럼값 기준으로 정렬(ASC, DESC)
// HAVING - GROUP BY 절에 대한 조건을 걸고 싶을 때 사용하는 명령어, 그룹화된 컬럼에 조건 걸기 
// WHERE - 처음부터 테이블 자체에 조건을 걸고 싶을 때 사용 
sol1 .
SELECT ANIMAL_TYPE, COUNT(ANIMAL_TYPE)
FROM ANIMAL_INS
GROUP BY ANIMAL_TYPE
ORDER BY ANIMAL_TYPE ASC

sol2. 
SELECT ANIMAL_TYPE, COUNT(ANIMAL_TYPE)
FROM ANIMAL_INS
WHERE ANIMAL_TYPE = 'Cat' OR ANIMAL_TYPE = 'Dog'
GROUP BY ANIMAL_TYPE
ORDER BY ANIMAL_TYPE;

** mysql 
SELECT ANIMAL_TYPE, COUNT(ANIMAL_TYPE)
FROM ANIMAL_INS
WHERE ANIMAL_TYPE = 'Cat' OR 'Dog';

// 두 번 이상 이름이 쓰인 이름 AND 이름이 쓰인 횟수를 조회 - HAVING
SELECT NAME,COUNT(NAME)
FROM ANIMAL_INS 
GROUP BY NAME 
HAVING COUNT(NAME) >= 2
ORDER BY NAME

// 입양 시각 구하기 - HOUR, BETWEEN AND 
// MY SQL. 시간에 대한 조건 넣는 방법 몰랐음(HOUR, BETWEEN AND)
SELECT HOUR(DATETIME), COUNT(DATETIME) 
FROM ANIMAL_OUTS
WHERE HOUR(DATETIME) BETWEEN 9 AND 19
GROUP BY HOUR(DATETIME)
ORDER BY HOUR(DATETIME) ASC 

// 입양 시각 구하기 - WITH, RECURSIVE, UNION, LEFT JOIN ON
// with recursive 로 임시 테이블 생성 
// UNION 조회한 다수의 select 문을 합칠 때 (중복되는 행은 하나만)
// UNION ALL이 중복제거 없이 모두 합쳐서 보여줌
// LEFT JOIN ~ ON 왼쪽 테이블 모두 + 오른쪽 테이블 일치하는 것만

WITH RECURSIVE TEMP AS( # 재귀쿼리 세팅
    SELECT 0 AS HOUR # 초기값 설정
    UNION ALL # 위, 아래 쿼리 값 연산
    SELECT HOUR + 1 FROM TEMP # HOUR를 하나씩 증가시킴
    WHERE HOUR < 23 # 반복 멈추는 조건
    )

//LEFT JOIN할 테이블인 ANIMAL_OUTS테이블을 불러옴
//TEMP HOUR 과 COUNT HOUR을 SELECT로 사용  
SELECT TEMP.HOUR, COUNT(HOUR(ANIMAL_OUTS.DATETIME)) AS COUNT

FROM TEMP
LEFT JOIN ANIMAL_OUTS 
ON TEMP.HOUR = HOUR(ANIMAL_OUTS.DATETIME)

//GROUP BY 없을 경우, COUNT가 전체 카운트가 되어버림
GROUP BY TEMP.HOUR
ORDER BY TEMP.HOUR ASC;

// ++ ORDER BY 1에 대해 
ORDER by 1 (테이블의 '컬럼(COLUMN)' 순서번호) 




