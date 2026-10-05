---
title: "让卡住的自动驾驶车重新动起来"
subtitle: "StuckSolver：一个外挂式的大模型模块，判断车为什么被困住并给出合法的脱困动作——或者听乘客一句话"
description: "自动驾驶车有时会被一个塑料袋逼停，然后等人来救。我们给标准的自动驾驶系统外挂了一个大模型，让车自己想办法脱困。驾驶评分 48.7 → 70.9，成功率 18% → 50%。"
lang: zh
order: 2
featured: true
venue: "研究项目 · IEEE 智能车大会（IV），2026"
tags: [大语言模型, 自动驾驶, CARLA, 人在环路]
cover_image: /assets/images/portfolio/stucksolver-cover.svg
cover_alt: "一条速度曲线：车停到零，StuckSolver 介入，车重新提速"
permalink: /zh/portfolio/stucksolver/
links:
  - label: "阅读论文"
    url: "https://ieeexplore.ieee.org/document/11623937"
    icon: "fas fa-file-lines"
---

2024 年 2 月，旧金山的一辆 Waymo 无人车在一个环岛里**连转了 17 圈**，最后由工程师远程解救。车没坏，它只是找不到一个"被允许执行"的动作。

这个项目要解决的就是这件事。

## 为什么要做

一趟行程里大部分路况自动驾驶都能应付。它应付不了的，是那种少见、但道路突然"讲不通"的瞬间：车道上飘来一个塑料袋、前方一辆车抛锚、两条车道都被路障封住。规划模块找不到合法路径，于是做了它唯一确定安全的事——停下。然后一直停着。

<figure class="figure-wide">
  <img src="/assets/images/portfolio/stucksolver-background.svg" alt="为什么卡住是个难题：车被一个小障碍逼停；现在只能呼叫远程接管或者让乘客自己开；缺的是一条从车内解决的路">
</figure>

现在只有两条出路，而且各有缺口：

- **呼叫远程操作员。** 有人在远端接管。可行，但要养一支随时待命的队伍，成本很高；而且在找到人之前，车只能干等。
- **让乘客自己开。** 前提是乘客*会开车*。这就把老人、残障人士和没有驾照的人排除在外——而正是这些人，最需要一辆会自己开的车。

缺的是第三条路：车自己脱困；或者乘客用一句话帮忙，而不是去握方向盘。

## 做了什么

**StuckSolver** 是一个接进现有自动驾驶系统的**外挂模块**。车本身的感知、规划、控制代码一行都不用改。它读车已经知道的信息，然后给出一个建议——而且只在车真的卡住时才出手。

<figure class="figure-wide">
  <img src="/assets/images/portfolio/stucksolver-system.svg" alt="StuckSolver 挂在车辆原有流程旁边：读取前视图像、周围物体、自车状态、地图和可用动作列表，分三步推理，再把行为决策交回规划模块">
</figure>

<div class="project-stats">
  <div class="project-stat"><span class="project-stat-value">3</span><span class="project-stat-label">步推理：看、判断、决定</span></div>
  <div class="project-stat"><span class="project-stat-value">0</span><span class="project-stat-label">训练，零样本直接用</span></div>
  <div class="project-stat"><span class="project-stat-value">5<small>km/h</small></span><span class="project-stat-label">低于此速度超过 1 秒才唤醒</span></div>
  <div class="project-stat"><span class="project-stat-value">220</span><span class="project-stat-label">条闭环测试路线</span></div>
</div>

推理过程就三步：

1. **看。** 从前视相机和物体列表里读出：前方有什么、多远、多快，红绿灯和标志说了什么。
2. **判断。** 车是真被困住了，还是正确地停在红灯前等行人过马路？如果是被困住，原因是什么？
3. **决定。** 从车*本来就允许*的动作里挑一个——停车、保持车道、跟车、换道——如果需要绕路，再给出重新规划的起点。

真正起作用的是两个设计选择：

