---
title: 记Hexo博客valine评论和阅读次数不兼容问题
date: 2019-04-24 20:48:48
tags: [博客,问题]
categories: 其他问题
---

&#160; &#160; &#160; &#160;之前在leancloud上注册了一个账号，用于集成博客的评论系统，配置完之后发现评论系统生效了，而记录阅读次数却不生效了，解决之后如下,评论和阅读次数同时生效。

![](aboutVisitorCount\p1.png)

#### LeanCloud评论和阅读次数冲突

&#160; &#160; &#160; &#160;valine的评论功能和阅读次数功能都是基于LeanCloud的，这一点相信配置过的人应该发现它们使用的相同LeanCloud的**id**和**key**，在**themes\next\_config.yml**配置文件中如下:

```
# Show number of visitors to each article.`

`\# You can visit https://leancloud.cn get AppID and AppKey.`

`leancloud_visitors:`

  `enable: false`

  `app_id: # your appid`

  `app_key: #your app_key`

`\# Valine.`

`\# You can get your appid and appkey from https://leancloud.cn`

`\# more info please open https://valine.js.org`

`valine:`

  `enable: true`

  `appid:  ****`

  `appkey:  ****`

  `notify: false # mail notifier , https://github.com/xCss/Valine/wiki`

  `verify: false # Verification code`

  `placeholder: Just go go # comment box placeholder`

  `avatar: mm # gravatar style`

  `guest_info: nick,mail,link # custom comment header`

  `pageSize: 10 # pagination size
```

&#160; &#160; &#160; &#160;之前由于配置了valine的enable为true，填上appid和appkey，评论系统生效，阅读次数失效。

#### 冲突解决

&#160; &#160; &#160; &#160;**<u>解决办法为在valine后面添加visitor和comment_count属性，并且开启leancloud_visitors,加上appid和appkey，**具体如下

```
# Show number of visitors to each article`

`\# You can visit https://leancloud.cn get AppID and AppKey.`

`leancloud_visitors:`

  `enable: true`

  `app_id: ****`

  `app_key: ****`



\`# Valine.`

`\# You can get your appid and appkey from https://leancloud.cn`

`\# more info please open https://valine.js.org`

`valine:`

  `enable: true`

  `appid:  ****`

  `appkey:  ****`

  `notify: false # mail notifier , https://github.com/xCss/Valine/wiki`

  `verify: false # Verification code`

  `placeholder: Just go go # comment box placeholder`

  `avatar: mm # gravatar style`

  `guest_info: nick,mail,link # custom comment header`

  `pageSize: 10 # pagination size`

  `visitor: true # leancloud-counter-security is not supported for now. When visitor is set to be true, appid and appkey are recommended to be the same as leancloud_visitors' for counter compatibility. Article reading statistic https://valine.js.org/visitor.html`

  `comment_count: true # if false, comment count will only be displayed in post page, not in home page
```

**&#160; &#160; &#160; &#160;借鉴**:https://blog.csdn.net/Aoman_Hao/article/details/87809762