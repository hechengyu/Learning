```shell
npm install hexo --save -g
npm install hexo-deployer-git --save -g
```





```powershell
mkdir demo
npx hexo init demo

cd demo/themes	
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia

cd ../
npm install hexo-deployer-git --save

npx hexo clean
npx hexo g
npx hexo s
npx hexo d
```

修改`_config.yaml`

```yaml
title: 每天学点系列
subtitle: ''
description: ''
keywords:
author: HeChengYu
language: en
timezone: ''

# 设置站点URL,例如: 'https://username.github.io/project'
url: https://hechengyu.github.io/Learning/

# 修改主题
theme: yilia

# 修改部署地址
deploy:
  type: git
  repository: git@github.com:hechengyu/Learning.git
  branch: dev
```



