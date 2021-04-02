apt install -y --no-install-recommends pulseaudio


# PulseAudio system daemon
cat <<'EOF' > /etc/systemd/system/pulseaudio.service
[Unit]
Description=PulseAudio Daemon
[Install]
WantedBy=multi-user.target
[Service]
Type=simple
PrivateTmp=true
ExecStart=/usr/bin/pulseaudio --system --disallow-exit --disable-shm --exit-idle-time=-1
EOF
systemctl enable pulseaudio.service
systemctl start pulseaudio.service

---


# PulseAudio settings
#mv /etc/pulse/daemon.conf /etc/pulse/daemon.conf.orig
#echo "resample-method = ffmpeg" > /etc/pulse/daemon.conf
#sed -i.orig 's/^load-module module-udev-detect$/load-module module-udev-detect tsched=0/' /etc/pulse/system.pa
echo "load-module module-bluetooth-policy" >> /etc/pulse/system.pa
echo "load-module module-bluetooth-discover" >> /etc/pulse/system.pa

mv /etc/pulse/client.conf /etc/pulse/client.conf.orig
cat <<'EOF' >> /etc/pulse/client.conf
default-server = /var/run/pulse/native
autospawn = no
EOF

usermod -a -G pulse-access root
usermod -a -G bluetooth pulse

# PulseAudio system daemon
cat <<'EOF' > /etc/systemd/system/pulseaudio.service
[Unit]
Description=PulseAudio Daemon
[Install]
WantedBy=multi-user.target
[Service]
Type=simple
PrivateTmp=true
ExecStart=/usr/bin/pulseaudio --system --disallow-exit --disable-shm --exit-idle-time=-1
EOF
systemctl enable pulseaudio.service
systemctl start pulseaudio.service
