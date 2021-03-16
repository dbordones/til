# Multiple instances for PiHole DNS (docker-compose.yml)
```
version: "2.1"
services:

  pihole1:
    networks:
      dnsnet:
        ipv4_address: 192.168.100.2
    container_name: ph1
    image: pihole/pihole:latest
    ports:
      - "1053:53/tcp"
      - "1053:53/udp"
      - "1067:67/udp"
      - "1080:80/tcp"
      - "1043:443/tcp"
    environment:
      TZ: 'America/Santiago'
      WEBPASSWORD: 'P4r4l3l3p1p3d0!'
      IPv6: 'false'
      DNSMASQ_LISTENING: 'all'
      ServerIP: '192.168.100.2'
      SKIPGRAVITYONBOOT: '1'
      DNS_FQDN_REQUIRED: 'true'
      DNS_BOGUS_PRIV: 'false'
      PIHOLE_DNS_: '9.9.9.11;149.112.112.11;8.8.8.8;8.8.4.4'
    volumes:
       - './pihole1/etc-pihole/:/etc/pihole/'
       - './pihole1/etc-dnsmasq.d/:/etc/dnsmasq.d/'
    dns:
      - 127.0.0.1
      - 1.1.1.1
    cap_add:
      - NET_ADMIN
      - SYS_NICE
      - NET_BIND_SERVICE
      - NET_RAW
    restart: unless-stopped

  pihole2:
    depends_on:
      - pihole1
    networks:
      dnsnet:
        ipv4_address: 192.168.100.3
    container_name: ph2
    image: pihole/pihole:latest
    ports:
      - "2053:53/tcp"
      - "2053:53/udp"
      - "2067:67/udp"
      - "2080:80/tcp"
      - "2043:443/tcp"
    environment:
      TZ: 'America/Santiago'
      WEBPASSWORD: 'P4r4l3l3p1p3d0!'
      IPv6: 'false'
      DNSMASQ_LISTENING: 'all'
      ServerIP: '192.168.100.2'
      SKIPGRAVITYONBOOT: '1'
      DNS_FQDN_REQUIRED: 'true'
      DNS_BOGUS_PRIV: 'false'
      PIHOLE_DNS_: '208.67.222.222;208.67.220.220;9.9.9.11;149.112.112.11'
    volumes:
       - './pihole2/etc-pihole/:/etc/pihole/'
       - './pihole2/etc-dnsmasq.d/:/etc/dnsmasq.d/'
    dns:
      - 127.0.0.1
      - 1.1.1.1
    cap_add:
      - NET_ADMIN
      - SYS_NICE
      - NET_BIND_SERVICE
      - NET_RAW
    restart: unless-stopped

networks:
    dnsnet:
        enable_ipv6: false
        driver: bridge
        driver_opts:
            com.docker.network.enable_ipv6: "false"
        ipam:
            driver: default
            config:
                - subnet: 192.168.100.0/28
                  gateway: 192.168.100.1

```