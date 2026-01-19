# 汽车美容服务 - 数据库设计（DDL）

> 基于可行性分析文档中的实体模型，输出 MySQL DDL
> 
> **待对接**：需与加油站平台现有表结构互证后调整

## 一、现有表依赖说明

本设计假设加油站平台已有以下基础表：

| 表名 | 说明 | 需确认字段 |
|------|------|------------|
| `t_user` | 用户表 | user_id, phone, name, openid |
| `t_site` | 站点表 | site_id, name, address |

**待确认**：
- [ ] 现有用户表结构是否一致？
- [ ] 站点表是否有扩展字段需求？
- [ ] 是否有统一的基础字段规范（如 create_by, update_by）？

---

## 二、用户与车牌

```sql
-- 车牌表
CREATE TABLE t_vehicle (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    plate_no        VARCHAR(20)     NOT NULL COMMENT '车牌号',
    user_id         BIGINT          NOT NULL COMMENT '绑定用户ID',
    bind_time       DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '绑定时间',
    source          VARCHAR(20)     NOT NULL DEFAULT 'SCAN' COMMENT '绑定来源: SCAN-扫码绑定, MANUAL-手动添加',
    is_deleted      TINYINT         NOT NULL DEFAULT 0,
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    update_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_user_id (user_id),
    INDEX idx_plate_no (plate_no)
) COMMENT '用户车牌表（多对多，一个车牌可被多用户绑定）';
```

**设计说明**：
- 一个车牌可被多个用户绑定（家庭共用车场景）
- 订单级别确定实际使用者
- `source` 标记绑定来源，便于数据分析

---

## 三、洗车券

```sql
-- 洗车券表
CREATE TABLE t_coupon (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    coupon_no       VARCHAR(32)     NOT NULL UNIQUE COMMENT '券编号',
    user_id         BIGINT          NOT NULL COMMENT '持有用户ID',
    type            VARCHAR(20)     NOT NULL COMMENT '券类型: WASH-洗车券, WAX-打蜡券, COMBO-组合券',
    source          VARCHAR(20)     NOT NULL COMMENT '来源: PURCHASE-购买, GIFT-加油满赠, ACTIVITY-活动',
    status          VARCHAR(20)     NOT NULL DEFAULT 'VALID' COMMENT '状态: VALID-有效, USED-已使用, EXPIRED-已过期',
    expire_time     DATETIME        NOT NULL COMMENT '过期时间',
    use_time        DATETIME        NULL COMMENT '使用时间',
    order_id        BIGINT          NULL COMMENT '使用时关联的订单ID',
    site_id         BIGINT          NOT NULL COMMENT '适用站点ID（0表示通用）',
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    update_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_user_id (user_id),
    INDEX idx_status_expire (status, expire_time)
) COMMENT '洗车券表';
```

**待确认**：
- [ ] 券是否需要支持转赠？（当前设计不支持）
- [ ] 是否需要记录券的金额面值？
- [ ] 加油满赠的发放是否由现有系统触发？

---

## 四、站点与工位

```sql
-- 工位表
CREATE TABLE t_station (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    site_id         BIGINT          NOT NULL COMMENT '所属站点ID',
    name            VARCHAR(50)     NOT NULL COMMENT '工位名称（如：1号位）',
    camera_id       VARCHAR(100)    NULL COMMENT '绑定摄像头ID',
    camera_ip       VARCHAR(50)     NULL COMMENT '摄像头IP地址',
    status          VARCHAR(20)     NOT NULL DEFAULT 'IDLE' COMMENT '状态: IDLE-空闲, OCCUPIED-有车, SERVING-服务中, WAITING-待取车',
    current_plate   VARCHAR(20)     NULL COMMENT '当前工位车牌（摄像头识别）',
    current_order_id BIGINT         NULL COMMENT '当前服务订单ID',
    is_enabled      TINYINT         NOT NULL DEFAULT 1 COMMENT '是否启用',
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    update_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_site_id (site_id)
) COMMENT '洗车工位表';
```

**设计说明**：
- `current_plate` 由摄像头识别后更新，支持覆盖
- `current_order_id` 关联当前服务订单，便于快速查询

---

## 五、员工

