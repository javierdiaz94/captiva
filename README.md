# Captiva AutoLogin

Captiva AutoLogin is a Linux service designed to automatically detect loss of internet connectivity, WiFi disconnections, MAC address changes, and captive portal interruptions. When any of these events occur, the service performs an automated login procedure to restore connectivity. It is intended for environments in which the network periodically drops or requires repeated authentication through a captive portal.

## Features

* Detects loss of internet access using ICMP ping checks.
* Detects WiFi interface disconnections.
* Detects MAC address changes, including randomization events.
* Performs automated captive portal login.
* Provides desktop notifications via Plyer and notify-send.
* Runs as a systemd service with automatic restart capability.
* Produces detailed logs accessible through journalctl.
* Lightweight implementation without unnecessary dependencies.

## Directory Structure

/opt/captiva/
captiva.py
credentials.json (optional)
README.md

## Installation

1. Clone the repository:
   `sudo git clone https://github.com/YOUR_USERNAME/captiva-autologin /opt/captiva`

2. Install dependencies:
   `sudo apt install python3 python3-pip`
   `pip install requests plyer`

3. Create a systemd service file:
   `sudo nano /etc/systemd/system/captiva.service`

   Insert the following content:

```
[Unit]
Description=Captiva Auto Login Service
After=network-online.target

[Service]
ExecStart=/usr/bin/python3 /opt/captiva/captiva.py
WorkingDirectory=/opt/captiva
Restart=always
RestartSec=3
User=YOUR_USERNAME
Environment=DISPLAY=:0
Environment=XAUTHORITY=/home/YOUR_USERNAME/.Xauthority

[Install]
WantedBy=multi-user.target
```

4. Reload systemd:
   `sudo systemctl daemon-reload`

5. Start the service:
   `sudo systemctl start captiva.service`

6. Enable it at boot:
   `sudo systemctl enable captiva.service`

## Logging

View the most recent logs:
`journalctl -u captiva.service -e`

View logs from today:
`journalctl -u captiva.service --since=today`

Stream logs in real time:
`journalctl -u captiva.service -f`

## Testing

Recommended tests:

1. Disable the WiFi adapter. The service should log and notify a disconnection event.
2. Reconnect to the WiFi network without completing the captive portal login. The service should perform the login automatically.
3. Change the MAC address manually using:
   `sudo ip link set wlan0 address 00:11:22:33:44:55`
   The service should detect the change and trigger a login procedure.

## Troubleshooting

Plyer notifications do not appear:
Ensure the service is running under your user account rather than root. Confirm the correctness of the DISPLAY and XAUTHORITY environment variables.

The script does not start:
Check the service status for errors:
`systemctl status captiva.service`

Plyer is not available in the system environment:
Install it system-wide using:
`sudo pip install plyer`

## Security

* Does not transmit sensitive data externally.
* Does not store credentials unless explicitly configured via an optional JSON file.
* Does not execute unsafe system commands.
* Does not require root privileges during runtime, only during installation.

## License

MIT License.

## Author

Angel 
