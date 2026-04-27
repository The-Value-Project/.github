# The Value Project

**Open schemas and specifications for B2B value-selling and sales-led pricing.**

The Value Project publishes a set of interoperable JSON schemas and accompanying specifications that represent the full commercial lifecycle of a B2B deal — from understanding what a customer values, to configuring a price, to issuing an auditable invoice.

These schemas are designed to be:

- **Machine-readable** — usable as structured output targets for LLMs (including OpenAI strict mode and Anthropic tool use)
- **Human-legible** — every constraint expressed in plain text, not code
- **Deterministic** — identical inputs produce identical outputs
- **Interoperable** — linked by stable IDs, composable across systems
- **Extensible** — core schemas define the standard; implementations may add fields without breaking conformance
- **Vendor-neutral** — applicable to any B2B SaaS or AI-backed product

---

## Repositories

### [`value-models`](https://github.com/the-value-project/value-models)

Schemas for understanding and quantifying customer value.

| Schema | Description |
|--------|-------------|
| `ValueModel` | Canonical Value Model (CVM) — defines variables, value drivers, equations, and tiers/modules for a product's value proposition |
| `CustomerVariables` | Customer-specific variable estimates with evidence sources, value driver applicability, and risk adjustments (execution risk + attribution percentage). Extensible to support additional customer intelligence fields. |

These schemas answer the question: *"What is this solution worth to this specific customer, and why?"*

→ [Browse the value-models repo](https://github.com/the-value-project/value-models)

---

### [`pricing-models`](https://github.com/the-value-project/pricing-models)

Schemas and a full specification for sales-led pricing, deal configuration, and invoice generation.

| Schema | Description |
|--------|-------------|
| `PricingModel` (PMS v1.4) | Structured, computable representation of a vendor's pricing — products, modules, tiers, plans, bundles, discounts, credit types, business rules |
| `DealConfiguration` | Customer-specific deal referencing a PMS instance — created by a salesperson or LLM |
| `InvoiceStatement` | Computed pricing output with full audit trail — line items, adjustments, entitlement summary |

These schemas, together with the accompanying specification, answer the question: *"Given what this customer wants to buy, what do they owe, and why?"*

→ [Browse the pricing-models repo](https://github.com/the-value-project/pricing-models)

---

## How the Schemas Relate

```
ValueModel ───────────────────────────────────────────────────────────┐
  Defines variables, drivers, equations, tiers/modules                │
                                                                       │
CustomerVariables ─────────────────────────────────────────────────┐  │
  Variable estimates (with confidence + sources)                    │  │
  Driver applicability + risk adjustments                           │  │
  References: ValueModel variables + value drivers                  │  │
                                                                    ▼  ▼
                                               [Quantified Value & Deal Recommendation]
                                                                       │
                                                                       ▼
PricingModel (PMS) ────────────────────────────────────────────────────┐
  Vendor's product catalog, modules, tiers, plans, rules              │
  tiers_and_modules referenced by ValueModel                          │
                                                                       │
DealConfiguration ─────────────────────────────────────────────────────┤
  Selects from PMS by ID (product_id, tier_id, plan_id, etc.)         │
  References: PricingModel.model_id                                    │
                                                                       │
                                                                       ▼
InvoiceStatement ──────────────────────────────────────────────────────┘
  Computed output: line items, adjustments, entitlements, totals
  References: DealConfiguration.config_id
```

The value schemas inform *which* product tier and modules best match a customer's needs and at what ROI. The pricing schemas compute the price of that configuration.

---

## Design Principles

1. **Structure over equations.** Price and value emerge from declared objects and primitives — no free-form expressions.
2. **Plain-text business rules.** Every constraint is stated in natural language interpretable by both humans and LLMs.
3. **Deterministic evaluation.** Identical inputs produce identical results.
4. **Auditability.** Every invoice line and every value claim traces to a source field.
5. **Extensible entitlements.** Any unit of value (seats, credits, API calls, tokens) is modeled generically via `credit_types` — never as hardcoded fields.
6. **Separation of concerns.** The model defines what's available. The configuration captures what was chosen. The output records what was computed.
7. **Extensibility by design.** Core schemas define the required standard. Implementations may add fields — or build richer schemas that extend the standard — without breaking conformance.
8. **Self-documenting.** Every schema field carries a description sufficient for an LLM or engineer to understand and apply it without external documentation.
9. **LLM-native.** Schemas are designed as structured output targets for frontier LLMs (GPT-4o strict mode, Anthropic tool use) and as context documents for agentic workflows.

---

## Schema Versioning

| Schema | Current Version | Stability |
|--------|----------------|-----------|
| `ValueModel` | 1.0 | Implementation-Ready |
| `CustomerVariables` | 1.0 | Implementation-Ready |
| `PricingModel` (PMS) | 1.4 | Implementation-Ready |
| `DealConfiguration` | 1.4 | Implementation-Ready |
| `InvoiceStatement` | 1.4 | Implementation-Ready |

Versions follow `MAJOR.MINOR`. Breaking changes increment MAJOR. Additive, backward-compatible changes increment MINOR.

---

## Contributing

The Value Project is in early-stage development. If you are building value-selling tooling, sales CPQ systems, or AI-assisted deal workflows, we welcome feedback on the schemas and specifications.

Please open issues in the relevant repository or reach out via [valueiq.ai](https://valueiq.ai).

---

*The Value Project is an initiative by [ValueIQ](https://valueiq.ai).*
