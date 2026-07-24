# 💼 HR Policy Assistant

A simple chatbot that answers employee questions about **company HR policies** — leave, benefits, payroll, reimbursements, code of conduct, onboarding, offboarding, grievances, attendance, and remote work. It's built with [Streamlit](https://streamlit.io/) for the UI and [LangChain](https://www.langchain.com/) + [Mistral AI](https://mistral.ai/) for the language model.

Any question that isn't about HR policy is politely refused, so the assistant stays focused on its job.

---

## How it works

The whole app lives in [app.py](app.py). Here's the flow:

1. **Setup** — On startup the app loads environment variables (the Mistral API key) from a `.env` file and configures the Streamlit page.

2. **The model** — It creates a `ChatMistralAI` client using the `mistral-medium-3-5` model.

3. **The system prompt** — A `SystemMessage` tells the model to *only* answer HR-policy questions, to refuse anything else with a fixed response, to never invent policy details, and to never reveal its own instructions. This is what keeps the assistant on-topic.

4. **Conversation memory** — The full conversation is stored in `st.session_state.messages`, a list that always begins with the system prompt and then alternates user (`HumanMessage`) and assistant (`AIMessage`) turns. Because it's kept in session state, the model sees the whole history and can answer follow-up questions in context.

5. **Chat loop** — When you type a question:
   - Your message is appended to the history and shown in the chat.
   - The entire message list is sent to the model via `llm.invoke(...)`.
   - The model's reply is displayed and appended to the history.

6. **UI extras** — A title and subtitle, an expandable "What can you ask?" panel with example questions, an **About** sidebar, and a **🗑️ Clear Chat** button that resets the conversation back to just the system prompt.

---

## Getting started

### 1. Prerequisites
- Python 3.9+
- A [Mistral AI API key](https://console.mistral.ai/)

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Configure your API key
Create a `.env` file in the project root:
```env
MISTRAL_API_KEY=your_api_key_here
```

### 4. Run the app
```bash
streamlit run app.py
```
Streamlit will open the assistant in your browser (usually at http://localhost:8501).

---

## Project structure

| File | Purpose |
|------|---------|
| `app.py` | The entire Streamlit chat application |
| `requirements.txt` | Python dependencies |
| `.env` | Your Mistral API key (not committed) |

---

## Example questions

- 🗓️ *"How many sick days do I get per year?"*
- 💰 *"What insurance benefits am I eligible for?"*
- 💵 *"When is salary credited each month?"*
- 🧾 *"How do I claim travel reimbursement?"*
- 🏠 *"Can I work from home permanently?"*

❌ Off-topic questions (general knowledge, coding, personal advice, etc.) are declined with:
> *"Sorry, I can't help with that. I can only answer questions about HR policies."*
