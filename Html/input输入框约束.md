---
title: input输入框约束 
tags: html
categories: html
date: 2019-04-14
---

<div align='center' ><font size='70'>input输入框约束</font></div>

* 输入框限制只能输入数字：` oninput = "value=value.replace(/[^\d]/g,'')"`
* 禁止输入空格（输入空格回退）： `onkeyup="this.value=this.value.replace(/(^\s+)|(\s+$)/g,'');"`
* 限制长度：`maxlength="5"`
* 只能输入汉字：`onkeyup="this.value=this.value.replace(/[^\u4e00-\u9fa5]/g,'')"`
* 禁止输入汉字：`onkeyup="value=value.replace(/[\u4e00-\u9fa5]/ig,'')"`