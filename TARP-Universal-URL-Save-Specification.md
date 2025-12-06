# TARP Universal URL Save Specification

**Version:** 1.0
**Status:** Under Development / Subject to Change  
**Author:** tarp.tokyo
**License:** TARP Tools License (see /terms/)

---

## Overview

This document defines a unified specification for saving and restoring application states using URL-encoded JSON data within the TARP ecosystem.

The primary goal is to enable:
- lossless restoration of application state,
- long-term backward compatibility,
- cross-tool interoperability, and
- URL-based sharing and portability.

This specification applies to all TARP Tools and applications that support state persistence, including but not limited to:

- structural editors  
- builders  
- visual generators  
- games  
- utilities  

Any future TARP software should follow this protocol to ensure consistent storage, recovery, and compatibility.

---

## Core Principles

1. **JSON as the universal state format**
   - Full application state is represented as a single JSON object.

2. **URL-based save mechanism**
   - The compressed JSON string MUST be stored in the URL query parameter.
   - The default parameter key is `data`.

3. **Compression**
   - The JSON MUST be compressed using `LZString.compressToEncodedURIComponent`.
   - Decompression MUST use `decompressFromEncodedURIComponent`.

4. **Backward compatibility**
   - The saved JSON MUST include version metadata (`v`) to ensure future compatibility.
   - Newer applications MUST support reading older versions of stored data.

5. **Cross-tool interoperability**
   - Shared core fields enable multiple tools to read, restore, or convert saved states.

6. **Stateless restoration**
   - A full state MUST be reconstructible solely from the URL query value, without relying on external storage.

---

## Required JSON Structure

```ts
type SaveData = {
  v: number                 // version of the stored format
  seed?: number             // optional: initial random seed
  state: unknown            // tool-specific state definition
  meta?: {
    timestamp?: number      // optional save time (epoch)
    tool?: string           // identifier of the tool
  }
}
````

### Mandatory Fields

| Field   | Required | Purpose                               |
| ------- | -------- | ------------------------------------- |
| `v`     | Yes      | Versioning and backward compatibility |
| `state` | Yes      | Serialized core application state     |

### Optional Fields

| Field            | Usage                                                      |
| ---------------- | ---------------------------------------------------------- |
| `seed`           | Reproducible initialization (shuffle, random layout, etc.) |
| `meta.timestamp` | Lightweight history reference                              |
| `meta.tool`      | Identifies source tool for debugging or interoperability   |

---

## URL Encoding Rules

The final encoded value MUST satisfy:

```
data = LZString.compressToEncodedURIComponent(JSON.stringify(SaveData))
```

Example URL:

```
https://tools.tarp.tokyo/solitaire?data=ABc3k25fS0...
```

---

## Restoration Rules

Upon loading, tools MUST:

1. Read the `data` parameter.
2. Decode with `decompressFromEncodedURIComponent`.
3. Parse JSON back to `SaveData`.
4. Resolve version compatibility using the `v` field.
5. Safely reconstruct all state elements.

If no `data` is provided, tools SHOULD initialize default state.

---

## Versioning Policy

* The `v` field represents structural format of `SaveData`.
* New versions MUST NEVER break the ability to restore older data.
* When necessary, tools MAY transform older formats at restore time.

Example policy:

```
v1 → v2: new field added
v2 → v3: new nested structure
All legacy transforms MUST be handled in import logic
```

---

## Cross-Tool Compatibility

All TARP Tools are encouraged to share common fields for interoperability:

```ts
meta.tool
meta.timestamp
seed
```

Tools MAY define their own internal `state`.

All tools MUST follow the encoding/decoding and version semantics defined here.

---

## Recommended Enhancements (Optional)

* Automatic URL update on state change
* LocalStorage fallback mirrors of the last saved `data`
* Move-log storage (for games or editors)
* Seed-sharing for deterministic challenges
* Read-only replay mode

These are optional and MUST NOT affect backward compatibility of the `SaveData` structure.

---

## Security Considerations

* Only client-owned data SHOULD be encoded.
* Sensitive information MUST NOT be stored inside the URL.
* Tools MUST treat imported JSON as untrusted.

---

## License Notice

This specification and all associated TARP implementation patterns are protected under the **TARP Tools License**.
Unauthorized reproduction, reverse engineering, modification, or re-distribution is prohibited.

See:
`https://tools.tarp.tokyo/terms/`

---

## Revision History

| Version   | Date       | Changes               |
| --------- | ---------- | --------------------- |
| 1.0 | 2025-12-06 | Initial version |
