# Day1 
---

## 接続設定（Connection Setup）

Day 1 を実行する前に、**デプロイ先の環境**に応じて Snowflake 接続を設定する必要があります。

---

### Streamlit in Snowflake（推奨）

- **事前設定は不要**
- Snowsight 上で Streamlit アプリを作成するだけで、自動的に接続されます  
→ **Step 1 に進んでください**

---

### ローカル開発 または Streamlit Community Cloud

**初回のみ設定が必要**です。

プロジェクトフォルダ直下に、以下のファイルを作成します。

```toml
.streamlit/secrets.toml
[connections.snowflake]
account = "xy12345.us-east-1"       # Snowsight → Account → View account details で確認
user = "yourusername"               # Snowflake のユーザー名
password = "yourpassword"           # Snowflake のパスワード
role = "ACCOUNTADMIN"               # 使用するロール
warehouse = "COMPUTE_WH"            # 使用するウェアハウス
database = "SNOWFLAKE_LEARNING_DB"  # 使用するデータベース
schema = "PUBLIC"                   # 使用するスキーマ
```

### ***1.ライブラリのインポート***
```toml
import streamlit as st
```
-Streamlitライブラリを読み込む
-WebアプリのUIを作成するために使用

### ***2.Snowflakeへの接続***
```toml
# 実行環境を自動判別して接続
try:
    from snowflake.snowpark.context import get_active_session
    session = get_active_session()
except:
    from snowflake.snowpark import Session
    session = Session.builder.configs(
        st.secrets["connections"]["snowflake"]
    ).create()
```
get_active_session
* Streamlit in Snowflake（SiS） で利用可能  
* 既に認証済みの Snowflake セッションを取得

session = get_active_session()  
* Snowflake への接続（セッション）を確立  
* 以降の SQL 実行で利用

except ブロック  
* ローカル環境や Streamlit Community Cloud で実行した場合に使用  
* .streamlit/secrets.toml に定義した認証情報を利用

Session.builder.configs(...)
* secrets に定義された情報を使って Snowflake セッションを作成

***なぜtry/execptを使うのか***
この書き方によって、1つのコードブロックで複数環境での動作可能になるため。
* Streamlit in Snowflake
* ローカル環境
* Streamlit Community Cloud

