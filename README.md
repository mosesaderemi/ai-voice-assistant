# AI Voice Assistant

A hands-free voice assistant that listens through the microphone, reasons with a large language model, performs tasks with tools such as Google Calendar, Google Contacts, Gmail and web search, and answers aloud.

## Overview

Speech is transcribed in real time with Deepgram. The transcript goes to a tool-calling agent that runs on a LiteLLM-compatible model (Llama 3 on Groq by default). The reply is converted back to speech with Deepgram text-to-speech and played through the speakers. The loop continues until you say "goodbye".

## Features

- Live speech-to-text over a Deepgram WebSocket stream
- Spoken replies using Deepgram text-to-speech
- Tool use through function calling:
  - Create events in Google Calendar
  - Add and look up contacts in Google Contacts
  - Send email through Gmail
  - Search the web with Tavily
  - Optional knowledge-base search over your own documents
- Model-agnostic: change one line in `main.py` to use another LiteLLM provider

## Tech Stack

| Area | Technology |
| --- | --- |
| Language | Python 3 |
| LLM access | LiteLLM, Groq, Google Gemini |
| Speech | Deepgram SDK, PyAudio, playsound |
| Retrieval | LangChain, Chroma, Google embeddings |
| Google services | Calendar API, People API (Contacts) |
| Search | Tavily |

## Project Structure

```text
.
├── main.py                           Entry point and tool registration
├── requirements.txt
├── .env.example
├── scripts/
│   ├── create_index.py               Builds a vector index from ./files
│   └── fetch_index.py                Tests retrieval against the index
└── src/
    ├── agents/agent.py               Tool-calling agent loop
    ├── prompts/prompts.py            System prompt
    ├── speech_processing/
    │   ├── speech_to_text.py         Deepgram live transcription
    │   ├── text_to_speech.py         Deepgram speech synthesis
    │   └── conversation_manager.py   Listen, think, speak loop
    ├── tools/
    │   ├── calendar/                 Google Calendar events
    │   ├── contacts/                 Add and fetch Google contacts
    │   ├── emails/                   Gmail sending
    │   └── search/                   Web search and knowledge base
    └── utils.py                      Google API scopes
```

## Getting Started

### Prerequisites

- Python 3.10 or later
- A working microphone and speakers
- Accounts and keys for Deepgram, Groq and Tavily
- A Google Cloud project with the Calendar and People APIs enabled and an OAuth desktop client
- A Gmail account with an app password for sending email

### Installation

```bash
git clone https://github.com/mosesaderemi/ai-voice-assistant.git
cd ai-voice-assistant
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

PyAudio may need a prebuilt wheel on Windows. On macOS install PortAudio first with `brew install portaudio`.

### Configuration

Copy `.env.example` to `.env` and set:

| Variable | Purpose |
| --- | --- |
| `GROQ_API_KEY` | Language model access |
| `DEEPGRAM_API_KEY` | Speech-to-text and text-to-speech |
| `TAVILY_API_KEY` | Web search tool |
| `GMAIL_MAIL` | Sender address for the email tool |
| `GMAIL_APP_PASSWORD` | Gmail app password |
| `GOOGLE_API_KEY` | Only needed for the optional knowledge-base tool |

Download your OAuth client file from Google Cloud, save it as `credentials.json` in the project root, and the first run will open a browser window to authorise access. A `token.json` file is then created for later runs. Both files are excluded from version control.

### Run

```bash
python main.py
```

Speak after the "Listening..." prompt. Say "goodbye" to end the session.

### Optional: knowledge base

Place documents in a `files` folder, run `python -m scripts.create_index`, and enable `KnowledgeSearchTool` in the `tools_list` in `main.py`.

## Security Notes

- Keep `.env`, `credentials.json` and `token.json` private.
- The email tool sends from your Gmail account, so review the prompts and permissions before using it with real contacts.

## Author

Aderemi Moses Timileyin
GitHub: [@mosesaderemi](https://github.com/mosesaderemi)
LinkedIn: [linkedin.com/in/mosesaderemi-6a70a5405](https://www.linkedin.com/in/mosesaderemi-6a70a5405)

## License

Released under the MIT License. See the `LICENSE` file for details.
