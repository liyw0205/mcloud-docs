---
title: 更新日志
description: mcloud-sign 主要版本与已提交能力变化
---

## 当前已提交状态

- 提供 `drawRedFlower(codes, configPath?)` 手工口令入口与 `autoDrawRedFlower(configPath?)` 自动抓取入口，均可遍历账号领取小红花。
- 直播口令任务 `liveRoomKoulingTask` 已接入 CLI 主流程（`直播口令.开启`，默认关闭），并提供 `dev:kouling` 独立运行入口。
- Runtime Adapter 已覆盖 Buffer、文件系统、路径、加密、压缩、进程信息和 WebSocket。
- Node.js 与 txiki.js 构建分别输出带运行时后缀的独立产物（`out/` 目录）。
- `fail` 按最低推送过滤组收录，但 `onlyError` 只检查 `error` 类型日志。
- 微信签到、微信抽奖和摇一摇已过期，不再实现。

:::note[文档发布边界]
本页只记录 `mcloud-sign` Git `HEAD` 已提交能力。未提交工作区中的试验或后续开发不会提前作为正式能力发布。
:::

## v2.1.0

- 新增推送日志级别配置 `minLevel`。
- 优化红包派对日志输出。
- 将 `fail` 输出调整为 info 级业务结果。
- 修复无效口令重试时 authorization 已被清除导致 WebSocket 口令来源失效的问题；任务结束前恢复凭证，不影响同账号后续任务。
- 小红花领取统计对缺失字段补默认值；响应解析异常不再误判为无效口令；`drawRedFlower` 入口补账号配置判空。

## v2.0.2

- 新增 `autoDrawRedFlower` 自动抓取口令并领取小红花的独立任务。
- 新增 `hasActiveLiveRoom` 前置检查：未开播时跳过口令抓取与领取，验证失败时不拦截。
- 口令抓取仅监听主播「中国移动云盘」的直播中直播间。
- 多账号共享口令缓存（5 小时 TTL）；存在无效口令时自动重听直播间一次，只提交新口令。
- 新增 `dev:kouling` 直播口令独立运行入口。

## v2.0.0

- 引入 v2 配置结构和多包架构。
- 新增小红花口令兑换与独立兑换入口。
- 推进 Node.js / txiki.js 双运行时支持。

## 历史功能说明

早期版本曾实现微信签到、微信抽奖和摇一摇。对应活动或接口现已过期；历史记录仅用于说明版本演进，不代表当前可用能力。