- **不添乱。** 车正常行驶时它完全沉默，只有车速低于 5 km/h 持续超过一秒才被唤醒。所以在 99% 一切正常的驾驶里它不花一分算力，也永远不会和规划模块抢方向。
- **只在合法动作里挑。** 模型不自己编轨迹，而是从规划模块当前给出的可选行为里选一个。它建议什么，车本来就知道怎么安全地执行。

又因为它懂语言，乘客可以插一句话：*"那就是个塑料袋，压过去就行。"* StuckSolver 把这句话翻译成车能执行的换道动作。不需要驾照。

## 效果如何

我们在 CARLA 里用 **Bench2Drive** 测试——220 条路线，每条包含一个困难的边缘场景。两个指标最关键：**驾驶评分**（整条路开得好不好，含违规扣分）和**成功率**（多少比例的路线在限时内干净跑完）。

<figure class="figure-wide">
  <img src="/assets/images/portfolio/stucksolver-results.svg" alt="驾驶评分从 48.7 升到 65.2，有乘客提示时达到 70.9；成功率从 18.2% 升到 36.3%，有乘客提示时达到 50.0%，与最好的端到端模型持平">
</figure>

说人话：

- 把 StuckSolver 挂到一个普通的规则型驾驶智能体上，驾驶评分提升了**约三分之一**，干净跑完全程的比例**翻了一倍**。
- 在合适的地方加一句乘客提示——大约发生在 **15% 的路线**上——这套组合就追平了 **Raw2Drive**，也就是该基准上最强的端到端驾驶模型。

最后这一点是我们最在意的：一个简单、可解释的规则型系统加上一个大模型，在不重新训练任何东西的前提下，达到了重量级学习系统的水平。

<figure class="figure-wide">
  <a href="/assets/images/portfolio/stucksolver-recovery.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/stucksolver-recovery.jpg" alt="仿真中的速度曲线：车以 20 km/h 行驶，因车道上的塑料袋刹停，约 6.6 秒时 StuckSolver 介入，14 秒前后恢复到 20 km/h"></a>
</figure>

<figure class="figure-wide" style="max-width: 640px;">
  <a href="/assets/images/portfolio/stucksolver-reroute.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/stucksolver-reroute.jpg" alt="两条车道都被路障封死，StuckSolver 判断无法换道，要求重新规划路线，地图上红线是绕行路径"></a>
</figure>

<div class="finding-grid">
  <div class="finding-card">
    <div class="finding-icon">🧩</div>
    <h3>是外挂，不是重写</h3>
    <p>通过常规接口接入，不改动车辆原有模块里的任何一行代码——现有车队可以直接加装，不必重构系统。</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">💬</div>
    <h3>不用方向盘也能帮忙</h3>
    <p>不会开车的乘客，只要说出自己看到了什么，就能让车重新动起来。模型会先用交通规则检查这个建议再执行。</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🔍</div>
    <h3>它会解释自己</h3>
    <p>每个决策都附带一段大白话的理由，比神经网络吐出的一个数字好审查得多。</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🎚️</div>
    <h3>是旋钮，不是开关</h3>
    <p>调整唤醒条件，就能在"便宜的规则型系统"和"大模型几乎全程参与"之间滑动，用算力换适应性。</p>
  </div>
</div>

## 它还解决不了什么

- **慢。** 平均每次推理约 2.8 秒。对一辆本来就停着的车没问题，但应付不了任何时间敏感的场景。下一步是蒸馏一个更快的模型。
- **还在仿真里。** 本文全部结果来自 CARLA。真实道路、真实传感器、真实时延都还在前面。
- **乘客提示只是一次演示，不是成品。** 我们只在明显有帮助的地方给了简单指令。*什么时候*该求助于人、人的话该信几分，本身就是另一个研究课题。

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">难的从来不是开车，而是知道一个塑料袋只是一个塑料袋。</p>

<p class="project-credit">与佐治亚大学 Qianwen Li 合作完成。论文发表于 <em>IEEE 智能车大会（IV 2026）</em>，2026 年 6 月，美国底特律。<br><a href="https://ieeexplore.ieee.org/document/11623937" target="_blank" rel="noopener">在 IEEE Xplore 阅读论文</a></p>
