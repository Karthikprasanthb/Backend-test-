 README.md (For GitHub)**  
Create a `README.md` file in the root folder.

```md
Research Paper Fetcher

A Python CLI tool to fetch research papers from **PubMed**, filter authors affiliated with pharmaceutical/biotech companies, and save results as a CSV file

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



## Dependencies
- **biopython**: For fetching PubMed data.
- **Poetry**: For package management.

## License
This project is licensed under the MIT Licens.
