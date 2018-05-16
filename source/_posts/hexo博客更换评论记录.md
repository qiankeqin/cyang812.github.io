title: hexo博客更新主题记录
date: 2017-05-29 22:59:19
tags:
- 多说
- Disqus
categories:
- 总结
---

# 1、更换评论
多说将于6月1号关闭，因此将评论系统从多说转到Disqus。更新的方法很简单，升级next主题，最新版的主题中自带了Disqus的评论，只需填入用户名即可。

<!-- more -->

# 2、添加运行时间
- 1、添加运行时间，如本站已运行 402 天 7 小时 23 分 11 秒。
添加方法：在`themes\next\layout\_partials`目录的`footer.swig`文件中添加如下代码：
```java
<!-- time -->
  <div id="show-time">
    <span id="span_dt_dt"></span>
  </div>
  <script>
      function show_date_time() {
        window.setTimeout("show_date_time()", 1000);
        BirthDay = new Date("4/21/2016 15:30:01");
        today = new Date();
        //总时间
        timeold = (today.getTime() - BirthDay.getTime());
        sectimeold = timeold / 1000
        secondsold = Math.floor(sectimeold);
        msPerDay = 24 * 60 * 60 * 1000
        e_daysold = timeold / msPerDay
        daysold = Math.floor(e_daysold);
        e_hrsold = (e_daysold - daysold) * 24;
        hrsold = Math.floor(e_hrsold);
        e_minsold = (e_hrsold - hrsold) * 60;
        minsold = Math.floor((e_hrsold - hrsold) * 60);
        seconds = Math.floor((e_minsold - minsold) * 60);
        span_dt_dt.innerHTML = "本站已运行 " + daysold + " 天 " + hrsold + " 小时 " + minsold + " 分 " + seconds + " 秒";
    }
show_date_time();
  <
  /script>
```