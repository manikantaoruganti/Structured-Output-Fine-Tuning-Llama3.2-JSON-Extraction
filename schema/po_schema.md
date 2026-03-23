# Purchase Order JSON Schema

## Overview
This document defines the binding JSON schema for all purchase order training examples. Every curated PO must conform to this schema exactly.

## Required Keys (All Must Be Present)

### 1. **buyer** (string)
- **Description:** The name of the organization or person placing the purchase order.
- **Format:** Any non-empty string, typically 2-100 characters.
- **If absent in document:** Use `null`
- **Example:** `"buyer": "XYZ Manufacturing Corp"`

### 2. **supplier** (string)
- **Description:** The name of the vendor or supplier from whom goods/services are being ordered.
- **Format:** Any non-empty string.
- **If absent in document:** Use `null`
- **Example:** `"supplier": "Industrial Supplies Ltd."`

### 3. **po_number** (string)
- **Description:** The unique identifier for this purchase order.
- **Format:** Any non-empty string (alphanumeric, may include '-', '/', '#').
- **If absent in document:** Use `null`
- **Example:** `"po_number": "PO-2024-0512"`

### 4. **date** (string, YYYY-MM-DD format)
- **Description:** The date the PO was issued.
- **Format:** ISO 8601 date format: `YYYY-MM-DD`
- **If absent in document:** Use `null`
- **Example:** `"date": "2024-03-20"`

### 5. **delivery_date** (string, YYYY-MM-DD format, or null)
- **Description:** The expected delivery date for the ordered items.
- **Format:** ISO 8601 date format: `YYYY-MM-DD`, or `null` if not present.
- **If absent in document:** Use `null` (this is an optional field)
- **Example:** `"delivery_date": "2024-04-20"` or `"delivery_date": null`

### 6. **currency** (string, 3-letter ISO 4217 code)
- **Description:** The currency in which the PO is issued.
- **Format:** Exactly 3-letter uppercase ISO currency code (USD, GBP, EUR, INR, JPY, etc.).
- **If absent in document:** Infer from context if possible; default to `"USD"` only if truly ambiguous.
- **Example:** `"currency": "USD"` or `"currency": "GBP"`

### 7. **total** (float)
- **Description:** The total purchase order amount.
- **Format:** Floating-point number with up to 2 decimal places.
- **If absent in document:** Use `null`
- **Example:** `"total": 25000.00`

### 8. **items** (array of objects)
- **Description:** A list of items (products or services) being ordered.
- **Format:** Array of objects; each object MUST have the following structure:
  ```json
  {
    "item_name": "string (product/service name)",
    "quantity": number (integer or float),
    "unit_price": number (float)
  }
  ```
- **Constraints:**
  - `item_name`: Non-empty string, e.g., "Server Hardware" or "Consulting Days".
  - `quantity`: Positive number (integer or float).
  - `unit_price`: Positive number (float), typically with up to 2 decimals.
  - If the PO has no items, use an empty array `[]`.
  - If a field is ambiguous or missing, mark it as `null` within the object.
- **If absent in document:** Use empty array `[]`
- **Example:**
  ```json
  "items": [
    {"item_name": "Industrial Printer Model X", "quantity": 2, "unit_price": 5000.00},
    {"item_name": "Maintenance Contract (Annual)", "quantity": 1, "unit_price": 15000.00}
  ]
  ```

## Complete Example

```json
{
  "buyer": "Global Retail Networks Inc.",
  "supplier": "Premium Wholesale Distributors",
  "po_number": "PO-2024-01245",
  "date": "2024-03-15",
  "delivery_date": "2024-04-30",
  "currency": "USD",
  "total": 150000.00,
  "items": [
    {"item_name": "Bulk Fabric Roll (Cotton Blend)", "quantity": 50, "unit_price": 2000.00},
    {"item_name": "Dyes and Chemicals", "quantity": 100, "unit_price": 500.00},
    {"item_name": "Quality Control Services", "quantity": 1, "unit_price": 5000.00}
  ]
}
```

## Validation Checklist

Before adding a PO example to the training set:

- [ ] All required keys are present (buyer, supplier, po_number, date, delivery_date, currency, total, items).
- [ ] String values are non-empty (or explicitly `null`).
- [ ] Dates are in YYYY-MM-DD format (or `null`).
- [ ] Numbers are actual floats/integers (not strings).
- [ ] Currency is a valid 3-letter ISO code.
- [ ] items is an array (possibly empty), and each item has item_name, quantity, unit_price.
- [ ] No extra keys are present that are not listed in this schema.
- [ ] The entire output is valid JSON and parseable by `json.loads()` in Python.
