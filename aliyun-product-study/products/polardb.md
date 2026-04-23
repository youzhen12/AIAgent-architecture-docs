# PolarDB - 云原生数据库

## 基本信息

- **产品全称**: PolarDB
- **产品类别**: 数据库服务
- **产品定位**: 云原生关系型数据库（计算存储分离架构）

## 产品用途

为高性能、高并发、自动扩展业务提供企业级云原生数据库服务：

- 高并发交易系统（游戏、电商等）
- 传统 Oracle 数据库迁移上云
- HTAP 混合事务/分析处理
- 全球分布式数据库（GDN）
- 时空数据处理（GIS）

## 产品特点

PolarDB 包含三个引擎：

- **PolarDB for MySQL**: 100% 兼容 MySQL，事务性能是开源 MySQL 5 倍，分析快 400 倍
- **PolarDB for PostgreSQL**: 100% 兼容 PostgreSQL，高度兼容 Oracle 语法
- **PolarDB-X (Xscale)**: 高性能分布式数据库，支持 PB 级存储

核心特性：

- **计算存储分离**: 共享存储架构，读扩展只计算节点费用
- **秒级弹性伸缩**: 数分钟内完成扩缩容
- **IMCI 列存**: 内置列存索引，支持 OLTP + OLAP 混合场景
- **GDN 全球数据库网络**: 跨地域同步低于 2 秒
- **500 TB 单实例存储**: 自动扩展按量付费
- **ADAM 迁移工具**: Oracle 迁移兼容评估免费使用

## 产品优点

1. **高性价比**: 相比 RDS MySQL，TCO 降低 50%（读扩展只增加计算节点费用）
2. **兼容性优秀**: MySQL 完全兼容，PostgreSQL 兼容 + Oracle 语法高度兼容
3. **HTAP 能力**: IMCI 列存索引支持实时 OLAP 查询（100 GB 数据查询快 30 倍）
4. **DDL 优化**: Fast DDL，1 TB 添加列只需 1 秒（开源 MySQL 10 倍快）
5. **备份恢复快**: 1 TB 数据备份 10 秒，恢复 10 分钟
6. **全球部署**: GDN 支持多地域读写分离，数据就近读取
7. **TPC-C 全球第一**: 在 TPC-C 基准测试中排名第一

## 产品缺点

1. **存储架构限制**: 共享存储架构在极端故障下恢复时间可能较长
2. **Oracle 兼容不完美**: 高度兼容但不是 100% 等价，部分存储过程需改造
3. **多引擎选择困惑**: 三个引擎架构差异较大，用户选型需要专业知识
4. **国际地域覆盖有限**: 海外地域数量和可用性不如 MySQL/AWS Aurora
5. **GDN 成本**: 跨地域同步需要额外付费

## 竞品对比

| 维度 | PolarDB | AWS Aurora | 腾讯云 TDSQL-C | Google Cloud SQL |
|------|---------|------------|---------------|------------------|
| 兼容引擎 | MySQL/PostgreSQL/Oracle | MySQL/PostgreSQL | MySQL/PostgreSQL | MySQL/PostgreSQL |
| 存储容量 | 500 TB | 128 TB | 数 PB | 64 TB |
| 读写分离节点 | 最多 16 个 | 最多 15 个 | 最多 16 个 | 最多 5 个 |
| HTAP 支持 | IMCI 列存 | Aurora Analytics | TDSQL-A | BigQuery 外部 |
| 全球同步 | <2 秒 (GDN) | 亚秒级 | 秒级 | 秒级 |
| Oracle 迁移 | ADAM 免费评估 | SCT 工具 | 需第三方 | DMS |
| 性能基准 | TPC-C 全球第一 | - | - | - |
| 中国价格 | 中等偏低 | 高 | 低 | 高 |

## 适用场景

- **高并发交易**: 游戏开合服、电商大促
- **Oracle 迁移**: 传统商业数据库替换，降低许可成本
- **HTAP 混合负载**: 同时需要 OLTP 和实时分析
- **全球化业务**: 多地域部署，数据就近读取
- **时空数据**: GIS 数据仓库、数字孪生

## 参考链接

- 官网: <https://www.alibabacloud.com/product/polardb>
- 文档: <https://www.alibabacloud.com/help/polardb/>
