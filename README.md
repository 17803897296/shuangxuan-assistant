# 双选会会前规划 · 企业优先级与岗位证据原型

面向本科生和研究生的校招双选会会前规划原型。

帮助学生把一份企业名单变成一张有依据的行动顺序：先走哪几家、哪些可以看、哪些暂时不用去。  
当前版本仍在验证阶段，**不是招聘、投递、资格判定或录用预测平台**。

> 本项目范围是「双选会会前规划」，不是 CareerSafari 职业探索 / O*NET 知识库项目。  
> 演示页面标题沿用 CareerSafari，仅作为页面品牌，不代表包含职业探索知识库。  
> 最近一次数据整理：2026-11-02  
> 当前状态：本地原型，岗位证据、匹配解释和企业岗位库链条已跑通；真实学生试点尚未开始。

---

## 1. 项目解决什么问题

一场双选会，学生真正能花在每家企业上的时间非常有限。

以南京大学秋招为例：

```text
3.5 小时 ÷ 118 家 ≈ 107 秒 / 家
```

学生只够做一个动作：**决定停不停**。

但会前能拿到的信息，往往只有一张企业名单。没有岗位、没有方向、没有依据，最后只能凭名字和感觉逛。

这个项目做的是**会前规划**：

- 把企业名单变成有依据的优先级排序
- 把分数拆成可解释的命中项和缺口
- 把岗位要求、来源和原文证据保留下来
- 让“为什么排在这里”可以被检查

不做：

- 简历投递
- 面试安排
- 资格判定
- 录用概率预测
- 现场实时调度

---

## 2. 当前进度

截至 2026-11-02 的本地数据：

| 项目       | 当前值                |
| -------- | ------------------:|
| 企业名单     | 118 家              |
| 有岗位记录的企业 | 37 家               |
| 岗位总数     | 277 条              |
| 资料不全企业   | 81 家               |
| 企业端契约测试  | 14 项通过             |
| 数据库查询链测试 | 2 项通过              |
| MCP 服务   | `company-jobs` 已连接 |

43 家初筛样本的漏斗：

```text
118 家名单
→ LLM 初筛 43 家
→ 最初只有 10 家抓到岗位
→ 规则补抓 + 无头浏览器 + BOSS 试点后 15 家有岗位
→ 其余 28 家进入「资料不全」
```

后来对 118 家做了全量搜索，当前是：

```text
37 家有岗位 / 81 家资料不全
```

这些数字是**本地原型状态**，不代表行业覆盖，也不代表岗位仍在招聘。

---

## 3. 产品做什么

### 3.1 用户画像

学生填写：

- 专业、年级、求职阶段
- 期望城市 / 家乡
- 方向兴趣
- 六项偏好权重：薪资、稳定、成长、自主、影响、平衡
- 目标企业类型
- 课程、项目、实习等经历

缺失信息保留为「未知」，不由系统猜测。

### 3.2 企业岗位

每家企业记录：

- 企业名称、城市、性质、行业
- 岗位列表
- 岗位来源
- 岗位证据
- 来源 URL
- 采集日期

每个岗位记录：

- 岗位名称
- 岗位要求
- 招聘人数
- 紧急程度
- 组别
- 薪资

### 3.3 匹配结果

匹配结果不只给一个分数，还会写清楚：

- 命中了什么
- 没中什么
- 基础分怎么算
- 偏好系数怎么算
- 加分项是什么
- 每一项匹配度的依据

同时保留：

- 岗位原文
- 岗位来源
- 企业信息

---

## 4. 匹配规则

当前公式：

```text
基础分 = 城市 40 + 方向 35 + 紧急度 25
偏好系数 = 1 + Σ(六项权重 × 匹配度) / 100
加分 = 企业类型 +5 / 家乡 +8
最终分 = 基础分 × 偏好系数 + 加分
```

结果四舍五入到 1 位小数。

分档：

```text
前 30%     优先去
30%–70%    可以看
其余       建议放弃
```

没有岗位记录的企业单独进入：

```text
资料不全
```

「资料不全」不占前三档名额，避免把“没有岗位数据”误当成“岗位不匹配”。

