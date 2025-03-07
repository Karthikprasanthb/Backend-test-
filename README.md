
Here’s a structured breakdown for your GitHub upload.  

---

## **1. Folder Structure**
```
backend-takehome/
│── papers_fetcher/          # Python Module
│   ├── __init__.py
│   ├── fetch.py             # Fetches PubMed papers
│   ├── filter.py            # Filters non-academic authors
│   ├── save.py              # Saves results as CSV
│── cli.py                   # CLI Program
│── pyproject.toml           # Poetry Configuration
│── README.md                # Documentation
│── .gitignore
```

---

## **2. README.md (For GitHub)**  
Create a `README.md` file in the root folder.

```md
# Research Paper Fetcher

A Python CLI tool to fetch research papers from **PubMed**, filter authors affiliated with **pharmaceutical/biotech companies**, and save results as a **CSV file**.

## Features
- Fetches papers using the **PubMed API**.
- Filters **non-academic authors** from pharmaceutical/biotech companies.
- Saves results in a **CSV file**.
- Supports command-line arguments (`--help`, `--debug`, `--file`).
- Uses **Poetry** for package management.

## Installation

### Prerequisites
- Python **3.8+**
- [Poetry](https://python-poetry.org/docs/#installation)

### Steps
1. Clone the repository:
   ```sh
   git clone https://github.com/yourusername/backend-takehome.git
   cd backend-takehome
   ```

2. Install dependencies:
   ```sh
   poetry install
   ```

## Usage

### Fetch Papers
```sh
poetry run get-papers-list "cancer treatment"
```

### Save to a CSV File
```sh
poetry run get-papers-list "diabetes research" -f results.csv
```

### Debug Mode
```sh
poetry run get-papers-list "AI in medicine" -d
```

## Project Structure
```
backend-takehome/
│── papers_fetcher/
│   ├── fetch.py   # Fetches papers from PubMed
│   ├── filter.py  # Filters company-affiliated authors
│   ├── save.py    # Saves results as CSV
│── cli.py         # CLI program
│── pyproject.toml # Poetry configuration
│── README.md      # Documentation
```

## Dependencies
- **biopython**: For fetching PubMed data.
- **Poetry**: For package management.

## License
This project is licensed under the MIT License.
```

---

## **3. Python Code (For GitHub)**  

### **`papers_fetcher/fetch.py`**  
Fetches research papers from PubMed.

```python
import os
from typing import List, Dict
from Bio import Entrez

Entrez.email = "your-email@example.com"  # Replace with your email

def fetch_papers(query: str, max_results: int = 10) -> List[Dict]:
    """Fetch papers from PubMed based on a query."""
    try:
        handle = Entrez.esearch(db="pubmed", term=query, retmax=max_results)
        record = Entrez.read(handle)
        handle.close()

        paper_ids = record["IdList"]
        if not paper_ids:
            return []

        handle = Entrez.efetch(db="pubmed", id=paper_ids, retmode="xml")
        papers = Entrez.read(handle)
        handle.close()

        return papers["PubmedArticle"]

    except Exception as e:
        print(f"Error fetching papers: {e}")
        return []
```

---

### **`papers_fetcher/filter.py`**  
Filters non-academic authors based on affiliation.

```python
from typing import List, Dict

ACADEMIC_KEYWORDS = ["university", "institute", "college", "school", "lab", "research"]
COMPANY_KEYWORDS = ["pharma", "biotech", "inc.", "ltd.", "gmbh", "corp", "llc"]

def is_company(affiliation: str) -> bool:
    """Check if an affiliation is from a company."""
    return any(word in affiliation.lower() for word in COMPANY_KEYWORDS)

def filter_non_academic_authors(papers: List[Dict]) -> List[Dict]:
    """Filter papers to include only those with at least one company-affiliated author."""
    results = []
    for paper in papers:
        authors = paper.get("MedlineCitation", {}).get("Article", {}).get("AuthorList", [])
        non_academic_authors = [
            author for author in authors if "AffiliationInfo" in author and is_company(author["AffiliationInfo"][0]["Affiliation"])
        ]
        
        if non_academic_authors:
            results.append({
                "PubmedID": paper["MedlineCitation"]["PMID"],
                "Title": paper["MedlineCitation"]["Article"]["ArticleTitle"],
                "Publication Date": paper["MedlineCitation"]["Article"]["ArticleDate"][0]["Year"],
                "Non-academic Author(s)": [author["LastName"] for author in non_academic_authors],
                "Company Affiliation(s)": [author["AffiliationInfo"][0]["Affiliation"] for author in non_academic_authors],
                "Corresponding Author Email": "N/A"  # PubMed does not always provide emails
            })
    return results
```

---

### **`papers_fetcher/save.py`**  
Saves the filtered results to a CSV file.

```python
import csv
from typing import List, Dict

def save_to_csv(data: List[Dict], filename: str):
    """Save extracted paper data to a CSV file."""
    if not data:
        print("No data to save.")
        return

    with open(filename, mode="w", newline="", encoding="utf-8") as file:
        writer = csv.DictWriter(file, fieldnames=data[0].keys())
        writer.writeheader()
        writer.writerows(data)

    print(f"Saved results to {filename}")
```

---

### **`cli.py`**  
Command-line interface to run the program.

```python
import argparse
from papers_fetcher.fetch import fetch_papers
from papers_fetcher.filter import filter_non_academic_authors
from papers_fetcher.save import save_to_csv

def main():
    parser = argparse.ArgumentParser(description="Fetch research papers from PubMed.")
    parser.add_argument("query", type=str, help="Search query for PubMed")
    parser.add_argument("-d", "--debug", action="store_true", help="Enable debug mode")
    parser.add_argument("-f", "--file", type=str, help="Output CSV file name")

    args = parser.parse_args()

    if args.debug:
        print("Debug mode enabled")

    print(f"Fetching papers for query: {args.query}")
    papers = fetch_papers(args.query)

    filtered_papers = filter_non_academic_authors(papers)

    if args.file:
        save_to_csv(filtered_papers, args.file)
    else:
        for paper in filtered_papers:
            print(paper)

if __name__ == "__main__":
    main()
```

---

### **`pyproject.toml`**  
Poetry configuration.

```toml
[tool.poetry]
name = "backend-takehome"
version = "0.1.0"
description = "Fetch and filter research papers from PubMed"
authors = ["Your Name <your-email@example.com>"]

[tool.poetry.dependencies]
python = "^3.8"
biopython = "^1.80"

[tool.poetry.scripts]
get-papers-list = "cli:main"
```

---

## **4. Uploading to GitHub**
### **Step 1: Initialize Repository**
```sh
git init
git add .
git commit -m "Initial commit"
```

### **Step 2: Push to GitHub**
```sh
git branch -M main
git remote add origin https://github.com/yourusername/backend-takehome.git
git push -u origin main
```

---

Now your project is ready for GitHub! Let me know if you need any modifications or explanations.