```sql
-- 员工表（洗车业务扩展）
CREATE TABLE t_wash_employee (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    emp_no          VARCHAR(32)     NOT NULL UNIQUE COMMENT '员工编号',
    name            VARCHAR(50)     NOT NULL COMMENT '姓名',
    phone           VARCHAR(20)     NOT NULL COMMENT '手机号',
    site_id         BIGINT          NOT NULL COMMENT '所属站点ID',
    role            VARCHAR(20)     NOT NULL COMMENT '角色: WASHER-洗车员, DISPATCHER-调度员, MANAGER-站长',
    qr_code         VARCHAR(200)    NULL COMMENT '个人收券码URL',
    status          VARCHAR(20)     NOT NULL DEFAULT 'ACTIVE' COMMENT '状态: ACTIVE-在职, INACTIVE-离职',
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    update_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_site_id (site_id)
) COMMENT '洗车员工表';
```

**待确认**：
- [ ] 是否复用现有员工表？还是独立建表？
- [ ] 员工角色是否与现有权限系统对接？

---

## 六、服务类型

```sql
-- 服务类型表
CREATE TABLE t_service_type (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    code            VARCHAR(32)     NOT NULL UNIQUE COMMENT '服务编码',
    name            VARCHAR(50)     NOT NULL COMMENT '服务名称',
    category        VARCHAR(20)     NOT NULL COMMENT '分类: WASH-清洗, BEAUTY-美容, REPAIR-维修',
    price           DECIMAL(10,2)   NOT NULL COMMENT '标准价格',
    duration        INT             NOT NULL COMMENT '标准时长（分钟）',
    need_part       TINYINT         NOT NULL DEFAULT 0 COMMENT '是否需要配件',
    allow_emp_add   TINYINT         NOT NULL DEFAULT 1 COMMENT '允许员工代客追加',
    sort_order      INT             NOT NULL DEFAULT 0 COMMENT '排序',
    is_enabled      TINYINT         NOT NULL DEFAULT 1,
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    update_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
) COMMENT '服务类型配置表';
```

**初始数据**：
```sql
INSERT INTO t_service_type (code, name, category, price, duration, need_part) VALUES
('WASH_BASIC', '普通洗车', 'WASH', 30.00, 30, 0),
('WASH_FINE', '精洗', 'WASH', 60.00, 45, 0),
('WAX', '打蜡', 'BEAUTY', 150.00, 45, 0),
('POLISH', '抛光', 'BEAUTY', 200.00, 60, 0),
('TIRE_CHANGE', '换轮胎', 'REPAIR', 50.00, 40, 1),
('WIPER_CHANGE', '换雨刮', 'REPAIR', 20.00, 10, 1);
```

---

## 七、排队

```sql
-- 排队表
CREATE TABLE t_queue (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    queue_no        VARCHAR(20)     NOT NULL COMMENT '排队号（如：A001）',
    user_id         BIGINT          NOT NULL COMMENT '用户ID',
    site_id         BIGINT          NOT NULL COMMENT '站点ID',
    status          VARCHAR(20)     NOT NULL DEFAULT 'WAITING' COMMENT '状态: WAITING-排队中, CALLED-已叫号, ENTERED-已进场, COMPLETED-已完成, EXPIRED-已过号',
    queue_date      DATE            NOT NULL COMMENT '排队日期',
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '取号时间',
    called_time     DATETIME        NULL COMMENT '叫号时间',
    entered_time    DATETIME        NULL COMMENT '进场时间',
    station_id      BIGINT          NULL COMMENT '分配工位ID',
    INDEX idx_site_date (site_id, queue_date),
    INDEX idx_user_id (user_id)
) COMMENT '排队表';
```

---

## 八、订单

### 8.1 订单主表

```sql
-- 美容订单主表
CREATE TABLE t_wash_order (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    order_no        VARCHAR(32)     NOT NULL UNIQUE COMMENT '订单编号',
    site_id         BIGINT          NOT NULL COMMENT '站点ID',
    station_id      BIGINT          NOT NULL COMMENT '工位ID',
    user_id         BIGINT          NOT NULL COMMENT '用户ID',
    plate_no        VARCHAR(20)     NOT NULL COMMENT '服务车牌',
    coupon_id       BIGINT          NULL COMMENT '使用的洗车券ID',
    queue_id        BIGINT          NULL COMMENT '关联排队ID',
    status          VARCHAR(20)     NOT NULL DEFAULT 'PENDING' COMMENT '状态: PENDING-待确认, CHECKING-外观检查, SERVING-服务中, WAITING_PAY-待支付, WAITING_PICKUP-待取车, COMPLETED-已完成, SUSPENDED-挂起, CANCELLED-已取消',
    total_amount    DECIMAL(10,2)   NOT NULL DEFAULT 0 COMMENT '订单总金额',
    coupon_amount   DECIMAL(10,2)   NOT NULL DEFAULT 0 COMMENT '券抵扣金额',
    pay_amount      DECIMAL(10,2)   NOT NULL DEFAULT 0 COMMENT '实付金额',
    pay_status      VARCHAR(20)     NOT NULL DEFAULT 'UNPAID' COMMENT '支付状态: UNPAID-未支付, PARTIAL-部分支付, PAID-已支付',
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    check_time      DATETIME        NULL COMMENT '外观检查时间',
    start_time      DATETIME        NULL COMMENT '服务开始时间',
    finish_time     DATETIME        NULL COMMENT '服务完成时间',
    pickup_time     DATETIME        NULL COMMENT '取车时间',
    update_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_user_id (user_id),
    INDEX idx_site_id (site_id),
    INDEX idx_plate_no (plate_no),
    INDEX idx_status (status)
) COMMENT '美容订单主表';
```

