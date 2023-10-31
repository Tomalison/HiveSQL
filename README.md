# HiveSQL
紀錄hivesql學習紀錄

## 大數據環境安裝與數據導入

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
