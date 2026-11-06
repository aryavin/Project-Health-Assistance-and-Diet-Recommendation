# 🥗 AI Health Assistant

A Streamlit app that calculates your BMI, BMR, TDEE and daily calorie target, then uses **RAG (Retrieval-Augmented Generation)** with an LLM to create a personalised one-day diet plan and answer nutrition questions.

> ⚠️ This app provides general health and nutrition information for educational purposes only. It is **not** medical advice.

---

## ✨ Features

- **Health metrics**: BMI, BMR (Mifflin-St Jeor), TDEE and calorie target based on your aim (maintain / loss / gain)
- **Diet Recommendation**: 5-meal one-day plan (Breakfast, Morning Snack, Lunch, Evening Snack, Dinner) with portion, calories and protein
- **Health Assistant**: ask nutrition questions, answered using a nutrition PDF as the knowledge base
- **RAG pipeline**: PDF → chunks → embeddings → FAISS vector store → top-3 retrieval → LLM

## 🧰 Tech Stack

| Part | Tool |
|---|---|
| UI | Streamlit |
| LLM | `openai/gpt-oss-120b` via Hugging Face Router (OpenAI-compatible client) |
| Embeddings | `sentence-transformers/all-MiniLM-L6-v2` |
| Vector DB | FAISS |
| RAG framework | LangChain |

## 📁 Project Structure

```
Project-Health-Assistance-and-Diet-Recommendation/
├── app.py               # Streamlit app (UI + prompts + LLM calls)
├── diet.py              # BMI, BMR, TDEE, calorie target functions
├── rag.py               # create_rag() and load_rag()
├── create_database.py   # One-time script to build the vector DB
├── llm_test.py          # Quick test for the LLM connection
├── prompt.md            # Prompt templates (diet + chatbot)
├── requirements.txt
├── .env.example         # Copy to .env and add your token
├── .gitignore
└── data/
    └── nutrition.pdf    # Your knowledge-base PDF (add this yourself)
```

## 🚀 Setup (Step by Step)

### 1. Clone the repo
```bash
git clone https://github.com/aryavin/Project-Health-Assistance-and-Diet-Recommendation
.git
cd Project-Health-Assistance-and-Diet-Recommendation

```

### 2. Create a virtual environment
```bash
python -m venv venv
# Windows
venv\Scripts\activate
# Mac / Linux
source venv/bin/activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Add your Hugging Face token
Create a token at https://huggingface.co/settings/tokens, then:
```bash
cp .env.example .env
```
Open `.env` and set:
```
HF_vineet=your_huggingface_token_here
```

### 5. Add the knowledge base PDF
Put your nutrition PDF at `data/nutrition.pdf`.

### 6. Build the vector database (run once)
```bash
python create_database.py
```
This creates a `vector_db/` folder.

### 7. Run the app
```bash
streamlit run app.py
```
Open http://localhost:8501

## 🧮 How the Calculations Work

- **BMI** = weight (kg) / height (m)²
- **BMR (Mifflin-St Jeor)**
  - Male: `10×weight + 6.25×height − 5×age + 5`
  - Female: `10×weight + 6.25×height − 5×age − 161`
- **TDEE** = BMR × activity factor

| Activity | Factor |
|---|---|
| Sedentary | 1.20 |
| Lightly Active | 1.375 |
| Moderately Active | 1.55 |
| Very Active | 1.725 |
| Extra Active | 1.90 |

- **Calorie target**: maintain = TDEE, loss = TDEE − 400, gain = TDEE + 300

## 🔄 How RAG Works Here

1. `create_rag()` loads the PDF and splits it into 500-character chunks (50 overlap)
2. Each chunk is embedded with MiniLM and stored in FAISS (`vector_db/`)
3. On a user request, `load_rag()` loads the index and retrieves the 3 most similar chunks
4. The chunks + user data are inserted into the prompt and sent to the LLM
5. The answer is displayed in Streamlit

## 🛠️ Troubleshooting

- **`FileNotFoundError`**: check that `data/nutrition.pdf` exists
- **"Rag is not connected"**: run `python create_database.py` first
- **401 / auth error**: check your token in `.env`
- **First run is slow**: the embedding model downloads once

## 🔮 Future Improvements

- Weekly meal plans
- Multiple allergy selection
- Chat history in the Health Assistant tab
- Docker + Streamlit Cloud deployment

## 📄 License

MIT
