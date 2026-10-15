# Prompt
Create a flow for full extract invoices into structured data. For example:
. Full extract text
. Regex to identify date, values, etc
. NER to identify company names, address, etc.
. Semantic Chunking
. Cross-field math validation
. Other strategies
I need all steps most used in rich solutions.
You must order the strategies from most easy to detect field to most complex.

# MyHUB - GPT6-Luna 
# Invoice extraction flow

A robust invoice solution should **preserve the original document structure**, extract all text, then use progressively more sophisticated methods to identify fields. The ordering below goes from **easiest and most deterministic** to **most complex and context-dependent**.

## 1. Receive and prepare the document

- Accept PDFs, scans, images, and supported office formats.
- Record file metadata, such as file type, page count, and source.
- Detect whether the PDF contains selectable text or is image-based.
- Correct page rotation, skew, and image quality where needed.
- Keep the original file for traceability.

## 2. Extract the full text

Use the best available method for each page:

- **Native text extraction** for digitally generated PDFs.
- **OCR** for scanned pages or image-only content.
- **OCR plus native-text comparison** when the document contains both.

Store more than a single plain-text string. Preserve:

- Page number
- Text block or line
- Word and line coordinates
- Reading order
- OCR confidence, when available
- Table or cell boundaries, when detected

This layout information helps distinguish, for example, an invoice date from a due date that appears nearby.

## 3. Detect document type and page structure

Before extracting fields, determine whether the document is an invoice, credit note, receipt, statement, or another document. Then identify likely regions:

- Header and supplier details
- Customer or “bill to” section
- Invoice summary
- Line-item tables
- Tax and totals section
- Payment instructions and footer

For multi-page documents, detect whether pages belong to the same invoice and whether tables continue across pages.

## 4. Extract the easiest fields first

Start with fields that have predictable formats or clear labels.

### 4.1 Explicit labels and regular expressions

Look for labels such as “Invoice No.”, “Date”, “Due Date”, “Total”, “Tax ID”, and their language-specific variants. Apply format checks and nearby-text rules.

Good initial candidates include:

- Invoice number
- Invoice date
- Due date
- Purchase order number
- Currency
- Tax or registration numbers
- Postal codes
- Email addresses and phone numbers

Regular expressions are useful for finding candidates, but should not be the only decision mechanism: the same date or number format can appear in unrelated contexts.

### 4.2 Normalize detected values

Convert recognized candidates into consistent representations:

- Dates into a standard date format
- Amounts into numeric values
- Currency symbols or codes into a normalized currency code
- Tax IDs into a canonical format, while retaining the original text
- Whitespace and punctuation into consistent forms

Retain both the **original value** and the **normalized value**.

## 5. Use labels, anchors, and spatial proximity

When a field does not have a unique format, use nearby text and page layout:

- Find a label, then inspect the text to its right, below, or within the same row.
- Use coordinates to associate labels with values.
- Use section context to distinguish fields, such as “Ship to” versus “Bill to.”
- Prefer candidates near likely invoice-summary or party-information regions.

This is usually more reliable than searching the entire document for a matching pattern.

## 6. Extract line-item tables

Line items are often the most important and most error-prone part of an invoice. Detect table rows and map columns using headers and alignment, including:

- Description
- Quantity
- Unit of measure
- Unit price
- Discount
- Tax rate or tax amount
- Line total

Handle wrapped descriptions, missing cells, repeated headers, and tables spanning multiple pages. Preserve the original row text so uncertain mappings can be reviewed.

## 7. Identify organizations, people, and addresses with NER

Use named-entity recognition (NER) to find likely:

- Supplier and customer names
- Person names
- Addresses
- Locations
- Tax authorities or financial institutions

Then assign roles using context. For example, a company name is not automatically the supplier: it could be the customer, a bank, or a company mentioned in a line-item description.

Combine NER with nearby labels such as “Supplier”, “From”, “Bill to”, and “Remit to”.

## 8. Apply semantic chunking

