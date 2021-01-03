---
title: ik_analyze
tags: elasticsearch
categories: elasticsearch
date: 2020-11-28
---



ik分词器可增加自己的词语

插件`ik—>config`下增加自己的字典文件，例如`fuzui.dic`,然后在`IKAnalyzer.cfg.xml`文件夹中设置

```xml
<entry key="ext_dict">fuzui.dic</entry>
```



