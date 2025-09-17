# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

FinXtract is an LLM-powered Flask web application that analyzes annual reports (PDFs) to extract structured insights. The application performs document structure detection, section text extraction, named entity recognition, question answering, and keyword trend analysis.

## Core Components

### Main Application Files
- `app.py` - Flask web application with routes for login, document analysis, Q&A, NER, and trend visualization
- `finchat_core.py` - Core PDF processing and OpenAI API integration module
- `templates/` - HTML templates for the web interface (login, structure, finchat, ner, trends)

### Key Data Files
- `goldstandard_keywordlist.txt` - Reference keywords for section detection in annual reports
- `keywords.txt` - User-defined keywords for analysis
- `uploads/` - Directory for uploaded PDF files

## Architecture

### PDF Processing Pipeline
1. **Document Upload** - PDFs uploaded via Flask file upload
2. **Table of Contents Detection** - Uses `find_toc_page()` to locate TOC by matching section titles
3. **Section Extraction** - Extracts section titles and page numbers using regex patterns
4. **GPT Processing** - Sends TOC text to OpenAI GPT-3.5-turbo for structured extraction
5. **Caching** - Full PDF text cached in memory (`pdf_text_cache`) for efficient querying

### Core Functions (`finchat_core.py`)
- `find_toc_page()` - Locates table of contents page by keyword matching
- `extract_all_sections_from_line()` - Extracts sections using regex pattern matching
- `send_toc_to_gpt()` - Processes TOC via OpenAI API for clean structure
- `query_section_from_pdf()` - Answers questions about specific document sections using GPT-4

### Web Application Routes
- `/` - Login page with hardcoded credentials (admin/vinnlp2024)
- `/structure` - Document structure analysis and Q&A interface
- `/finchat` - Section-specific question answering
- `/ner` - Named entity recognition using spaCy
- `/trends` - Keyword trend analysis with Plotly visualizations

## Development Commands

### Running the Application
```bash
python app.py
```

### Dependencies
The application requires:
- Flask and related packages
- OpenAI Python client
- pdfplumber for PDF processing
- spaCy with `en_core_web_sm` model for NER
- matplotlib and plotly for visualizations
- pandas for data processing

### Environment Configuration
Set up `.env` file with:
- `OPENAI_API_KEY` - OpenAI API key for GPT models
- `FLASK_SECRET_KEY` - Flask session secret key

## Data Processing Patterns

### Section Detection
Uses fuzzy matching against `goldstandard_keywordlist.txt` to identify relevant sections in annual reports. The system looks for patterns like "Section Title ... Page Number" in TOC pages.

### Question Answering
Implements a retrieval-augmented generation pattern where:
1. User specifies page range and question
2. System extracts relevant text from cached PDF
3. GPT-4 answers question based on extracted content

### Keyword Analysis
Tracks keyword frequency across document sections and generates trend visualizations using Plotly with cycling color schemes.

## Session Management
- Simple session-based authentication
- PDF content cached in global variables (`pdf_text_cache`, `toc_cache`)
- File uploads stored in `uploads/` directory with secure filename handling