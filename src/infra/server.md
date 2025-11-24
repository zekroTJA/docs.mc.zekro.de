# Server

Der Minecraft-Server als auch Dienste wie Grafana für die Monitoring-Page laufen auf einem [bare metal](https://en.wikipedia.org/wiki/Bare-metal_server) Server bei [Hetzner](https://www.hetzner.com/).

## Hardware

- CPU: [Intel i7-7700](https://www.intel.com/content/www/us/en/products/sku/97128/intel-core-i77700-processor-8m-cache-up-to-4-20-ghz/specifications.html) (4c8t @ 4.2GHz)
- RAM: 2x16GiB DDR4
- Storage: 2x512GB SATA SSD (RAID 1)
- NIC: 1 Gbit [Intel I219-LM](https://www.intel.com/content/www/us/en/products/sku/82185/intel-ethernet-connection-i219lm/specifications.html)
- OS: Debian 12 (Bookworm)

![](/assets/server-fastfetch.png)
