# TeX で部誌をかく人へ

## はじめに

TeX とは、組版処理を行うための古くからあるソフトウェアです。TeX を使うことで、美しいレイアウトの文書を作成することができます。TeX は、LaTeX というマクロパッケージを使うことで、より簡単に使うことができます。

## 環境構築

### macOS

macOS では、Homebrew を使って TeX をインストールすることができます。Homebrew をインストールした後、以下のコマンドを実行してください。

```
brew install mactex-no-gui --cask
```

### Windows

Windows では、TeX Live をインストールすることで TeX を使うことができます。TeX Live は、[ここ](http://www.tug.org/texlive/acquire-netinstall.html)から`install-tl-windows.exe`をダウンロードして実行してください。

## `latexmk`の設定

まずは `latexmk` の設定です。

### What is _latexmk_ ?

LaTeX のソースを PDF にビルドする時に、

- 毎回コマンドを打つのを自動化
- ファイルが変わったら自動で PDF を更

処理をやってくれるツールが _latexmk_ です。
基本的な使い方は `latexmk ファイル名.tex` です。

### .latexmkrc の編集

`latexmk` ではビルド方法などの設定を _~/.latexmkrc_ （Windows では `%USERPROFILE%\.latexmkrc` ）に書くのが標準的です。

以下は私の `~/.latexmkrc` の内容です。

```perl:~/.latexmkrc
#!/usr/bin/env perl

# LaTeX
$latex = 'platex -synctex=1 -halt-on-error -file-line-error %O %S';
$max_repeat = 5;

# BibTeX
$bibtex = 'pbibtex %O %S';
$biber = 'biber --bblencoding=utf8 -u -U --output_safechars %O %S';

# index
$makeindex = 'mendex %O -o %D %S';

# DVI / PDF
$dvipdf = 'dvipdfmx %O -o %D %S';
$pdf_mode = 3;

# preview
$pvc_view_file_via_temporary = 0;
if ($^O eq 'linux') {
    $dvi_previewer = "xdg-open %S";
    $pdf_previewer = "xdg-open %S";
} elsif ($^O eq 'darwin') {
    $dvi_previewer = "open %S";
    $pdf_previewer = "open %S";
} else {
    $dvi_previewer = "start %S";
    $pdf_previewer = "start %S";
}

# ourdir

$out_dir = 'generated';

# clean up
$clean_full_ext = "%R.synctex.gz"
```

## VSCode の設定

## 拡張機能 _LaTeX Workshop_ のインストール

**LaTeX Workshop** は VSCode で TeX ソースを書くに必須と言っても過言ではない拡張機能です。シンタックスハイライトや補完、PDF のプレビューなど様々な機能を提供してくれます。

VSCode 左側の拡張機能パネルを開いて `latex` と入力し、 一番上の **LaTeX Workshop** をインストールしてください。

![latex_workshop.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/320683/e074540a-fdbe-4b1b-516a-89327ae3eced.jpeg)

## `settings.json` の編集

VSCode では設定を `settings.json` に書きます。VSCode 内の左下の歯車マークから **設定** を選択し、タブ右上のファイルマークを押すと JSON が開きます。

![select_settings.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/320683/50fae1a4-72bd-e06c-011a-1c1f0b80ddac.jpeg)

![settings_ui.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/320683/5299ffa2-aeda-f385-a0c7-b62a0d720666.jpeg)

以下は私の _settings.json_ です。

```javascript
// ---------- LaTeX Workshop ----------

  // 使用パッケージのコマンドや環境の補完を有効にする
  "latex-workshop.intellisense.package.enabled": true,

  // 生成ファイルを削除するときに対象とするファイル
  // デフォルト値に "*.synctex.gz" を追加
  "latex-workshop.latex.clean.fileTypes": [
    "*.aux",
    "*.bbl",
    "*.blg",
    "*.idx",
    "*.ind",
    "*.lof",
    "*.lot",
    "*.out",
    "*.toc",
    "*.acn",
    "*.acr",
    "*.alg",
    "*.glg",
    "*.glo",
    "*.gls",
    "*.ist",
    "*.fls",
    "*.log",
    "*.fdb_latexmk",
    "*.snm",
    "*.nav",
    "*.dvi",
    "*.synctex.gz"
  ],

  // 生成ファイルを "generated" ディレクトリに吐き出す
  "latex-workshop.latex.outDir": "generated",

  // ビルドのレシピ
  "latex-workshop.latex.recipes": [
    {
      "name": "latexmk",
      "tools": ["latexmk"]
    }
  ],

  // ビルドのレシピに使われるパーツ
  "latex-workshop.latex.tools": [
    {
      "name": "latexmk",
      "command": "latexmk",
      "args": ["-silent", "-outdir=%OUTDIR%", "%DOC%"],
      "env": {
        "path": "/Library/TeX/texbin/" //macOSの場合
      }
    }
  ],
```

- `"editor.suggest.snippetsPreventQuickSuggestions": false` や `"editor.tabSize": 2` は言語ごとの設定にしています。もし、全ての言語に適用したい場合は、`"[tex]": {}` などでラップせず、グローバルに直接書いてください。

### ビルドとプレビュー

`ctrl + alt + b` （ Mac では `command(⌘) + option(⌥) + b` ） で TeX ソースのビルドが出来ます。一度ビルドすると保存するたびに自動でビルドしてくれます。また、 `ctrl + alt + v` （ `command(⌘) + option(⌥) + v` ） で横にプレビューが開きます。

これらはコマンドパレットや TeX パネルの中からも実行できます。

![build_and_preview.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/320683/2742843c-8fab-de20-22be-6445aff27608.gif)

### PDF から TeX ソースにジャンプ

PDF ビューワー上での `ctrl + クリック` （ `command(⌘) + クリック` ） で対応する TeX ソース箇所にジャンプできます。

![jump.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/320683/e3c8e8c7-c7fa-d016-f397-eed240ba3020.gif)

### まだまだ機能はたくさん

LaTeX Workshop は他にもたくさん機能を持っています。例えば、

- `ctrl + alt + c` （ `command(⌘) + option(⌥) + c` ）で生成ファイル（ `.aux` など）を削除
- 多種多様なスニペット
  - `\foo` で `\begin{foo} 〜 \end{foo}` といった環境の展開
  - `@a` で `\alpha` 、 `@b` で `\beta` といったギリシャ文字の展開
  - `MRM` で `\mathrm{}` 、 `MBF` で `\mathbf` といった数学用フォントの展開
- 多機能なサイドバー
  - ナビゲーション
  - スニペットパネル

などがあります。
