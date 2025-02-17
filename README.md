# MQTT Install and Cloudflare Tunnel

## Run

```bash
mosquitto -c /etc/mosquitto/mosquitto.conf

mosquitto_sub -u test -P Test123 -h <ip> -p 1888 -t "rover1/status"

mosquitto_pub -u test -P Test123 -h <ip> -p 1888 -m "ON" -t "rover1/status"
```

---

## Installation

### Server Installation

```bash
sudo apt update
sudo apt install -y mosquitto

sudo systemctl status mosquitto
sudo systemctl restart mosquitto
```

### Client Installation

```bash
sudo apt install -y mosquitto-clients
```

### Testing

```bash
mosquitto_sub -t "home/lights/sitting_room"
mosquitto_pub -m "ON" -t "home/lights/sitting_room"
```

---

## Configuration

### Mosquitto Configuration

#### Editing Default Configuration

```bash
sudo nano /etc/mosquitto/conf.d/default.conf
```

Add the following lines:

```
allow_anonymous false
password_file /etc/mosquitto/passwd
```

#### Setting Up Password File

```bash
sudo nano /etc/mosquitto/passwd
```

Add users:

```
test:Test123
rover:Rover123
drone:Drone123
```

Then update password file:

```bash
sudo mosquitto_passwd -U /etc/mosquitto/passwd
sudo cat /etc/mosquitto/passwd
```

#### Updating Main Configuration File

```bash
sudo nano /etc/mosquitto/mosquitto.conf
```

Add the following:

```
listener 1888
listener 9001
protocol websockets
allow_anonymous false
password_file /etc/mosquitto/passwd
connection_messages true
log_timestamp true
```

---

## MQTT Tunnel with Cloudflare

### Creating a Cloudflare Tunnel

```bash
cloudflared tunnel create <NAME>
cloudflared tunnel list
```

### Configuring Cloudflare Tunnel

```bash
cd ~/.cloudflared/
nano config.yml
```

Add the following configuration:

```
tunnel: e29fddfentials-file: /root/.cloudflared/UUID.json

ingress:
  - hostname: domain.com
    service: ws://localhost:9001
  - service: http_status:404
```



### Running the Tunnel

```bash
cloudflared tunnel route dns <NAME> domain.com
cloudflared tunnel run <NAME>
```

### Installing and Starting Cloudflared Service

```bash
sudo cloudflared service install
sudo systemctl start cloudflared
sudo systemctl status cloudflared
```

---

## Notes

- Ensure Mosquitto is correctly configured before starting the service.
- Verify Cloudflare tunnel setup for remote access.
- Use `mosquitto_sub` and `mosquitto_pub` to test MQTT messaging.

---

## Author

Project by **[Your Name/Company]**

