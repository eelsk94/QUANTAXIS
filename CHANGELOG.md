# QUANTAXIS 更新纪要

<!-- TOC -->

- [QUANTAXIS 更新纪要](#quantaxis-更新纪要)
    - [1.0.64 (unreleased)](#1064-unreleased)
    - [1.0.63](#1063)
    - [1.0.62](#1062)
    - [1.0.61](#1061)
    - [1.0.60](#1060)
    - [1.0.59](#1059)
    - [1.0.58](#1058)
    - [1.0.57](#1057)
    - [1.0.56](#1056)
    - [1.0.55](#1055)
    - [1.0.54](#1054)
    - [1.0.53](#1053)
    - [1.0.52](#1052)
    - [1.0.51](#1051)
    - [1.0.50](#1050)
    - [1.0.49](#1049)
    - [1.0.48](#1048)
    - [1.0.47](#1047)
    - [1.0.46](#1046)
    - [1.0.45](#1045)
    - [1.0.44](#1044)
    - [1.0.43](#1043)
    - [1.0.42](#1042)
    - [1.0.41](#1041)
    - [1.0.40](#1040)
    - [1.0.39](#1039)
    - [1.0.38](#1038)
    - [1.0.37](#1037)
    - [1.0.36](#1036)
    - [1.0.35](#1035)
    - [1.0.34](#1034)
    - [1.0.33](#1033)
    - [1.0.32](#1032)
    - [1.0.31](#1031)
    - [1.0.30](#1030)
    - [1.0.29](#1029)
    - [1.0.28](#1028)
    - [1.0.27](#1027)
    - [1.0.26](#1026)
    - [1.0.25](#1025)

<!-- /TOC -->
## 1.0.64 (unreleased)

1. 修复了QA_RISK的bug
2. 实时采集的数据,支持实时采样 (QA_fetch_quotation/QA_data_tick_resample)


## 1.0.63 

1. 紧急修复因为ts.get_stock_basics()获取error导致的无法存储问题
2. 增加了对于选股的需求(选股模块.md)

released in : JULY 9, 2018

## 1.0.62 

1. QA_DataStruct_Indicator 类增加 ```groupby``` 函数和  ```add_func```函数 ,用法和QA_DataStruct_xxxx_DAY/MIN 一致
2. QA_DataStruct_Block 增加两个视图 ```view_code``` 和 ```view_block```
3. QA_DataStruct_xxx_Day/Min 增加一个 ```fast_moving(pct)``` 函数, 用于表达bar的快速涨跌幅(返回series)
4. QA_Data 增加一个 QA_DataStruct_Series() 类, 用于分析行情的series数据
5. QA_DataStruct_Block 重写, 改成Multiindex驱动的数据格式
6. 实现了一个快速分析全市场一段时间内异动的代码
    ```python
    # 引入QUANTAXIS
    import QUANTAXIS as QA
    # 获取全市场版块
    block=QA.QA_fetch_stock_block_adv()
    # 获取全市场股票
    code=QA.QA_fetch_stock_list_adv().code.tolist()
    # 获取全市场2018-07-05的1分钟线
    min_data=QA.QA_fetch_stock_min_adv(code,'2018-07-05','2018-07-05','1min')
    # 查找1分钟线bar涨幅超过3%的股票
    L=min_data.fast_moving(0.03)
    # 使用SeriesDataStruct加载结果
    L1=QA.QA_DataStruct_Series(L)
    # 查看某一个时刻的股票代码
    L1.select_time('2018-07-05 09:33:00').code
    # 使用版块查找这个时段的代码归属版块
    block.get_code(L1.select_time('2018-07-05 09:31:00').code).view_block
    block.get_code(L1.select_time('2018-07-05 09:31:00','2018-07-05 09:41:00').code).view_block
    ```

    返回
    ```text
    blockname
    IP变现                     [300426]
    ST板块                     [000953]
    上周强势                     [300547]
    两年新股             [002808, 300547]
    低市净率                     [002541]
    军民融合                     [300265]
    创业300            [300278, 300426]
    参股金融                     [000953]
    国防军工             [300265, 300278]
    小盘股              [002808, 300265]
    已高送转             [002541, 300547]
    户数减少                     [300042]
    户数增加             [300278, 300547]
    新能源车                     [300547]
    昨日振荡                     [300265]
    昨日涨停                     [300426]
    昨曾涨停                     [300265]
    昨高换手                     [601990]
    智能机器                     [300278]
    次新开板                     [601990]
    次新股                      [601990]
    皖江区域                     [002541]
    破净资产                     [002541]
    股权激励             [300278, 300547]
    股权转让             [000953, 300042]
    近期新低                     [002541]
    送转潜力                     [300042]
    送转超跌             [002808, 300426]
    高质押股     [300042, 300265, 300278]
    ```
7. 修复了save financialfiles的代码
8. 修复了QAWEB在非windows机器上的bug
9. 添加了 save option_day 保存50etf期权的命令到数据库中
10. 增加了config文件的 update_all.py 和 update_x.py 文件, 用于自动化任务管理
11. 增加QASetting模块, 用于QUANTAXIS的设置/配置/任务管理

released in : JULY 8, 2018


## 1.0.61 

1. QA_MARKET 增加订单查询子线程函数```start_order_threading```,线程名称('ORDER') (如股票无回报,需要另外开线程查询是否成交)[如果需要在初始化的时候开启: if_start_orderthreading=True]

    ```python
    threading.enumerate()
    [<_MainThread(MainThread, started 23780)>,
    <Thread(Thread-4, started daemon 4504)>,
    <Heartbeat(Thread-5, started daemon 7760)>,
    <HistorySavingThread(IPythonHistorySavingThread, started 23764)>,
    <ParentPollerWindows(Thread-3, started daemon 17028)>,  
    <Thread(pymongo_server_monitor_thread, started daemon 20440)>,
    <Thread(pymongo_kill_cursors_thread, started daemon 20216)>,
    <QA_ENGINE with ['ORDER', 'SPE_BROKER', 'BACKTEST_BROKER'] kernels>,
    <QA_ThreadORDER  id=2226925613408>,
    <QA_ThreadSPE_BROKER  id=2226855623648>,
    <QA_ThreadBACKTEST_BROKER  id=2226925616992>]
    ```
    
2. QA_ORDER 增加一个 ```realorder_id ``` 用于记录订单在报给交易所后返回的order_id
3. 修复了QA_fetch_get_exchangerate_list的bug

released in : JULY 4, 2018

## 1.0.60 

1. groupy 默认参数中 sort设置为false
2. 加速指标运算/前后复权 (视股票数量而定,3000多只股票提速20倍)
3. 回测加速(test_backtest/MACD_JCSC.py) 从14秒提速到2秒
4. QA_Risk 增加

> max_holdmarketvalue 最大持仓市值,min_holdmarketvalue 最小持仓市值, average_holdmarketvalue 平均持仓市值
> max_cashhold 最大闲置现金, min_cashhold 最小持仓现金, average_cashhold 平均持仓现金

5. QA_Performance 增加:

> win_rate(methods='FIFO') 胜率
> average_profit(methods='FIFO') 平均利润

6. 增加QA_Trade模块,QATrade_Realtime类(未完成)

7. 支持 期权数据/ 港股数据获取/ 部分美股数据/ 国际期货数据/ 宏观指标/ 汇率数据/

- QA_fetch_get_option_list 获取期权列表(郑州商品期权/大连商品期权/上海商品期权/中金所期权/上海股票期权)
- QA_fetch_get_globalfuture_list 获取国际期货列表(伦敦金属/伦敦石油/纽约商品/纽约石油/芝加哥谷/东京工业品/纽约期货/新加坡期货/马来期货)
- QA_fetch_get_hkstock_list 获取香港主板/创业板股票
- QA_fetch_get_hkfund_list  获取香港基金列表
- QA_fetch_get_hkindex_list 获取香港指数列表
- QA_fetch_get_usstock_list 获取美股股票列表
- QA_fetch_get_macroindex_list 获取宏观指数列表
- QA_fetch_get_exchangerate_list 获取汇率数据(基础汇率/交叉汇率)

- QA_fetch_get_option_day 获取期权(郑州商品期权/大连商品期权/上海商品期权/中金所期权/上海股票期权)日线
- QA_fetch_get_globalfuture_day 获取国际期货日线(伦敦金属/伦敦石油/纽约商品/纽约石油/芝加哥谷/东京工业品/纽约期货/新加坡期货/马来期货)
- QA_fetch_get_hkstock_day 获取香港主板/创业板股票日线
- QA_fetch_get_hkfund_day  获取香港基金日线
- QA_fetch_get_hkindex_day 获取香港指数日线
- QA_fetch_get_usstock_day 获取美股股票日线
- QA_fetch_get_macroindex_day 获取宏观指数日线
- QA_fetch_get_exchangerate_day 获取汇率数据(基础汇率/交叉汇率)日线

- QA_fetch_get_option_min 获取期权(郑州商品期权/大连商品期权/上海商品期权/中金所期权/上海股票期权)分钟线
- QA_fetch_get_globalfuture_min 获取国际期货分钟线(伦敦金属/伦敦石油/纽约商品/纽约石油/芝加哥谷/东京工业品/纽约期货/新加坡期货/马来期货)
- QA_fetch_get_hkstock_min 获取香港主板/创业板股票分钟线
- QA_fetch_get_hkfund_min  获取香港基金分钟线
- QA_fetch_get_hkindex_min 获取香港指数分钟线
- QA_fetch_get_usstock_min 获取美股股票分钟线
- QA_fetch_get_macroindex_min 获取宏观指数分钟线
- QA_fetch_get_exchangerate_min 获取汇率数据(基础汇率/交叉汇率)分钟线

8. python3 CTP接口 [WINDOWS/LINUX]
9. shipane broker增加key参数, 安全性保障
10. QA_MARKET 增加在注册账户的时候的交易同步(实盘/模拟盘)
11. @yehoha 增加了对QA_DATASTRUCT的振幅的修改
12. @zsluedem 对虚拟货币币安交易所的数据进行了优化
13. QA_WEB 增加本地实时5挡行情接口 ip:port/marketdata/stock/price?code=xxxxxx
14. QA_WEB 增加了对于非windows下的机器多进程的支持

released in : JULY 4, 2018

## 1.0.59 

1. 修改了DataStruct的high_limit和low_limit的计算方式
    - 惰性计算,取消在初始化的时候的计算
    - 修复了多code的时候的bug

2. 修改了groupby写法, 增加的QADataStruct的groupby参数
3. 修改了前复权等各种涉及groupby('code')可能报错的情况,改成level层面的操作,以后不会出现warning
    
released in : JUNE 27, 2018


## 1.0.58 
1. QA_Account 增加hold_time属性, 显示持仓时间
2. 对于QA_Query 的 QA_fetch_financialfiles进行修改, 优化返回结果
3. QA_DataStruct_Block 修改了get_block方法, 可以获取多个block_name
4. 修改了financialdicts里面,两个重复的净利润,将现金流量表中的改成netProfitFromOperatingActivities
5. QA_SU_save_stock_info_tushare加到主函数中
6. QAAnalysis_Block细微修改,增加__repr__
7. 文档增加回测和测试账户部分(Documents/)
8. 增加指数装饰器@QDS_IndexDayWarpper, @QDS_IndexMinWarpper
9. 更新jupyter的文档(Documents/usejupyter.md)
10. DataStruct的high_limit和low_limit的bug修复
11. @喜欢你 更新了mac下的financialfiles存储问题

released in : JUNE 27, 2018

## 1.0.57 

1. 重新修改了依赖项
released in : JUNE 24, 2018

## 1.0.56 
1. 优化了 'crawl eastmoney zjlx all' 获取东方财富资金流向的操作，保存到mongodb数据库中
2. @pchaos 完善了通过配置文件排除ip(某些ip长期BAD RESPONSE),同时补充一个requirements
3. 实盘易单账户测试完毕
4. 期货实时tick的接口修复
5. 数据获取QAFetch的jupyter例子更新(jupyterexample/QAFetch.ipynb)
6. 修改ORDER_MODEL 中的对应values为 大写
7. 增加实盘易broker的query_clients方法
8. 修改了QAWeb的获取数据优先级,避免在无mongodb的时候的```connection timeout```问题
9. QA_Account 修改了两个函数(```account.get_history(start,end)``` 获取历史成交,```hold_table``` 修改去除0持仓的股票 )
10. QA_Risk 增加一个property(```risk.daily_market_value```每日总市值)
11. 优化了Backtest_broker的market_data的判定,加入series的支持
released in : JUNE 24, 2018

## 1.0.55

优化了save financialfiles 的逻辑
released in : JUNE 18, 2018

## 1.0.54

优化了save financialfiles 的逻辑
released in : JUNE 17, 2018

## 1.0.53

优化了save financialfiles 的逻辑
released in : JUNE 17, 2018

## 1.0.52 

1. @几何提交了 比特币部分的爬虫
2. QAWEB部分后台增加了基于account_cookie的查询(ip:port/accounts?account_cookie=xxx)
3. @几何 优化了setup.py文件
4. 财务数据的存储,获取
5. QA_fetch_financial_report
6. QACLI--> save financialfiles
7. QASU.QA_SU_save_financial_files()

released in : JUNE 17, 2018

## 1.0.51 

1. 增加三个函数到QA主函数中: QA_fetch_get_future_transaction, QA_fetch_get_future_transaction_realtime, QA_fetch_get_future_realtime

released in : JUNE 14, 2018

## 1.0.50 
1. 添加了获取东方财富个股资金流向保存到sqlite的命令， windows 和 mac 下测试过
2. crawl eastmoney zjlx 6位股票代码 命令 和获取所有股票资金流向 crawl eastmoney zjlx all 的命令，
3. 添加了 QUANTAXIS_CRAWLY 目录，一个scrapy的空的项目，后期支持 各种经济新闻，证券报刊信息，热点咨询的获取
4. QUANTAXIS/QAWeb 用tornado的后台重写
5. 基于websocket的实时数据推送
6. 期货历史tick,期货实时数据支持

released in : JUNE 14, 2018

## 1.0.49 

1. @喜欢你 提交了资金流向爬虫(QUANTAXIS CLI/ crawl)
2. 修复1.0.48-2的引入,使用ImportError错误项
3. dockerfile更新

released in : JUNE 14, 2018


## 1.0.48

1. 修改了QA_Portfolio, 增加init_hold, init_hold_table 字段,可以查看组合的初始化持仓,以及带account的初始化持仓
2. 修改了QA_Risk的引入, 测试引入import tkinter

released in : JUNE 12, 2018

## 1.0.47

1. 修改了QAMARKET 适配t0回测
2. 增加t0回测示例
3. 分钟线撮合不再加一分钟
4. T0回测买入限额,QA_Account.buy_available
5. 修改示例,使用随机买卖来测试框架 https://github.com/QUANTAXIS/QUANTAXIS/blob/master/test_backtest/T0backtest.ipynb
6. 增加对于多个标的的t0账户的支持
7. 修复一个QA_Account下计算account.trade因为pivot_table默认使用np.mean作为arg_func的bug,该bug会导致在相同时间开了方向相反的仓位,会被计算成平均数
8. 修复了一个QA_fetch_stock_day_full()中set_index的bug

released in : JUNE 12, 2018

## 1.0.46
1. 命令行中 添加了 save stock_info_tushare 保存tushare股票列表的信息到数据库中
2. 修改了实盘易 broker 增加对接
3. 修改了base_datastruct的 selects,select_time,select_month,get_bar,select_code,增加异常处理(ValueError)
4. 基于pandas的反馈,使用remove_unused_levels来对索引进行更新
5. 大幅修改 base_datastruct方法的 select_time_by_gap, splits, add_func方法,优化性能
6. 增加了一个期货下单接口(QUANTAXIS_trade/WYFFuture)
7. 成交量复权修正
8. 实盘易下单对接(单账户)
9. 删除emoji导致的windows输出不兼容
10. 增加部分广州ip
11. 增加一个通达信的成交记录读取接口
12. 修改存储打印
13. 修改了分钟线初始化的column请求,使用if in columns来代替
14. 修改了Backtest内部在获取_quotation时候的dict匹配,使用pd.Timestamp来代替
15. 修改了threadeng, 使用raise error 报错
16. 修改了QA_Account/QA_Portfolio的账户初始化过程, init_assets==> init_cash, 新版的init_assets(只读属性)会返回一个dict{'cash':xx,'hold':{}}
17. 删除了初始化过程中cash/history的输入
18. QA_Account 增加两个property self.datetime/self.date 均为account运行的时候的实时时间和日期
19. QA_Account 增加一个close_positions_order 属性, 仅限T0账户使用, 返回一个list,里面都是封装好的QA_Order
20. 对于QA_Account的T0模式增加一系列适配
21. 修改一个example,展示T0的使用,更多文档正在补充
22. QA_RISK 修改了利润的计算模式,以及benchmark的assets(改为从收盘价计算资产)
23. QA_RISK 增加一个利润构成表 risk.profit_construct
24. QA_RISK 增加总手续费,总印花税(risk.total_commission,risk.total_tax)
25. QA_RISK 增加市值表计算(risk.market_value)
26. 修复了QA_Account的一个计算daily_cash的bug

released in : JUNE 11, 2018

## 1.0.45 

1. 在安装完毕后,会弹出一个浏览器页面,告知最新更新
2. 修复1.0.42出现的一个bug (select_code的问题), 同时兼顾1.0.44的写法进行修改

```
因此先用set_index去重做一次index
影响的有selects,select_time,select_month,get_bar,select_code
```

released in :JUNE 03, 2018



## 1.0.44

1. @2018/06/03 pandas 的索引问题导致
https://github.com/pandas-dev/pandas/issues/21299

因此先用set_index去重做一次index
影响的有selects,select_time,select_month,get_bar

released in :JUNE 03, 2018


## 1.0.43
1. quantaxis 命令行 save 命令错误的 异常处理
2. QA_Risk 插件增加对于assets计算的修改(如果撮合按不复权撮合,risk也按不复权去计算assets)

released in :JUNE 03, 2018
 

## 1.0.42 

1. QDS的DataStruct 删除 __reversed__ 和 reverse方法
2. QDS增加回__reversed__方法 但是会raise NotImplementError,方便在reversed内置方法调用时报错
3. _quotation_base 类中 __add__ __sub__ 的测试代码
4. _quotation_base 类中 __getitem__  类型判断，的测试代码 
5. QAQuery_Advance 中函数获取数据的参数检查
6. QA_DataStruct_Indicators 增加指标类
7. QADATASTRUCT 的selects, select_time,get_bar函数的速度更新
8. QADataStruct_Indicators 指标类的索引速度更新(详见 [QUANTAXIS INDICATOR](https://github.com/QUANTAXIS/QUANTAXIS/blob/master/Documents/indicators.md))

released in :JUNE 01, 2018


## 1.0.41
 1. 增加了财务表的注释和翻译QAData/financial_means.py
 2. @喜欢你 更新了布林带的回测示例
 3. @Roy T.Burns 提交了关于回测Risk插件画图的显示错误
 4. @尧 对于无GUI的机器引入matplotlib做了测试和修改
 5. 增加 QARisk的 benchmark_profit
 6. 新增两个装饰器 关于QUANTAXIS DATAStruct ==> 简称QDS 
    ```
    @QDS_StockDayWarpper 
    @QDS_StockMinWarpper
    ```
 7. 将QDS的方法暴露出来 [concat,from_tushare](https://github.com/QUANTAXIS/QUANTAXIS/blob/master/QUANTAXIS/QAData/dsmethods.py)
    QDS的装饰器主要是用于将别处获取的数据之间转化为QDS格式
    ```
    import QUANTAXIS as QA
    import tushare as ts


    @QA.QDS_StockDayWarpper
    def get_stockday_adv(code,start,end):
        return QA.QA_fetch_get_stock_day('tdx',code,start,end)


    @QA.QDS_StockDayWarpper
    def get_stockday_ts(code,start,end):
        return ts.get_k_data(code,start,end)

    print(get_stockday_adv('000001','2018-05-01','2018-05-21'))
    print(get_stockday_ts('000001','2018-05-01','2018-05-21'))

    ```
 8. @喜欢你 按照test_backtest中的MACD_JCSC.py的写法移植到了unittest来运行，后期加入assert来自动测试
 9. @喜欢你 修改了QA_Risk的显示,以及更新PR说明

released in :May 30, 2018


## 1.0.40

 1. 修改base的函数 AVEDEV 返回SERIES
 2. @宋 @喜欢你 修改kernal,kernal_dict --> kernel, kernel_dict
 3. QA_Performance 增加两个属性 pnl_lifo, pnl_fifo
 4. QA_Performance 增加两个方法 plot_pnlmoney.plot_pnlratio
 5. @尧 在无GUI的电脑上的matplotlib引入时报错的兼容处理
 6. @yehonghao 增加了龙虎榜数据的获取和存储

released in :May 28, 2018

## 1.0.39

 1. 增加seaborn依赖项  需要pip install seaborn 
 2. QA_Risk 增加两个画图方法: plot_dailyhold() plot_signal()

released in :May 24, 2018

## 1.0.38

 1. 修改了COUNT函数,现在返回series格式 在@musicx的[ISSUE 429](https://github.com/QUANTAXIS/QUANTAXIS/issues/429)问题上进行了改进
 2. 修改了PortfolioView类,修改account_cookie为PVIEW_xxx,增加contained_cookie 作为承载的account的cookie集合
 3. @taurusWang 对于QA_fetch_stock_day_adv的异常值进行了修改
 4. @taurusWang 对于代码进行了大量的注释
 5. @royburns 提供了金叉死叉的回测代码 test_backtest/目录下
 6. 修改了QA_RISK 增加了一个画图方法: plot_assets_curve()方法

released in :May 23, 2018

## 1.0.37

 1. @yssource将log文件都放入~/.quantaxis/log目录中 (* 在windows中,users/username/.quantaxis/log),减少log文件的垃圾输出
 2. @cc/pchaos 的建议: 将log的位置放在setting文件中

released in :May 22, 2018

## 1.0.36

 对于策略示例做了一些适当性的调整

released in :May 21, 2018

## 1.0.35

1. 增加 QA_Account 增加一个方法 hold_table(datetime) 方便在复盘的时候查看某一个时间点的账户持仓
2. 增加 QA_Account 增加一个方法 hold_price(datetime) 使用vwap成交量加权算法计算持仓均价
3. 增加 QA_Account 增加一个属性 trade_range 返回账户的交易时间段(所有交易日)
4. 修改 base_datastruct 修改以便兼容多个股票的DataStruct的指标计算

受影响的方法/属性
 - self.max
 - self.min
 - self.mean
 - self.price_diff
 - self.pvariance
 - self.variance
 - self.stdev
 - self.pstdev
 - self.mode
 - self.mean_harmonic
 - self.amplitude
 - self.skew
 - self.kurt
 - self.pct_change
 - self.mad

released in :May 21, 2018

## 1.0.34 

1. 增加: QA_Account增加一个属性 running_time 用于记录该账户的运行时间(会同步到数据库,所以从数据库取出的account也是当时运行的时间)
2. @Roy T.Burns 对于QAUSER的修改 增加了自定义user_cookie的功能
3. @几何提出的对于MONGODB uri以及本地文件设置的问题 
```
1.0.34会在本地创建一个.quantaxis目录,用于存储设置等
同时可以对于.quantaxis/setting/config.ini进行修改,配置默认数据库
```
4. @taurusWang对于QA的整体注释和代码结构做了系统性的优化

released in :May 19, 2018

## 1.0.33

1. 取消初始化quantaxis的时候选择服务器,改成获取事件触发时选取

released in :May 18, 2018

## 1.0.32

1. 对于账户的修改: 增加了QA_Account.orders 作为委托/订单记录器
2. 对于base_datastruct的修改 : 增加了部分代码的注释
3. 对于QA_BACKTESTBROKER做了修改: 增加了对于市价单等的bug修复
4. 对于QA_BACKTESTBROKER做了修改: 修改了BY_MONEY/BY_AMOUNT的成交机制
5. 对于QA_DEALER进行了修改,减少成交回报报文,去除MARKET_DATA部分数据
6. 对于QAUtil.QADate_trade做了修改,更改交易时间为9:15-11:30/1:00-3:00的时间,加入盘前集合竞价的数据
7. 对于QARisk做了修改,更改了最大回撤的计算

感谢@尧 zhongjy1992@outlook.com 对于该版本做出的巨大贡献

released in :May 17, 2018

## 1.0.31

1.0.31 更新了关于DATAStruct的易用性

1. 增加了一个参数 split_dicts, 以KV对形式拆分DataStruct,可以快速寻找个股的DataStruct

```python


[In1]: datafq.split_dicts

{'000014': < QA_DataStruct_Stock_day with 1 securities >,
 '000037': < QA_DataStruct_Stock_day with 1 securities >,
 '000555': < QA_DataStruct_Stock_day with 1 securities >,
 '000670': < QA_DataStruct_Stock_day with 1 securities >,
 '000677': < QA_DataStruct_Stock_day with 1 securities >,
 '000681': < QA_DataStruct_Stock_day with 1 securities >,
 '000687': < QA_DataStruct_Stock_day with 1 securities >,
 '000801': < QA_DataStruct_Stock_day with 1 securities >,
 '000868': < QA_DataStruct_Stock_day with 1 securities >,
 '002068': < QA_DataStruct_Stock_day with 1 securities >,
 '002077': < QA_DataStruct_Stock_day with 1 securities >,
 '002137': < QA_DataStruct_Stock_day with 1 securities >,
 '002203': < QA_DataStruct_Stock_day with 1 securities >,
 '002258': < QA_DataStruct_Stock_day with 1 securities >,
 '002371': < QA_DataStruct_Stock_day with 1 securities >,
 '002376': < QA_DataStruct_Stock_day with 1 securities >}
```

example:

从一个DataStruct包里面快速拿到000014的股票,选择2018-04-01以前15天的数据,计算这部分数据的MACD
```python
R=datafq.split_dicts
R['000014'].select_time_with_gap('2018-04-01',15,'<=').add_func(QA.QA_indicator_MACD,1,2)
```
released in :May 15, 2018

## 1.0.30

1.0.30更新了关于回测和数据查询的代码

1. 修改了查询股票info的代码, 支持多个股票同时查询
2. 修改了QA_Account下单时的```cash_available```结算bug
3. 

released in :May 14, 2018



## 1.0.29

1.0.29更新了关于数据查询的代码

1. 更新了前复权/后复权的计算,保证了即使在复权数据全无的时候,返回正确的复权结果
2. 更新了查询权息数据库的代码,现在支持多个股票同时查询
3. 加速了复权的效率

released in :May 14, 2018


## 1.0.28


ATTENTION CHANGELOG 1.0.28
修改了Account的send_order方法, 区分按数量下单和按金额下单两种方式

- AMOUNT_MODEL.BY_PRICE ==> AMOUNT_MODEL.BY_MONEY # 按金额下单
- AMOUNT_MODEL.BY_AMOUNT # 按数量下单

在按金额下单的时候,应给予 money参数
在按数量下单的时候,应给予 amount参数


```python
Account=QA.QA_Account()

Order_bymoney=Account.send_order(code='000001',
                                price=11,
                                money=0.3*Account.cash_available,
                                time='2018-05-09',
                                towards=QA.ORDER_DIRECTION.BUY,
                                order_model=QA.ORDER_MODEL.MARKET,
                                amount_model=QA.AMOUNT_MODEL.BY_MONEY
                                 )

Order_byamount=Account.send_order(code='000001',
                                price=11,
                                amount=100,
                                time='2018-05-09',
                                towards=QA.ORDER_DIRECTION.BUY,
                                order_model=QA.ORDER_MODEL.MARKET,
                                amount_model=QA.AMOUNT_MODEL.BY_AMOUNT
                                 )
```

released in :May 10, 2018

## 1.0.27

2018-05-06

修改并增加了大量的公式,统一成dataframe格式返回,可以被直接concat合并出来

预计将对于indicator类进行重写并缓存/本地存储,方便快速调用

修改了QA_Account的下单模式, 修复了下单的判断bug

released in :May 10, 2018

## 1.0.26

2018-05-02

1.0.26 对于回测进行了一些优化

1. 增加了对于RISK类的参数

- 增加了```init_assets```, ```last_assets```参数,更方便查看

- 修改了计算年化收益率的公式

2. 修改了simple_backtest 函数的逻辑:

- simple_backtest 之前的 重设账户资金的写法错误, 已更正

- simple_backtest 现在会随机下单(增加随机函数)


3. 修改了```QADATASTRUCT```中日线结构的参数

增加了 ```next_day_high_limit``` 和 ```next_day_low_limit```参数,方便计算,明日涨跌停

released in :May 02, 2018


## 1.0.25 

2018-04-27

1.0.25 增加对于查询的优化:

1. 优化了查询时输入的参数

当code的列表如果是[000001,000002... ]等int形式时,会出现不支持的错误,1.0.25进行了优化

常见原因是如果code从csv中取出,csv会自动讲code变成整数的形式,如果在传参之前没有进行 ```code.apply(str).tolist()```的话,会出现此错误

2. 优化了查询的返回

在偶见的数据库数据重复时,会对数据自动去重并返回结果

released in :Apr 27, 2018


