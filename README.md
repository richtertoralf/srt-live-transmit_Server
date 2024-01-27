# srt-live-transmit_Server
srt-live-transmit Server
>Ich habe Encoder, bei denen SRT nicht komplett implementiert ist. So wird mir z.B. keine Möglichkeit gegeben, eine `streamid`einzugeben. Dadurch kann ich zu einem Port immer nur einen Stream senden.
>Dieser simple Server bietet mir die Möglichkeit, einen SRT-Stream über einen Port zu empfangen, Informationen, wie die `streamid` zu ergänzen und dann an einen weiteren Server, z.B. einen `mediaMTX`-Server, weiterzuleiten.

```
Encoder --> srt-live-transmit Server --> mediaMTX Server
            xxx.xxx.xxx.xxx:xxx61        192.168.97.3:8890?streamid...cam61
            xxx.xxx.xxx.xxx:xxx62        192.168.97.3:8890?streamid...cam62
            xxx.xxx.xxx.xxx;xxx63        192.168.97.3:8890?dtreamid...cam63
```

- viruelle Server mit `public IP` bei z.B. Hetzner mit `Ubuntu` mieten
- privates Netzwerk einrichten, um die Server "lokal" verbinden zu können
- Ports in der Firewall freigeben, z.B. `UDP xxx61 bis xxx64`
- systemd service units anlegen

## srt-live-transmit installieren
```
apt install srt-tools
```
installierte Version anzeigen lassen:
```
srt-live-transmit -version
```
## Systemd service units anlegen
```
systemctl edit --full --force srtLiveTransCam61
```
und Folgendes einfügen:
```
[Unit]
Description=srt-live-transmit via Port 40061

[Service]
Typ=simple
ExecStart=/usr/bin/srt-live-transmit 'srt://0.0.0.0:40061?mode=listener' 'srt://192.168.97.3:8890?streamid=publish:cam61'
Restart=always

[Install]
WantedBy=multi-user.target
```
persistent einrichten:
```
systemctl stop srtLiveTransCam61
systemctl enable srtLiveTransCam61
systemctl start srtLiveTransCam61
systemctl status srtLiveTransCam61
```
Status verfolgen:
```
journalctl -u srtLiveTransCam61 -f
```
