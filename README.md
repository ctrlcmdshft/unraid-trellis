# Unofficial Trellis template for Unraid

[![Validate template](https://github.com/ctrlcmdshft/unraid-trellis/actions/workflows/validate.yml/badge.svg)](https://github.com/ctrlcmdshft/unraid-trellis/actions/workflows/validate.yml)

This repository provides an **unofficial community-maintained Unraid Docker template** for the upstream container image [`ghcr.io/lpierpoint/trellis:latest`](https://github.com/users/lpierpoint/packages/container/package/trellis).

It is not affiliated with, endorsed by, or maintained by Trellis, Strand, or their developers. Trellis itself is **not included, copied, modified, built, or redistributed** here. Unraid pulls the container image directly from its upstream publisher on GitHub Container Registry.

## Install on Unraid

### Prepare the appdata directory

Trellis runs inside the upstream container as the unprivileged user and group `10001:10001`. Before creating the container, open the Unraid terminal and prepare its appdata directory:

```bash
mkdir -p /mnt/user/appdata/trellis
chown -R 10001:10001 /mnt/user/appdata/trellis
chmod -R u+rwX /mnt/user/appdata/trellis
```

This allows Trellis to create `/config/trellis.config.json` without running the application as root. If you choose a different host appdata path, substitute that exact path in all three commands.

### Manual template install

1. Download [`templates/trellis.xml`](https://raw.githubusercontent.com/ctrlcmdshft/unraid-trellis/main/templates/trellis.xml).
2. Copy it to `/boot/config/plugins/dockerMan/templates-user/my-trellis.xml` on the Unraid server.
3. In the Unraid web interface, open **Docker**, choose **Add Container**, and select **Trellis** from the template list.
4. Review the settings and apply the template.
5. Open `http://YOUR-UNRAID-IP:8477`.

You can confirm the service is responding at `http://YOUR-UNRAID-IP:8477/System/Ping`.

Defaults:

| Setting | Value |
|---|---|
| Image | `ghcr.io/lpierpoint/trellis:latest` |
| Network | `bridge` |
| Web UI | `http://[IP]:[PORT:8477]` |
| Host port | `8477` |
| Container port | `8477/tcp` |
| Appdata | `/mnt/user/appdata/trellis` |
| Container config | `/config` |
| Restart policy | `unless-stopped` |

The host port and appdata path may be changed during installation. Do not change the container port or `/config` target unless the upstream image changes.

## Troubleshooting

### The WebUI does not open

1. Check that the container is running rather than stopped or repeatedly restarting.
2. Open **Docker → Trellis → Logs** in Unraid and inspect the first startup error.
3. Confirm the port mapping is `8477:8477/TCP`. If host port `8477` is already occupied, choose another host port in the template; keep the container port at `8477`.
4. Visit `http://YOUR-UNRAID-IP:8477/System/Ping`. A response means the service is running even if the main page is not loading.
5. If the logs mention `/config`, `trellis.config.json`, or permission denied, run the appdata preparation commands above and restart the container.

Do not solve appdata permission errors by enabling privileged mode or running the container as root. The upstream image is designed to run as user `10001`.

## Updates

The template tracks the upstream `latest` tag. When the publisher replaces that tag, Unraid's Docker update check can report an update and pull it directly from `ghcr.io/lpierpoint/trellis`. Persistent data remains in `/mnt/user/appdata/trellis`.

This repository does not mirror the image and does not control its release schedule. Because `latest` is mutable, review upstream changes and keep backups of appdata before updating.

## Docker Compose alternative

Unraid's standard Docker template is the supported path for this repository. A matching [`docker-compose.example.yml`](docker-compose.example.yml) is included for testing or other Compose-capable environments:

```bash
mkdir -p ./trellis-config
docker compose -f docker-compose.example.yml up -d
```

The example uses a local `./trellis-config` directory so it is portable. Set `TRELLIS_CONFIG_DIR=/mnt/user/appdata/trellis` to mirror the Unraid path.

When using a bind-mounted directory with Compose, ensure it is writable by UID/GID `10001:10001` before starting the service.

## Verified upstream image metadata

The `latest` OCI image was inspected directly from GHCR on 2026-09-20. At that time it:

- published Linux images for `amd64` and `arm64`;
- exposed `8477/tcp`;
- declared `/config` as a volume;
- set `PORT=8477` and `TRELLIS_CONFIG=/config/trellis.config.json`;
- included a health check against `/System/Ping`;
- ran as an unprivileged user; and
- declared its image license metadata as `NOASSERTION`.

These are observations of a mutable upstream tag, not a promise by this repository. No authoritative Trellis source-code license is granted or inferred here.

## Community Applications submission

The repository includes `ca_profile.xml` and a parser-compatible version-2 container template. Before submission, create an Unraid forum support topic if Community Applications review requires one, then replace the issue tracker support URL in the XML/profile with that topic. Validate and scan the repository through the [Unraid Community Apps submission portal](https://ca.unraid.net/submit/new).

## Branding and icon

The icon in `assets/` is an original generic lattice/container mark created for this integration. It intentionally contains no Trellis or Strand logo, wordmark, or copied artwork. It may be replaced only with artwork the contributor owns or has explicit permission to use. See [`assets/README.md`](assets/README.md).

## Support boundaries

- Template, documentation, or icon problem: [open an issue here](https://github.com/ctrlcmdshft/unraid-trellis/issues/new/choose).
- Trellis application or upstream image problem: contact the upstream publisher through an official channel, if one is available.
- Do not send credentials, tokens, configuration files, or private logs in an issue.

## License

The template, documentation, workflow, and original community icon in this repository are licensed under the MIT License; see [`LICENSE`](LICENSE). That license does **not** apply to Trellis, Strand, the upstream container image, or any third-party software.

See [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md) for upstream credit, trademark notices, verified license limitations, and acknowledgement of the Trellis/Strand developers, the upstream publisher, and the Unraid community.
