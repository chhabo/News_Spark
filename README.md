8# 基于Spark2.x新闻网大数据实时分析可视化系统项目

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





import org.apache.hc.client5.http.impl.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.HttpClients;
import org.apache.hc.client5.http.impl.io.PoolingHttpClientConnectionManagerBuilder;
import org.apache.hc.client5.http.ssl.SSLConnectionSocketFactory;
import org.apache.hc.core5.ssl.SSLContexts;
import org.apache.hc.core5.ssl.TrustStrategy;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.client.HttpComponentsClientHttpRequestFactory;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.http.converter.StringHttpMessageConverter;
import org.springframework.web.client.RestTemplate;

import javax.net.ssl.SSLContext;
import java.nio.charset.StandardCharsets;
import java.security.KeyManagementException;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;
import java.util.List;

@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate() throws NoSuchAlgorithmException, KeyManagementException, KeyStoreException {
        TrustStrategy acceptingTrustStrategy = (x509Certificates, s) -> true;
        SSLContext sslContext = SSLContexts.custom()
                .loadTrustMaterial(null, acceptingTrustStrategy)
                .build();
        SSLConnectionSocketFactory csf = new SSLConnectionSocketFactory(sslContext);

        CloseableHttpClient httpClient = HttpClients.custom()
                .setSSLSocketFactory(csf)
                .setConnectionManager(PoolingHttpClientConnectionManagerBuilder.create()
                        .setSSLSocketFactory(csf)
                        .build())
                .build();

        HttpComponentsClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory(httpClient);

        RestTemplate restTemplate = new RestTemplate(requestFactory);
        setCharset(restTemplate);
        return restTemplate;
    }

    private void setCharset(RestTemplate restTemplate) {
        List<HttpMessageConverter<?>> messageConverters = restTemplate.getMessageConverters();
        for (HttpMessageConverter<?> messageConverter : messageConverters) {
            if (messageConverter instanceof StringHttpMessageConverter) {
                ((StringHttpMessageConverter) messageConverter).setDefaultCharset(StandardCharsets.UTF_8);
            }
        }
    }
}


import org.apache.hc.client5.http.impl.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.HttpClients;
import org.apache.hc.client5.http.impl.io.PoolingHttpClientConnectionManagerBuilder;
import org.apache.hc.client5.http.ssl.SSLConnectionSocketFactory;
import org.apache.hc.core5.ssl.SSLContexts;
import org.apache.hc.core5.ssl.TrustStrategy;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.client.HttpComponentsClientHttpRequestFactory;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.http.converter.StringHttpMessageConverter;
import org.springframework.web.client.RestTemplate;

import javax.net.ssl.SSLContext;
import java.nio.charset.StandardCharsets;
import java.security.KeyManagementException;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;
import java.util.List;

@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate() throws NoSuchAlgorithmException, KeyManagementException, KeyStoreException {
        TrustStrategy acceptingTrustStrategy = (x509Certificates, s) -> true;
        SSLContext sslContext = SSLContexts.custom()
                .loadTrustMaterial(null, acceptingTrustStrategy)
                .build();
        SSLConnectionSocketFactory csf = new SSLConnectionSocketFactory(sslContext);

        CloseableHttpClient httpClient = HttpClients.custom()
                .setConnectionManager(PoolingHttpClientConnectionManagerBuilder.create()
                        .setSSLSocketFactory(csf)
                        .build())
                .build();

        HttpComponentsClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory(httpClient);

        RestTemplate restTemplate = new RestTemplate(requestFactory);
        setCharset(restTemplate);
        return restTemplate;
    }

    private void setCharset(RestTemplate restTemplate) {
        List<HttpMessageConverter<?>> messageConverters = restTemplate.getMessageConverters();
        for (HttpMessageConverter<?> messageConverter : messageConverters) {
            if (messageConverter instanceof StringHttpMessageConverter) {
                ((StringHttpMessageConverter) messageConverter).setDefaultCharset(StandardCharsets.UTF_8);
            }
        }
    }
}


Feature	Kafka	Google Cloud Pub/Sub
Type	Open-source distributed streaming platform	Fully managed messaging service
Message Model	Log-based messaging system, consumers pull messages	Publish-subscribe model
Data Storage	Persistent storage, messages stored on disk	Temporary storage, messages have retention period
Management	Self-managed or managed services (e.g., Confluent Cloud)	Fully managed by Google Cloud
Scalability	Highly scalable by adding more Broker nodes	Automatically scales to handle any throughput
Latency	Low latency, suitable for real-time data processing	Moderate latency, optimized for high throughput and reliability
Fault Tolerance	High, with built-in replication and recovery mechanisms	High, with multi-region replication
Use Cases	Real-time data processing, event sourcing, log aggregation, stream processing	Event-driven architectures, real-time analytics, messaging between microservices
Ecosystem	Includes Kafka Streams, Kafka Connect, etc.	Seamless integration with GCP services like BigQuery, Dataflow, Cloud Functions
Summary:
Kafka: Ideal for high throughput, low latency applications with the ability to manage and maintain the cluster.

