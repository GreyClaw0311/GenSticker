# 表情包模板自动生成系统 - 需求文档

**文档版本**: v1.0
**创建时间**: 2026-03-12
**作者**: 灰 (OpenClaw Assistant)

---

## 一、项目概述

### 1.1 项目背景

表情包已成为互联网社交的重要表达方式，但目前表情包生成主要依赖人工选择模板和编辑，缺乏智能化和自动化能力。

### 1.2 项目目标

开发一套**表情包模板自动生成系统**，实现：
1. 根据用户输入（图片/文字/图文）自动生成表情包
2. 自动爬取网络热梗，转化为可用模板
3. 支持静态图片和GIF动图输出

### 1.3 类似项目调研

| 项目 | 技术栈 | 特点 | GitHub星标 |
|------|--------|------|-----------|
| **memetron3000** | Python + FastAPI + LLM | AI生成+模板驱动 | 14⭐ |
| **MemeFast** | 全平台应用 | 2000+模板+AI生成 | 商业产品 |
| **Meme-It** | JavaScript + Fabric.JS | Canvas编辑+Imgflip API | 29⭐ |
| **Imgflip API** | - | 提供热门表情包模板 | - |

---

## 二、功能需求

### 2.1 核心功能

#### 功能1: 表情包生成

**输入**:
- 文字：用户输入文本内容
- 图片：用户上传自定义图片
- 图文组合：文字+图片混合

**处理**:
- 自动匹配最合适的模板
- 将用户输入填充到模板指定位置
- 支持字体、颜色、位置调整

**输出**:
- 静态图片 (PNG/JPG)
- GIF动图

#### 功能2: 模板管理

**模板来源**:
1. **固定模板库**: 预设经典表情包模板
2. **爬取模板**: 从表情包网站自动爬取热门模板
3. **用户上传**: 用户自定义模板

**模板结构**:
```json
{
  "template_id": "drake_hotline",
  "name": "Drake Hotline Bling",
  "image_url": "templates/drake.jpg",
  "regions": [
    {
      "type": "text",
      "position": {"x": 100, "y": 50},
      "size": {"width": 200, "height": 50},
      "font": "Impact",
      "color": "#FFFFFF",
      "stroke": "#000000"
    }
  ],
  "tags": ["reaction", "comparison"],
  "popularity": 95
}
```

#### 功能3: 热梗爬取

**爬取目标**:
- 表情包网站: imgflip.com, memedroid.com, 9gag.com
- 社交平台: Reddit r/memes, Twitter热点
- 中文平台: 斗图啦, 表情包网站

**爬取策略**:
- 定时爬取（每日/每周）
- 按热度排序
- 去重处理
- 自动识别可编辑区域

#### 功能4: 智能匹配

**AI文本匹配**:
- 用户输入文本
- 分析文本情感/主题
- 推荐最合适的模板

**图像匹配**:
- 分析用户上传图片
- 识别图片内容
- 匹配模板风格

### 2.2 扩展功能

#### 功能5: AI生成模式

- 基于LLM生成表情包文案
- AI自动选择模板
- AI生成全新表情包

#### 功能6: 批量生成

- 支持多组输入批量生成
- 模板变体生成

#### 功能7: 社交分享

- 一键分享到社交平台
- 生成分享链接

---

## 三、技术方案

### 方案A: 传统图像处理方案

**技术栈**:
- 后端: Python + Flask/FastAPI
- 图像处理: Pillow (PIL), OpenCV
- 前端: React/Vue + Fabric.js

**优点**:
- 不依赖AI，成本低
- 响应速度快
- 可控性强

**缺点**:
- 需要预设模板
- 智能化程度低

**架构图**:
```
用户输入 → 模板匹配 → 图像合成 → 输出表情包
              ↓
         模板库(数据库)
```

### 方案B: AI驱动方案

**技术栈**:
- 后端: Python + FastAPI
- LLM: OpenAI GPT-4 / Claude / Gemini
- 图像生成: DALL-E / Stable Diffusion
- 图像处理: Pillow

**优点**:
- 智能化程度高
- 可生成创意表情包
- 自动匹配能力强

**缺点**:
- API成本高
- 响应速度慢
- 生成结果不稳定

**架构图**:
```
用户输入 → LLM分析 → 模板推荐/生成 → 图像合成 → 输出表情包
              ↓
         AI模型API
```

