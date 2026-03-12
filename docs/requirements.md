# GenSticker - 表情包模板自动生成系统需求文档

**文档版本**: v2.0
**创建时间**: 2026-03-12
**最后更新**: 2026-03-13
**作者**: 灰 (OpenClaw Assistant)

---

## 一、项目概述

### 1.1 项目背景

表情包已成为互联网社交的重要表达方式，但目前表情包生成主要依赖人工选择模板和编辑，缺乏智能化和自动化能力。用户在制作表情包时面临以下痛点：

1. **模板选择困难** - 面对海量模板，不知道哪个最合适
2. **意图表达不清** - 想表达的情绪难以转化为合适的模板
3. **编辑繁琐** - 需要手动调整文字位置、字体、大小等

### 1.2 项目目标

开发一套 **GenSticker 表情包智能生成系统**，实现以下核心功能：

```
用户输入（图片/文本/图文） → AI意图识别 → Top 9模板推荐 → 一键生成表情包
```

**核心价值主张**：
- 🎯 **智能意图识别** - 自动理解用户想表达的情绪/场景
- 🔍 **多候选推荐** - 提供 Top 9 最匹配模板，给用户选择空间
- ⚡ **一键生成** - 选择模板后即刻生成，无需繁琐编辑

### 1.3 竞品调研

| 项目 | 核心功能 | 意图识别 | Top N推荐 | 开源/API | 评价 |
|------|----------|---------|-----------|----------|------|
| **MemeFast** | AI生成+2000模板 | ✅ 文本 | ❌ 仅1个 | ❌ 无 | 商业产品，无法集成 |
| **memetron3000** | LLM+模板驱动 | ✅ 文本 | ❌ 无 | ✅ 开源 | 功能较基础 |
| **Imgflip API** | 模板库+生成 | ❌ 无 | ❌ 无 | ✅ API | 无智能推荐 |
| **GenSticker** | 智能生成系统 | ✅ 图+文+混合 | ✅ Top 9 | ✅ 开源 | **本项目** |

---

## 二、核心功能需求

### 2.1 功能概述

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           GenSticker 核心流程                            │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐             │
│   │   用户输入    │ ──▶│  AI意图识别   │ ──▶│ Top 9推荐    │             │
│   │ (图/文/图文)  │    │              │    │              │             │
│   └──────────────┘    └──────────────┘    └──────┬───────┘             │
│                                                   │                     │
│                                                   ▼                     │
│                                          ┌──────────────┐              │
│                                          │  用户选择    │              │
│                                          │  (9选1)      │              │
│                                          └──────┬───────┘              │
│                                                   │                     │
│                                                   ▼                     │
│                                          ┌──────────────┐              │
│                                          │  一键生成    │              │
│                                          │  表情包      │              │
│                                          └──────────────┘              │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2.2 详细功能需求

#### 功能1: 多模态输入

**输入类型**:

| 输入类型 | 描述 | 示例 |
|---------|------|------|
| **纯文本** | 用户输入一段文字描述 | "当你的代码终于跑通" |
| **纯图片** | 用户上传一张图片 | 上传一张猫咪照片 |
| **图文混合** | 图片 + 文字描述 | 猫咪照片 + "我：想要零食" |

**输入处理**:
```json
{
  "input_type": "text|image|mixed",
  "text": "用户输入的文本内容",
  "image": "base64编码的图片数据",
  "context": "可选的上下文信息"
}
```

#### 功能2: AI意图识别

**识别维度**:

| 维度 | 说明 | 示例 |
|------|------|------|
| **情感** | 情绪类型 | 开心、悲伤、愤怒、惊讶、无奈 |
| **场景** | 使用场景 | 工作、生活、学习、社交 |
| **主题** | 内容主题 | 对比、反应、吐槽、炫耀 |
| **风格** | 表达风格 | 幽默、讽刺、正能量、丧文化 |

**意图识别输出**:
```json
{
  "intent": {
    "emotion": "无奈",
    "scene": "工作",
    "theme": "吐槽",
    "style": "幽默",
    "keywords": ["加班", "周末", "老板"],
    "confidence": 0.92
  }
}
```

#### 功能3: Top 9 模板推荐 ⭐ 核心功能

**推荐策略**:
1. **语义匹配** - 基于意图与模板标签的语义相似度
2. **热度排序** - 结合模板流行度分数
3. **个性化** - 可选的用户偏好学习
4. **多样性** - 确保推荐结果有一定差异性

