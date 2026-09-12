---
title: 小红花与直播口令
description: 手工口令领取、自动口令抓取与小红花领取
---

mcloud-sign 提供三种使用方式：CLI 主流程内开启直播口令任务、独立入口 `autoDrawRedFlower`，以及手工传入口令的 `drawRedFlower`。

:::note[配置开关]
直播口令任务由配置项 `直播口令.开启` 控制（默认关闭）：

```json
{
  "直播口令": { "开启": true }
}
```

开启后，CLI 主流程会在每个账号任务列表中执行口令抓取与小红花领取。
:::

## 手工领取

单文件入口导出 `drawRedFlower`：

```javascript
import { drawRedFlower } from "./index.mjs";

await drawRedFlower([
  "云盘宠粉会员日",
  "会员日福利多多"
]);
```

该入口会加载配置并遍历全部有效账号。也可以传入第二个参数指定配置路径：

```javascript
await drawRedFlower(["口令内容"], "/absolute/path/to/asign.json");
```

## 自动抓取

`autoDrawRedFlower` 会自动获取直播口令并遍历所有账号领取小红花，不依赖 CLI 主流程，适合在直播活动时段配合 cron 调用：

```javascript
import { autoDrawRedFlower } from "./index.mjs";

await autoDrawRedFlower();
// 或指定配置路径
await autoDrawRedFlower("/absolute/path/to/asign.json");
```

也可以使用 CLI 包附带的独立运行入口：

```bash
npm run dev:kouling
```

cron 示例（活动时段内每 30 分钟一次）：

```cron
0,30 9-11 * * * cd /path/to/mcloud-sign && npx tsx -e "import('@asunajs/core').then(m => m.autoDrawRedFlower())"
```

## 领取过程

对每个账号，程序会：

1. 检查「中国移动云盘」主播是否开播，未开播则跳过口令抓取与领取。
2. 获取当前小红花数量。
3. 上报直播活动所需埋点。
4. 尝试领取首次参与奖励。
5. 逐个兑换口令。
6. 查询复活花和最终数量。

被服务端拒绝的口令（非成功、非已兑换）会记录为无效口令，并自动重新监听一次直播间、只提交新发现的口令。多账号共享一份口令缓存（5 小时 TTL），首个账号抓取成功后，后续账号直接复用，无效口令会从缓存中剔除。

## 口令来源

`getLiveKouling` 并行从两个来源抓取口令，合并去重后返回：

1. WebSocket 实时监听直播弹幕（默认收到首个口令即返回，内部超时上限 180 秒）。
2. 小红书主页抓取。

仅监听主播「中国移动云盘」的直播中直播间；其他主播或未开播时会跳过。

## 注意事项

- 口令通常有活动时效，过期后接口会返回业务结果。
- 直播未开播、SSO 或直播登录临时失败时，任务会跳过或降级（验证失败不拦截，避免漏掉口令）。
- WebSocket 弹幕解析按格式提取口令（多行「复制口令N：」、单行、「口令是XXX」、纯数字编号），并对「口令是什么？」这类提问式弹幕做了过滤。
