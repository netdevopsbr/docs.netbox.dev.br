# Route Targets

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

A route target is a particular type of [extended BGP community](https://tools.ietf.org/html/rfc4360#section-4) used to control the redistribution of routes among VRF tables in a network. Route targets can be assigned to individual VRFs in NetBox as import or export targets (or both) to model this exchange in an L3VPN. Each route target must be given a unique name, which should be in a format prescribed by [RFC 4364](https://tools.ietf.org/html/rfc4364#section-4.2), similar to a VR route distinguisher.

## Fields

### Name

The route target identifier formatted in accordance with [RFC 4360](https://tools.ietf.org/html/rfc4360).
