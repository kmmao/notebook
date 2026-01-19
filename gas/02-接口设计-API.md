# 汽车美容服务 - 接口设计（API）

> 各端 API 接口文档
> 
> **待对接**：需与加油站平台现有接口规范互证后调整

## 一、接口规范

### 1.1 通用约定

| 项目 | 规范 |
|------|------|
| 协议 | HTTPS |
| 数据格式 | JSON |
| 编码 | UTF-8 |
| 时间格式 | `yyyy-MM-dd HH:mm:ss` |
| 认证方式 | Token（待与现有系统对齐） |

### 1.2 响应格式

```json
{
  "code": 0,           // 0=成功，非0=失败
  "msg": "success",    // 提示信息
  "data": {}           // 业务数据
}
```

### 1.3 错误码定义

| 错误码 | 说明 |
|--------|------|
| 0 | 成功 |
| 400 | 参数错误 |
| 401 | 未登录 |
| 403 | 无权限 |
| 404 | 资源不存在 |
| 500 | 服务器错误 |
| 1001 | 券已使用 |
| 1002 | 券已过期 |
| 1003 | 工位不可用 |
| 1004 | 库存不足 |

**待确认**：
- [ ] 是否复用现有系统错误码体系？
- [ ] Token 认证机制是否一致？

---

## 二、用户端 API（微信H5/小程序）

> 基础路径：`/api/user`

### 2.1 券相关

#### 获取我的洗车券列表

```
GET /api/user/coupons
```

