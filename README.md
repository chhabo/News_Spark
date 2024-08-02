# 基于Spark2.x新闻网大数据实时分析可视化系统项目

## 一、业务需求分析

- 捕获用户浏览日志信息
- 实时分析前20名流量最高的新闻话题
- 实时统计当前线上已曝光的新闻话题
- 统计哪个时段用户浏览量最高


## 二、系统架构图设计

![image](https://github.com/ZzXxL1994/News_Spark/blob/master/z_pic/news1.png)


## 三、系统数据流程设计

![image](https://github.com/ZzXxL1994/News_Spark/blob/master/z_pic/news2.png)


## 四、集群资源规划设计

![image](https://github.com/ZzXxL1994/News_Spark/blob/master/z_pic/news3.png)


## 五、步骤详解

### 具体请参考[博客](https://blog.csdn.net/u011254180/article/details/80172452)

``` js
#!/bin/bash

# 定义源目录和目标目录
source_dir="/path/to/source_directory"
target_dir="/path/to/target_directory"

# 创建目标目录（如果不存在）
mkdir -p "$target_dir"

# 获取当前日期的前一天
yesterday=$(date -d "yesterday" +%Y%m%d)

# 遍历源目录中的所有文件
for file in "$source_dir"/*; do
  if [ -f "$file" ]; then
    # 获取文件的修改日期
    file_date=$(date -r "$file" +%Y%m%d)
    
    # 如果文件日期大于一天
    if [ "$file_date" -lt "$yesterday" ]; then
      # 获取文件的月份
      file_month=$(date -r "$file" +%Y%m)
      # 创建月份目录（如果不存在）
      mkdir -p "$target_dir/$file_month"
      # 压缩文件
      tar -czf "$file_month/$(basename "$file").tar.gz" -C "$source_dir" "$(basename "$file")"
      # 移动压缩文件到目标目录
      mv "$file_month/$(basename "$file").tar.gz" "$target_dir/$file_month"
    else
      # 压缩文件
      tar -czf "$(basename "$file").tar.gz" -C "$source_dir" "$(basename "$file")"
      # 移动压缩文件到目标目录
      mv "$(basename "$file").tar.gz" "$target_dir"
    fi
  fi
done

echo "符合条件的文件已压缩并移动到目标目录。"

