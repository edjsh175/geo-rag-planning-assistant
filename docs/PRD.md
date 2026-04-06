# GeoAI 空间规划智能检索与可视化系统
## 产品需求文档（PRD）融合版 V2.2
**文档版本**：V2.2（业务规划 + 技术架构融合版 + GeoServer扩展 + 进度更新）
**编制日期**：2026年3月26日
**修订日期**：2026年4月2日
**产品定位**：测绘/空间规划领域垂直型Agentic RAG+WebGIS智能检索系统
**核心目标**：解决传统测绘标准库「检索低效、数据孤岛、无空间关联」痛点，打造可落地、可展示的企业级高标准全栈GeoAI产品（适配秋招技术展示需求）
**适用场景**：测绘行业从业者标准查询、GIS专业学生学习研究、秋招技术能力展示

### 修订历史
| 版本 | 修订日期 | 修订内容 | 修订人 |
|------|----------|----------|--------|
| V1.0 | 2026-03-20 | 初始业务规划版（豆包PRD） | 产品团队 |
| V2.0 | 2026-03-26 | 融合技术架构版（Claude分析） | 技术团队 |
| V2.1 | 2026-03-30 | 添加GeoServer OGC标准支持与Docker容器化部署方案 | 技术团队 |
| V2.2 | 2026-04-02 | 更新开发进度：阶段1后端完成、智谱API配置完成、服务启动验证 | 技术团队 |

### 文档融合说明
本PRD融合了：
1. **业务规划版（豆包PRD）**：用户画像、产品场景、功能模块、开发排期、风险控制
2. **技术架构版（Claude分析）**：数据字典、API契约、技术选型、异常熔断、防坑指南
3. **现有代码分析**：基于实际代码的数据库结构、配置参数、已有功能

---

## 目录
1. 产品概述
2. 核心目标
3. 用户画像（业务规划精华）
4. 核心功能需求（融合版）
5. 技术架构与数据契约（技术架构精华）
6. 开发排期与验收标准（业务规划精华）
7. 风险控制与异常熔断（技术架构精华）
8. 附录：数据库结构、API契约、配置参数

---

## 1. 产品概述
### 1.1 产品定位
面向**测绘工程、空间规划、自然资源管理**领域的垂直型智能检索系统，融合**大模型RAG（检索增强生成）** 与**WebGIS可视化**技术，打通「自然语言提问→双库混合检索→LLM智能总结→空间地理信息高亮」的全流程闭环，实现测绘标准的**语义化、结构化、空间化**查询。

### 1.2 核心价值
1. **解决行业痛点**：替代传统标准库「关键词模糊搜索、无上下文、无空间关联」的低效模式，支持自然语言提问，直接关联标准对应的地理区域/应用场景；
2. **技术展示价值**：作为GIS专业全栈项目，覆盖**爬虫数据采集、非结构化数据ETL、双库联动、向量检索、WebGIS可视化**全技术链路，适配秋招技术能力展示；
3. **学习研究价值**：为GIS学生提供「标准查询+空间关联」的一体化工具，降低测绘标准学习的门槛。

### 1.3 核心特性
- 双库联动：MySQL元数据管理 + PostgreSQL+pgvector+PostGIS向量+空间存储；
- 智能检索：自然语言提问→意图识别→元数据过滤+向量召回→LLM总结；
- 空间联动：检索结果自动抽取空间指令，驱动WebGIS地图实时高亮目标区域；
- 企业级稳定性：支持安全解压、幂等性入库、API熔断重试，适配Windows生产环境。

---

## 2. 核心目标
### 2.1 业务目标
1. 实现300+测绘标准文档的**结构化入库、语义化检索、空间化展示**；
2. 支持自然语言提问的准确率≥85%，检索结果相关性≥90%；
3. 打通「文本检索→空间可视化」的闭环，实现检索结果与地理区域的精准关联。

### 2.2 技术目标
1. 落地**企业级双库联动架构**，实现元数据与向量数据的实时同步；
2. 保证脚本/系统的**稳定性与兼容性**，支持Windows环境无报错运行，重复执行无冗余数据；
3. 打造**可扩展的全栈架构**，预留功能接口（如标准上传、空间数据导入、大模型切换）；
4. 实现核心功能的**可展示性**，适配秋招技术讲解与演示需求。