### 方案C: 混合方案 ⭐ 推荐

**技术栈**:
- 后端: Python + FastAPI
- 图像处理: Pillow, OpenCV
- 可选LLM: OpenRouter (多模型接入)
- 前端: React + Fabric.js

**优点**:
- 兼顾成本和智能
- 响应速度可控
- 灵活性强

**缺点**:
- 架构复杂
- 需要维护多套逻辑

**架构图**:
```
                    ┌─────────────┐
                    │  用户输入    │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │  意图分析    │
                    └──────┬──────┘
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
   ┌─────▼─────┐     ┌─────▼─────┐     ┌─────▼─────┐
   │ 简单模式   │     │ 智能模式   │     │ AI生成    │
   │ (传统)    │     │ (匹配)    │     │ (LLM)    │
   └─────┬─────┘     └─────┬─────┘     └─────┬─────┘
         │                 │                 │
         └─────────────────┼─────────────────┘
                           │
                    ┌──────▼──────┐
                    │  图像合成    │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │  输出表情包  │
                    └─────────────┘
```

---

## 四、数据库设计

### 4.1 模板表 (templates)

| 字段 | 类型 | 说明 |
|------|------|------|
| id | INT | 主键 |
| name | VARCHAR | 模板名称 |
| image_url | VARCHAR | 模板图片URL |
| regions | JSON | 可编辑区域定义 |
| tags | VARCHAR[] | 标签 |
| category | VARCHAR | 分类 |
| popularity | INT | 热度分数 |
| source | VARCHAR | 来源 |
| created_at | TIMESTAMP | 创建时间 |

### 4.2 生成记录表 (generations)

| 字段 | 类型 | 说明 |
|------|------|------|
| id | INT | 主键 |
| template_id | INT | 模板ID |
| input_text | TEXT | 输入文本 |
| input_images | JSON | 输入图片 |
| output_url | VARCHAR | 输出图片URL |
| mode | VARCHAR | 生成模式 |
| created_at | TIMESTAMP | 创建时间 |

### 4.3 热梗表 (trending_memes)

| 字段 | 类型 | 说明 |
|------|------|------|
| id | INT | 主键 |
| title | VARCHAR | 标题 |
| image_url | VARCHAR | 图片URL |
| source_url | VARCHAR | 来源URL |
| platform | VARCHAR | 平台 |
| hotness | INT | 热度 |
| crawled_at | TIMESTAMP | 爬取时间 |

---

## 五、API设计

### 5.1 核心API

#### POST /api/v1/generate

**请求**:
```json
{
  "mode": "smart",  // simple/smart/ai
  "input": {
    "text": "当你的代码终于跑通",
    "images": ["base64..."]
  },
  "template_id": "drake_hotline",  // 可选
  "output_format": "png",  // png/jpg/gif
  "options": {
    "font": "Impact",
    "font_size": 24,
    "text_color": "#FFFFFF"
  }
}
```

**响应**:
```json
{
  "success": true,
  "result": {
    "image_url": "https://cdn.example.com/meme_xxx.png",
    "template_used": "drake_hotline",
    "created_at": "2026-03-12T15:30:00Z"
  }
}
```

#### GET /api/v1/templates

**响应**:
```json
{
  "templates": [
    {
      "id": "drake_hotline",
      "name": "Drake Hotline Bling",
      "thumbnail": "https://...",
      "popularity": 95,
      "tags": ["reaction", "comparison"]
    }
  ],
  "total": 100,
  "page": 1
}
```

#### POST /api/v1/recommend

**请求**:
```json
{
  "text": "我不想上班"
}
```

**响应**:
```json
{
  "recommendations": [
    {
      "template_id": "office_no",
      "confidence": 0.85,
      "preview_url": "https://..."
    }
  ]
}
```

#### POST /api/v1/crawl

**请求**:
```json
{
  "source": "imgflip",
  "limit": 50
}
```

---

## 六、技术实现细节

### 6.1 图像合成模块

