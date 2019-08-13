---
title: Hexo博客迁移
date: 2019-08-13 19:47:11
updated: 2019-06-07 15:47:11
categories:
- Hexo
---

    背景：
    将Hexo环境源码上传至github后，在新电脑（什么环境都未安装）上如何将Hexo环境复刻下来，重新开始写博客
    上传

## 一、组件安装
1、git

```
yum install -y git
```
2、nodejs

```
yum install -y nodejs
```
3、npm

```
yum install -y npm
```
4、hexo-cli

```
yum install -y hexo-cli
```
## 二、环境复刻
1、将github上hexo源码分支clone至本地（我的分支名为hexo）

```
git clone -b hexo https://github.com/Liang2L/liang2l.github.io.git
```
2、依赖安装

```
npm install
```
==备注==：在npm安装依赖时，可能会报错

```
npm: relocation error: npm: symbol SSL_set_cert_cb, version libssl.so.10 not defined in file libssl.so.10 with link time reference
```
此时将openssl更新即可

```
yum update -y openssl
```
3、安装git推送环境

```
git config --global user.name "Liang2L"
git config --global user.email "469449505@qq.com"

//检查输入是否正确
git config user.name
git config user.email

//创建SSH
ssh-keygen -t rsa -C "469449505@qq.com"

//获取公钥
cat /root/.ssh/id_rsa.pub
```
4、在github上settings中创建SSH，将获取的公钥复制进去即可

```
//查看SSH是否创建成功
ssh -T git@github.com
```
## 三、新环境上更新博客
1、将环境源码推送至hexo分支上

```
git add .
git commit -m "hexo迁移测试"
git push origin hexo
```
2、将生成的html静态文件推送至master分支上

```shell
hexo g
hexo d
```