
## High Priority Implementation Items
These items should be implemented first as they represent core functionality changes or critical security features: 

| Feature                                           | Priority Level | Justification                                                | Implementation Notes                                         |
|---------------------------------------------------|----------------|--------------------------------------------------------------|--------------------------------------------------------------|
| **DCQL Support**                                  | HIGH           | Since removal of Presentation Definition, required for basic operation. | - MUST support receiving and processing the `dcql_query` parameter as a JSON object. <br>-The Wallet MUST parse the structure of a valid DCQL query. <br>- MUST handle mandatory `credentials` array with `id` and `format` fields. <br>- Claims Query Processing: If a Credential Query includes a claims array, the Wallet MUST process each Claims Query object within this array. <br>- MUST Implement Claims Path Pointer processing.<br> -Upon successful evaluation of the DCQL query and obtaining End-User consent, the Wallet MUST generate the `vp_token` response parameter. |
| **DCQL Structure and Parameter Changes**          | HIGH           | Enhances DCQL expression and is now required                 | - `trusted_authorities`: Added to the Credential Query and advanced claim filtering.<br>-`multiple` : Added to the Credential Query to explicitly allow requesting more than one Presentation matching a single query ID. |
| **DCQL Credential Format Requirements - `meta`**  | HIGH           | Certain parameters within the meta object of a Credential Query are now mandatory for specific formats. Previously optional. | Relevant for DIIP: <br> For **W3C VC:** `type_values` is now mandatory in the `meta` parameter. <br> **SD-JWT VC:** `vct_values` is now mandatory in the `meta` parameter. |
| **Client Identifier Prefixes**                    | HIGH           | Updated from "Client Identifier Schemes" and critical for verifier identification | - Update terminology in code, no longer scheme but now "prefix" <br>- Update metadata parameter to `client_id_prefixes_supported`<br>- Ensure supported prefixes are correctly processed. |
| **Format Identifier Updates**                     | HIGH           | Critical for SD-JWT VC interoperability                      | - Update `vc+sd-jwt` to `dc+sd-jwt`<br>- Update any related format-specific parameters<br>- Test with sample credentials |
| **Update `vp_formats` to `vp_formats_supported`** | HIGH           | The Verifier metadata parameter used to define supported formats and proof types is renamed to vp_formats_supported | Update all references from `vp_formats` to `vp_formats_supported` in Verifier metadata <br>- Update Wallet metadata parameter from `vp_formats_supported` to match new structure. |
| **VP Token Structure**                            | HIGH           | Essential for correct response handling                      | - Ensure VP Token is always a JSON object where keys are credential IDs and values are arrays <br>- Even single presentations must be wrapped in an array. <br>- Support single Presentation and multiple Presentations case <br>- When multiple=false or omitted, the array MUST contain only one element. |

## Medium Priority Implementation Items

Important but can be implemented after the high-priority items:

| Feature | Priority Level | Justification | Implementation Notes |
| ------- | -------------- | ------------- | -------------------- |
| **Credential Set Query** | MEDIUM | Optional. Enhances DCQL for more complex credential request scenarios | - Build on basic DCQL implementation<br>- Implement required/optional credential set handling<br>- Test with multiple credential options |

## Should Not Be Required for DIIPv4 
The following changes between draft 23 and draft 28 should not be required for DIIPv4.
| Feature                                  | Priority Level | Justification                                                | Implementation Notes                                         |
|------------------------------------------|----------------|--------------------------------------------------------------|--------------------------------------------------------------|
| **Request URI Method: post**             | LOW            | Enhancement for cross-device flows, but get works as fallback | - Implement if cross-device optimization is important<br>- Support `wallet_metadata` and `wallet_nonce` parameters |
| **Digital Credentials API Support**      | MEDIUM         | Important if targeting browser or mobile integration         | - Implement protocol value handling<br>- Support both signed and unsigned requests<br>- Support response through DC API |
| **Verifier Attestations**                | LOW            | Enhanced verifier trust model, but basic authentication works without it | - Implement if needed for high-assurance scenarios<br>- Start with the attestation types most relevant to your use cases |
| **Presentations Without Holder Binding** | HIGH           | Critical for supporting biometric binding, or claims-based binding | - Implement `require_cryptographic_holder_binding` parameter in DCQL<br>- MUST use `state` parameter when holder binding is disabled<br>- `state` MUST be cryptographically strong random with ≥128 bits entropy<br>- Verify `state` value is returned correctly in response<br>- Implement proper security considerations for non-bound presentations |
| **Response Encryption**                  | MEDIUM         | Security enhancement that replaces JARM                      | - Implement direct JWT encryption<br>- Support `direct_post.jwt` response mode<br>- Remove JARM-specific code |
| **SIOPv2 Integration**                   | LOW            | Optional enhancement, not required for basic operation       | - Implement if we require self-issued ID tokens<br>- Build on existing `vp_token id_token` implementation |
| **Enhanced Security Features**           | LOW            | Important but can be phased in                               | - Implement session fixation protection<br>- Enhance replay protection<br>- Implement secure value matching |
| **Transaction Data Support**             | HIGH           | Important for transaction authorization use cases            | - Implement `transaction_data` parameter<br>- Implement credential-specific transaction data handling<br>- If our wallets dont support this, then at the very least must return an error when receiving a request which includes `transaction_data` |