### 4.1 可解释性

每个企业会展示：

- 城市是否命中
- 方向是否命中
- 岗位是否急招
- 薪资、稳定、成长、自主、影响、平衡各自命中了什么
- 企业类型 / 家乡加分
- 反向词命中
- 未查到岗位时，哪些判断只能按中性分

### 4.2 当前权重状态

六项偏好权重是**待校准的工程参数**。

还没有经过真实学生试点校准，因此：

- 不输出录用概率
- 不声称推荐准确率
- 不把分数当成“企业好坏”
- 只把它当成会前时间分配的建议

---

## 5. 数据来源

当前岗位数据来自：

1. 企业官网 / 官方招聘页
2. 学校就业网
3. Bing 搜索结果
4. BOSS 直聘（可选，登录态试点，低频）
5. 无登录公开页 / 无头浏览器渲染

原则：

```text
宁可写「未知」，不要编。
```

抓不到岗位时：

- 保留企业城市、性质、行业
- 标记「岗位未查到」
- 放入「资料不全」
- 不用其他公司的岗位填充

### 5.1 已接入的无登录 MCP

```text
company-jobs-mcp
工具：search_company_jobs(company, city, limit)
```

特点：

- 不登录
- 不带 Cookie
- 不碰 BOSS 账号
- 来源：企业官网、学校就业网、公开招聘页
- JS 页面用无头 Edge 渲染

实测：

```text
国元证券股份有限公司
→ 财富管理管培生
→ source: https://gyzq.zhiye.com/jobs
```

### 5.2 BOSS 直聘说明

BOSS 数据只作为补充：

- 需要登录，存在账号风险
- 不适合批量高频抓取
- 薪资数字有字体混淆，当前写成「未知」
- 已写入：东方财富 15 条、腾讯 15 条（与原有 5 条合并为 20 条）
- 京东方北京搜到的主要是车间/质检岗，未写入

BOSS **不是默认数据源**。

---

## 6. 架构

```text
企业名单
   ↓
企业岗位采集
  ├─ 官网 / 学校就业网
  ├─ 无登录 MCP
  ├─ 无头 Edge 渲染 JS 页面
  └─ BOSS（可选，低频）
   ↓
岗位数据库 / 缓存
   ↓
用户画像
   ↓
匹配引擎
   ↓
优先级 + 分档 + 命中项 + 证据
   ↓
页面
```

### 6.1 数据库优先查询链

用户搜索企业时：

```text
JobStore.lookup(company)
   ├─ 命中 → 直接返回（data_source = "db"）
   └─ 未命中
        ↓
   在线搜索 portal.build(company)
        ↓
   JobStore.save(company, item)
        ↓
   返回（data_source = "online"）
```

当前数据库实现是 `NullJobStore`：

```text
不读、不写，所有请求都回退到在线搜索
```

组员实现自己的 `JobStore` 后，调用：

```python
job_store.set_store(MyJobStore())
```

即可接 SQLite / Postgres / 现有服务。

接口：

```python
class JobStore:
    def lookup(self, company): ...
    def save(self, company, item): ...
```

数据库 schema、TTL、公司别名、负缓存、去重由组员决定；当前只建链条，不写数据库内容。

详见 `ent_tools/数据库接入说明.md`。

---

## 7. 快速开始

### 7.1 本地运行

```bash
git clone https://github.com/17803897296/shuangxuan-assistant.git
cd shuangxuan-assistant
cd ent_tools
python -X utf8 portal.py --port 8099
```

打开：

```text
http://127.0.0.1:8099/fair.html
```

健康检查：

```text
http://127.0.0.1:8099/api/health
```

返回 `{"ok": true}` 即启动成功。

Windows 没有 Python 时，也可以直接双击：

```text
ent_tools\启动演示服务.cmd
```

### 7.2 Docker（可选）

```bash
docker compose up -d --build
```

打开 `http://127.0.0.1:8099/fair.html`。停止：

```bash
docker compose down
```

### 7.3 模型密钥（可选）

```bash
cp ent_tools/.env.example ent_tools/.env
```

