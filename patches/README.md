# patches/ — 对 UniAD eval 的改动

`0001-uniad-eval-per-frame-planning-export.patch`：在官方 eval 里加**逐帧 planning 指标导出**，
**非破坏性**（不改变任何全局指标数值）。改动 3 个文件：

- `projects/mmdet3d_plugin/uniad/dense_heads/planning_head_plugin/planning_metrics.py`
  新增 `compute_per_frame()`：复用官方 L2/碰撞数学，内部 `clone()` 后计算，不碰累加器、不污染输入张量。
- `projects/mmdet3d_plugin/uniad/apis/test.py`
  eval 主循环逐帧收集 `{token, command, L2_*, col_*, col_any}`，结尾写 CSV。
- `tools/test.py`
  新增 `--planning-csv` 参数（默认落到 `--out` 同目录）。

## 应用

```bash
cd /path/to/UniAD
git apply patches/0001-uniad-eval-per-frame-planning-export.patch
```

或直接看我的 fork 分支：
**https://github.com/SailorChiu/UniAD/tree/stage1-eval-export**

## 非破坏性验证

逐帧 CSV 各 `L2_*` 列的均值，**逐位等于**官方全局 PrettyTable 打印的 L2 行
（`0.1718 / 0.3094 / 0.5475 / 0.8964 / 1.3638 / 1.9029`）——
证明逐帧计算与官方累加路径数学一致、未改动全局指标。
