# Systemd

[systemd](https://systemd.io/) is a system and services manager, sometimes referred to as an init system. It is now the default services manager on Ubuntu 16.04 and CentOS 7.

## Cheatsheet

These are the commands you're probably going to use the most:

```bash
master $ systemctl start [name.service]
master $ systemctl stop [name.service]
master $ systemctl restart [name.service]
master $ systemctl reload [name.service]
master $ systemctl status [name.service]
master $ systemctl is-active [name.service]
master $ systemctl list-units --type service --all
```

For example, checking the status of the `kubelet`

```bash
master $ systemctl status kubelet
```

systemd has 12 unit types. `.service` is system services, and when you're running any of the above commands you can leave off the .service extension, because systemd assumes a service unit if you don't specify something else.

The service definition file for kubelet is typically under

```bash
master $ ls -al /etc/systemd/system/kubelet.service.d/
```

If you fix something, you may need to run

```bash
node01 $ systemctl daemon-reload
```

Then restart the service

```bash
node01 $ systemctl restart kubelet
```