### 2.3 体验目标
1. 操作极简：无需专业技术背景，自然语言提问即可获取结果；
2. 响应高效：检索请求响应时间≤3s，地图高亮无延迟；
3. 结果清晰：检索结果包含**标准原文引用、元数据、空间关联信息**，可溯源、可验证。

---

## 3. 用户画像（业务规划精华）
本产品核心用户分为3类，各用户的核心需求、使用场景、痛点如下：

| 用户画像       | 核心使用场景                | 核心需求                          | 核心痛点                          |
|----------------|-----------------------------|-----------------------------------|-----------------------------------|
| 测绘/规划从业者 | 工作中查询测绘标准、应用场景 | 精准查询标准原文、关联适用地理区域 | 传统搜索低效，无空间关联，需手动匹配 |
| GIS专业学生    | 课程学习、毕业设计、科研     | 理解标准内涵、关联空间应用场景    | 标准晦涩，无可视化辅助，学习效率低 |
| 秋招面试官     | 考察候选人全栈技术能力      | 查看产品技术架构、功能落地性      | 项目同质化严重，无专业领域壁垒    |

**核心设计原则**：以「测绘/规划从业者」的实际需求为核心，兼顾「GIS学生」的学习需求与「秋招面试官」的技术展示需求。

---

## 4. 核心功能需求（融合版）
### 4.1 数据管理模块（后台）【基于现有代码增强】
#### 4.1.1 压缩包安全解压功能（已实现100%）
- **现有实现**：`build_vector_db.py`中的`safe_extract_archives()`函数已实现按分类/标准名创建独立目录
- **待增强**：支持更多压缩格式，添加解压异常监控和重试机制
- **数据流转**：`压缩包目录` → `md_extracted_safe/{分类}/{标准号+标准名}/` → `full.md`

#### 4.1.2 双库元数据联动功能（已实现100%）
- **现有实现**：`build_vector_db.py`中的`get_standard_metadata()`函数已实现MySQL查询11个元数据字段
- **关键优化**：标准号自动转换（`_`→`/`），已实现MySQL模糊匹配逻辑
- **数据契约**：
  ```sql
  -- MySQL表：standard_norm_detail（现有）
  -- 字段：standard_code, keyword, draft_unit, drafter, chinese_name, english_name,
  --      release_date, implement_date, release_unit, charge_unit,
  --      replace_standard, standard_status, application_scope, reference_standard, pdf_path, ps

  -- PostgreSQL表：policy_chunks（现有）
  -- 字段：id, standard_code, category, keyword, chinese_name, english_name, release_date,
  --      implement_date, standard_status, release_unit, charge_unit, draft_unit,
  --      replace_standard, application_scope, document_name, header_path, content, embedding
  ```

### 4.2 知识库构建模块（后台）【基于现有代码增强】
#### 4.2.1 Markdown智能切分功能（已实现100%）
- **现有实现**：`build_vector_db.py`中的切分逻辑（Markdown标题切分+500字符细切）
- **切片增强**：已实现元数据注入到切片头部，提升检索相关性

#### 4.2.2 文本增强与向量化功能（已实现100%）
- **现有实现**：集成智谱`embedding-3`模型生成2048维向量，添加指数退避重试装饰器
- **重试机制**：指数退避重试（1s→2s→4s→8s，最多4次），网络波动时自动重试
- **向量存储**：PostgreSQL + pgvector扩展，支持余弦相似度计算

#### 4.2.3 幂等性入库功能（已实现100%）
- **现有实现**：先删除同标准旧记录，再批量插入新记录，事务保护
- **待优化**：批量插入性能优化（每100条提交一次）

### 4.3 智能检索模块（核心前台+后台）【待开发】
#### 4.3.1 自然语言查询功能（API契约）
```json
// 请求体
{
  "query": "四川省滑坡防治的最新标准是什么？",
  "category": "滑坡",  // 可选
  "top_k": 5,  // 召回数量
  "include_spatial": true  // 是否提取空间指令
}

// 响应体
{
  "status": "success",
  "answer": "根据检索结果，四川省最新的滑坡防治标准是...",
  "references": [
    {
      "standard_code": "DB61/T 1533-2022",
      "chinese_name": "公路上边坡崩塌滑坡灾害风险评估指南",
      "category": "崩塌",
      "release_date": "2022-12-01",
      "draft_unit": "四川省交通规划设计研究院",
      "excerpt": "本标准适用于四川省范围内...",
      "similarity_score": 0.92
    }
  ],
  "spatial_action": {
    "action_type": "highlight_region",
    "adcode": "510000",
    "region_name": "四川省",
    "coordinates": [[...]]  // GeoJSON坐标
  },
  "processing_time": 2.3
}
```

