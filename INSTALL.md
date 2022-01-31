# Install instructions

Build
`docker buildx build --platform linux/arm/v7 -t home-assistant-rts -f Dockerfile.quick .`

Save
`docker save home-assistant-rts | xz > home-assistant-rts.tar.xz && scp -v  home-assistant-rts.tar.xz pi@darkmate.local:`

Load
`podman load --input home-assistant-rts.tar`

Run
`podman run   --init --device=/dev/ttyACM0 -v /etc/localtime:/etc/localtime:ro -v /etc/home-assistant:/config --network=host --name=homeassistant home-assistant-rts`