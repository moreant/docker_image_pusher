# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

Docker Images Pusher - 使用 GitHub Actions 将国外 Docker 镜像同步到阿里云私有仓库的自动化工具。

## 核心文件

- `images.txt` - 镜像配置列表，每行一个镜像，支持 `#` 注释和 `--platform` 参数
- `.github/workflows/docker.yaml` - GitHub Actions 工作流，包含所有同步逻辑

## 镜像格式规范

```
# 注释行
nginx:latest
--platform=linux/arm64 xiaoyaliu/alist
k8s.gcr.io/kube-state-metrics/kube-state-metrics:v2.0.0
```

## 工作流触发方式

1. **手动触发**: GitHub Actions 页面点击 "Run workflow"
2. **自动触发**: 推送到 main 分支时自动执行
3. **定时触发**: 可在 docker.yaml 中配置 `schedule`

## 环境变量配置

需要在 GitHub 仓库 Settings → Secrets and variables → Actions 中配置：

- `ALIYUN_REGISTRY` - 阿里云仓库地址（如 registry.cn-hangzhou.aliyuncs.com）
- `ALIYUN_NAME_SPACE` - 阿里云命名空间
- `ALIYUN_REGISTRY_USER` - 阿里云用户名
- `ALIYUN_REGISTRY_PASSWORD` - 阿里云密码

## 关键架构逻辑

docker.yaml 中的两个循环：

1. **第一轮循环** - 检测镜像重名情况，使用关联数组记录命名空间
2. **第二轮循环** - 实际拉取、重命名、推送镜像

镜像命名规则：
- 普通镜像: `{ALIYUN_REGISTRY}/{NAMESPACE}/{镜像名:tag}`
- 多架构镜像: `{平台信息前缀}_{镜像名:tag}`
- 重名镜像: `{命名空间前缀}_{镜像名:tag}`

## 修改 images.txt 后的操作

提交到 main 分支后自动触发 GitHub Actions 构建。无需手动执行任何命令。
