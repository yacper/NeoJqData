# NeoJqData
聚宽 JqData C# Api

update: 2022/7/21 经确认，聚宽http接口功能降级，主要focus在python接口，有些接口已无法从http方式取得数据，比如get_ticks.

## 0. IJQDataClient -- JqData Api Interface
```cs
public interface IJQDataClient
{
    string              ApiKey { get;}
    string              LastErrMsg { get; }
...
}
```
通过IJQDataClient可以与聚宽数据进行通信。


## 1. Connction
### ConnectionState
```cs
public enum EConnectionState
{
    Disconnected =0,
    Connecting,
    Connected,
    Disconnecting,
}

 /// <summary>
 /// Gets a value indicating whether is the client connected to tws
 /// </summary>
 EConnectionState ConnectionState { get; }
```
### Connect
```cs
IJQDataClient _Client = new JQDataClient(user, password);
bool ret = await _Client.connect();
Assert.AreEqual(ret, true);
```
## 2. security Management
### Get securityInfo
```cs
SecurityInfo rtn = await  _Client.get_security_info("000001.XSHE");
rtn.Should().NotBeNull();
```

### get_all_securities
```cs
public enum ECodeType // 证券类型
{
    stock,
    fund,
    index,
    futures,
    etf,
    lof,
    fja,
    fjb,
    QDII_fund,
    open_fund,
    bond_fund,
    stock_fund,
    money_market_fund,
    mixture_fund,
    options
}

// 获取一个类别下的所有证券信息
var rtn = await  _Client.get_all_securities(ECodeType.futures);
ret.Should().NotBeEmpty();
```

## 3. Historical Data Management
### Get historical data
```cs
//code: 证券代码
//count: 大于0的整数，表示获取bar的条数，不能超过5000
//unit: bar的时间单位, 支持如下周期：1m, 5m, 15m, 30m, 60m, 120m, 1d, 1w, 1M。其中m表示分钟，d表示天，w表示周，M表示月
//end_date：查询的截止时间，默认是今天
//fq_ref_date：复权基准日期，该参数为空时返回不复权数据
Task<List<Bar>>     get_price(string code, ETimeFrame timeframe = ETimeFrame.m1, int count = 5000, DateTime? endDate = null, DateTime? fq_ref_date = null);

//指定开始时间date和结束时间end_date时间段，获取行情数据
//code: 证券代码
//unit: bar的时间单位, 支持如下周期：1m, 5m, 15m, 30m, 60m, 120m, 1d, 1w, 1M。其中m表示分钟，d表示天，w表示周，M表示月
//date : 开始时间，不能为空，格式2018-07-03或2018-07-03 10:40:00，如果是2018-07-03则默认为2018-07-03 00:00:00
//end_date：结束时间，不能为空，格式2018-07-03或2018-07-03 10:40:00，如果是2018-07-03则默认为2018-07-03 23:59:00
//fq_ref_date：复权基准日期，该参数为空时返回不复权数据
//注：当unit是1w或1M时，第一条数据是开始时间date所在的周或月的行情。当unit为分钟时，第一条数据是开始时间date所在的一个unit切片的行情。 最大获取1000个交易日数据
Task<List<Bar>>     get_price_period(string code, ETimeFrame timeframe, DateTime date, DateTime? endDate = null, DateTime? fq_ref_date = null);

Task<List<CurrentPrice>>  get_current_price(IEnumerable<string> codes); 

// 需要付费
// startdate的参数名不对，无法正确指定，目前函数有问题
Task<List<Tick>>     get_ticks(string code, DateTime? startDate = null, DateTime? endDate = null, int count = 5000, string fields ="None",bool skip = true , bool df = false);
```

更多请参看unitTests.
