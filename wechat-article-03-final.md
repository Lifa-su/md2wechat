# 我花了一个周末，做了一个AI工具网站，分享完整过程

> 从想法到上线，48小时，零成本。这篇文章记录了我从0到1搭建一个AI工具箱网站的全过程——技术选型、踩坑记录、部署上线、成本分析，以及我对变现的一些思考。

![封面](images/article-03-cover.png)

---

## 起因：一个周五晚上的冲动

上周五下班回家，刷到一个帖子：有人做了一个简单的AI写作工具网站，挂了Google AdSense，月入几百刀。

评论区炸了，有人说"这也能赚钱？"，有人问技术栈，有人说"我也想做一个"。

我当时的反应是——这东西我也能做啊。

不是凡尔赛。认真想了一下，一个AI工具网站的核心就三件事：前端页面、调用大模型API、部署上线。2024年这三件事的门槛已经低到令人发指了。

于是我给自己定了个挑战：**这个周末，从零开始，做一个AI工具箱网站，周日晚上之前上线。**

泡了杯咖啡，打开电脑，开干。

---

## 第一步：想清楚做什么

在写第一行代码之前，我先花了半小时想清楚几个问题：

**做什么工具？**

市面上AI工具网站太多了，做大而全没意义。我决定做一个"小而美"的AI工具箱，先上3-5个高频小工具：

- AI文案改写（输入一段文字，换个风格输出）
- AI周报生成器（输入本周做了啥，生成一篇像样的周报）
- AI英文润色（中式英语 → 地道英语）
- AI代码解释器（贴一段代码，用大白话解释）
- AI取名助手（输入需求，生成各种名字）

这些工具有几个共同特点：需求真实、实现简单、每个工具就是一个prompt。

**目标用户是谁？**

不是程序员（程序员自己会调API），而是那些知道AI很厉害但不知道怎么用的普通人——运营、产品、学生、自媒体作者。

**核心体验是什么？**

打开网页 → 选工具 → 输入内容 → 点按钮 → 拿到结果。整个过程不超过10秒。不需要注册，不需要登录，先让用户爽到。

想清楚这些，心里就有底了。

---

## 第二步：技术选型——为什么是Next.js + TailwindCSS + DeepSeek

技术选型这件事，我的原则是：**周末项目不炫技，用最熟的、最快的、最省钱的。**

![技术架构图](images/article-03-chart-01.png)

### 前端框架：Next.js

选Next.js的理由很直接：

1. **全栈能力**。Next.js的API Routes让我不需要单独搞一个后端服务，前后端一个项目搞定。调用大模型API的逻辑直接写在`/api`路由里，干净利落。
2. **SSR + SEO友好**。工具网站要靠搜索引擎获取流量，Next.js的服务端渲染天然对SEO友好，这一点纯React SPA做不到。
3. **Vercel亲儿子**。部署到Vercel零配置，`git push`就上线，不用折腾服务器。

一行命令创建项目：

```bash
npx create-next-app@latest ai-toolbox --typescript --tailwind --app --src-dir
```

### UI样式：TailwindCSS

不用TailwindCSS的理由我已经想不到了。

以前写CSS，光给class起名字就能纠结半天——`.card-wrapper`还是`.card-container`？`.title-text`还是`.heading`？用了Tailwind之后，这种无意义的心智负担直接消失。

更重要的是**速度**。周末项目最怕的就是在样式上磨时间。Tailwind让我专注于布局和交互，而不是跟CSS文件较劲。配合Next.js的`create-next-app`，初始化的时候勾选Tailwind，开箱即用，零配置。

一个工具卡片组件，几行就搞定：

```tsx
function ToolCard({ title, desc, icon }: ToolCardProps) {
  return (
    <div className="group rounded-2xl border border-gray-200 p-6 
                    hover:border-blue-400 hover:shadow-lg 
                    transition-all duration-200 cursor-pointer">
      <div className="text-3xl mb-3">{icon}</div>
      <h3 className="font-semibold text-lg mb-2">{title}</h3>
      <p className="text-gray-500 text-sm">{desc}</p>
    </div>
  );
}
```

清晰、直观、不用切文件。

