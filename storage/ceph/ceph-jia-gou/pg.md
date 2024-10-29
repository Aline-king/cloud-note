# PG

PG全称Placement Grouops，是一个逻辑的概念，一个PG包含多个OSD。

引入PG这一层其实是为了更好的分配数据和定位数据。

写数据的时候，写入主osd，冗余两份。
