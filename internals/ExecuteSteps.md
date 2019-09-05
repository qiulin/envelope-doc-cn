# 执行流程

### EnvelopeMain

Envelope 启动入口为 EnvelopeMain.class。在 EnvelopeMain.main 中做了必要的配置读取之后通过 Runner.run 启动流程。

### Runner 流程

#### 1. 合并校验配置

1. 如果配置文件中有 config-loader 选项，通过 config-loader 加载额外的配置，并创建 ConfigLoader 对象
2. 校验配置，可以通过 configuration.validation.enabled 配置项跳过配置校验的步骤
3. 否则，则获取 steps 列表，并调用 validate 校验 step 配置和 application 配置

#### 2. 提取作业步骤（Step）

1. 根据配置提取 4 种组件，DataStep（默认）、LoopStep（循环）、TaskStep（外部任务）、DecisionStep（条件判断）
2. DataStep 组件中只初始化了 Input，如果 Input 为 BatchInput，则该 DataStep 为 BatchStep，如果 Input 是 SteamInput， 则该 DataStep 为 SteamStep
3. 如果配置了事件回调，则通知之

#### 3. 根据 steps 判断执行模型（批处理还是流处理）

整个作业中只要有 SteamStep，则判定就是流处理模型。

#### 4. 根据配置和 steps 初始化上下文

1. 把 application 配置挂载到 INSTANCE.config 下
2. 设置 INSTANCE.mode
3. 创建 SparkSession

#### 5. 初始化安全选项

#### 6. 初始化事件监听

#### 7. 初始化累加器

1. 获取 DataStep 中的累加器，并初始化之。

#### 8. 初始化 UDFs

1. 根据配置注册 UDFs

#### 9. 初始化线程池

1. 根据配置的线程数量，初始化线程池

#### 10. 回调 Pipeline 启动事件

1. 广播 Pipeline 启动事件

#### 11. 启动批或流处理


1. 如果是流处理则调用 runSteaming 启动 steps
2. 如果是批处理，则调用 runBatch 启动 steps


### Streaming 作业执行流程

1. 抽取出无依赖关系的批处理流程 independentNonStreamingSteps，然后通过 runBatch 运行之
2. 

### Batch 作业启动流程