在 `ent_tools/.env` 里配置：

```dotenv
DEEPSEEK_API_KEY=...
DEEPSEEK_BASE_URL=...
DEEPSEEK_MODEL=...
```

没有密钥时，规则抽取和确定性匹配仍可运行。

### 7.4 测试

```bash
cd ent_tools
python -X utf8 -m unittest discover -s tests -v
```

---

## 8. 项目结构

```text
demo/
  fair.html                 双选会主页面
  index.html                完整版页面
  start.html                入口
  collect.html              采集页
  questionnaire.json        问卷定义
  README.md                 架构与交接契约

ent_tools/
  portal.py                 本地 8099 服务、企业搜索、岗位补全
  build_fair_page.py        生成 demo/fair.html
  enrich_by_llm.py          企业资料采集
  build_cache.py            归一、补缺、证据校验
  company_module.py         企业端契约生成与校验
  sync_lookup_to_companies.py  合并在线岗位到 companies.json
  company_jobs_mcp.py       无登录公司岗位 MCP
  job_lookup.py             数据库优先、在线兜底的查询链
  job_store.py              岗位数据库接口
  tests/                    契约测试、数据库链条测试
  data/
    company_names.json      企业名单
    research_cache.json     采集结果
    companies.json          企业端契约数据
    lookup_cache.json       在线查询缓存
    _43_funnel_report.md    43 家漏斗报告
    _43_funnel.csv          43 家逐家明细

opencode.json               本地 MCP 配置
Dockerfile                  可选 Docker 部署
docker-compose.yml          Docker Compose
.dockerignore               Docker 构建忽略
交付说明.md                 交付与验收说明
```

---

## 9. 企业端契约

企业记录固定 10 个字段：

```text
id name nature city industry jobs
jobSource jobEvidence source date
```

岗位固定 6 个字段：

```text
title requirements headcount urgency group salary
```

缺失值统一写字符串：

```text
"未知"
```

不允许 `null`、空字符串或省略 key。

枚举：

```text
nature      央企 / 国企 / 民企 / 外企 / 合资 / 事业单位
industry    制造 / 医药 / IT / 金融 / 建筑 / 能源 / 消费 / 物流 / 教育 / 政府事业 / 其他
urgency     高 / 中 / 低 / 未知
jobSource   校招公告 / 社招信息 / 主营业务推测 / 未知
tier        优先去 / 可以看 / 建议放弃 / 资料不全
```

校验：

```powershell
cd ent_tools
.runtime\python\python.exe -X utf8 company_module.py --validate data/companies.json
```

---

## 10. 当前限制

1. 118 家企业里只有 37 家有岗位记录，81 家资料不全。
2. 岗位来源覆盖不均，金融、供应链、部分制造业仍需补不同企业的近期来源。
3. BOSS 直聘有登录/反爬限制，不能作为稳定的默认数据源。
4. BOSS 薪资数字有字体混淆，当前写「未知」。
5. 没有真实学生试点，推荐效果和权重尚未校准。
6. 不做录用概率预测，不判断学生是否“够格”。
7. 岗位可能已过期，页面不承诺岗位仍在招聘。
8. 数据库 schema、TTL、公司别名、去重策略尚未落地。
9. 职业探索 / O*NET 知识库不在本项目范围内。

---

## 11. 三条不能让的

### ① 查不到写「未知」，不许编

宁可一条数据没有，不要一条看着像的。错误的数据比没有数据更糟。

### ② 分数必须可解释

不能只给排序。每个分数要能回答：

```text
为什么高？为什么低？命中了什么？没中什么？
```

### ③ 演示必须能离线

采集可以联网，演示那一刻必须能本地跑。

---

## 12. 参考

- 架构与交接契约：`demo/README.md`
- 数据库接入：`ent_tools/数据库接入说明.md`
- 43 家漏斗分析：`ent_tools/data/_43_funnel_report.md`
- 交付说明：`交付说明.md`
- 企业端字段与校验：`ent_tools/企业端_README.md`

---

## License

内部原型，暂未确定开源许可证。
