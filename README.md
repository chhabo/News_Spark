

58# 基于Spark2.x新闻网大数据实时分析可视化系统项目

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

import React, { useState } from 'react';
import * as XLSX from 'xlsx';
import { format } from 'date-fns';  // 使用 date-fns 库

const CsvParser = () => {
  const [data, setData] = useState([]);

  const handleFileUpload = (event) => {
    const file = event.target.files[0];
    const reader = new FileReader();

    reader.onload = (e) => {
      const binaryStr = e.target.result;
      const workbook = XLSX.read(binaryStr, { type: 'binary' });
      const sheetName = workbook.SheetNames[0];
      const sheet = XLSX.utils.sheet_to_json(workbook.Sheets[sheetName], { header: 1 });
      const formattedSheet = sheet.map((row) => row.map((cell) => {
        // 检查输入是否为字符串，且不是纯数字字符串，且可以被解析为日期
        if (typeof cell === 'string' && isNaN(cell) && !/^\d+$/.test(cell) && !isNaN(Date.parse(cell))) {
          const date = new Date(cell);
          return format(date, 'MM/dd/yyyy HH:mm:ss');  // 格式化时间为 "MM/dd/yyyy HH:mm:ss"
        }
        return cell;
      }));
      setData(formattedSheet);
    };

    reader.readAsBinaryString(file);
  };

  return (
    <div>
      <input type="file" accept=".csv" onChange={handleFileUpload} />
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

export default CsvParser;


import java.util.concurrent.*;
import java.util.concurrent.ConcurrentHashMap;

public class MQTimeoutHandler {

    // 使用ConcurrentHashMap存储消息ID和发送时间
    private final ConcurrentHashMap<String, Long> pendingMessages = new ConcurrentHashMap<>();
    private final ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
    private static final long TIMEOUT_MS = 5000; // 超时时间5秒

    public void start() {
        // 每隔1秒检查一次超时
        scheduler.scheduleAtFixedRate(this::checkForTimeouts, 0, 1, TimeUnit.SECONDS);
    }

    private void checkForTimeouts() {
        long currentTime = System.currentTimeMillis();
        // 遍历并移除超时的消息
        pendingMessages.entrySet().removeIf(entry -> {
            if (currentTime - entry.getValue() > TIMEOUT_MS) {
                handleExpiredMessage(entry.getKey());
                return true; // 移除该条目
            }
            return false;
        });
    }

    private void handleExpiredMessage(String messageId) {
        // 处理超时逻辑，例如重发或记录日志
        System.out.println("消息超时: " + messageId);
        // 示例：重发消息
        resendMessage(messageId);
    }

    private void resendMessage(String messageId) {
        // 实现重发逻辑
        System.out.println("重发消息: " + messageId);
    }

    public void sendMessage(String messageId) {
        // 模拟发送消息
        System.out.println("发送消息: " + messageId);
        pendingMessages.put(messageId, System.currentTimeMillis());
    }

    public void onMessageAck(String messageId) {
        // 收到确认后移除
        pendingMessages.remove(messageId);
        System.out.println("收到确认: " + messageId);
    }

    public void shutdown() {
        scheduler.shutdown();
    }

    public static void main(String[] args) throws InterruptedException {
        MQTimeoutHandler handler = new MQTimeoutHandler();
        handler.start();

        // 模拟发送消息
        handler.sendMessage("MSG-001");
        handler.sendMessage("MSG-002");

        // 模拟确认消息
        Thread.sleep(2000);
        handler.onMessageAck("MSG-001"); // MSG-002将超时

        Thread.sleep(4000); // 等待足够时间触发检查
        handler.shutdown();
    }
}



为了在Java程序中处理订单时埋点并监控处理速度，并将数据推送至Prometheus，可以按照以下步骤进行：

### 1. 添加依赖
在项目的构建文件（如Maven的`pom.xml`）中添加Micrometer和Prometheus的依赖：

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
    <version>1.10.0</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### 2. 配置Spring Boot Actuator
在`application.properties`中启用Prometheus端点：

```properties
management.endpoints.web.exposure.include=prometheus
management.endpoint.prometheus.enabled=true
```

### 3. 定义监控指标
在订单处理类中注入`MeterRegistry`并定义指标：

```java
import io.micrometer.core.instrument.MeterRegistry;
import io.micrometer.core.instrument.Timer;
import io.micrometer.core.instrument.Counter;

@Component
public class OrderProcessor {

    private final Timer orderProcessingTimer;
    private final Counter successCounter;
    private final Counter failureCounter;

    public OrderProcessor(MeterRegistry registry) {
        orderProcessingTimer = Timer.builder("order.processing.duration")
                .description("订单处理耗时")
                .tags("unit", "seconds")
                .register(registry);

        successCounter = Counter.builder("orders.processed.total")
                .description("成功处理的订单数")
                .tag("status", "success")
                .register(registry);

        failureCounter = Counter.builder("orders.processed.total")
                .description("处理失败的订单数")
                .tag("status", "failure")
                .register(registry);
    }
}
```

### 4. 在订单处理方法中埋点
使用定义的指标记录处理时间和结果：

```java
public void processOrder(Order order) {
    Timer.Sample sample = Timer.start();

    try {
        // 业务处理逻辑
        // ...

        // 记录成功
        successCounter.increment();
    } catch (Exception e) {
        // 记录失败
        failureCounter.increment();
        throw e;
    } finally {
        // 记录处理时间
        sample.stop(orderProcessingTimer);
    }
}
```

### 5. 使用AOP简化埋点（可选）
通过切面统一监控所有订单处理方法：

```java
@Aspect
@Component
public class OrderMonitoringAspect {

    private final Timer orderProcessingTimer;
    private final Counter successCounter;
    private final Counter failureCounter;

    public OrderMonitoringAspect(MeterRegistry registry) {
        orderProcessingTimer = Timer.builder("order.processing.duration")
                .register(registry);
        successCounter = Counter.builder("orders.processed.total")
                .tag("status", "success").register(registry);
        failureCounter = Counter.builder("orders.processed.total")
                .tag("status", "failure").register(registry);
    }

    @Around("execution(* com.example.service.OrderService.processOrder(..))")
    public Object monitorOrderProcessing(ProceedingJoinPoint joinPoint) throws Throwable {
        Timer.Sample sample = Timer.start();
        try {
            Object result = joinPoint.proceed();
            successCounter.increment();
            return result;
        } catch (Exception e) {
            failureCounter.increment();
            throw e;
        } finally {
            sample.stop(orderProcessingTimer);
        }
    }
}
```

### 6. 配置Prometheus抓取数据
在Prometheus的`prometheus.yml`中添加任务：

```yaml
scrape_configs:
  - job_name: 'order-service'
    scrape_interval: 15s
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['your-app-host:8080']
```

### 7. 使用Grafana可视化
在Grafana中创建仪表板，添加Prometheus数据源，使用以下查询：

- **平均处理时间**：
  ```
  rate(order_processing_duration_seconds_sum[5m]) / rate(order_processing_duration_seconds_count[5m])
  ```

- **成功率**：
  ```
  sum(rate(orders_processed_total{status="success"}[5m])) / sum(rate(orders_processed_total[5m]))
  ```

### 8. 高级配置（可选）
- **自定义Buckets**：调整直方图的分桶策略以更精确监控延迟分布：
  ```java
  Timer.builder("order.processing.duration")
      .serviceLevelObjectives(Duration.ofMillis(100), Duration.ofMillis(500))
      .register(registry);
  ```

- **标签扩展**：根据业务需求添加更多维度标签（如订单类型）：
  ```java
  .tag("order_type", order.getType())
  ```

### 验证步骤
1. 启动应用后访问 `http://localhost:8080/actuator/prometheus`，确认指标存在。
2. 在Prometheus控制台执行查询验证数据收集。
3. 在Grafana中配置仪表板查看实时监控数据。

通过以上步骤，你可以实现对订单处理速度的实时监控，并通过Prometheus和Grafana进行可视化及告警配置。



@Configuration
@EnableAuthorizationServer
public class OAuth2AuthServerConfig extends AuthorizationServerConfigurerAdapter {
    @Autowired
    private AuthenticationManager authenticationManager;

    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients.inMemory()
            .withClient("mu_client")
            .secret("{noop}mu_secret")
            .authorizedGrantTypes("password", "refresh_token")
            .scopes("read", "write")
            .accessTokenValiditySeconds(3600);
    }

    @Bean
    public TokenStore tokenStore() {
        return new JwtTokenStore(jwtAccessTokenConverter());
    }

    @Bean
    public JwtAccessTokenConverter jwtAccessTokenConverter() {
        JwtAccessTokenConverter converter = new JwtAccessTokenConverter();
        converter.setSigningKey("mu_signing_key");
        return converter;
    }

    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) {
        endpoints
            .tokenStore(tokenStore())
            .authenticationManager(authenticationManager)
            .accessTokenConverter(jwtAccessTokenConverter());
    }
}
@Configuration
@EnableResourceServer
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {
    @Override
    public void configure(HttpSecurity http) throws Exception {
        http
            .anonymous().disable()
            .requestMatcher(new OAuthRequestedMatcher())
            .authorizeRequests()
            .anyRequest().authenticated();
    }

    private static class OAuthRequestedMatcher implements RequestMatcher {
        public boolean matches(HttpServletRequest request) {
            String auth = request.getHeader("Authorization");
            return auth != null && auth.startsWith("Bearer");
        }
    }
}

