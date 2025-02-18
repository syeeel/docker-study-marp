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

以下の app.py を作成する

```python
# シンプルなPythonスクリプト
print("Hello from Docker!")
print("This is a minimal Docker example.")
```

同一フォルダに以下の Dockerfile を作成する

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

### Docker Compose のメリット

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

### compose.yaml の基本構造

compose.yaml は、Docker Compose の設定ファイルです。以下は基本的な構造例です：

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

- **services**: アプリケーションを構成するコンテナの定義
  - **build**: Dockerfile のパス
  - **image**: 使用する Docker イメージ
  - **ports**: ポートマッピング
  - **volumes**: データの永続化設定
  - **environment**: 環境変数の設定
  - **depends_on**: 依存関係の定義

---

## 4.3 Docker Compose のインストール

```bash
# Macの場合（Homebrew）
brew install docker-compose

# Windowsの場合
# Docker Desktop for Windowsをインストール
# https://www.docker.com/products/docker-desktop
```

---

## 4.4 Docker Compose の基本的なコマンド

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

## 4.5 実践例：Web アプリケーション環境の構築

以下は、Python の Web アプリケーションと PostgreSQL を組み合わせた環境の例です：

```yaml
services:
  web:
    build: .
    command: python app.py
    volumes:
      - .:/code
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://postgres:example@db:5432/myapp
    depends_on:
      - db
```

---

```yaml
  db:
    image: postgres:13
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=example
      - POSTGRES_DB=myapp

volumes:
  postgres_data:
```

---

対応する Dockerfile：

```dockerfile
FROM python:3.9
WORKDIR /code
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

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
