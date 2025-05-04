
Assessment Recommendation System using RAG & Gemini Embeddings
---------------------------------------------------------------

This project is a smart assessment recommendation engine that helps HRs, recruiters, and hiring platforms 
find the best-fit SHL assessments based on a simple natural language query. It uses a combo of Google's 
Gemini embeddings and FAISS for fast similarity-based retrieval. A lightweight Flask web app handles the 
frontend and API.

=======================
1. DATA COLLECTION
=======================

We scraped the SHL Product Catalog:
URL: https://www.shl.com/solutions/products/product-catalog/

Scraped 366 assessments using requests + BeautifulSoup.

Fields extracted:
- data-entity-id
- Assessment Name
- Relative URL
- Remote Testing (Yes/No)
- Adaptive/IRT (Yes/No)
- Test Type (e.g. Knowledge & Skills)
- Assessment Length (cleaned into numeric mins)

Example raw entry:
4038, C Programming (New), https://www.shl.com/..., Yes, No, Knowledge & Skills, 10

=======================
2. DATA CLEANING
=======================

Cleaned using pandas:
- Removed null/missing fields
- Converted assessment length to float
- Retained only relevant fields

Saved as:
assessment.csv

Sample final row:
3827, .NET Framework 4.5, Yes, Yes, Knowledge & Skills, 30.0

=======================
3. EMBEDDINGS & FAISS INDEX
=======================

We prepared the text by combining relevant metadata fields into one sentence per assessment.

Then:
- Used Google Gemini (via google-generativeai) to generate embeddings.
- Stored vectors in FAISS for fast nearest-neighbor search.
- Kept original text records (for display) in a separate pickle file.

Files produced:
- vector.index → FAISS index file
- vector_texts.pkl → Pickled original descriptions

=======================
4. RAG PIPELINE (rag_pipeline.py)
=======================

The RAG logic:
- Take user query
- Generate embedding using Gemini
- Search FAISS index for top-10 similar assessments
- Return results with key metadata (name, type, duration, etc.)

No large language model generation involved; just semantic search.

=======================
5. FLASK WEB APP
=======================

Simple interface with:
- Search box for entering query
- Table view of recommended assessments

Also exposes a REST endpoint.

=======================
6. API DETAILS
=======================

Endpoint: POST /api/search

Example Request:
{
  "query": "Looking for a Flutter developer test with personality screening, about 45 mins"
}

Example Response:
{
  "results": [
    "Android Development (New) | Type: Knowledge & Skills | Remote: Yes | Adaptive: No | Length: 7.0"
  ]
}

=======================
7. DESIGN DECISIONS
=======================

- URLs excluded from embeddings to reduce token size and increase relevance.
- Original entity IDs and links are still stored for display or future use.
- Embedding is modular — can swap Gemini with OpenAI or Cohere later.
- RAG here refers to retrieval + ranking, no generator model involved.

=======================
8. FUTURE IDEAS
=======================

- Add UI filters (duration, remote, test type, etc.)
- Return full URLs in results for easy linking
- Let user give feedback to improve re-ranking
- Add multi-language support
- Explore adding LLM summarization on top

=======================
9. FILE STRUCTURE
=======================

project_root/
│
├── data/
│   ├── assessment.csv
│   ├── vector.index
│   └── vector_texts.pkl
│
├── src/
│   ├── scrape_catalog.py
│   ├── rag_pipeline.py
│   ├── api.py
│   └── app.py
│
├── templates/
│   └── index.html
│
├── requirements.txt
└── README.txt (this file)

=======================
10. TECH USED
=======================

- Python 3.11
- FAISS
- Google Generative AI API (Gemini)
- Flask
- pandas
- BeautifulSoup
- pickle

Enjoy the project 🚀