**推荐输出**:
```json
{
  "recommendations": [
    {
      "rank": 1,
      "template_id": "drake_hotline",
      "template_name": "Drake Hotline Bling",
      "thumbnail_url": "https://...",
      "confidence": 0.95,
      "match_reason": "完美匹配'吐槽+工作'场景"
    },
    {
      "rank": 2,
      "template_id": "distracted_boyfriend",
      "template_name": "Distracted Boyfriend",
      "thumbnail_url": "https://...",
      "confidence": 0.88,
      "match_reason": "适合表达'对比'主题"
    }
    // ... 共9个推荐
  ],
  "total": 9,
  "intent_summary": "检测到'工作吐槽'意图，推荐对比/反应类模板"
}
```

**UI展示**:
```
┌─────────────────────────────────────────────────┐
│  为您推荐 9 个最匹配的模板：                      │
│                                                 │
│  ┌───┐ ┌───┐ ┌───┐                             │
│  │ 1 │ │ 2 │ │ 3 │   ← 第一行 (最推荐)          │
│  └───┘ └───┘ └───┘                             │
│                                                 │
│  ┌───┐ ┌───┐ ┌───┐                             │
│  │ 4 │ │ 5 │ │ 6 │   ← 第二行                  │
│  └───┘ └───┘ └───┘                             │
│                                                 │
│  ┌───┐ ┌───┐ ┌───┐                             │
│  │ 7 │ │ 8 │ │ 9 │   ← 第三行                  │
│  └───┘ └───┘ └───┘                             │
│                                                 │
│         [ 点击选择，一键生成 ]                   │
└─────────────────────────────────────────────────┘
```

#### 功能4: 一键生成表情包

**生成流程**:
1. 用户从 Top 9 中选择一个模板
2. 系统自动填充文字/图片到模板
3. 预览生成结果
4. 用户可微调（可选）
5. 确认保存/分享

**生成选项**:
```json
{
  "template_id": "drake_hotline",
  "auto_fill": true,
  "text_positions": "auto",
  "font": "Impact",
  "output_format": "png",
  "quality": "high"
}
```

---

## 三、技术架构

### 3.1 整体架构

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              前端层 (Web/App)                            │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │   输入组件   │  │   推荐展示   │  │   预览编辑   │  │   分享组件   │    │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘    │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                              API网关层                                   │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                      FastAPI + 负载均衡                          │   │
│  └─────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
        ┌───────────────────────────┼───────────────────────────┐
        ▼                           ▼                           ▼
┌───────────────┐          ┌───────────────┐          ┌───────────────┐
│   意图识别服务  │          │   推荐服务     │          │   生成服务     │
│               │          │               │          │               │
│ ┌───────────┐ │          │ ┌───────────┐ │          │ ┌───────────┐ │
│ │ 文本理解   │ │          │ │ 向量检索   │ │          │ │ 图像合成   │ │
│ │ (LLM)     │ │          │ │ (Milvus)  │ │          │ │ (Pillow)  │ │
│ └───────────┘ │          │ └───────────┘ │          │ └───────────┘ │
│ ┌───────────┐ │          │ ┌───────────┐ │          │ ┌───────────┐ │
│ │ 图像理解   │ │          │ │ 排序算法   │ │          │ │ 文字渲染   │ │
│ │ (CLIP/BLIP)│ │          │ │ (规则+ML) │ │          │ │ (Pillow)  │ │
│ └───────────┘ │          │ └───────────┘ │          │ └───────────┘ │
└───────────────┘          └───────────────┘          └───────────────┘
        │                           │                           │
        └───────────────────────────┼───────────────────────────┘
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                              数据层                                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │  PostgreSQL │  │   Redis     │  │   Milvus    │  │   MinIO     │    │
│  │  (元数据)   │  │  (缓存)     │  │  (向量库)   │  │  (图片存储)  │    │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘    │
└─────────────────────────────────────────────────────────────────────────┘
```

### 3.2 核心模块设计

#### 模块1: 意图识别模块

**技术选型**:

| 输入类型 | 技术方案 | 说明 |
|---------|---------|------|
| **文本** | LLM (GPT-4/Claude/GLM) | 理解语义、提取关键词、识别情感 |
| **图片** | CLIP / BLIP-2 | 图像内容理解、图文匹配 |
| **图文混合** | 多模态LLM (GPT-4V/GLM-4V) | 综合理解图像和文本 |

**实现示例**:
```python
class IntentRecognizer:
    def __init__(self, llm_client, image_encoder):
        self.llm = llm_client
        self.encoder = image_encoder
    
    async def recognize(self, input_data: InputData) -> Intent:
        """识别用户意图"""
        if input_data.type == "text":
            return await self._recognize_text(input_data.text)
        elif input_data.type == "image":
            return await self._recognize_image(input_data.image)
        else:  # mixed
            return await self._recognize_mixed(input_data)
    
    async def _recognize_text(self, text: str) -> Intent:
        prompt = f"""
        分析以下文本，提取用户想要表达的表情包意图：
        
        文本：{text}
        
        请返回JSON格式：
        {{
            "emotion": "情感(开心/悲伤/愤怒/惊讶/无奈等)",
            "scene": "场景(工作/生活/学习/社交等)",
            "theme": "主题(对比/反应/吐槽/炫耀等)",
            "style": "风格(幽默/讽刺/正能量/丧文化等)",
            "keywords": ["关键词1", "关键词2"],
            "suitable_template_types": ["模板类型1", "模板类型2"]
        }}
        """
        result = await self.llm.generate(prompt)
        return Intent.parse_raw(result)
