# HDFS(하둡 분산 파일시스템)
## 개념
### HDFS의 블록
- 블록의 손상과 디스크 및 머신에 대처하기 위해 각 블록은 물리적으로 분리된 다수의 머신(기본 3대)에 복제
- 블록이 손상되거나 머신의 장애로 다른 걸 못쓰게 된다면, 다른 복제본을 쓸수 있게 함.
### 네임노드와 데이터노드
- HDFS 클러스터는 마스터- 워커 패턴으로 동작하는 두 종유릐 노드가 존제
- 네임노드: 파일 시스템의 네임스페이스를 사용
- 데이터 노드: 데이터에 블록이 저장되어 있지만 이러한 블록 정보를 이용하여 파일을 재구성할 수 없기 떄문에 

### 리소스 관리자와 스케줄러
- YARN: 관리자의 역할을 수행
	- 리소스를 효과적으로 항당하고 사용자 애플리케이션을 스케줄링하는 시스템
	- 리소스를 컨테이너로 분할 
	- 스케줄러 , 컨테이너 관리 컨테이너 분리 수행

### 맵리듀스
- 분산된 저장환경에서 저장된 노드에서 실행하여 결과를 모으는 개념
- 자바언어를 지원함. ( 대부분은 HIVE를 사용함)

## 네이버 뉴스 기사 크롤링 해서 데이터 적재하기
```
#해당 기간의 페이지에 주소 수집
import pandas as pd
from bs4 import BeautifulSoup as BS
import requests


url = "https://news.naver.com/main/list.naver?mode=LS2D&sid2={}&mid=shm&sid1=101&date={}&page={}"
sid2_cate=[259, 258, 261, 771, 260, 310, 263]
total =[]
for date_ in [x.strftime("%Y%m%d") for x in pd.date_range('20220821', '20220823')]:#날짜에 대한 for문
    for category in sid2_cate: #각 경제에서 금융, 증권, 산업/재계, 부동산 등 카테고리의 for문
        r = requests.get(url.format(category, date_, 300), headers=head)
        last_page = BS(r.text).find("div", class_="paging").find("strong").text
        for page_num in range(1, int(last_page)+1): #페이징을 위한 for문
            # print(url.format(category, date_, page_num))
            r3 = requests.get(url.format(category, date_, page_num), headers =head)
            total.extend(list(set([x['href'] for x in BS(r3.text, 'lxml').find("div", class_="list_body newsflash_body").findAll("a")])))
```

##  실제로 py파일로 실행
```
import requests
from bs4 import BeutifulSoup as BS
import os, pickle
from mutiprocessing import Pool

#본문 기사 가져오기
def get_article(url):
    head = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36"}
    if os.path.isdir("./article") == False:
        os.mkdir("./article")
    r = requests.get(url, headers=head)
    article = BS(r.text).find("div", id="newsct_article")
    with open("./article/{}.txt".format("_".join(url.split("/")[-1].split("?"))), "w", encoding='utf-8') as f:
              f.write(article.text.strip())
    #return article.text.strip() 
    
if __name__ == "__main__":
    #pickle로 다시 주소값 리스트 가져오기
    with open("./naver_article_id.pkl", "rb") as f:
        total = pickle.load(f)
    
    pool = Pool(processes=4)
    
    pool.map(get_article, total)
```

