# 📅 30 Days of AI – Day 8 まとめ  
**Meet the Chat Elements**

---

## 🎯 目的
- Streamlit が提供する **チャットUI用コンポーネント** を理解する  
- ChatGPT のような **対話型アプリの基本構造** を学ぶ

---

## 🧠 Day 8 で学ぶこと
- `st.chat_message` を使った **チャット形式の表示**
- `st.chat_input` による **ユーザー入力受付**
- 入力に応じた **リアクティブなUI更新**

---

## 🧩 主な要素

### 1️⃣ チャットメッセージの表示
```python
with st.chat_message("user"):
    st.write("Hello! Can you explain what Streamlit is?")

with st.chat_message("assistant"):
    st.write("Streamlit is an open-source Python framework for building data apps.")
    st.bar_chart([10, 20, 30, 40])
