Python

import streamlit as st
from openai import OpenAI

# Page settings
st.set_page_config(
    page_title="Red AI",
    page_icon="🔴",
    layout="centered"
)

# Red AI design
st.markdown("""
<style>
.stApp {
    background-color: #160000;
    color: white;
}
h1 {
    color: #ff2020;
    text-align: center;
}
.stChatInput {
    border: 2px solid #ff2020;
}
</style>
""", unsafe_allow_html=True)

st.title("🔴 RED AI")
st.caption("Your Personal AI Assistant")

# API connection
try:
    client = OpenAI(api_key=st.secrets["OPENAI_API_KEY"])
except Exception:
    st.error("API key is not configured. Add it in Streamlit Secrets.")
    st.stop()

# Chat history
if "messages" not in st.session_state:
    st.session_state.messages = []

# Display old messages
for message in st.session_state.messages:
    with st.chat_message(message["role"]):
        st.markdown(message["content"])

# Chat input
prompt = st.chat_input("Ask Red AI anything...")

if prompt:
    st.session_state.messages.append(
        {"role": "user", "content": prompt}
    )

    with st.chat_message("user"):
        st.markdown(prompt)

    with st.chat_message("assistant"):
        try:
            response = client.chat.completions.create(
                model="gpt-4o-mini",
                messages=[
                    {
                        "role": "system",
                        "content": (
                            "You are Red AI, a friendly personal "
                            "assistant. Answer questions clearly "
                            "and help students understand subjects."
                        )
                    },
                    *st.session_state.messages
                ]
            )

            answer = response.choices[0].message.content
            st.markdown(answer)

            st.session_state.messages.append(
                {"role": "assistant", "content": answer}
            )

        except Exception as e:
            st.error(f"Something went wrong: {e}")

if st.button("Clear Chat"):
    st.session_state.messages = []
    st.rerun()
    
