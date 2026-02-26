# Disable host DNS
Otherwise the port is already in use.

```bash
sudo nano /etc/systemd/resolved.conf 
```
Uncomment `#DNSStubListener=yes` and change to `DNSStubListener=no` then save and exit.

Then restart systemd-resolved:

```bash
sudo systemctl restart systemd-resolved
```