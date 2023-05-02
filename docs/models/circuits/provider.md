# Fornecedor

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

A provider is any entity which provides some form of connectivity of among sites or organizations within a site. While this obviously includes carriers which offer Internet and private transit service, it might also include Internet exchange (IX) points and even organizations with whom you peer directly. Each [circuit](./circuit.md) within NetBox must be assigned a provider and a circuit ID which is unique to that provider.

## Fields

### Name

A unique human-friendly name.

### Slug

A unique URL-friendly identifier. (This value can be used for filtering.)

### ASN

The AS number assigned to this provider.

!!! warning "Legacy field"
    This field is being removed in NetBox v3.4. Users are highly encouraged to use the [ASN model](../ipam/asn.md) to track AS number assignment for providers.

### ASNs

The [AS numbers](../ipam/asn.md) assigned to this provider (optional).

### Account Number

The administrative account identifier tied to this provider for your organization.

### Portal URL

The URL for the provider's customer service portal.

### NOC Contact

Contact details for the provider's network operations center (NOC).

### Admin Contact

Administrative contact details for the provider.