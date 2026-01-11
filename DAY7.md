# 📅 30 Days of AI – Day 7 まとめ  
**Theming and Layout**

---

## 🎯 目的
- Day 6 で作成した生成AIアプリをベースに  
- **見た目・レイアウトを改善**し、  
- 「機能的」→「**プロダクトらしいUI/UX**」へ進化させる

---

## 🧠 Day 7 でやったこと
- **Dark Mode（ダークテーマ）** の適用  
- **サイドバー中心のレイアウト設計**  
- ステータス表示と出力結果を整理し、視認性を向上  
- ブランド感のある配色・UIに調整

---

## 🧩 主なポイント

### 1️⃣ グローバルテーマ設定（Theming）
- `.streamlit/config.toml` を使用
- CSSを書かずにアプリ全体の見た目を統一

**主な設定内容**
- `base = "dark"`：ダークモード化
- `primaryColor`：アクセントカラー設定
- `buttonRadius = "full"`：ボタンをピル型に
- `[theme.sidebar]`：サイドバー専用の配色指定

👉 **コードを書かずにUIの印象を大きく変えられる**

---

### 2️⃣ レイアウト改善（Sidebar活用）
- 入力URL：メイン画面
- 設定系（トーン・文字数）：**サイドバーへ移動**

```python
with st.sidebar:
    st.title("LinkedIn Post Generator v3")
    tone = st.selectbox(...)
    word_count = st.slider(...)
