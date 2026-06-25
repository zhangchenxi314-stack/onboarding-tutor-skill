# Sequence Diagram Guide（时序图规范）

## 基本原则

时序图用于展示流程、调用链、数据流转。一个图讲清楚一件事。

## 规模控制

- 参与者（participant）：5-8 个
- 交互次数：10-15 次
- 嵌套深度：不超过 3 层（alt/loop/par）
- 超过规模的，拆成多个图

## 参与者命名

```
participant A as 客户端
participant B as API网关
participant C as 服务名（中文）
```

- 别名用大写字母（A, B, C），简洁
- `as` 后面用中文业务名称

## 消息类型

| 箭头 | 含义 | 使用场景 |
|------|------|----------|
| `->>` | 同步调用 | HTTP/RPC 请求 |
| `-->>` | 异步/响应 | 回调、MQ 消息、返回值 |
| `-x` | 失败/异常 | 调用失败、超时 |
| `--x` | 异步失败 | MQ 消费失败 |

## 常用块

```
alt 条件1
    A->>B: 正常路径
else 条件2
    A->>B: 降级路径
end

loop 每N秒
    A->>B: 轮询
end

par 并行
    A->>B: 操作1
and
    A->>C: 操作2
end
```

## 标注（Note）

```
Note right of A: 关键说明
Note over A,B: 横跨多个参与者的说明
```

## 完整示例：订单创建流程

```
sequenceDiagram
    participant U as 用户
    participant G as API网关
    participant O as 订单服务
    participant I as 库存服务
    participant M as 消息队列
    participant F as 履约服务

    U->>G: POST /order/create
    G->>O: 转发请求（鉴权通过）

    alt 库存充足
        O->>I: 预占库存(sku, qty)
        I-->>O: 预占成功
        O->>O: 写入订单表
        O->>M: 发送"订单创建"事件
        O-->>G: 200 OK (order_id)
        G-->>U: 返回订单ID
        M-->>F: 消费事件，启动履约
    else 库存不足
        O->>I: 预占库存(sku, qty)
        I-->>O: 库存不足
        O-->>G: 409 库存不足
        G-->>U: 提示库存不足
    end
```

## 注意事项

1. 不要在时序图中写代码片段
2. 不要用过长的消息文本（>40字符就考虑缩写）
3. 用 Note 标注关键设计决策（如"此处用异步是为了解耦"）
4. 参与者顺序按调用方向排列（左→右 = 上游→下游）
5. 错误路径用 alt/else 明确展示，不要省略
