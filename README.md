BusFare 公交自动扣费系统 - 完整项目文档 v2.0
================================================================================

一、项目概述
================================================================================

1.1 项目背景
随着城市公共交通的快速发展，传统投币方式面临现金管理复杂、找零困难、无法统计客流等问题。本项目旨在构建一套完整的公交自动扣费解决方案，通过手机扫码实现无现金乘车，提升乘客体验和运营效率。

1.2 项目目标
- 实现乘客手机扫码乘车，自动扣费
- 为公交公司提供设备管理和交易监控后台
- 支持多线路、多设备同时运行
- 统一使用Android平台，简化开发和部署

1.3 核心功能模块
| 模块 | 功能描述 |
|------|---------|
| 乘客APP | 生成动态二维码、余额查询、交易记录、充值 |
| 刷卡机APP | 二维码扫描、扣费处理、实时心跳、离线支持 |
| 后台管理 | 用户管理、设备管理、交易统计、充值管理 |
| API服务 | 统一数据接口，支持各端通信 |

================================================================================
二、技术架构
================================================================================

2.1 系统架构
系统采用分层架构设计，所有移动端均基于 .NET MAUI 开发，部署于 Android 平台。

架构层次：
- 用户端层：乘客APP（MAUI Android）、刷卡机APP（MAUI Android）
- 服务层：REST API（ASP.NET Core）
- 数据层：SQLite数据库
- 管理端层：WPF桌面应用

2.2 技术栈
| 组件 | 技术选型 | 说明 |
|------|---------|------|
| 后端API | ASP.NET Core 8.0 | 跨平台、高性能Web框架 |
| 数据库 | SQLite | 轻量级、免部署、零配置 |
| 二维码生成 | QRCoder | 服务端生成Base64二维码 |
| 乘客端 | .NET MAUI Android | 跨平台移动开发框架 |
| 刷卡机 | .NET MAUI Android | 统一开发，支持扫码 |
| 扫码识别 | ZXing.Net.Maui | MAUI原生二维码扫描库 |
| MVVM框架 | CommunityToolkit.Mvvm | 简化开发、代码复用 |
| 后台管理 | WPF (.NET 8) | Windows桌面应用 |

2.3 项目结构
BusFare/
  src/
    BusFare.Shared/                  # 共享库（模型、数据库服务）
    BusFare.Server.Api/             # API服务 (ASP.NET Core)
    BusFare.Server.Desktop/         # 后台管理端 (WPF)
    BusFare.Client.Android/         # 乘客端 (MAUI Android)
    BusFare.Machine.Android/        # 刷卡机端 (MAUI Android)
  BusFare.sln                       # 解决方案文件

2.4 通信机制
- 移动端与服务器：REST API over HTTP/HTTPS
- 心跳机制：每30秒自动上报设备状态
- 扫码枪支持：USB HID接口，即插即用
- 模拟器访问：使用10.0.2.2访问宿主机localhost:5000

================================================================================
三、功能模块详解
================================================================================

3.1 乘客端 APP (BusFare.Client.Android)

3.1.1 登录模块
- 手机号+验证码登录：用户输入手机号，获取4位验证码
- 验证码有效期：5分钟
- 会话管理：Token存储，自动登录
- 界面设计：深色主题，科技感

3.1.2 首页（二维码）
- 动态二维码：每60秒刷新，显示用户乘车码
- 余额显示：当前账户余额，实时更新
- 有效期倒计时：显示二维码剩余有效时间
- 刷新按钮：手动刷新二维码
- 充值入口：快速充值10元

3.1.3 交易记录
- 历史查询：显示近期交易记录
- 详情查看：线路、时间、金额
- 筛选功能：按时间范围筛选

3.1.4 界面设计
- 主色调：深色背景（#1a1a2e）
- 强调色：红色（#e94560）、青色（#00d9ff）
- 卡片式布局：圆角15px，阴影效果
- 字体：Microsoft YaHei（微软雅黑）

3.2 刷卡机端 APP (BusFare.Machine.Android)

3.2.1 设备注册
- 设备码：唯一标识（如M001）
- 线路名：所属公交线路（如1路）
- 票价：单程票价设置（默认2.00元）
- 注册状态显示：已注册/未注册

3.2.2 扫码扣费
- 摄像头扫码：实时读取二维码
- 扫码枪支持：USB HID输入，即插即用
- 扣费流程：扫码->验证->扣费->显示结果
- 结果展示：大字体显示扣费成功/失败
- 自动隐藏：5秒后自动隐藏结果