```

#### 模块2: 模板推荐模块

**技术方案**:

```
意图向量 → 向量检索(Top 50) → 重排序 → Top 9
                ↑
            模板向量库
```

**实现示例**:
```python
class TemplateRecommender:
    def __init__(self, vector_store, reranker):
        self.store = vector_store
        self.reranker = reranker
    
    async def recommend(self, intent: Intent, top_k: int = 9) -> List[Template]:
        """推荐Top K模板"""
        
        # 1. 将意图转换为查询向量
        query_text = f"{intent.emotion} {intent.scene} {intent.theme} {' '.join(intent.keywords)}"
        query_vector = self.encoder.encode(query_text)
        
        # 2. 向量检索 (召回Top 50)
        candidates = await self.store.search(query_vector, top_k=50)
        
        # 3. 重排序 (精排Top 9)
        scored = []
        for template in candidates:
            score = self._compute_score(intent, template)
            scored.append((template, score))
        
        scored.sort(key=lambda x: x[1], reverse=True)
        top_9 = [t for t, s in scored[:9]]
        
        return top_9
    
    def _compute_score(self, intent: Intent, template: Template) -> float:
        """综合评分"""
        scores = []
        
        # 语义匹配分 (权重: 0.4)
        semantic_score = cosine_similarity(intent.vector, template.vector)
        scores.append(0.4 * semantic_score)
        
        # 热度分 (权重: 0.3)
        popularity_score = template.popularity / 100.0
        scores.append(0.3 * popularity_score)
        
        # 标签匹配分 (权重: 0.3)
        tag_score = len(set(intent.keywords) & set(template.tags)) / max(len(intent.keywords), 1)
        scores.append(0.3 * tag_score)
        
        return sum(scores)
```

#### 模块3: 表情包生成模块

**实现示例**:
```python
class MemeGenerator:
    def __init__(self, font_path: str):
        self.font_path = font_path
    
    async def generate(
        self, 
        template: Template, 
        user_input: InputData,
        output_path: str
    ) -> str:
        """生成表情包"""
        
        # 加载模板
        img = Image.open(template.image_path)
        draw = ImageDraw.Draw(img)
        
        # 根据用户输入填充
        if user_input.type == "text":
            text = user_input.text
        elif user_input.type == "image":
            # 将用户图片融入模板
            img = self._blend_images(img, user_input.image, template.regions)
            text = None
        else:  # mixed
            img = self._blend_images(img, user_input.image, template.regions)
            text = user_input.text
        
        # 渲染文字
        if text:
            for region in template.text_regions:
                self._render_text(draw, region, text)
        
        # 保存
        img.save(output_path, quality=95)
        return output_path
    
    def _render_text(self, draw, region, text):
        """渲染文字"""
        font = ImageFont.truetype(
            self.font_path, 
            region.font_size
        )
        draw.text(
            (region.x, region.y),
            text,
            font=font,
            fill=region.color,
            stroke_width=2,
            stroke_fill="black"
        )
