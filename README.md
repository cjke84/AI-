# BTC/USDT OKX自动交易机器人

## 项目概述

这是一个基于Python开发的BTC/USDT永续合约自动化交易机器人，集成了技术分析、市场情绪指标和AI辅助决策功能，可在OKX交易所进行实盘或模拟交易。
根据布欧的量化日记的修改 原地址https://github.com/huojichuanqi/ds

## 功能特性

- **多维度数据分析**：结合技术指标、市场情绪和K线形态进行综合分析
- **智能仓位管理**：根据信号信心度和市场趋势动态调整仓位大小
- **AI辅助决策**：利用DeepSeek AI模型进行深度市场分析和交易信号生成
- **风险控制机制**：内置止损止盈、仓位限制和趋势跟踪功能
- **灵活部署**：支持实盘交易和测试模式

## 环境依赖

```bash
pip install openai ccxt pandas python-dotenv schedule requests
```

## 配置说明

### 环境变量配置

在项目根目录创建 `.env` 文件并配置以下参数：

```env
DEEPSEEK_API_KEY=your_deepseek_api_key
OKX_API_KEY=your_okx_api_key
OKX_SECRET=your_okx_secret
OKX_PASSWORD=your_okx_password
```

### 核心交易参数

在 `TRADE_CONFIG` 中可配置的关键参数：

- `symbol`: 交易对 (默认 `BTC/USDT:USDT`)
- `leverage`: 杠杆倍数 (默认 10)
- `timeframe`: K线周期 (默认 `15m`)
- `test_mode`: 测试模式开关 (默认 `False`)
- `position_management`: 智能仓位管理配置
- `hold_signal_config`: HOLD信号减仓功能配置

## 运行流程

1. **初始化阶段**
   - 加载环境变量和API密钥
   - 配置OKX交易所参数（全仓模式、单向持仓）
   - 设置杠杆和验证账户状态

2. **定时执行循环**
   - 每15分钟整点执行一次交易决策
   - 获取最新的BTC/USDT K线数据
   - 计算技术指标（MA、RSI、MACD、布林带等）
   - 获取市场情绪数据
   - 调用DeepSeek AI生成交易信号
   - 根据信号执行智能交易

3. **智能交易逻辑**
   - 根据信号信心度动态调整仓位
   - 支持同方向加减仓操作
   - HOLD信号时按配置减仓
   - 严格的风控和异常处理机制

## 使用方法

1. 创建 `.env` 文件并填入相应的API密钥
2. 安装所需依赖包
3. 运行脚本：`python ds_ox.py`
4. 程序将首次立即执行交易，之后每15分钟整点自动执行

## 注意事项

- 确保OKX账户有足够的USDT余额
- 初始运行前需手动清除所有逐仓持仓
- 建议先开启测试模式验证功能
- 程序会自动处理交易所设置和仓位管理
- 持续监控日志输出以了解交易状态

## 核心模块说明

### 交易所设置模块
- `setup_exchange()`: 初始化OKX交易所，设置全仓模式和杠杆

### 数据获取模块
- `get_btc_ohlcv_enhanced()`: 获取BTC K线数据并计算技术指标
- `calculate_technical_indicators()`: 计算各类技术指标
- `get_sentiment_indicators()`: 获取市场情绪指标

### AI分析模块
- `analyze_with_deepseek()`: 使用DeepSeek分析市场并生成交易信号
- `generate_technical_analysis_text()`: 生成技术分析文本

### 交易执行模块
- `execute_intelligent_trade()`: 执行智能交易
- `calculate_intelligent_position()`: 计算智能仓位大小
- `execute_buy_signal()` / `execute_sell_signal()`: 执行BUY/SELL信号

### 风控管理模块
- `get_current_position()`: 获取当前持仓情况
- `wait_for_next_period()`: 等待到下一个整点执行时间

## 配置详解

### 智能仓位管理配置

```python
'position_management': {
    'enable_intelligent_position': False,  # 是否启用智能仓位管理
    'base_usdt_amount': 500,  # USDT投入下单基数
    'high_confidence_multiplier': 1.5,  # 高信心信号倍数
    'medium_confidence_multiplier': 1.0,  # 中等信心信号倍数
    'low_confidence_multiplier': 0.5,  # 低信心信号倍数
    'max_position_ratio': 50,  # 单次最大仓位比例
    'trend_strength_multiplier': 1.2,  # 趋势强度倍数
    # 新增市场因子参数
    'volatility_multiplier': {  # 波动率调整因子
        'low': 1.1,    # 低波动率时增加仓位
        'medium': 1.0, # 中等波动率时正常仓位
        'high': 0.8    # 高波动率时减少仓位
    },
    'volume_multiplier': {  # 成交量调整因子
        'low': 0.8,    # 低成交量时减少仓位
        'medium': 1.0, # 中等成交量时正常仓位
        'high': 1.2    # 高成交量时增加仓位
    },
    # 仓位衰减因子（连续同向交易时减少仓位）
    'position_decay_factor': 0.9,
    # 最小仓位（即使在最低配置下也不低于此值）
    'min_position': 0.01
}
```

### HOLD信号减仓功能配置

```python
'hold_signal_config': {
    'enable_hold_reduce': True,  # 是否启用HOLD信号减仓功能
    'reduce_on_nth_hold': 3,  # 第几次出现HOLD信号时减仓
    'reduce_ratio': 0.5  # 减仓比例（0.5表示减半，0.3表示减30%等）
}
```

## 日志说明

程序会同时输出日志到控制台和文件(`trading_bot.log`)，日志级别包括：
- INFO: 一般信息
- WARNING: 警告信息
- ERROR: 错误信息

通过日志可以追踪程序运行状态、交易决策过程和异常情况。
