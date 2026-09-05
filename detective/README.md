# 内科侦探所 · 呼吸病学系列

定格动画式临床教学游戏。受众：住院规培医师 / 实习医师。**仅用于个人临床教学，非商业化用途。**

- 在线入口：`https://niuontheway-ops.github.io/resident-doctor/detective/`
- 内容依据：《内科学》（第10版）、《诊断学》（第10版）
- 听诊音：B站《肺部听诊合集（14分钟）》真实录音逐段提取（13 段）+ 壶兰呼吸 heart_normal
  - **每段裁为 10 秒教学片段后 base64 内联进 `index.html`**，零外链、零路径依赖
  - 因此预览面板、双击 `file://` 打开、离线包、GitHub Pages **全环境都能出声**，不再依赖 `audio/` 目录
  - 原始完整录音仍保留在 `audio/`，仅作素材备份，运行时不加载
- 影像：真实临床教学影像（本仓库 `img/` + PMC 开放获取文献，来源与许可见游戏内报告详情页标注）
- 规模：15 案 × 4 份候选报告 = 60 份，其中 49 份为真实影像，11 份为手绘 SVG 检验单（血常规／痰涂片／血气／肺功能／肿瘤标志物／D-二聚体／FTND 量表）

## 已归档案件（15 案）
1. 第一案《铁锈色的证词》—— 肺炎链球菌肺炎
2. 第二案《越来越短的呼吸》—— 慢性阻塞性肺疾病急性加重期
3. 第三案《夜半的哨音》—— 支气管哮喘重度急性发作
4. 第四案《分层的痰》—— 支气管扩张症（体位引流＋咯血防窒息）
5. 第五案《低调的潜伏者》—— 继发性肺结核（羊鸣音＋2HRZE/4HR）
6. 第六案《会诊单上的阴影》—— 原发性支气管肺癌·中央型（固定哮鸣音＋支气管镜活检）
7. 第七案《泄了气的篮球》—— 自发性气胸·张力性（对比听诊＋第2肋间穿刺减压）
8. 第八案《肺边的深潭》—— 结核性渗出性胸膜炎（压迫性肺不张＋限速抽液）
9. 第九案《腿上来的刺客》—— 肺血栓栓塞症（听诊全正常的教学反转＋抗凝）
10. 第十案《织密的网》—— 特发性肺纤维化（Velcro 音＋抗纤维化治疗）
11. 第十一案《感冒之后》—— 急性气管-支气管炎（自限性疾病，**不用抗生素**）
12. 第十二案《溃败的肺》—— 肺脓肿·吸入性（厌氧菌混合感染＋8~12 周长疗程）
13. 第十三案《肺坏了，心也跟着累》—— 慢性肺源性心脏病（高浓度吸氧致 CO₂ 麻醉／强心利尿过度两大陷阱）
14. 第十四案《白肺》—— 呼吸衰竭与 ARDS（顽固性低氧＋小潮气量 6 ml/kg 肺保护通气）
15. 第十五案《戒不掉的那一根》—— 烟草依赖（四处听诊全正常，诊断靠病史与 FTND 量表；5A 干预＋一线药物）

呼吸系统疾病章节已全量归档，无待办病种。

## 听诊音设计特色
- 两次点击交互：第一次只报部位，第二次才揭示声音身份
- `silent.mp3`（4 秒静音文件）：用于「呼吸音消失」——气胸/大量积液的教学核心（「安静」本身就是证据）
- 第九案四处听诊全部正常：训练学员识别「喘重而肺里安静」的血管性病变
- 每个听诊点含 `heard/finding/teach` 三层：《诊断学》第10版知识点随听随学

## 如何续写一个新案（复用说明）
打开 `index.html`，在 `CASES` 数组中追加一个对象即可，结构：
- `no/name/disease/patient`：案名、病种、病人信息
- `chief`：主诉+现病史（病人开口时自动入案卷）
- `lines.f1/f1b/f2ok/f3end`：四格对话
- `hotspots[]`：听诊点（`pos` 引用共享坐标 POS 0-3，`sound` 用 audio/ 真实录音文件名）
- `reports[]`：四选一报告（`img` 指向本仓库真实影像，标注来源；非影像必须给 `svg`，否则该卡片会渲染成空白）
  - 现有 `svg` 模板：`wbc_low` `wbc_high` `wbc_bact` `afb_pos` `abg_normal` `pft_early` `pft_obstruct` `tumor_marker` `ddimer_neg` `ftnd_low`（在 `reportSVG()` 中扩展）
- `quiz[]`：诊断+治疗选择题（`fb` 内写教科书依据）
- `endings.perfect/reportOnly/dead` + `review[]`：结局与教学复盘
最后在 `LOCKED` 数组中移除对应病种名（`LOCKED` 为空时案卷柜会自动隐藏「待归档」标题）。

### 新增案件后的自检
用 Node 跑一遍数据校验（案件结构、音频/图片是否存在、svg 模板是否定义、报告是否漏配图）：
```
node _verify.js   # 脚本见 skill detective-case-authoring
```
重点：`reports` 必须恰好 1 个 `ok:true`；`hotspots` 必须 4 个且 `pos` 不重复（引用共享坐标 `POS` 0-3）；`quiz` 每题 4 选项且恰好 1 个 `ok:true`。

新增音频时用 ffmpeg 提取：
```
ffmpeg -vn -i P0X.mp4 -ac 1 -ar 22050 -b:a 64k 音频名.mp3
```
静音文件（呼吸音消失）：`ffmpeg -f lavfi -i anullsrc=r=22050:cl=mono -t 4 -b:a 64k silent.mp3`

## 本地/跨平台使用
`index.html` 自带全部听诊音（base64 内联），影像走同目录 `img/`。拷贝到任意静态服务器、或直接双击 index.html 即可运行，可离线使用。

## 音频相关：踩过的坑（改代码前必读）
1. **音频池被"先占位"污染**：`startAuscultation()` 预加载与 `playAusc()` 播放**必须都用 `auscSrc(file)`**。若预加载用了 `AUDIO_BASE+file`，它会先用外部路径把 `auscPool[file]` 占住，`playAusc` 因"池内已有"而复用外部对象，内联数据形同虚设——表现是"内联加了却仍在请求外部 mp3"。
2. **选择器 `#hotspots`**：听诊点容器 id 是 `hotspots`，不是 `hs-layer`（旧版遗留名，已不存在）。
3. **headless 测流程会假死**：无头浏览器把后台 `setInterval` 节流到约 1 次/秒，打字机 22ms/字会被拖成 1 秒/字，看起来像"对话卡住"。实测需加 `--disable-background-timer-throttling --disable-renderer-backgrounding`，或直接点击气泡跳过打字。

### 更换/新增听诊音
```
ffmpeg -hide_banner -y -ss <起点秒> -t 10 -i 源.mp3 \
  -vn -ac 1 -ar 22050 -b:a 32k -map_metadata -1 -write_id3v2 0 输出.mp3
```
裁完用 `ffmpeg -i 输出.mp3 -af volumedetect -f null -` 确认 `mean_volume` 不低于 -40 dB（避免裁到静音段），
再用 `_inline_audio.py` 重新生成内联数据。静音文件（呼吸音消失）：
`ffmpeg -f lavfi -i anullsrc=r=22050:cl=mono -t 3 -ac 1 -ar 22050 -b:a 32k -write_id3v2 0 silent.mp3`
