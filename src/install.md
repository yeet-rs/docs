# Installing on a non NixOS server

This may sometimes be preferred because if you run yeetd on a yeet provisioned hosts yeetd would provision itself. Based on your threat modell this may not be advised.

## Required Packages

- gcc
- cargo (as root): `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

## Install yeetd

`sudo cargo install --git https://github.com/yeet-rs/yeet.git yeetd  --root /usr/local`

copy the systemd service from `yeet-server/yeetd.service` and past it into `sudo systemctl edit --force --full yeetd.service`.

Modify the relevant ENV variables (YEET_CERT / YEET_CERT_KEY)