### 大模型API：DeepSeek

这是整个项目最关键的选择，也是我最想分享的部分。

很多人第一反应是用OpenAI的API。但我算了一笔账：

| 模型 | 输入价格（每百万token） | 输出价格（每百万token） |
|------|----------------------|----------------------|
| GPT-4o | $5.00 | $15.00 |
| GPT-4o mini | $0.15 | $0.60 |
| DeepSeek-V3 | $0.28 | $0.42 |

DeepSeek-V3的价格大概是GPT-4o的**二十分之一**，甚至比GPT-4o mini还便宜。而且如果命中缓存，输入价格低到$0.028/百万token，几乎可以忽略不计。

对于我这种工具网站来说，每次请求的token量不大（输入几百token，输出几百token），DeepSeek的成本优势是碾压级的。

更妙的是，DeepSeek的API**完全兼容OpenAI的SDK格式**。这意味着什么？意味着我用`openai`这个npm包就能调用DeepSeek，只需要改一下`baseURL`和`apiKey`：

```typescript
import OpenAI from 'openai';

const client = new OpenAI({
  baseURL: 'https://api.deepseek.com',
  apiKey: process.env.DEEPSEEK_API_KEY,
});
```

就这么简单。如果以后想切换到其他模型，改一行配置就行。

**注册DeepSeek开发者账号，新用户送500万token的免费额度**，够我测试和初期运营用很久了。

---

## 第三步：核心代码实现

整个项目的核心逻辑其实很简单：前端收集用户输入 → 调用后端API → 后端把请求转发给DeepSeek → 流式返回结果。

### 后端API路由

在Next.js的App Router里，创建一个API路由 `src/app/api/generate/route.ts`：

```typescript
import OpenAI from 'openai';
import { NextRequest } from 'next/server';

const client = new OpenAI({
  baseURL: 'https://api.deepseek.com',
  apiKey: process.env.DEEPSEEK_API_KEY,
});

// 每个工具对应一个system prompt
const TOOL_PROMPTS: Record<string, string> = {
  rewrite: '你是一个文案改写专家。用户会给你一段文字和目标风格，请按要求改写。保持原意，调整语气和表达方式。',
  weekly: '你是一个周报写作助手。用户会告诉你本周做了什么，请生成一篇结构清晰、措辞专业的周报。包含：本周完成、进行中、下周计划三个部分。',
  polish: '你是一个英文润色专家。用户会给你一段英文，请润色为地道、自然的英文表达。指出原文的问题并解释修改理由。',
  explain: '你是一个代码解释专家。用户会给你一段代码，请用通俗易懂的中文解释这段代码做了什么，逐行或逐块解释关键逻辑。',
  naming: '你是一个取名专家。根据用户的需求（产品名、品牌名、英文名等），生成10个创意名字，每个附上含义解释。',
};

export async function POST(req: NextRequest) {
  const { tool, input } = await req.json();
  
  const systemPrompt = TOOL_PROMPTS[tool];
  if (!systemPrompt) {
    return Response.json({ error: '未知工具' }, { status: 400 });
  }

  const stream = await client.chat.completions.create({
    model: 'deepseek-chat',
    messages: [
      { role: 'system', content: systemPrompt },
      { role: 'user', content: input },
    ],
    stream: true,
  });

  // 流式返回
  const encoder = new TextEncoder();
  const readable = new ReadableStream({
    async start(controller) {
      for await (const chunk of stream) {
        const text = chunk.choices[0]?.delta?.content || '';
        controller.enqueue(encoder.encode(text));
      }
      controller.close();
    },
  });

  return new Response(readable, {
    headers: { 'Content-Type': 'text/plain; charset=utf-8' },
  });
}
```

这段代码做了几件事：

1. 根据工具类型选择对应的system prompt
2. 调用DeepSeek API，开启流式输出
3. 把流式结果直接透传给前端

**为什么用流式输出？** 因为大模型生成文本需要几秒钟，如果等全部生成完再返回，用户会盯着一个loading转圈，体验很差。流式输出让文字一个一个蹦出来，像打字机一样，用户感知上快很多。

### 前端调用

