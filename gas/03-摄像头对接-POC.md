# 汽车美容服务 - 摄像头对接 POC

> DS-2CD3646WDV3-LZ ISAPI 车牌识别对接验证
> 
> **待验证**：需在实际设备上跑通后确认

## 一、POC 目标

跑通 DS-2CD3646WDV3-LZ 的 ISAPI 车牌识别，验证：

1. ✅ 能否通过 ISAPI 获取车牌识别结果
2. ✅ 能否订阅实时车牌识别事件
3. ✅ 识别准确率和延迟是否满足需求

---

## 二、设备信息

| 项目 | 值 |
|------|-----|
| 型号 | DS-2CD3646WDV3-LZ |
| 协议 | ISAPI（HTTP RESTful） |
| 认证 | Digest Auth |
| 默认端口 | 80（HTTP）/ 443（HTTPS） |
| 功能 | 车牌识别（ANPR）、事件推送 |

### 设备准备清单

| 准备项 | 说明 | 状态 |
|--------|------|------|
| 摄像头到货 | DS-2CD3646WDV3-LZ | [ ] |
| 网络配置 | 固定IP、与服务器同网段 | [ ] |
| 初始化 | 修改默认密码、开启ISAPI | [ ] |
| 测试环境 | 有车牌的测试车辆 | [ ] |

---

## 三、POC 步骤

### Step 1：基础连通性验证

```bash
# 测试摄像头连通性
curl -X GET "http://<CAMERA_IP>/ISAPI/System/deviceInfo" \
  --digest -u admin:password

# 预期响应
<?xml version="1.0" encoding="UTF-8"?>
<DeviceInfo>
  <deviceName>DS-2CD3646WDV3-LZ</deviceName>
  <model>DS-2CD3646WDV3-LZ</model>
  <serialNumber>DS-2CD3646WDV3-LZ20210101AACHXXXXXXX</serialNumber>
  <firmwareVersion>V5.x.x</firmwareVersion>
  ...
</DeviceInfo>
```

**验证点**：
- [ ] 能获取设备信息
- [ ] 设备型号正确
- [ ] 固件版本记录

---

### Step 2：获取车牌识别通道配置

```bash
# 查询智能分析通道
curl -X GET "http://<CAMERA_IP>/ISAPI/Traffic/channels" \
  --digest -u admin:password
```

**预期响应**：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<TrafficChannelList>
  <TrafficChannel>
    <id>1</id>
    <enabled>true</enabled>
    <videoInputChannelID>1</videoInputChannelID>
  </TrafficChannel>
</TrafficChannelList>
```

**验证点**：
- [ ] 通道已启用
- [ ] 通道ID记录

---

### Step 3：配置车牌识别参数（可选）

```bash
# 获取当前配置
curl -X GET "http://<CAMERA_IP>/ISAPI/Traffic/channels/1/vehicleDetect" \
  --digest -u admin:password

# 修改配置（如需要）
curl -X PUT "http://<CAMERA_IP>/ISAPI/Traffic/channels/1/vehicleDetect" \
  --digest -u admin:password \
  -H "Content-Type: application/xml" \
  -d '<?xml version="1.0" encoding="UTF-8"?>
<VehicleDetect>
  <enabled>true</enabled>
  <snapInterval>1000</snapInterval>
</VehicleDetect>'
```

---

### Step 4：触发手动抓拍

```bash
# 手动触发抓拍
curl -X PUT "http://<CAMERA_IP>/ISAPI/Traffic/channels/1/vehicleDetect/manualCapture" \
  --digest -u admin:password \
  -H "Content-Type: application/xml" \
  -d '<ManualCapture><captureNumber>1</captureNumber></ManualCapture>'
```

**预期响应**：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<ManualCaptureResult>
  <requestURL>/ISAPI/Traffic/channels/1/vehicleDetect/manualCapture</requestURL>
  <statusCode>1</statusCode>
  <statusString>OK</statusString>
</ManualCaptureResult>
```

**验证点**：
- [ ] 抓拍命令执行成功
- [ ] 能获取抓拍结果

---

### Step 5：订阅车牌识别事件（推荐方式）

```bash
# 订阅事件流（长连接）
curl -X GET "http://<CAMERA_IP>/ISAPI/Event/notification/alertStream" \
  --digest -u admin:password \
  --no-buffer
```

**事件格式（multipart）**：
```
--boundary
Content-Type: application/json
Content-Length: xxx

{
  "ipAddress": "192.168.1.100",
  "portNo": 80,
  "protocol": "HTTP",
  "macAddress": "xx:xx:xx:xx:xx:xx",
  "channelID": 1,
  "dateTime": "2026-01-19T14:30:00+08:00",
  "activePostCount": 1,
  "eventType": "ANPR",
  "eventState": "active",
  "eventDescription": "ANPR",
  "ANPR": {
    "licensePlate": "粤B12345",
    "plateColor": "blue",
    "plateType": "normal",
    "confidence": 95,
    "direction": "approach",
    "vehicleType": "car"
  }
}
--boundary
Content-Type: image/jpeg
Content-Length: xxx

<binary image data>
--boundary--
```

