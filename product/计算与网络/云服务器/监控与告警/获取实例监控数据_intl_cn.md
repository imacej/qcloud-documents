腾讯云默认为所有用户提供云监控功能，无需用户手动开通。但用户必须使用了某种腾讯云产品后云监控才能开始收集监控数据，要查看这些监控数据，有以下几种方式：

## 通过云产品控制台获取
云服务器在自身的控制台页面提供改了单独的监控数据读取选项卡。监控页面可查看到云服务器实例的 CPU 、内存、网络带宽、磁盘等监控数据，并可任意调整查看的时间段。

1. 登录 [云服务器控制台](https://console.cloud.tencent.com/cvm)  。

2. 列表中,单击要查看的实例 ID，进入详情页。

3. 单击【监控】选项卡。

## 通过云监控控制台获取
云监控控制台是所有产品监控数据的统一入口，用户可以在这里查看到云服务器的监控数据。监控页面可查看到云服务器实例的 CPU 、内存、网络带宽、磁盘等监控数据，并可任意调整查看的时间段。

1. 登录 [云监控控制台](https://console.cloud.tencent.com/monitor/overview) 。

2. 在左侧导航选择【云产品监控】-【云服务器】。

3. 列表中，单击要查看的实例 ID，进入监控详情页。

## 通过 API 获取
用户可以使用 GetMonitorData 接口获取所有产品的监控数据，具体内容可以参考 [读取监控数据 API](/doc/api/405/4667) 。


