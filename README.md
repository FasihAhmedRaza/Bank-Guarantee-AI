# 🏦 Bank Guarantee AI

An intelligent Streamlit web application that extracts data from Bank Guarantee documents (PDF/Image) using Google Gemini AI, and generates formal confirmation letters in both **English** and **Arabic** with downloadable **Word (.docx)** and **PDF** formats.

---

## 📋 Table of Contents

- [Features](#-features)
- [Technology Stack](#-technology-stack)
- [Project Structure](#-project-structure)
- [Installation](#-installation)
- [Configuration](#-configuration)
- [Usage](#-usage)
- [Complete Flow Diagram](#-complete-flow-diagram)
- [Concepts Explained (Roman Urdu)](#-concepts-explained-roman-urdu)
- [Troubleshooting](#-troubleshooting)
- [License](#-license)

---

## ✨ Features

- 📄 Upload Bank Guarantee documents (PDF, PNG, JPG, JPEG)
- 🤖 AI-powered field extraction using Google Gemini
- ✏️ Editable fields for manual corrections
- 📝 Generates formal letters in English and Arabic
- 📥 Download as Word (.docx) and PDF
- 🔄 Fallback AI models for reliability
- 🔁 Retry logic with exponential backoff
- 🔒 Duplicate detection using file hashing
- 🌐 Deployed on Streamlit Cloud

---

## 🛠 Technology Stack

| Technology | Purpose |
|---|---|
| **Streamlit** | Web UI Framework |
| **Google Gemini AI** | AI/OCR Document Extraction |
| **PyMuPDF (fitz)** | PDF → Image Conversion |
| **python-docx** | Word Template Fill |
| **LibreOffice** | Word → PDF Conversion (Server) |
| **PIL/Pillow** | Image Processing |
| **hashlib** | Duplicate File Detection |

---

## 📁 Project Structure

```
Bank Guarantee AI/
├── app.py                    # Main Streamlit application
├── requirements.txt          # Python dependencies
├── packages.txt              # System packages (LibreOffice)
├── .streamlit/
│   └── secrets.toml          # API keys & configuration
├── Data/
│   └── رد ضمان عقد.docx      # Arabic/English Word template
├── venv/                     # Virtual environment
└── README.md                 # This file
```

---

## 🚀 Installation

### Prerequisites

- Python 3.9+
- Google Gemini API Key ([Get one here](https://aistudio.google.com/apikey))

### Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/fasihahmedraza/bank-guarantee-ai.git
   cd bank-guarantee-ai
   ```

2. **Create virtual environment**
   ```bash
   python -m venv venv
   ```

3. **Activate virtual environment**

   **PowerShell (if script execution is disabled):**
   ```powershell
   Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
   venv\Scripts\activate
   ```

   **Command Prompt:**
   ```cmd
   venv\Scripts\activate
   ```

4. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

5. **Run the app**
   ```bash
   streamlit run app.py
   ```

---

## ⚙️ Configuration

### Streamlit Secrets

Create `.streamlit/secrets.toml`:

```toml
GOOGLE_API_KEY = "your-google-gemini-api-key-here"
SHOW_STATUS = false
```

| Key | Description |
|---|---|
| `GOOGLE_API_KEY` | Your Google Gemini API key (required) |
| `SHOW_STATUS` | Show/hide status messages to client (default: `false`) |

### System Packages (for Streamlit Cloud)

The `packages.txt` file installs LibreOffice on Linux for PDF conversion:

```
libreoffice
```

---

## 📖 Usage

1. **Select Guarantee Type** → Tender Bond or Performance Bond
2. **Upload Document** → PDF or Image file
3. **AI Auto-Extracts** → Fields are populated automatically
4. **Review & Edit** → Modify any extracted fields if needed
5. **Generate Letter** → Click to create English + Arabic letters
6. **Download** → Get Word (.docx) or PDF file

---

## 🔷 Complete Flow Diagram

### Main Application Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    APP STARTS                                │
│              streamlit run app.py                             │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│              PAGE CONFIGURATION                              │
│  • Set page title: "Bank Guarantee AI"                       │
│  • Layout: centered                                          │
│  • Load SHOW_STATUS from secrets (True/False)                │
│  • Define MODELS_FALLBACK list                               │
│  • Set TEMPLATE_DIR & TEMPLATE_CONTRACT paths                │
│  • Define FIELD_KEYS list                                    │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│         GUARANTEE TYPE SELECTION                             │
│  ┌─────────────────────────────────────┐                     │
│  │  Select Box:                        │                     │
│  │   • Tender Bond Guarantee           │                     │
│  │   • Performance Bond Guarantee      │                     │
│  └─────────────────────────────────────┘                     │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│            API KEY CHECK                                     │
│  ┌──────────────────────────────────┐                        │
│  │ Read GOOGLE_API_KEY from secrets │                        │
│  └───────────────┬──────────────────┘                        │
│                  │                                            │
│          ┌───────┴────────┐                                  │
│          │  Key Valid?     │                                  │
│          └───┬────────┬───┘                                  │
│          NO  │        │ YES                                   │
│          ▼   │        │                                       │
│    ┌─────────┴──┐     │                                      │
│    │ Show Error │     │                                      │
│    │ st.stop()  │     ▼                                      │
│    └────────────┘  Continue                                  │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│              FILE UPLOAD                                      │
│  ┌─────────────────────────────────────┐                     │
│  │  st.file_uploader()                 │                     │
│  │  Accepts: PDF, PNG, JPG, JPEG       │                     │
│  └───────────────┬─────────────────────┘                     │
│                  │                                            │
│          ┌───────┴────────┐                                  │
│          │ File Uploaded?  │                                  │
│          └───┬────────┬───┘                                  │
│          NO  │        │ YES                                   │
│          ▼   │        ▼                                       │
│     Wait for │   Continue to                                 │
│     upload   │   Processing                                  │
└──────────────┴──────────┬───────────────────────────────────┘
                          │
                          ▼
```

### File Processing Flow

```
┌─────────────────────────────────────────────────────────────┐
│              FILE PROCESSING                                 │
│                                                              │
│  file_bytes = uploaded_file.read()                           │
│  file_hash = SHA256(file_bytes)                              │
│                                                              │
│          ┌────────────────────────┐                          │
│          │   What is file type?   │                          │
│          └───┬────────────────┬───┘                          │
│              │                │                               │
│         PDF  │          IMAGE │                               │
│              ▼                ▼                               │
│  ┌───────────────┐  ┌────────────────┐                      │
│  │ _pdf_to_images│  │ PIL.Image.open │                      │
│  │               │  │                │                       │
│  │  For each     │  │ Single image   │                      │
│  │  page (max 5):│  │ returned       │                      │
│  │  • load_page()│  └───────┬────────┘                      │
│  │  • get_pixmap │          │                                │
│  │    (200 DPI)  │          │                                │
│  │  • Convert to │          │                                │
│  │    PIL Image  │          │                                │
│  └───────┬───────┘          │                                │
│          │                  │                                │
│          └──────┬───────────┘                                │
│                 ▼                                             │
│     ┌───────────────────┐                                    │
│     │  images[] list     │                                   │
│     │  (PIL Images)      │                                   │
│     └─────────┬─────────┘                                    │
│               │                                              │
│               ▼                                              │
│     ┌───────────────────┐                                    │
│     │ st.image(preview) │                                    │
│     │ Show pages to user│                                    │
│     └─────────┬─────────┘                                    │
└───────────────┼──────────────────────────────────────────────┘
                │
                ▼
```

### AI Extraction Flow

```
┌──────────────────────────────────────────────────────────────────┐
│         extract_fields_from_images()                              │
│                                                                   │
│  1. BUILD PROMPT                                                  │
│     "Extract data from bank guarantee document...                 │
│      Return JSON with: date, bank_name, bank_name_ar,             │
│      guarantee_number, guarantee_date, amount,                    │
│      company_name, company_name_ar, guarantee_type_ar"            │
│                                                                   │
│  2. BUILD CONTENTS                                                │
│     contents = [prompt_text]                                      │
│     for each image:                                               │
│       img → PNG bytes → Part.from_bytes()                         │
│       contents.append(image_part)                                 │
│                                                                   │
│  3. MODEL FALLBACK + RETRY LOOP                                   │
│                                                                   │
│     Model 1: gemini-2.5-flash-lite                                │
│     ┌──────────────────────────────────────────────┐              │
│     │  Attempt 1 → API Call                        │              │
│     │     ├── SUCCESS → Parse JSON → Return ✅     │              │
│     │     └── FAIL → Wait 3 sec                    │              │
│     │  Attempt 2 → API Call                        │              │
│     │     ├── SUCCESS → Parse JSON → Return ✅     │              │
│     │     └── FAIL → Wait 6 sec                    │              │
│     │  Attempt 3 → API Call                        │              │
│     │     ├── SUCCESS → Parse JSON → Return ✅     │              │
│     │     └── FAIL → Switch to next model 🔄       │              │
│     └──────────────────────────────────────────────┘              │
│                                                                   │
│     Model 2: gemini-2.0-flash-lite  (same retry logic)            │
│     Model 3: gemini-2.5-flash       (same retry logic)            │
│     Model 4: gemini-3-flash-preview (same retry logic)            │
│                                                                   │
│     All Models Failed → st.error() → raise Exception ❌          │
└──────────────────────────────────────────────────────────────────┘
```

### JSON Extraction from AI Response

```
┌─────────────────────────────────────────────────────────────┐
│          _extract_json(text)                                 │
│                                                              │
│   AI Raw Response:                                           │
│   "Here is the data: {\"date\":\"04 Dec\"} done"            │
│                                                              │
│   Step 1: text.strip()                                       │
│           │                                                  │
│           ▼                                                  │
│   ┌─────────────────────────────────┐                        │
│   │ Starts with { AND Ends with } ? │                        │
│   └─────┬──────────────────┬────────┘                        │
│     YES │                  │ NO                               │
│         ▼                  ▼                                  │
│   json.loads(text)     Find { and } positions                │
│         │              json.loads(text[start:end+1])          │
│     ┌───┴───┐              │                                 │
│  SUCCESS  FAIL         ┌───┴───┐                             │
│     │       │       SUCCESS  FAIL                            │
│     ▼       ▼          │       │                              │
│  Return   Step 2    Return   Return                          │
│  dict ✅             dict ✅  None ❌                         │
└─────────────────────────────────────────────────────────────┘
```

### Generate Letter Flow

```
┌─────────────────────────────────────────────────────────────┐
│         "Generate Letter" CLICKED                            │
│                                                              │
│   ┌─────────────────────────────────────┐                    │
│   │ All fields filled?                  │                    │
│   └──────┬──────────────────┬───────────┘                    │
│      NO  │                  │ YES                             │
│      ▼   │                  ▼                                 │
│  Show Error          build_letter_template(data)             │
│                              │                                │
│                    ┌─────────┴──────────┐                     │
│                    │                    │                      │
│                    ▼                    ▼                      │
│          _build_english_letter   _build_arabic_letter         │
│                    │                    │                      │
│                    │    ════════════    │                      │
│                    └────────┬───────────┘                     │
│                             │                                 │
│                             ▼                                 │
│                    Display in UI                              │
│                    📝 English Letter                          │
│                    📝 Arabic Letter                           │
│                             │                                 │
│                             ▼                                 │
│                    fill_word_template(data)                   │
│                             │                                 │
│                    ┌────────┴────────┐                        │
│                    │                 │                         │
│                    ▼                 ▼                         │
│            📄 Word Download   convert_docx_to_pdf()          │
│                                     │                         │
│                              ┌──────┴──────┐                  │
│                           SUCCESS        FAIL                 │
│                              │              │                  │
│                              ▼              ▼                  │
│                        📕 PDF Download   Warning              │
└─────────────────────────────────────────────────────────────┘
```

### Word → PDF Conversion Flow

```
┌─────────────────────────────────────────────────────────────┐
│       convert_docx_to_pdf()                                  │
│                                                              │
│   1. Find LibreOffice binary (soffice / libreoffice)         │
│                                                              │
│   ┌────────────────────────┐                                 │
│   │ LibreOffice found?     │                                 │
│   └────┬──────────────┬────┘                                 │
│    NO  │              │ YES                                   │
│    ▼   │              ▼                                       │
│  Return None    2. Save docx to temp folder                  │
│                 3. Run: soffice --headless --convert-to pdf   │
│                 4. Read generated PDF bytes                   │
│                 5. Return pdf_bytes ✅                        │
└─────────────────────────────────────────────────────────────┘
```

### Session State Data Flow

```
┌─────────────────────────────────────────────────────────────┐
│    SESSION STATE (Persists across Streamlit re-runs)         │
│                                                              │
│   KEY                  │  EXAMPLE VALUE                      │
│   ─────────────────────┼────────────────────────────────     │
│   date                 │  "04 December 2025"                 │
│   bank_name            │  "Abu Dhabi Islamic Bank"           │
│   bank_name_ar         │  "بنك أبوظبي الإسلامي"              │
│   guarantee_number     │  "OLGAE061250000983"                │
│   guarantee_date       │  "08-December-2025"                 │
│   amount               │  "AED 140,000.00"                   │
│   company_name         │  "XYZ Trading LLC"                  │
│   company_name_ar      │  "شركة أكس واي زد للتجارة"           │
│   guarantee_type_ar    │  "ضمان عطاء"                         │
│   last_extracted_hash  │  "2cf24dba5fb0a30e..."              │
│                                                              │
│   WRITERS:                    READERS:                        │
│   • Auto-extract on upload    • Text input fields (UI)       │
│   • "Extract With AI" button  • "Generate Letter" button     │
│   • User manual edits         • fill_word_template()         │
│                               • Hash comparison              │
└─────────────────────────────────────────────────────────────┘
```

### End-to-End Summary

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌───────────┐    ┌──────────┐
│  SELECT  │    │  UPLOAD  │    │    AI    │    │  REVIEW   │    │ GENERATE │
│  TYPE    │───▶│  PDF/    │───▶│ EXTRACT  │───▶│  & EDIT   │───▶│ LETTER   │
│          │    │  IMAGE   │    │ FIELDS   │    │  FIELDS   │    │          │
└──────────┘    └──────────┘    └──────────┘    └───────────┘    └────┬─────┘
                                                                      │
                     ┌────────────────────────────────────────────────┘
                     │
                     ▼
          ┌─────────────────────┐
          │      OUTPUT          │
          │                     │
          │  📝 English Letter  │
          │  📝 Arabic Letter   │
          │  📄 Word Download   │
          │  📕 PDF Download    │
          └─────────────────────┘
```

---

## 📚 Concepts Explained (Roman Urdu)

### 1. Imports Ka Kaam

| Import | Kya Karta Hai |
|---|---|
| `hashlib` | File ka unique fingerprint banata hai (duplicate check) |
| `io` | Memory mein file read/write (bina disk use kiye) |
| `json` | JSON data parse karna (AI ka response) |
| `os` | File paths aur folders handle karna |
| `tempfile` | Temporary files banane ke liye |
| `time` | Delay/wait ke liye (retry logic) |
| `fitz` | PyMuPDF - PDF ko images mein convert karta hai |
| `streamlit` | Web app banana (UI) |
| `python-docx` | Word (.docx) files padhna/likhna |
| `PIL` | Images handle karna |
| `google.genai` | Google Gemini AI API |

### 2. Streamlit Page Setup

```python
st.set_page_config(page_title="Bank Guarantee AI", layout="centered")
st.title("🏦 Bank Guarantee AI")
```

- `set_page_config` → Browser tab ka title set karta hai
- `st.title()` → Page par heading dikhata hai
- Jaise HTML mein `<title>` aur `<h1>` hota hai, waise hi yeh kaam karta hai

### 3. SHOW_STATUS Flag

```python
SHOW_STATUS = bool(st.secrets.get("SHOW_STATUS", False))
```

- **Purpose:** Client ko status messages (success, warning) dikhane ya chupane ke liye
- `st.secrets` → Streamlit ki secret file se value padhta hai (jaise `.env` file)
- `SHOW_STATUS = False` → Client ko koi status nahi dikhega

### 4. Models Fallback List

```python
MODELS_FALLBACK = [
    "gemini-2.5-flash-lite",
    "gemini-2.0-flash-lite",
    "gemini-2.5-flash",
    "gemini-3-flash-preview"
]
```

- Agar pehla AI model busy hai ya error de, to agla model try karo
- Jaise restaurant mein ek chef busy hai to doosra chef order le le

### 5. JSON Extraction

```python
def _extract_json(text):
```

- AI kabhi extra text bhi bhejta hai JSON ke saath
- Yeh function pehle pure text ko JSON parse karta hai
- Agar fail ho, to `{` aur `}` ke beech ka part nikalta hai

**Example:**
```python
text = 'Here is the result: {"name": "Ali"} hope this helps'
# Pehle pure text ko parse → FAIL
# Phir { se } tak ka part nikale → {"name": "Ali"} → SUCCESS
```

### 6. PDF to Images

```python
def _pdf_to_images(pdf_bytes, max_pages=5):
```

- AI model directly PDF nahi padh sakta, images chahiye
- Har page ko 200 DPI quality mein image bana deta hai
- Maximum 5 pages tak (performance ke liye)
- Jaise aap PDF ka screenshot lete ho har page ka — yahi kaam yeh function karta hai programmatically

### 7. AI Se Data Extract Karna

```python
def extract_fields_from_images(client, images, guarantee_type, model_name):
```

**Kaam:**
1. AI ko images bhejo with a prompt
2. AI document padh kar JSON return kare with fields
3. Agar model busy ho → 3 baar retry karo with increasing wait time
4. Agar 3 retries fail → next model try karo

**Retry Logic:**
```
Attempt 1 fail → 3 sec wait → Attempt 2
Attempt 2 fail → 6 sec wait → Attempt 3
Attempt 3 fail → Switch to next model
```

### 8. Letter Building (English + Arabic)

```python
def build_letter_template(data):
```

- English aur Arabic letter alag alag banta hai
- Dono ko `====...====` se separate kiya gaya hai
- Formal bank letter format follow karta hai

### 9. Word Template Fill

```python
def fill_word_template(data):
```

- `Data/رد ضمان عقد.docx` mein ek pre-designed Word template hai
- Template mein ek table hai jisme rows hain
- Har row mein Arabic (col 0) aur English (col 1) content hai
- Function data ko table cells mein inject karta hai
- Pehle run ka text replace karta hai, formatting same rehti hai

### 10. Word to PDF Conversion

```python
def convert_docx_to_pdf(docx_bytes):
```

- **LibreOffice** use karta hai (free, open-source)
- `--headless` = bina GUI ke background mein chale
- Temporary folder mein docx save karo → LibreOffice se PDF banao → PDF bytes return karo

**Flow:**
```
docx_bytes → temp/letter.docx → LibreOffice → temp/letter.pdf → pdf_bytes
```

### 11. Session State

```python
st.session_state.setdefault("date", "")
```

- **Problem:** Streamlit har interaction par pura script re-run karta hai
- **Solution:** `session_state` mein data save karo jo persist rahe
- `setdefault` = agar key nahi hai to default value set karo, warna rehne do

### 12. File Hash (Duplicate Check)

```python
file_hash = hashlib.sha256(file_bytes).hexdigest()
```

- Har file ka unique hash banata hai
- Agar same file dobara upload ho → AI call skip karo (already extracted)
- Naya file upload ho → AI call karo

### 13. Download Buttons

```python
st.download_button(label="📄 Download Word", data=docx_bytes, ...)
```

- `st.download_button` → Browser mein download button dikhata hai
- `data` = file ka content (bytes)
- `mime` = file type batata hai browser ko

---

## 🔧 Troubleshooting

### PowerShell Execution Policy Error

```
venv\Scripts\activate : cannot be loaded because running scripts is disabled
```

**Fix:**
```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

### VS Code Extension Installation Error

```
Failed to fetch: TypeError: Failed to fetch
```

**Fix:**
- Check internet connection
- Restart VS Code
- Install from command line:
  ```bash
  code --install-extension ms-python.python
  ```
- Clear cache: Delete `%APPDATA%\Code\Cache` folder

### PDF Download Not Working

- Ensure `packages.txt` contains `libreoffice`
- On local machine, install LibreOffice manually
- On Streamlit Cloud, it installs automatically

### API Key Error

- Ensure `GOOGLE_API_KEY` is set in `.streamlit/secrets.toml`
- Get a key from [Google AI Studio](https://aistudio.google.com/apikey)

---

## 📄 License

This project is for educational and internal use purposes.

---

## 👨‍💻 Author

**Fasih Ahmed Raza**

- GitHub: [@fasihahmedraza](https://github.com/fasihahmedraza)

---

> Built with ❤️ using Streamlit & Google Gemini AI