#### 4.3.2 混合检索功能（技术架构核心）
1. **意图识别**：调用智谱大模型提取关键词、分类、标准号、空间区域
2. **元数据过滤**：`WHERE category = '滑坡' AND implement_date >= '2020-01-01'`
3. **向量召回**：余弦相似度计算，召回Top-K切片
4. **LLM总结**：基于召回切片生成自然语言回答，标注引用

#### 4.3.3 空间指令提取功能（WebGIS联动）
- **输入**：LLM回答 + 召回切片
- **输出**：标准化的空间指令JSON
- **空间数据源**：行政区划GeoJSON（ADCODE映射表）

### 4.4 WebGIS可视化模块（前台核心）【待开发】
#### 4.4.1 地图基础功能
- **技术栈**：Vue3 + OpenLayers 7.0+
- **底图**：高德电子地图/卫星地图切换
- **操作**：缩放、平移、复位、比例尺显示
- **图层服务**：支持加载GeoServer发布的WMS/WFS服务，实现企业级GIS架构（可选）

#### 4.4.2 空间指令联动功能
- **区域高亮**：接收`spatial_action`指令，加载GeoJSON并以红色半透明多边形高亮
- **视角定位**：自动飞至高亮区域，自适应缩放级别
- **多区域支持**：不同区域不同颜色区分

#### 4.4.3 空间信息展示功能
- **信息弹窗**：鼠标悬停显示区域名称、ADCODE、关联标准号
- **标准跳转**：点击标准号跳转到左侧引用卡片

### 4.5 系统管理模块（后台）【业务规划补充】
#### 4.5.1 配置管理功能
- **数据库配置**：MySQL/PostgreSQL连接信息，支持连接测试
- **大模型配置**：智谱API Key、模型选择、请求超时、重试次数
- **检索配置**：向量召回数、切片大小、重叠度

#### 4.5.2 日志管理功能
- **日志类型**：入库日志、检索日志、错误日志、操作日志
- **日志筛选**：按类型、时间、关键词筛选
- **日志导出**：支持导出为CSV格式

#### 4.5.3 数据统计功能
- **知识库统计**：已入库标准数、切片数、分类数、向量库大小
- **检索统计**：总检索次数、成功率、平均响应时间

---

## 5. 技术架构与数据契约（技术架构精华）
### 5.1 技术栈选型
```
数据层：
├── MySQL 8.0+：元数据存储（现有）
├── PostgreSQL 18+ + pgvector 0.5+：向量存储（现有）
├── PostgreSQL 18+ + PostGIS 3.0+：空间数据存储（待添加）
└── 本地文件系统：压缩包、Markdown文件、日志

服务层（FastAPI 0.100+）：
├── 数据处理模块：基于现有build_vector_db.py增强
├── 向量工程模块：基于现有向量化逻辑增强
├── 智能检索模块：全新开发（RAG+LLM）
├── 系统管理模块：全新开发
└── API网关模块：请求校验、响应格式化

GIS服务层（GeoServer 2.24+ & OGC标准）：
├── WMS（Web Map Service）：发布行政区划、基础底图等地图切片服务
├── WFS（Web Feature Service）：发布矢量要素服务，支持前端动态查询与渲染
├── 样式服务（SLD）：自定义地图渲染样式，适配不同业务场景
└── 数据源连接：连接PostGIS空间数据库，实现空间数据动态发布

前端层（Vue3 3.3+）：
├── 首页模块：左文右图布局，查询+地图
├── 知识库管理模块：压缩包上传、入库进度
├── 系统管理模块：配置、日志、统计
└── 公共组件模块：地图组件、弹窗组件、日志组件
```

**GIS服务架构说明**：
本系统提供两种GIS服务方案，适应不同场景需求：

