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
    <img src="https://kazulog.fun/wp-content/uploads/2023/12/Docker-Logo.png" alt="Docker" style="border-radius: 10px; width: 50%;">
</div>

---

# 目次

1. Docker の基礎知識

   - なぜ Docker が必要か？
   - 環境構築の課題
   - 仮想環境の種類
   - コンテナの登場
   - 仮想マシン vs コンテナ
   - 仮想化の 5 レベル
   - Docker のイメージ

---

2. 環境準備

   - Docker のインストール
   - 基本的なコマンド

3. Docker の実践

   - Docker の基本操作
   - Dockerfile の作成
   - イメージのビルドと実行

---

4. Docker Compose 入門

   - Docker Compose とは
   - Docker と Docker Compose の違い
   - Docker Compose のメリット
   - compose.yaml の基本構造
   - インストールと基本コマンド
   - 実践例：Go アプリケーション環境の構築

---

5. Dev Container 入門
   - devcontainer とは
   - devcontainer のメリット
   - システム要件とインストール
   - 基本的な使い方
   - Dev Container Features
   - 実践的な例：Python プロジェクト
   - 高度な設定
   - デバッグ設定
   - トラブルシューティング

---

# 1. Docker の基礎知識

<div style="text-align: center; margin-bottom: 40px;">
  Docker は、アプリケーションをコンテナ化して開発、配布、実行するためのプラットフォームです。
</div>

<div style="display: flex; justify-content: center;">
    <img src="https://kazulog.fun/wp-content/uploads/2023/12/Docker-Logo.png" alt="Docker" style="border-radius: 10px; width: 50%;">
</div>

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
    <p style="font-size: 30px;">環境構築に時間がかかる</p>
    <p style="font-size: 30px;">チーム間で環境が異なる</p>
    <p style="font-size: 30px;">本番環境との差異</p>
    <p style="font-size: 30px;">メンテナンスが難しい</p>
  </div>

  <div style="flex-shrink: 0;">
    <img src="https://miro.medium.com/v2/resize:fit:1280/1*rJgmy0nos999rx5NvSijUQ.png" alt="Docker containers" style="border-radius: 10px; width: 450px;">
  </div>
</div>

---

## 1.3 仮想環境の種類

### ハイパーバイザー（Hypervisor）とは

<div style="display: flex; justify-content: space-between;">
  <div style="width: 60%;">
    <p>ハイパーバイザーは、物理的なハードウェア上で複数の仮想マシンを動作させるための基本ソフトウェアです。</p>
    <p style="font-weight: bold;">主な種類：</p>
    <ul>
      <li>Type 1（ベアメタル型）
        <ul>
          <li>ハードウェアに直接インストール</li>
          <li>例：VMware ESXi, Microsoft Hyper-V</li>
        </ul>
      </li>
      <li>Type 2（ホスト型）
        <ul>
          <li>既存のOSの上で動作</li>
          <li>例：VirtualBox, VMware Workstation</li>
        </ul>
      </li>
    </ul>
  </div>
  
  <div style="width: 35%;">
    <img src="https://upload.wikimedia.org/wikipedia/commons/e/e1/Hyperviseur.png" alt="Hypervisor Types" style="width: 100%; border-radius: 10px;">
  </div>
</div>

---

## 1.4 コンテナの登場

<div style="display: flex; flex-direction: column; align-items: center">

  <div>
    <p style="font-weight: bold; font-size: 30px;">仮想マシン：完全な OS を仮想化</p>
    <p style="font-weight: bold; font-size: 30px;">コンテナ：アプリケーション実行に必要な部分のみを仮想化</p>
  </div>

  <div style="display: flex; justify-content: center;">
    <div style="flex-shrink: 0;">
      <img src="https://www.docker.com/wp-content/uploads/2021/11/container-vm-whatcontainer_2.png" alt="VM vs Container" style="border-radius: 10px;">
    </div>
    <div style="flex-shrink: 0;">
      <img src="https://www.docker.com/ja-jp/app/uploads/2021/11/docker-containerized-appliction-blue-border_2.png" alt="VM vs Container" style="border-radius: 10px;">
    </div>
  </div>