Pub/Sub: Perfect for quick setup, reduced management work, and integration with other GCP services.

This should help you decide based on your specific needs and use cases. If you have any more questions, feel free to ask!




Why is Kafka So Fast:

Log Partitioning:

Splits Topic into multiple partitions, allowing parallel read and write operations, increasing throughput.

Sequential Writing:

Data is written to disk sequentially, reducing disk seek time and increasing write efficiency.

Zero Copy:

Utilizes the zero-copy technology of the operating system, reducing CPU overhead by directly transferring data from disk to network.

Efficient Messaging:

Uses consumer groups to achieve parallel consumption of messages, improving processing efficiency.

Data Compression:

Supports compression formats like GZIP and Snappy, reducing bandwidth usage while maintaining efficient message delivery.

Distributed Architecture:

The cluster consists of multiple brokers, ensuring high availability and scalability.

#!/bin/bash

# 检查参数数量
if [ "$#" -ne 2 ]; then
  echo "Usage: $0 <username> <public_key_file>"
  exit 1
fi

USERNAME=$1
PUBLIC_KEY_FILE=$2

# 检查用户是否存在
if ! id "$USERNAME" &>/dev/null; then
  echo "User $USERNAME does not exist"
  exit 1
fi

# 检查公钥文件是否存在
if [ ! -f "$PUBLIC_KEY_FILE" ]; then
  echo "Public key file $PUBLIC_KEY_FILE does not exist"
  exit 1
fi

# 创建用户的.ssh目录（如果不存在）
USER_HOME=$(eval echo ~$USERNAME)
SSH_DIR="$USER_HOME/.ssh"
mkdir -p "$SSH_DIR"
chown "$USERNAME:$USERNAME" "$SSH_DIR"
chmod 700 "$SSH_DIR"

# 将公钥添加到authorized_keys文件
AUTHORIZED_KEYS="$SSH_DIR/authorized_keys"
cat "$PUBLIC_KEY_FILE" >> "$AUTHORIZED_KEYS"
chown "$USERNAME:$USERNAME" "$AUTHORIZED_KEYS"
chmod 600 "$AUTHORIZED_KEYS"

echo "Public key added to $AUTHORIZED_KEYS for user $USERNAME"

import React, { useState } from 'react';
import * as XLSX from 'xlsx';

const ExcelReader = () => {
  const [data, setData] = useState([]);

  const handleFileUpload = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();

    reader.onload = (event) => {
      const binaryStr = event.target.result;
      const workbook = XLSX.read(binaryStr, { type: 'binary' });
      const sheetName = workbook.SheetNames[0];
      const sheet = workbook.Sheets[sheetName];

      const jsonData = XLSX.utils.sheet_to_json(sheet, {
        header: 1,
        raw: false,
        defval: '',
        cellDates: true, // 将单元格解析为日期
        dateNF: 'yyyy-mm-dd' // 设置日期格式
      });

      const parsedData = jsonData.map(row =>
        row.map(cell => {
          if (cell instanceof Date) {
            // 将日期格式化为字符串
            return cell.toISOString().split('T')[0];
          } else if (typeof cell === 'string' && !isNaN(Date.parse(cell))) {
            // 处理字符串日期格式
            return new Date(cell).toISOString().split('T')[0];
          } else {
            return cell;
          }
        })
      );

      setData(parsedData);
    };

    reader.readAsBinaryString(file);
  };

  return (
    <div>
      <input type="file" onChange={handleFileUpload} />
      <table>
        <tbody>
          {data.map((row, rowIndex) => (
            <tr key={rowIndex}>
              {row.map((cell, cellIndex) => (
                <td key={cellIndex}>{cell}</td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
};

export default ExcelReader;





import java.time.LocalDate
import java.time.format.DateTimeFormatter
import java.time.format.DateTimeParseException
import java.time.ZoneId

object DateParser {
  // 定义两种日期格式的解析器
  val formatter1 = DateTimeFormatter.ofPattern("yyyy/MM/dd")
  val formatter2 = DateTimeFormatter.ofPattern("dd/MM/yy")

  def parseDate(dateStr: String): Long = {
    try {
      // 尝试使用第一种格式解析
      val date = LocalDate.parse(dateStr, formatter1)
      // 返回时间戳
      date.atStartOfDay(ZoneId.systemDefault()).toEpochSecond
    } catch {
      case e1: DateTimeParseException =>
        try {
          // 如果第一种格式解析失败，尝试使用第二种格式
          val date = LocalDate.parse(dateStr, formatter2)
          // 返回时间戳
          date.atStartOfDay(ZoneId.systemDefault()).toEpochSecond
        } catch {
          case e2: DateTimeParseException =>
            throw new IllegalArgumentException(s"无法解析日期字符串: $dateStr")
        }
    }
  }

  def main(args: Array[String]): Unit = {
    // 测试不同格式的日期字符串
    val dateStr1 = "2024/09/21"
    val dateStr2 = "21/09/24"

    println(s"$dateStr1 的时间戳: ${parseDate(dateStr1)}")
    println(s"$dateStr2 的时间戳: ${parseDate(dateStr2)}")
  }
}


