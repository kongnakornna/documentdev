
### ✅ **การ Monitor และ Logging Stack แบบมืออาชีพ**

## **1. Three Pillars of Observability**
```
1. **Metrics** - ตัวเลขวัด performance (CPU, Memory, Request rate)
2. **Logs** - เหตุการณ์ที่บันทึกตามเวลา
3. **Traces** - ติดตาม request ผ่าน distributed systems
```

## **2. Monitoring Stack Architecture**

### **2.1 Modern Monitoring Stack**
```yaml
# docker-compose.monitoring.yml
version: '3.8'
services:
  # Metrics Collection
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports: ["9090:9090"]
  
  # Metrics Visualization
  grafana:
    image: grafana/grafana
    ports: ["3000:3000"]
    environment:
      GF_SECURITY_ADMIN_PASSWORD: admin
  
  # Log Aggregation
  loki:
    image: grafana/loki
    ports: ["3100:3100"]
  
  promtail:
    image: grafana/promtail
    volumes:
      - /var/log:/var/log
      - ./promtail-config.yml:/etc/promtail/config.yml
  
  # Distributed Tracing
  jaeger:
    image: jaegertracing/all-in-one
    ports: ["16686:16686"]
  
  # Alert Management
  alertmanager:
    image: prom/alertmanager
    ports: ["9093:9093"]
  
  # Uptime Monitoring
  uptime-kuma:
    image: louislam/uptime-kuma
    ports: ["3001:3001"]
```

## **3. Logging Stack ระดับ Production**

### **3.1 Structured Logging Pattern**
```python
# logging_config.py
import json
import logging
import sys
from pythonjsonlogger import jsonlogger
from datetime import datetime
from typing import Dict, Any, Optional

class StructuredLogger:
    def __init__(self, service_name: str, env: str = "production"):
        self.service_name = service_name
        self.env = env
        self.setup_logging()
    
    def setup_logging(self):
        """Configure structured JSON logging"""
        logger = logging.getLogger()
        logger.setLevel(logging.INFO)
        
        # Remove existing handlers
        logger.handlers.clear()
        
        # Console handler (JSON format)
        console_handler = logging.StreamHandler(sys.stdout)
        formatter = jsonlogger.JsonFormatter(
            fmt='%(asctime)s %(name)s %(levelname)s %(message)s',
            datefmt='%Y-%m-%dT%H:%M:%S%z',
            rename_fields={
                'asctime': 'timestamp',
                'name': 'service',
                'levelname': 'level',
                'message': 'message'
            }
        )
        console_handler.setFormatter(formatter)
        logger.addHandler(console_handler)
        
        # File handler (optional)
        file_handler = logging.FileHandler(f'{self.service_name}.log')
        file_handler.setFormatter(formatter)
        logger.addHandler(file_handler)
    
    def log_with_context(
        self,
        level: str,
        message: str,
        event_type: str,
        correlation_id: Optional[str] = None,
        user_id: Optional[str] = None,
        additional_fields: Optional[Dict[str, Any]] = None
    ):
        """Log with structured context"""
        extra_fields = {
            'service': self.service_name,
            'environment': self.env,
            'event_type': event_type,
            'correlation_id': correlation_id or self._generate_correlation_id(),
            'user_id': user_id,
            'timestamp': datetime.utcnow().isoformat() + 'Z',
        }
        
        if additional_fields:
            extra_fields.update(additional_fields)
        
        log_method = getattr(logging, level.lower())
        log_method(message, extra={'extra': extra_fields})
    
    def _generate_correlation_id(self) -> str:
        """Generate unique correlation ID for request tracing"""
        import uuid
        return str(uuid.uuid4())
    
    # Convenience methods
    def info(self, message: str, **kwargs):
        self.log_with_context('INFO', message, **kwargs)
    
    def error(self, message: str, error: Optional[Exception] = None, **kwargs):
        error_context = {}
        if error:
            error_context = {
                'error_type': type(error).__name__,
                'error_message': str(error),
                'stack_trace': self._format_stack_trace(error)
            }
        
        if 'additional_fields' not in kwargs:
            kwargs['additional_fields'] = {}
        kwargs['additional_fields'].update(error_context)
        
        self.log_with_context('ERROR', message, **kwargs)
    
    def _format_stack_trace(self, error: Exception) -> str:
        import traceback
        return ''.join(traceback.format_exception(
            type(error), error, error.__traceback__
        ))

# Usage
logger = StructuredLogger(service_name="payment-service", env="production")

# Example usage in business logic
def process_payment(user_id: str, amount: float, payment_method: str):
    correlation_id = logger._generate_correlation_id()
    
    logger.info(
        "Payment processing started",
        event_type="payment_started",
        user_id=user_id,
        correlation_id=correlation_id,
        additional_fields={
            'amount': amount,
            'payment_method': payment_method,
            'step': 'validation'
        }
    )
    
    try:
        # Business logic
        logger.info(
            "Payment validated",
            event_type="payment_validated",
            user_id=user_id,
            correlation_id=correlation_id,
            additional_fields={'step': 'processing'}
        )
        
        # Simulate error
        if amount > 10000:
            raise ValueError("Amount exceeds limit")
            
    except Exception as e:
        logger.error(
            "Payment processing failed",
            event_type="payment_failed",
            error=e,
            user_id=user_id,
            correlation_id=correlation_id,
            additional_fields={
                'amount': amount,
                'error_code': 'LIMIT_EXCEEDED'
            }
        )
        raise
```