</div>

---

## 1.5 仮想マシン vs コンテナ

<div style="display: flex; justify-content: center; gap: 2rem;">
  <div>
    <p style="font-weight: bold; font-size: 30px;">OS配備 : 超高速</p>
    <p style="font-weight: bold; font-size: 30px;">アプリ配備 : 超高速</p>
    <p style="font-weight: bold; font-size: 30px;">アプリ : 超軽量</p>
    <p style="font-weight: bold; font-size: 30px;">メモリ使用量 : 少ない</p>
    <p style="font-weight: bold; font-size: 30px;">可搬性 : 高い</p>
  </div>
    <div style="flex-shrink: 0;">
      <img src="https://www.docker.com/wp-content/uploads/2021/11/container-vm-whatcontainer_2.png" alt="VM vs Container" style="border-radius: 10px; width: 350px;">
    </div>
    <div style="flex-shrink: 0;">
      <img src="https://www.docker.com/ja-jp/app/uploads/2021/11/docker-containerized-appliction-blue-border_2.png" alt="VM vs Container" style="border-radius: 10px; width: 350px;">
    </div>
</div>

---

## 1.6 仮想化の 5 レベル

### 1. Instruction Set Architecture（ISA）レベル

- プロセッサと命令セットの間を抽象化する仮想化
- 異なる命令セットのエミュレーションを実現
- コード解釈や動的バイナリトランスレーション技術を使用

### 2. Hardware Abstraction Layer（HAL）レベル

- I/O デバイスやメモリなどの物理デバイスと OS の間を抽象化
- クラウド環境の Virtual Machine がこのレベルに該当
- Hypervisor を使用して VM を作成・管理

---

### 3. OS レベル

- OS とアプリケーションの間を抽象化
- 隔離された環境を軽量に作成可能
- Docker コンテナはこのレベルの仮想化の代表例

### 4. Library レベル

- ユーザーとライブラリ間を抽象化
- User-level ABI や API エミュレーション
- WINE、vCUDA などが該当

---

### 5. Application レベル

- 単一アプリケーションの抽象化
- Process-level virtualization とも呼ばれる
- 高級言語実行環境で一般的に使用:
  - JVM (Java Virtual Machine)
  - Python インタプリタ
  - Node.js の V8 エンジン
  - Go ランタイム
  - Ruby の YARV

---

<div style="display: flex; justify-content: center;">
  <img src="https://www.hitechnectar.com/wp-content/uploads/2020/03/Five-Levels-of-Virtualization.jpg" alt="Five Levels of Virtualization" style="border-radius: 10px;">
</div>

---

## 1.7 Docker のイメージ

<div style="display: flex; justify-content: center;">
  <div>
    <p style="font-size: 24px;">① Docker Hub等のリポジトリから公式のイメージを取得（pull）</p>
    <p style="font-size: 24px;">② 取得イメージをもとにDockerFileを作成し、自作のイメージを作成（build）</p>
    <p style="font-size: 24px;">③ イメージからコンテナを作成（create）</p>
    <p style="font-size: 24px;">④ コンテナを起動（start）</p>
  </div>

  <div style="flex-shrink: 0;">
    <img src="https://o2mamiblog.com/wp-content/uploads/2022/07/Docker-1_page-0001.jpg" alt="Dockerのイメージ" style="border-radius: 10px;width: 700px;">
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

もしくは Docker Desktop をインストール
https://www.docker.com/ja-jp/get-started/

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

## 3.1 Docker の基本操作

既存のイメージを Docker Hub から取得する

```bash
# イメージの取得
docker pull python:3.9

# コンテナの作成と起動
docker run -it python:3.9
```

起動したコンテナ上で python を実行する

```python
print("Hello, Docker!")

```

---

python のバージョンを確認する

```python
import sys
print(sys.version)
```

コンテナから抜ける

```python
exit()
```

---

