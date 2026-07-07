# XML (eXtensible Markup Language) : Best Practices & Architecture

## 1. Security: The XXE Threat (CRITICAL)
- **The Threat:** XML External Entity (XXE) injection allows attackers to read local files on your server (like `/etc/passwd`) or execute server-side request forgeries (SSRF) if the parser evaluates external entities.
- **Rule:** **ALWAYS disable DTDs (Document Type Definitions) and External Entities** in your XML parser configuration before parsing any untrusted XML. This is the most critical security rule for XML.

## 2. Validation & Contracts
- **XSD (XML Schema Definition):** Use XSD to define the strict structure, data types, and required nodes of your XML documents. Validate incoming XML against the XSD before attempting to extract its data.

## 3. Parsing Strategy (DOM vs. SAX)
Choose the correct parsing architecture based on the payload size:
- **DOM (Document Object Model):** Loads the entire XML tree into memory. Use this *only* for small files (under a few megabytes) where you need to traverse back and forth across nodes.
- **SAX (Simple API for XML) or Streaming Parsers:** Reads the file sequentially node by node without loading the whole file into RAM. Use this *mandatory* for large datasets (GBs of data) to prevent Out-Of-Memory (OOM) crashes.

## 4. Namespaces
- **Avoid Ambiguity:** Always use explicitly defined XML namespaces (`xmlns:prefix="URI"`) when merging data from multiple sources to prevent tag name collisions (e.g., `<customer:id>` vs `<product:id>`).
- **URI Standard:** The namespace URI does not need to point to a live web page, but it should be a unique identifier controlled by your organization (e.g., `urn:mycompany:module:v1`).