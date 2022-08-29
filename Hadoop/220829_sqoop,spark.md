# HIVE example
### 파일질라로 파일 올리기(아파트 실거래 파일)
![image](https://user-images.githubusercontent.com/85923524/187101853-1aeaf196-4521-406f-b562-ff56993cfbc1.png)
### 현재 ec2 user에 파일을 올렸으니 hadoop계정유저로 권한 바꾸고 폴더안에 있는 파일들도 권한 바꾸기
-  sudo chown -R hadoop:hadoop ./apt
### hdfs에 폴더 /apt 폴더 만들기
- hdfs dfs -mkdir /apt
### 업로드 한 파일 put하기
- hdfs dfs -put ./apt/* /apt 
- hdfs dfs -ls /apt/ (파일 확인)
### HIVE 켜기, 쿼리날리기
-데이터 형태

![image](https://user-images.githubusercontent.com/85923524/187102952-3c770d3f-f413-49fb-ba0b-0920c71c6d32.png)
- hive
```
CREATE EXTERNAL TABLE IF NOT EXISTS apt (
sigungu STRING,
bunji   STRING,
MainNumber STRING,
PartNumber STRING,
complex   STRING,
area      FLOAT,
con_month STRING,
con_day   STRING,
amount    INT,
apt_floor STRING,
con_year  STRING, 
road_name STRING)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
   "separatorChar" = ",",
   "quoteChar"     = "\""
)  
LOCATION '/apt'
tblproperties ("skip.header.line.count"="16"); #16라인까지는 스킵
```
- 완료 화면 및 데이터 확인
![image](https://user-images.githubusercontent.com/85923524/187103069-71665763-6d43-449e-ac30-9be0490da3c4.png)
> 한글이 깨지는 현상이 일어났음.
> python3으로 읽어드리자(설치가 안되어있다면 sudo yum install python3)
- vim ~/trans.py
```
import os 

def trans_encoding(dirs):
    with open("{}.txt".format(dirs), "w", encoding='utf-8') as trans:
        with open("{}".format(dirs), "r", encoding='euc-kr') as f:
            for line in f:
                trans.write(line)

for roots, dirs, files in os.walk("/home/hadoop/apt"):
    for file in files:
        trans_encoding(roots + '/' + file)

```
> with open("{}".format(dirs), "r", encoding='euc-kr') as f: -> euc-kr로 인코딩된것을 라인을 읽어 드려서    
>  with open("{}.txt".format(dirs), "w", encoding='utf-8') as trans: ->utf-8로 쓴다.   
>  for roots, dirs, files in os.walk("/home/hadoop/apt"):
	    for file in files:
	        trans_encoding(roots + '/' + file) home에 hadoop에 apt폴더안 파일을 읽어드리기 
        
- python3 trans.py 실행
- head -n 20 파일명 
- tail -n 20 파일명 
### 다시 만든 폴더를 다시 하둡에 올리기
- hdfs dfs -mkdir /apt2
- hdfs dfs -put ./*.txt /apt2
- hdfs dfs -ls /apt2
### 다시 hive 쿼리에 보내기
```
CREATE EXTERNAL TABLE IF NOT EXISTS apt2 (
sigungu STRING,
bunji   STRING,
MainNumber STRING,
PartNumber STRING,
complex   STRING,
area      FLOAT,
con_month STRING,
con_day   STRING,
amount    INT,
apt_floor STRING,
con_year  STRING, 
road_name STRING)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
   "separatorChar" = ",",
   "quoteChar"     = "\""
)  
LOCATION '/apt2' #위치 변경
tblproperties ("skip.header.line.count"="16");

```

### 현재 테이블 amount, apt_floor가 컬럼이 스위치 되어있음
![image](https://user-images.githubusercontent.com/85923524/187106619-190101ce-4a17-4405-84b8-3443d8b933ba.png)
- ALTER TABLE apt2 CHANGE apt_floor money STRING;
## 집계함수로 활용해서 데이터 탐색

### 전월세 포함해서 제일 비싼 집의 금액 찾기
- select max(CAST(regexp_replace(money, ',', '') AS INT)) from apt2;
	- money에서 ,로 되어있는 것을 없애주고, int형으로 형변환을 한 값의 가장 큰 값을 알려줘
- select * from apt2 where CAST(regexp_replace(money, ',' , '') AS INT) = (select max(CAST(regexp_replace(money, ',' , '') AS INT)) from apt2);
	- 가장 비싼 곳의 정보를 조회해줘
### selenium을 이용해서 현재 사용하는 데이터를 가져오는 과정 맡기기
```
import selenium
from selenium import webdriver

# warning off 
import warnings
warnings.filterwarnings('ignore')

options = webdriver.ChromeOptions()
options.add_argument('headless') # 창 안띠우게 하는 방법

#driver =  webdriver.Chrome("./chromedriver.exe" , options=options)
driver =  webdriver.Chrome("./chromedriver.exe")

driver.get("http://rtdown.molit.go.kr/")

driver.window_handles #팝업창을 포함한 3개 창이 뜸

driver.switch_to.window(window_name = driver.window_handles[0]) # 페이지 3개중 메인페이지만 포커싱

driver.page_source # 소스코드 보기
```
- 마지막 코드 실행 출력화면
![image](https://user-images.githubusercontent.com/85923524/187111153-9853ea6c-73b6-4401-b149-054ab26c2205.png)
> 문제 지금 소스코드가 제대로 안보임 막아 놓았음   
>  실제로의 url주소를 가져와야함. -> driver.get("http://rtdown.molit.go.kr/countLog.do") 로 변경

#### html 셀렉터 함수로 계약일자 숫자 넣기
```
#셀렉터 함수 사용하기
from selenium.webdriver.common.by import By
driver.find_element(By.CSS_SELECTOR, "#searchFromDt").clear()

driver.find_element(By.CSS_SELECTOR, "#searchFromDt").send_keys("20220101")

driver.find_element(By.CSS_SELECTOR, "#searchToDt").clear()

driver.find_element(By.CSS_SELECTOR, "#searchToDt").send_keys("20220131")

#파일구분에서 csv로 바꾸기
driver.find_element(By.CSS_SELECTOR, "#fileType > option:nth-child(2)").click()

# 조회클릭하기
driver.find_element(By.CSS_SELECTOR, "#listForm > div > table:nth-child(2) > tbody > tr:nth-child(2) > td > a > img").click()

```

- 날짜생성함수 만들어서 월별로 가져오기 
```
#날짜생성함수 만들어서 월별로 가져오기 
from datetime import datetime, date
import calendar
import time 
for month in range(1, 8):
    start = "2022{:02}{:02}".format(month, 1)
    end =  "2022{:02}{}".format(month, calendar.monthrange(2022, month)[-1])
    driver.find_element(By.CSS_SELECTOR, "#searchFromDt").clear()
    driver.find_element(By.CSS_SELECTOR, "#searchFromDt").send_keys(start)
    driver.find_element(By.CSS_SELECTOR, "#searchToDt").clear()
    driver.find_element(By.CSS_SELECTOR, "#searchToDt").send_keys(end)
    driver.find_element(By.CSS_SELECTOR, "#fileType > option:nth-child(2)").click()
    driver.find_element(By.CSS_SELECTOR, "#listForm > div > table:nth-child(2) > tbody > tr:nth-child(2) > td > a > img").click()
    time.sleep(20)
```
> 보안코드만 빼면 자동화 완성이 되는 것이다.
#### 새롭게 데이터를 받을 것을 하둡에 파일을 올리기(with  filezilla)
![image](https://user-images.githubusercontent.com/85923524/187116472-a0d010bb-04bf-4471-831f-b26c2bcf885a.png)
#### trans.py 에서 폴더 경로 변경
-  정부 데이터는 euc-kr 형태로 되어있어서 인코딩을 해줘야한다.
![image](https://user-images.githubusercontent.com/85923524/187116584-8e7b75c3-8bf2-40b0-9091-52487ec99dd1.png)
- python3 trans.py 실행
![image](https://user-images.githubusercontent.com/85923524/187116974-5672d28a-a941-447f-a7bf-d255600e575a.png)
#### 하둡에다 폴더 만들고 인코딩한 파일들 집어넣기
- hdfs dfs -mkdir /apt_2022
- hdfs dfs -put ~/apt_2022/*.txt /apt_2022
- hdfs dfs -ls /apt_2022
#### Hive 실행
```
CREATE EXTERNAL TABLE IF NOT EXISTS apt2022 (
sigungu STRING,
bunji   STRING,
MainNumber STRING,
PartNumber STRING,
complex   STRING,
area      FLOAT,
con_month STRING,
con_day   STRING,
amount    INT,
apt_floor STRING,
con_year  STRING, 
road_name STRING)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
   "separatorChar" = ",",
   "quoteChar"     = "\""
)  
LOCATION '/apt_2022'
tblproperties ("skip.header.line.count"="16");
```
### 집계함수로 데이터 조회하기
- select * from apt2022 where CAST(regexp_replace(amount, ',' , '') AS INT) = (select max(CAST(regexp_replace(amount, ',' , '') AS INT)) from apt2022 );
	- 가장 비싼 곳의 정보를 조회해줘
![image](https://user-images.githubusercontent.com/85923524/187118179-667baf93-5361-4cee-b2e1-bb47cb20cd83.png)
> PH129가 나온것을 알 수 있다.
### view 테이블 만들기
```
CREATE VIEW apt_view2 AS select split(sigungu, " ")[0] as si, split(sigungu, " ")[1] as gu, split(sigungu, " ")[2] as dong, area, con_year, con_month, con_day, CAST(regexp_replace(amount, ',' , '') AS INT) as amount, apt_floor  from apt2022;
```
- 컬럼형태를 바꾸었다.
- desc apt2022;
![image](https://user-images.githubusercontent.com/85923524/187118577-252c17fe-9ebe-4f87-b312-9e982014d15b.png)
- desc apt_view2;
![image](https://user-images.githubusercontent.com/85923524/187118482-a0abee33-42a0-45c5-b901-b497c9f39962.png)

### 서울특별시에서  구별로 평균 매매가 확인 
- select gu, avg(amount) from apt_view2 where si = '서울특별시' group by gu;
![image](https://user-images.githubusercontent.com/85923524/187118920-20988c3b-fce7-4464-87d2-360256c7a31f.png)
### 평균으로 내림차순으로 정렬
- select gu, avg(amount) as mean from apt_view2 where si = '서울특별시' group by gu order by mean desc;
![image](https://user-images.githubusercontent.com/85923524/187118995-f8437904-dfdc-45d1-9084-6c98b5b5b2e0.png)
>용산구가 제일비싸다

# 실습진행
1. 날짜, 역명, 구분, 04~ 05.... 00~01
	-역명은 통일(서울역(150),서울역(426) -> 서울역
2. 데이터 베이스 생성:
	- database: openapi
	- table 명 : subway_2016
	
![image](https://user-images.githubusercontent.com/85923524/187200103-de749b41-9f57-46fb-9b51-adc765975bb1.png)

![image](https://user-images.githubusercontent.com/85923524/187200286-f03654ce-0083-4e8b-af38-865f4591ed82.png)
![image](https://user-images.githubusercontent.com/85923524/187200492-211a7f5f-3b2d-47aa-8d52-76b86d80a502.png)
![image](https://user-images.githubusercontent.com/85923524/187200873-1e05f5f9-7b1d-4eb6-8dcf-022ed79b49c1.png)
![image](https://user-images.githubusercontent.com/85923524/187201165-42236d8e-ea28-4085-ae85-2d15fc2243b7.png)
![image](https://user-images.githubusercontent.com/85923524/187201316-40b5cddc-ee75-4056-bd3e-924d939c4e41.png)
![image](https://user-images.githubusercontent.com/85923524/187201585-8b570fb0-210a-453d-b6c9-8bcd2ceda1a8.png)
![image](https://user-images.githubusercontent.com/85923524/187201715-64d99a1a-3159-47f6-921f-52f146639ed2.png)

[subway_notebook](https://github.com/junhyeukkwon/TIL/blob/main/Hadoop/subway_datahandling.ipynb)
# Hadoop, sqoop
## sqoop
하둡과 관계형 데이터데이스 간에 데이터를 전송할 수 있도록 설계된 오픈소스 소프트웨어 이다.
![image](https://user-images.githubusercontent.com/85923524/187101413-fd6fae9d-68ce-48c1-aeb9-8b694abc46b9.png)

