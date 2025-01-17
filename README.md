# ゼロから始める Docker 入門

# git clone から新たにレポジトリを作成する

```
git clone {gitリポジトリのURL} {新しいレポジトリ名}
cd {新しいレポジトリ名}
git remote remove origin
rm -rf .git
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin {gitリポジトリのURL}
git push -u origin main
```

# Mermaid を記述する方法

<!-- Marpと認識させるおまじない -->

## Basic Pie Chart

<!-- class名をmermaidとした要素タグ内に出力するMermaidコードを記載 -->
<pre class="mermaid">
pie title What Voldemort doesn't have?
  "FRIENDS" : 5
  "FAMILY" : 3
  "NOSE" : 45
</pre>

<!-- Mermaidを読み込み -->
<script type="module">
import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11.4.1/dist/mermaid.esm.min.mjs';
mermaid.initialize({ startOnLoad: true });
</script>

# HTML 生成コマンド

## Github Pages で公開する場合、以下のコマンドで docs/index.html を生成する必要があります。

```
npx marp src/ゼロから始めるDocker入門.md --html --allow-local-files --theme-set ./style/ -o docs/index.html --embed-cs
```

# PDF 生成コマンド

```
npx marp src/ゼロから始めるDocker入門.md --pdf --allow-local-files -o docs/index.pdf
```

# 編集可能な PPTX に変換するコマンド

```
marp --pptx --pptx-editable ゼロから始めるDocker入門.md
```

※ Chrome や LibreOffice がインストールされている必要があります。そのため、ローカルでの実行が望ましいです。

LibreOffice をインストールするコマンド

```
brew install libreoffice
```