Divide the document into meaningful, layout-aware sections rather than arbitrary character-length chunks. Useful chunks include:

- Supplier block
- Customer block
- Invoice metadata
- Each line-item table
- Tax and totals summary
- Payment instructions

Semantic chunking helps downstream models focus on relevant context and reduces confusion between similar values. Keep links from each chunk back to its page and coordinates.

## 9. Use contextual extraction for ambiguous fields

For fields that remain unclear, use a document model or language model with a strict output schema. Provide the relevant section, the candidate fields, and rules such as:

- Do not infer a value that is not present.
- Return `null` when a field cannot be identified.
- Include the source text and page for each extracted value.
- Separate the supplier from the customer.
- Distinguish subtotal, tax, total, amount paid, and balance due.

Use this as a fallback or resolver—not as a replacement for deterministic extraction and validation.

## 10. Validate fields and cross-field arithmetic

Run checks after extraction:

- Is the invoice date valid and plausible?
- Is the due date consistent with the document?
- Do line totals agree with quantity, unit price, discount, and tax rules?
- Do subtotal, taxes, discounts, and total reconcile?
- Does the amount paid plus balance due agree with the total, when those fields are present?
- Is the currency consistent across the invoice?
- Are required fields present for the document type?

Allow for rounding, tax-specific rules, and invoices where the displayed totals do not follow a simple formula. A failed check should flag the record for review, not silently overwrite values.

## 11. Resolve conflicts and detect duplicates

If multiple methods find different values:

- Compare evidence quality, such as a clear label, table position, OCR confidence, and validation results.
- Prefer candidates supported by multiple independent signals.
- Keep competing candidates and their evidence for audit.
- Check for duplicate invoices using supplier identity, invoice number, date, and total.

## 12. Score confidence and route exceptions

Assign confidence at the **field level**, not just the document level. For each value, retain:

- Extracted value
- Normalized value
- Source text
- Page and coordinates
- Extraction method
- Confidence or validation status

Automatically process high-confidence records. Send low-confidence fields, failed validations, and conflicting candidates to human review. Feed corrected results back into rules and models.

## 13. Produce structured output

Use a stable schema and retain provenance. For example:

```json
{
  "document_type": "invoice",
  "invoice_number": {
    "value": "INV-10428",
    "normalized_value": "INV-10428",
    "page": 1,
    "method": "label_and_regex",
    "confidence": 0.98
  },
  "invoice_date": {
    "value": "15/10/2026",
    "normalized_value": "2026-10-15",
    "page": 1,
    "method": "label_and_date_parser",
    "confidence": 0.96
  },
  "supplier": {
    "name": "Example Supplier Ltd.",
    "tax_id": "00.000.000/0001-00",
    "address": "Example address",
    "page": 1,
    "method": "ner_and_context",
    "confidence": 0.91
  },
  "line_items": [
    {
      "description": "Service plan",
      "quantity": 2,
      "unit_price": 150.00,
      "line_total": 300.00,
      "page": 1,
      "confidence": 0.94
    }
  ],
  "currency": "BRL",
  "subtotal": 300.00,
  "tax_total": 0.00,
  "invoice_total": 300.00,
  "validation": {
    "status": "passed",
    "issues": []
  }
}
```

## Recommended detection order

1. **Explicit labels plus predictable formats** — invoice number, dates, tax IDs, email, phone.
2. **Normalization and format checks** — dates, currencies, amounts, identifiers.
3. **Label and spatial association** — supplier, customer, subtotal, due date.
4. **Table and row extraction** — line items, quantities, prices, taxes.
5. **NER with contextual role assignment** — company names, people, and addresses.
6. **Semantic chunking and document-aware extraction** — ambiguous sections and multi-page context.
7. **Contextual model or LLM resolution** — unresolved or conflicting fields.
8. **Cross-field validation, duplicate detection, and human review** — final quality controls.

In practice, the strongest systems combine these methods: start with deterministic extraction, use layout and context to resolve candidates, then apply models and human review only where necessary.

