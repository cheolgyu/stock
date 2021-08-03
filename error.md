
select pg_size_pretty(pg_total_relation_size('hist.price_2021'));
--select pg_size_pretty(pg_database_size('dev_remove'));

---------------------------------------------------------------------=
public.after_closing() 오래걸림.
여러 개 합쳐진거 각자  실행해서 어디가 느린지 찾기.
월별 거래량 합계 에서 느린 경우 년도별로 거래량 미리 구해서 저장해놓기
    변하는건 마지막 2달의 거래량만 계산하기 

---------------------------------------------------------------------=
060150 인선이엔티
반등정보의 x2값이  종가가가 마지막 거래일이 아님. 시가는 마지막 거래일인데


SELECT *
FROM 
HIST.price 
where 
code_id = (select id from meta.code where code='060150')
order by dt desc limit 10
;
select *
from hist.rebound 
where code_id = (select id from meta.code where code='060150')
and price_type=3
order by x1 desc 



---------------------------------------------------------------------=
완료 order by dt asc 안써서 

2021/08/02 05:34:16 reBoundHandler.go:44:  BoundHandler  start
2021/08/02 05:36:31 ReBoundDao.go:72: 쿼리:stmt.Exec 오류:  2573 3

func InsertHistReBound(code_id int, price_type_id int, list []model.Point, upsert bool) error {
hist rebound 계산 함수 잘못된듯.

q_last_rebound 에서 order by dt asc  안써서 그런듯 확인필요.




---------------------------------------------------------------------=
2021/08/02 03:21:35 CompanyDao.go:105: 쿼리:InsertPublicTbCode:stmt.Exec 오류: 
2021/08/02 03:21:35 CompanyDao.go:106: 쿼리:InsertPublicTbCode:stmt.Exec 오류:  {Code_id:2501 Code:353190 Name:휴럼 Code_type:1 Market_type:8 Stop:false}

2021/08/02 03:21:35 CompanyDao.go:107: pq: duplicate key value violates unique constraint "company_name_key"
    INSERT INTO public.company(
        code_id, code, name, code_type, market_type, stop)
        VALUES (2501, '353190', '휴럼', 1, 8, false)
        ON CONFLICT (code_id) DO UPDATE 
        SET code_id=2501, code='353190', name='휴럼', code_type=1, market_type=8, stop=false


    -- {Code_id:2501 Code:353190 Name:휴럼 Code_type:1 Market_type:8 Stop:false}	


완료==확인필요====hists.rebound 중복데이터 있음.
    SELECT count(*) OVER() AS full_count,*
            from hist.rebound
            WHERE 1 = 1
            and code_id = (select id from meta.code where code= '003580')
            and price_type ='5'
            and x1 = 20210712
            limit 10 OFFSET 0
    1. 테이블 삭제
    2. 마이그레이션
    3. wriitte. rebound 실행
    문제없음.
    한번더 실행해보기
    3. 재실행 해도 문제없음.
    4. 3. 재실행 해도 문제없음.



완료== company.rebound 조회시 Sort = "" 

완료==확인필요====view_monthly_peek 데이터 이상함 

    SELECT *
    from public.view_monthly_peek
    WHERE 1 = 1
            and market_type in (  7  , 8  , 9  )  order by  peek_percent  desc limit 30 OFFSET 0
    
    1. is_peek is true 추가

site 피크월-> 상세페이지 -> 뒤로가기 누르면 오류뜸.

        
        
        