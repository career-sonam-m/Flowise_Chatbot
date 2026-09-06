# Ornativa Jewels - Flowise RAG Chatbot

A Retrieval-Augmented Generation (RAG) chatbot for **Ornativa Jewels** built visually using **Flowise** and **LangChain**. The chatbot ingests product catalogue data from `Jewellery Details.pdf` to answer customer inquiries, maintain multi-turn conversation memory, and provide grounded product recommendations.

- **Live Public Chatbot**: [https://cloud.flowiseai.com/chatbot/5a6f6405-1f10-43e8-b1b4-ac26fc6eb026](https://cloud.flowiseai.com/chatbot/5a6f6405-1f10-43e8-b1b4-ac26fc6eb026)  
  *Fully hosted 24/7 on Flowise Cloud — test the chatbot instantly in your browser on any device without local setup.*
- **Live Flowise Canvas**: [https://cloud.flowiseai.com/canvas/5a6f6405-1f10-43e8-b1b4-ac26fc6eb026](https://cloud.flowiseai.com/canvas/5a6f6405-1f10-43e8-b1b4-ac26fc6eb026)  
  *Access the workflow editor canvas on Flowise Cloud to view the live multi-node pipeline and inspect node configurations.*

> **Local Execution**: To set up and run this chatbot flow separately on your local machine, please follow the [Installation & Execution Guide](#installation--execution-guide) below.

---

## What is Flowise?

**Flowise** is an open-source visual tool for building customized Large Language Model (LLM) orchestration pipelines and AI applications using **LangChain**. It enables developers to visually connect components—such as document loaders, text splitters, vector stores, embedding models, and chat memory—on a drag-and-drop canvas to build RAG chatbots and AI agents without writing boilerplate code.

---

## Technical Stack & Configuration

| Component | Model / Tool | Configuration |
|-----------|--------------|---------------|
| **LLM** | OpenAI `gpt-4o-mini` | Temperature: `0.5`, Max Tokens: `300` |
| **Embeddings** | OpenAI `text-embedding-3-small` | Standard vector representation |
| **Text Splitter** | Recursive Character Splitter | Chunk Size: `1000`, Overlap: `100` |
| **Vector Store** | In-Memory Vector Store | Top-K Retrieval: `4` |
| **Memory** | Buffer Memory | Key: `chat_history` (multi-turn context) |
| **Orchestrator** | Conversational Retrieval QA Chain | Grounded prompts & source document attribution |

---

## RAG Pipeline Architecture

```
[Jewellery Details.pdf] ──> File Loader ──> Text Splitter (1000/100)
                                                    │
                                         OpenAI Embeddings
                                                    │
                                                    ▼
User Query ──> Conversational QA Chain ──> In-Memory Vector Store (Top 4)
                      ▲        │
                      │        ▼
               Buffer Memory  ChatOpenAI (gpt-4o-mini) ──> Response
```

---

## Prompt & Out-of-Stock Handling

### System Prompt (Response Prompt)
> *"You are Ornativa's virtual jewellery expert assistant. Be polite, welcoming, concise, and factual.*
> 
> *- If the user greets you (e.g. "hi", "hello", "good morning"), respond warmly as Ornativa's virtual jewellery expert and offer to assist them with exploring the jewellery catalogue.*
> *- For product questions, answer accurately using ONLY the provided context.*
> *- If an item is out of stock in the context, suggest a relevant in-stock alternative product from the catalogue.*
> *- If the question is unrelated to jewellery or greetings and cannot be answered from context, politely state that you can only answer questions regarding Ornativa's jewellery catalogue."*

### Out-of-Stock Strategy
If a customer inquires about an item marked out-of-stock in `Jewellery Details.pdf`:
1. The vector store retrieves the product page context showing unavailability alongside adjacent items.
2. The LLM politely informs the customer and recommends an in-stock alternative from the catalogue (e.g., *"Ruby Solitaire Ring is out of stock. You may like the Classic Diamond Ring instead."*).

---

## Installation & Execution Guide

### Prerequisites

Before running this project, ensure the following are installed on your system:

| Requirement | Minimum Version | How to Check |
|-------------|----------------|--------------|
| **Node.js** | v18.0.0 or higher | `node --version` |
| **npm** | v9.0.0 or higher | `npm --version` |
| **OpenAI API Key** | — | [Get one here](https://platform.openai.com/api-keys) |

### Step 1: Install Node.js (if not installed)

**Windows:**
1. Download the installer from [https://nodejs.org/](https://nodejs.org/) (LTS version recommended)
2. Run the installer and follow the prompts
3. Restart your terminal after installation
4. Verify installation:
   ```powershell
   node --version
   npm --version
   ```

**macOS (using Homebrew):**
```bash
brew install node
```

**Linux (Ubuntu/Debian):**
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Step 2: Install Flowise

**Option A — Global Install (Recommended):**
```powershell
npm install -g flowise
```

**Option B — Run without installing (using npx):**
```powershell
npx flowise start
```

> **Note:** If you encounter permission errors on Windows, run PowerShell as Administrator. On macOS/Linux, prefix with `sudo`.

### Step 3: Start Flowise

```powershell
npx flowise start
```

Flowise will start and display:
```
Flowise server is running on port 3000
```

Open your browser and navigate to: **http://localhost:3000**

> **Tip:** If port 3000 is already in use, start on a different port:
> ```powershell
> npx flowise start --port 3001
> ```

### Step 4: Import the Chatflow JSON

To execute this project in Flowise, load the pre-configured workflow JSON file (`Ornativa Jewels Chatbot Chatflow.json`):

1. Open your web browser and navigate to **[http://localhost:3000](http://localhost:3000)**.
2. In the Flowise interface, click **Chatflows** on the sidebar or dashboard.
3. Click the **`+ Add New`** button in the top-right corner.
4. On the workflow canvas, click the **Settings icon (⚙️)** in the upper-right corner.
5. Select **"Load Chatflow"** from the menu.
6. Browse to this project directory and select `Ornativa Jewels Chatbot Chatflow.json`.
7. The complete multi-node RAG pipeline will load onto your canvas.

### Step 5: Configure Credentials & Response Prompt

1. **API Credentials**: Attach your OpenAI API key to both the **OpenAI Embeddings** and **ChatOpenAI** nodes via the **Connect Credential** dropdown.
2. **Response Prompt (System Instructions)**: 
   - Click on the **Conversational Retrieval QA Chain** node on the canvas.
   - Click **Additional Parameters** at the bottom of the node (if collapsed).
   - Enter your prompt into the **Response Prompt** field:
     > *"You are Ornativa's virtual jewellery expert assistant. Be polite, welcoming, concise, and factual. Handle greetings warmly, answer product questions accurately using context, and suggest in-stock alternatives for unavailable items."*
   - *(Note: When importing `Ornativa Jewels Chatbot Chatflow.json`, this Response Prompt comes pre-configured inside the node.)*

### Step 6: Upload the Product PDF

1. Click on the **File Loader** node on the canvas
2. Click **"Upload File"**
3. Select `Jewellery Details.pdf` from this project folder
4. This PDF provides all product details used by the RAG system

### Step 7: Save & Execute the Chatbot

1. Click the **Save Icon (💾)** at the top right corner to save the chatflow
2. Flowise will index the document and build the RAG pipeline
3. Click the **Chat Icon (💬)** at the top right to open the interactive chat interface
4. Type your questions directly into the chat prompt to test!

### Step 8: Test with Sample Queries

Try these queries to verify everything works:

```
"What products do you have?"
"What is the price of the gold necklace?"
"What products are made of silver?"
"Do you have earrings in stock?"
"What would you recommend for a budget of ₹5000?"
"Tell me more about the last product you mentioned"  ← (tests multi-turn memory)
```

### Stopping Flowise

Press `Ctrl + C` in the terminal where Flowise is running.


