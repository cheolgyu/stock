```
docker --version
Docker version 20.10.4, build d3cb89e

docker-compose --version
docker-compose version 1.29.2, build 5becea4c
```
---
## 배포 
### ssh접속
   1. ssh -i "highserpot_stock.pem" ec2-user@3.37.161.89
### 준비
    ```
    cd  C:\Users\cheolgyu\go\github.com\cheolgyu\stock
    scp -i   "highserpot_stock.pem" -r ./* ec2-user@3.37.161.89:~/stock/

    이미지 만들기. cd img
    Docker save -o stock-write-ticker.tar stock-write-ticker:latest
    Docker save -o stock-read-pub-api.tar stock-read-pub-api:latest
    Docker save -o postgres-13-3-alpine.tar postgres:13.3-alpine
    Docker save -o stock-caddy-latest.tar stock-caddy:latest
    Docker save -o amir20-dozzle-latest.tar amir20/dozzle:latest

    업로드
    scp -i "../highserpot_stock.pem" -r ./* ec2-user@3.37.161.89:~/stock/img
    

    cd stock/img/

    docker load -i  stock-write-ticker.tar
    docker load -i  stock-read-pub-api.tar
    docker load -i  postgres-13-3-alpine.tar
    docker load -i  stock-caddy-latest.tar
    docker load -i  amir20-dozzle-latest.tar

    cd ../ && docker-compose up -d 
    
    upload ec2 dump_file 
        C:\Users\cheolgyu\Desktop\backup\dumpFile
        scp -i   "highserpot_stock.pem" ./dump_2021-06-29.sql ec2-user@3.37.161.89:~/stock/dump_2021-06-29.sql
        100%  244MB   7.4MB/s
        scp -i   "highserpot_stock.pem" ./dump_2021-06-30.sql ec2-user@3.37.161.89:~/stock/dump_2021-06-30.sql
    
    upload container dump_file
        docker cp  ~/stock/dump_2021-06-29.sql stock-db:/var/lib/postgresql/dump_2021-06-29.sql
        docker cp  ~/stock/dump_2021-06-30.sql stock-db:/var/lib/postgresql/dump_2021-06-30.sql

    container db 접속
         docker exec -it stock-db /bin/sh
         su - postgres
         psql --dbname dev  < /var/lib/postgresql/dump_2021-06-29.sql
         psql --dbname dev  < /var/lib/postgresql/dump_2021-06-30.sql
         
    

    

    chmod +x dbment 
    ```
    ec2 업로드 전 기존 프로세스 kill -9 p_id 시키기.

---
```
배포- site
    append .vscode/task.json 
    업로드-site
        scp -i "./highserpot_stock.pem" -r ../stock-read-pub-site/dist ec2-user@3.37.161.89:~/stock/caddy/site
```
```
배포- api
    Docker save -o stock-read-pub-api.tar stock-read-pub-api:latest
    Docker save -o stock-write-ticker.tar stock-write-ticker:latest

    scp -i "../highserpot_stock.pem"  ./stock-read-pub-api.tar ec2-user@3.37.161.89:~/stock/img
    scp -i "../highserpot_stock.pem"  ./stock-write-ticker.tar ec2-user@3.37.161.89:~/stock/img

    

    docker-compose stop stock-api
    docker-compose stop stock-write-ticker

    
    docker-compose rm -f stock-api
    docker-compose rm -f stock-write-ticker

    docker rmi -f stock-read-pub-api:latest
    docker rmi -f stock-write-ticker:latest

    docker load -i  stock-read-pub-api.tar
    docker load -i  stock-write-ticker.tar
    
    docker load -i  stock-write-ticker.tar
    docker-compose up -d
```

### ec2 업로드
1. cd C:\Users\cheolgyu\go\github.com\cheolgyu\stock\
   1. dbment
      1. scp -i "highserpot_stock.pem" .env.prod  ec2-user@3.35.30.100:~/.env.prod
      2. scp -i "highserpot_stock.pem" bin/dbment  ec2-user@3.35.30.100:~/dbment

### ec2 실행 명령어
1. ssh 접속
   1. dbment
      1. ticker에서 실행시킴
      2. 수동 
         1. ./dbment -run=daily -prod

---
### postgres 백업
1. export
    ```
    postgres container 
    su - postgres
    pg_dump dev -n public -n company -n hist -n utils -E utf-8 > dump_2021-07-09.sql
    pg_dump dev -t hist.bound_market -t hist.bound_stock  -E utf-8 > dump_2021-06-30.sql
    pg_dump dev  -E utf-8 > dump_2021-08-18.sql

    window 
    docker cp stock-write_db_1:/var/lib/postgresql/dump_2021-07-09.sql C://Users//cheolgyu//Desktop//backup//dumpFile//dump_2021-07-09.sql
    docker cp stock-write_db_1:/var/lib/postgresql/dump_2021-06-30.sql C://Users//cheolgyu//Desktop//backup//dumpFile//dump_2021-06-30.sql
    docker cp stock-write_db_1:/var/lib/postgresql/dump_2021-08-18-2.sql C://Users//cheolgyu//Desktop//backup//dumpFile//dump_2021-08-18.sql
    

    ```
2. import
    ```
    su - postgres
    psql --dbname prod --host database-stock-1.czunxjjslnrd.ap-northeast-2.rds.amazonaws.com --port 5432 --username postgres < dumpFile.sql    
    psql --dbname dev  < ./data/dumpFile/2021-06-17/public.dumpFile.sql
    psql --dbname dev  < ./data/dumpFile/2021-06-17/company.dumpFile.sql
    psql --dbname dev  < ./data/dumpFile/2021-06-17/utils.dumpFile.sql
    psql --dbname dev  < ./data/dumpFile/2021-06-17/market.dumpFile.sql
    psql --dbname dev  < ./data/dumpFile/2021-06-17/price.dumpFile.sql
    psql --dbname dev  < ./data/dumpFile/2021-06-17/bound.dumpFile.sql
    

    window 
    docker cp ./dump_2021-08-18.sql stock-write_db_1:/var/lib/postgresql/dump_2021-08-18.sql
    psql --dbname dev  < /var/lib/postgresql/dump_2021-08-18.sql
    ```

---