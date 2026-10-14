# Prompt (Modo IA)
structured ai document semantic analysis

# Modo IA
A structured AI document semantic analysis transforms free-form or semi-structured files (such as PDFs, contracts, emails, and tables) into organized, machine-readable intelligence by interpreting underlying meaning rather than just matching keywords. [1] (https://www.llamaindex.ai/glossary/semantic-document-parsing), [2] (https://graphwise.ai/components/semantic-analytics/)

Core Concepts

• Semantic Parsing: Uses Natural Language Processing (NLP) to identify intent, resolve ambiguity, and map relationships between concepts inside text.
• Logical Unit Segmentation: Breaks down documents by structural metadata (headings, clauses, sentence boundaries) before generating embeddings to prevent local context from getting lost in a broad summary.
• Structured Outputs: Enforces strict target schemas (such as JSON or Pydantic) so downstream systems can reliably consume extracted data. [1] (https://www.docsie.io/blog/glossary/semantic-analysis/), [2] (https://medium.com/tutai-ai/semantic-search-and-structured-outputs-making-llms-work-smarter-with-data-a9ac2becf161), [3] (https://www.llamaindex.ai/glossary/semantic-document-parsing)

Key Capabilities & Processing Steps

1. Extraction & Recognition:
	• Identifies named entities (people, organizations, locations), dates, and domain-specific sentiment.
	• Parses complex structures like merged-cell tables via specialized frameworks (e.g., table-centric parsing models). [1] (https://arxiv.org/abs/2508.10311), [2] (https://www.youtube.com/watch?v=0I07YAuF8xA&t=634), [3] (https://graphwise.ai/components/semantic-analytics/)
2. Contextual Mapping:
	• Links related obligations, renewal dates, and risk clauses in legal agreements.
	• Maps unformatted records into corporate taxonomies and semantic layers. [1] (https://parseur.com/blog/semantic-document-understanding), [2] (https://www.youtube.com/shorts/-SlKQn8DwLs), [3] (https://graphwise.ai/components/semantic-analytics/)
3. Vector & RAG Integration:
	• Converts parsed chunks into numerical embeddings stored in vector databases for conceptual similarity search.
	• Grounds Large Language Models (LLMs) to drastically reduce hallucinations. [1] (https://www.wisdom.ai/resources/guides/data-analytics/ai-data-preparation), [2] (https://medium.com/tutai-ai/semantic-search-and-structured-outputs-making-llms-work-smarter-with-data-a9ac2becf161), [3] (https://www.youtube.com/shorts/-SlKQn8DwLs)

Common Use Cases

• Legal & Compliance: Automated review of contracts, policy documents, and cross-jurisdictional frameworks.
• Healthcare & Insurance: Combining structured patient charts with unstructured medical history reports.
• Logistics & Supply Chain: Standardizing variable shipping forms, customs paperwork, and bills of lading. [1] (https://www.mdpi.com/2079-8954/14/10/1252), [2] (https://dev.to/imaginex/building-ai-agents-semantic-integration-of-structured-and-unstructured-data-using-openai-agent-sdk-5641), [3] (https://parseur.com/blog/semantic-document-understanding)
Learn more about parsing mechanics through LlamaIndex Semantic Document Parsing or explore structured retrieval workflows via TUTAI.ai Semantic Search Overview. [1] (https://medium.com/tutai-ai/semantic-search-and-structured-outputs-making-llms-work-smarter-with-data-a9ac2becf161), [2] (https://www.llamaindex.ai/glossary/semantic-document-parsing)
If you have a specific goal in mind, tell me:
• What type of document you are trying to analyze (PDFs, invoices, legal contracts, etc.)
• What output format you need (JSON, a database table, a summary report)
I can help you design a parsing or RAG workflow for it.

# Prompt
invoices in pdf and the output format will be JSON

# Modo IA
```json
{
  "$schema": "https://json-schema.org",
  "title": "InvoiceSemanticAnalysis",
  "type": "object",
  "properties": {
    "invoice_metadata": {
      "type": "object",
      "properties": {
        "invoice_number": { "type": ["string", "null"] },
        "purchase_order_number": { "type": ["string", "null"] },
        "issue_date": { "type": ["string", "null"], "format": "date" },
        "due_date": { "type": ["string", "null"], "format": "date" },
        "currency": { "type": ["string", "null"], "description": "ISO 4217 currency code (e.g., USD, EUR)" }
      },
      "required": ["invoice_number", "issue_date", "currency"]
    },
    "parties": {
      "type": "object",
      "properties": {
        "vendor": {
          "type": "object",
          "properties": {
            "name": { "type": "string" },
            "tax_id": { "type": ["string", "null"] },
            "address": { "type": ["string", "null"] },
            "email": { "type": ["string", "null"], "format": "email" }
          },
          "required": ["name"]
        },
        "buyer": {
          "type": "object",
          "properties": {
            "name": { "type": "string" },
            "tax_id": { "type": ["string", "null"] },
            "address": { "type": ["string", "null"] },
            "email": { "type": ["string", "null"], "format": "email" }
          },
          "required": ["name"]
        }
      },
      "required": ["vendor", "buyer"]
    },
    "line_items": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "item_id": { "type": ["string", "null"] },
          "description": { "type": "string" },
          "quantity": { "type": "number" },
          "unit_price": { "type": "number" },
          "total_amount": { "type": "number" },
          "tax_rate": { "type": ["number", "null"], "description": "Percentage value (e.g., 0.15 for 15%)" }
        },
        "required": ["description", "quantity", "unit_price", "total_amount"]
      }
    },
    "financial_summary": {
      "type": "object",
      "properties": {
        "subtotal": { "type": "number" },
        "tax_total": { "type": "number" },
        "discount_total": { "type": ["number", "null"] },
        "grand_total": { "type": "number" }
      },
      "required": ["subtotal", "tax_total", "grand_total"]
    },
    "semantic_validation": {
      "type": "object",
      "properties": {
        "is_mathematically_consistent": { 
          "type": "boolean", 
          "description": "True if sum of line items matches subtotal, and subtotal + tax - discounts matches grand_total" 
        },
        "confidence_scores": {
          "type": "object",
          "properties": {
            "metadata_extraction": { "type": "number", "minimum": 0, "maximum": 1 },
            "line_items_extraction": { "type": "number", "minimum": 0, "maximum": 1 },
            "financial_summary_extraction": { "type": "number", "minimum": 0, "maximum": 1 }
          }
        },
        "flagged_anomalies": {
          "type": "array",
          "items": { "type": "string" },
          "description": "List of potential risks, such as missing business registry details or mismatched totals"
        }
      },
      "required": ["is_mathematically_consistent", "confidence_scores"]
    }
  },
  "required": ["invoice_metadata", "parties", "line_items", "financial_summary", "semantic_validation"]
}
```
Use o código com cuidado.

# Prompt
Create a flow for full extract invoices into structured data. For example:
1. Full extract text
2. Apply Regex to identify date, values, etc
3. Apply NER to identify company names, address, etc
I need all steps most used in rich solutions.  

# Modo IA
Here is the production-grade workflow used in modern, high-accuracy intelligent document processing (IDP) solutions to convert unstructured PDF invoices into structured JSON.
```
[ Native/OCR Text Extraction ] ➔ [ Document Layout Analysis ] ➔ [ Heuristic Filtering (Regex) ]
[ Final Schema Validation ] 🧠 [ LLM / SLM Extraction ] 🪓 [ Semantic Chunking & Prompting ] 🔀 [ Core NER ]
``` 

Phase 1: Ingestion & Text Extraction


1. Hybrid Text Extraction

• Digital PDFs: Use libraries like PyMuPDF (fitz) or Apache PDFBox to extract native text layers, bounding boxes, and embedded fonts directly.
• Scanned PDFs/Images: Route through a modern OCR engine. Use Tesseract (open-source) or cloud APIs like AWS Textract, Azure Document Intelligence, or Google Cloud Document AI which are pre-trained specifically on financial document layouts.

2. Layout & Vision Analysis (VQA)

• Invoices are highly visual. A simple top-to-bottom text dump destroys table relationships.
• Apply LayoutLMv3 or Donut models to recognize reading order, separate headers from columns, and flag multi-page document boundaries.

Phase 2: Hybrid Extraction Layer


3. Deterministic Filtering (Regex & Rules)

• Purpose: Extract predictable patterns instantly to save API costs and compute time.
• Targets: Standard date formats (\d{2}/\d{2}/\d{4}), ZIP codes, phone numbers, and explicit identifiers like Tax ID: or EIN:.

4. Named Entity Recognition (NER)

• Purpose: Isolate names and entities that do not follow a fixed format.
• Implementation: Use a spaCy pipeline or a fine-tuned BERT variant (dbmdz/bert-large-cased-finetuned-conll03) to tag ORG (Vendor/Buyer names) and LOC (Billing addresses).

5. Semantic Chunking & LLM/SLM Reasoning

• Purpose: Handle variable structures, interpret line items, and extract semantic meaning.
• Strategy: Format the layout-preserved text and pass it to a small fine-tuned model (like Phi-3-Medium) or an LLM (like GPT-4o-mini).
• Enforcement: Use Instructor or Outlines to force the model to output exactly the JSON schema provided in the previous step.

Phase 3: Validation & Business Logic


6. Cross-Field Math Validation

• Run automated programmatic scripts to verify invoice logic:
\(\sum (\text{Quantity}\times \text{Unit\ Price})=\text{Subtotal}\)
\(\text{Subtotal}+\text{Tax}-\text{Discount}=\text{Grand\ Total}\)
• If the math fails, flag the invoice for a Human-in-the-Loop (HITL) review queue.

7. Schema & Integrity Check

• Validate the final JSON payload against your target JSON Schema.
• Standardize text strings (e.g., converting "Jan 12, 2026" into ISO 2026-01-12 and fuzzy-matching vendor names against your internal ERP database master list).
Would you like an executable Python blueprint using open-source libraries (like PyMuPDF + spaCy + Pydantic) to see how this works in code, or should we focus on how to handle complex multi-page nested tables?