### **3.2 ELK Stack Implementation**
```yaml
# docker-compose.elk.yml
version: '3.8'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.10.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - "9200:9200"
    volumes:
      - elasticsearch-data:/usr/share/elasticsearch/data

  logstash:
    image: docker.elastic.co/logstash/logstash:8.10.0
    ports:
      - "5000:5000"  # TCP input
      - "5001:5001/udp"  # UDP input
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
      - ./patterns:/usr/share/logstash/patterns
    depends_on:
      - elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:8.10.0
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    depends_on:
      - elasticsearch

volumes:
  elasticsearch-data:
```

```conf
# logstash.conf
input {
  # Multiple input sources
  tcp {
    port => 5000
    codec => json_lines
  }
  
  udp {
    port => 5001
    codec => json_lines
  }
  
  beats {
    port => 5044
  }
  
  # File input for legacy systems
  file {
    path => "/var/log/*.log"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  # Parse JSON logs
  if [message] =~ /^{.*}$/ {
    json {
      source => "message"
      target => "parsed_json"
    }
  }
  
  # Add metadata
  mutate {
    add_field => {
      "[@metadata][received_at]" => "%{@timestamp}"
      "[@metadata][host]" => "%{host}"
    }
  }
  
  # Grok patterns for unstructured logs
  grok {
    match => { "message" => "%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:level} %{GREEDYDATA:log_message}" }
  }
  
  # GeoIP for IP addresses
  if [client_ip] {
    geoip {
      source => "client_ip"
      target => "geoip"
    }
  }
  
  # User agent parsing
  if [user_agent] {
    useragent {
      source => "user_agent"
      target => "user_agent_info"
    }
  }
  
  # Remove sensitive data
  mutate {
    remove_field => ["password", "credit_card", "ssn", "auth_token"]
  }
}

output {
  # Primary output to Elasticsearch
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "logs-%{+YYYY.MM.dd}"
    document_type => "_doc"
  }
  
  # Secondary output for debugging
  stdout {
    codec => rubydebug
  }
  
  # Archive to S3
  if [type] == "archive" {
    s3 {
      access_key_id => "AWS_ACCESS_KEY"
      secret_access_key => "AWS_SECRET_KEY"
      region => "us-east-1"
      bucket => "log-archive-bucket"
      time_file => 5
      prefix => "logs/%{+YYYY}/%{+MM}/%{+dd}/"
    }
  }
}
```

## **4. Metrics Monitoring**

### **4.1 Prometheus + Grafana Setup**
```yaml
# prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

rule_files:
  - "alert_rules.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['alertmanager:9093']

scrape_configs:
  # Monitor Prometheus itself
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  
  # Node exporters for system metrics
  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']
  
  # Application metrics
  - job_name: 'web-api'
    static_configs:
      - targets: ['web-api:8080']
    metrics_path: '/metrics'
    
  # Database metrics
  - job_name: 'postgres'
    static_configs:
      - targets: ['postgres-exporter:9187']
  
  # Custom business metrics
  - job_name: 'business-metrics'
    static_configs:
      - targets: ['metrics-exporter:9091']
```