1. **轻量级WebGIS方案（MVP推荐）**：
   - **架构**：后端FastAPI直接从PostGIS查询空间数据，生成GeoJSON发送给前端OpenLayers渲染。
   - **优点**：开发快速、部署简单、适合敏捷开发与MVP验证。
   - **适用场景**：秋招技术展示、快速原型验证、轻量级应用。

2. **正统GIS企业级方案（GeoServer + OGC标准）**：
   - **架构**：将PostGIS中的行政区划数据通过GeoServer发布为OGC标准服务（WMS/WFS），前端OpenLayers通过调用OGC服务加载地图图层。
   - **优点**：符合企业级GIS架构规范、支持地图缓存、切片加速、样式分离、多源数据集成。
   - **适用场景**：秋招中展示GIS架构深度、生产环境部署、复杂空间分析需求。

**部署建议**：
- 秋招技术展示：推荐使用**轻量级方案**，快速实现核心功能演示。
- 生产环境/长期运维：推荐使用**正统GIS方案**，提升系统可维护性与扩展性。
- 两种方案可并行实现，通过配置开关切换，兼顾演示与生产需求。

### 5.2 数据字典（核心契约）
#### 5.2.1 MySQL表：standard_norm_detail（现有）
```sql
CREATE TABLE standard_norm_detail (
  id INT AUTO_INCREMENT PRIMARY KEY,
  standard_code VARCHAR(100) NOT NULL COMMENT '标准号（如DB61/T 1533-2022）',
  keyword VARCHAR(100) COMMENT '关键词/分类',
  draft_unit VARCHAR(255) COMMENT '起草单位',
  drafter VARCHAR(255) COMMENT '起草人',
  chinese_name VARCHAR(500) COMMENT '中文名称',
  english_name VARCHAR(500) COMMENT '英文名称',
  release_date DATE COMMENT '发布日期',
  implement_date DATE COMMENT '实施日期',
  release_unit VARCHAR(255) COMMENT '发布单位',
  charge_unit VARCHAR(255) COMMENT '归口单位',
  replace_standard VARCHAR(500) COMMENT '代替标准',
  standard_status VARCHAR(50) DEFAULT '现行' COMMENT '标准状态',
  application_scope TEXT COMMENT '适用范围',
  reference_standard TEXT COMMENT '引用标准',
  pdf_path VARCHAR(500) COMMENT 'PDF文件路径',
  ps TEXT COMMENT '备注信息',
  UNIQUE KEY uk_standard_code (standard_code)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

#### 5.2.2 PostgreSQL表：policy_chunks（现有+空间扩展）
```sql
-- 现有表结构（已通过fix_db_schema.py修复）
CREATE TABLE policy_chunks (
    id bigserial PRIMARY KEY,
    standard_code varchar(100),  -- 核心标识1：标准号
    category varchar(100),       -- 核心标识2：行业关键词
    keyword varchar(100),        -- 关键词（从MySQL同步）
    chinese_name varchar(500),   -- 中文名称（从MySQL同步）
    english_name varchar(500),   -- 英文名称（从MySQL同步）
    release_date varchar(20),    -- 发布日期（从MySQL同步）
    implement_date varchar(20),  -- 实施日期（从MySQL同步）
    standard_status varchar(50), -- 标准状态
    release_unit varchar(255),   -- 发布单位（从MySQL同步）
    charge_unit varchar(255),    -- 归口单位（从MySQL同步）
    draft_unit varchar(255),     -- 起草单位（从MySQL同步）
    replace_standard varchar(500), -- 代替标准（从MySQL同步）
    application_scope text,      -- 适用范围（从MySQL同步）
    document_name varchar(255),  -- 原始文件名（压缩包名）
    header_path text,            -- 章节层级（Markdown标题路径）
    content text,                -- 具体文本切片（原始内容）
    embedding vector(2048)       -- embedding-3模型输出的向量
);

-- 待添加：空间扩展表（PostGIS）
CREATE TABLE spatial_regions (
    id SERIAL PRIMARY KEY,
    adcode VARCHAR(10) NOT NULL COMMENT '行政区划代码',
    region_name VARCHAR(100) NOT NULL COMMENT '区域名称',
    geometry GEOMETRY(MultiPolygon, 4326) COMMENT '空间几何',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(adcode)
);

