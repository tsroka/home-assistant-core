# Install instructions

Images are pushed to the **private registry** at **`oneoffive.local:5000`**.

**Before the first `docker push` or `docker pull`**, install the registry CA and restart Docker on that machine. Full steps (Linux vs macOS, `docker login`, catalog `curl`, troubleshooting) are in **`ansible-config/DOCKER.md`** — for example `~/projects/ansible-config/DOCKER.md` or, if this repo and ansible-config are siblings, `../ansible-config/DOCKER.md`.

Summary: registry HTTPS on port **5000**; CA file is served at **`http://oneoffive.local/docker-registry-ca.crt`** and must be installed as **`ca.crt`** under Docker’s **`certs.d/oneoffive.local:5000/`** (then restart Docker / Docker Desktop).

---

## Build and push

```bash
docker buildx build --platform linux/arm64/v8 \
  -t oneoffive.local:5000/home-assistant-rts \
  -f Dockerfile.quick .

docker push oneoffive.local:5000/home-assistant-rts
```

## Pull and run (target host)

After the registry CA is installed on the host that will run the image:

```bash
docker pull oneoffive.local:5000/home-assistant-rts
```

## Save / restore (offline or without registry)

Save locally (image must be tagged or referenced by ID):

```bash
docker save oneoffive.local:5000/home-assistant-rts > home-assistant-rts.tar
scp -v home-assistant-rts.tar pi@darkmate.local:
```

Load:

```bash
podman load --input home-assistant-rts.tar
```

Run (adjust tag if you loaded a different name):

```bash
podman run --init --device=/dev/ttyACM0 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /etc/home-assistant:/config \
  --network=host \
  --name=homeassistant \
  oneoffive.local:5000/home-assistant-rts
```

On hosts that use **podman** to **pull** from the registry, configure trust for `oneoffive.local:5000` per Podman’s docs (same CA; paths differ from Docker Desktop).
