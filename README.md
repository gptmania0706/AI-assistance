# AWSでAIコーディング環境（Aider＋Gemini＋Qwen）構築手順

このドキュメントでは、AWS EC2（Ubuntu 24.04
LTS）上に、Aider・Gemini・Qwen（API／ローカル）の3種類を利用できるAIコーディング環境を構築する手順をまとめています。

------------------------------------------------------------------------

# 動作確認済み環境

  項目      バージョン
  --------- -------------------------
  AWS EC2   Ubuntu Server 24.04 LTS
  Ubuntu    24.04 LTS
  Python    3.12
  Ollama    0.31.2
  Aider     0.86.2

------------------------------------------------------------------------

# 利用できるAI

  AI                 接続方法
  ------------------ ----------------------------
  Gemini 3.5 Flash   Google AI Studio API
  Qwen3              Ollama（ローカル）
  Qwen3              DashScope（OpenAI互換API）

この環境では、3種類のAIを用途に応じて切り替えて利用できます。

------------------------------------------------------------------------

# 1. EC2インスタンス作成

Ubuntu Server 24.04 LTS を選択します。

# 2. SSH接続

``` bash
ssh -i "C:\Users\AI1\.ssh\inst0709.pem" ubuntu@<EC2のIPアドレス>
```

# 3. パッケージ更新

``` bash
sudo apt update
sudo apt upgrade -y
```

# 4. Python環境構築

インストール

``` bash
sudo apt install -y python3 python3-pip python3-venv git
```

確認

``` bash
python3 --version
pip3 --version
git --version
```

# 5. Ollamaインストール

``` bash
curl -fsSL https://ollama.com/install.sh | sh
```

確認

``` bash
ollama --version
systemctl status ollama
```

# 6. ローカルモデル取得

``` bash
ollama pull qwen3:14b
```

確認

``` bash
curl http://127.0.0.1:11434/api/tags
```

# 7. ローカルモデル動作確認

``` bash
ollama run qwen3:14b
```

# 8. Python仮想環境作成

``` bash
python3 -m venv ~/aider-env
source ~/aider-env/bin/activate
```

# 9. Aiderインストール

``` bash
pip install --upgrade pip
pip install aider-chat
aider --version
```

# 10. APIキー永続化

``` bash
nano ~/.bashrc
```

末尾へ追加

``` bash
export GEMINI_API_KEY="xxxxxxxx"

export OPENAI_API_KEY="xxxxxxxx"

export OPENAI_API_BASE="https://dashscope-intl.aliyuncs.com/compatible-mode/v1"
```

反映

``` bash
source ~/.bashrc
```

確認

``` bash
echo $GEMINI_API_KEY
echo $OPENAI_API_KEY
echo $OPENAI_API_BASE
```

# 11. プロジェクト作成

``` bash
mkdir hello
cd hello
git init
```

# 12. Geminiで起動

``` bash
aider --model gemini/gemini-3.5-flash
```

# 13. Qwen APIで起動

``` bash
aider --model openai/qwen3-coder-plus
```

> モデル名は契約しているDashScopeのモデルに合わせて変更してください。

# 14. Qwenローカルで起動

``` bash
aider --model ollama_chat/qwen3:14b
```

# 15. Hello World

Aiderへ入力

``` text
PythonでHello Worldを書いて
```

確認されたら

``` text
Y
```

# 16. 実行

``` bash
python3 hello.py
```

実行結果

``` text
Hello, World!
```

------------------------------------------------------------------------

# 日常利用

``` bash
ssh -i "C:\Users\AI1\.ssh\inst0709.pem" ubuntu@<EC2のIPアドレス>
source ~/aider-env/bin/activate
cd ~/hello
```

Gemini

``` bash
aider --model gemini/gemini-3.5-flash
```

Qwen API

``` bash
aider --model openai/qwen3-coder-plus
```

Qwenローカル

``` bash
aider --model ollama_chat/qwen3:14b
```

------------------------------------------------------------------------

# 将来的な拡張

OpenAI互換APIを利用するサービスであれば、`OPENAI_API_KEY` と
`OPENAI_API_BASE` を変更するだけで利用できます。

-   DeepSeek API
-   GLM API
-   Moonshot Kimi
-   SiliconFlow
-   OpenRouter
