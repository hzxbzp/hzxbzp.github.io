---
title: "你的车，听你的"
subtitle: "PACE-ADS：三个 AI 智能体，让自动驾驶车既读路况，也读车里那个人，并据此调整怎么开"
description: "现在的自动驾驶只对交通做反应，把乘客当成货物。PACE-ADS 加了三个智能体——一个看路，一个看人，一个做决定——同一个路口可以开得利落，也可以开得温和；车被困住时，一句话就能解围。"
lang: zh
order: 0
featured: true
venue: "研究项目 · Journal of Intelligent and Connected Vehicles，2026"
tags: [大模型智能体, 以人为中心的自动驾驶, 自动驾驶, CARLA]
cover_image: /assets/images/portfolio/pace-ads-cover.svg
cover_alt: "同一个红灯下的两条速度曲线：赶时间的乘客对应晚刹车、快起步；紧张的乘客对应早减速、缓起步"
permalink: /zh/portfolio/pace-ads/
links:
  - label: "阅读预印本"
    url: "https://arxiv.org/abs/2506.11842"
    icon: "fas fa-file-lines"
---

两个人在不同的日子坐进同一辆无人车。一个赶飞机，一个第一次坐、心里发慌。车在红灯前的刹车方式对两人一模一样——于是两次都没对：对前者太磨蹭，对后者太猛。

车根本不知道坐在里面的是谁。

## 为什么要做

今天的自动驾驶系统是为"看路"设计的。它所有的感知都朝外：红绿灯、别的车、行人。后座上那个人，对任何一个决策都没有贡献。

<figure class="figure-wide">
  <img src="/assets/images/portfolio/pace-ads-background.svg" alt="现在的车只对交通做反应，不读车里的人，所以每位乘客得到的都是同一种开法；车被困住时乘客也无从帮忙。缺的是一辆能感知乘客、也能听乘客说话的车">
</figure>

代价有两个。**这趟车谁都不合身**——一种开法，发给所有人。而当车犯迷糊停在路上，**乘客明明看得出前面只是个纸袋，却没有办法告诉它**。

两件事根源相同：从人到驾驶，中间没有一条通路。

## 做了什么

**PACE-ADS** 把这条通路接通了。三个大模型智能体挂在车辆原有软件旁边——原有部分一行不改——它们合起来把"车里是谁、他现在怎么样"变成一个真正的驾驶决策。

<figure class="figure-wide">
  <img src="/assets/images/portfolio/pace-ads-system.svg" alt="三个智能体挂在车辆原有软件旁边：驾驶员智能体读路况，心理学家智能体读乘客的生理信号和话语，协调者把两者变成交回规划模块的驾驶行为">
</figure>

<div class="project-stats">
  <div class="project-stat"><span class="project-stat-value">3</span><span class="project-stat-label">个智能体：看路、看人、做决定</span></div>
  <div class="project-stat"><span class="project-stat-value">0</span><span class="project-stat-label">训练，全部零样本运行</span></div>
  <div class="project-stat"><span class="project-stat-value">4</span><span class="project-stat-label">个日常场景测试</span></div>
  <div class="project-stat"><span class="project-stat-value">30</span><span class="project-stat-label">次重复运行支撑每个数字</span></div>
</div>

- **驾驶员智能体读路况。** 不只是"23 米处有个物体"，而是这是个什么场景：天气、施工区、信号灯状态、周围有谁、他们看上去要做什么。
- **心理学家智能体读乘客。** 它接收原始信号——面部表情、心率、脑电——加上乘客说出口的话，判断这个人是平静、焦虑还是着急，以及他到底想要什么。它前面没有另接一个情绪分类器，推断是智能体自己从原始信号里完成的。
- **协调者做决定。** 安全第一，其次才是乘客偏好。它从车本来就会的动作里挑一个——跟车、保持车道、换道、停车——再决定做得多干脆。

有三个设计让这件事站得住：

- **个性化是有边界的。** 协调者调的每一个参数都落在一个与交通法规挂钩的固定安全区间内。着急的乘客可以换来更利落的开法，但换不来比规则更短的间距。
- **它不添乱。** 它工作在慢速的高层决策层，只在乘客状态变化、乘客说话、或者车被困住时才醒来。实时控制始终留在车辆原有软件里。
- **拿不准就问。** 与其瞎猜，协调者会说明是什么卡住了车，然后把问题交给乘客。

## 开法真的变了吗

我们在 CARLA 里跑了四个日常场景——红灯、行人过街、需要变道绕开的施工区、普通跟车——在五种乘客状态下各重复 30 次。

<figure class="figure-wide">
  <img src="/assets/images/portfolio/pace-ads-results-personalization.svg" alt="随着乘客从着急变为焦虑，车的巡航速度下降、与行人的停车距离增大、变道所需间隙变大；与行人的距离始终不低于 1.5 米安全下限">