```python
# metrics_collector.py
from prometheus_client import (
    Counter, Gauge, Histogram, Summary, 
    start_http_server, generate_latest, CONTENT_TYPE_LATEST
)
import time
from typing import Dict, Any
from functools import wraps

class BusinessMetrics:
    """Custom business metrics collector"""
    
    def __init__(self):
        # Request metrics
        self.request_count = Counter(
            'http_requests_total',
            'Total HTTP requests',
            ['method', 'endpoint', 'status']
        )
        
        self.request_duration = Histogram(
            'http_request_duration_seconds',
            'HTTP request duration in seconds',
            ['method', 'endpoint'],
            buckets=[0.1, 0.5, 1.0, 2.0, 5.0]
        )
        
        self.request_size = Summary(
            'http_request_size_bytes',
            'HTTP request size in bytes',
            ['method', 'endpoint']
        )
        
        # Business metrics
        self.payments_processed = Counter(
            'payments_processed_total',
            'Total payments processed',
            ['currency', 'status']
        )
        
        self.active_users = Gauge(
            'active_users_current',
            'Current number of active users'
        )
        
        self.cart_size = Histogram(
            'cart_size_items',
            'Number of items in shopping cart',
            buckets=[1, 3, 5, 10, 20]
        )
        
        # System metrics
        self.database_connections = Gauge(
            'database_connections_current',
            'Current database connections',
            ['database', 'state']
        )
        
        self.queue_length = Gauge(
            'message_queue_length',
            'Current message queue length',
            ['queue_name']
        )
        
        # Error metrics
        self.error_count = Counter(
            'application_errors_total',
            'Total application errors',
            ['error_type', 'service']
        )
    
    def track_request(self, method: str, endpoint: str):
        """Decorator to track HTTP requests"""
        def decorator(func):
            @wraps(func)
            def wrapper(*args, **kwargs):
                start_time = time.time()
                
                try:
                    response = func(*args, **kwargs)
                    status = 'success'
                    self.request_count.labels(
                        method=method, 
                        endpoint=endpoint, 
                        status='200'
                    ).inc()
                    return response
                except Exception as e:
                    status = 'error'
                    self.request_count.labels(
                        method=method, 
                        endpoint=endpoint, 
                        status='500'
                    ).inc()
                    
                    self.error_count.labels(
                        error_type=type(e).__name__,
                        service='web-api'
                    ).inc()
                    raise
                finally:
                    duration = time.time() - start_time
                    self.request_duration.labels(
                        method=method, 
                        endpoint=endpoint
                    ).observe(duration)
            return wrapper
        return decorator
    
    def record_payment(self, amount: float, currency: str, success: bool):
        """Record payment metrics"""
        status = 'success' if success else 'failed'
        self.payments_processed.labels(
            currency=currency, 
            status=status
        ).inc()
        
        if success:
            # Record revenue
            revenue_gauge = Gauge(
                'revenue_total',
                'Total revenue',
                ['currency']
            )
            revenue_gauge.labels(currency=currency).inc(amount)
    
    def update_active_users(self, count: int):
        """Update active users gauge"""
        self.active_users.set(count)
    
    def update_database_connections(self, db_name: str, active: int, idle: int):
        """Update database connection metrics"""
        self.database_connections.labels(
            database=db_name, 
            state='active'
        ).set(active)
        
        self.database_connections.labels(
            database=db_name, 
            state='idle'
        ).set(idle)

# Usage example
metrics = BusinessMetrics()

# Start metrics server
start_http_server(9091)

# In Flask/Django middleware
@app.before_request
def before_request():
    request.start_time = time.time()

@app.after_request
def after_request(response):
    if hasattr(request, 'start_time'):
        duration = time.time() - request.start_time
        metrics.request_duration.labels(
            method=request.method,
            endpoint=request.path
        ).observe(duration)
    
    metrics.request_count.labels(
        method=request.method,
        endpoint=request.path,
        status=response.status_code
    ).inc()
    
    return response
```

