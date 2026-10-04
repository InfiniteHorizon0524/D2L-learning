本项目是我基于[《动手学深度学习》](https://zh.d2l.ai/)一书所做的实验代码合集，所用 `python` 版本是 $3.9$，张量积算采用 `torch`。

## 环境配置

> 以下命令在 Windows + Anaconda 下实测通过：Python 3.9.23 / PyTorch 1.12.0+cpu / d2l 0.17.6。
> 全程约 5～8 分钟（取决于网速），其中 torch 的 wheel 约 155 MB。

### 1. 创建并激活虚拟环境

```bash
conda create -n d2l python=3.9 pip -y --override-channels \
    -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda activate d2l
```

说明：

- `--override-channels -c <清华镜像>` 用于绕开默认源 `repo.anaconda.com`（国内常连不上）；如果你的网络能直连官方源，也可直接写 `conda create -n d2l python=3.9 pip -y`。
- 必须用 Python 3.9（3.10 亦可）：d2l 0.17.6 钉住 `numpy==1.21.5`、`pandas==1.2.4`，3.11 及以上没有对应的预编译 wheel。

### 2. 安装 PyTorch（CPU 版）

```bash
python -m pip install torch==1.12.0 torchvision==0.13.0
```

### 3. 安装 d2l，并锁死 matplotlib-inline

```bash
python -m pip install d2l==0.17.6 matplotlib-inline==0.1.7
```

> ⚠️ `matplotlib-inline` 必须锁在 0.1.x（本例 0.1.7）。
> d2l 0.17.6 钉死 `matplotlib==3.5.1`，而 matplotlib-inline ≥ 0.2 会调用只有新版 matplotlib 才有的 `rcParams._get()`，导致 `from d2l import torch as d2l` 直接抛出 `AttributeError: 'RcParams' object has no attribute '_get'`。
> 由于该包的元数据里没有声明 matplotlib 的版本依赖，pip 不会自动拦截，因此**不要在本环境中执行 `pip install -U ipykernel` / `-U ipython`**，否则会把 matplotlib-inline 重新升回 0.2.x。

### 4. 自检并运行

```bash
python -m pip check
# 期望输出：No broken requirements found.

python -c "import torch, d2l; print(torch.__version__, d2l.__version__)"
# 期望输出：1.12.0+cpu 0.17.6
```

自检通过后即可运行本章代码。若该节提供 `.py` 版本，直接执行：

```bash
python 3.2-LinearRegression.py
# features: tensor([-0.9139, -0.5656])
# label: tensor([4.2996])
```

若该节是 notebook，则逐格运行对应的 `.ipynb`（见下一步）。

### 5. 在 notebook 中使用

用 VS Code 打开 `3.2-LinearRegression.ipynb`，右上角内核选择 `d2l` 环境即可。
若改用浏览器版 Jupyter，请先在 `d2l` 环境内启动，这样 notebook 里的 `python3` 内核会解析到本环境：

```bash
conda activate d2l
jupyter notebook
```

若希望内核列表里直接出现名为 `d2l` 的条目，可执行：

```bash
conda activate d2l
python -m ipykernel install --user --name d2l --display-name "d2l"
```

### 备选：用 `requirements.txt` 一键安装

不想手动逐个安装，也可以在激活环境后直接：

```bash
python -m pip install -r requirements.txt
```

- `requirements.txt` 由 `pip freeze` 生成，是第 2、3 步的等价替代，其中已包含关键版本 `d2l==0.17.6`、`matplotlib==3.5.1`、`matplotlib-inline==0.1.7`、`torch==1.12.0`，因此不会踩到下面的 `_get` 报错。
- 实测：全新 Python 3.9 环境下 3.6 分钟装完，`pip check` 无冲突，`import torch, d2l` 正常。
- ⚠️ 这是 **Windows + Python 3.9** 的快照，包含 `pywin32`、`pywinpty`、`colorama` 等平台相关包，在 Linux / macOS 上会安装失败。

## 常见报错

**`AttributeError: 'RcParams' object has no attribute '_get'`**

```
File "...\site-packages\d2l\torch.py", line 36, in <module>
    from matplotlib_inline import backend_inline
File "...\site-packages\matplotlib_inline\backend_inline.py", line 218, in _enable_matplotlib_integration
    backend = matplotlib.rcParams._get("backend")
AttributeError: 'RcParams' object has no attribute '_get'
```

原因是 `matplotlib-inline` 被升到了 0.2.x，与本环境的 `matplotlib 3.5.1` 不兼容（注意：即便脚本本身不画图，`import d2l` 时也会触发）。修复：

```bash
python -m pip install "matplotlib-inline==0.1.7"
```

## 附：实测版本

| 组件 | 版本 |
| --- | --- |
| Python | 3.9.23 |
| torch / torchvision | 1.12.0+cpu / 0.13.0 |
| d2l | 0.17.6 |
| numpy / pandas | 1.21.5 / 1.2.4 |
| matplotlib / matplotlib-inline | 3.5.1 / 0.1.7 |
| jupyter / ipykernel | 1.0.0 / 6.31.0 |