Dockerfile を作成してイメージを作成する

フォルダを作成して移動する

```bash
mkdir python-sample
cd python-sample
```

以下の `app.py` を作成する

```python
# シンプルなPythonスクリプト
print("Hello from Docker!")
print("This is a minimal Docker example.")
```

---

同一フォルダに以下の `Dockerfile` を作成する

```dockerfile
FROM python:3.9

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]
```

---

Dockerfile をビルドする

```bash
docker build -t my-python-app .
```

コンテナを起動する

```bash
docker run -it my-python-app
```

コンテナを停止する

```bash
docker stop my-python-app
```

---

# 4. Docker Compose 入門

Docker Compose は、複数の Docker コンテナを効率的に管理・実行するためのツールです。開発環境で複数のサービス（Web サーバーや DB など）を連携させる際に特に威力を発揮します。

<div style="display: flex; justify-content: center;">
  <img src="https://future-architect.github.io/images/20240620a/20210117130925.jpg" alt="Docker Compose" style="border-radius: 10px; width: 700px;">
</div>

---

## 4.1 Docker Compose とは

<div style="display: flex; justify-content: center;">
  <img src="https://www.kagoya.jp/howto/wp-content/uploads/dockercomposeimg.png.webp" alt="Docker Compose" style="border-radius: 10px; width: 700px;">
</div>

---

## 4.2 Docker と Docker Compose の違い

<div style="display: flex; justify-content: center;">
  <img src="https://www.kagoya.jp/howto/wp-content/uploads/1f60e842dd5eb7b5c1eb066d36267426.png.webp" alt="Docker Compose" style="border-radius: 10px; width: 600px;">
</div>

---

## 4.3 Docker Compose のメリット

1. **複数コンテナの簡単な管理**
   - 複数のコンテナを 1 つのコマンドで起動/停止
   - 設定を一元管理できる
   - 開発環境の再現性が向上
2. **運用上のミス軽減**
   - 手作業による設定ミスを防止
   - 環境の一貫性を保証
   - チーム間での共有が容易
3. **ネットワーク設定の簡素化**
   - コンテナ間の通信が自動設定
   - ホスト名による通信が可能
   - IP アドレス変更に強い

---

## 4.4 compose.yaml の基本構造

compose.yaml は Docker Compose の設定ファイルです。以下は基本的な構造例です：

```yaml
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    environment:
      FLASK_ENV: development
    depends_on:
      - db
  db:
    image: postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: example
volumes:
  postgres_data:
```

---

### 主要な設定項目

| 設定項目        | 説明                                     |
| --------------- | ---------------------------------------- |
| **services**    | アプリケーションを構成するコンテナの定義 |
| **build**       | Dockerfile のパス                        |
| **image**       | 使用する Docker イメージ                 |
| **ports**       | ポートマッピング                         |
| **volumes**     | データの永続化設定                       |
| **environment** | 環境変数の設定                           |
| **depends_on**  | 依存関係の定義                           |

---

## 4.5 Docker Compose のインストール

```bash
# Macの場合（Homebrew）
brew install docker-compose

# Windowsの場合
# Docker Desktop for Windowsをインストール
# https://www.docker.com/products/docker-desktop
```

---

## 4.6 Docker Compose の基本的なコマンド

```bash
# コンテナの起動（バックグラウンド実行）
docker compose up -d

# コンテナの停止と削除
docker compose down

# コンテナの再起動
docker compose restart

# ログの確認
docker compose logs

# サービスの状態確認
docker compose ps
```

---

## 4.7 実践例：Go アプリケーション環境の構築

以下は、Go のシンプルな API サーバーを構築する例です：

`main.go`の内容：

```go
package main

import (
    "encoding/json"
    "log"
    "net/http"
)

type Response struct {
    Message string `json:"message"`
}
```

---

```go
func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        response := Response{Message: "Hello, World!"}
        w.Header().Set("Content-Type", "application/json")
        json.NewEncoder(w).Encode(response)
    })

    log.Println("Server starting on port 8080...")
    if err := http.ListenAndServe(":8080", nil); err != nil {
        log.Fatal(err)
    }
}
```

