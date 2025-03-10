---
title: Performance.onresourcetimingbufferfull
slug: Web/API/Performance/resourcetimingbufferfull_event
---
{{APIRef("Resource Timing API")}}

**`onresourcetimingbufferfull`** 属性是一个在{{event("resourcetimingbufferfull")}}事件触发时会被调用的 {{domxref("EventHandler","event handler")}} 。这个事件当浏览器的资源时间性能缓冲区已满时会触发。

## 语法

```plain
callback = performance.onresourcetimingbufferfull = buffer_full_cb;
```

### 返回值

- callback
  - : 一个当{{event("resourcetimingbufferfull")}} 事件触发时调用的{{event("Event_handlers", "event handler")}} 。

## 例子

下面的示例在 `onresourcetimingbufferfull` 属性上设置一个回调函数。

```js
function buffer_full(event) {
  console.log("WARNING: Resource Timing Buffer is FULL!");
  performance.setResourceTimingBufferSize(200);
}
function init() {
  // Set a callback if the resource buffer becomes filled
  performance.onresourcetimingbufferfull = buffer_full;
}
<body onload="init()">
```

## 规范

{{Specifications}}

## 浏览器兼容性

{{Compat("api.Performance.onresourcetimingbufferfull")}}

## 参见

- {{event("resourcetimingbufferfull")}} event
- {{domxref("Performance.clearResourceTimings","Performance.clearResourceTimings()")}}
- {{domxref("Performance.setResourceTimingBufferSize","Performance.setResourceTimingBufferSize()")}}