**验证点**：
- [ ] 长连接建立成功
- [ ] 车辆经过时能收到事件
- [ ] 事件包含车牌号
- [ ] 事件包含图片

---

### Step 6：解析事件数据

Python 测试脚本：

```python
import requests
from requests.auth import HTTPDigestAuth
import re

def subscribe_camera(camera_ip, username, password):
    url = f"http://{camera_ip}/ISAPI/Event/notification/alertStream"
    
    with requests.get(
        url, 
        auth=HTTPDigestAuth(username, password),
        stream=True
    ) as response:
        buffer = b""
        for chunk in response.iter_content(chunk_size=1024):
            buffer += chunk
            # 解析 multipart 边界
            if b"--boundary" in buffer:
                parts = buffer.split(b"--boundary")
                for part in parts:
                    if b"application/json" in part:
                        # 提取 JSON 部分
                        json_match = re.search(b'{.*}', part, re.DOTALL)
                        if json_match:
                            event_json = json_match.group().decode('utf-8')
                            print(f"收到事件: {event_json}")
                            # 解析车牌
                            import json
                            event = json.loads(event_json)
                            if 'ANPR' in event:
                                plate = event['ANPR']['licensePlate']
                                confidence = event['ANPR']['confidence']
                                print(f"识别车牌: {plate}, 置信度: {confidence}%")
                buffer = parts[-1] if parts else b""

if __name__ == "__main__":
    subscribe_camera("192.168.1.100", "admin", "your_password")
```

---

## 四、Java 集成代码

### 4.1 摄像头服务类

```java
@Service
@Slf4j
public class HikCameraService {
    
    private final WebClient webClient;
    private final StationService stationService;
    private final Map<Long, Disposable> subscriptions = new ConcurrentHashMap<>();
    
    /**
     * 订阅摄像头事件（启动时调用）
     */
    @PostConstruct
    public void subscribeAllCameras() {
        List<Station> stations = stationService.findAllWithCamera();
        for (Station station : stations) {
            subscribeCamera(station);
        }
        log.info("已订阅 {} 个摄像头", stations.size());
    }
    
    /**
     * 订阅单个摄像头
     */
    public void subscribeCamera(Station station) {
        String cameraIp = station.getCameraIp();
        Long stationId = station.getId();
        
        // 取消已有订阅
        if (subscriptions.containsKey(stationId)) {
            subscriptions.get(stationId).dispose();
        }
        
        String url = String.format("http://%s/ISAPI/Event/notification/alertStream", cameraIp);
        
        Disposable subscription = webClient.get()
            .uri(url)
            .headers(h -> {
                // 注意：实际需要实现 Digest Auth
                String auth = "admin:password";
                h.setBasicAuth(auth);
            })
            .retrieve()
            .bodyToFlux(DataBuffer.class)
            .map(this::parseMultipart)
            .filter(Objects::nonNull)
            .subscribe(
                event -> handlePlateEvent(event, stationId),
                error -> {
                    log.error("摄像头订阅异常: stationId={}, error={}", stationId, error.getMessage());
                    // 5秒后重连
                    scheduleReconnect(station, 5000);
                }
            );
        
        subscriptions.put(stationId, subscription);
        log.info("已订阅摄像头: stationId={}, ip={}", stationId, cameraIp);
    }
    
    /**
     * 处理车牌识别事件
     */
    private void handlePlateEvent(PlateEvent event, Long stationId) {
        log.info("摄像头识别车牌: stationId={}, plate={}, confidence={}",
            stationId, event.getPlateNo(), event.getConfidence());
        
        // 更新工位当前车牌
        stationService.updateCurrentPlate(
            stationId, 
            event.getPlateNo(), 
            event.getImageUrl(),
            event.getCaptureTime()
        );
    }
    
    /**
     * 解析 multipart 数据
     */
    private PlateEvent parseMultipart(DataBuffer buffer) {
        // 解析 multipart 格式，提取 JSON 和图片
        // 实现略...
        return null;
    }
    
    /**
     * 重连调度
     */
    private void scheduleReconnect(Station station, long delayMs) {
        CompletableFuture.delayedExecutor(delayMs, TimeUnit.MILLISECONDS)
            .execute(() -> subscribeCamera(station));
    }
    
    @PreDestroy
    public void cleanup() {
        subscriptions.values().forEach(Disposable::dispose);
    }
}
```

### 4.2 事件实体类