前端用`fetch`读取流式响应：

```typescript
async function generate(tool: string, input: string) {
  setLoading(true);
  setResult('');
  
  const res = await fetch('/api/generate', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ tool, input }),
  });

  const reader = res.body?.getReader();
  const decoder = new TextDecoder();
  
  while (reader) {
    const { done, value } = await reader.read();
    if (done) break;
    setResult(prev => prev + decoder.decode(value));
  }
  
  setLoading(false);
}
```

就这么多。没有Redux，没有状态管理库，一个`useState`搞定。周末项目，够用就行。

---

## 第四步：踩坑记录

顺利吗？当然不。记录几个我踩过的坑：

### 坑1：流式响应在Vercel上的超时问题

Vercel的Serverless Function默认超时时间是10秒（免费版）。大模型生成长文本经常超过10秒，直接报504。

**解决方案：** 在`route.ts`里加上Edge Runtime声明：

```typescript
export const runtime = 'edge';
```

Edge Function的超时时间是30秒，而且对流式响应更友好。加了这一行，问题解决。

### 坑2：DeepSeek API偶尔响应慢

DeepSeek的API在高峰期（尤其是国内晚上8-10点）偶尔会慢，首个token的等待时间可能到3-5秒。

**解决方案：** 
- 前端加一个"AI正在思考中..."的提示，管理用户预期
- 设置合理的`max_tokens`，不要让模型生成太长的内容
- 后续考虑加一层缓存，相同输入直接返回缓存结果

### 坑3：Prompt调优比写代码花的时间还多

这是我没想到的。代码半天就写完了，但每个工具的prompt我反复调了十几版。

比如周报生成器，第一版prompt生成的周报太官方、太套话，像机器人写的。后来我在prompt里加了一句"语气要自然，像一个靠谱的同事在汇报工作，不要用'本周我积极推进了...'这种套话"，效果立刻好了很多。

**经验：prompt是产品的灵魂，值得花时间打磨。**

### 坑4：移动端适配

我一开始只在电脑上开发和测试，周六晚上用手机打开一看——输入框太小、按钮挤在一起、结果区域溢出。

好在用了TailwindCSS，响应式适配改起来很快：

```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  {tools.map(tool => <ToolCard key={tool.id} {...tool} />)}
</div>
```

`grid-cols-1 md:grid-cols-2 lg:grid-cols-3`——手机一列、平板两列、电脑三列。一行搞定。

---

## 第五步：部署到Vercel

部署是整个过程中最丝滑的环节，没有之一。

1. 把代码推到GitHub仓库
2. 打开 vercel.com，用GitHub账号登录
3. 点"Import Project"，选刚才的仓库
4. Vercel自动检测到这是Next.js项目，构建配置都帮你填好了
5. 在环境变量里加上`DEEPSEEK_API_KEY`
6. 点"Deploy"

大概等了40秒，网站就上线了。Vercel还自动分配了一个`.vercel.app`的域名，直接可以访问。

后续每次`git push`，Vercel会自动重新部署。改了代码，推上去，几十秒后线上就更新了。这个体验真的很爽，完全不用操心服务器、Nginx、SSL证书这些东西。

如果你有自己的域名，在Vercel后台绑定一下就行，SSL证书自动签发，全程不超过5分钟。

---

## 成本分析：到底花了多少钱？

这是很多人关心的问题。我来算一笔账：

| 项目 | 费用 |
|------|------|
| Next.js | 开源免费 |
| TailwindCSS | 开源免费 |
| Vercel部署（Hobby计划） | 免费 |
| DeepSeek API（新用户赠送额度） | 免费 |
| 域名（可选，用.vercel.app也行） | ≈ ¥50/年 |
| **总计** | **¥0 ~ ¥50** |

没看错，几乎零成本。

当然，如果网站流量起来了，DeepSeek的API调用会产生费用。但按照DeepSeek的定价，假设每天1000次请求，每次平均消耗1000个token（输入+输出），一个月的API费用大概是：

```
1000次/天 × 30天 × 1000 token × $0.42/百万token ≈ $12.6/月 ≈ ¥90/月
```

