# web-tracing-next

## 本地调试
先 `pnpm install`
```
第一步：初始化测试项目仓库
nr test

第二步：打包并监听各个sdk
nr watch

第三步：运行js测试项目
nr test:js

nr test:vue2 (也可以运行vue2测试项目)
nr test:vue3 (也可以运行vue3测试项目)
```

## 文档调试
```
nr docs
```

## 粗略版本
### 一期（sdk已有能力）
+ 简单支持 vue2 + vue3
+ 自动采集 + 暴露api给用户手动采集上报
+ 采集功能：【用户事件采集、页面跳转采集、请求采集、错误采集、资源加载采集】
+ 采集上传方法：只提供 sendBeacon(内部自动降级为image)

### 二期已完成功能
+ 整体代码结构更改
+ js -> ts
+ 文档系统与sdk核心代码融合 (vuepress -> vitepress)

### 二期未完成功能
+ 支持区域曝光度采集
+ sourcemap 错误跟踪
+ 白屏检测
+ 首次首屏数据更精确化
+ 现有bug解决
+ 更好的兼容 vue2、vue3（react以及小程序优先级靠后一些）
+ 支持暴露更多变量（例如最大缓存数、延迟上传时间、dom埋点名称等等）
+ 支持hook以及自定义hook
+ 关于用户信息的重构 - 【1.分为未登录与已登录的场景，登录后进行绑定，机器与用户id进行多对多绑定(更多方案还在确定中) 2.支持动态修改用户信息】
+ 支持更多上传方式(这个可以做成钩子，让用户自己决定用哪种，sdk可以提供多种方式以参数的形式传进钩子)
+ 支持延迟加载sdk
+ 支持暴露更多sdk内部方法（例如使用者想要拿到此时的硬件数据）
+ 支持抽样发送，数据临时存储本地的形式减少服务端压力（这里同样也支持断网续联后发送）
+ 支持更多的事件以及行为监听
+ 支持对特定dom监控事件（例如监控页面button按钮的点击事件，这在大批量埋点场景中比较需要，另提供一些属性来标识特定按钮无需采集）(这个先不做)
+ 支持区间打点，区间采集(记录开始和结束，筛选统计开始和结束之间的事件行为,统计到一个分组中)
+ 支持静默（通过配置来设定遇到大批量事件时sdk静默或者用范围时间记录上报，例如当大批量报错时，这个可以让用户来自己控制，sdk会给出钩子）
+ 支持错误信息去重（并自动转为区间事件）(注意原生和vue是否会重复，sdk这个范围要做好去重)(其次这里的场景应该是比如说下午到晚上都报错，那肯定不能一直捕捉错误，错误类型和时间范围应该是集合为一个事件就行)
+ 为 vue3 提供一系列个性化hooks
+ 提供 ignoreErrors，ignoreRequest 等
+ demo官网示例更简单化且提供在线编写能力
+ 探索：一些关键性的api能让使用者去替代更改、比如监听网络状态的内部实现支持使用者去重写
+ 探索：插件化（核心功能+其他插件的形式）
+ 探索：错误录屏

### 二期取消功能
+ 支持加密传输（加密方式待确定）（这个给用户来做，毕竟钩子已经暴露了）


## tips
1. 此场景怎么做：用户一直点一个带了埋点的按钮，怎么去重呢，简单的节流去重？
2. 用户行为应该给个id，要不然后台不好快速找特定事件，点击按钮可以自己给id，但是切换事件或者曝光就拿不到id了，得找个办法去定义
3. 全部事件先存本地，等到了一个期限再上传
4. 给用户中途更改参数的函数
5. 考虑一些枚举也给用户可以改


## 技术层面重构点
1. options参数集中管理
2. 事件注册以及事件改写集中管理，其他模块需要的则会去模块存放/获取
3. 后面可以用 proxy 来自动化一些东西吗
4. 受不了了，后面研究下用@的方式引入
5. 在业务中碰到的跳转客服功能，看看能不能在这里用utils包实现