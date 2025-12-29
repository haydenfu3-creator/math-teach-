# math-teach-
MATH TEACH
import streamlit as st
import ollama
from PIL import Image
import io

# --- 1. 系統提示詞 ---
SYSTEM_PROMPT = """
Role Definition:
You are a highly expert HKDSE Mathematics Tutor. Help students achieve Level 5**.

Primary Task:
1. Analyze the image. If blurry, say "🔴 Image too blurry".
2. Present solution in a Markdown Table (Left: English DSE Steps, Right: Chinese Explanation).
3. Add a "Problem-Solving Mindset" section at the end.

Format:
Use LaTeX for math. Use traditional Chinese for explanations.
"""

# --- 2. 網頁介面設定 ---
st.set_page_config(page_title="HKDSE 數學 5** 導師 (Local AI)", page_icon="📐", layout="wide")

st.title("🎓 HKDSE 數學 5** 解題助手 (本地版)")
st.markdown("""
**這是離線版本 (Local AI)**。
我們使用您電腦上的 **Llama 3.2 Vision** 模型來解題，**無需 API Key**，無地區限制。
""")

# --- 3. 側邊欄：模型狀態 ---
with st.sidebar:
    st.header("設定")
    st.success("🟢 使用本地模型：Llama 3.2 Vision")
    st.info("請確保您已安裝 Ollama 並執行過 `ollama run llama3.2-vision`")

# --- 4. 主程式邏輯 ---
uploaded_file = st.file_uploader("📸 請上傳題目圖片 (JPG/PNG)", type=["jpg", "png", "jpeg"])

if uploaded_file is not None:
    # 顯示圖片
    image = Image.open(uploaded_file)
    st.image(image, caption='已上傳的題目', width=400)

    if st.button("🚀 開始解題 (Local Solve)"):
        try:
            with st.spinner('🧠 本地 AI 正在思考中... (速度取決於您的電腦效能)'):
                
                # 將圖片轉換為二進制格式傳給 Ollama
                img_byte_arr = io.BytesIO()
                image.save(img_byte_arr, format=image.format)
                img_bytes = img_byte_arr.getvalue()

                # 發送請求給本地 Ollama
                response = ollama.chat(
                    model='llama3.2-vision',
                    messages=[{
                        'role': 'user',
                        'content': SYSTEM_PROMPT + "\n\nSolve this problem based on the image.",
                        'images': [img_bytes]
                    }]
                )
                
                # 顯示結果
                st.success("分析完成！")
                st.markdown("---")
                st.markdown(response['message']['content'])
                
        except Exception as e:
            st.error(f"發生錯誤：請檢查 Ollama 是否正在執行。\n錯誤訊息：{e}")

# --- 5. 頁尾 ---
st.markdown("---")
st.markdown("Powered by Local Llama 3.2 Vision • Privacy Focused")
