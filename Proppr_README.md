# 🏠 Proppr Property Parser — Production

A production-grade n8n workflow that automatically reads raw property requirement messages from Google Sheets, extracts structured data using a **locally hosted Ollama AI (LLaMA 3)**, validates the output, and saves clean structured records — with full error handling, routing, and logging.

> Built by [keviv777](https://github.com/keviv777) as part of the [india-ai-agent-toolkit](https://github.com/keviv777/india-ai-agent-toolkit)

---

## 🧠 How It Works

```
[Start Parser — Scheduled/Manual Trigger]
 │
 ▼
[Read Raw Messages — Google Sheets]
 │
 ▼
[Filter and Classify]
 │
 ▼
[Route Messages]
 ├── Valid ──► [Split Multi Requirements]
 │                 │
 │                 ▼
 │          [Extract with Ollama AI]  ◄── POST http://localhost:11434
 │                 │
 │                 ▼
 │          [Validate and Parse AI Response]
 │                 │
 │                 ▼
 │          [Route Parsed Results]
 │           ├── Success ──► [Save to Structured Output — Sheets]
 │           └── Failed  ──► [Log Errors — Sheets]
 │                           [Save Failed to Structured Output]
 │                           [Mark as Processed — Sheets]
 └── Skipped ──► [Save Skipped Messages — Sheets]
```

---

## ✨ Features

- ✅ **Production-grade pipeline** — full error handling, logging, and routing
- ✅ **AI-powered extraction** — pulls price, location, BHK, area, amenities from raw text
- ✅ **Zero API cost** — uses self-hosted Ollama (no OpenAI / cloud AI billing)
- ✅ **Smart routing** — success / failed / skipped paths handled separately
- ✅ **Duplicate prevention** — marks processed records to avoid re-parsing
- ✅ **Google Sheets as database** — no external DB required
- ✅ **Handles multi-requirement messages** — splits and processes each requirement individually

---

## 🔧 Tech Stack

| Tool | Purpose |
|------|---------|
| n8n (self-hosted) | Workflow orchestration |
| Ollama (LLaMA 3) | Local AI for data extraction |
| Google Sheets | Input data + structured output storage |
| JavaScript (Code nodes) | Data validation + JSON parsing |

---

## 📊 What Data Gets Extracted

From a raw message like:
> *"Need 2BHK in Andheri West, budget 45k, prefer furnished, available immediately"*

The AI extracts:

```json
{
  "bhk": "2BHK",
  "location": "Andheri West",
  "budget": "45000",
  "furnished": "furnished",
  "availability": "immediate",
  "amenities": [],
  "raw_message": "Need 2BHK in Andheri West..."
}
```

---

## 🗂️ Google Sheets Structure

### Input Sheet (Raw Messages)
| Column | Description |
|--------|-------------|
| `message` | Raw property requirement text |
| `source` | Where message came from (WhatsApp, form, etc.) |
| `processed` | TRUE/FALSE — marks if already parsed |
| `timestamp` | When message was received |

### Output Sheet (Structured Data)
| Column | Description |
|--------|-------------|
| `bhk` | Number of bedrooms |
| `location` | Area/city preference |
| `budget` | Budget in INR |
| `furnished` | Furnished / semi / unfurnished |
| `availability` | Timeline / move-in date |
| `amenities` | List of required amenities |
| `raw_message` | Original message for reference |

### Error Log Sheet
| Column | Description |
|--------|-------------|
| `raw_message` | Message that failed |
| `error_reason` | Why parsing failed |
| `timestamp` | When error occurred |

---

## 🚀 Setup Instructions

### 1. Prerequisites
- n8n running (self-hosted recommended)
- Ollama installed locally: [ollama.ai](https://ollama.ai)
- LLaMA 3 model pulled: `ollama pull llama3`
- Google Sheets API credentials set up in n8n

### 2. Install Ollama + Pull Model

```bash
# Install Ollama (Mac)
brew install ollama

# Or Linux
curl https://ollama.ai/install.sh | sh

# Pull the model
ollama pull llama3

# Start Ollama server
ollama serve
# Runs at http://localhost:11434
```

### 3. Import Workflow
1. Download `Proppr_Property_Parser_CLEAN.json`
2. In n8n → **Import from file**
3. Connect your Google Sheets credentials

### 4. Configure Google Sheets
1. Create a Google Sheet with the input structure above
2. In n8n, update the **Read Raw Messages** node with your Sheet ID
3. Update all **append: sheet** nodes with your output Sheet IDs

### 5. Run
- **Manual:** Click "Execute workflow" in n8n
- **Scheduled:** Add a Schedule Trigger node (e.g., every 30 minutes)

---

## 📁 Files

```
proppr-property-parser/
├── Proppr_Property_Parser_CLEAN.json   # n8n workflow (import this)
└── README.md
```

---

## 💡 Use Cases

- Real estate agencies processing WhatsApp/form enquiries
- Property portals structuring raw lead data
- Rental platforms auto-classifying requirements
- Any business extracting structured data from messy text messages

---

## ⚠️ Important Notes

- Ollama must be running locally on port `11434` for the workflow to work
- For cloud deployment, update the Ollama URL to your server's address
- Google Sheets credentials must be configured in n8n's credential manager

---

## 📄 License

MIT — free to use, modify, and build upon.