CREATE INDEX idx_spatial_regions_geometry ON spatial_regions USING GIST(geometry);
```

### 5.3 API接口契约（开发团队必须遵守）
#### 5.3.1 检索接口：`POST /api/search`
```json
// 请求示例
{
  "query": "四川省滑坡防治标准",
  "category": "滑坡",
  "top_k": 5,
  "filter": {
    "min_date": "2020-01-01",
    "status": "现行"
  }
}

// 响应示例（成功）
{
  "code": 200,
  "message": "success",
  "data": {
    "answer": "检索到3个相关标准...",
    "references": [...],
    "spatial_action": {...},
    "processing_time": 2.1,
    "query_id": "q_20260326123045_abc123"
  }
}

// 响应示例（失败）
{
  "code": 500,
  "message": "向量检索失败，请检查网络连接",
  "data": null,
  "fallback_answer": "检索服务暂时不可用，请稍后重试"
}
```

#### 5.3.2 知识库管理接口：`POST /api/knowledge/upload`
```json
// 请求：多部分表单数据
// 字段：file (压缩包文件), category (分类), force (是否强制重建)

// 响应
{
  "code": 200,
  "message": "上传成功，正在处理...",
  "data": {
    "task_id": "task_20260326123045_xyz789",
    "status": "processing",
    "estimated_time": 30
  }
}
```

#### 5.3.3 系统状态接口：`GET /api/system/status`
```json
{
  "code": 200,
  "data": {
    "database": {
      "mysql": {"connected": true, "tables": 5, "standards": 150},
      "postgresql": {"connected": true, "chunks": 5200, "vectors": "2048维"}
    },
    "api": {
      "zhipuai": {"available": true, "rate_limit_remaining": 950},
      "embedding": {"model": "embedding-3", "dimension": 2048}
    },
    "performance": {
      "avg_response_time": 2.3,
      "total_queries": 1245,
      "success_rate": 96.2
    }
  }
}
```

### 5.4 异常熔断策略（工业级保障）
#### 5.4.1 大模型API熔断
```python
# 指数退避重试机制（已实现为装饰器版本）
def exponential_backoff(max_retries=4, base_delay=1):
    """
    指数退避重试装饰器
    重试序列: 1s, 2s, 4s, 8s
    """
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_retries):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_retries - 1:
                        print(f"[错误] 达到最大重试次数，执行失败: {e}")
                        return None
                    delay = base_delay * (2 ** attempt)
                    print(f"[重试] 接口调用失败: {e}，{delay}秒后进行第{attempt + 2}次重试...")
                    time.sleep(delay)
        return wrapper
    return decorator

# 使用示例
@exponential_backoff(max_retries=4)
def get_embedding(text):
    # 调用智谱AI embedding-3 的逻辑
    pass