3.2.3 心跳机制
- 定时上报：每30秒向服务器发送心跳
- 状态同步：获取最新票价、线路信息
- 离线检测：心跳失败时提示网络问题

3.2.4 离线支持
- 本地缓存：网络断开时缓存交易记录
- 自动重试：恢复网络后自动上传
- 状态指示：显示在线/离线状态

3.3 后台管理端 (BusFare.Server.Desktop)

3.3.1 用户管理
- 用户列表：查看所有注册用户
- 余额管理：查看/调整用户余额
- 禁用启用：用户状态控制
- 搜索功能：按手机号搜索

3.3.2 交易记录
- 实时监控：查看最新交易
- 筛选查询：按时间、线路、用户筛选
- 统计报表：日/月/年交易量
- 导出功能：支持导出Excel

3.3.3 设备管理
- 设备列表：所有刷卡机状态
- 在线监控：设备心跳状态
- 参数配置：修改线路、票价
- 设备注册：添加新设备

3.3.4 充值管理
- 充值记录：所有充值流水
- 手动充值：后台手动添加余额
- 充值统计：充值金额汇总

================================================================================
四、数据库设计
================================================================================

4.1 数据库文件
- 路径：%LOCALAPPDATA%/BusFare/busfare.db
- 类型：SQLite
- 自动创建：首次启动时自动初始化

4.2 数据表结构

4.2.1 Users（用户表）
CREATE TABLE Users (
    Id TEXT PRIMARY KEY,           -- UUID
    Phone TEXT UNIQUE NOT NULL,   -- 手机号
    PasswordHash TEXT,            -- 密码哈希（预留）
    Balance REAL DEFAULT 0,       -- 余额
    Status INTEGER DEFAULT 1,     -- 状态：1正常，0禁用
    CreatedAt TEXT                -- 创建时间
);

4.2.2 Transactions（交易表）
CREATE TABLE Transactions (
    Id TEXT PRIMARY KEY,          -- UUID
    UserId TEXT,                  -- 用户ID
    Amount REAL,                  -- 金额
    TransactionTime TEXT,        -- 交易时间
    LineName TEXT,                -- 线路名
    MachineId TEXT,               -- 刷卡机ID
    FOREIGN KEY (UserId) REFERENCES Users(Id)
);

4.2.3 QRCodes（二维码表）
CREATE TABLE QRCodes (
    Id TEXT PRIMARY KEY,          -- UUID
    UserId TEXT,                  -- 用户ID
    Token TEXT UNIQUE,           -- 二维码Token
    ExpiresAt TEXT,              -- 过期时间
    IsUsed INTEGER DEFAULT 0,     -- 是否已使用
    UsedAt TEXT,                  -- 使用时间
    FOREIGN KEY (UserId) REFERENCES Users(Id)
);

4.2.4 CardMachines（刷卡机表）
CREATE TABLE CardMachines (
    Id TEXT PRIMARY KEY,          -- UUID
    DeviceCode TEXT UNIQUE,      -- 设备码
    LineName TEXT,                -- 线路名
    TicketPrice REAL DEFAULT 2,   -- 票价
    Status INTEGER DEFAULT 1,     -- 状态
    LastHeartbeat TEXT,          -- 最后心跳时间
    CreatedAt TEXT                -- 创建时间
);

4.2.5 Recharges（充值记录表）
CREATE TABLE Recharges (
    Id TEXT PRIMARY KEY,          -- UUID
    UserId TEXT,                  -- 用户ID
    Amount REAL,                  -- 充值金额
    RechargeTime TEXT,           -- 充值时间
    Operator TEXT                 -- 操作员
);

================================================================================
五、API接口设计
================================================================================

5.1 健康检查
GET /health
响应：{"status":"ok","time":"2026-05-25T10:00:00"}

5.2 用户接口

5.2.1 发送验证码
POST /api/user/sendCode
请求：{"phone":"13800138000"}
响应：{"success":true,"message":"验证码已发送","code":"1234"}（开发环境）

5.2.2 用户登录
POST /api/user/login
请求：{"phone":"13800138000","code":"1234"}
响应：{"success":true,"userId":"xxx","token":"xxx","balance":100.00}

5.2.3 查询余额
GET /api/user/{id}/balance
响应：{"balance":100.00}

5.3 二维码接口