```

---

## 四、数据库设计

### 4.1 模板表 (templates)

| 字段 | 类型 | 说明 |
|------|------|------|
| id | UUID | 主键 |
| name | VARCHAR(255) | 模板名称 |
| description | TEXT | 模板描述 |
| image_url | VARCHAR(512) | 模板图片URL |
| thumbnail_url | VARCHAR(512) | 缩略图URL |
| text_regions | JSONB | 文字区域定义 |
| image_regions | JSONB | 图片区域定义 |
| tags | VARCHAR(64)[] | 标签列表 |
| category | VARCHAR(64) | 分类 |
| emotion_tags | VARCHAR(32)[] | 情感标签 |
| scene_tags | VARCHAR(32)[] | 场景标签 |
| theme_tags | VARCHAR(32)[] | 主题标签 |
| popularity | INT | 热度分数 |
| usage_count | INT | 使用次数 |
| embedding | VECTOR(768) | 语义向量 |
| is_active | BOOLEAN | 是否启用 |
| created_at | TIMESTAMP | 创建时间 |
| updated_at | TIMESTAMP | 更新时间 |

### 4.2 推荐记录表 (recommendations)

| 字段 | 类型 | 说明 |
|------|------|------|
| id | UUID | 主键 |
| session_id | UUID | 会话ID |
| user_input_type | VARCHAR(16) | 输入类型 |
| user_input_text | TEXT | 用户输入文本 |
| user_input_image | VARCHAR(512) | 用户输入图片 |
| intent_result | JSONB | 意图识别结果 |
| recommended_templates | UUID[] | 推荐模板ID列表 |
| selected_template | UUID | 用户选择的模板 |
| created_at | TIMESTAMP | 创建时间 |

### 4.3 生成记录表 (generations)

| 字段 | 类型 | 说明 |
|------|------|------|
| id | UUID | 主键 |
| recommendation_id | UUID | 关联推荐记录 |
| template_id | UUID | 使用的模板 |
| output_url | VARCHAR(512) | 输出图片URL |
| output_format | VARCHAR(8) | 输出格式 |
| created_at | TIMESTAMP | 创建时间 |

---

## 五、API设计

### 5.1 意图识别API

#### POST /api/v1/intent/recognize

**请求**:
```json
{
  "input_type": "text",
  "text": "当你的代码终于跑通",
  "options": {
    "detailed": true
  }
}
```

**响应**:
```json
{
  "success": true,
  "intent": {
    "emotion": "开心",
    "scene": "工作",
    "theme": "炫耀",
    "style": "幽默",
    "keywords": ["代码", "跑通", "终于"],
    "suitable_types": ["reaction", "celebration"],
    "confidence": 0.94
  }
}
```

### 5.2 模板推荐API

#### POST /api/v1/templates/recommend

**请求**:
```json
{
  "input": {
    "type": "text",
    "text": "当你的代码终于跑通"
  },
  "options": {
    "top_k": 9,
    "include_thumbnails": true,
    "diversity": 0.3
  }
}
```

**响应**:
```json
{
  "success": true,
  "intent_summary": "检测到'工作炫耀'意图，推荐庆祝/反应类模板",
  "recommendations": [
    {
      "rank": 1,
      "template_id": "drake_hotline",
      "template_name": "Drake Hotline Bling",
      "thumbnail_url": "https://cdn.example.com/thumb/drake.jpg",
      "confidence": 0.95,
      "match_reason": "适合表达'对比/成功'场景",
      "tags": ["reaction", "comparison"]
    }
    // ... 共9个
  ],
  "total": 9
}
```

### 5.3 表情包生成API

#### POST /api/v1/meme/generate

**请求**:
```json
{
  "template_id": "drake_hotline",
  "input": {
    "type": "text",
    "text": "当你的代码终于跑通"
  },
  "options": {
    "font": "Impact",
    "font_size": "auto",
    "text_color": "#FFFFFF",
    "output_format": "png",
    "quality": "high"
  }
}
```

**响应**:
```json
{
  "success": true,
  "result": {
    "meme_id": "meme_abc123",
    "image_url": "https://cdn.example.com/memes/meme_abc123.png",
    "thumbnail_url": "https://cdn.example.com/memes/thumb/meme_abc123.png",
    "template_used": "drake_hotline",
    "created_at": "2026-03-13T08:00:00Z"
  }
}
```

### 5.4 一站式API (推荐 + 生成)

#### POST /api/v1/meme/create

**请求**:
```json
{
  "input": {
    "type": "text",
    "text": "当你的代码终于跑通"
  },
  "mode": "recommend",  // recommend: 返回推荐 / generate: 直接生成第一个
  "options": {
    "top_k": 9,
    "auto_select": false  // true时自动选择第一个模板生成
  }
}
```

**响应 (mode=recommend)**:
```json
{
  "success": true,
  "intent": { ... },
  "recommendations": [ ... ],  // Top 9
  "session_id": "sess_xyz789"  // 用于后续生成
}
```

---

## 六、技术选型

### 6.1 推荐技术栈

| 层级 | 组件 | 技术选型 | 备选方案 |
|------|------|----------|----------|
| **前端** | 框架 | React 18 + TypeScript | Vue 3 |
| | UI组件 | Ant Design / Shadcn | Material UI |
| | 图片编辑 | Fabric.js | Konva.js |
| **后端** | 框架 | FastAPI (Python) | Flask |
| | 异步任务 | Celery + Redis | RQ |
| **AI/ML** | 文本理解 | GLM-4 / GPT-4 | Claude |
| | 图像理解 | CLIP / BLIP-2 | Florence-2 |
| | 向量检索 | Milvus | Pinecone |
| **存储** | 关系数据库 | PostgreSQL + pgvector | MySQL |
| | 缓存 | Redis | Memcached |
| | 对象存储 | MinIO | S3 |
| **部署** | 容器化 | Docker + K8s | Docker Compose |

### 6.2 LLM选择建议

| 场景 | 推荐模型 | 原因 |
|------|---------|------|
| **国内部署** | GLM-4 | 中文效果好，合规 |
| **海外部署** | GPT-4o-mini | 性价比高，多语言 |
| **私有化** | Llama 3 / Qwen2 | 开源可控 |
| **多模态** | GLM-4V / GPT-4V | 图文理解能力强 |

---

## 七、项目计划

### 7.1 开发阶段

| 阶段 | 内容 | 工期 | 里程碑 |
|------|------|------|--------|
| **阶段一** | 基础架构 | 1周 | 项目脚手架、数据库、API框架 |
| **阶段二** | 意图识别 | 1周 | 文本/图像意图识别模块 |
| **阶段三** | 模板推荐 | 1周 | 向量检索、Top 9推荐算法 |
| **阶段四** | 表情包生成 | 1周 | 图像合成、文字渲染 |
| **阶段五** | 前端开发 | 1.5周 | Web界面、交互优化 |
| **阶段六** | 测试优化 | 0.5周 | 功能测试、性能优化 |
| **阶段七** | 部署上线 | 0.5周 | Docker化、部署 |

**总计**: 约6.5周

### 7.2 MVP范围

**第一版 MVP 包含**:
- ✅ 文本输入 + 意图识别
- ✅ Top 9 模板推荐
- ✅ 一键生成表情包
- ✅ 基础Web界面

**后续版本**:
- 图片输入支持
- 图文混合输入
- 用户偏好学习
- GIF动图支持

---

## 八、风险与对策

| 风险 | 影响 | 概率 | 对策 |
|------|------|------|------|
| LLM响应延迟 | 中 | 高 | 异步处理 + 缓存热门意图 |
| 模板推荐不准 | 高 | 中 | 用户反馈机制 + 持续优化 |
| 图片版权问题 | 高 | 中 | 使用开源/授权模板 + 用户自上传 |
| 存储成本 | 中 | 低 | 图片压缩 + CDN加速 |
| 并发压力 | 中 | 中 | Redis缓存 + 水平扩展 |

---

## 九、成功指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| **意图识别准确率** | ≥ 85% | 用户反馈正确率 |
| **推荐命中率** | ≥ 70% | 用户从前9中选择的比例 |
| **生成速度** | ≤ 3秒 | 从选择到生成完成 |
| **用户满意度** | ≥ 4.0/5.0 | 应用评分 |

---

## 十、总结

### 核心亮点

1. **多模态输入** - 支持文本、图片、图文混合三种输入方式
2. **智能意图识别** - AI自动理解用户想表达的情绪和场景
3. **Top 9 推荐** - 提供多样选择，而非单一结果
4. **一键生成** - 选择模板后即刻输出，无需繁琐编辑

### 与竞品差异

| 特性 | MemeFast | GenSticker |
|------|----------|------------|
| 意图识别 | 仅文本 | 文本+图片+混合 |
| 推荐数量 | 1个 | **9个** |
| 开源/API | 无 | **完全开源** |
| 可定制性 | 无 | **高度可定制** |

---

**文档版本**: v2.0  
**最后更新**: 2026-03-13 07:50  
**更新内容**: 根据最新需求明确核心功能为"意图识别 + Top 9模板推荐 + 一键生成"