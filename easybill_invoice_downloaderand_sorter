import os
import time
import requests
from pathlib import Path
from dotenv import load_dotenv
from datetime import datetime

# Load API key from .env file
load_dotenv()
API_KEY = os.getenv("EASYBILL_API_KEY")

# Define the year to filter invoices (adjust as needed)
YEAR = 2023

# Base URL for Easybill REST API
BASE_URL = "https://api.easybill.de/rest/v1"

# Target folder to store downloaded invoices
DESTINATION_BASE = Path(r"<replace>")

# Set up HTTP request headers
HEADERS = {
    "Authorization": f"Bearer {API_KEY}",
    "Accept": "application/json"
}

def fetch_invoices():
    """
    Fetch all outgoing invoices for the selected year using the Easybill API.
    Downloads are paginated. PDF files are stored in subfolders by month.
    """
    page = 1
    while True:
        print(f"📄 Loading page {page} ...")
        params = {
            "per_page": 100,
            "page": page,
            "document_type": "invoice",
            "min_date": f"{YEAR}-01-01",
            "max_date": f"{YEAR}-12-31"
        }

        response = requests.get(f"{BASE_URL}/documents", headers=HEADERS, params=params)

        if response.status_code == 429:
            print("⚠️ Rate limit reached. Waiting 10 seconds ...")
            time.sleep(10)
            continue

        response.raise_for_status()
        data = response.json()
        documents = data.get("items", [])

        if not documents:
            print("✅ No more documents found.")
            break

        for doc in documents:
            download_invoice(doc)

        page += 1
        time.sleep(1)  # Optional pause for rate-limiting safety (especially on PLUS accounts)

def download_invoice(doc):
    """
    Download a single invoice PDF from Easybill using the document ID.
    Saves the file in a folder named by its issue month (YYYY-MM).
    Falls back to 'Unsorted' if no document date is found.
    """
    doc_id = doc.get("id")
    doc_number = doc.get("document_number", f"invoice_{doc_id}")
    filename = f"{doc_number}.pdf"

    document_date = doc.get("document_date")

    # Determine subfolder based on invoice issue date
    if document_date:
        date_obj = datetime.strptime(document_date, "%Y-%m-%d")
        folder_name = date_obj.strftime("%Y-%m")
    else:
        folder_name = "Unsorted"

    target_dir = DESTINATION_BASE / folder_name
    target_dir.mkdir(parents=True, exist_ok=True)
    file_path = target_dir / filename

    if file_path.exists():
        print(f"🔁 Already exists: {filename}")
        return

    print(f"⬇️ Downloading: {filename}")
    pdf_url = f"{BASE_URL}/documents/{doc_id}/pdf"
    pdf_response = requests.get(pdf_url, headers=HEADERS)

    if pdf_response.status_code == 429:
        print("⚠️ Too many PDF downloads – waiting 10 seconds")
        time.sleep(10)
        return download_invoice(doc)  # Retry

    pdf_response.raise_for_status()
    with open(file_path, "wb") as f:
        f.write(pdf_response.content)

    print(f"✅ Saved: {filename} → {target_dir}")

if __name__ == "__main__":
    fetch_invoices()
