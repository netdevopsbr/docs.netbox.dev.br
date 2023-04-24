# Housekeeping

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

NetBox includes a `housekeeping` management command that should be run nightly. This command handles:

* Clearing expired authentication sessions from the database
* Deleting changelog records older than the configured [retention time](../configuration/miscellaneous.md#changelog_retention)
* Deleting job result records older than the configured [retention time](../configuration/miscellaneous.md#jobresult_retention)
* Check for new NetBox releases (if [`RELEASE_CHECK_URL`](../configuration/miscellaneous.md#release_check_url) is set)

This command can be invoked directly, or by using the shell script provided at `/opt/netbox/contrib/netbox-housekeeping.sh`. This script can be linked from your cron scheduler's daily jobs directory (e.g. `/etc/cron.daily`) or referenced directly within the cron configuration file.

```shell
sudo ln -s /opt/netbox/contrib/netbox-housekeeping.sh /etc/cron.daily/netbox-housekeeping
```

!!! note
    On Debian-based systems, be sure to omit the `.sh` file extension when linking to the script from within a cron directory. Otherwise, the task may not run.

The `housekeeping` command can also be run manually at any time: Running the command outside scheduled execution times will not interfere with its operation.