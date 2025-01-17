---
marp: true
theme: default
paginate: true
lang: "ja"
header: "ゼロから始める Docker 入門"
footer: "©2025 Satoshi Yoshimura"
style: |
  /* プレゼンテーション全体のスタイル */
  section {
    font-family: "Noto Sans JP", "Hiragino Kaku Gothic ProN", "Hiragino Sans", Meiryo, sans-serif;
    background-color: white;
    font-size: 24px;
    line-height: 1.5;
    justify-content: flex-start;
    align-items: flex-start;
    padding-top: 50px;
  }

  /* 見出しのスタイル */
  h1 {
    color: #230ee0;
    font-size: 40px;
    margin-bottom: 1em;
  }

  h2 {
    color: #230ee0;
    font-size: 32px;
    margin-bottom: 0.8em;
  }

  /* リストのスタイル */
  ul, ol {
    font-size: 22px;
    margin-left: 1em;
    line-height: 1.6;
  }

  /* ネストされたリストのスタイル */
  ul ul, ul ol, ol ul, ol ol {
    font-size: 20px;
    margin-top: 0.3em;
    margin-bottom: 0.3em;
  }

  /* リストアイテムの間隔 */
  li {
    margin-bottom: 0.5em;
  }

  /* テキストのスタイル */
  p {
    font-size: 24px;
    margin-bottom: 1em;
  }

  /* コードブロックのスタイル */
  pre {
    background-color: #f8f9fa;
    border-radius: 4px;
    padding: 1em;
  }

  /* ヘッダーとフッターのスタイル */
  header, footer {
    color: #666;
    font-size: 0.8em;
  }

  /* 画像のスタイル */
  section img {
    display: block;
    margin: 0 auto;
    width: 400px;
  }

  /* Mermaidのフォントサイズ */
  .mermaid { 
    font-size: 16px;
  }
math: mathjax
mermaid: true
---

<!-- Mermaidを読み込み -->
<!-- これを読み込まないとmermaidがVSCode拡張機能やHTMLで正しく表示されません。 -->
<script type="module">
import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11.4.1/dist/mermaid.esm.min.mjs';
mermaid.initialize({ startOnLoad: true });
</script>
<!-- Mermaidを読み込み -->

# ゼロから始める Docker 入門

<div style="display: flex; justify-content: center;">
    <img src="https://www.docker.com/wp-content/uploads/2022/03/vertical-logo-monochromatic.png" alt="Docker" style="border-radius: 10px; width: 30%;">
</div>

---

# 目次

1. Docker の基礎知識
   - なぜ Docker が必要か？
   - 環境構築の課題
   - 仮想環境の種類
2. 環境準備
   - Docker のインストール
   - 基本的なコマンド
   - ターミナルの使い方

---

3. Docker の実践
   - コンテナの基本操作
   - Docker Compose 入門
   - イメージの管理
4. 応用編と devcontainer
   - Docker Hub の活用
   - VSCode と devcontainer
   - ベストプラクティス

---

# 1. Docker の基礎知識

<pre class="mermaid">
mindmap
  root((Docker))
    環境の統一
      ローカル環境
      サーバー環境
      チーム開発
    コンテナ化
      アプリケーション
      依存関係
      設定
    効率的な開発
      迅速なデプロイ
      スケーラビリティ
      保守性
</pre>

---

## 1.1 なぜ Docker が必要か？

開発者が直面する一般的な問題：

- **環境の違い**

  - "自分の環境では動くのに..."
  - 本番環境との差異
  - チームメンバー間の環境差

- **依存関係の管理**
  - ライブラリのバージョン
  - システムの設定
  - 複雑な環境構築

---

## 1.2 環境構築の課題

<div style="display: flex; justify-content: center; gap: 2rem;">
  <div>
    <p style="font-weight: bold; font-size: 40px;">従来の課題</p>
    <p>環境構築に時間がかかる</p>
    <p>チーム間で環境が異なる</p>
    <p>本番環境との差異</p>
  </div>

  <div style="flex-shrink: 0;">
    <img src="https://www.docker.com/wp-content/uploads/2021/09/docker-containerized-appliction-blue-border_2.png" alt="Docker containers" style="border-radius: 10px;">
  </div>
</div>

---

## 1.3 仮想環境の種類

<div style="display: flex; justify-content: center; gap: 2rem;">
  <div>
    <p style="font-weight: bold; font-size: 40px;">仮想マシン vs コンテナ</p>
    <p>仮想マシン：完全な OS を仮想化</p>
    <p>コンテナ：アプリケーション実行に必要な部分のみを仮想化</p>
    <p>Dockerはコンテナ型仮想化を採用</p>
  </div>

  <div style="flex-shrink: 0;">
    <img src="https://www.docker.com/wp-content/uploads/2021/11/container-vm-whatcontainer_2.png" alt="VM vs Container" style="border-radius: 10px;">
  </div>
</div>

---

# 2. 環境準備

## 2.1 Docker のインストール

```bash
# Macの場合（Homebrew）
brew install --cask docker

# Windowsの場合
# Docker Desktop for Windowsをインストール
# https://www.docker.com/products/docker-desktop
```

---

## 2.2 基本的なコマンド

```bash
# イメージの取得
docker pull python:3.9

# コンテナの作成と起動
docker run -it python:3.9

# コンテナの一覧表示
docker ps

# コンテナの停止
docker stop <container_id>
```

---

# 3. Docker の実践

## 3.1 コンテナの基本操作

```yaml
# docker-compose.yml の例
version: "3"
services:
  web:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - .:/code
    environment:
      - DEBUG=1
```

---

## 3.2 Docker Compose 入門

<pre class="mermaid">
graph LR
    A[docker-compose.yml] --> B[コンテナ定義]
    B --> C[ビルド]
    C --> D[実行]
    D --> E[管理]
</pre>

---

# 4. 応用編と devcontainer

## 4.1 Docker Hub の活用

- 公式イメージの利用
- カスタムイメージの作成
- イメージの共有とバージョン管理

---

## 4.2 VSCode と devcontainer

```json
// .devcontainer/devcontainer.json
{
  "name": "Python 3",
  "build": {
    "dockerfile": "Dockerfile",
    "context": ".."
  },
  "extensions": ["ms-python.python", "ms-python.vscode-pylance"]
}
```

---

# まとめ

- Docker の基本概念を理解する
- 環境構築の自動化
- コンテナによる開発環境の統一
- devcontainer による効率的な開発

---

# 参考資料・Q&A

## 参考資料

- [Docker 公式ドキュメント](https://docs.docker.com/)
- [Docker Compose ドキュメント](https://docs.docker.com/compose/)
- [VSCode Remote Development](https://code.visualstudio.com/docs/remote/remote-overview)