</figure>

规律贯穿所有场景。着急的乘客得到的是接近限速巡航、晚而干脆地刹车、起步迅速；焦虑的乘客得到的是速度减半、更早开始减速、在行人周围留出大得多的余量。安全底线也守住了：即使在最利落的状态下，车与行人的距离也从未低于规定的 1.5 米。

最直观的是施工区——车必须向左并线绕过封闭车道：

<figure class="figure-wide" style="max-width: 720px;">
  <a href="/assets/images/portfolio/pace-ads-gap.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/pace-ads-gap.jpg" alt="同一个施工区的两张俯视图：乘客着急时，车挤进 1.6 米的间隙；乘客焦虑时，车等到 20 米以上的空档才并线"></a>
  <figcaption>同一个施工区，同一套规则，两位乘客。左边，车用掉了第一个合法可用的间隙；右边，它让整列车先过。点击放大。</figcaption>
</figure>

## 车被困住的时候

系统的另一半是脱困。我们构造了三个常规系统讲不通道理的处境：车道上的纸袋、被路障完全封死的道路，以及一个让车不停绕环岛的路径规划——正是旧金山那辆 Waymo 绕了 17 圈的那种失败。

<figure class="figure-wide">
  <img src="/assets/images/portfolio/pace-ads-results-recovery.svg" alt="基线智能体在 90 次运行中一次都没能脱困；StuckSolver 成功率 83.3%，PACE-ADS 达到 91.1%，提升集中在环岛和道路封闭场景，静态障碍物场景的下降来自拒绝执行">
</figure>

基线一次都没出来——90 次运行，零。PACE-ADS 在其中 91% 的运行里脱困，而提升主要来自那两个需要真正"理解处境"而不只是"检测物体"的场景。

<figure class="figure-wide">
  <a href="/assets/images/portfolio/pace-ads-roundabout.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/pace-ads-roundabout.jpg" alt="车在环岛里反复绕圈，直到乘客说停车并从右侧出口离开，协调者把这句话拆成停车、换道、巡航，重新规划路线后驶出环岛"></a>
  <figcaption>环岛。车一圈圈绕着，直到乘客开口："停车！从右边的出口出去。"协调者把这句话拆成 停车 → 换道 → 巡航，选好新的路径点，车就出来了。点击放大。</figcaption>
</figure>

有一个结果是反方向的，值得留意。在纸袋场景里，PACE-ADS 的成绩**低于**我们之前的系统，因为它有时会在把乘客的指令与驾驶员智能体看到的画面核对之后，拒绝"压过去"这个指令。那是拒绝，不是失败——但同一个机制在挡住危险指令的同时，偶尔也会挡住一个本来安全的指令。

## 它读得懂陌生人吗

前面所有驾驶测试用的都是同一个人的信号。为了看它能不能迁移，我们把心理学家智能体放到数据集全部 43 名被试上，不做任何针对个人的调整：识别乘客状态的**准确率是 64%**。

远谈不上完美——但误差的形状比这个数字更重要。将近一半的错误发生在同一种情绪的相邻强度之间（把"非常焦虑"读成"焦虑"），而把着急读反成焦虑的情况不到 5%。也就是说，读错通常只会让调整的**幅度变小**，而不是方向反了。完全去掉脑电信号只损失 3.7 个百分点——这对一套最终要装进真车的系统很有意义。

## 它还解决不了什么

- **全部是仿真。** CARLA 给的是干净的感知和理想的执行，真车两样都没有。
- **乘客的情绪是"注入"的。** 我们按脚本注入情绪信号，而不是测量一个真人对车的反应。所以这些实验证明的是系统对给定状态序列的响应是连贯的，而不是真实乘客坐得更舒服了。
- **五种状态是工具，不是真相。** 情绪是连续的，"非常焦虑"和"焦虑"只是我们为了能衡量、能讨论而选的标签。
- **安全核查是推理，不是保证。** 协调者会把乘客指令和画面对照着权衡，但那是判断，不是证明。乘客的话是系统纳入考量的信息，不是必须服从的命令。
- **每次决策 2.2 秒。** 在慢速决策层够用，但目前依赖远程 API。把它搬上车，是下一步的工作。

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">一辆分不清乘客是从容还是害怕的车，算不上真正自主——它只是孤身一人。</p>

<p class="project-credit">与佐治亚大学 Wenjie Zhao、Qianwen Li 合作完成。论文已被 <em>Journal of Intelligent and Connected Vehicles</em>（2026）接收，期刊版本尚未上线。<br><a href="https://arxiv.org/abs/2506.11842" target="_blank" rel="noopener">在 arXiv 阅读预印本</a></p>
