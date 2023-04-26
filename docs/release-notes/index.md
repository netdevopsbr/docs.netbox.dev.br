# Release Notes

NetBox releases are numbered as major, minor, and patch releases. For example, version 3.1.0 is a minor release, and v3.1.5 is a patch release. Briefly, these can be described as follows:

* **Major** - Introduces or removes an entire API or other core functionality
* **Minor** - Implements major new features but may include breaking changes for API consumers or other integrations
* **Patch** - A maintenance release which fixes bugs and may introduce backward-compatible enhancements

Minor releases are published in April, August, and December of each calendar year. Patch releases are published as needed to address bugs and fulfill minor feature requests, typically around every one to two weeks.

This page contains a history of all major and minor releases since NetBox v2.0. For more detail on a specific patch release, please see the release notes page for that specific minor release.

#### [Version 3.4](./version-3.4.md) (December 2022)

* New Global Search ([#10560](https://github.com/netbox-community/netbox/issues/10560))
* Virtual Device Contexts ([#7854](https://github.com/netbox-community/netbox/issues/7854))
* Saved Filters ([#9623](https://github.com/netbox-community/netbox/issues/9623))
* JSON/YAML Bulk Imports ([#4347](https://github.com/netbox-community/netbox/issues/4347))
* Update Existing Objects via Bulk Import ([#7961](https://github.com/netbox-community/netbox/issues/7961))
* Scheduled Reports & Scripts ([#8366](https://github.com/netbox-community/netbox/issues/8366))
* API for Staged Changes ([#10851](https://github.com/netbox-community/netbox/issues/10851))

#### [Version 3.3](./version-3.3.md) (August 2022)

* Multi-object Cable Terminations ([#9102](https://github.com/netbox-community/netbox/issues/9102))
* L2VPN Modeling ([#8157](https://github.com/netbox-community/netbox/issues/8157))
* PoE Interface Attributes ([#1099](https://github.com/netbox-community/netbox/issues/1099))
* Half-Height Rack Units ([#51](https://github.com/netbox-community/netbox/issues/51))
* Restrict API Tokens by Client IP ([#8233](https://github.com/netbox-community/netbox/issues/8233))
* Reference User in Permission Constraints ([#9074](https://github.com/netbox-community/netbox/issues/9074))
* Custom Field Grouping ([#8495](https://github.com/netbox-community/netbox/issues/8495))
* Toggle Custom Field Visibility ([#9166](https://github.com/netbox-community/netbox/issues/9166))

#### [Version 3.2](./version-3.2.md) (April 2022)

* Plugins Framework Extensions ([#8333](https://github.com/netbox-community/netbox/issues/8333))
* Modules & Module Types ([#7844](https://github.com/netbox-community/netbox/issues/7844))
* Custom Object Fields ([#7006](https://github.com/netbox-community/netbox/issues/7006))
* Custom Status Choices ([#8054](https://github.com/netbox-community/netbox/issues/8054))
* Improved User Preferences ([#7759](https://github.com/netbox-community/netbox/issues/7759))
* Inventory Item Roles ([#3087](https://github.com/netbox-community/netbox/issues/3087))
* Inventory Item Templates ([#8118](https://github.com/netbox-community/netbox/issues/8118))
* Service Templates ([#1591](https://github.com/netbox-community/netbox/issues/1591))
* Automatic Provisioning of Next Available VLANs ([#2658](https://github.com/netbox-community/netbox/issues/2658))

#### [Version 3.1](./version-3.1.md) (December 2021)

* Contact Objects ([#1344](https://github.com/netbox-community/netbox/issues/1344))
* Wireless Networks ([#3979](https://github.com/netbox-community/netbox/issues/3979))
* Dynamic Configuration Updates ([#5883](https://github.com/netbox-community/netbox/issues/5883))
* First Hop Redundancy Protocol (FHRP) Groups ([#6235](https://github.com/netbox-community/netbox/issues/6235))
* Conditional Webhooks ([#6238](https://github.com/netbox-community/netbox/issues/6238))
* Interface Bridging ([#6346](https://github.com/netbox-community/netbox/issues/6346))
* Multiple ASNs per Site ([#6732](https://github.com/netbox-community/netbox/issues/6732))
* Single Sign-On (SSO) Authentication ([#7649](https://github.com/netbox-community/netbox/issues/7649))

#### [Version 3.0](./version-3.0.md) (August 2021)

* Updated User Interface ([#5893](https://github.com/netbox-community/netbox/issues/5893))
* GraphQL API ([#2007](https://github.com/netbox-community/netbox/issues/2007))
* IP Ranges ([#834](https://github.com/netbox-community/netbox/issues/834))
* Custom Model Validation ([#5963](https://github.com/netbox-community/netbox/issues/5963))
* SVG Cable Traces ([#6000](https://github.com/netbox-community/netbox/issues/6000))
* New Views for Models Previously Under the Admin UI ([#6466](https://github.com/netbox-community/netbox/issues/6466))
* REST API Token Provisioning ([#5264](https://github.com/netbox-community/netbox/issues/5264))
* New Housekeeping Command ([#6590](https://github.com/netbox-community/netbox/issues/6590))
* Custom Queue Support for Plugins ([#6651](https://github.com/netbox-community/netbox/issues/6651))

#### [Version 2.11](./version-2.11.md) (April 2021)

* Journaling Support ([#151](https://github.com/netbox-community/netbox/issues/151))
* Parent Interface Assignments ([#1519](https://github.com/netbox-community/netbox/issues/1519))
* Pre- and Post-Change Snapshots in Webhooks ([#3451](https://github.com/netbox-community/netbox/issues/3451))
* Mark as Connected Without a Cable ([#3648](https://github.com/netbox-community/netbox/issues/3648))
* Allow Assigning Devices to Locations ([#4971](https://github.com/netbox-community/netbox/issues/4971))
* Dynamic Object Exports ([#4999](https://github.com/netbox-community/netbox/issues/4999))
* Variable Scope Support for VLAN Groups ([#5284](https://github.com/netbox-community/netbox/issues/5284))
* New Site Group Model ([#5892](https://github.com/netbox-community/netbox/issues/5892))
* Improved Change Logging ([#5913](https://github.com/netbox-community/netbox/issues/5913))
* Provider Network Modeling ([#5986](https://github.com/netbox-community/netbox/issues/5986))

#### [Version 2.10](./version-2.10.md) (December 2020)

* Route Targets ([#259](https://github.com/netbox-community/netbox/issues/259))
* REST API Bulk Deletion ([#3436](https://github.com/netbox-community/netbox/issues/3436))
* REST API Bulk Update ([#4882](https://github.com/netbox-community/netbox/issues/4882))
* Reimplementation of Custom Fields ([#4878](https://github.com/netbox-community/netbox/issues/4878))
* Improved Cable Trace Performance ([#4900](https://github.com/netbox-community/netbox/issues/4900))

#### [Version 2.9](./version-2.9.md) (August 2020)

* Object-Based Permissions ([#554](https://github.com/netbox-community/netbox/issues/554))
* Background Execution of Scripts & Reports ([#2006](https://github.com/netbox-community/netbox/issues/2006))
* Named Virtual Chassis ([#2018](https://github.com/netbox-community/netbox/issues/2018))
* Changes to Tag Creation ([#3703](https://github.com/netbox-community/netbox/issues/3703))
* Dedicated Model for VM Interfaces ([#4721](https://github.com/netbox-community/netbox/issues/4721))
* REST API Endpoints for Users and Groups ([#4877](https://github.com/netbox-community/netbox/issues/4877))

#### [Version 2.8](./version-2.8.md) (April 2020)

* Remote Authentication Support ([#2328](https://github.com/netbox-community/netbox/issues/2328))
* Plugins ([#3351](https://github.com/netbox-community/netbox/issues/3351))

#### [Version 2.7](./version-2.7.md) (January 2020)

* Enhanced Device Type Import ([#451](https://github.com/netbox-community/netbox/issues/451))
* Bulk Import of Device Components ([#822](https://github.com/netbox-community/netbox/issues/822))
* External File Storage ([#1814](https://github.com/netbox-community/netbox/issues/1814))
* Rack Elevations Rendered via SVG ([#2248](https://github.com/netbox-community/netbox/issues/2248))

#### [Version 2.6](./version-2.6.md) (June 2019)

* Power Panels and Feeds ([#54](https://github.com/netbox-community/netbox/issues/54))
* Caching ([#2647](https://github.com/netbox-community/netbox/issues/2647))
* View Permissions ([#323](https://github.com/netbox-community/netbox/issues/323))
* Custom Links ([#969](https://github.com/netbox-community/netbox/issues/969))
* Prometheus Metrics ([#3104](https://github.com/netbox-community/netbox/issues/3104))

#### [Version 2.5](./version-2.5.md) (December 2018)

* Patch Panels and Cables ([#20](https://github.com/netbox-community/netbox/issues/20))

#### [Version 2.4](./version-2.4.md) (August 2018)

* Webhooks ([#81](https://github.com/netbox-community/netbox/issues/81))
* Tagging ([#132](https://github.com/netbox-community/netbox/issues/132))
* Contextual Configuration Data ([#1349](https://github.com/netbox-community/netbox/issues/1349))
* Change Logging ([#1898](https://github.com/netbox-community/netbox/issues/1898))

#### [Version 2.3](./version-2.3.md) (February 2018)

* Virtual Chassis ([#99](https://github.com/netbox-community/netbox/issues/99))
* Interface VLAN Assignments ([#150](https://github.com/netbox-community/netbox/issues/150))
* Bulk Object Creation via the API ([#1553](https://github.com/netbox-community/netbox/issues/1553))
* Automatic Provisioning of Next Available Prefixes ([#1694](https://github.com/netbox-community/netbox/issues/1694))
* Bulk Renaming of Device/VM Components ([#1781](https://github.com/netbox-community/netbox/issues/1781))

#### [Version 2.2](./version-2.2.md) (October 2017)

* Virtual Machines and Clusters ([#142](https://github.com/netbox-community/netbox/issues/142))
* Custom Validation Reports ([#1511](https://github.com/netbox-community/netbox/issues/1511))

#### [Version 2.1](./version-2.1.md) (July 2017)

* IP Address Roles ([#819](https://github.com/netbox-community/netbox/issues/819))
* Automatic Provisioning of Next Available IP ([#1246](https://github.com/netbox-community/netbox/issues/1246))
* NAPALM Integration ([#1348](https://github.com/netbox-community/netbox/issues/1348))

#### [Version 2.0](./version-2.0.md) (May 2017)

* API 2.0 ([#113](https://github.com/netbox-community/netbox/issues/113))
* Image Attachments ([#152](https://github.com/netbox-community/netbox/issues/152))
* Global Search ([#159](https://github.com/netbox-community/netbox/issues/159))
* Rack Elevations View ([#951](https://github.com/netbox-community/netbox/issues/951))
