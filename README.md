# OCR + NER Correction System

## Overview

This Jupyter notebook demonstrates an advanced Natural Language Processing (NLP) pipeline that combines Optical Character Recognition (OCR) and Named Entity Recognition (NER) for processing mortgage documents. The system employs sophisticated NLP techniques to clean, correct, and extract structured information from OCR-processed text.

## System Architecture

The pipeline consists of several integrated components:

### 1. Optical Character Recognition (OCR) Processing
- Converts scanned documents and images into machine-readable text
- Handles various document formats and layouts
- Preserves text structure and formatting

### 2. Named Entity Recognition (NER)
- Uses state-of-the-art BERT models for entity detection
- Identifies key information like names, dates, addresses, and financial details
- Employs custom-trained models for domain-specific entity recognition

### 3. Text Processing & Cleaning
- Implements advanced text normalization techniques
- Handles spacing and formatting inconsistencies
- Uses SymSpell for spell checking and correction

### 4. Machine Learning Components
- Transformer-based models for contextual understanding
- Spacy's linguistic pipeline for text processing
- Custom entity extraction rules

## Data Structure

The system processes mortgage document data with the following structure:
- `row_id`: Unique identifier for each row
- `doc_id`: Document identifier
- `ocr_para_id`: Paragraph identifier within the document
- `ocr_para_type`: Type of paragraph (e.g., Title, Paragraph)
- `ocr_para_text`: The actual text content from OCR processing

## Processing Pipeline

### Phase 1: Data Loading and Exploratory Analysis

1. **Data Loading**: Loads mortgage offer data from Excel file
2. **Error Analysis**: Identifies common OCR errors including:
   - Extra whitespace (5.49% of rows)
   - Missing spaces between words (2.44% of rows)
   - Digit/letter substitutions (13.41% of rows)
   - Special character issues (8.54% of rows)
   - Case inconsistencies (40.85% of rows)

### Phase 2: Dictionary Setup and Word Frequency Analysis

1. **Frequency Dictionary Download**: Downloads a comprehensive English frequency dictionary (82,765 words)
2. **Word Frequency Analysis**: Analyzes text to identify:
   - 551 unique words in the dataset
   - 58 frequent words appearing 5+ times
   - Domain-specific terms not in standard dictionaries

3. **Dictionary Enhancement**:
   - Boosts priority of frequent words by 1000x
   - Adds protected words (HSBC, UK, PLC, APRC, etc.) with highest priority
   - Creates domain-specific corrections

### Phase 3: Text Cleaning and Correction

#### 3.1 Spacing Detection and Correction
```python
def segment_text(text):
    """
    Segments text by correcting spacing issues while preserving special cases.
    - Skips numbered list items
    - Preserves short words and codes
    - Uses SymSpell for word segmentation
    """
```

#### 3.2 Digit Detection and Correction
```python
def process_row(row):
    """
    Identifies and corrects digit-to-letter OCR errors:
    - 0 → o, 1 → l, 3 → e, 5 → s, etc.
    - Preserves postal codes, phone numbers, dates
    - Applies context-aware substitution rules
    """
```

#### 3.3 Date Format Correction
```python
def fix_dates(text):
    """
    Corrects common OCR errors in dates:
    - Handles patterns like 04/03/202S → 04/03/2025
    - Preserves various date formats
    """
```

### Phase 4: Spelling Detection and Correction

#### 4.1 Dictionary-Based Spelling Check
```python
def check_misspellings_symspell(text):
    """
    Detects misspelled words using SymSpell dictionary:
    - Identifies words not in dictionary
    - Excludes contractions and known terms
    - Returns list of potential misspellings
    """
```

#### 4.2 NER-Based Spelling Detection
```python
def find_unknown_words_with_ner(text, frequency_dict_path):
    """
    Uses NER to classify unknown words:
    - Loads BERT-based NER model (dslim/bert-base-NER)
    - Combines BERT and spaCy NER results
    - Classifies words as entities vs. truly unknown
    - Filters out special patterns (codes, numbers, etc.)
    """
```

#### 4.3 Spelling Correction
```python
def correct_misspelled_words(text: str, misspelled_words: list) -> str:
    """
    Corrects only identified misspelled words:
    - Preserves numbers, codes, and protected words
    - Uses SymSpell for correction suggestions
    - Maintains original text structure
    """
```

### Phase 6: Quality Assessment

```python
def calculate_confidence_score(original_text: str, cleaned_text: str) -> float:
    """
    Calculates confidence scores using multiple metrics:
    - Sequence similarity ratio (50% weight)
    - Word length similarity (25% weight)
    - Character count similarity (25% weight)
    - Returns combined confidence score (0-1)
    """
```

## Key Features

### Error Detection Capabilities
- **Spacing Issues**: Detects and corrects extra whitespace and missing spaces
- **Character Substitutions**: Handles common OCR digit/letter confusions
- **Date Corrections**: Fixes malformed dates and timestamps
- **Spelling Errors**: Identifies and corrects misspellings using dictionary lookup

### NER Integration
- **Entity Recognition**: Uses BERT-based models to identify named entities
- **Context Awareness**: Distinguishes between misspellings and valid entities
- **Domain Adaptation**: Handles mortgage-specific terminology

### Quality Assurance
- **Confidence Scoring**: Provides reliability metrics for corrections
- **Preservation Logic**: Maintains important codes, numbers, and formatting
- **Validation Rules**: Ensures corrections don't break document structure

## Output

The system produces:
1. **Cleaned Text**: Corrected version of original OCR text
2. **Confidence Scores**: Reliability metrics for each correction
3. **Error Analysis**: Detailed breakdown of issues found and fixed
4. **Entity Recognition**: Identified named entities and their classifications

## Dependencies

- **Core Libraries**: pandas, numpy, torch, transformers
- **NLP Libraries**: spacy, symspellpy
- **Text Processing**: difflib, re, collections
- **Models**: 
  - BERT NER model: `dslim/bert-base-NER`
  - spaCy English model: `en_core_web_sm`
  - SymSpell frequency dictionary

## Usage

1. Load the notebook and install required dependencies
2. Place the Excel data file in the same directory
3. Run cells sequentially to process the mortgage documents
4. Review the cleaned text and confidence scores in the final output

