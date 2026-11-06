# RAG-Driven Resume Assessment & Decision Intelligence System

An AI-assisted resume evaluation application that analyzes PDF resumes using **document parsing, measurable resume signals, retrieval-augmented generation (RAG), and a Groq-hosted large language model**.

The system compares resume content with a local recruiter/ATS guideline knowledge base and produces a structured assessment with an overall score, ATS compatibility feedback, strengths, issues, and prioritized recommendations.

> This is a resume feedback and learning tool. Its score is an AI-generated assessment, not an actual score from a company's Applicant Tracking System (ATS), and it does not predict or guarantee hiring outcomes.

## Key Features

- **PDF resume intake:** accepts PDF files up to 8 MB.
- **Resume text extraction:** extracts text from PDF pages with `pdfplumber`.
- **Document signal analysis:** measures page count, word count, bullet count, contact details, recognized sections, quantified metrics, and weak phrases.
- **RAG-based guideline retrieval:** uses TF-IDF vectors and cosine similarity to retrieve relevant guidance from a local JSON knowledge base.
- **Category-balanced retrieval:** retrieves screening guidelines across ATS formatting, content impact, skills, experience, education, red flags, and summary/contact details.
- **LLM-powered assessment:** sends resume text, measured signals, and retrieved guidance to a Groq-hosted model.
- **Structured feedback:** returns an overall score and verdict, ATS compatibility score, strengths, flaws with severity and suggested fixes, section feedback, and the top three priority improvements.
- **Evidence-conscious output:** instructs the model to ground feedback in the uploaded resume and provided screening guidelines instead of inventing candidate details.
- **Interactive web interface:** provides PDF upload, processing and error states, and a formatted results dashboard.

## System Architecture

```text
                 ┌──────────────────────────┐
                 │      Web Frontend       │
                 │  HTML / CSS / JavaScript│
                 └────────────┬─────────────┘
                              │ PDF upload
                              ▼
                 ┌──────────────────────────┐
                 │       Flask API          │
                 │     POST /api/analyze    │
                 └────────────┬─────────────┘
                              ▼
                 ┌──────────────────────────┐
                 │     Resume Parser        │
                 │        pdfplumber        │
                 │  Text + document signals│
                 └────────────┬─────────────┘
                              ▼
                 ┌──────────────────────────┐
                 │       RAG Engine         │
                 │  TF-IDF + cosine         │
                 │  similarity retrieval    │
                 └────────────┬─────────────┘
                              │ Relevant guideline context
                              ▼
                 ┌──────────────────────────┐
                 │       Groq LLM           │
                 │ Resume + signals + rules│
                 │    Structured JSON      │
                 └────────────┬─────────────┘
                              ▼
                 ┌──────────────────────────┐
                 │     Results Dashboard    │
                 │ Score, verdict, feedback │
                 └──────────────────────────┘
```

### Processing Workflow

1. A user selects a PDF resume in the frontend.
2. The frontend uploads it to the Flask API using the `resume` form field.
3. The backend validates the file type and enforces the 8 MB file-size limit.
4. The resume parser extracts text and computes document-level signals.
5. The RAG engine vectorizes the extracted resume text and retrieves relevant recruiter/ATS rules from the local knowledge base using TF-IDF and cosine similarity.
6. The backend builds a prompt containing the resume text, measured signals, and retrieved guidelines.
7. The Groq API generates a structured JSON assessment.
8. The frontend renders the returned scores, verdict, strengths, issues, section feedback, and priority fixes.

## Technology Stack

| Component | Technologies |
|---|---|
| Language | Python |
| Backend API | Flask, Flask-CORS |
| PDF extraction | pdfplumber |
| Retrieval | TF-IDF, cosine similarity |
| ML / text processing | scikit-learn |
| Generative AI | Groq API, configurable chat model |
| Configuration | python-dotenv |
| Frontend | HTML, CSS, JavaScript |
| Knowledge store | Local JSON file |

## Pre-trained BiGRU Model

The pre-trained BiGRU model is distributed as a GitHub Release asset to keep the binary file out of the source tree.