---

`compose.yaml`の設定：

```yaml
services:
  api:
    build: .
    ports:
      - "8080:8080"
    volumes:
      - .:/app
    environment:
      - GO_ENV=development

volumes:
  go_modules:
```

---

対応する`Dockerfile`：

```dockerfile
FROM golang:1.21-alpine

WORKDIR /app

# アプリケーションのソースコードをコピー
COPY . .

# アプリケーションをビルドして実行
CMD ["go", "run", "main.go"]
```

---

フォルダの作成と移動:

```bash
mkdir go-app
cd go-app
```

作成したフォルダに`main.go` `Dockerfile` `compose.yaml`を作成する

```bash
touch main.go
touch Dockerfile
touch compose.yaml
```

---

アプリケーションの起動：

```bash
docker compose up --build
```

API の動作確認：

```bash
curl http://localhost:8080
# 応答: {"message":"Hello, World!"}
```

---

# 5. Dev Container 入門

## 5.1 devcontainer とは

<div style="display: flex; justify-content: center;">
  <img src="https://code.visualstudio.com/assets/docs/devcontainers/containers/architecture-containers.png" alt="Dev Container Architecture" style="border-radius: 10px; width: 700px;">
</div>

Visual Studio Code Dev Containers は、コンテナを完全な機能を備えた開発環境として使用できるようにする拡張機能です。

---

## 5.2 devcontainer のメリット

<div style="display: flex; justify-content: space-between;">
  <div style="width: 45%;">
    <h3>開発者にとって</h3>
    <ul>
      <li>環境構築の簡素化</li>
      <li>チーム間での環境の統一</li>
      <li>プロジェクト切り替えの容易さ</li>
      <li>ローカル環境の汚染防止</li>
    </ul>
  </div>
  <div style="width: 45%;">
    <h3>チームにとって</h3>
    <ul>
      <li>新メンバーの参加がスムーズ</li>
      <li>環境の標準化</li>
      <li>トラブルシューティングの効率化</li>
      <li>CI/CDとの親和性</li>
    </ul>
  </div>
</div>

---

## 5.3 システム要件

### ローカル/リモートホスト要件

<div style="font-size: 20px;">

| OS             | 要件                                                                                                                        |
| -------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Windows        | Docker Desktop 2.0+ (Windows 10 Pro/Enterprise)<br>Windows 10 Home (2004+)は Docker Desktop 2.3+と WSL 2 バックエンドが必要 |
| macOS          | Docker Desktop 2.0+                                                                                                         |
| Linux          | Docker CE/EE 18.06+と Docker Compose 1.21+                                                                                  |
| リモートホスト | 最小 1GB RAM（推奨：2GB RAM、2 コア CPU）                                                                                   |

</div>

---

## 5.4 インストール手順

