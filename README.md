# 🤖 Chatbot WiData

Assistente virtuale intelligente per **WiData Srl** — startup IoT e Smart Cities di Sassari — costruito con [Streamlit](https://streamlit.io/) e [Claude (Anthropic)](https://www.anthropic.com/), con supporto RAG su documenti PDF.

---

## ✨ Funzionalità

- 💬 **Chat conversazionale** con memoria della sessione (multi-turn)
- 📄 **RAG su PDF** — carica un documento e il chatbot risponde basandosi sul suo contenuto
- 🧠 **Powered by Claude Haiku** (`claude-haiku-4-5`) via API Anthropic con streaming in tempo reale
- 🛡️ **Guardrail di input** — blocco di prompt injection e limitazione a 2000 caratteri
- ⚙️ **Configurazione dinamica** dalla sidebar: nome chatbot, temperature, numero di chunk RAG
- 📊 **Monitoraggio token e costi** stimati in tempo reale
- 👍 **Feedback utente** con sistema thumbs up/down integrato
- 📝 **Logging conversazioni** su file `.jsonl` per audit e analisi

---

## 🏗️ Architettura

```
┌─────────────────────────────────────────┐
│              Streamlit UI               │
│  ┌──────────┐        ┌───────────────┐  │
│  │ Sidebar  │        │  Chat Window  │  │
│  │ Settings │        │  (streaming)  │  │
│  │ PDF Load │        └───────────────┘  │
│  └──────────┘                           │
└────────────────────┬────────────────────┘
                     │
          ┌──────────▼──────────┐
          │   RAG Pipeline      │
          │  chunka_testo()     │
          │  indicizza_pdf()    │
          │  cerca_rag()        │
          └──────────┬──────────┘
                     │
          ┌──────────▼──────────┐
          │  Anthropic Claude   │
          │  (Haiku streaming)  │
          └─────────────────────┘
```

Il testo dei PDF viene suddiviso in chunk con overlap, indicizzato in memoria e recuperato tramite keyword matching prima di ogni chiamata al modello.

---

## 📦 Requisiti

```
anthropic>=0.40.0
streamlit>=1.35.0
pypdf>=4.0.0
requests>=2.31.0
```

Installa le dipendenze con:

```bash
pip install -r requirements.txt
```

---

## 🚀 Avvio rapido

### In locale

1. **Clona il repository**
   ```bash
   git clone https://github.com/lorymasia/chatbot.git
   cd chatbot
   ```

2. **Crea un ambiente virtuale**
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/macOS
   venv\Scripts\activate     # Windows
   ```

3. **Installa le dipendenze**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configura la API key Anthropic**

   Crea il file `.streamlit/secrets.toml`:
   ```toml
   ANTHROPIC_API_KEY = "sk-ant-..."
   ```
   Oppure esporta come variabile d'ambiente:
   ```bash
   export ANTHROPIC_API_KEY="sk-ant-..."
   ```

5. **Avvia l'app**
   ```bash
   streamlit run app_completa.py
   ```
   
---

## 📁 Struttura del progetto

```
chatbot/
├── app_completa.py        # App principale Streamlit
├── requirements.txt       # Dipendenze Python
├── chat_log.jsonl         # Log conversazioni (generato a runtime)
```

---

## 🔧 Configurazione sidebar

| Parametro | Default | Descrizione |
|-----------|---------|-------------|
| Nome chatbot | `Chatbot WiData` | Titolo visualizzato nell'interfaccia |
| Temperature | `0.7` | Creatività delle risposte (0.0 = deterministico, 1.0 = creativo) |
| Chunk RAG | `3` | Numero di chunk PDF da includere nel contesto |

---

## 📝 Formato log conversazioni

Le conversazioni vengono salvate in `chat_log.jsonl`, una riga per interazione:

```json
{"timestamp": "2026-06-09T10:08:31.333452", "domanda": "Autonomia?", "risposta": "Il sensore dura 2 anni.", "len_contesto": 15}
```

---

## 🛡️ Sicurezza

- **Prompt injection**: filtro su pattern vietati (`ignore previous instructions`, ecc.)
- **Limite input**: massimo 2000 caratteri per messaggio
- **Grounding documentale**: il modello risponde _solo_ in base ai documenti caricati; se non ha informazioni, lo dichiara esplicitamente


---

## 📄 Licenza

Distribuito sotto licenza MIT. Vedi `LICENSE` per i dettagli.

---
