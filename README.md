# CSV Schema Validator

A browser-based tool for validating CSV files against a defined schema. Upload your data, define your rules, review violations, fix what you can, reject what you can't, and export a clean dataset.

**[Try it live →](https://michaelf-pm.github.io/csv-schema-validator)**

---

## What it does

1. **Load** – upload a data CSV and a schema file (JSON or CSV format)
2. **Define** – edit validation rules per column using the built-in schema builder
3. **Validate** – the tool checks every value against every rule and categorises each violation
4. **Resolve** – auto-fixable violations are corrected silently; others are surfaced for manual review or rejection
5. **Export** – download a cleaned CSV (fixes applied, rejected rows removed) and a plain-text audit report

### Rules supported per column

| Rule | Description |
|---|---|
| `type` | Expected data type: `text`, `numeric`, `integer`, `date`, `boolean` |
| `required` | Field cannot be null or empty |
| `allowed_values` | Pipe-separated enumeration of permitted values |
| `min` / `max` | Numeric or date range bounds |
| `max_length` | Maximum string length |
| `format` | Named format: `alphanumeric`, `email`, `date-iso`, `warehouse` |
| `on_violation` | What to do when a rule is broken: `auto`, `prompt`, or `reject` |

### Violation handling

- **`auto`** – fix silently where possible (e.g. reformat dates to ISO 8601, strip `$` from numeric fields)
- **`prompt`** – surface the violation and ask the user to enter a corrected value or skip
- **`reject`** – mark the row as invalid; it will be excluded from the cleaned export

Everything runs locally in the browser. No data is uploaded anywhere.

---

## Schema format

Schemas can be uploaded as JSON or CSV. Sample files for an inventory dataset are included in the repo.

**JSON**
```json
{
  "name": "Inventory Record",
  "fields": [
    {
      "name": "sku",
      "type": "text",
      "required": true,
      "max_length": 20,
      "format": "alphanumeric",
      "on_violation": "reject"
    },
    {
      "name": "selling_price",
      "type": "numeric",
      "required": true,
      "min": 0,
      "on_violation": "prompt"
    }
  ]
}
```

**CSV**
```
field_name,type,required,allowed_values,min,max,max_length,format,on_violation
sku,text,true,,,,20,alphanumeric,reject
selling_price,numeric,true,,0,,,,prompt
```

---

## Why I built this

The [CSV Audit Tool](https://github.com/Michael-Fehle-PM/csv_audit_tool) in this portfolio tells you *what is wrong* with a dataset. This tool takes the next step: it tells you *what should be there*, compares the two, and gives you a structured way to resolve the gap.

This mirrors how data quality works in production. A database has a schema – field types, constraints, permitted values – and any data entering the system is validated against it. In martech and fintech pipelines, violations need to be handled in one of three ways: fix automatically, escalate for human review, or reject outright. This tool makes that triage process explicit and auditable.

---

## Product decisions worth noting

**Why support both JSON and CSV schema formats?**
A data engineer or developer will have a schema definition to hand and will want to upload it directly – JSON is the natural format for that audience, and mirrors real-world tooling like JSONSchema, dbt, and Great Expectations. An ops manager or analyst probably doesn't have a schema file, but can answer "what should this column contain?" using a spreadsheet. Supporting both formats removes the barrier for both audiences without compromising the tool for either.

**Why a schema builder UI?**
Not every user arrives with a schema. The builder lets anyone define rules column by column directly in the interface, lowering the barrier to a first run. It also means the schema is always visible and editable – you can adjust a rule and re-validate without touching a file.

**Why three violation actions rather than just pass/fail?**
A binary pass/fail is too blunt for real-world data. A missing date format is fixable in seconds; a missing required ID field means the row is unloadable. Treating both the same way either over-rejects clean-enough data or under-flags genuinely broken records. The `auto` / `prompt` / `reject` model gives the user proportionate control.

**Known limitations**
Size validation is currently a flat enumeration. A future version could support category-dependent size rules – apparel sizes (XS–XXL), shoe sizes (numeric), children's sizes (age-based) – using a conditional schema structure where rules for one field depend on the value of another.

---

## Sample files

The repo includes a sample inventory schema in both formats:

- [`sample-schema.json`](sample-schema.json) – JSON schema for a martech inventory dataset
- [`sample-schema.csv`](sample-schema.csv) – CSV equivalent of the same schema

Fields covered: `sku`, `product_name`, `category`, `colour`, `size`, `unit_cost`, `selling_price`, `stock_quantity`, `warehouse_location`, `supplier_id`, `last_updated`.

---

## Built with

- Vanilla HTML, CSS, and JavaScript – no frameworks, no dependencies
- [Tabler Icons](https://tabler.io/icons) for iconography
- Built iteratively using [Claude](https://claude.ai) as a coding assistant

---

## Running locally

No build step required:

```bash
git clone https://github.com/MichaelF-PM/csv-schema-validator.git
cd csv-schema-validator
open index.html
```

---

## Potential next iterations

- **Conditional rules** – validate a field differently depending on the value of another field (e.g. size rules dependent on category)
- **Custom format definitions** – let users define their own regex-based format rules in the schema
- **Multi-sheet support** – validate Excel files with multiple tabs against separate schemas
- **Schema export** – download the schema built in the UI as JSON or CSV for reuse
- **Diff mode** – compare two versions of the same dataset against a shared schema and highlight what changed
- **BYO schema** – build the schema directly in the UI without having to create it offsite and import it

---

## Related tools

- [CSV Audit Tool](https://github.com/Michael-Fehle-PM/csv_audit_tool) – structural data quality audit: nulls, duplicates, outliers, mixed delimiters, injection payloads

---

## About

Built by [Michael F](https://github.com/Michael-Fehle-PM) as part of a portfolio of data tooling projects. Background in SaaS product management across martech and fintech, with a focus on data quality, ETL pipelines, and operational tooling.
