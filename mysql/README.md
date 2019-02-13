1. 表名忽略大小写
在[mysqld]下加入一行：lower_case_table_names=1
2. 配置字符字符集
[client]
default-character-set=utf8
[mysqld]
character-set-server=utf8
[mysql]
default-character-set=utf8