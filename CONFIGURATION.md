[CONFIGURATION.md](https://github.com/user-attachments/files/23291555/CONFIGURATION.md)
# 交易机器人配置说明

本文档详细解释了 `config.json` 配置文件中各个参数的含义和作用。

## 基础交易配置

```json
{
  "trading": {
    "symbol": "BTC/USDT:USDT",
    "leverage": 20,
    "timeframe": "15m",
    "test_mode": false,
    "use_demo_trading": true
  }
}
```

- `symbol`: 交易对符号，OKX合约格式
- `leverage`: 杠杆倍数，只影响保证金不影响下单价值
- `timeframe`: K线周期，使用15分钟K线
- `test_mode`: 测试模式，设置为 true 时不会真实下单
- `use_demo_trading`: 是否使用模拟交易模式

## 数据配置

```json
{
  "trading": {
    "data": {
      "data_points": 64
    }
  }
}
```

- `data_points`: 获取的历史数据点数，64根15分钟K线等于16小时数据

## 风险管理配置

```json
{
  "trading": {
    "risk_management": {
      "risk_per_trade": 0.1,
      "max_portfolio_risk": 0.5,
      "min_position_size": 0.01,
      "max_position_size": 1.0,
      "position_sizing_mode": "fixed",
      "max_position_hold_bars": 2,
      "trailing_stop_ratio": 0.003,
      "min_confidence": "LOW"
    }
  }
}
```

- `risk_per_trade`: 每笔交易风险占总资金比例
- `max_portfolio_risk`: 投资组合最大风险比例
- `min_position_size`: 最小仓位大小
- `max_position_size`: 最大仓位大小
- `position_sizing_mode`: 仓位计算模式
- `max_position_hold_bars`: 最大持仓K线数
- `trailing_stop_ratio`: 移动止损比例
- `min_confidence`: 最低信号信心度

## 智能仓位管理配置

```json
"position_management": {
  "enable_intelligent_position": true,
  "base_usdt_amount": 100,
  "fixed_position_size": 3.0,
  "default_account_balance": 1000,
  "high_confidence_multiplier": 1.2,
  "medium_confidence_multiplier": 0.8,
  "low_confidence_multiplier": 0.4,
  "max_position_ratio": 40,
  "trend_strength_multiplier": 1.1,
  "volatility_multiplier": {
    "low": 1.0,
    "medium": 0.9,
    "high": 0.6
  },
  "volume_multiplier": {
    "low": 0.7,
    "medium": 1.0,
    "high": 1.1
  },
  "position_decay_factor": 0.85,
  "min_position": 0.01,
  "max_balance_adjustments": 5,
  "min_position_ratio": 0.1
}
```

- `enable_intelligent_position`: 是否启用智能仓位管理
- `base_usdt_amount`: USDT投入下单基数
- `fixed_position_size`: 固定仓位大小（当智能仓位管理禁用时使用）
- `default_account_balance`: 当无法获取账户余额时的默认值
- `high_confidence_multiplier`: 高信心信号时的仓位倍数
- `medium_confidence_multiplier`: 中等信心信号时的仓位倍数
- `low_confidence_multiplier`: 低信心信号时的仓位倍数
- `max_position_ratio`: 单次最大仓位比例（占总资金的百分比）
- `trend_strength_multiplier`: 趋势较强时的仓位倍数

### 波动率调整因子
- `volatility_multiplier.low`: 低波动率时仓位倍数
- `volatility_multiplier.medium`: 中等波动率时仓位倍数
- `volatility_multiplier.high`: 高波动率时仓位倍数

### 成交量调整因子
- `volume_multiplier.low`: 低成交量时仓位倍数
- `volume_multiplier.medium`: 中等成交量时仓位倍数
- `volume_multiplier.high`: 高成交量时仓位倍数

- `position_decay_factor`: 连续同向交易时的仓位衰减系数
- `min_position`: 最小仓位数量（张）
- `max_balance_adjustments`: 最大资金调整次数
- `min_position_ratio`: 最小仓位比例

## HOLD信号减仓功能配置

```json
"hold_signal_config": {
  "enable_hold_reduce": true,
  "enable_dynamic_reduction": true,
  "min_hold_count": 1,
  "reduction_thresholds": {
    "high": 60,
    "medium": 40,
    "low": 25
  },
  "reduction_ratios": {
    "high": 0.7,
    "medium": 0.5,
    "low": 0.3
  },
  "reduction_weights": {
    "volatility": 25,
    "rsi": 30,
    "volume": 20,
    "pnl": 15,
    "trend": 5,
    "hold_count": 25
  },
  "condition_thresholds": {
    "bb_extreme": 0.75,
    "rsi_extreme": 70,
    "volume_low": 0.8,
    "pnl_threshold": 0.02
  }
}
```

- `enable_hold_reduce`: 是否启用HOLD信号减仓功能
- `enable_dynamic_reduction`: 是否启用动态减仓
- `min_hold_count`: 触发减仓的最小HOLD信号次数
- `reduction_thresholds`: 减仓评分阈值（高/中/低）
- `reduction_ratios`: 减仓比例（高/中/低）
- `reduction_weights`: 各因素权重分配
  - `volatility`: 波动率因素权重
  - `rsi`: RSI因素权重
  - `volume`: 成交量因素权重
  - `pnl`: 盈亏因素权重
  - `trend`: 趋势因素权重
  - `hold_count`: HOLD次数因素权重
- `condition_thresholds`: 各条件阈值
  - `bb_extreme`: 布林带极端位置阈值
  - `rsi_extreme`: RSI极端区域阈值
  - `volume_low`: 成交量萎缩阈值
  - `pnl_threshold`: 盈亏阈值

## 止盈止损配置

```json
"stop_loss_config": {
  "high_confidence_multiplier": 0.985,
  "medium_confidence_multiplier": 0.98,
  "low_confidence_multiplier": 0.975,
  "enable_stop_loss": false
},
"take_profit_config": {
  "high_confidence_multiplier": 1.015,
  "medium_confidence_multiplier": 1.02,
  "low_confidence_multiplier": 1.025,
  "enable_take_profit": false
}
```

> ⚠️ **注意：止盈止损功能默认为关闭状态**，如需启用请将对应的 `enable_stop_loss` 或 `enable_take_profit` 设置为 `true`

### 止损配置
- `high_confidence_multiplier`: 高信心信号止损倍数（0.985表示1.5%的止损空间）
- `medium_confidence_multiplier`: 中等信心信号止损倍数（0.98表示2%的止损空间）
- `low_confidence_multiplier`: 低信心信号止损倍数（0.975表示2.5%的止损空间）
- `enable_stop_loss`: 是否启用止损功能（true表示启用，false表示禁用）

### 止盈配置
- `high_confidence_multiplier`: 高信心信号止盈倍数（1.015表示1.5%的止盈空间）
- `medium_confidence_multiplier`: 中等信心信号止盈倍数（1.02表示2%的止盈空间）
- `low_confidence_multiplier`: 低信心信号止盈倍数（1.025表示2.5%的止盈空间）
- `enable_take_profit`: 是否启用止盈功能（true表示启用，false表示禁用）

## 短线交易配置

```json
"short_term_config": {
  "max_hold_time": 3600,
  "quick_stop_loss_ratio": 0.03,
  "min_trade_interval": 300
}
```

- `max_hold_time`: 最大持仓时间（秒）
- `quick_stop_loss_ratio`: 快速止损比例
- `min_trade_interval`: 最小交易间隔（秒）

## 技术分析配置

```json
"technical_analysis": {
  "indicators": {
    "sma_periods": [3, 5, 8, 20, 50],
    "ema_periods": [3, 5, 8],
    "rsi_period": 5,
    "macd_fast": 5,
    "macd_slow": 10,
    "macd_signal": 3,
    "bollinger_bands_period": 10,
    "bollinger_bands_std": 1.5,
    "stoch_rsi_period": 5,
    "atr_period": 5
  },
  "thresholds": {
    "rsi_overbought": 70,
    "rsi_oversold": 30,
    "stoch_rsi_overbought": 0.7,
    "stoch_rsi_oversold": 0.3,
    "volume_above_average": 1.1
  }
}
```

### 指标周期配置
- `sma_periods`: SMA周期设置
- `ema_periods`: EMA周期设置
- `rsi_period`: RSI计算周期
- `macd_fast`: MACD快线周期
- `macd_slow`: MACD慢线周期
- `macd_signal`: MACD信号线周期
- `bollinger_bands_period`: 布林带计算周期
- `bollinger_bands_std`: 布林带标准差倍数
- `stoch_rsi_period`: 随机RSI计算周期
- `atr_period`: ATR计算周期

### 阈值配置
- `rsi_overbought`: RSI超买阈值
- `rsi_oversold`: RSI超卖阈值
- `stoch_rsi_overbought`: 随机RSI超买阈值
- `stoch_rsi_oversold`: 随机RSI超卖阈值
- `volume_above_average`: 成交量高于平均值的倍数阈值

## 重试配置

```json
"retry_config": {
  "max_retries": 3,
  "retry_delay": 5
}
```

- `max_retries`: 最大重试次数
- `retry_delay`: 重试间隔（秒）