### **4.2 Grafana Dashboard Configuration**
```json
{
  "dashboard": {
    "title": "Production Monitoring",
    "panels": [
      {
        "title": "System Health",
        "type": "stat",
        "targets": [{
          "expr": "100 - (avg(rate(node_cpu_seconds_total{mode=\"idle\"}[5m])) * 100)",
          "legendFormat": "CPU Usage"
        }],
        "thresholds": {
          "steps": [
            {"color": "green", "value": null},
            {"color": "yellow", "value": 70},
            {"color": "red", "value": 90}
          ]
        }
      },
      {
        "title": "Application Requests",
        "type": "graph",
        "targets": [{
          "expr": "sum(rate(http_requests_total[5m])) by (endpoint)",
          "legendFormat": "{{endpoint}}"
        }]
      },
      {
        "title": "Error Rate",
        "type": "graph",
        "targets": [{
          "expr": "sum(rate(http_requests_total{status=~\"5..\"}[5m])) / sum(rate(http_requests_total[5m])) * 100",
          "legendFormat": "Error Rate %"
        }],
        "thresholds": {
          "steps": [
            {"color": "green", "value": null},
            {"color": "yellow", "value": 1},
            {"color": "red", "value": 5}
          ]
        }
      },
      {
        "title": "Business Metrics",
        "type": "table",
        "targets": [{
          "expr": "payments_processed_total",
          "instant": true,
          "format": "table"
        }]
      }
    ],
    "time": {
      "from": "now-6h",
      "to": "now"
    },
    "refresh": "30s"
  }
}
```

## **5. Distributed Tracing**

### **5.1 OpenTelemetry Implementation**
```python
# tracing_config.py
from opentelemetry import trace
from opentelemetry.exporter.jaeger.thrift import JaegerExporter
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import (
    BatchSpanProcessor,
    ConsoleSpanExporter,
)
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.instrumentation.redis import RedisInstrumentor
from opentelemetry.instrumentation.sqlalchemy import SQLAlchemyInstrumentor

def setup_tracing(service_name: str, endpoint: str = "http://jaeger:14268/api/traces"):
    """Setup distributed tracing with OpenTelemetry"""
    
    # Create tracer provider
    trace.set_tracer_provider(TracerProvider())
    tracer_provider = trace.get_tracer_provider()
    
    # Configure exporters
    jaeger_exporter = JaegerExporter(
        agent_host_name="jaeger",
        agent_port=6831,
    )
    
    # Or use OTLP for newer systems
    otlp_exporter = OTLPSpanExporter(
        endpoint=endpoint,
        insecure=True,
    )
    
    # Console exporter for development
    console_exporter = ConsoleSpanExporter()
    
    # Add processors
    span_processor = BatchSpanProcessor(jaeger_exporter)
    tracer_provider.add_span_processor(span_processor)
    
    # Add console processor for development
    if os.getenv("ENVIRONMENT") == "development":
        tracer_provider.add_span_processor(
            BatchSpanProcessor(console_exporter)
        )
    
    # Auto-instrumentation
    FlaskInstrumentor().instrument()
    RequestsInstrumentor().instrument()
    RedisInstrumentor().instrument()
    SQLAlchemyInstrumentor().instrument()
    
    return trace.get_tracer(service_name)

# Usage in application
tracer = setup_tracing("payment-service")

def process_order(order_id: str):
    with tracer.start_as_current_span("process_order") as span:
        # Add attributes to span
        span.set_attribute("order.id", order_id)
        span.set_attribute("service.name", "payment-service")
        
        # Add events
        span.add_event("order_processing_started")
        
        try:
            # Business logic
            validate_order(order_id)
            
            with tracer.start_as_current_span("process_payment"):
                process_payment(order_id)
            
            with tracer.start_as_current_span("update_inventory"):
                update_inventory(order_id)
            
            span.add_event("order_processing_completed")
            span.set_status(trace.Status(trace.StatusCode.OK))
            
        except Exception as e:
            # Record error in span
            span.record_exception(e)
            span.set_status(trace.Status(
                trace.StatusCode.ERROR,
                str(e)
            ))
            raise

# Context propagation
from opentelemetry.propagate import inject, extract
from opentelemetry.trace.propagation.tracecontext import TraceContextTextMapPropagator

def make_external_request(url: str, data: dict):
    headers = {}
    
    # Inject trace context into headers
    inject(headers)
    
    # Make request with headers
    response = requests.post(
        url,
        json=data,
        headers=headers
    )
    
    return response

def receive_external_request(request):
    # Extract trace context from incoming request
    carrier = dict(request.headers)
    context = extract(carrier)
    
    # Start span with extracted context
    with tracer.start_as_current_span(
        "handle_external_request",
        context=context
    ):
        # Process request
        pass
```

