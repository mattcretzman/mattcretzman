---
title: "How AI Analyzes Text Message Evidence for Family Law Cases"
description: "Technical deep-dive into building AI systems for legal text analysis. OCR pipelines, NLP architecture, and court-ready output generation."
published: true
tags: ai, nlp, legaltech, machinelearning, ocr
canonical_url: https://blog.mattcretzman.com/ai-text-message-evidence-family-law
---

In January 2026, a family law attorney sent me 847 screenshots of text messages from a custody case. She'd spent three weekends manually organizing them chronologically. The process was error-prone, exhausting, and she still wasn't sure she'd caught every important pattern.

I built TextEvidence.ai to solve this. Here's the technical architecture behind analyzing text message evidence at scale.

## The Challenge

A typical contested divorce involves 5,000+ messages spanning months or years. The problems with manual review:

- Screenshots lack conversation context
- Important exchanges buried in irrelevant chatter  
- Cross-platform correlation (iMessage, WhatsApp, SMS) is difficult
- Pattern detection requires analyzing hundreds of interactions

## Technical Architecture

### Phase 1: OCR and Data Extraction

**The Problem:** iPhone screenshots, Android exports, PDF printouts, CSV files from carriers, XML from iPhone backups—each structures data differently.

**The Solution:** Multi-modal extraction pipeline

```python
# Simplified extraction flow
extraction_strategies = {
    'iphone_screenshot': IOCRStrategy(),
    'android_export': CsvParserStrategy(),
    'carrier_csv': CarrierCsvStrategy(),
    'pdf_export': PdfOcrStrategy(),
    'xml_backup': IphoneXmlStrategy()
}

def extract_messages(source_file):
    file_type = detect_format(source_file)
    strategy = extraction_strategies[file_type]
    return strategy.extract(source_file)
```

**OCR Pipeline:**
- Primary: Tesseract with legal-message-specific training
- Fallback: Commercial APIs for complex layouts
- Confidence scoring for every extraction
- Human review flags for low-confidence timestamps

Accuracy is critical—a single misread timestamp corrupts the entire timeline.

### Phase 2: Normalization and Threading

Once extracted, messages need standardization:

```python
class MessageNormalizer:
    def normalize(self, raw_messages):
        normalized = []
        for msg in raw_messages:
            normalized.append({
                'timestamp': self.parse_timestamp(msg),
                'sender': self.normalize_sender(msg),
                'content': msg['body'],
                'platform': msg['source_platform'],
                'thread_id': self.infer_thread(msg)
            })
        return sorted(normalized, key=lambda x: x['timestamp'])
```

**Timestamp Handling:**
- Timezone detection and conversion
- Daylight saving adjustments
- 12/24-hour format normalization
- Relative date resolution ("yesterday" → actual date)

**Thread Reconstruction:**
Not just chronological sorting. We identify reply chains using:
- Temporal proximity (messages within 4 hours)
- Participant overlap
- Content similarity (quoted text detection)
- Platform-specific threading hints

### Phase 3: Entity Extraction

Family law cases involve multiple participants. Understanding who said what requires robust NER.

```python
entities = {
    'person': extract_names(text),
    'phone_numbers': extract_phones(text),
    'email_addresses': extract_emails(text),
    'dates': extract_date_references(text),
    'locations': extract_places(text),
    'financial': extract_monetary_amounts(text)
}

# Cross-reference across conversations
entity_resolution = resolve_entity_mentions(entities)
```

**Entity Resolution Challenge:**
"Mom" in Thread A and a phone number in Thread B might refer to the same person. We use:
- Contextual clues (messages mentioning both)
- Temporal correlation
- Linguistic patterns (how participants refer to each other)

### Phase 4: Sentiment and Pattern Analysis

**Why General Sentiment Models Fail:**
Legal communication uses formal dispute language that general models misclassify as negative when it's actually neutral professional communication.

**Our Approach:**
Fine-tuned models on legal communication datasets:

```python
class LegalSentimentAnalyzer:
    def __init__(self):
        self.model = load_fine_tuned_legal_model()
        self.intensity_scorer = IntensityScorer()
        
    def analyze_trajectory(self, messages):
        sentiments = []
        for msg in messages:
            sentiment = self.model.predict(msg['content'])
            intensity = self.intensity_scorer.score(msg)
            sentiments.append({
                'timestamp': msg['timestamp'],
                'sentiment': sentiment,
                'intensity': intensity,
                'latency': calculate_response_latency(msg)
            })
        return detect_patterns(sentiments)
```

**Pattern Detection:**

```python
def detect_harassment_cycles(messages):
    """Identify repeated contact after requests to stop"""
    patterns = []
    stop_requests = find_stop_requests(messages)
    
    for request in stop_requests:
        subsequent = get_messages_after(request, days=30)
        unsolicited = filter_unsolicited(subsequent, request['sender'])
        
        if len(unsolicited) > threshold:
            patterns.append({
                'type': 'harassment_cycle',
                'after_request': request['timestamp'],
                'message_count': len(unsolicited),
                'messages': unsolicited
            })
    return patterns
```

### Phase 5: Court-Ready Output Generation

**Line Numbering:**
Every message gets a sequential line number for court citations.

```python
def generate_exhibit(messages, exhibit_id):
    numbered = []
    for i, msg in enumerate(messages, 1):
        numbered.append({
            'line_number': i,
            'timestamp': msg['timestamp'],
            'sender': msg['sender'],
            'content': msg['content'],
            'hash': generate_hash(msg)  # For authenticity verification
        })
    return format_exhibit_pdf(numbered, exhibit_id)
```

**Export Formats:**

```python
export_formats = {
    'pdf_exhibit': generate_pdf_exhibit,
    'chronological_narrative': generate_narrative,
    'pattern_summary': generate_pattern_report,
    'raw_data': export_structured_data
}
```

## Authentication and Chain of Custody

**Metadata Preservation:**
Original timestamps, sender info, delivery status—all preserved with hash verification.

```python
def verify_integrity(original, processed):
    original_hash = hash_message(original)
    processed_hash = processed['source_hash']
    return original_hash == processed_hash
```

**Analysis Transparency:**
Every flagged pattern includes:
- Specific triggering messages
- Analytical criteria applied
- Human-reviewable excerpts
- Confidence scores

## Lessons Learned

**1. Format Diversity is Harder Than Expected**
Every carrier, every phone model, every export method produces slightly different output. We now support 40+ format variants.

**2. Timestamp Accuracy is Everything**
A single timezone error can invalidate an entire analysis. We now triple-verify all timestamps.

**3. Legal Context Requires Domain Tuning**
General NLP models fail on legal text. Fine-tuning on legal communication was essential.

**4. Explainability is Non-Negotiable**
Courts won't accept black box analysis. Every output must be explainable and verifiable.

**5. Human-in-the-Loop Remains Critical**
AI organizes evidence for attorney review. Final judgments remain human.

## Technical Stack

- **OCR:** Tesseract (custom training) + Azure Computer Vision (fallback)
- **NLP:** spaCy + custom legal models + OpenAI GPT-4 for complex inference
- **Data Processing:** Pandas + custom pipelines
- **Output Generation:** ReportLab (PDF), Jinja2 (templating)
- **Infrastructure:** Python/FastAPI, PostgreSQL, Redis

## The Future

Current development:
- **Cross-platform correlation** — Linking messages with social media, location data
- **Voice analysis** — Transcription and tone analysis for voicemail
- **Predictive models** — Identifying escalation patterns before they occur
- **API integrations** — Direct import from OurFamilyWizard, TalkingParents

---

*Matt Cretzman is the founder of TextEvidence.ai. This article was originally published on [mattcretzman.com](https://blog.mattcretzman.com/ai-text-message-evidence-family-law).*

**About the Author:**
Matt Cretzman builds AI systems for legal applications. He's also the founder of OpenClaw (AI agent framework), LeadStorm AI (autonomous marketing), and Stormbreaker Digital.