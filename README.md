# 前言

[![](https://img.shields.io/github/stars/yeasy/docker\_practice.svg?style=social\&label=Stars)](https://github.com/yeasy/docker\_practice) [![](https://img.shields.io/github/release/yeasy/docker\_practice/all.svg)](https://github.com/yeasy/docker\_practice/releases) [![](https://img.shields.io/badge/Based-Docker%20CE%20v20.10-blue.svg)](https://github.com/docker/docker-ce) [![](https://img.shields.io/badge/Docker%20%E6%8A%80%E6%9C%AF%E5%85%A5%E9%97%A8%E4%B8%8E%E5%AE%9E%E6%88%98-jd.com-red.svg)](https://union-click.jd.com/jdc?e=\&p=JF8AANADIgZlGF0VAxUDVBJdHDISBFAfWRcCGzcRRANLXSJeEF4aVwkMGQ1eD0kdSVJKSQVJHBIEUB9ZFwIbGAxeB0gyS34PbFlHVHNkI0MQEAoIcSxyBWFLRAtZK1olABYHXR9eHAoQN2UbXCVQfN\_jrYOwsw7T\_5SOnZUiBmUbXBYBFwBVG14UBBAAZRxbHDJJUjscCEEHEQ4FSA4VBhBQZStrFjIiN1UrWCVAfARQT1gQA0cFAEwOEAcRDlMTDEALQAFTEwwRUhMAUR1cJQATBlES)

**v1.3.0**

| 语言                                                | -                                                  |
| ------------------------------------------------- | -------------------------------------------------- |
| [简体中文](https://github.com/yeasy/docker\_practice) | [阅读](https://vuepress.mirror.docker-practice.com/) |

[Docker](https://www.docker.com) 是个划时代的开源项目，它彻底释放了计算虚拟化的威力，极大提高了应用的维护效率，降低了云计算应用开发的成本！使用 Docker，可以让应用的部署、测试和分发都变得前所未有的高效和轻松！

无论是应用开发者、运维人员、还是其他信息技术从业人员，都有必要认识和掌握 Docker，节约有限的生命。

本书既适用于具备基础 Linux 知识的 Docker 初学者，也希望可供理解原理和实现的高级用户参考。同时，书中给出的实践案例，可供在进行实际部署时借鉴。前六章为基础内容，供用户理解 Docker 的基本概念和操作；7 \~ 9 章介绍包括数据管理、网络等高级操作；第 10 \~ 12 章介绍了容器生态中的几个核心项目；13、14 章讨论了关于 Docker 安全和实现技术等高级话题。后续章节则分别介绍包括 Etcd、Fedora CoreOS、Kubernetes、容器云等相关热门开源项目。最后，还展示了使用容器技术的典型的应用场景和实践案例。

* 在线阅读：[docker-practice.com](https://vuepress.mirror.docker-practice.com/)，[GitBook](https://yeasy.gitbook.io/docker\_practice/)，[Github](https://github.com/yeasy/docker\_practice/blob/master/SUMMARY.md)
* 离线阅读：[`$ docker run -it --rm -p 4000:80 ccr.ccs.tencentyun.com/dockerpracticesig/docker_practice:vuepress`](https://github.com/yeasy/docker\_practice/wiki/%E7%A6%BB%E7%BA%BF%E9%98%85%E8%AF%BB%E5%8A%9F%E8%83%BD%E8%AF%A6%E8%A7%A3)

Docker 自身仍在快速发展中，生态环境也在蓬勃成长。建议初学者使用最新稳定版本的 Docker 进行学习实践。欢迎 [参与项目维护](contributing.md)。

* [修订记录](changelog.md)
* [贡献者名单](https://github.com/yeasy/docker\_practice/graphs/contributors)

## 微信小程序

<div align="center">

<img src="https://docker_practice.gitee.io/pic/dp-wechat-miniprogram.jpg" alt="" width="200">

</div>

**微信扫码 随时随地阅读\~**

## 技术交流

<div align="center">

<img src="https://docker_practice.gitee.io/pic/dpsig-wechat.jpg" alt="" width="200">

</div>

**微信扫码 加入群聊\~ 或者微信添加 `dpsigs` 邀请入群**

欢迎加入 Docker 技术交流 QQ 群，分享 Docker 资源，交流 Docker 技术。

* QQ 群 I （已满）：341410255
* QQ 群 II （已满）：419042067
* QQ 群 III （已满）：210028779
* QQ 群 IV （已满）：483702734
* QQ 群 V （已满）：460598761
* QQ 群 VI （已满）：581983671
* QQ 群 VII （已满）：252403484
* QQ 群 VIII（已满）：544818750
* QQ 群 IX （已满）：571502246
* QQ 群 X （可加）：145983035

> 如果有容器技术相关的疑问，请通过 [Issues](https://github.com/yeasy/docker\_practice/issues/new/choose) 来提出。

## 进阶学习

[![](https://github.com/yeasy/docker\_practice/raw/master/\_images/docker\_primer3.png)](https://union-click.jd.com/jdc?e=\&p=JF8AANADIgZlGF0VAxUDVBJdHDISBFAfWRcCGzcRRANLXSJeEF4aVwkMGQ1eD0kdSVJKSQVJHBIEUB9ZFwIbGAxeB0gyS34PbFlHVHNkI0MQEAoIcSxyBWFLRAtZK1olABYHXR9eHAoQN2UbXCVQfN\_jrYOwsw7T\_5SOnZUiBmUbXBYBFwBVG14UBBAAZRxbHDJJUjscCEEHEQ4FSA4VBhBQZStrFjIiN1UrWCVAfARQT1gQA0cFAEwOEAcRDlMTDEALQAFTEwwRUhMAUR1cJQATBlES)

《[Docker 技术入门与实战](https://union-click.jd.com/jdc?e=\&p=JF8AANADIgZlGF0VAxUDVBJdHDISBFAfWRcCGzcRRANLXSJeEF4aVwkMGQ1eD0kdSVJKSQVJHBIEUB9ZFwIbGAxeB0gyS34PbFlHVHNkI0MQEAoIcSxyBWFLRAtZK1olABYHXR9eHAoQN2UbXCVQfN\_jrYOwsw7T\_5SOnZUiBmUbXBYBFwBVG14UBBAAZRxbHDJJUjscCEEHEQ4FSA4VBhBQZStrFjIiN1UrWCVAfARQT1gQA0cFAEwOEAcRDlMTDEALQAFTEwwRUhMAUR1cJQATBlES)》第三版已经面世，介绍最新的容器技术栈，欢迎大家阅读使用并反馈建议。

* [京东图书](https://union-click.jd.com/jdc?e=\&p=JF8AANADIgZlGF0VAxUDVBJdHDISBFAfWRcCGzcRRANLXSJeEF4aVwkMGQ1eD0kdSVJKSQVJHBIEUB9ZFwIbGAxeB0gyS34PbFlHVHNkI0MQEAoIcSxyBWFLRAtZK1olABYHXR9eHAoQN2UbXCVQfN\_jrYOwsw7T\_5SOnZUiBmUbXBYBFwBVG14UBBAAZRxbHDJJUjscCEEHEQ4FSA4VBhBQZStrFjIiN1UrWCVAfARQT1gQA0cFAEwOEAcRDlMTDEALQAFTEwwRUhMAUR1cJQATBlES)
* [China-Pub](http://product.china-pub.com/8052127)

## 鼓励项目

<div align="center">

<img src="https://github.com/yeasy/docker_practice/raw/master/_images/donate.jpeg" alt="" width="200">

</div>

**欢迎鼓励项目一杯 coffee\~**