```

#### 5.4.2 零召回降级话术
```python
FALLBACK_RESPONSES = {
    "no_results": "抱歉，未找到与您问题直接相关的标准。建议：1) 尝试其他关键词；2) 扩大分类范围；3) 联系管理员添加相关标准。",
    "api_error": "检索服务暂时不可用，请稍后重试。当前展示为缓存结果或示例数据。",
    "timeout": "请求超时，可能是网络问题或服务器繁忙。简化查询或稍后重试可能有效。"
}
```

#### 5.4.3 数据库连接池与重连
- **连接池配置**：最小5连接，最大20连接
- **重连策略**：断开后自动重连（最多3次），间隔3秒
- **健康检查**：每分钟检查一次数据库连接状态

---

## 6. 开发排期与验收标准（业务规划精华）
### 6.1 开发排期（4周）
#### 阶段1：需求梳理与环境搭建（第1周，共7天）
- **核心任务**：确认产品需求、搭建开发环境、初始化项目结构
- **具体工作**：
  1. ✅ 细化功能需求，确认技术栈与架构（已完成）
  2. ✅ 搭建MySQL/PostgreSQL数据库，安装pgvector/PostGIS插件（已完成）
  3. 🔄 初始化Python后端项目（FastAPI）与Vue3前端项目（后端完成，前端待开发）
  4. ✅ 申请智谱AI API Key，完成接口调试（已配置GLM-4.5-Air和Embedding-3）
  5. ✅ 准备测绘标准压缩包、MySQL元数据、行政区划GeoJSON数据（部分完成）

#### 阶段2：后台核心功能开发（第2周，共7天）
- **核心任务**：开发数据管理、知识库构建、智能检索、系统管理模块的后台逻辑与API接口
- **具体工作**：
  1. ✅ 基于现有`build_vector_db.py`增强，添加PostGIS支持（部分完成，大模型重试机制已完成）
  2. 🟡 开发意图识别、混合检索、LLM总结、空间指令提取功能（LLM总结已实现）
  3. ⬜ 开发配置管理、日志管理、数据统计功能
  4. ⬜ 编写所有功能的API接口，完成接口文档

#### 阶段3：前端核心功能开发（第3周，共7天）
- **核心任务**：开发首页、知识库管理、系统管理的前端页面与交互，对接后台API
- **具体工作**：
  1. ⬜ 开发首页左文右图布局，实现查询输入、分类筛选功能
  2. ⬜ 开发检索结果展示模块，实现回答、引用卡片、导出功能
  3. ⬜ 集成OpenLayers地图，实现地图基础操作、GeoJSON加载、区域高亮功能
  4. ⬜ 开发知识库管理、系统管理页面，对接后台配置、日志、统计API

#### 阶段4：联调测试与BUG修复（第4周前3天，共3天）
- **核心任务**：前后端联调、功能测试、性能测试、兼容性测试，修复发现的BUG
- **具体工作**：
  1. ⬜ 全流程联调：从压缩包入库到自然语言查询再到地图高亮
  2. ⬜ 功能测试：逐个验证所有功能点是否满足需求
  3. ⬜ 性能测试：测试检索响应时间、入库效率、地图加载速度
  4. ⬜ 兼容性测试：在不同浏览器、不同操作系统上测试

#### 阶段5：验收上线与文档编写（第4周后4天，共4天）
- **核心任务**：产品验收、一键部署脚本开发、产品/开发/部署文档编写
- **具体工作**：
  1. ⬜ 按验收标准进行产品验收
  2. ⬜ 开发Windows/Linux一键部署脚本
  3. ⬜ 编写产品需求文档（PRD）、开发文档、部署文档、使用手册
  4. ⬜ 整理项目代码，提交至代码仓库
  5. ⬜ 制作产品演示视频，适配秋招展示需求

### 6.2 验收标准（可量化、可验证）
#### 6.2.1 功能验收标准
1. **数据管理模块**
   - ✅ 支持ZIP/RAR/7Z三种压缩包格式，批量解压无报错（现有）
   - ✅ 标准号自动转换（_→/），元数据同步成功率≥99%（现有）

2. **知识库构建模块**
   - ✅ Markdown切分保留标题层级（现有）
   - ✅ 文本增强正确注入元数据，向量化成功率≥99%（已验证）
   - ✅ 幂等性入库有效，重复执行无冗余数据（现有）

3. **智能检索模块**
   - ⬜ 自然语言查询支持示例问题及自定义问题
   - ⬜ 意图识别准确率≥85%，能正确提取分类、标准号、空间区域
   - ⬜ 混合检索结果相关性≥90%，按相似度+发布时间排序
   - ⬜ LLM总结回答准确、简洁，符合专业语境
   - ⬜ 空间指令提取准确率≥80%，能正确提取行政区划ADCODE
   - ⬜ 检索响应时间≤3s，支持5个并发查询无明显延迟

4. **WebGIS可视化模块**
   - ⬜ 地图支持缩放、平移、底图切换，基础操作无卡顿
   - ⬜ 接收空间指令后，地图飞至高亮区域+渲染≤0.5s，高亮无偏移
   - ⬜ 鼠标悬停高亮区域显示弹窗，点击标准号可跳转到对应引用卡片
   - ⬜ 

#### 6.2.2 非功能验收标准
1. **性能验收**：单压缩包入库≤30s，检索响应≤3s，地图加载≤1s
2. **兼容性验收**：Windows 10/11、Chrome/Edge/Firefox浏览器无乱码、无报错
3. **稳定性验收**：连续运行72小时无崩溃，重复执行操作无报错

---

## 7. 风险控制与异常熔断（技术架构精华）
### 7.1 技术风险与应对措施
#### 风险1：pgvector/PostGIS插件安装配置复杂
- **风险等级**：高
- **应对措施**：
  - 提前制作详细的安装配置教程（包含Windows/Linux）
  - 提供Docker容器化部署方案作为备选
  - 遇到问题在GIS技术社区（如GIS Stack Exchange）寻求帮助

#### 风险2：大模型意图识别/空间指令提取准确率不足
- **风险等级**：中
- **应对措施**：
  - 优化大模型提示词，针对测绘专业场景定制模板
  - 实现多级召回策略：语义召回→关键词召回→全文召回
  - 添加人工修正接口，收集错误样本持续优化

#### 风险3：WebGIS地图高亮偏移、卡顿
- **风险等级**：中
- **应对措施**：
  - 使用简化版行政区划GeoJSON（简化多边形点数）
  - 实现地图瓦片缓存机制，减少重复加载
  - 添加地图性能监控，自动降级显示模式

### 7.2 开发风险与应对措施
#### 风险1：开发时间不足，无法按排期完成
- **风险等级**：高
- **应对措施**：
  - MVP原则：优先开发核心功能（入库、检索、地图高亮）
  - 每日站会跟踪进度，及时调整任务优先级
  - 准备好降级方案（如先实现基础检索，再增强智能检索）

#### 风险2：前后端联调接口不兼容
- **风险等级**：中
- **应对措施**：
  - 提前制定API接口规范（使用OpenAPI/Swagger）
  - 后端先编写接口文档，前端按文档开发
  - 每日进行接口联调，发现问题及时修复

### 7.3 运营风险与应对措施
#### 风险1：智谱AI API Key过期/额度用尽
- **风险等级**：中
- **应对措施**：
  - 实现API额度监控，低额度时给出警告提示
  - 预留备用API Key轮换机制
  - 添加本地Embedding模型作为降级方案（如Sentence-BERT）

#### 风险2：测绘标准数据更新，本地数据无法同步
- **风险等级**：低
- **应对措施**：
  - 预留标准数据在线更新接口
  - 设计增量更新机制，避免全量重建
  - 后期可扩展爬虫自动更新功能

---

## 8. 附录：关键配置与代码参考
### 8.1 当前项目配置（基于config.py和config_pachong.py）
```python
# 智谱AI配置（现有）
ZHIPU_API_KEY = "f8acb3f151a0410897278c2a620abedf.OyMbU8EFpwYp6lsU"
EMBEDDING_MODEL = "embedding-3"
VECTOR_DIMENSION = 2048

