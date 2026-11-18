# 自动化脚本 实践笔记

> 能被脚本化的重复劳动，就不该每天手工点一遍。

## 为什么值得关注

最近在整理自动化脚本相关的资料，把零散的笔记重新梳理了一遍，顺手记下几个容易被忽略的点。

## 核心要点

- 把配置从代码里抽出来，路径、账号、阈值都能外部调整。
- 脚本要可重复执行，失败后重跑不能产生脏数据。
- 关键步骤打印进度和结果，方便中途排查。
- 加超时和重试，但重试要有上限和退避。

## 具体做法

下面用一个最小示例把流程串起来，实际项目里按需扩展即可：

```python
import time
from functools import wraps

def retry(times: int = 3, delay: float = 1.0):
    def deco(fn):
        @wraps(fn)
        def wrapper(*args, **kwargs):
            for attempt in range(1, times + 1):
                try:
                    return fn(*args, **kwargs)
                except Exception:
                    if attempt == times:
                        raise
                    time.sleep(delay * attempt)
        return wrapper
    return deco
```

示例只保留了主流程，真实场景里的日志、超时和重试同样重要。

## 注意事项

- 定时任务要有并发保护，避免上一轮还没跑完就叠上来。
- 把执行结果落盘，出问题时有据可查。

## 小结

自动化脚本没有银弹，关键是把取舍想清楚，然后保持记录和复盘。

---
*2026年11月18日*
