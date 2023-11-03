# HiveSQL
紀錄hivesql學習紀錄

## 大數據環境安裝與數據導入

###
- Hadoop 分散式檔案系統 (HDFS)：HDFS 是分散式檔案系統，也是 Hadoop 生態系統的主要元件，支援高總處理量的應用程式資料存取作業，而無需預先界定結構定義。
- Yet Another Resource Negotiator (YARN)：YARN 是資源管理平台，負責管理叢集中的運算資源，並使用這些資源為使用者的應用程式排程。這個平台會在 Hadoop 系統上執行排程和資源分配。
- MapReduce：MapReduce 是處理大量資料的程式設計模型，並使用分散式和平行運算演算法保留處理邏輯，藉此協助編寫應用程式，將多個大型資料集轉換為一個方便管理的資料集。

### Hadoop環境安裝
file:///C:/Users/tomsu/Downloads/02.%E5%A4%A7%E6%95%B8%E6%93%9A%E7%92%B0%E5%A2%83%E5%AE%89%E8%A3%9D%E8%88%87%E6%95%B8%E6%93%9A%E5%B0%8E%E5%85%A5.html#%E6%AD%A5%E9%A9%9F%E4%B8%80%E5%AE%89%E8%A3%9Ddocker
- docker安裝 : https://www.runoob.com/docker/windows-docker-install.html
-  https://docs.docker.com/desktop/install/windows-install/
-  在terminal : cd \Users\tomsu\OneDrive\文件\docker-hadoop-hive-parquet\
- 在terminal : docker-compose up -d  /  docker-compose up
- 登入127.0.0.1:8888 或是 http://localhost:8888/

