# GeoAI 产品需求文档（PRD）

## 文档信息
- 版本：V2.3（与当前代码对齐）
- 日期：2026-04-22
- 适用仓库：`ragAI知识库`
- 文档目的：描述当前可运行能力、问题现状与后续迭代方向，作为研发与联调基线。

---

## 1. 项目定位

GeoAI 是一个面向测绘/国土空间规划标准资料的智能检索与可视化系统，核心链路为：
1. 标准文档采集、解压、切分与向量化。
2. 基于向量数据库进行语义检索。
3. 大模型结合召回片段生成回答。
4. 前端地图（2D/3D）联动呈现空间信息。

目标用户：
- 测绘与规划从业者（标准查询、政策依据定位）
- GIS 学生/研究者（学习与案例分析）
- 技术面试演示场景（展示全栈与 GeoAI 能力）

---

## 2. 当前系统边界（以代码为准）

### 2.1 前端
- 技术：React + TypeScript + Vite + OpenLayers + Cesium + Zustand。
- 主界面：聊天面板 + 地图视图（2D/3D切换）+ 图层控制 + 文档抽屉。
- API 对接前缀：`/api`。

### 2.2 后端
当前并存两套实现：
1. **主业务后端（建议联调用）**：`Backend/main.py` + `Backend/app/*`。
2. **轻量后端（历史/并行）**：`src/geoai/api/main.py`。

说明：
- 前端 `frontend/src/services/*` 当前按 `Backend/app/api/*` 的接口组织方式编写。
- `docker-compose.yml` 默认 backend 命令仍指向 `src.geoai.api.main:app`，存在契约偏差。

### 2.3 数据与基础设施
- PostgreSQL（向量检索 + 空间表）
- MySQL（标准元数据）
- Redis（会话/缓存）
- MinIO（对象存储）
- 可选 GeoServer（地图服务）

---

## 3. 已实现能力清单

### 3.1 检索链路（可用）
- 检索接口：`/api/search/query`。
- 支持：
  - 意图识别（search/greeting/clarification/dialog_management/other）。
  - 非检索意图短路处理。
  - 向量召回（pgvector）。
  - 大模型生成回答（普通与流式）。
  - 对话历史截断，降低上下文膨胀风险。

### 3.2 空间能力（部分可用）
- 省级行政区 GeoJSON 获取：`/api/spatial/provinces`（查询 `spatial_regions`）。
- 常用空间接口已定义，但多数仍为占位实现。

### 3.3 文档管理（接口在，能力未全部落地）
- 已有上传、预签名 URL、列表、详情、删除等路由骨架。
- 实际持久化、索引重建、统计等能力仍在完善中。

### 3.4 数据处理脚本（可运行）
- 文档处理与向量构建：`scripts/build_vector_db.py`。
- 空间数据导入：`scripts/import_spatial_data.py`。
- 数据验证与清理脚本：`scripts/verify_data.py`、`scripts/clean_and_rebuild.py`。

---

## 4. 当前问题与风险

### 4.1 架构一致性风险（最高优先级）
- 问题：前端契约与 docker 默认后端入口不一致。
- 影响：本地/容器联调时容易出现接口缺失、行为不一致。
- 建议：统一后端主线（建议以 `Backend` 为主），并同步修正 docker 启动命令。

### 4.2 接口完成度风险
- `documents`、`spatial` 部分 API 仍是 TODO/模拟返回。
- 会导致“页面能打开，但关键业务闭环不完整”。

### 4.3 安全风险
- 代码与配置中存在默认凭据/密钥示例。
- 上线前必须改为环境变量注入并轮换密钥。

### 4.4 文档一致性风险
- 历史文档存在“技术栈与实现不一致”问题（例如前端技术描述过时）。
- 本 PRD 与 README 已基于当前代码校正。

---

## 5. 核心需求（下一阶段）

### 5.1 P0（必须完成）
1. 统一后端入口与契约。
2. 完成文档管理最小闭环：上传 -> 入库 -> 可检索 -> 可回溯。
3. 完成空间联动最小闭环：回答中的行政区提取 -> 地图定位高亮。
4. 清理默认密钥与硬编码敏感配置。

### 5.2 P1（高优先）
1. 完成 `hybrid search`（文本 + 空间）真实逻辑。
2. 完成检索建议、反馈、历史记录接口。
3. 提升文档详情页数据完整性（来源、时间、分类、引用链）。

### 5.3 P2（增强）
1. 引入可观测性（请求耗时、召回质量、错误率）。
2. GeoServer/WMS/WFS 深度集成。
3. 多租户/权限/审计能力。

---

## 6. API 基线（当前约定）

### 6.1 检索
- `POST /api/search/query`
- `POST /api/search/hybrid`
- `GET /api/search/suggest`
- `GET /api/search/similar/{doc_id}`

### 6.2 空间
- `GET /api/spatial/provinces`
- `POST /api/spatial/query`
- `POST /api/spatial/geocode`
- `POST /api/spatial/reverse-geocode`
- `GET /api/spatial/distance`

### 6.3 文档
- `POST /api/documents/presigned-url`
- `POST /api/documents/upload`
- `GET /api/documents/list`
- `GET /api/documents/{doc_id}`
- `DELETE /api/documents/{doc_id}`

### 6.4 系统
- `GET /`
- `GET /health`
- `GET /api/system/*`

---

## 7. 非功能要求（当前阶段）

- 稳定性：核心检索接口可用，异常可回退，不因单一依赖不可用而整体崩溃。
- 性能目标（开发阶段）：
  - 语义检索 P95 < 3s（不含大模型长回答场景）
  - 地图区域定位响应 < 1s（本地环境）
- 可维护性：接口契约统一、文档与实现同步更新。

---

## 8. 里程碑（建议）

### M1（1周）
- 完成后端主线统一。
- 调整 docker-compose 对齐主后端。
- 打通前后端稳定联调。

### M2（1~2周）
- 完成文档管理闭环。
- 完成空间联动闭环。
- 完成基础异常处理与日志补全。

### M3（2周）
- 完成 hybrid search。
- 完成统计与反馈体系。
- 输出可演示版本与验收清单。

---

## 9. 验收标准（对齐当前目标）

1. 用户在前端发起检索，可得到引用明确的回答与文档列表。
2. 若回答包含行政区信息，可触发地图定位/高亮。
3. 上传新文档后可在检索中命中并回溯详情。
4. 本地与 docker 启动方式行为一致，不再出现接口主线冲突。
5. 配置中无生产可见的默认密钥。

---

## 10. 附录：项目内关键文件
- 后端入口（主）：`Backend/main.py`
- 后端入口（并行）：`src/geoai/api/main.py`
- 检索服务：`Backend/app/services/search_service.py`
- 空间服务：`Backend/app/services/spatial_service.py`
- 前端主应用：`frontend/src/App.tsx`
- 前端 API 配置：`frontend/src/lib/api/config.ts`
- 容器编排：`docker-compose.yml`
