# Invoice JSON Schema

## Overview
This document defines the binding JSON schema for all invoice training examples. Every curated invoice must conform to this schema exactly. Deviations from this schema in training examples will compromise the fine-tuning process.

## Required Keys (All Must Be Present)

### 1. **vendor** (string)
- **Description:** The name of the company or individual that issued the invoice.
- **Format:** Any non-empty string, typically 2-100 characters.
- **If absent in document:** Use `null`
- **Example:** `"vendor": "Acme Corporation Ltd."`

### 2. **invoice_number** (string)
- **Description:** The unique identifier for this invoice, as printed on the document.
- **Format:** Any non-empty string (alphanumeric, may include special characters like '-', '/', '#').
- **If absent in document:** Use `null`
- **Example:** `"invoice_number": "INV-2024-0045"`

### 3. **date** (string, YYYY-MM-DD format)
- **Description:** The issue date of the invoice.
- **Format:** ISO 8601 date format: `YYYY-MM-DD`
- **If absent in document:** Use `null`
- **Example:** `"date": "2024-03-15"`

### 4. **due_date** (string, YYYY-MM-DD format, or null)
- **Description:** The date by which payment is due.
- **Format:** ISO 8601 date format: `YYYY-MM-DD`, or `null` if not present.
- **If absent in document:** Use `null` (this is an optional field)
- **Example:** `"due_date": "2024-04-15"` or `"due_date": null`

### 5. **currency** (string, 3-letter ISO 4217 code)
- **Description:** The currency in which the invoice is issued.
- **Format:** Exactly 3-letter uppercase ISO currency code (USD, GBP, EUR, INR, JPY, etc.).
- **If absent in document:** Infer from context if possible; default to `"USD"` only if truly ambiguous.
- **Example:** `"currency": "USD"` or `"currency": "EUR"`

### 6. **subtotal** (float)
- **Description:** The sum of all line items before tax.
- **Format:** Floating-point number, may have up to 2 decimal places.
- **If absent in document:** Use `null` (if only total is present, do NOT compute subtotal)
- **Example:** `"subtotal": 15000.00`

### 7. **tax** (float or null)
- **Description:** The total tax amount.
- **Format:** Floating-point number (up to 2 decimal places), or `null` if not present.
- **If absent in document:** Use `null` (do NOT hallucinate or infer tax)
- **Example:** `"tax": 1500.00` or `"tax": null`

### 8. **total** (float)
- **Description:** The total invoice amount (subtotal + tax, or the final amount due).
- **Format:** Floating-point number with up to 2 decimal places.
- **If absent in document:** Use `null`
- **Example:** `"total": 16500.00`

### 9. **line_items** (array of objects)
- **Description:** A list of line items (products or services) on the invoice.
- **Format:** Array of objects; each object MUST have the following structure:
  ```json
  {
    "description": "string (product/service name)",
    "quantity": number (integer or float),
    "unit_price": number (float)
  }
  ```
- **Constraints:**
  - `description`: Non-empty string, e.g., "Office Supplies" or "Consulting Services".
  - `quantity`: Positive number (integer or float).
  - `unit_price`: Positive number (float), typically with up to 2 decimals.
  - If the document has no line items, use an empty array `[]`.
  - If a field is ambiguous or missing, mark it as `null` within the object.
- **If absent in document:** Use empty array `[]`
- **Example:**
  ```json
  "line_items": [
    {"description": "Software License", "quantity": 1, "unit_price": 5000.00},
    {"description": "Support Services", "quantity": 12, "unit_price": 800.00}
  ]
  ```

## Complete Example

```json
{
  "vendor": "Global Tech Solutions Inc.",
  "invoice_number": "INV-2024-00789",
  "date": "2024-03-10",
  "due_date": "2024-04-10",
  "currency": "USD",
  "subtotal": 50000.00,
  "tax": 5000.00,
  "total": 55000.00,
  "line_items": [
    {"description": "Cloud Services (Monthly)", "quantity": 1, "unit_price": 30000.00},
    {"description": "Technical Support", "quantity": 200, "unit_price": 100.00}
  ]
}
```

## Validation Checklist

Before adding an invoice example to the training set:

- [ ] All required keys are present (vendor, invoice_number, date, due_date, currency, subtotal, tax, total, line_items).
- [ ] String values are non-empty (or explicitly `null`).
- [ ] Dates are in YYYY-MM-DD format (or `null`).
- [ ] Numbers are actual floats/integers (not strings like `"1500.00"`).
- [ ] Currency is a valid 3-letter ISO code.
- [ ] line_items is an array (possibly empty), and each item has description, quantity, unit_price.
- [ ] No extra keys are present that are not listed in this schema.
- [ ] The entire output is valid JSON and parseable by `json.loads()` in Python.
