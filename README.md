## 🧾 Easybill Invoice Automation Toolkit

A set of Python tools to automate downloading and organizing invoices from **Easybill** and local folders.  
Ideal for bookkeeping, accounting, or preparing documents for your **tax advisor**.

---

### 🔧 Features

#### ✅ `easybill_invoice_downloader.py`
- API KEY with Easybill Account
- Connects to the [Easybill API](https://www.easybill.de/api/) via bearer token
- Downloads all **outgoing invoices** for a selected year
- Automatically creates subfolders like `2023-01`, `2023-02`, ...
- Handles API rate limits and retries if needed
- [Documentation](https://support.easybill.de/hc/de/articles/115003808529-API-Key-REST-Schnittstelle)
- [API Overview](https://www.easybill.de/api/?srsltid=AfmBOoolH-nGTzyxQlnfNx3EtIIodwgISjfsP3aD57PNiVrwMxArNRwZ#/document/get_documents__id__pdf)

#### ✅ `ai_pdf_invoice_sorter.py`
- Scans a local folder of **incoming invoice PDFs**
- Tries to extract the invoice **date from the filename**
- If no date is found, it uses **GPT-4o** to extract the date from the PDF text
- Organizes all PDFs into monthly folders
- Saves unrecognized files into a fallback folder `Unsorted/`

---

### 📁 Example Folder Structure

```
invoices/
├── Eingangsrechnung_Musterfirma.pdf
└── Sorted/
    ├── 2023-01/
    │   └── Eingangsrechnung_Musterfirma.pdf
    └── Unsorted/
        └── keine_datumserkennung.pdf
```

```
outgoing/
├── 2023-03/
│   └── invoice_20231001.pdf
```

---

### ⚙️ Requirements

Install dependencies once via pip:

```bash
pip install openai requests pdfplumber python-dotenv
```

Create a `.env` file with your credentials:

```
# For AI PDF Sorting
OPENAI_API_KEY=sk-...

# For Easybill Downloader
EASYBILL_API_KEY=your_easybill_token_here
```

---

### 🚀 Usage

#### ▶️ 1. Download Easybill Invoices
```bash
python easybill_invoice_downloader.py
```

- Downloads all invoices from a specific year (editable via `YEAR = 2023`)
- Stores them in your target path organized by month

#### ▶️ 2. Sort Local Incoming PDFs
Link: [https://github.com/AntonioBlago/ai-invoice-organizer-pdf](https://github.com/AntonioBlago/ai-invoice-organizer-pdf)
```bash
python ai_pdf_invoice_sorter.py
```

- Analyzes all PDF files in your specified input folder
- Uses regex and/or GPT-4o to determine invoice dates
- Moves them to the appropriate `Sorted/YYYY-MM` folder

---

### 💡 Notes

- GPT-4o is only called when the filename does not contain a date
- API rate limits are handled gracefully (especially for Easybill PLUS plan)
- File movement is **copy-based** – original files remain untouched

---

### 🔐 API Key Security

Make sure you **do not commit your `.env` file** to Git! Add this to your `.gitignore`:

```
.env
```

---

### 📌 License

MIT – free for personal and commercial use.

### 📌 Contact
[SEO Antonio Blago](https://antonioblago.de)