# PostgreSQL配置（现有）
DB_CONFIG = {
    "dbname": "geoai_db",
    "user": "postgres",
    "password": "postgres",
    "host": "localhost",
    "port": "5432"
}

# MySQL配置（现有）
MYSQL_CONFIG = {
    "host": "localhost",
    "port": 3306,
    "user": "root",
    "password": "root",
    "database": "disaster_knowledge",
    "charset": "utf8mb4"
}

# 文件路径配置（现有）
COMPRESSED_DIR = r"D:\work\shixi\py\md_output"  # 压缩包目录
SAFE_EXTRACT_DIR = "md_extracted_safe"  # 安全解压目录
```

### 8.2 待添加的PostGIS配置
```python
# 新增到config.py
POSTGIS_CONFIG = {
    "dbname": "geoai_spatial",
    "user": "postgres",
    "password": "postgres",
    "host": "localhost",
    "port": "5432"
}

# 行政区划数据配置
GEOJSON_DIR = "data/geojson"  # GeoJSON文件目录
REGION_MAPPING = {
    "510000": "四川省",
    "110000": "北京市",
    "310000": "上海市",
    # ... 其他省级行政区划
}
```

### 8.3 核心代码位置参考
1. **知识库构建**：`build_vector_db.py`（主入口）
2. **MySQL元数据查询**：`build_vector_db.py`中的`get_standard_metadata()`
3. **向量化函数**：`build_vector_db.py`中的`get_embedding()`
4. **PostgreSQL表修复**：`fix_db_schema.py`
5. **数据清理**：`clean_and_rebuild.py`
6. **数据验证**：`verify_data.py`
7. **现有爬虫逻辑**：`search_module.py`、`grab_module.py`（可作为数据采集参考）

### 8.4 下一步行动清单（更新于2026-04-03）
1. **已完成**：搭建FastAPI项目框架，配置智谱API（GLM-4.5-Air + Embedding-3），实现大模型重试机制
2. **当前重点**：完成智能检索核心逻辑（LLM总结已实现，意图识别+空间指令提取待开发）
3. **下一阶段**：开发Vue3前端页面和OpenLayers地图组件
4. **最后阶段**：联调测试、性能优化、文档编写与部署

### 8.5 Docker容器化部署方案
为提升系统可移植性与部署效率，本项目提供完整的Docker容器化部署方案，支持一键启动所有依赖服务。

#### 8.5.1 服务容器规划
```yaml
# docker-compose.yml 核心服务定义
version: '3.8'

