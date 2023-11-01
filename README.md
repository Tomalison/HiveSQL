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
AA
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
- 
