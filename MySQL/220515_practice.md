# 220515_Practice 
* 현재 월요일이면 프로젝트를 진행하기 앞서 데이터베이스 모델링에 대해서 공부를 하고 있다.    
* 많이 부족하지만 조금씩 예제를 진행을 하며 익히고 있다.   

## 데이터 모델링이란?
* 데이터 모델링이란 정보 시스템 구축의 대상이 되는 업무 내용을 분석하여 이해하고 약속된 표기법에 의해 표현하는걸 의미.
*  분석된 모뎅을 가지고 실제 데이터베이스를 생성하여 개발 및 데이터 관리에 사용되는, 데이터 베이스 설계의 핵심과정.
* 데이터를 추상화 한 데이터 모델은 데이터 베이스의 골격을 이해하고 그 이해를 바탕으로 SQL문장을 기능과 성능적인 측면에서 효율적으로 작성하기 위해 꼭 알아야한다.

### 데이터 모델링 순서

1. 업무 파악
2. 개념적 데이터 모델링
3. 논리적 데이터 모델링
4. 물리적 데이터 모델링

#### 1.업무파악
* 업무파알을 제대로 하기 못하면, 정확한 모델링을 할 수 없다.
* 해당 업무를 파악하기 좋은 방법으로는 UI를 의뢰인과 함께 확인해 나아가는 것이다.
* 궁극적으로 만들어야 하는것이 무엇인지 심도있게 알아봐야한다.

