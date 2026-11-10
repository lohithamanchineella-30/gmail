# Spam and Fraud Detection for Gmail Emails

This project is a Flask web application that connects to your Gmail inbox, fetches recent emails via the Gmail API, and classifies them as **spam** or **not spam** using a machine learning model trained on SMS/email text.

The app stores analysis history and aggregate results in a local SQLite database and renders the UI using Jinja2 templates and Bootstrap.

## Tech stack

- **Backend**: Python (Flask), SQLite (`sqlite3`)
- **ML**: `scikit-learn` (Naive Bayes + TF–IDF), `pandas`
- **Gmail integration**: `google-api-python-client`, `oauth2client`, `httplib2`
- **Frontend**: Jinja2 templates, Bootstrap, jQuery

## Project structure (key files)

- `app.py` – main Flask application and routes
- `ML_code.py` – classifier training, saving, and loading
- `gmail.py` – Gmail API authentication and email fetching
- `database.py` – SQLite helper for users, history, and results
- `data/SMS.csv` – training data for the spam classifier (tab‑separated)
- `models/` – persisted ML model file(s), e.g. `vector1.pkl` (created automatically)
- `templates/` – HTML templates
- `static/` – CSS/JS assets

## Prerequisites

- **Python**: Python 3.10+ (project tested with Python 3.10)
- A Gmail account you can use for testing
- A Google Cloud project with Gmail API enabled and a downloaded `credentials.json`

## Setup

1. **Clone or copy the project**

   Place the project in a directory like:

   ```text
   f:\bca project\major_project
   ```

2. **Create and activate a virtual environment (recommended)**

   ```bash
   python -m venv .venv
   .venv\Scripts\activate
   ```

   On PowerShell, you may need:

   ```powershell
   .venv\Scripts\Activate.ps1
   ```

3. **Install dependencies**

   From the project root (`major_project`):

   ```bash
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

4. **Prepare Gmail API credentials**

   - In the Google Cloud Console, enable the **Gmail API**.
   - Create **OAuth client ID** credentials for a desktop or web application.
   - Download the JSON file and save it as **`credentials.json`** in the project root (same folder as `app.py`).

   The app will create a `token.json` file automatically on first successful authentication.

5. **Verify training data**

   Make sure the file `data/SMS.csv` exists and is readable.  
   It should be a tab‑separated file with two columns: label (`spam`/`ham`) and message text.

## Running the app

From the project root:

```bash
python app.py
```

By default, Flask will start on `http://127.0.0.1:5000/` (or `http://localhost:5000/`).

## End‑to‑end usage flow

1. **Sign up**
   - Visit `http://localhost:5000/`.
   - Click **Sign Up**, fill in the form, and submit.

2. **Sign in**
   - Click **Sign In** and log in using the same credentials.

3. **Authenticate with Gmail**
   - After sign in, click **Fetch Emails** in the navbar.
   - When prompted, the app will run `gmail.py` which:
     - Uses `credentials.json` + Gmail API to request access.
     - Opens a browser window for you to log in and grant permissions.
   - On success, a success message is shown and `token.json` is created.
   - If authentication fails, an error message will be flashed on the home page.

4. **Fetch emails**
   - On the **Fetch Emails** page, choose the maximum number of emails (default 100).
   - Click **Get Emails** to fetch message snippets from Gmail.

5. **Analyze emails**
   - Click **Analyze Emails**.
   - The app will:
     - Ensure a `models/vector1.pkl` model exists (training and saving one if needed).
     - Load the persisted model and vectorizer.
     - Classify each fetched email as **spam** or **not spam**.
     - Store per‑email history and aggregate results in the SQLite database.

6. **View history and results**
   - Use the **Results** nav link to view stored results/history (via the dedicated routes).

## Notes and troubleshooting

- **First run model training**: On the first analysis, the app will train a model from `data/SMS.csv`, create the `models/` directory if needed, and save `vector1.pkl` automatically.
- **Database**: The SQLite database file (`db.sqlite3`) is created automatically in the project root. Errors during table creation are logged to the Flask logger.
- **Gmail authentication issues**:
  - Ensure `credentials.json` is in the project root.
  - Delete `token.json` if you need to re‑authenticate.
  - Check the terminal logs for detailed error messages if authentication fails.

## Development

- For local debugging, Flask runs with `debug=True` in `app.py`.
- You can change the secret key and other configuration by editing `app.py` or using environment variables for production use.

## GitHub / repository notes

- This project is intended to be hosted on a **public GitHub repository** without an explicit open‑source license (default “all rights reserved”).
- **Do not commit secrets or local artifacts**:
  - `credentials.json` (Gmail OAuth client secrets)
  - `token.json` (Gmail access/refresh token)
  - `db.sqlite3` (local SQLite database)
  - IDE configuration folders like `.idea/` or `.vscode/`
- These paths are covered in the project’s `.gitignore` so they will not be added to Git by default.
- When creating the Git repository from the project root, you can run:

  ```bash
  git init
  git add .
  git commit -m "Initial commit for Gmail spam detection project"
  ```

  Then create a new repository on GitHub and push this local repo to it following GitHub’s instructions.

