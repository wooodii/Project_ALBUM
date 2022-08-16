// 있었는데요 없었습니다 - JOIN, AS, 날짜비교
//as = Alias 별칭 
// INS, OUTS 두필드에 공통적으로 있는 ANIMAL_ID를 토대로 필드를 연결해준다. 
SELECT O.ANIMAL_ID, O.NAME
FROM ANIMAL_OUTS AS O 
LEFT JOIN ANIMAL_INS AS I
ON O.ANIMAL_ID = I.ANIMAL_ID
WHERE I.DATETIME > O.DATETIME
ORDER BY I.DATETIME;

// 오랜 기간 보호한 동물(아직 입양을 가지 못한) - 조건 NULL, order 2정렬 
select ins.name, ins.datetime
from animal_ins as ins
    left outer join animal_outs as outs
    on ins.animal_id = outs.animal_id
where outs.animal_id is null
order by ins.datetime limit 3

SELECT I.NAME, I.DATETIME
FROM ANIMAL_INS I LEFT JOIN ANIMAL_OUTS O ON I.ANIMAL_ID = O.ANIMAL_ID
WHERE O.ANIMAL_ID IS NULL
ORDER BY 2 
// 테이블의 2번째 열 기준으로 정렬
LIMIT 3;

//조건 like 를 이용한 입소 후 중성화된 동물 리스트 만들기 

// sol.1오답
select
 ANIMAL_ID
,ANIMAL_TYPE
,NAME
from ANIMAL_OUTS 
where (SEX_UPON_OUTCOME like 'Spayed%' or SEX_UPON_OUTCOME like 'Neutered%')
and ANIMAL_ID in (
select ANIMAL_ID
from ANIMAL_INS 
where SEX_UPON_INTAKE like 'Intact%'

//sol2.정답 
// join using - 두 테이블간 필드 이름이 같은 경우 사용가능, 중복필드는 한번만 가져옴(animal_id)
// join on - 두 테이블간 조인해야 하는 필드 이름이 다른 경우 사용(각각의 필드를 가져와서 중복이 생성)
SELECT i.ANIMAL_ID, i.ANIMAL_TYPE,i.NAME
FROM ANIMAL_INS i JOIN ANIMAL_OUTS o USING (ANIMAL_ID)
WHERE i.SEX_UPON_INTAKE != o.SEX_UPON_OUTCOME
ORDER BY i.ANIMAL_ID