## **6. Alerting System**

### **6.1 Prometheus Alert Rules**
```yaml
# alert_rules.yml
groups:
  - name: system_alerts
    rules:
      # High CPU usage
      - alert: HighCPUUsage
        expr: 100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
        for: 5m
        labels:
          severity: warning
          team: infra
        annotations:
          summary: "High CPU usage on {{ $labels.instance }}"
          description: "CPU usage is above 80% for 5 minutes"
      
      # High memory usage
      - alert: HighMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100 > 85
        for: 5m
        labels:
          severity: warning
          team: infra
        annotations:
          summary: "High memory usage on {{ $labels.instance }}"
          description: "Memory usage is above 85% for 5 minutes"
  
  - name: application_alerts
    rules:
      # High error rate
      - alert: HighErrorRate
        expr: sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) * 100 > 5
        for: 2m
        labels:
          severity: critical
          team: backend
        annotations:
          summary: "High error rate on {{ $labels.endpoint }}"
          description: "Error rate is above 5% for 2 minutes"
      
      # Slow response time
      - alert: SlowResponseTime
        expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 2
        for: 5m
        labels:
          severity: warning
          team: backend
        annotations:
          summary: "Slow response time on {{ $labels.endpoint }}"
          description: "95th percentile response time is above 2 seconds"
      
      # Business alerts
      - alert: PaymentProcessingDown
        expr: rate(payments_processed_total[10m]) == 0
        for: 5m
        labels:
          severity: critical
          team: payments
        annotations:
          summary: "Payment processing is down"
          description: "No payments processed in the last 10 minutes"
      
      # Custom business alert
      - alert: RevenueDrop
        expr: delta(revenue_total[1h]) < -10000
        labels:
          severity: warning
          team: business
        annotations:
          summary: "Revenue drop detected"
          description: "Revenue dropped by more than 10,000 in the last hour"
```

### **6.2 Alert Manager Configuration**
```yaml
# alertmanager.yml
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'alerts@yourcompany.com'
  smtp_auth_username: 'alerts@yourcompany.com'
  smtp_auth_password: '${SMTP_PASSWORD}'

route:
  group_by: ['alertname', 'severity']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h
  receiver: 'default-receiver'
  
  routes:
    - match:
        severity: critical
      receiver: 'pagerduty'
      continue: true
    
    - match:
        team: infra
      receiver: 'slack-infra'
    
    - match:
        team: backend
      receiver: 'slack-backend'
    
    - match:
        team: business
      receiver: 'email-business'

receivers:
  - name: 'default-receiver'
    webhook_configs:
      - url: 'http://webhook-receiver:8080/alerts'
  
  - name: 'pagerduty'
    pagerduty_configs:
      - service_key: '${PAGERDUTY_KEY}'
  
  - name: 'slack-infra'
    slack_configs:
      - channel: '#alerts-infra'
        api_url: '${SLACK_WEBHOOK_URL}'
        title: 'Infrastructure Alert'
        text: '{{ range .Alerts }}*Alert:* {{ .Annotations.summary }}\n*Description:* {{ .Annotations.description }}\n{{ end }}'
  
  - name: 'slack-backend'
    slack_configs:
      - channel: '#alerts-backend'
        api_url: '${SLACK_WEBHOOK_URL}'
  
  - name: 'email-business'
    email_configs:
      - to: 'business-team@yourcompany.com'
        subject: 'Business Alert: {{ .GroupLabels.alertname }}'
        html: '{{ template "email.business.html" . }}'

templates:
  - '/etc/alertmanager/templates/*.tmpl'
```

## **7. Log Analysis & Visualization**

### **7.1 Kibana/Logstash Dashboards**
```json
{
  "visualization": {
    "title": "Log Analysis Dashboard",
    "visState": {
      "type": "histogram",
      "params": {
        "addLegend": true,
        "addTimeMarker": true,
        "categoryAxes": [{
          "id": "CategoryAxis-1",
          "type": "category"
        }]
      },
      "aggs": [
        {
          "id": "1",
          "enabled": true,
          "type": "count",
          "schema": "metric",
          "params": {}
        },
        {
          "id": "2",
          "enabled": true,
          "type": "date_histogram",
          "schema": "segment",
          "params": {
            "field": "@timestamp",
            "interval": "auto"
          }
        },
        {
          "id": "3",
          "enabled": true,
          "type": "terms",
          "schema": "group",
          "params": {
            "field": "level.keyword",
            "size": 5
          }
        }
      ]
    },
    "savedSearchId": "log-search"
  }
}
```

