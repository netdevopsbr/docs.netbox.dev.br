# Parâmetros Diversos (Miscellaneous)

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br): Essa página não foi traduzida ainda!

## ADMINS

NetBox will email details about critical errors to the administrators listed here. This should be a list of (name, email) tuples. For example:

```python
ADMINS = [
    ['Hank Hill', 'hhill@example.com'],
    ['Dale Gribble', 'dgribble@example.com'],
]
```

---

## BANNER_BOTTOM

!!! tip "Dynamic Configuration Parameter"

Sets content for the bottom banner in the user interface.

---

## BANNER_LOGIN

!!! tip "Dynamic Configuration Parameter"

This defines custom content to be displayed on the login page above the login form. HTML is allowed.

---

## BANNER_TOP

!!! tip "Dynamic Configuration Parameter"

Sets content for the top banner in the user interface.

!!! tip
    If you'd like the top and bottom banners to match, set the following:
    
    ```python
    BANNER_TOP = 'Your banner text'
    BANNER_BOTTOM = BANNER_TOP
    ```

---

## CHANGELOG_RETENTION

!!! tip "Dynamic Configuration Parameter"

Default: 90

The number of days to retain logged changes (object creations, updates, and deletions). Set this to `0` to retain
changes in the database indefinitely.

!!! warning
    If enabling indefinite changelog retention, it is recommended to periodically delete old entries. Otherwise, the database may eventually exceed capacity.

---

## ENFORCE_GLOBAL_UNIQUE

!!! tip "Dynamic Configuration Parameter"

Default: False

By default, NetBox will permit users to create duplicate prefixes and IP addresses in the global table (that is, those which are not assigned to any VRF). This behavior can be disabled by setting `ENFORCE_GLOBAL_UNIQUE` to True.

---

## `FILE_UPLOAD_MAX_MEMORY_SIZE`

Default: `2621440` (2.5 MB).

The maximum amount (in bytes) of uploaded data that will be held in memory before being written to the filesystem. Changing this setting can be useful for example to be able to upload files bigger than 2.5MB to custom scripts for processing.

---

## GRAPHQL_ENABLED

!!! tip "Dynamic Configuration Parameter"

Default: True

Setting this to False will disable the GraphQL API.

---

## JOBRESULT_RETENTION

!!! tip "Dynamic Configuration Parameter"

Default: 90

The number of days to retain job results (scripts and reports). Set this to `0` to retain
job results in the database indefinitely.

!!! warning
    If enabling indefinite job results retention, it is recommended to periodically delete old entries. Otherwise, the database may eventually exceed capacity.

---

## MAINTENANCE_MODE

!!! tip "Dynamic Configuration Parameter"

Default: False

Setting this to True will display a "maintenance mode" banner at the top of every page. Additionally, NetBox will no longer update a user's "last active" time upon login. This is to allow new logins when the database is in a read-only state. Recording of login times will resume when maintenance mode is disabled.

---

## MAPS_URL

!!! tip "Dynamic Configuration Parameter"

Default: `https://maps.google.com/?q=` (Google Maps)

This specifies the URL to use when presenting a map of a physical location by street address or GPS coordinates. The URL must accept either a free-form street address or a comma-separated pair of numeric coordinates appended to it.

---

## MAX_PAGE_SIZE

!!! tip "Dynamic Configuration Parameter"

Default: 1000

A web user or API consumer can request an arbitrary number of objects by appending the "limit" parameter to the URL (e.g. `?limit=1000`). This parameter defines the maximum acceptable limit. Setting this to `0` or `None` will allow a client to retrieve _all_ matching objects at once with no limit by specifying `?limit=0`.

---

## METRICS_ENABLED

Default: False

Toggle the availability Prometheus-compatible metrics at `/metrics`. See the [Prometheus Metrics](../integrations/prometheus-metrics.md) documentation for more details.

---

## PREFER_IPV4

!!! tip "Dynamic Configuration Parameter"

Default: False

When determining the primary IP address for a device, IPv6 is preferred over IPv4 by default. Set this to True to prefer IPv4 instead.

---

## QUEUE_MAPPINGS

Allows changing which queues are used internally for background tasks.

```python
QUEUE_MAPPINGS = {
    'webhook': 'low',
    'report': 'high',
    'script': 'high',
}
```

If no queue is defined the queue named `default` will be used.

---

## RELEASE_CHECK_URL

Default: None (disabled)

This parameter defines the URL of the repository that will be checked for new NetBox releases. When a new release is detected, a message will be displayed to administrative users on the home page. This can be set to the official repository (`'https://api.github.com/repos/netbox-community/netbox/releases'`) or a custom fork. Set this to `None` to disable automatic update checks.

!!! note
    The URL provided **must** be compatible with the [GitHub REST API](https://docs.github.com/en/rest).

---

## RQ_DEFAULT_TIMEOUT

Default: `300`

The maximum execution time of a background task (such as running a custom script), in seconds.