+++
title = 'Hello World'
date = 2024-04-03T15:36:21+08:00
draft = false
tags = ["测试", "Markdown"]
series = ["测试用内容"]
series_order = 1
+++

## 你好，世界！

>这是本站的第一篇推文

本站使用基于Go的Hugo引擎构建于Github Pages

本文的剩余部分用于测试 **本主题(Blowfish)** 独有的各种语法功能

{{< alert "codepen">}}
测试警告语句
{{< /alert >}}


{{< typeit 
  tag=h3
  speed=50
  breakLines=false
  loop=true
>}}
这是第一篇推文 
现在是测试Typeit动态效果的部分
{{< /typeit >}}


测试Timeline语句

{{< timeline >}}

{{< timelineItem icon="github" header="header" badge="Repository" subheader="Github建立仓库" >}}
2024.04.02 于Github建立网站代码仓库
{{< /timelineItem >}}


{{< timelineItem icon="code" header="Another Awesome Header" badge="code" subheader="代码编写" >}}
2024.04.03选取构建代码引擎
<ul>
  <li>Jekyll-Hydejeck</li>
  <li>Hugo-Blowfish</li>
</ul>
{{< /timelineItem >}}

{{< timelineItem icon="star" header="Shortcodes" badge="AWESOME" >}}
2024.04.03上传第一篇推文
{{< /timelineItem >}}

{{< /timeline >}}


测试嵌入Youtube视频

{{< youtubeLite id="SgXhGb-7QbU" label="Blowfish-tools demo" >}}

