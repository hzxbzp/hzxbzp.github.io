---
title: "先指出来，再做决定"
subtitle: "一个数据集和训练方法，让驾驶模型说清楚路上哪几样东西真正决定了下一步，并指出它们在画面里的位置"
description: "驾驶模型能把路口描述得很流畅，却说不出这段描述里哪三样东西真正决定了它接下来怎么开。这个项目造的就是「先指出证据，再给出决定」那种训练数据，并在八个开源模型上都验证有效。"
lang: zh
order: 0.5
featured: true
venue: "研究项目 · 审稿中，2026"
cover_image: /assets/images/portfolio/grounded-cover.jpg
cover_alt: "数据集中的一帧：一盏信号灯和三位骑行者被标为决定车辆下一步动作的要素"
tags: [视觉语言模型, 自动驾驶, 数据集, 视觉定位]
permalink: /zh/portfolio/grounded-driving/
---

拿一张车辆前视画面去问视觉语言模型看到了什么，你会得到一段很流畅的描述。再追问一句：这段描述里，哪三样东西真正决定了车接下来怎么开，它们具体在画面的什么位置？回答就撑不住了。

这个项目要补的就是这一环：让模型先把证据指出来，再开口解释。

<figure class="figure-wide">
  <video class="project-video" controls muted loop playsinline preload="metadata" poster="/assets/images/portfolio/grounded-cover.jpg">
    <source src="/assets/video/scene-cyclists.mp4" type="video/mp4">
  </video>
</figure>

## 训练数据缺的那一环

给这类模型用的驾驶数据集大致有三种，每一种都漏掉了一点东西。场景描述把整幅画面讲清楚，却从不说哪个物体起了决定作用。问答式数据教模型一次回答一个问题，从"看到什么"到"所以怎么开"之间没有一条连贯的线。写成文字的推理链读着通顺，但和画面是脱钩的——模型可以煞有介事地提到一个根本不存在的行人，而数据里没有任何东西能把它拦下来。

缺的是中间那一环：**是这个**物体、在**这些像素**上，所以车要减速。

## 数据集里有什么

素材来自 Waymo 的端到端驾驶数据集，它专门收集那些别扭的情况——很少见，但一旦出现就决定这趟开得好不好。我们在 2,492 个片段、约十一小时的驾驶数据上标注了 **416,119 帧**和 **395,379 个决定性要素**，分成四大类：其他车辆、弱势道路使用者、障碍物、交通控制设施，下面还有十九个更细的类型。

每一帧给的不是一个标签，而是一条链。先是环境：天气、能见度、道路形态。然后是最多五个真正起作用的要素，每个都有一个掩码精确标出它在哪、一个 1 到 5 的影响力排序、以及它的状态和看上去的意图。接着，对每个要素给一句话，说明它如何改变了车的可选动作。再然后是一段把它们串起来的简短理由。最后才是决定：一个高层动作，加上未来五秒应该走的轨迹。

标注是人和机器轮流做的。有经验的驾驶者挑出并排序真正要紧的要素，在画面上点一下；分割模型把这些点变成掩码；视觉语言模型补上物体类型、读出标志上的字；语言模型起草每个要素的影响说明和整帧的理由；最后由规则检查核对"说出来的动作"和"画出来的轨迹"是否一致。场景环境标注有一半经过人工复核，要素的选择由标注者交叉检查、分歧时第三人投票裁定，自动环节也和人工标签做了抽样比对——交通灯状态一致率 97.8%，车辆类型 99.0%。

## 数据集里的更多场景

<div class="video-grid">
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-red-light.jpg"><source src="/assets/video/scene-red-light.mp4" type="video/mp4"></video>
    <figcaption>消防车从消防站驶出</figcaption>
  </figure>
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-rain.jpg"><source src="/assets/video/scene-rain.mp4" type="video/mp4"></video>
    <figcaption>雨后傍晚的居民区街道</figcaption>
  </figure>
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-school-bus.jpg"><source src="/assets/video/scene-school-bus.mp4" type="video/mp4"></video>
    <figcaption>经过一辆停靠的校车</figcaption>
  </figure>
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-pull-over.jpg"><source src="/assets/video/scene-pull-over.mp4" type="video/mp4"></video>
    <figcaption>在陡坡上超越一辆公交车</figcaption>
  </figure>
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-animal.jpg"><source src="/assets/video/scene-animal.mp4" type="video/mp4"></video>
    <figcaption>驶过陡坡的坡顶</figcaption>
  </figure>
</div>

## 分两步学

把整条链一次性喂给模型效果并不好——难的部分会把简单的部分淹掉。所以要按顺序学。

第一步只要求它"看见"：读出环境、判断哪些要素重要、指出它们在哪、给出影响力排序。链条下游的内容全部不计入损失，视觉编码器可以自由调整。

第二步才学推理和规划——影响说明、理由、动作、轨迹——而这一次视觉编码器被冻住，这样它刚学会的"指认"能力不会在练习写字的过程中悄悄退化。那些行为发生转折的帧（比如从加速变成刹车）会被更多地采样，否则模型几乎见不到足够多的例子。

怎么评价"指得准不准"需要讲究：五十米外的一盏信号灯和占了半幅画面的一辆公交车，"差不多对"的含义完全不同。这里用的度量会按物体自身的尺寸缩放容差——预测点落在物体掩码里算命中，或者落在一个随物体大小增长的距离之内也算。

## 结果

我们用这套方法微调了八个开源视觉语言模型，从通用模型到专为驾驶打造的模型都有。方向是一致的。

提升最大的是"找出要紧的要素"：**召回率从 0.10–0.49 升到 0.62–0.79**，而且被找出的要素类型判断正确率达到 **90–98%**。由模型评分的解释质量平均提高约 **0.36**（满分 1），驾驶理由提高约 **0.31**。

规划也跟着变好。平均而言，五秒轨迹误差下降了约 **7.8 米**，终点误差下降了约 **11.9 米**——在好几个底座模型上，这是"方向大致没错"和"真的能用"之间的差别。

还有一个我们没料到的结果：输出变**短**了，大约少 18.5 个 token，推理也变**快**了，每帧快约 0.32 秒。教会模型该看哪里，似乎也让它不再废话。

## 边界在哪

大部分标注文字是语言模型起草、人工复核的，而不是人从零写出来的——正是这一点让这个规模成为可能，也正是关于它最该说清楚的一条。定位度量是新提出的，所以它给出的数字没法和此前发表的结果直接比较。而且这里全部是在录制数据上的离线评测：定位更准、轨迹误差更低，和"车开得更好"不是一回事，要跨过这一步，需要把车放进回路里。

<p class="project-credit">论文审稿中；项目名称、代码与数据链接在审稿期结束后再公开。</p>
