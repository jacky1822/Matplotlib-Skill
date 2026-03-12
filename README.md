# matplotlib-cjk-font

讓 Claude 在產生 Python 繪圖程式碼時，自動加入繁體中文字型設定，確保標題、軸標籤、圖例正確顯示，不出現方塊字（□）或亂碼。

---

## 支援的繪圖套件

| 套件 | 支援狀況 |
|------|----------|
| `matplotlib` | ✅ 完整支援 |
| `seaborn` | ✅ 完整支援 |
| `pandas` plot | ✅ 完整支援 |
| `plotly` 靜態輸出 | ✅ 支援 |

---

## 跨平台字型對照

| 作業系統 | 主要字型 | 備用字型 |
|----------|----------|----------|
| Windows | 微軟正黑體 `msjh.ttc` | 細明體 `mingliu.ttc` |
| macOS | STHeiti Light | Arial Unicode MS |
| Linux | Noto Sans CJK TC | WenQuanYi Zen Hei |

---

## 安裝方式

1. 下載 [`matplotlib-cjk-font.skill`](https://github.com/your-username/matplotlib-cjk-font/releases/latest/download/matplotlib-cjk-font.skill)
2. 前往 [Claude.ai](https://claude.ai) → 設定 → Skills
3. 點「安裝 Skill」，上傳 `.skill` 檔案
4. 完成！之後 Claude 產生繪圖程式碼時會自動套用

---

## 使用方式

安裝後，只要對 Claude 說：

> 「幫我畫一個每月銷售額的長條圖」

Claude 就會自動在程式碼頂部加入以下字型設定區塊：

```python
import matplotlib.pyplot as plt
import matplotlib.font_manager as fm
import matplotlib as mpl
import platform, os

def _setup_cjk_font():
    system = platform.system()
    candidates = {
        "Windows": ["C:/Windows/Fonts/msjh.ttc", "C:/Windows/Fonts/msjhbd.ttc", "C:/Windows/Fonts/mingliu.ttc"],
        "Darwin":  ["/System/Library/Fonts/STHeiti Light.ttc", "/System/Library/Fonts/STHeiti Medium.ttc", "/Library/Fonts/Arial Unicode MS.ttf"],
        "Linux":   ["/usr/share/fonts/truetype/noto/NotoSansCJK-Regular.ttc", "/usr/share/fonts/opentype/noto/NotoSansCJK-Regular.ttc", "/usr/share/fonts/truetype/wqy/wqy-zenhei.ttc"],
    }
    font_name = None
    for path in candidates.get(system, []):
        if os.path.exists(path):
            fm.fontManager.addfont(path)
            font_name = fm.FontProperties(fname=path).get_name()
            break
    if not font_name:
        installed = {f.name for f in fm.fontManager.ttflist}
        for name in ["Microsoft JhengHei", "PingFang TC", "Noto Sans CJK TC", "WenQuanYi Zen Hei"]:
            if name in installed:
                font_name = name
                break
    if font_name:
        mpl.rcParams["font.sans-serif"] = [font_name, "DejaVu Sans"]
        mpl.rcParams["font.family"] = "sans-serif"
    else:
        print("Warning: CJK font not found. Install from https://fonts.google.com/noto/specimen/Noto+Sans+TC")
    mpl.rcParams["axes.unicode_minus"] = False

_setup_cjk_font()
```

### seaborn 使用範例

```python
import seaborn as sns
_setup_cjk_font()           # 在 set_theme() 之前呼叫一次即可
sns.set_theme(style="whitegrid")
```

---

## Linux 找不到字型？

若出現 `Warning: CJK font not found`，請安裝 Noto CJK 字型：

```bash
# Ubuntu / Debian
sudo apt install fonts-noto-cjk

# Fedora / RHEL
sudo dnf install google-noto-sans-cjk-fonts
```

---

## 授權

MIT