```java
@Data
public class PlateEvent {
    private String cameraId;
    private String plateNo;
    private String plateColor;
    private Integer confidence;
    private LocalDateTime captureTime;
    private String imageUrl;      // 存储到 OSS 后的 URL
    private byte[] imageData;     // 原始图片数据
}
```

### 4.3 Digest Auth 实现

```java
@Component
public class DigestAuthInterceptor implements ExchangeFilterFunction {
    
    @Override
    public Mono<ClientResponse> filter(ClientRequest request, ExchangeFunction next) {
        // 第一次请求获取 nonce
        return next.exchange(request)
            .flatMap(response -> {
                if (response.statusCode().value() == 401) {
                    String wwwAuth = response.headers().asHttpHeaders()
                        .getFirst("WWW-Authenticate");
                    // 解析 Digest 参数并计算响应
                    String authHeader = calculateDigestAuth(request, wwwAuth);
                    ClientRequest authRequest = ClientRequest.from(request)
                        .header("Authorization", authHeader)
                        .build();
                    return next.exchange(authRequest);
                }
                return Mono.just(response);
            });
    }
    
    private String calculateDigestAuth(ClientRequest request, String wwwAuth) {
        // 实现 Digest Auth 计算
        // 解析 realm, nonce, qop 等参数
        // 计算 HA1, HA2, response
        return "Digest username=..., realm=..., nonce=..., ...";
    }
}
```

---

## 五、POC 验收标准

| 验收项 | 标准 | 测试方法 | 状态 |
|--------|------|----------|------|
| 连通性 | 能获取设备信息 | curl 命令 | [ ] |
| 抓拍 | 手动触发抓拍返回车牌 | curl 命令 | [ ] |
| 事件订阅 | 车辆经过时能收到事件推送 | Python 脚本 | [ ] |
| 识别率（白天） | > 95% | 10辆车测试 | [ ] |
| 识别率（夜间） | > 90% | 10辆车测试 | [ ] |
| 延迟 | 车辆入位到收到事件 < 2秒 | 计时测试 | [ ] |
| 稳定性 | 长连接保持 > 24小时不断 | 持续监控 | [ ] |
| 重连 | 断网恢复后自动重连 | 手动断网测试 | [ ] |

---

## 六、备选方案

如果 ISAPI 事件订阅不稳定，备选方案：

### 方案 A：轮询模式

```java
@Scheduled(fixedRate = 1000)  // 每秒轮询
public void pollPlateResult() {
    for (Station station : stations) {
        String url = String.format(
            "http://%s/ISAPI/Traffic/channels/1/vehicleDetect/plates",
            station.getCameraIp()
        );
        // 获取最新识别结果
        // 对比上次结果，有新车牌则处理
    }
}
```

| 优点 | 缺点 |
|------|------|
| 简单可靠 | 有 1 秒延迟 |
| 无需长连接 | 轮询压力 |

### 方案 B：海康 SDK

使用官方 Java SDK（HCNetSDK）：

```xml
<dependency>
    <groupId>com.hikvision</groupId>
    <artifactId>hcnetsdk</artifactId>
    <version>6.1.x</version>
</dependency>
```

| 优点 | 缺点 |
|------|------|
| 功能全面 | 依赖重（需 native 库） |
| 官方支持 | 跨平台兼容性 |

### 方案 C：萤石云

通过萤石云平台获取事件：

| 优点 | 缺点 |
|------|------|
| 无需本地对接 | 需订阅费用 |
| 管理方便 | 依赖外网 |

---

## 七、问题排查指南

### 常见问题

| 问题 | 可能原因 | 解决方案 |
|------|----------|----------|
| 401 Unauthorized | 密码错误或未使用 Digest Auth | 检查认证方式 |
| 连接超时 | 网络不通 | 检查 IP 和防火墙 |
| 无事件推送 | 车牌识别未开启 | 登录 Web 管理页检查配置 |
| 识别率低 | 光线、角度问题 | 调整摄像头位置 |
| 长连接断开 | 网络不稳定 | 实现自动重连 |

### 调试命令

```bash
# 测试网络连通性
ping <CAMERA_IP>

# 测试 HTTP 端口
telnet <CAMERA_IP> 80

# 查看摄像头能力
curl -X GET "http://<CAMERA_IP>/ISAPI/System/capabilities" \
  --digest -u admin:password

# 查看事件订阅状态
curl -X GET "http://<CAMERA_IP>/ISAPI/Event/notification/httpHosts" \
  --digest -u admin:password
```

---

## 八、待办事项

| # | 任务 | 负责人 | 状态 |
|---|------|--------|------|
| 1 | 采购摄像头 | - | [ ] |
| 2 | 网络环境准备 | - | [ ] |
| 3 | 执行 POC 步骤 | - | [ ] |
| 4 | 记录验收结果 | - | [ ] |
| 5 | 确定最终方案 | - | [ ] |
| 6 | 完成 Java 集成 | - | [ ] |