1. **VS Code のインストール**

   - [VS Code 公式サイト](https://code.visualstudio.com/)からダウンロード

2. **Dev Containers 拡張機能のインストール**
   - VS Code の拡張機能マーケットプレイスから「Dev Containers」を検索
   - もしくはコマンドラインから:

```bash
code --install-extension ms-vscode-remote.remote-containers
```

---

### Dev Container のインストール

<div style="display: flex; justify-content: center;">
  <img src="https://blog.kinto-technologies.com/assets/blog/authors/torii/2022-12-10-dev-container/devcontainer-01.webp" alt="Dev Container Architecture" style="border-radius: 10px; width: 700px;">
</div>

---

## 5.5 基本的な使い方

### devcontainer.json の作成

```json
{
  "name": "Node.js & TypeScript",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:1-20-bullseye",
  "forwardPorts": [3000],
  "customizations": {
    "vscode": {
      "extensions": ["dbaeumer.vscode-eslint", "esbenp.prettier-vscode"]
    }
  },
  "postCreateCommand": "npm install"
}
```

---

### devcontainer.json の主要な設定項目

<div style="font-size: 20px;">

| 設定項目          | 説明                        | 例                                                    |
| ----------------- | --------------------------- | ----------------------------------------------------- |
| name              | コンテナの名前              | `"name": "Python 3"`                                  |
| image             | 使用する Docker イメージ    | `"image": "mcr.microsoft.com/devcontainers/python:3"` |
| build             | Dockerfile からのビルド設定 | `"build": { "dockerfile": "Dockerfile" }`             |
| forwardPorts      | 転送するポート              | `"forwardPorts": [3000, 5000]`                        |
| customizations    | VS Code 固有の設定          | `"customizations": { "vscode": { ... } }`             |
| extensions        | インストールする拡張機能    | `"extensions": ["ms-python.python"]`                  |
| settings          | VS Code 設定                | `"settings": { "python.linting.enabled": true }`      |
| postCreateCommand | コンテナ作成後のコマンド    | `"postCreateCommand": "npm install"`                  |

</div>

---

## 5.6 Dev Container Features

Features は、開発コンテナに追加のツールやランタイムを簡単に追加できる機能です。

```json
{
  "features": {
    "ghcr.io/devcontainers/features/github-cli:1": {},
    "ghcr.io/devcontainers/features/docker-in-docker:2": {},
    "ghcr.io/devcontainers/features/python:1": {
      "version": "3.11"
    }
  }
}
```

---

## 5.7 実践的な例：Python プロジェクト

### プロジェクト構造

```
myproject/
├── .devcontainer/
│   ├── devcontainer.json
│   └── Dockerfile
├── src/
│   └── main.py
└── requirements.txt
```

---

### VS Code で Dev Container を開く

<div style="text-align: center;">
<p>VS Code 左下のメニューから「Dev Container」を選択する</p>
  <img src="https://industrial-inference.com/wp-content/uploads/2023/08/image.png" style="border-radius: 10px; width: 300px; margin: 10px auto;">
</div>

<div style="text-align: center;">
<p>New Dev Container を選択する<p>
  <img src="https://chigusa-web.com/wp-content/uploads/2023/04/2023-04-01_17h48_49.png" alt="Dev Container Architecture" style="border-radius: 10px; width: 700px;">
</div>

---

### Dockerfile

```dockerfile
FROM mcr.microsoft.com/devcontainers/python:3.11

WORKDIR /workspace

COPY requirements.txt /tmp/pip-tmp/
RUN pip3 --disable-pip-version-check --no-cache-dir install -r /tmp/pip-tmp/requirements.txt \
    && rm -rf /tmp/pip-tmp

USER vscode
```

---

### devcontainer.json

```json
{
  "name": "Python Project",
  "build": {
    "dockerfile": "Dockerfile",
    "context": ".."
  },
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-python.python",
        "ms-python.vscode-pylance",
        "ms-python.black-formatter"
      ],
      "settings": {
        "python.defaultInterpreterPath": "/usr/local/bin/python",
        "python.linting.enabled": true,
        "python.formatting.provider": "black"
      }
    }
  },
  "forwardPorts": [8000],
  "postCreateCommand": "pip install --user -r requirements.txt",
  "remoteUser": "vscode"
}
```

---

## 5.8 高度な設定

### ボリュームマウントの最適化

```json
{
  "mounts": [
    "source=${localWorkspaceFolder}/data,target=/workspace/data,type=bind,consistency=cached",
    "source=node_modules,target=${containerWorkspaceFolder}/node_modules,type=volume"
  ]
}
```

### 環境変数の設定

```json
{
  "remoteEnv": {
    "DATABASE_URL": "postgresql://user:pass@localhost:5432/db",
    "NODE_ENV": "development"
  }
}
```

---

## 5.9 デバッグ設定

### launch.json の例（Python）

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: Current File",
      "type": "python",
      "request": "launch",
      "program": "${file}",
      "console": "integratedTerminal"
    }
  ]
}
```

---

## 5.10 トラブルシューティング

<div style="font-size: 20px;">

| 問題                 | 解決策                                                                               |
| -------------------- | ------------------------------------------------------------------------------------ |
| コンテナが起動しない | - Docker サービスの状態確認<br>- リソース制限の確認<br>- ログの確認                  |
| パフォーマンスが悪い | - ボリュームマウントの最適化<br>- WSL2 の使用（Windows）<br>- リソース割り当ての調整 |
| 拡張機能が動作しない | - 拡張機能の再インストール<br>- VS Code の再起動<br>- 互換性の確認                   |

</div>

---

# まとめ

- Docker は、開発環境の標準化と共有を実現し、プロジェクトの一貫性を保つための強力なツール。
- コンテナ技術により、チーム開発の効率が大幅に向上し、メンバー間の環境差を解消する。
- Docker Compose を使用することで、複数のサービスを簡単に管理・実行でき、開発環境の
  再現性が向上する。
- VS Code との深い統合により、開発者は直感的に作業でき、迅速なフィードバックを得られる。
- 適切な設定と運用により、開発ワークフローを最適化し、トラブルシューティングの時間を短縮する。
- Dev Container を利用することで、開発環境の構築が簡素化され、新メンバーの参加がスムーズに。

---

# 参考資料

- [Docker 公式ドキュメント](https://docs.docker.com/)
- [Docker Compose 公式ドキュメント](https://docs.docker.com/compose/)
- [VS Code Dev Containers](https://code.visualstudio.com/docs/devcontainers/containers)
- [Dev Container 仕様](https://containers.dev/)
- [Dev Container Features](https://containers.dev/features)

---

# 参考文献

- [Docker 実践ガイド](https://www.amazon.co.jp/Docker%E5%AE%9F%E8%B7%B5%E3%82%AC%E3%82%A4%E3%83%89-%E7%AC%AC3%E7%89%88-impress-top-gear/dp/429501589X/ref=sr_1_1_sspa?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=3JZXR33O72URO&dib=eyJ2IjoiMSJ9.0K3RjdLLdjiIw82EoPjEkXRvyFTktlgO1zwQskOFiaM-nJSkKCu_kgs6uZXSifrOjcOqG8pFugGq-Js-6EHQ82KGyo1gppSVKk6wlh2L0JcUqRsRU6bQEZ8nwwf0UWF2eZ3GiaIW1NGbpwKgT0MXEJN5Zn43q2W69EbOEO9OqLi_vPXWK82VgkowjBKh3h_MhBrnbUOkprIOOVpb0OpxHc-BzKvA-jNidgANfY0fFNTw12DaO_F5kmbtGSrP5pPGSJkD0QGxXsMD_OmDjArx3G7XkTvsN8gyiwzq4g7RlPk.tSMqZUmv5vdzppDlT8bqw8QT_MqSk5MYe1ZQvrbOKpQ&dib_tag=se&keywords=docker&qid=1739969520&sprefix=docker%2Caps%2C152&sr=8-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1)
- [図解! Docker のツボとコツがゼッタイにわかる本](https://www.amazon.co.jp/%E5%9B%B3%E8%A7%A3-Docker%E3%81%AE%E3%83%84%E3%83%9C%E3%81%A8%E3%82%B3%E3%83%84%E3%81%8C%E3%82%BC%E3%83%83%E3%82%BF%E3%82%A4%E3%81%AB%E3%82%8F%E3%81%8B%E3%82%8B%E6%9C%AC-%E8%A7%92%E9%96%93-%E5%AE%9F/dp/4798065919/ref=sr_1_1?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=2IMB8ZYPAU6T4&dib=eyJ2IjoiMSJ9.K2Dyo1dfvjF0oyF9GJp5SYIcsNVGp0DS11oH2qbjlMRPoXL17lIMMGdkyO9tUF3nNbSvzz1LeXsXf2-VQ3C0cxPErILPo6rDTrsWT_xJYeg2BfHJzXOhwFWQks3N7kvSFTdevkESAfevbStQAErt5dIxFLdaEZLLPchV4snHxaIOHJ95WVvM5jFOdaooE7teuj3gr4mpLJOeLQTb2Nsw-y70tnpMhkOJRx971Pd_wTdQWSrXCxr5QtaVrtzuoH_zrknLIRy5OuFwBFiXxLL3NYk6RNJkucvgF-QtwHbneB2Cdpj6O3GroSMDzFcP7cWQ0M0-ycvAip539zvOVIsu1v_4EdIvPeFzlsQUSOo_gsaIAmLHJJS0twje3bvKxTyYa9pntn8cAGKF0BUV33_4U9prB8bAnB3qzf9bJxHSAVT88_K95nZ8kWGM1VqHUPCs._DLhKk_UlRVhqli3h4nyCSEorlDBhUox4S0q1qeenEQ&dib_tag=se&keywords=docker+%E5%9B%B3%E8%A7%A3&qid=1739969668&sprefix=docker+%E5%9B%B3%E8%A7%A3%2Caps%2C146&sr=8-1)
- [Linux のしくみ　―実験と図解で学ぶ OS、仮想マシン、コンテナの基礎知識](https://www.amazon.co.jp/%EF%BC%BB%E8%A9%A6%E3%81%97%E3%81%A6%E7%90%86%E8%A7%A3%EF%BC%BDLinux%E3%81%AE%E3%81%97%E3%81%8F%E3%81%BF-%E2%80%95%E5%AE%9F%E9%A8%93%E3%81%A8%E5%9B%B3%E8%A7%A3%E3%81%A7%E5%AD%A6%E3%81%B6OS%E3%80%81%E4%BB%AE%E6%83%B3%E3%83%9E%E3%82%B7%E3%83%B3%E3%80%81%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%81%AE%E5%9F%BA%E7%A4%8E%E7%9F%A5%E8%AD%98%E3%80%90%E5%A2%97%E8%A3%9C%E6%94%B9%E8%A8%82%E7%89%88%E3%80%91-%E6%AD%A6%E5%86%85-%E8%A6%9A/dp/429713148X/ref=sr_1_1_sspa?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=13I5NX2SSN0X1&dib=eyJ2IjoiMSJ9.X0Lwqdudm3TEujscUbU2erq14A8O-mF4HMNy0rrT5J5Oi1mxydfPNjbUQQn6EQ5ctIEn2fSH5DuQRkMR8F9yl0Gd3zPavzMyDca6al1ul24kHFAo5xp1EkVSI-M05CFXpDWfHaoJA3m1Z6qtNfVZ3PTtXg-lI4_5obde6aL_o3o0iIa4gxVuRh1gk3Tdd96vJ1hPAVkc5rJ6u1u-uOsUZC-1g7AU0gwvivKH4klYWojTECXqvdp7gNI67BQvCu85ZiOKFFgf86K8AZEXjmprhzQnB09t2iIfaIltlwQSyUA.5jjFdMt28tpCXAVJme4qE6nnIK-kbN-UEKQPxa66NlI&dib_tag=se&keywords=linux&qid=1739969707&sprefix=linux%2Caps%2C154&sr=8-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1)
- [新しい Linux の教科書](https://www.amazon.co.jp/%E6%96%B0%E3%81%97%E3%81%84Linux%E3%81%AE%E6%95%99%E7%A7%91%E6%9B%B8-%E7%AC%AC%EF%BC%92%E7%89%88-%E4%B8%89%E5%AE%85-%E8%8B%B1%E6%98%8E/dp/4815624313/ref=pd_sbs_d_sccl_2_1/355-8074755-0324134?pd_rd_w=7CivU&content-id=amzn1.sym.cc4f782f-18e8-4959-acfc-ffdf7905539a&pf_rd_p=cc4f782f-18e8-4959-acfc-ffdf7905539a&pf_rd_r=E64A10PZ2SSKEFXXYSYW&pd_rd_wg=rAmQR&pd_rd_r=47df3ecb-ba16-4b70-b369-653dc6dce496&pd_rd_i=4815624313&psc=1)
