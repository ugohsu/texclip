# texsvg / texweb

LaTeX 数式・TikZ 図を SVG に変換してクリップボードにコピーするツール。  
CLI (`texsvg`) とブラウザ GUI (`texweb`) の 2 つのコンポーネントで構成される。

---

## 依存ツール

| ツール | 用途 | インストール例 |
|--------|------|---------------|
| `lualatex` | タイプセット（lualatex モード） | TeX Live に同梱 |
| `ptex2pdf` | タイプセット（uplatex モード） | TeX Live に同梱 |
| `pdftocairo` | PDF → SVG / PNG 変換 | `apt install poppler-utils` |
| `xclip` | クリップボードへのコピー | `apt install xclip` |
| Python 3.8 以上 | スクリプト実行 | |

---

## インストール

`scripts/` ディレクトリを `$PATH` に追加する。

```bash
# ~/.bashrc または ~/.zshrc に追記
export PATH="$PATH:/path/to/project/scripts"
```

追記後、シェルを再起動するか `source ~/.bashrc` を実行する。

---

## texsvg

`.tex` ファイルをタイプセットし、SVG をクリップボードにコピーする。

### 使い方

```
texsvg (--math | --body | --full) [OPTIONS] FILE
```

### 入力モード（いずれか 1 つ必須）

| オプション | 説明 |
|-----------|------|
| `--math`, `-m` | 数式本文のみ。`$\displaystyle ... $` とプリアンブルを自動補完 |
| `--body`, `-b` | `document` 環境の本文のみ。プリアンブルと `\begin{document}` ... を自動補完 |
| `--full`, `-f` | 完全な `.tex` ファイル。補完なし |

### オプション

| オプション | デフォルト | 説明 |
|-----------|-----------|------|
| `--engine ENGINE`, `-e ENGINE` | `lualatex` | エンジン: `lualatex` または `uplatex` |
| `--no-copy` | — | クリップボードへのコピーを行わない |
| `--out DIR` | — | SVG をディレクトリに保存する |

### 例

**数式（lualatex）**

```bash
# formula.tex の中身:
# \int_0^\infty e^{-x^2} dx = \frac{\sqrt{\pi}}{2}

texsvg --math formula.tex
```

**複数行数式・TikZ（完全な .tex）**

```tex
% figure.tex
\documentclass[varwidth, crop]{standalone}
\usepackage{amsmath, amssymb, amsfonts, newtxtext, newtxmath}
\usepackage{luatexja}
\begin{document}
  \[
    \begin{aligned}
      &\mathit{lnBAS}_{i,-2} = \\
      &\beta_0 + \mathit{Others}
    \end{aligned}
  \]
\end{document}
```

```bash
texsvg --full figure.tex
```

**uplatex でタイプセットして SVG を保存**

```bash
texsvg --math -e uplatex --out ./out formula.tex
```

---

## texweb

ブラウザで操作できるローカル Web アプリを起動する。

### 使い方

```bash
texweb            # http://localhost:8765 を自動でブラウザ表示
texweb --port 9000
```

`Ctrl+C` で停止。セッション中の一時ファイルは停止時に自動削除される。

### 操作方法

1. **+ New** ボタンでエントリを追加する。
2. 左パネルの textarea に LaTeX を入力する。
3. モードとエンジンをセレクタで選択する。
4. **Typeset ▶** を押す（または **Ctrl+Enter**）。
5. 右パネルにプレビューが表示される。
6. ボタンで出力を取得する。

| ボタン | 動作 |
|--------|------|
| **SVG コピー** | SVG をクリップボードにコピー（Inkscape 向け） |
| **PNG コピー（高解像度）** | 300 dpi PNG をクリップボードにコピー（Word・Google Docs 向け） |
| **PDF ↓** | PDF をダウンロード |
| **SVG ↓** | SVG をダウンロード |

複数のエントリを左パネルのリストで管理でき、クリックで切り替えられる。  
エントリはセッション内にのみ保持される（ページリロードで消える）。

---

## 既存コマンドとの関係

`copyFigure/` 内の `cf` / `mathpdf` コマンドは変更していない。  
`texsvg` / `texweb` はこれらとは独立して動作する。
