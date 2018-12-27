# metric-client

python版的指标收集客户端

## 安装

```sh
pip install metric-client
```

## 使用

```python
from metric_client import MetricClient

# send_api: 接收推送数据的API
# token：负责appcode的身份验证，不能为空
token = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ2ZXJzaW9uIjoxLCJhcHBjb2RlIjoib3BzX21ldHJpY2d3IiwidG9fdXNlciI6Inh4eHguemhhbyIsImlhdCI6MTU0NTczNTczMH0.Aj8srWIjyFwxhcMrZlCxyNlP44uLG0iiR31ynyYd4Bw'
send_api = 'http://localhost:6066/v1/metric/send'
metric = MetricClient(send_api, token)
metric.set('set', 344)
metric.counter('counter', 100)
metric.timing('timing', 100)
metric.summary('summary', 100)
```

## 指标类型

### set

set 类型的指标，在一个指标周期（比如1分钟）中只会取最后一个值，是最普通的的指标类型。

该指标不需要在一个周期内做任何聚合操作，所以这种类型的指标可以复写（也就是说，你可以为这种类型的指标指定时间戳，该时间戳可以是过去的，也可以是将来的）

### counter

counter 类型的指标，可用于记录qps等场景。

在一个指标周期（比如1分钟）可以发生变化，可以增加，也可以减小。该指标需要在一个周期内做聚合操作，所以不可以指定时间戳（也就是说，只能按照当前时间戳计算）。
该指标最后输出一个汇总值（即周期内所有值的求和）。

### timing

timing 类型的指标，可用于记录代码执行时间。
每次update指定一个value和count（默认为1），该指标最后输出一组汇总值（最大值，最小值，总和，总数量，平均值）
需要在一个周期内做聚合操作，所以不可以指定时间戳（也就是说，只能按照当前时间戳计算）。

### summary

summary 是一种更加复杂的统计类型指标，会输出一组汇总值，如平均值，总和，总数量，百分位数（P90，P99等）。

该指标需要在一个周期内做聚合操作，所以不可以指定时间戳（也就是说，只能按照当前时间戳计算）。

该指标的的输入支持两种类型：元数据（raw）和t-digest序列化字符串（tdigest）