每天1000次请求，一个月API费用不到100块人民币。这个成本，随便挂个广告就能覆盖。

Vercel的免费计划每月有100GB带宽和10万次Serverless Function调用，对于初期项目绰绰有余。

![成本分析](images/article-03-chart-02.png)

---

## 变现思路

做这个项目的时候，我就在想变现的事。几个方向：

### 1. 广告收入
最直接的方式。网站有了稳定流量后，接入Google AdSense。AI工具类网站的用户停留时间长（等AI生成结果的时候会看页面），广告展示效果不错。

### 2. 付费增值
基础功能免费，高级功能收费。比如：
- 免费用户每天5次，付费用户不限次数
- 免费用户用DeepSeek-V3，付费用户可以选GPT-4o
- 付费用户支持批量处理、导出结果

### 3. API转售
把自己封装好的prompt + API包装成服务，卖给其他开发者或企业。比如"周报生成API"，按调用次数收费。

### 4. 流量变现
网站有了流量之后，可以做AI相关产品的推荐和导流。很多AI产品有推广佣金计划。

### 5. 内容引流
每个工具页面都是一个SEO入口。用户搜"AI周报生成器"、"AI文案改写"这些关键词，就可能找到你的网站。有了流量，可以引导到公众号、社群，做更深度的变现。

我个人最看好的是**2+5的组合**：用免费工具获取流量和用户，用付费增值赚钱，同时通过内容引流建立个人品牌。

---

## 一些个人感悟

做完这个项目，我有几个感触：

**1. 2024年做独立开发，门槛真的很低了。**

Next.js + Vercel让部署变成了一键操作，DeepSeek让AI能力的调用成本降到了几乎为零。以前做一个AI产品，光服务器和API费用就要几千块/月起步，现在几十块就能搞定。技术不是瓶颈，想法和执行力才是。

**2. Prompt engineering是被低估的技能。**

写代码的部分，说实话，有AI辅助编程工具帮忙，半天就搞定了。但prompt的调优，需要反复测试、对比、迭代，这个过程没法偷懒。好的prompt和差的prompt，输出质量差距巨大。这是一个值得深入研究的方向。

**3. 先做出来，再慢慢优化。**

周末做的东西肯定不完美——没有用户系统、没有数据统计、没有错误监控、UI也比较粗糙。但这些都可以后面慢慢加。最重要的是先让它跑起来，先让真实用户用起来，根据反馈再迭代。

完美主义是独立开发者最大的敌人。

**4. 同质化竞争确实是个问题。**

AI工具网站这个赛道，说实话竞争很激烈。你能想到的工具，别人大概率也想到了。但我觉得机会在于：大部分人只是想到了，没有做；做了的人里，大部分没有持续运营。**能坚持迭代和推广的人，最终会跑出来。**

而且，工具本身的差异化可能不大，但围绕工具建立的内容、社群、个人品牌，是别人抄不走的。

---

## 下一步计划

这个周末项目只是一个起点，接下来我打算：

- [ ] 加上用户系统（用NextAuth.js，支持微信登录）
- [ ] 加上使用次数限制和付费功能（用Stripe或国内支付）
- [ ] 做SEO优化，每个工具单独一个页面，写好meta标签
- [ ] 加上更多工具（AI翻译、AI摘要、AI思维导图...）
- [ ] 接入数据统计（Google Analytics或Umami）
- [ ] 写一系列教程文章，用内容带流量

如果你也想做类似的项目，我的建议是：**别想太多，这个周末就开始。**

技术栈就用我这套：Next.js + TailwindCSS + DeepSeek API + Vercel。从最简单的一个工具开始，先部署上线，再慢慢加功能。

整个过程中遇到的问题，90%都能通过搜索解决。剩下的10%，欢迎来找我交流。

---

*我是 LiFa，一个AI独立开发者。正在记录从0到1做AI产品的全过程。如果这篇文章对你有帮助，欢迎关注我的公众号「AI独立开发日记」，一起搞事情。*

*下一篇预告：我打算写写怎么用AI辅助编程工具（Cursor/Copilot）提升10倍开发效率，以及我在实际项目中的使用心得。敬请期待。*