#### 2. 개념적 데이터 모델링
* 개념적 모델링은 내가 하고자하느 일의 데이터간 관계를 구상하는 단계이다.
* 개념적 데이터 모델은 추상화 수준이 높고 업무중심적이고 포괄적인 수준의 모델일을 수행한다.
* 그것을 표현하기 위해**ERD**를 생성 하는것.
> 엔티티 = Table
> Attribute = Column
> Relation = PK, FK
> Tuple = Row
![enter image description here](https://ta-ye.github.io/assets/img/RDB1.png)


#### 3. 논리적 데이터 모델링
* 엔티디 중심의 상위 수준의 엔티티 중심의 모델이 완성되면 구체화된 업무중심의 데이터 모델을 만들러 내는데 이것을 논리적인 데이터 모델링이라고한다.
* 이 단계에서 업무에 대한 key,속성, 관계들을 표시, 정규화 활동을 수행한다.
* 정규회는 데이터 모델의 일관성을 확보, 중복을 제거하여 신뢰성있는 데이터 구조를 얻는데 목적이 있음.
* 단순히 추상적인 데이터에서 보다 구체적인 데이터로 작성해야한다.
* 예를 들어 회원 정보의 아이디, 비밀번호에 각 데이터 타입을 명시해 주고 각 데이터간의 관계를 정밀하게 맺어주며 테이블 키를 지정해줘야한다.

![enter image description here](https://gitmind.com/wp-content/uploads/2020/10/er-diagram-creator.jpg)

#### 4. 물리적 데이터 모델링
* 물리적 데이터 모델링은 최종적을 데이터를 관리할 데이터 베리스를 선택하고, 서택한 데이터 베이스에 실제 테이블을 만드는 작업을 말함.

```sql
/* 테이블 생성 */

-- 회원정보

create  table  member_tbl (

member_uid  bigint  primary  key auto_increment,

member_name  varchar(45)  unique  not  null,

member_pwd  varchar(45)  not  null,

member_status  boolean  not  null

);

-- 로그인기록정보

create  table  login_info_tbl(

member_name  varchar(45)  not  null,

info_ip  varchar(45)  not  null,

info_date datetime  not  null,

constraint  fk_member_name  foreign  key (member_name)  references  member_tbl (member_name)

);

-- 게시판

create  table  board_tbl (

board_uid  bigint  primary  key auto_increment,

member_name  varchar(45)  not  null,

board_title  varchar(45)  not  null,

board_date datetime  not  null,

board_hit  int  not  null,

board_post  varchar(5000)  not  null,

constraint  fk_member_name  foreign  key(member_name)  references  member_tbl(member_name)

);

-- 게시판 풀텍스트 인덱스 생성

create  Fulltext index idx_title  on  board_tbl ( board_title );

create  Fulltext index idx_post  on  board_tbl ( board_post );

-- show index from board_tbl ;

-- 댓글

create  table  reply_tbl (

reply_uid  bigint  primary  key auto_increment,

board_uid  bigint  not  null,

member_name  varchar(45)  not  null,

reply_date datetime  not  null,

reply_post  varchar(1000)  not  null,

foreign  key(board_uid)  references  board_tbl(board_uid),

foreign  key(member_name)  references  member_tbl(member_name)

);

-- 댓글 풀텍스트 인덱스 생성

create  Fulltext index idx_reply  on  reply_tbl ( reply_post );
  

```
출처: [https://inpa.tistory.com/entry/DB-📚-데이터-모델링-1N-관계-📈-ERD-다이어그램#데이터_모델링_이란?](https://inpa.tistory.com/entry/DB-%F0%9F%93%9A-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%AA%A8%EB%8D%B8%EB%A7%81-1N-%EA%B4%80%EA%B3%84-%F0%9F%93%88-ERD-%EB%8B%A4%EC%9D%B4%EC%96%B4%EA%B7%B8%EB%9E%A8#%EB%8D%B0%EC%9D%B4%ED%84%B0_%EB%AA%A8%EB%8D%B8%EB%A7%81_%EC%9D%B4%EB%9E%80?) [👨‍💻 Dev Scroll]


## ERD(Entity Relationship Diagram)
* ERD는 데이터베이스 구조를 한 눈에 알아보기 위해 그려놓은 다이어그램이다.   
* ERD 단어에서 의미하는 그대로 'Entity' 개체와 'Relationship' 관계를 중점적으로 표시하는 다이어그램이다.   

### ERD 구성 요소 표기법
---
#### Entity 
* Entity는 정의 가능한 사물 또는 개념을 의미.
* 사람이나, 객체 혹으니 개념이나 이벤트등을 entity로 둘 수 있다.   
* 객체에는 가구나 자동차를 예시로 들 수 있다.
* 개념은 프로필이나 자기소개서들이 있겠고, 이벤트는 거래등이 있을 수 있다.   
* 데이터 베이스를 설계할 때 '테이블'이 Entity 로 정의될 수 있다.     

#### Entity Attribute
* 각각의 entity에는 속성을 포함하고 있다.   
* Attribute는 개체가 갖고있는 속성이다.   
* 예를 들어 사람이라면 , 나이, 이름, 생년월일 등 속성들을 포함할수 있다.   
* 여기서 필요한건, 데이터 타입이다. 속성에 맞는 데이터 타입을 명시해줘야한다.   

#### **Entity 분류**

|**구 분**|**내 용**|
|--|--|
|유형 엔티티|물리적인 형태가 있습니다.(예 : 고객, 상품, 거래처, 학생, 교수 등)
|무형 엔티티|물리적인 형태가 없고 개념적으로만 존재하는 엔티티입니다. (예 : 인터넷 장바구니, 부서 조직 등)
|문서 엔티티|업무 절차상에서 사용되는 문서나 장부, 전표에 대한 엔티티입니다. (거래명세서, 주문서 등)
|이력 엔티티|업무상 반복적으로 이루어지는 행위나 사건의 내용을 일자별, 시간별로 저장하기 위한 엔티티입니다 . ( 예 : 입고 이력, 출고 이력, 구매 이력 등)
|코드 엔티티|무형 엔티티의 일종으로 각종 코드를 관리하기 위한 엔티티입니다. (예 : 국가코드, 각종 분류 코드)

  
  
출처: [https://inpa.tistory.com/entry/DB-📚-데이터-모델링-1N-관계-📈-ERD-다이어그램#데이터_모델링_이란?](https://inpa.tistory.com/entry/DB-%F0%9F%93%9A-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%AA%A8%EB%8D%B8%EB%A7%81-1N-%EA%B4%80%EA%B3%84-%F0%9F%93%88-ERD-%EB%8B%A4%EC%9D%B4%EC%96%B4%EA%B7%B8%EB%9E%A8#%EB%8D%B0%EC%9D%B4%ED%84%B0_%EB%AA%A8%EB%8D%B8%EB%A7%81_%EC%9D%B4%EB%9E%80?) [👨‍💻 Dev Scroll]
 
#### ERD 구성요소표기법 - 키와 제약조건   
제약조건(constraint)은 데이터베이스 데이터의 정확성을 유지하기 위한 목적으로 사용하며 테이블에 저장할 데이터를 제약하는 특수한 규칙이다.   

**PK primary key**   
* id와 같이 테이블의 컬럼에서 명확히 구분을 지을 수 있는 것으로 기본키로 지정.   

**FK foreign key**   
* PK를 받아와서 키로 사용한다. 이때 키를 받는 테이블은 자식테이블이 되고, 키를 주는 테이블은 부모테이블이 되는것이다.   

#### ERD 구성요소표기법 - 관계 선 긋기

**두 개체의 관계 - 점선 & 실선**   
1. 두 관계 중 부모의 키를 PK로 받는지 여부에 따라서 점선, 실선 표기가 다르게 된다.   
2. 실선: 식별관계    
	- 부모 자식 관계에서 자식이 부모의 키를 외래키로 참조해 자신의 키로 설정.   
3. 점선: 비식별관계   
	-  부모 자식 관계에서 자식이 부모의 키를 일반속성으로 참조.   
**두 개체의 관계 - 선의 끝 모양**
* 관계가 존재하는 두 entity사이에 한 entity에서 다른 entity 몇개의 개체와 대응되는지 제약조건을 표기하기위해 선을 그어 표현
* 대표적으로 mapping cardinality(한 개체에서 발생 횟수)의 종류는 다음과 같다.   
> One to One: 1대1 대응   
> One to many: 1대 다 대응   
> Many to One:  다 대 1 대응   
> Many to many: 다 대 다 대응   

![enter image description here](https://blog.kakaocdn.net/dn/bQlRXW/btq6WhbzAwM/fvcO5VHwT2eTtBwexZeCCK/img.png)

**두 개체의 관계 - 필수/ 선택 기호**
'o' 표시가 있다면 없어도 되는개체 선택      
'|' 표기가 있는 반드시 개체가 있어야 한다 필수      

ex)   
김철수 학생은 게임이 취미라서, 취미 테이블에 없기 떄문에 관계가 없다 (선택)     
​     
어떤 학생이 어떤 취미를 갖는데, 그 학생이 존재하지 않는다면 뭔가 잘못된 것이다.     
학번 21003 학생의 취미는 낚시 라는 정보가 있다면 21003학번의 학생의 정보가 학생 엔티티에 반드시 존재해야 한다. 이와 같이 어느 한 쪽이 존재하면 다른 쪽도 반드시 존재해야 하는 관계를 필수 관계라고 한다. (필수)     
  



