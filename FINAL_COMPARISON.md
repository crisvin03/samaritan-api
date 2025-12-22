# XML vs JSON Comparison (PESO-Bulan Portal API)

## Summary
Both XML and JSON are supported by the API. JSON is the default for speed and simplicity; XML is provided for interoperability and schema-friendly integrations. Choose based on client needs.

## Structure & Readability
- **JSON:** Lightweight, terse, easy for humans to read, naturally maps to objects/arrays.
- **XML:** Verbose, supports attributes and mixed content, naturally hierarchical.

## Tooling & Parsing
- **JSON:** Native in browsers and modern languages; minimal parsing code; great with REST/HTTP and JavaScript.
- **XML:** Strong tooling for validation (XSD), transformations (XSLT), and document-centric workflows; more boilerplate to parse.

## Validation
- **JSON:** Validated here with custom schema checks; JSON Schema is available but less universally enforced.
- **XML:** Can be validated with XSD; good for strict contracts. In this API we perform the same business rules on parsed XML as JSON.

## Payload Size & Performance
- **JSON:** Typically smaller payloads; faster to parse; ideal for bandwidth-sensitive or high-throughput use.
- **XML:** Larger payloads due to tags; parsing overhead is higher. Acceptable when strict schemas or XML-centric systems are required.

## Error Handling
- **Both:** API returns the same structure in either format:
  - `success`, `status_code`, `message`, `data`, `errors[]`, `timestamp`
  - HTTP status codes used consistently (200/201/400/401/404/500)
- **Recommendation:** Clients should rely on HTTP status + `errors` field regardless of format.

## Interoperability
- **JSON:** Best with modern web/mobile clients, SPAs, and microservices.
- **XML:** Useful for legacy systems, enterprise integrations, and partners that require XML or XSD validation.

## When to choose JSON
- Web/mobile apps, SPAs, and JavaScript-heavy clients
- Performance-sensitive scenarios (smaller/faster)
- Simpler payloads without document-style needs

## When to choose XML
- Integrations that mandate XML/XSD
- Document-centric or strongly typed enterprise workflows
- When transformations (XSLT) or rich metadata are needed

## How the API supports both
- **Request format selection:**
  - JSON (default): `Content-Type: application/json`
  - XML: `Content-Type: application/xml` or `?format=xml`
- **Response format selection:**
  - JSON (default)
  - XML: `Accept: application/xml` or `?format=xml`
- **Shared validation & logic:** Requests are normalized to arrays; the same validation and business rules run regardless of input format.

## Practical trade-offs
- **Use JSON by default** for speed, size, and native client support.
- **Offer XML** for partners/clients needing strict schemas or legacy compatibility.
- **Keep contracts identical**: Same fields, errors, and status codes in both formats to reduce client divergence.

## Testing tips
- Postman: toggle headers `Accept` and `Content-Type` between `application/json` and `application/xml`, or append `?format=xml`.
- Curl examples:
  - JSON: `curl -H "Content-Type: application/json" ...`
  - XML: `curl -H "Accept: application/xml" -H "Content-Type: application/xml" ...`