### filezilla로 크롤링된 파일 올리기
#### hadoop에 키파일로 hadoop 계정으로 들어가기
- cd ~/.ssh
- cat id_rsa
- 이후 안에 내용을 확장자 .pem으로 파일 저장후 puttygen으로 .pek로 키파일로 저장
- filezilla에서 새호스트로 아래처럼 설정 (키파일 경로는 앞서 만든 키파일)
![image](https://user-images.githubusercontent.com/85923524/186335226-929846d5-810e-484d-a536-e87a9f40c6fb.png)

![image](https://user-images.githubusercontent.com/85923524/186334609-1f4d5de2-6c21-49a2-8b38-9bdf5a192ada.png)
### hadoop에다 올리기

#### unzip 패키지 다운
sudo yum install unzip -y

#### 압축풀기
unzip article.zip

#### hadoop에 압축을 풀고 폴더 생성 후 옮기기
mkdir naver && mv *.txt ./naver

#### 실행 순서
start-dfs
start-yarn
start-mr 

#### 하둡 hdfs 안에 naver 폴더 생성 
 hdfs dfs -mkdir /naver

#### hdfs안에 있는 naver폴더에  파일 넣기
hdfs dfs -put ./naver/* /naver

#### naver 폴더에 있는 모든 파일을 mapreduce실행
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.4.jar grep /naver /output2 '[가-힣]+'
실행 중인 이미지
![image](https://user-images.githubusercontent.com/85923524/186335989-f4c59b72-ed5a-4a98-8b92-f2833ef058ae.png)
- datanode2 의 IP주소에서 :8088을 붙이면 mapreduce의 진행 상황을 볼수 있다.
![image](https://user-images.githubusercontent.com/85923524/186336717-7a311c55-caf7-4d67-9235-236f254b24e7.png)
- 완료가 되면 진행 상황이 finished 가 된다.
![image](https://user-images.githubusercontent.com/85923524/186337451-ed15f99c-057c-44bb-bdad-8d64890a763f.png)

- hdfs dfs -cat/output2/* 이 명령어를 실행을 하면 output2의 파일에 있는  naver폴더에 있는 파일 전체를 mapreduce된 형태( 단어로 분리가 되어 있는)로 저장되어 있는 것을 볼 수 있다.
 ![image](https://user-images.githubusercontent.com/85923524/186337881-8c0c3130-5c26-407c-bfd6-722fabae2687.png)

## HIVE 사용하기

### namenode에서 mariadb 서버 설치하기

- sudo yum install mariadb-server -y

### mariadb 실행하기
- sudo systemctl enable --now mariadb
- mysql_secure_installation
![image](https://user-images.githubusercontent.com/85923524/186344507-1d08b085-6a23-464b-8de4-8b633f1ab8c5.png)
### 접속하기
-mysql -u root -pjun123

### 하이브가 쓸 데이터베이스 만들기
- create database hivedb;
### 유저 생성
- create user hiveuser@localhost identified by 'hivepw';
### hive 권한 설정
- grant all privileges on hivedb.* to hiveuser@localhost;
- grant all privileges on hivedb.* to hiveuser@'client' identified by 'hivepw'; -> 데이터 베이스에 속한 모든테이블한테 client ip에 hiveuser 한테  hivepw로 들어올 때 권한을 부여
- flush privileges; -> 종료

### hadoop@client에서 .bashrc 에 설정 부여
- export HIVE_HOME=/home/hadoop/hive -> 문장 추가
	- 순서가 PATH보다 위에 있어야한다.
- source ~/.bashrc
### apache hive 다운 받기
- wget https://dlcdn.apache.org/hive/hive-3.1.2/apache-hive-3.1.2-bin.tar.gz

### 압축 풀기
- tar xvzf apache-hive-3.1.2-bin.tar.gz

### 파일명 변경
- mv apache-hive-3.1.2-bin ./hive


### 필수 파일 filezilla 로 올리기
- /home/hadoop/hive/conf 에다가 hive 설정 파일 붙여넣기(파일2개)
![image](https://user-images.githubusercontent.com/85923524/186348653-6b4ff3fe-5218-48d4-bde5-e359ef8c7699.png)

- guava.jar 파일 삭제

![image](https://user-images.githubusercontent.com/85923524/186348916-06e17dd5-7366-4b12-9acb-b1d5e32a71a6.png)

- hadoop계정에서 /home/hadoop/conf/lib에 .jar파일 올리기
![image](https://user-images.githubusercontent.com/85923524/186349211-49b18212-da6a-4a69-a03c-af0ffe36523e.png)

### putty  에서 해야할 명령어
- hdfs dfs -mkdir -p /user/hive/warehouse
- hdfs dfs -chmod g+w /user/hive/warehouse
- hdfs dfs -mkdir /tmp
- hdfs dfs -chmod g+w /tmp (  g+w 게스트한테 쓰기 권한을 주기)
- hdfs dfs -ls / 최상위 폴더를 볼수 있는 명령어

### 초기화 명령어
-  schematool -initSchema -dbType mysql

### hivedb 테이블 보기
- ssh namenode
- mysql -u root -pjun123
- use hivedb; ( db 사용)
- show tables;
![image](https://user-images.githubusercontent.com/85923524/186351698-ecb0238c-4a55-476c-a148-8f0c24429d3e.png)
