给 Wordpress 站点添加 opentelemetry 支持

## 前置条件
1. php-fpm 安装了 opentelemetry, grpc, protobuf 扩展


## 安装步骤:
1. 下载代码

```bash
cd /path/to/this-repo

git clone https://github.com/coolseven/wordpress-prepend-opentelemetry 
```

2. 下载 composer 依赖

```bash 
cd  wordpress-prepend-opentelemetry 
composer install
composer dump-autoload
```

3. 将 wordpress-prepend-opentelemetry 添加到 php-fpm 的 auto_prepend_file 中

```nginxconf
fastcgi_param PHP_VALUE "auto_prepend_file=\"/var/www/wordpress-prepend-opentelemetry/vendor/autoload.php\"";
```

4. 为 php-fpm 设置 opentelemetry 的运行时环境变量 (以 docker-compose.yml 为例)

```yaml
  php-fpm:
    image: registry.cn-hangzhou.aliyuncs.com/laradock-based/laradock-php-fpm:latest
    volumes:
      - /path/to/this-repo/wordpress-prepend-opentelemetry:/var/www/wordpress-prepend-opentelemetry
    environment:
      - OTEL_PHP_AUTOLOAD_ENABLED=true
      - OTEL_SERVICE_NAME=your-otel-name
      - OTEL_TRACES_EXPORTER=otlp
      - OTEL_METRICS_EXPORTER=none
      - OTEL_LOGS_EXPORTER=none
      - OTEL_EXPORTER_OTLP_PROTOCOL=grpc
      - OTEL_EXPORTER_OTLP_ENDPOINT=your-endpoint
      - OTEL_EXPORTER_OTLP_HEADERS=Authentication=your-key
      - OTEL_PROPAGATORS=baggage,tracecontext
```