### **7.2 Real-time Log Analysis**
```python
# log_analyzer.py
import json
from collections import defaultdict, deque
from typing import Dict, List, Any
from datetime import datetime, timedelta
import re

class RealTimeLogAnalyzer:
    def __init__(self, window_minutes: int = 5):
        self.window_minutes = window_minutes
        self.log_buffer = deque(maxlen=10000)
        self.patterns = self._compile_patterns()
        self.metrics = defaultdict(lambda: defaultdict(int))
        
    def _compile_patterns(self):
        """Compile regex patterns for log analysis"""
        return {
            'error_pattern': re.compile(r'ERROR|CRITICAL|FATAL', re.IGNORECASE),
            'sql_pattern': re.compile(r'SELECT|INSERT|UPDATE|DELETE', re.IGNORECASE),
            'url_pattern': re.compile(r'GET|POST|PUT|DELETE\s+(\S+)'),
            'ip_pattern': re.compile(r'\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}'),
            'email_pattern': re.compile(r'[\w\.-]+@[\w\.-]+\.\w+'),
        }
    
    def analyze_log_entry(self, log_entry: Dict[str, Any]):
        """Analyze a single log entry"""
        timestamp = datetime.fromisoformat(log_entry['timestamp'].replace('Z', ''))
        
        # Remove old entries
        cutoff_time = timestamp - timedelta(minutes=self.window_minutes)
        while self.log_buffer and self.log_buffer[0]['timestamp'] < cutoff_time:
            old_entry = self.log_buffer.popleft()
            self._remove_from_metrics(old_entry)
        
        # Add new entry
        self.log_buffer.append({
            'timestamp': timestamp,
            'entry': log_entry
        })
        
        # Update metrics
        self._update_metrics(log_entry)
        
        # Check for anomalies
        anomalies = self._detect_anomalies(log_entry)
        
        return {
            'metrics': dict(self.metrics),
            'anomalies': anomalies,
            'buffer_size': len(self.log_buffer)
        }
    
    def _update_metrics(self, log_entry: Dict[str, Any]):
        """Update real-time metrics"""
        # Count by level
        level = log_entry.get('level', 'INFO').upper()
        self.metrics['level'][level] += 1
        
        # Count by service
        service = log_entry.get('service', 'unknown')
        self.metrics['service'][service] += 1
        
        # Count errors
        if level in ['ERROR', 'CRITICAL', 'FATAL']:
            self.metrics['errors']['total'] += 1
            
            # Error by type
            error_type = log_entry.get('error_type', 'unknown')
            self.metrics['errors_by_type'][error_type] += 1
        
        # Extract and count URLs
        message = log_entry.get('message', '')
        url_matches = self.patterns['url_pattern'].findall(message)
        for url in url_matches:
            self.metrics['urls'][url] += 1
        
        # Count SQL queries
        if self.patterns['sql_pattern'].search(message):
            self.metrics['sql_queries']['total'] += 1
    
    def _detect_anomalies(self, log_entry: Dict[str, Any]) -> List[Dict[str, Any]]:
        """Detect anomalies in log entry"""
        anomalies = []
        
        # Check for error spikes
        if log_entry.get('level') == 'ERROR':
            error_rate = self.metrics['errors']['total'] / max(len(self.log_buffer), 1)
            if error_rate > 0.1:  # More than 10% error rate
                anomalies.append({
                    'type': 'error_spike',
                    'rate': error_rate,
                    'message': f'High error rate: {error_rate:.2%}'
                })
        
        # Check for suspicious patterns
        message = log_entry.get('message', '')
        if 'password' in message.lower() or 'credit_card' in message.lower():
            anomalies.append({
                'type': 'sensitive_data',
                'message': 'Potential sensitive data in logs'
            })
        
        # Check for SQL injection patterns
        sql_injection_patterns = [
            r'(\'\s*OR\s*\'\w\'=\'\w)',  # SQL injection pattern
            r'(\bDROP\s+TABLE\b)',  # Drop table
            r'(\bUNION\s+SELECT\b)'  # Union select
        ]
        
        for pattern in sql_injection_patterns:
            if re.search(pattern, message, re.IGNORECASE):
                anomalies.append({
                    'type': 'sql_injection',
                    'pattern': pattern,
                    'message': 'Potential SQL injection attempt'
                })
        
        return anomalies
    
    def get_summary(self):
        """Get summary of current log analysis"""
        total_entries = len(self.log_buffer)
        error_count = self.metrics['errors']['total']
        
        return {
            'time_window_minutes': self.window_minutes,
            'total_entries': total_entries,
            'error_rate': error_count / max(total_entries, 1),
            'top_services': sorted(
                self.metrics['service'].items(),
                key=lambda x: x[1],
                reverse=True
            )[:5],
            'top_errors': sorted(
                self.metrics['errors_by_type'].items(),
                key=lambda x: x[1],
                reverse=True
            )[:5],
            'active_anomalies': self._get_active_anomalies()
        }
    
    def _get_active_anomalies(self):
        """Get currently active anomalies"""
        # Implementation depends on your anomaly detection logic
        pass

# Usage
analyzer = RealTimeLogAnalyzer(window_minutes=5)

# Process logs in real-time
for log_line in log_stream:
    log_entry = json.loads(log_line)
    result = analyzer.analyze_log_entry(log_entry)
    
    if result['anomalies']:
        print(f"Anomalies detected: {result['anomalies']}")
    
    # Periodically print summary
    if len(analyzer.log_buffer) % 100 == 0:
        summary = analyzer.get_summary()
        print(f"Summary: {summary}")
```