### 8.2 订单明细表

```sql
-- 订单明细表
CREATE TABLE t_wash_order_item (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    order_id        BIGINT          NOT NULL COMMENT '订单ID',
    service_type_id BIGINT          NOT NULL COMMENT '服务类型ID',
    service_name    VARCHAR(50)     NOT NULL COMMENT '服务名称（冗余）',
    employee_id     BIGINT          NULL COMMENT '服务员工ID',
    price           DECIMAL(10,2)   NOT NULL COMMENT '服务价格',
    status          VARCHAR(20)     NOT NULL DEFAULT 'PENDING' COMMENT '状态: PENDING-待开始, DOING-进行中, DONE-已完成',
    is_addon        TINYINT         NOT NULL DEFAULT 0 COMMENT '是否追加服务',
    pay_status      VARCHAR(20)     NOT NULL DEFAULT 'UNPAID' COMMENT '支付状态',
    start_time      DATETIME        NULL,
    finish_time     DATETIME        NULL,
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_order_id (order_id)
) COMMENT '订单服务明细表';
```

### 8.3 订单照片表

```sql
-- 订单照片表
CREATE TABLE t_wash_order_photo (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    order_id        BIGINT          NOT NULL COMMENT '订单ID',
    type            VARCHAR(20)     NOT NULL COMMENT '类型: CHECK-外观检查, PROCESS-过程记录, COMPLETE-完成确认',
    url             VARCHAR(500)    NOT NULL COMMENT '照片URL',
    employee_id     BIGINT          NOT NULL COMMENT '上传员工ID',
    remark          VARCHAR(200)    NULL COMMENT '备注',
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_order_id (order_id)
) COMMENT '订单照片表';
```

---

## 九、配件库存

### 9.1 配件表

```sql
-- 配件表
CREATE TABLE t_part (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    code            VARCHAR(32)     NOT NULL UNIQUE COMMENT '配件编码',
    name            VARCHAR(100)    NOT NULL COMMENT '配件名称',
    category        VARCHAR(20)     NOT NULL COMMENT '分类: TIRE-轮胎, WIPER-雨刮, OIL-机油, FILTER-滤芯, OTHER-其他',
    brand           VARCHAR(50)     NULL COMMENT '品牌',
    spec            VARCHAR(100)    NULL COMMENT '规格型号',
    unit            VARCHAR(10)     NOT NULL COMMENT '单位: 条/个/升',
    base_price      DECIMAL(10,2)   NOT NULL COMMENT '基础售价',
    lead_time       INT             NOT NULL DEFAULT 3 COMMENT '无货配送时间（天）',
    is_enabled      TINYINT         NOT NULL DEFAULT 1,
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    update_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
) COMMENT '配件表';
```

### 9.2 配件库存表

```sql
-- 配件库存表（每站点独立库存）
CREATE TABLE t_part_stock (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    part_id         BIGINT          NOT NULL COMMENT '配件ID',
    site_id         BIGINT          NOT NULL COMMENT '站点ID',
    quantity        INT             NOT NULL DEFAULT 0 COMMENT '当前库存',
    safety_qty      INT             NOT NULL DEFAULT 5 COMMENT '安全库存（低于此值预警）',
    update_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    UNIQUE KEY uk_part_site (part_id, site_id)
) COMMENT '配件库存表';
```

### 9.3 库存流水表