- **Download model:** [BiGRU_Model.keras](https://github.com/Raghu113-dev/RAG-Driven-Resume-Assessment-Decision-Intelligence-System/releases/download/v1.0/BiGRU_Model.keras)
- **Release page:** [v1.0 – BiGRU Model](https://github.com/Raghu113-dev/RAG-Driven-Resume-Assessment-Decision-Intelligence-System/releases/tag/v1.0)
- **File size:** 41.5 MB
- **SHA-256:** `d01fa32055cb8bb9404544983feeeefb850a2f4f493883007271afe763cb9645`

Verify the downloaded file's checksum before loading it. On Windows PowerShell, run:

```powershell
Get-FileHash .\\BiGRU_Model.keras -Algorithm SHA256
```

## Assessment Output

The backend requests a JSON assessment with the following information:

| Output | Description |
|---|---|
| Overall score | Model-generated score from 0 to 100 |
| Verdict | Likely Accepted, Borderline, or Likely Rejected |
| ATS compatibility | Score and resume-specific parsing risks |
| Strengths | Positive findings and why they matter |
| Flaws | Identified issues, severity, and actionable fixes |
| Quantification examples | Sample rewrites illustrating where measurable impact could be added |
| Section feedback | Notes for summary, experience, skills, education, and formatting |
| Priority fixes | Three improvements to address first |
| Document metadata | Page count, word count, bullet count, metric count, and recognized sections |

Scores and verdicts are generated at request time by the LLM. They are not fixed benchmark results and may vary across runs.

## Repository Structure

The current repository uses capitalized folder names and includes spaces in some filenames:

```text
RAG-Driven-Resume-Assessment-Decision-Intelligence-System/
├── Backend/
│   ├── app.py
│   ├── rag_engine.py
│   ├── resume_parser.py
│   ├── knowledge_base.json
│   └── requirements (1).txt
├── Frontend/
│   ├── index (1).html
│   ├── script .js
│   └── style .css
├── Samples/
├── README.md
└── .gitignore
```

The repository currently stores the README as `README .md` (with a space). GitHub's standard repository landing-page convention is `README.md`; consider renaming the file to `README.md` so it renders automatically.

## Getting Started

### Prerequisites

- Python 3.9 or later (use a version compatible with the pinned dependencies).
- A Groq API key.
- Git.

### 1. Clone the repository

```bash
git clone https://github.com/Raghu113-dev/RAG-Driven-Resume-Assessment-Decision-Intelligence-System.git
cd RAG-Driven-Resume-Assessment-Decision-Intelligence-System
```

### 2. Set up the backend environment

**Windows PowerShell**

```powershell
cd Backend
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r "requirements (1).txt"
```

**macOS / Linux**

```bash
cd Backend
python3 -m venv venv
source venv/bin/activate
pip install -r "requirements (1).txt"
```

### 3. Configure the Groq API key

Create a file named `.env` inside the `Backend/` directory:

```env
GROQ_API_KEY=your_groq_api_key
GROQ_MODEL=llama-3.3-70b-versatile
```

Get an API key from the [Groq Console](https://console.groq.com/keys).

**Security:** Never commit your real API key or upload your populated `.env` file. Keep it local and add `.env` to `.gitignore`.

### 4. Start the backend

From inside the `Backend/` directory, run:

```bash
python app.py
```

The Flask server starts on:

```text
http://localhost:5000
```

Check service status at:

```text
http://localhost:5000/api/health
```

### 5. Start the frontend

Open a second terminal, navigate to the frontend folder, and start a static server:

```bash
cd Frontend
python -m http.server 8080
```

Open [http://localhost:8080](http://localhost:8080) in your browser.

The frontend currently points to `http://localhost:5000`. For a deployed backend, update the `API_BASE` constant in `Frontend/script .js` to the backend URL.

## API Reference

### Health Check

**Endpoint:** `GET /api/health`

Returns service status, whether the Groq key is configured, and the configured model name.

### Analyze Resume

**Endpoint:** `POST /api/analyze`

**Content type:** `multipart/form-data`

| Parameter | Type | Required | Details |
|---|---|---|---|
| `resume` | PDF file | Yes | Resume PDF, maximum 8 MB |

The endpoint returns a JSON object containing the assessment fields listed above.

**Example request using cURL:**

```bash
curl -X POST http://localhost:5000/api/analyze \
  -F "resume=@sample_resume.pdf"
```

**Common error responses:**

| HTTP status | Meaning |
|---|---|
| 400 | Missing file, empty filename, unsupported file type, or file exceeds size limit |
| 422 | PDF cannot be read or contains no extractable text |
| 500 | Groq API key is not configured |
| 502 | Groq request failed or the model response could not be parsed as JSON |

## RAG Implementation

The retrieval component is implemented locally in `Backend/rag_engine.py`.

- Loads recruiter/ATS guideline entries from `Backend/knowledge_base.json`.
- Builds a TF-IDF representation using unigrams and bigrams with English stop words removed.
- Converts the uploaded resume text into a query vector.
- Calculates cosine similarity between the query and knowledge-base entries.
- Retrieves up to three relevant entries for each configured category.
- Passes the retrieved content to the LLM as context for the evaluation.

This combines explicit retrieval with LLM-based generation, making the assessment grounded in the project's local guideline collection.

## Limitations & Responsible Use

- This is an educational and portfolio project, not an official ATS product or hiring decision tool.
- The score is generated by an LLM and is not calibrated against a real company's hiring outcomes.
- The application currently evaluates a resume against general screening guidelines; it does not perform job-description-specific matching.
- Text-based PDF extraction may not work for scanned or image-only documents.
- The assessment depends on the availability, configured model, and rate limits of the Groq API.
- Do not use the result as the sole basis for hiring, rejection, or other consequential employment decisions. Human review is essential.

## Potential Enhancements

- Add job-description input and retrieve role-specific skill and keyword criteria.
- Add explainability views showing which retrieved rules informed each recommendation.
- Add automated tests for PDF validation, parser output, retrieval relevance, and API responses.
- Add authentication, request logging, and deployment configuration.
- Evaluate consistency and usefulness against a documented set of human-reviewed resumes.

## Author

**Raghu Datta Pailla**

- GitHub: [Raghu113-dev](https://github.com/Raghu113-dev)
- Repository: [RAG-Driven Resume Assessment & Decision Intelligence System](https://github.com/Raghu113-dev/RAG-Driven-Resume-Assessment-Decision-Intelligence-System)

---

*Developed as a portfolio project to demonstrate practical skills in Python, NLP, information retrieval, RAG, generative AI, API development, and frontend integration.*
