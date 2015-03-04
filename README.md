
# Usage

**Scenario 1:**

``` shell
# Run with weechat folder mounted to ~/vmdata/weechat on the host
mkdir -p ~/.weechat
docker run -t -i -d -v ~/.weechat:/weechat inanimate/weechat
```
``` shell
# Attach to the running weechat from the host
dtach -a ~/.weechat/weechat.dtach
```

**Scenario 2:**

```shell
# Bring weechat to the front without detaching. Note that you would want to run this in screen/tmux most likely.
docker run -t -i -v ~/.weechat:/weechat inanimate/weechat
```

In addition to the socket file, the shared volume will be used for
configuration and logging (`$HOME/.weechat` on a typical weechat installation).

> Note, we recommend you utilize Scenario 2. This is more straightforward and a more proper way to run docker containers with single apps:)


## Troubleshooting

If `dtach` in the guest has problems with permissions when creating
its file socket on the shared volume, it may be caused by SELinux.
This excerpt from `man docker-run` describes the problem and a workaround:

> When using SELinux, be aware that the host has no knowledge of container
> SELinux  policy.   Therefore, in the above example, if SELinux policy is
> enforced, the /var/db directory is not writable  to  the  container.   A
> "Permission Denied" message will occur and an avc: message in the host's
> syslog.
> 
> To work around this, at time of writing this  man  page,  the  following
> command  needs  to  be  run  in order for the proper SELinux policy type
> label to be attached to the host directory:
> 
>     # chcon -Rt svirt_sandbox_file_t /var/db
> 
> Now, writing to the /data1 volume in the container will be  allowed  and
> the changes will also be reflected on the host in /var/db.

The relevant command corresponding to the usage example would be:

``` shell
# Set the SELinux context of the ~/vmdata/weechat directory on the host
chcon -Rt svirt_sandbox_file_t ~/.weechat
```