@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

    @Bean
    public UserDetailsService userDetailsService() {
        return username -> new User(username, "{noop}password", 
            AuthorityUtils.createAuthorityList("ROLE_USER"));
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService());
    }
}
public class MuRequestWrapper implements HttpServletRequest {
    private final MuRequest rawRequest;
    private final Map<String, String> headers = new HashMap<>();

    public MuRequestWrapper(MuRequest request) {
        this.rawRequest = request;
        request.getHeaders().forEach(h -> headers.put(h.getName(), h.getValue()));
    }

    // 实现关键方法
    @Override
    public String getHeader(String name) {
        return headers.getOrDefault(name, "");
    }

    @Override
    public String getMethod() {
        return rawRequest.getMethod().name();
    }
}

public class MuResponseWrapper implements HttpServletResponse {
    private final MuResponse rawResponse;
    
    public MuResponseWrapper(MuResponse response) {
        this.rawResponse = response;
    }

    @Override
    public void setStatus(int sc) {
        rawResponse.setStatus(sc);
    }

    @Override
    public void addHeader(String name, String value) {
        rawResponse.getHeaders().add(name, value);
    }
}
public class OAuth2Handler {
    private final FilterChainProxy springSecurityFilterChain;

    public OAuth2Handler(ApplicationContext context) {
        this.springSecurityFilterChain = context.getBean(FilterChainProxy.class);
    }