## **8. Monitoring Best Practices**

### **8.1 Checklist สำหรับ Production Monitoring**
```markdown 
```
## Infrastructure Monitoring
- [ ] CPU/Memory/Disk usage monitoring
- [ ] Network I/O monitoring
- [ ] Database connection pool monitoring
- [ ] Load balancer health checks
- [ ] SSL certificate expiration monitoring

## Application Monitoring
- [ ] HTTP request rate and latency
- [ ] Error rate (4xx, 5xx)
- [ ] Application-specific business metrics
- [ ] Cache hit/miss ratios
- [ ] Queue length and processing time

## Logging
- [ ] Structured JSON logging
- [ ] Correlation IDs for request tracing
- [ ] Sensitive data masking
- [ ] Log retention policy (30-90 days)
- [ ] Log aggregation and centralization

## Alerting
- [ ] Alert thresholds based on SLOs
- [ ] Escalation policies
- [ ] Alert deduplication
- [ ] On-call rotation
- [ ] Alert fatigue management

## Dashboard
- [ ] Service-level dashboards
- [ ] Business metrics dashboards
- [ ] Real-time monitoring
- [ ] Historical trend analysis
- [ ] Mobile-friendly dashboards


### **8.2 SLO/SLI/SLA Definitions**
```python
# slo_monitoring.py
class SLOMonitor:
    def __init__(self):
        self.slos = {
            'availability': {
                'target': 0.9995,  # 99.95%
                'window': '30d',
                'metric': 'uptime'
            },
            'latency': {
                'target': 0.95,  # 95th percentile
                'threshold_ms': 200,
                'window': '7d',
                'metric': 'response_time'
            },
            'error_rate': {
                'target': 0.01,  # 1%
                'window': '1h',
                'metric': 'error_percentage'
            }
        }
    
    def calculate_sli(self, metric: str, data: List[float]) -> float:
        """Calculate Service Level Indicator"""
        if metric == 'availability':
            successful_requests = sum(1 for x in data if x == 1)
            total_requests = len(data)
            return successful_requests / total_requests if total_requests > 0 else 0
        
        elif metric == 'latency':
            # 95th percentile latency
            sorted_data = sorted(data)
            index = int(0.95 * len(sorted_data))
            return sorted_data[index] if sorted_data else 0
        
        elif metric == 'error_rate':
            errors = sum(1 for x in data if x >= 400)
            total = len(data)
            return errors / total if total > 0 else 0
        
        return 0
    
    def check_slo_compliance(self, metric: str, sli_value: float) -> Dict:
        """Check if SLI meets SLO target"""
        slo = self.slos.get(metric)
        if not slo:

