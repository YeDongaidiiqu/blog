---
title: mac开发环境配置
date: 2021-05-23 13:07:51
tags: Mac
---

#### brew包管理安装

```
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

#### git ssh配置

```
1. 配置git用户名和邮箱
git config --global user.name "xxx"
git config --global user.email "xxx@xxx.com"
2. 生成ssh私钥和公钥
ssh-keygen -t rsa -C "xxx@xxx.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
3. 将公钥加入到git的ssh私钥管理中
```