```sql
-- 库存流水表
CREATE TABLE t_part_stock_log (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    part_id         BIGINT          NOT NULL,
    site_id         BIGINT          NOT NULL,
    type            VARCHAR(20)     NOT NULL COMMENT '类型: IN-入库, OUT-出库, CHECK-盘点',
    quantity        INT             NOT NULL COMMENT '变动数量（正负）',
    before_qty      INT             NOT NULL COMMENT '变动前数量',
    after_qty       INT             NOT NULL COMMENT '变动后数量',
    order_id        BIGINT          NULL COMMENT '关联订单ID（出库时）',
    operator_id     BIGINT          NOT NULL COMMENT '操作人ID',
    remark          VARCHAR(200)    NULL,
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_part_site (part_id, site_id)
) COMMENT '库存流水表';
```

### 9.4 订单配件表

```sql
-- 订单配件表
CREATE TABLE t_wash_order_part (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    order_id        BIGINT          NOT NULL COMMENT '订单ID',
    part_id         BIGINT          NOT NULL COMMENT '配件ID',
    part_name       VARCHAR(100)    NOT NULL COMMENT '配件名称（冗余）',
    quantity        INT             NOT NULL COMMENT '使用数量',
    price           DECIMAL(10,2)   NOT NULL COMMENT '单价',
    total_price     DECIMAL(10,2)   NOT NULL COMMENT '总价',
    employee_id     BIGINT          NOT NULL COMMENT '操作员工ID',
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_order_id (order_id)
) COMMENT '订单配件使用表';
```

### 9.5 用户配件预约单

```sql
-- 用户配件预约单（无货时）
CREATE TABLE t_user_part_order (
    id              BIGINT          PRIMARY KEY AUTO_INCREMENT,
    user_id         BIGINT          NOT NULL COMMENT '用户ID',
    part_id         BIGINT          NOT NULL COMMENT '配件ID',
    part_name       VARCHAR(100)    NOT NULL COMMENT '配件名称（冗余）',
    quantity        INT             NOT NULL COMMENT '预约数量',
    site_id         BIGINT          NOT NULL COMMENT '站点ID',
    status          VARCHAR(20)     NOT NULL DEFAULT 'ORDERING' COMMENT '状态: ORDERING-采购中, ARRIVED-已到货, COMPLETED-已完成, CANCELLED-已取消',
    expect_date     DATE            NOT NULL COMMENT '预计到货日期',
    arrive_date     DATE            NULL COMMENT '实际到货日期',
    order_id        BIGINT          NULL COMMENT '关联服务订单ID（完成时）',
    notify_status   VARCHAR(20)     NOT NULL DEFAULT 'PENDING' COMMENT '通知状态: PENDING-待通知, SENT-已通知',
    create_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    update_time     DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_user_id (user_id),
    INDEX idx_status (status)
) COMMENT '用户配件预约单';
```

---

## 十、表清单汇总

| 序号 | 表名 | 说明 | 依赖 |
|------|------|------|------|
| 1 | t_vehicle | 用户车牌表 | t_user |
| 2 | t_coupon | 洗车券表 | t_user, t_site |
| 3 | t_station | 洗车工位表 | t_site |
| 4 | t_wash_employee | 洗车员工表 | t_site |
| 5 | t_service_type | 服务类型配置表 | - |
| 6 | t_queue | 排队表 | t_user, t_site |
| 7 | t_wash_order | 美容订单主表 | t_user, t_site, t_station, t_coupon |
| 8 | t_wash_order_item | 订单明细表 | t_wash_order, t_service_type |
| 9 | t_wash_order_photo | 订单照片表 | t_wash_order |
| 10 | t_part | 配件表 | - |
| 11 | t_part_stock | 配件库存表 | t_part, t_site |
| 12 | t_part_stock_log | 库存流水表 | t_part, t_site |
| 13 | t_wash_order_part | 订单配件表 | t_wash_order, t_part |
| 14 | t_user_part_order | 用户配件预约单 | t_user, t_part, t_site |

---

## 十一、待对接确认项

| # | 问题 | 影响范围 | 确认状态 |
|---|------|----------|----------|
| 1 | 现有用户表字段结构 | t_vehicle, t_coupon, t_queue, t_wash_order | [ ] |
| 2 | 现有站点表字段结构 | t_station, t_coupon, t_queue | [ ] |
| 3 | 员工表是否复用现有 | t_wash_employee | [ ] |
| 4 | 是否有统一的审计字段规范 | 全部表 | [ ] |
| 5 | 订单编号生成规则 | t_wash_order | [ ] |
| 6 | 券编号生成规则 | t_coupon | [ ] |
| 7 | 支付系统对接方式 | t_wash_order 支付状态 | [ ] |
