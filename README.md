# ISO20022Oracle

**The first MCP server for ISO 20022 stablecoin compliance.**

12 tools covering SWIFT MT→MX migration, Digital Token Identifiers (ISO 24165), structured address validation, MiCA cross-checks, and Travel Rule compliance.

```
npx -y mcp-remote https://mcp.feedoracle.io/iso20022/mcp/
```

## Why this exists

Three deadlines are converging:

| Deadline | What | Impact |
|----------|------|--------|
| **Nov 2025** | SWIFT MT retired | All cross-border payments must use MX (ISO 20022) |
| **Jul 2026** | MiCA enforcement | Unauthorized stablecoins blocked in EU |
| **Nov 2026** | Structured addresses | Unstructured postal addresses rejected by SWIFT/SEPA/CHAPS |

Banks, payment providers, and compliance teams need to verify that stablecoin settlement references, address formats, and authorization status are correct *before* sending an ISO 20022 message. This MCP automates those checks.

## Tools

| Tool | What it does |
|------|-------------|
| `validate_message` | Validate ISO 20022 XML (pacs.008, camt.053, pain.001) for schema compliance and Nov 2026 address deadline |
| `check_structured_address` | Check postal address against SWIFT/SEPA structured format requirements |
| `dti_lookup` | Digital Token Identifier (ISO 24165) for USDC, USDT, EURC, RLUSD, DAI, XRP, XLM, PYUSD |
| `generate_pacs008` | Generate pacs.008 XML skeleton with optional stablecoin settlement via DTI |
| `travel_rule_check` | FATF Travel Rule compliance check mapped to ISO 20022 fields |
| `stablecoin_iso_profile` | Complete ISO 20022 integration profile: DTI, networks, MiCA status, XRPL details |
| `mica_cross_check` | Can this stablecoin be used in EU ISO 20022 payment flows? PASS/WARN/BLOCK |
| `sanctions_screen_iso` | Screen originator/beneficiary from ISO 20022 messages against EU/OFAC/UN lists |
| `message_convert_check` | MT→MX migration status (MT103→pacs.008, MT940→camt.053, etc.) |
| `swift_deadlines` | All SWIFT/ISO 20022 compliance deadlines with countdown and urgency levels |
| `message_catalog` | All ISO 20022 message types relevant to stablecoin/crypto payments |
| `health_check` | Service status |

## Example: RLUSD settlement via XRPL

```json
{
  "name": "generate_pacs008",
  "arguments": {
    "debtor_name": "Deutsche Bank AG",
    "debtor_bic": "DEUTDEFF",
    "creditor_name": "Santander UK plc",
    "creditor_bic": "ABBYGB2L",
    "amount": "50000.00",
    "currency": "USD",
    "stablecoin": "RLUSD"
  }
}
```

Returns a complete pacs.008.001.10 XML with:
- Settlement method: `INGA` (Instructed Agent — on-chain)
- DTI reference: `DTI-RLUSD-RP01`
- Network: XRPL
- Structured addresses (Nov 2026 compliant)

## DTI Registry

| Token | DTI | Issuer | MiCA Status | Networks |
|-------|-----|--------|-------------|----------|
| USDC | DTI-4GBB49K10 | Circle | ✅ AUTHORIZED | Ethereum, Polygon, Solana, Base, + |
| EURC | DTI-EURC-CF01 | Circle | ✅ AUTHORIZED | Ethereum, Solana, Base, + |
| USDT | DTI-X9DT2GCM0 | Tether | ❌ NOT AUTHORIZED | Ethereum, Tron, Solana, + |
| RLUSD | DTI-RLUSD-RP01 | Ripple | ⏳ PENDING | XRPL, Ethereum |
| DAI | DTI-DAI-MK01 | MakerDAO | ❓ UNKNOWN | Ethereum, Polygon, + |

## Connect

**Claude Desktop / Cursor / Windsurf:**
```json
{
  "mcpServers": {
    "iso20022": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.feedoracle.io/iso20022/mcp/"]
    }
  }
}
```

**Direct HTTP:**
```bash
curl -X POST https://mcp.feedoracle.io/iso20022/mcp/ \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"dti_lookup","arguments":{"symbol":"RLUSD"}}}'
```

## Part of FeedOracle

ISO20022Oracle is part of the [FeedOracle](https://feedoracle.io) compliance evidence infrastructure and the [ToolOracle](https://tooloracle.io) MCP platform (50+ servers, 530+ tools).

Built with the [FeedOracle Enterprise Trust Layer](https://mcp.feedoracle.io/trust/mcp/) — ES256K signing, Evidence Registry, on-chain anchoring.

## License

MIT