    public boolean handleRequest(MuRequest request, MuResponse response) {
        try {
            HttpServletRequest servletRequest = new MuRequestWrapper(request);
            HttpServletResponse servletResponse = new MuResponseWrapper(response);
            
            springSecurityFilterChain.doFilter(
                servletRequest, 
                servletResponse,
                (req, res) -> {} // 空实现的过滤器链结束回调
            );
            return true;
        } catch (Exception e) {
            response.write("Authentication failed: " + e.getMessage());
            return false;
        }
    }
}
public class MuOAuth2Server {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();
        context.register(SecurityConfig.class, OAuth2AuthServerConfig.class);
        context.refresh();

        OAuth2Handler handler = new OAuth2Handler(context);
        
        MuServer server = MuServerBuilder.muServer()
            .withHttpPort(8080)
            .addHandler((req, res) -> {
                if (req.getUri().startsWith("/api/")) {
                    return handler.handleRequest(req, res);
                }
                return false;
            })
            .start();
    }
}
<dependencies>
    <!-- Spring Security OAuth2核心库 -->
    <dependency>
        <groupId>org.springframework.security.oauth</groupId>
        <artifactId>spring-security-oauth2</artifactId>
        <version>2.5.2.RELEASE</version>
    </dependency>

    <!-- Mu Server -->
    <dependency>
        <groupId>com.mu-server</groupId>
        <artifactId>mu-server-core</artifactId>
        <version>2.5.0</version>
    </dependency>

    <!-- JWT支持 -->
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-jwt</artifactId>
        <version>1.1.1.RELEASE</version>
    </dependency>
</dependencies>