**请求参数**：无

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "valid": [
      {
        "id": 1,
        "couponNo": "C202601190001",
        "type": "WASH",
        "typeName": "洗车券",
        "expireTime": "2026-02-19",
        "source": "GIFT",
        "sourceName": "加油满赠"
      }
    ],
    "used": [],
    "expired": []
  }
}
```

#### 获取券详情

```
GET /api/user/coupons/{id}
```

---

### 2.2 扫码消券

#### 扫员工码消券

```
POST /api/user/scan/use-coupon
```

**请求参数**：
```json
{
  "empQrCode": "EMP_QR_xxxxx",     // 员工二维码内容
  "couponId": 123,                 // 选择的券ID
  "plateNo": "粤B12345"            // 确认的车牌
}
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "orderId": 10001,
    "orderNo": "WO202601190001",
    "stationName": "1号位",
    "status": "CHECKING"
  }
}
```

**错误场景**：
| 场景 | code | msg |
|------|------|-----|
| 券已使用 | 1001 | 该券已使用 |
| 券已过期 | 1002 | 该券已过期 |
| 员工码无效 | 400 | 无效的员工码 |

---

### 2.3 排队

#### 扫码取号

```
POST /api/user/queue/take
```

**请求参数**：
```json
{
  "siteId": 1
}
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "queueId": 100,
    "queueNo": "A015",
    "position": 5,           // 前面还有5人
    "estimateWait": 25       // 预计等待25分钟
  }
}
```

#### 查询排队状态

```
GET /api/user/queue/status
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "queueId": 100,
    "queueNo": "A015",
    "status": "WAITING",
    "position": 3,
    "estimateWait": 15
  }
}
```

#### 取消排队

```
DELETE /api/user/queue/{id}
```

---

### 2.4 订单

#### 订单列表

```
GET /api/user/orders
```

**请求参数**：
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| status | string | 否 | 筛选状态 |
| page | int | 否 | 页码，默认1 |
| size | int | 否 | 每页条数，默认10 |

#### 订单详情

```
GET /api/user/orders/{id}
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "orderId": 10001,
    "orderNo": "WO202601190001",
    "plateNo": "粤B12345",
    "status": "SERVING",
    "statusName": "服务中",
    "createTime": "2026-01-19 14:30:00",
    "items": [
      {
        "serviceName": "洗车",
        "price": 30.00,
        "status": "DOING",
        "isAddon": false
      }
    ],
    "photos": [
      {
        "type": "CHECK",
        "url": "https://...",
        "createTime": "2026-01-19 14:35:00"
      }
    ],
    "totalAmount": 30.00,
    "couponAmount": 30.00,
    "payAmount": 0.00
  }
}
```

#### 确认追加服务

```
POST /api/user/orders/{id}/confirm-addon
```

**请求参数**：
```json
{
  "addonItemIds": [50, 51]    // 确认的追加项ID
}
```

#### 支付追加服务

```
POST /api/user/orders/{id}/pay
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "payOrderId": "PAY202601190001",
    "payAmount": 150.00,
    "wxPayParams": {
      // 微信支付参数
    }
  }
}
```

---

### 2.5 车辆

#### 我的车辆列表

```
GET /api/user/vehicles
```

**响应示例**：
```json
{
  "code": 0,
  "data": [
    {
      "plateNo": "粤B12345",
      "bindTime": "2026-01-15 10:00:00",
      "serviceCount": 5
    }
  ]
}
```

#### 车辆服务历史

```
GET /api/user/vehicles/{plateNo}/history
```

---

### 2.6 配件预约

#### 待处理配件列表

```
GET /api/user/part-orders
```

**响应示例**：
```json
{
  "code": 0,
  "data": [
    {
      "id": 1,
      "partName": "米其林轮胎 225/45R17",
      "quantity": 4,
      "status": "ARRIVED",
      "statusName": "已到货",
      "expectDate": "2026-01-22",
      "arriveDate": "2026-01-21",
      "siteName": "XX加油站"
    }
  ]
}
```

#### 预约安装

```
POST /api/user/part-orders/{id}/book
```

**请求参数**：
```json
{
  "bookDate": "2026-01-22",
  "bookTime": "10:00"
}
```

---

## 三、员工端 API（APP）

> 基础路径：`/api/emp`

### 3.1 收券

#### 获取我的收券二维码

```
GET /api/emp/qrcode
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "qrCode": "EMP_QR_xxxxx",
    "qrCodeUrl": "https://...",    // 二维码图片URL
    "empName": "张三",
    "stationName": "1号位"
  }
}
```

#### 获取当前工位摄像头识别的车牌

```
GET /api/emp/station/current-plate
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "stationId": 1,
    "stationName": "1号位",
    "plateNo": "粤B12345",
    "confidence": 0.95,
    "captureTime": "2026-01-19 14:30:00",
    "imageUrl": "https://..."
  }
}
```

#### 确认/修正车牌

```
POST /api/emp/station/confirm-plate
```

**请求参数**：
```json
{
  "stationId": 1,
  "plateNo": "粤B12345",     // 确认或修正后的车牌
  "isManual": false          // 是否手动输入
}
```

---

### 3.2 订单操作

#### 当前服务中订单

```
GET /api/emp/orders/current
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "orderId": 10001,
    "orderNo": "WO202601190001",
    "plateNo": "粤B12345",
    "status": "CHECKING",
    "userName": "王*生",
    "items": [
      {
        "id": 100,
        "serviceName": "洗车",
        "status": "PENDING"
      }
    ]
  }
}
```

#### 上传照片

```
POST /api/emp/orders/{id}/photos
```

**请求参数**：
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| type | string | 是 | CHECK-外观检查, PROCESS-过程记录 |
| files | file[] | 是 | 照片文件（multipart） |
| remark | string | 否 | 备注 |

#### 开始外观检查

```
POST /api/emp/orders/{id}/start-check
```

#### 开始服务

```
POST /api/emp/orders/{id}/start-service
```

#### 服务完成

```
POST /api/emp/orders/{id}/complete
```

---

### 3.3 追加服务

#### 可追加服务列表

```
GET /api/emp/service-types
```

**响应示例**：
```json
{
  "code": 0,
  "data": [
    {
      "id": 2,
      "code": "WAX",
      "name": "打蜡",
      "price": 150.00,
      "duration": 45
    }
  ]
}
```

#### 添加追加服务

```
POST /api/emp/orders/{id}/addon
```

**请求参数**：
```json
{
  "serviceTypeId": 2,
  "remark": "客户要求打蜡"
}
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "addonId": 50,
    "needUserConfirm": true,
    "pushStatus": "SENT"
  }
}
```

#### 搜索配件

```
GET /api/emp/parts?keyword=轮胎
```

#### 查询配件库存

```
GET /api/emp/parts/{id}/stock
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "partId": 1,
    "partName": "米其林轮胎 225/45R17",
    "quantity": 8,
    "price": 800.00,
    "inStock": true
  }
}
```

#### 添加配件

```
POST /api/emp/orders/{id}/add-part
```

**请求参数**：
```json
{
  "partId": 1,
  "quantity": 4
}
```

---

### 3.4 绩效

#### 今日绩效

```
GET /api/emp/performance/today
```

**响应示例**：
```json
{
  "code": 0,
  "data": {
    "date": "2026-01-19",
    "totalOrders": 8,
    "services": [
      { "name": "洗车", "count": 5 },
      { "name": "打蜡", "count": 2 },
      { "name": "换胎", "count": 1 }
    ]
  }
}
```

#### 本月绩效

```
GET /api/emp/performance/month
```

---

## 四、管理后台 API（PC）

> 基础路径：`/api/admin`

### 4.1 工位管理

| 方法 | 接口 | 说明 |
|------|------|------|
| GET | `/api/admin/stations` | 工位列表 |
| PUT | `/api/admin/stations/{id}` | 更新工位配置 |
| GET | `/api/admin/stations/realtime` | 实时工位状态 |

### 4.2 订单管理

| 方法 | 接口 | 说明 |
|------|------|------|
| GET | `/api/admin/orders` | 订单列表（分页、筛选） |
| GET | `/api/admin/orders/{id}` | 订单详情 |
| POST | `/api/admin/orders/{id}/cancel` | 取消订单 |
| POST | `/api/admin/orders/{id}/resume` | 恢复挂起订单 |

**订单列表请求参数**：
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| status | string | 否 | 状态筛选 |
| plateNo | string | 否 | 车牌筛选 |
| stationId | long | 否 | 工位筛选 |
| startDate | date | 否 | 开始日期 |
| endDate | date | 否 | 结束日期 |
| page | int | 否 | 页码 |
| size | int | 否 | 每页条数 |

### 4.3 券管理

| 方法 | 接口 | 说明 |
|------|------|------|
| GET | `/api/admin/coupons` | 券列表 |
| POST | `/api/admin/coupons/batch-issue` | 批量发券 |
| GET | `/api/admin/coupon-stats` | 券统计（发放/核销/过期） |

**批量发券请求**：
```json
{
  "type": "WASH",
  "userIds": [1, 2, 3],
  "expireDays": 30,
  "source": "ACTIVITY"
}
```

### 4.4 配件管理

| 方法 | 接口 | 说明 |
|------|------|------|
| GET | `/api/admin/parts` | 配件列表 |
| POST | `/api/admin/parts` | 新增配件 |
| PUT | `/api/admin/parts/{id}` | 更新配件 |
| GET | `/api/admin/parts/stock` | 库存列表 |
| POST | `/api/admin/parts/stock/in` | 入库 |
| GET | `/api/admin/parts/stock/warning` | 库存预警列表 |

**入库请求**：
```json
{
  "partId": 1,
  "siteId": 1,
  "quantity": 20,
  "remark": "月度补货"
}
```

### 4.5 排队调度

| 方法 | 接口 | 说明 |
|------|------|------|
| GET | `/api/admin/queue` | 当前排队列表 |
| POST | `/api/admin/queue/{id}/assign` | 分配工位 |
| POST | `/api/admin/queue/{id}/skip` | 跳过（过号） |
| GET | `/api/admin/queue/capacity` | 今日剩余容量 |

**剩余容量响应**：
```json
{
  "code": 0,
  "data": {
    "totalMinutes": 2160,
    "usedMinutes": 825,
    "remainMinutes": 1335,
    "estimateWashCount": 44
  }
}
```

### 4.6 统计报表

| 方法 | 接口 | 说明 |
|------|------|------|
| GET | `/api/admin/stats/overview` | 总览（今日订单/收入/工位利用率） |
| GET | `/api/admin/stats/service` | 服务类型统计 |
| GET | `/api/admin/stats/employee` | 员工绩效统计 |
| GET | `/api/admin/stats/trend` | 趋势图数据（日/周/月） |

**总览响应**：
```json
{
  "code": 0,
  "data": {
    "todayOrders": 58,
    "todayRevenue": 12500.00,
    "avgDuration": 35,
    "stationUtilization": 0.75,
    "vsYesterday": {
      "orders": 0.12,
      "revenue": 0.08
    }
  }
}
```

---

## 五、摄像头回调 API（内部）

> 基础路径：`/api/internal`

### 车牌识别事件回调

```
POST /api/internal/camera/plate-event
```

**请求参数**（摄像头推送）：
```json
{
  "cameraId": "CAM001",
  "plateNo": "粤B12345",
  "plateColor": "蓝",
  "confidence": 0.95,
  "captureTime": "2026-01-19T14:30:00+08:00",
  "imageBase64": "..."
}
```

**响应**：
```json
{
  "code": 0
}
```

**说明**：
- 此接口由海康摄像头调用，需配置白名单
- 接收到事件后更新对应工位的 `current_plate`
- 图片需存储到 OSS 并记录 URL

---

## 六、接口清单汇总

### 用户端（12个）

| # | 方法 | 接口 | 说明 |
|---|------|------|------|
| 1 | GET | /api/user/coupons | 我的洗车券列表 |
| 2 | GET | /api/user/coupons/{id} | 券详情 |
| 3 | POST | /api/user/scan/use-coupon | 扫码消券 |
| 4 | POST | /api/user/queue/take | 排队取号 |
| 5 | GET | /api/user/queue/status | 排队状态 |
| 6 | DELETE | /api/user/queue/{id} | 取消排队 |
| 7 | GET | /api/user/orders | 订单列表 |
| 8 | GET | /api/user/orders/{id} | 订单详情 |
| 9 | POST | /api/user/orders/{id}/confirm-addon | 确认追加服务 |
| 10 | POST | /api/user/orders/{id}/pay | 支付 |
| 11 | GET | /api/user/vehicles | 我的车辆 |
| 12 | GET | /api/user/part-orders | 待处理配件 |

### 员工端（14个）

| # | 方法 | 接口 | 说明 |
|---|------|------|------|
| 1 | GET | /api/emp/qrcode | 我的收券码 |
| 2 | GET | /api/emp/station/current-plate | 当前车牌 |
| 3 | POST | /api/emp/station/confirm-plate | 确认车牌 |
| 4 | GET | /api/emp/orders/current | 当前订单 |
| 5 | POST | /api/emp/orders/{id}/photos | 上传照片 |
| 6 | POST | /api/emp/orders/{id}/start-check | 开始检查 |
| 7 | POST | /api/emp/orders/{id}/start-service | 开始服务 |
| 8 | POST | /api/emp/orders/{id}/complete | 完成服务 |
| 9 | GET | /api/emp/service-types | 服务列表 |
| 10 | POST | /api/emp/orders/{id}/addon | 追加服务 |
| 11 | GET | /api/emp/parts | 搜索配件 |
| 12 | GET | /api/emp/parts/{id}/stock | 配件库存 |
| 13 | POST | /api/emp/orders/{id}/add-part | 添加配件 |
| 14 | GET | /api/emp/performance/today | 今日绩效 |

### 管理后台（18个）

| # | 方法 | 接口 | 说明 |
|---|------|------|------|
| 1-3 | - | /api/admin/stations/* | 工位管理 |
| 4-7 | - | /api/admin/orders/* | 订单管理 |
| 8-10 | - | /api/admin/coupons/* | 券管理 |
| 11-15 | - | /api/admin/parts/* | 配件管理 |
| 16-19 | - | /api/admin/queue/* | 排队调度 |
| 20-23 | - | /api/admin/stats/* | 统计报表 |

---

## 七、待对接确认项

| # | 问题 | 影响范围 | 确认状态 |
|---|------|----------|----------|
| 1 | Token 认证机制 | 全部接口 | [ ] |
| 2 | 错误码体系 | 全部接口 | [ ] |
| 3 | 分页参数规范 | 列表接口 | [ ] |
| 4 | 文件上传方式 | 照片上传 | [ ] |
| 5 | 微信支付对接 | 支付接口 | [ ] |
| 6 | 接口版本管理 | 全部接口 | [ ] |