### 數據導入
- 在HUE環境中建立資料庫 create database if not exists dw comment 'data warehouse庫'
- 將CSV透過以下圖中的資料匯入
 ![image](https://github.com/Tomalison/HiveSQL/assets/96727036/4d46e496-7eed-44cb-8b3e-e80ef7b206b7)
- 接著將這些CSV透過以下程式碼導入

``` sh
create table dw.dws_mkt_act_d 
(
act_id            string    comment '活動id', 
act_name          string    comment '活動名稱,例如新春好禮',
act_start_time    timestamp comment '活動開始時間',
act_end_time      timestamp comment '活動結束時間',
ump_id            string    comment '優惠券id',
ump_name          string    comment '優惠券名,例如無門檻50元優惠',
ump_type          string    comment '優惠券類型,1=打折 2=滿減, 3=無門檻折抵',
ump_state         string    comment '1生效 2强制失效(之前已领取的自动作废) 3已结束'
)
comment '行銷活動表'
row format delimited fields terminated by ',' stored as  textfile
tblproperties("skip.header.line.count"="1");


create table dw.dim_city city_code 
(
city_code     string  comment '縣市代碼',
city          string  comment '縣市名稱',
area_code     string  comment '區里代碼',
area          string  comment '區里名稱',
village_code  string  comment '村里代碼',
village       string  comment '村里名稱'
)
comment '城市維度表'
row format delimited fields terminated by ',' stored as  textfile
tblproperties("skip.header.line.count"="1");


create table dw.dws_user_d
(
user_id         string      comment '用戶id',
user_name       string      comment '用戶姓名',
register_time   timestamp   comment '註冊時間',
city            string      comment '城市id',
age             string      comment '年齡區間',
is_marry        int         comment '是否結婚,1=是 ,0=否',
gender          string      comment '性別',
mobile          string      comment '手機號'
) 
comment '用戶表,用戶id唯一'
row format delimited fields terminated by ',' stored as  textfile
tblproperties("skip.header.line.count"="1");
; 


create table dw.dws_order_d
(
order_id        string      comment '訂單id,30位數',
valid_time      timestamp   comment '訂單生效時間',
original_price  double      comment '原價',
pay_amount      double      comment '支付金額',
order_state     string      comment '訂單狀態, 5=支付成功',
goods_type      string      comment '商品類型id',
goods_id        string      comment '商品id',
sale_channel    string      comment '銷售渠道(web官網,移動端官網,app商城)',
pay_way         string      comment '支付方式(信用卡支付,第三方支付...)',
is_return       string      comment '訂單是否退貨',
order_mark      string      comment '訂單備註',
ump_id          string      comment '優惠券id',
user_id         string      comment '用戶id',
city            string      comment '城市'
)
comment '訂單表,訂單id唯一'
row format delimited fields terminated by ',' stored as  textfile
tblproperties("skip.header.line.count"="1");

load data inpath '/user/tomsu25478/dw.dws_mkt_act_d.csv' into table dw.dws_mkt_act_d;
load data inpath '/user/tomsu25478/dim_city.csv' into table dw.dim_city;
load data inpath '/user/tomsu25478/dw.dws_user_d.csv' into table dw.dws_user_d;
load data inpath '/user/tomsu25478/dw.dws_order_d.csv' into table dw.dws_order_d;
```

### 認識數據模型
![image](https://github.com/Tomalison/HiveSQL/assets/96727036/09c11c46-a1b0-4379-9fc5-d55ace509573)

- desc dw.dws_order_d ;  (desc 就是description

### Select 起手式
以下是一個完整的Hive SQL語句
``` sh
select...
 from...
  where...
   group by...
    having...
     order by...
      limit...
```
一定要按照以上的順序
- select...from...表示從table中取出某個字段
- where為限制條件，可理解為取子數據
- group by為分組
- having為分組的限制條件
- order by為排序
- limit為限制返回結果的行數

### SQL簡介
- select order_id,order_state from dw.dws_order_d; 取得該表該欄位 / *取全部
- select order_id,order_state from dw.dws_order_d  limit 10; 限制資料10筆
- order by 可對數據進行升序(預設)或降序(desc) ，後續要繼續排，後面也可以在加字段，這是一個全局的排序，會吃資源、耗效能 / 為了保證全局是有序的排列，這個任務的工作只會對應到一個map跟reduce，不會被打散到多個map中
- distinct 排除重複，把一個字段內重複的值排除掉，只保留下唯一的 select distinct order_state, sale_channel from dw.dws_order_d; (範例是雙重去重)
  ![image](https://github.com/Tomalison/HiveSQL/assets/96727036/01f51efd-c813-493f-8c45-8be712b4055a)
- where 搭配過濾的子句
``` sh
- 比較運算(> , = , < , <> , <= , >=, != )
- (NOT) Between...And...介於..之間(NOT不包含邊界值)
- (NOT) IN過濾...包含於...
- (NOT)(要注意大小寫)模糊查詢:LIKE與通配符(%)...指定特定的**字符**做模糊匹配
- NULL過濾...(不)為空值 (WHERE is (not) null
- 多過濾條件組合 : and交集 or聯集  如果 where sale_channel='web官網' or sale_channel='移動端官網' and order_state=5 ，這樣的查詢and為一組，先看and才是or
```
### column的四則運算與別名
- 字符串跟時間無法用+ - * /  select 1+2,1-2,1*2,1/2,1+2*2,(1+2)*2,(1+2)/(1-2) 都符合數學的四則運算
- as 就是取別名 : 只能包含小寫字母、數字與下滑線，不可以下滑線開頭，不可以只包含數字； 如果有別名後，order by就要用別名

### 聚合函數
``` sh
- count() |返回column中的非NULL的行數|
- sum() |返回column中所有值非NULL的求和結果|
- avg() |返回column中所有值非NULL的平均值|
- max() |返回column中所有值非NULL的最大值|
- min() |返回column中所有值非NULL的最小值|
- stddev |返回column中所有值非NULL的標準差|
- variance() |返回column中所有值非NULL的變藝術| 
```
![image](https://github.com/Tomalison/HiveSQL/assets/96727036/17ee4039-cf69-480b-8a0d-aad34e7f5860)

#### Group BY指定分組對象
- 例如上圖範例，如果要比較'63'跟'65'這兩個城市，前面要先多city這個字段，select city  group by city;
![image](https://github.com/Tomalison/HiveSQL/assets/96727036/fae7da37-b466-4b2b-8a42-95227bf002be)
- gruop by是不能使用別名的，要用原本的

#### DISTINCT與聚合函數的配合
![image](https://github.com/Tomalison/HiveSQL/assets/96727036/3124e357-63e5-4e26-80a8-8602cd79b633)

#### HAVING過濾分組後的數據
- 經過聚合之後，再去篩選 就可以在gruop by後面加上having (分組後)
![image](https://github.com/Tomalison/HiveSQL/assets/96727036/612e44d6-26de-4d0d-be6c-ffa2af13b282)
- Having 也可以加上 or 雙條件吻合就可以一起叫出來
![image](https://github.com/Tomalison/HiveSQL/assets/96727036/be998595-25b3-47dd-a9d9-9c5a20e9899b)

#### HIVE語句的執行順序
- Hive語句執行順序 : select...from...where...gruop by...having...order by...limit
-  代碼的執行順序其實是 : from...where...group by...having...select...distinct...order by...limit
- explain
![image](https://github.com/Tomalison/HiveSQL/assets/96727036/8df660ad-512c-4a5e-a851-1ddd17daa2da)

#### Group by去重
- 在傳統SQL中，用group by的去重效能比distinc好，但在Hadoop不一定有差

### 控制函數

#### 認識Case When與If

```sh
CASE WHEN 條件1 THEN VALUE1
     WHEN 條件2 THEN VALUE2
     ----
     WHEN 條件N THEN VALUEN
   ELSE VALUE_UNKNOW
   END
```
```sh
SELECT
  order_id,
  city,
  CASE WHEN city in ('10017','63','65','10003','10004') THEN '北區'
       WHEN city in ('10015','66','10007','10008','10009') THEN '中區'
       WHEN city in ('Chiayi','67','64','10013') THEN '南區'
       ELSE '其他-東部或離島' 
   END as city_group
FROM dws_order_d；
```
```sh
IF(條件1，VALUE1，VALUE_UNKNOW)
如果條件表達式為TRUE，則返回"VALUE"，否則返回"VALUE_UNKNOW"

SELECT IF(1=1,'YES','NO') AS C1,
       IF(1=2,'YES','NO') AS C2,
用法為
IF(條件1,value1,
 IF(條件2,value2,value_unknow)) 套嵌
```
```sh
SELECT
  order_id,
  city,
  IF (city in ('10017','63','65','10003','10004') , '北區',
       IF (city in ('10015','66','10007','10008','10009') , '中區',
       IF (city in ('Chiayi','67','64','10013') , '南區'
       , '其他-東部或離島' )))
   as city_group
FROM dws_order_d；
```
- 對控制函數分組 : group by要取別名之前的字段，或取別名之前的表達式
```sh
SELECT
  IF (city in ('10017','63','65','10003','10004') , '北區',
       IF (city in ('10015','66','10007','10008','10009') , '中區',
       IF (city in ('Chiayi','67','64','10013') , '南區'
       , '其他-東部或離島' )))
   as city_group,
  count(order_id) as order_cou
FROM dws_order_d
GROUP BY  IF (city in ('10017','63','65','10003','10004') , '北區',
       IF (city in ('10015','66','10007','10008','10009') , '中區',
       IF (city in ('Chiayi','67','64','10013') , '南區'
       , '其他-東部或離島' )))；
```
![image](https://github.com/Tomalison/HiveSQL/assets/96727036/284f2a9a-a373-47bc-bd58-8ea92559f56c)

- 彙總函數與控制函數的結合
```sh
SELECT
  count(IF (city in ('10017','63','65','10003','10004') ,order_id,null)) as order_cou_north
FROM dws_order_d
以此類推
``` 
- 控制函數的判斷順序
 ![image](https://github.com/Tomalison/HiveSQL/assets/96727036/df7da8ca-d57a-4006-9f29-7d6e81b5ba79)
以上圖範例，65映射到北區，即使你查詢的字段滿足多個條件，但是case when 跟 if 他滿足條件是從最前面開始，當滿足條件之後，他不會館後面條件是否滿足，除非不滿足才會往下判斷下去。

### 用函數高校處理數據
#### 常用數值處理函數
#### 常用時間處理函數
#### 常用字段串處理函數
### 煩人的數據缺失與極端值
#### 評估數據質量
#### 缺失值對聚合函數及算數運算的影響
#### 缺失值的處理
#### 查找極端值
### 子查詢
#### FROM子查詢
#### WHERE子查詢
### 實現多表查詢，利用JOIN實現表的橫向連結
#### 認識4種JOIN
#### JOIN語句
#### LEFT JOIN與RIGHT JOIN語句
#### FULL JOIN語句
### 實現多表查詢，利用UNION實現表的縱向連結
#### UNION與UNION ALL
#### UNION的廠警 : 指標管理
### 改變表的結構:行列互轉
#### 字段折上去
#### 字段折下來
### 強大的窗口函數
#### 了解甚麼是窗口函數
#### 用row_number()給流水號
#### 用LEAD()查詢往下第n行
### 庫、表的操作
#### 數據庫的操作
#### 方便分析的臨時表
#### 標準的建表語句
#### 追加新數據
#### 導入CSV數據
### 手把手一起寫LEETCODE SQL題(HARD)
#### LEETCODE DATABASE題簡介
- EXERCISE1:185.DEOARTMENT TOP THREE SALARIES
- EXERCISE2:262.TRIPS AND USERS
- EXERCISE3:601.HUMAN TRAFFIC OF STADIUM

