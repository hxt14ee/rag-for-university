# 🎓 Local AI University Consultant (RAG Pipeline)

**Приватная система ответов на вопросы по документам ВУЗа.**
Превращает сухие PDF-регламенты в точные ответы.
<img width="1480" height="485" alt="image" src="https://github.com/user-attachments/assets/1a4db848-7f4c-433f-9efb-7076a0e60fca" />

![n8n](https://img.shields.io/badge/Orchestration-n8n-FF6B6B)
![Qdrant](https://img.shields.io/badge/Vector_DB-Qdrant-D50032)
![Ollama](https://img.shields.io/badge/Local_LLM-Ollama-000000)
![Model](https://img.shields.io/badge/AI-Google_Gemma_3-4285F4)

## ⚡️ О проблеме и решении

**Проблема:** Абитуриенты и студенты теряются в бюрократии. Чтобы узнать про стипендию, индивидуальный график или общежитие, нужно перерыть десятки PDF-файлов со сложным юридическим языком. Деканат отвечает медленно, а информации слишком много.

**Решение:** Локальный RAG-агент (Retrieval-Augmented Generation), который работает как **"Цифровой Методист"**. Он не выдумывает факты, а находит конкретные абзацы в официальных приказах и переводит их на человеческий язык. Всё работает **локально и приватно**, без отправки документов в OpenAI.

### ✨ Ключевые возможности
* **Zero-Hallucination Policy:** Жесткий промпт-инжиниринг запрещает модели "додумывать". Если ответа нет в документе — бот честно об этом скажет.
* **Privacy First:** Полная автономность. Все документы и векторы хранятся на вашем сервере (Docker), LLM работает через Ollama.

---

## 🛠 Архитектура решения

Проект реализован в **n8n** и разделен на два независимых потока для оптимизации ресурсов.

```mermaid
graph LR
    subgraph "Ingestion Flow (Загрузка знаний)"
        PDF["📄 PDF Files"] -->|Upload| Splitter["✂️ Text Splitter"]
        Splitter -->|Chunk Size: 1000| Embed["🧠 Embedding Model"]
        Embed -->|Vectors| Qdrant[("🗄️ Qdrant DB")]
    end

    subgraph "Chat Flow (Диалог)"
        User["👤 Студент"] -->|Question| Chain["🔗 Conversational QA Chain"]
        Chain -->|Query Vector| Retriever["🔍 Vector Retriever"]
        Retriever -.->|Search Top-20| Qdrant
        Qdrant -.->|Context| Chain
        Chain -->|Context + Strict Prompt| LLM["🤖 Gemma 3 (Ollama)"]
        LLM -->|Final Answer| User
    end