services:
  # PostgreSQL + PostGIS + pgvector 三合一数据库
  postgis:
    image: postgis/postgis:16-3.4
    container_name: geoai-postgis
    environment:
      POSTGRES_DB: geoai_db
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgis_data:/var/lib/postgresql/data
      - ./init-scripts:/docker-entrypoint-initdb.d  # 初始化脚本
    restart: unless-stopped

  # GeoServer 地图服务
  geoserver:
    image: geoserver/geoserver:2.24.0
    container_name: geoai-geoserver
    environment:
      GEOSERVER_ADMIN_USER: admin
      GEOSERVER_ADMIN_PASSWORD: geoserver
    ports:
      - "8080:8080"
    volumes:
      - geoserver_data:/opt/geoserver/data_dir
      - ./geoserver/styles:/styles  # 自定义样式
    depends_on:
      - postgis
    restart: unless-stopped

  # MySQL 元数据数据库
  mysql:
    image: mysql:8.0
    container_name: geoai-mysql
    environment:
      MYSQL_DATABASE: disaster_knowledge
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
      - ./mysql-init:/docker-entrypoint-initdb.d
    restart: unless-stopped

  # FastAPI 后端服务
  backend:
    build: ./backend
    container_name: geoai-backend
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@postgis:5432/geoai_db
      - MYSQL_URL=mysql://root:root@mysql:3306/disaster_knowledge
      - GEOSERVER_URL=http://geoserver:8080/geoserver
    ports:
      - "8000:8000"
    depends_on:
      - postgis
      - mysql
      - geoserver
    restart: unless-stopped

  # Vue3 前端服务（Nginx）
  frontend:
    build: ./frontend
    container_name: geoai-frontend
    ports:
      - "80:80"
    depends_on:
      - backend
    restart: unless-stopped

volumes:
  postgis_data:
  mysql_data:
  geoserver_data:
```

#### 8.5.2 部署流程
1. **环境准备**：安装Docker与Docker Compose，克隆项目代码。
2. **配置调整**：根据实际环境修改`docker-compose.yml`中的端口、密码等配置。
3. **一键启动**：执行 `docker-compose up -d` 启动所有服务。
4. **服务验证**：
   - 访问 `http://localhost:80` 打开前端界面
   - 访问 `http://localhost:8000/docs` 查看后端API文档
   - 访问 `http://localhost:8080/geoserver` 登录GeoServer管理界面（admin/geoserver）
5. **数据初始化**：通过后端管理接口上传压缩包，自动构建知识库。

#### 8.5.3 秋招演示优势
- **环境隔离**：避免本地环境配置冲突，确保演示环境一致。
- **快速部署**：面试前5分钟即可拉起完整系统，展示工程化能力。
- **架构展示**：容器化架构体现现代DevOps理念，提升技术印象分。
- **扩展便捷**：可轻松添加Redis缓存、Elasticsearch检索等组件。

---

## 文档审批
**产品负责人**：__________
**技术负责人**：__________
**开发团队**：GIS专业开发团队
**文档状态**：✅ 已完成业务规划、技术架构融合与GeoServer扩展
**下一步**：阶段1后端完成、智谱API配置完成，阶段2智能检索模块中LLM总结已实现，待开发意图识别与空间指令提取功能

> **备注**：本PRD既可用于指导开发团队实现具体功能，也可用于秋招面试展示完整的产品思维和技术架构能力。建议将第3章（用户画像）、第6章（开发排期）用于产品能力展示，第5章（技术架构）、第7章（风险控制）用于技术能力展示。