5.3.1 生成二维码
POST /api/qrcode/generate
请求：{"userId":"xxx"}
响应：{"success":true,"token":"xxx","expiresAt":"2026-05-25T10:01:00","qrCodeData":"data:image/png;base64,..."}

5.4 交易接口

5.4.1 扣费请求
POST /api/transaction/deduct
请求：{"qrCodeToken":"xxx","cardMachineId":"","deviceCode":"M001"}
响应：{"success":true,"message":"扣费成功","balance":98.00,"transactionId":"xxx"}

5.4.2 查询交易记录
GET /api/transaction/list?userId=xxx
响应：{"transactions":[...]}

5.5 设备接口

5.5.1 设备注册
POST /api/machine/register
请求：{"deviceCode":"M001","lineName":"1路","ticketPrice":2.00}
响应：{"success":true,"machineId":"xxx"}

5.5.2 设备心跳
POST /api/machine/heartbeat
请求：{"deviceCode":"M001","cardMachineId":""}
响应：{"success":true,"ticketPrice":2.00,"lineName":"1路"}

5.6 充值接口

5.6.1 充值
POST /api/recharge
请求：{"userId":"xxx","amount":10}
响应：{"success":true,"newBalance":110.00}

================================================================================
六、开发环境与工具
================================================================================

6.1 开发环境
- 操作系统：Windows 10/11
- 开发工具：Visual Studio 2022 / VS Code
- .NET SDK：8.0+
- Android SDK：API 34+
- MAUI workload：已安装

6.2 项目依赖
- BusFare.Shared: Microsoft.Data.Sqlite, System.Text.Json
- BusFare.Server.Api: ASP.NET Core, QRCoder, Swashbuckle
- BusFare.Client.Android: Microsoft.Maui.Controls, ZXing.Net.Maui, CommunityToolkit.Mvvm
- BusFare.Machine.Android: Microsoft.Maui.Controls, ZXing.Net.Maui, CommunityToolkit.Mvvm
- BusFare.Server.Desktop: CommunityToolkit.Mvvm

6.3 端口配置
- API服务：localhost:5000
- 模拟器访问：10.0.2.2:5000（映射到宿主机）
- Swagger文档：/swagger

================================================================================
七、部署方案
================================================================================

7.1 开发环境部署
1. 启动API服务：dotnet run --project src/BusFare.Server.Api
2. 打开模拟器或连接Android设备
3. 运行MAUI应用进行测试

7.2 生产环境部署

7.2.1 服务器
- 操作系统：Windows Server 2019+ 或 Linux
- .NET运行时：8.0
- 数据库：SQLite（小型）或 PostgreSQL（大型）
- 配置：2核4G起

7.2.2 乘客手机
- Android版本：8.0+
- 分发方式：APK直装或应用市场

7.2.3 刷卡机
- Android设备：平板电脑/手机
- 建议：屏幕5寸以上，支持USB OTG
- 网络：4G/WiFi

7.3 APK分发方式
- 应用市场：Google Play等
- 企业内部分发：MDM/EMM
- 直接安装：APK文件

================================================================================
八、测试计划
================================================================================

8.1 单元测试
- 数据库操作测试
- API接口测试
- 二维码生成/解析测试

8.2 集成测试
- 乘客APP <-> API服务
- 刷卡机APP <-> API服务

8.3 UI测试
- MAUI界面兼容性
- 不同Android版本测试

8.4 场景测试
- 正常扫码扣费
- 二维码过期
- 网络异常
- 多设备并发

================================================================================
九、风险与对策
================================================================================

| 风险 | 影响 | 对策 |
|------|------|------|
| Android SDK未安装 | 无法构建APK | 运行 dotnet workload install android |
| MAUI相机兼容性 | 部分设备无法扫描 | 使用 ZXing.Net.Maui，测试多款设备 |
| 网络不稳定 | 扣费失败 | 本地缓存+重试机制+离线模式 |
| 数据库并发 | 数据冲突 | 事务处理+锁机制 |
| 二维码过期 | 扫码失败 | 实时刷新+过期提示 |

================================================================================
十、后续扩展
================================================================================

10.1 功能扩展
- 电子月票/年票
- 换乘优惠
- 余额提醒通知
- 家人代付功能
- iOS版本支持

10.2 技术扩展
- 微服务架构
- Redis缓存层
- 消息队列
- 大数据分析平台
- 实时推送通知

================================================================================
文档结束
================================================================================