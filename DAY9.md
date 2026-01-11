# Day 9: Understanding Session State (Streamlit) 🧠
> 30 Days of AI / Day 9 の学習内容まとめ  
> テーマ：**Streamlit は基本ステートレス**。状態（値）を保持したいなら **`st.session_state`** を使う。

---

## 1. ゴール（このサンプルで理解すること）
このコードは、同じ「+ / - カウンター」でも以下の違いが出ることを **左右のカラムで比較**して体感するための教材です。

- **左（Standard Variable）**：普通の変数でカウント → クリックのたびに 0 に戻る（実質メモリがない）
- **右（Session State）**：`st.session_state` でカウント → クリックしても値が保持される（メモリがある）

---

## 2. 前提：Streamlit はなぜリセットされるのか？
Streamlit はユーザー操作（ボタンクリック等）があるたびに、**スクリプト全体を上から最後まで再実行（rerun）**します。

つまり、コード中にこう書くと：

```python
count_wrong = 0
```

ボタンを押すたびに **必ずこの行が再実行**され、カウンターが **0 に初期化**されます。

---

## 3. 画面構成（UI）
コード冒頭でタイトル・注意書きを出し、`st.columns(2)` で **2列レイアウト**にしています。

```python
st.title(":material/memory: Understanding Session State")
st.warning("**Instructions:** Try clicking the + and - buttons in both columns to see the difference.")

col1, col2 = st.columns(2)
```

- `:material/...:` は **Material Icons** 表示用の記法（Streamlit の機能）
- `st.columns(2)` は横並びの領域を作り、`col1` と `col2` に分割

---

## 4. 左カラム：❌ Standard Variable（間違いやすい例）
### 4.1 コードの要点
```python
count_wrong = 0
if st.button(...):
    count_wrong += 1
st.metric("Standard Count", count_wrong)
```

### 4.2 何が起きるか（クリック時の流れ）
たとえば「+」ボタンを押したとき：

1. Streamlit が **rerun** を発生させる（スクリプトが最初から再実行）
2. `count_wrong = 0` が実行される → **必ず 0 に戻る**
3. `if st.button("+"):` が True になり、`count_wrong += 1` → **1 になる**
4. 表示は 1 になる
5. 次にもう一回押すと、また **1 になる**（0→+1 の繰り返し）

結果として、
- 連打しても **1 を超えない**
- 「-」は **-1 を下回らない**
- “状態が保持されない” ことが見える

### 4.3 なぜ `key` が必要？
ボタンは同じページ内に複数あるため、Streamlit が内部で識別できるように `key` が必要です。

```python
st.button(":material/add:", key="std_plus")
st.button(":material/remove:", key="std_minus")
```

---

## 5. 右カラム：✅ Session State（正しい例）
### 5.1 3ステップの基本パターン
このサンプルは session_state の鉄板 3ステップを示しています。

#### ① Initialization（初期化：最初の1回だけ作る）
```python
if "counter" not in st.session_state:
    st.session_state.counter = 0
```

- **重要**：毎回 `= 0` しない
- まだキーがないときだけ初期化する → これで rerun しても値が保持される

#### ② Modification（更新：インクリメント / デクリメント）
```python
if st.button(":material/add:", key="state_plus"):
    st.session_state.counter += 1

if st.button(":material/remove:", key="state_minus"):
    st.session_state.counter -= 1
```

- `st.session_state.counter` は辞書のように保持される “状態”
- ボタンが押されたタイミングで値を変更

#### ③ Read（参照：表示する）
```python
st.metric("State Count", st.session_state.counter)
```

---

## 6. このコードがしている処理の全体像（疑似フロー）
### 左（Standard Variable）
- rerun → `count_wrong = 0`  
- ボタンが押されていれば ±1  
- 表示 → 1 or -1 で頭打ち

### 右（Session State）
- rerun → `counter` がなければ 0 を作る（初回のみ）  
- ボタンが押されていれば `st.session_state.counter` を ±1  
- 表示 → **累積で増減**する

---

## 7. ここがポイント（試験・実務で超重要）
- Streamlit は **操作のたびにスクリプト全体が再実行**される
- 普通の変数は再実行で初期化されるため **状態保持には不向き**
- 状態を保持したい場合は **`st.session_state` を使う**
- `if "key" not in st.session_state:` が **基本中の基本**

---

## 8. 応用：session_state を使うと何ができる？
- チャット履歴（messages の保持）
- フォーム入力値の保持（入力途中で rerun しても消えない）
- ページ間で共有する値（マルチページアプリ）
- カウンター、ステップ、フラグ（ログイン状態、処理の進行状態）

---

## 9. 改善アイデア（より実務っぽくするなら）
### 9.1 コールバック（on_click）で可読性UP
ボタン内で直接 `+= 1` する代わりに関数化できます。

```python
def inc():
    st.session_state.counter += 1

def dec():
    st.session_state.counter -= 1

st.button("＋", on_click=inc)
st.button("－", on_click=dec)
```

### 9.2 リセットボタンを追加
```python
if st.button("Reset"):
    st.session_state.counter = 0
```

### 9.3 キー名の衝突に注意
同じページ内で `key` が重複するとエラーになるので、
- ボタン key
- session_state の key  
どちらも命名ルールを決めておくと安全。

---

## 10. 参考：今回の完成コード（原文）
```python
import streamlit as st

st.title(":material/memory: Understanding Session State")

st.warning("**Instructions:** Try clicking the + and - buttons in both columns to see the difference.")

# Create two columns for side-by-side comparison
col1, col2 = st.columns(2)

# --- COLUMN 1: THE WRONG WAY ---
with col1:
    st.header(":material/cancel: Standard Variable")
    st.write("This resets on every click.")

    # This line runs every time you click ANY button on the page.
    # It effectively erases your progress immediately.
    count_wrong = 0

    # We use nested columns here to put the + and - buttons side-by-side
    subcol_left, subcol_right = st.columns(2)

    with subcol_left:
        # Note: We must give every button a unique 'key'
        if st.button(":material/add:", key="std_plus"):
            count_wrong += 1

    with subcol_right:
        if st.button(":material/remove:", key="std_minus"):
            count_wrong -= 1

    st.metric("Standard Count", count_wrong)
    st.caption("It never gets past 1 or -1 because `count_wrong` resets to 0 before the math happens.")


# --- COLUMN 2: THE RIGHT WAY ---
with col2:
    st.header(":material/check_circle: Session State")
    st.write("This memory persists.")

    # 1. Initialization: Create the key only if it doesn't exist yet
    if "counter" not in st.session_state:
        st.session_state.counter = 0

    # We use nested columns here as well
    subcol_left_2, subcol_right_2 = st.columns(2)

    with subcol_left_2:
        # 2. Modification: Update the dictionary value (Increment)
        if st.button(":material/add:", key="state_plus"):
            st.session_state.counter += 1

    with subcol_right_2:
        # 2. Modification: Update the dictionary value (Decrement)
        if st.button(":material/remove:", key="state_minus"):
            st.session_state.counter -= 1

    # 3. Read: Display the value
    st.metric("State Count", st.session_state.counter)
    st.caption("This works because we only set the counter to 0 if it doesn't exist.")

# Footer
st.divider()
st.caption("Day 9: Understanding Session State | 30 Days of AI")
```

---

## 11. まとめ（最短で覚えるなら）
- Streamlit は **rerun 前提**
- 変数は毎回初期化される → **状態は消える**
- 状態を残したい → **`st.session_state`**
- 初期化は「キーがない時だけ」  
  `if "x" not in st.session_state: st.session_state.x = 初期値`

以上！