```python
from PIL import Image, ImageDraw, ImageFont

def generate_meme(template, text_regions, output_path):
    """生成表情包"""
    # 加载模板图片
    img = Image.open(template.image_path)
    draw = ImageDraw.Draw(img)
    
    # 填充文字
    for region in text_regions:
        font = ImageFont.truetype(region.font, region.font_size)
        draw.text(
            (region.x, region.y),
            region.text,
            font=font,
            fill=region.color,
            stroke_width=2,
            stroke_fill="black"
        )
    
    # 保存
    img.save(output_path)
```

### 6.2 模板区域识别

**方法1: 人工标注**
- 为每个模板手动定义可编辑区域

**方法2: 自动识别**
- 使用OCR识别模板中的文字区域
- 分析文字位置和大小
- 保存区域定义

### 6.3 热梗爬取模块

```python
import requests
from bs4 import BeautifulSoup

def crawl_imgflip():
    """爬取imgflip热门模板"""
    url = "https://imgflip.com/memetemplates"
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    
    templates = []
    for item in soup.select('.mt-item'):
        template = {
            'name': item.select_one('.mt-title').text,
            'image_url': item.select_one('img')['src'],
            'popularity': int(item.select_one('.mt-count').text)
        }
        templates.append(template)
    
    return templates
```

### 6.4 智能匹配算法

```python
from sentence_transformers import SentenceTransformer

class TemplateMatcher:
    def __init__(self):
        self.model = SentenceTransformer('all-MiniLM-L6-v2')
        self.template_embeddings = {}
    
    def match(self, user_text, top_k=5):
        """匹配最合适的模板"""
        # 编码用户输入
        query_embedding = self.model.encode(user_text)
        
        # 计算相似度
        scores = []
        for template_id, embedding in self.template_embeddings.items():
            score = cosine_similarity(query_embedding, embedding)
            scores.append((template_id, score))
        
        # 返回Top K
        return sorted(scores, key=lambda x: x[1], reverse=True)[:top_k]
```

---

## 七、项目计划

### 7.1 开发阶段

| 阶段 | 内容 | 工期 |
|------|------|------|
| **阶段一** | 基础框架搭建 | 1周 |
| - | 后端API框架 | 2天 |
| - | 数据库设计 | 1天 |
| - | 基础图像合成 | 2天 |
| **阶段二** | 核心功能开发 | 2周 |
| - | 模板管理系统 | 3天 |
| - | 热梗爬取模块 | 4天 |
| - | 智能匹配算法 | 3天 |
| **阶段三** | AI功能集成 | 1周 |
| - | LLM文本生成 | 3天 |
| - | AI模板推荐 | 2天 |
| **阶段四** | 前端开发 | 1周 |
| - | Web界面 | 3天 |
| - | 图片编辑器 | 2天 |
| **阶段五** | 测试与优化 | 1周 |
| - | 功能测试 | 2天 |
| - | 性能优化 | 2天 |
| - | 部署上线 | 1天 |

**总计**: 约6周

### 7.2 技术选型建议

| 组件 | 推荐方案 | 备选方案 |
|------|----------|----------|
| 后端框架 | FastAPI | Flask |
| 数据库 | PostgreSQL | MongoDB |
| 图像处理 | Pillow | OpenCV |
| 前端框架 | React | Vue |
| 编辑器 | Fabric.js | Konva.js |
| LLM | OpenRouter | OpenAI |
| 缓存 | Redis | - |
| 存储 | MinIO | S3 |

---

## 八、风险与对策

| 风险 | 影响 | 对策 |
|------|------|------|
| 爬取被封禁 | 高 | 使用代理池、控制频率、多源爬取 |
| API成本过高 | 中 | 混合方案、缓存热门模板 |
| 模板版权问题 | 高 | 使用开源/公共模板、用户自上传 |
| AI生成质量不稳定 | 中 | 后处理、人工审核机制 |
| 图片存储成本 | 中 | CDN加速、图片压缩 |

---

## 九、总结

### 推荐方案

**混合方案（方案C）** 是最佳选择：
1. **成本可控**: 传统模式成本低，AI模式按需使用
2. **功能完整**: 支持简单编辑、智能匹配、AI生成三种模式
3. **扩展性强**: 模块化设计，易于添加新功能
4. **用户体验好**: 响应速度快，生成质量可控

### 下一步

1. 确认技术方案
2. 搭建开发环境
3. 实现基础图像合成功能
4. 构建模板库

---

**文档版本**: v1.0
**最后更新**: 2026-03-12 23:30