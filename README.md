# IPTV & VOD Media grabbing RO Providers

# Disclaimer

This script is not affiliated in any shape or form or commissioned/supported by
any providers used. For more information about their platforms, visit their
respective official website.

This script offers a way to retrieve the streams using the credentials PROVIDED
BY YOU.

THIS SCRIPT DOES NOT OFFER FREE IPTV.

THERE IS NO WARRANTY FOR THE SCRIPT, IT MIGHT NOT WORK AT ALL AND IT CAN BREAK
AND STOP WORKING AT ANY TIME.

---

This project is written in TypeScript and runs on Deno. It allows you to access
romanian media providers with the provided credentials and provides stream URLs
for the available media.

## Prerequisites

Before running this project, you must have Deno installed. You can download it
[here](https://deno.land/#installation).

## Providers

| Name        | Authentication Required |
| ----------- | ----------------------- |
| Digi24      | No                      |
| Digi-Online | Yes                     |
| AntenaPlay  | Yes                     |
| Voyo        | Yes                     |
| Pro-Plus    | Yes                     |

## Usage

See the [wiki](https://github.com/redmusicxd/iptvro_v2/wiki) for API
documentation

To use this project, run the following command:

`deno run --allow-read --allow-write --allow-env --allow-net src/index.ts`

A `configs` dir will then be created and inside it multiple .json files for each
module(provider) where you can input the appropriate credentials.

### Docker

Pull the image

`docker pull ghcr.io/rednblkx/iptvro_v2:main`

Run it

`docker run -it --init -p 8090:3000 -v ./logs:/app/logs -v ./configs:/app/configs ghcr.io/rednblkx/iptvro_v2:main`

As the image runs as non-root user (UID 1000, GID 1000), you need to make sure
the `configs` and `logs` directories have the right permissions.

The following command should ensure they have the right permissions

`chown -R 1000:1000 configs; chown -R 1000:1000 logs`

### VPS troubleshooting / reinstall (Docker)

Most VPS issues come from (1) port 3000 already used, (2) broken/old image, or (3) volume permissions (the container runs as UID/GID 1000).

- Ensure folders exist and are writable by UID 1000:
	- `mkdir -p configs logs`
	- `sudo chown -R 1000:1000 configs logs`
- Update to the latest image and restart:
	- `docker compose pull && docker compose up -d`
- Check if the container is restarting / unhealthy:
	- `docker compose ps`
	- `docker compose logs --tail=200 -f`
- If it fails to start, check if port 3000 is already in use:
	- `sudo lsof -i :3000` (or change the mapping in `docker-compose.yaml`, e.g. `8090:3000`)
- Quick endpoint check from the VPS host:
	- `curl -sS http://127.0.0.1:3000/modules | head`

If you paste the output of `docker compose ps` and the last ~200 lines from `docker compose logs --tail=200`, I can tell you exactly what broke.

### VPS reinstall from git (recommended)

If you want the latest fixes without waiting for the prebuilt GHCR image to update, build and run from this repository on your VPS:

- `git clone https://github.com/rednblkx/iptvro_v2.git && cd iptvro_v2`
- `bash scripts/vps_reinstall.sh`

This will build a local image, create/mount `configs/` + `logs/`, set UID/GID 1000 permissions, and run the container on port `8090`.

After install, run diagnostics:

- `bash scripts/check_iptv.sh 8090`

### AntenaPlay credentials (recommended)

AntenaPlay requires valid credentials.

- Set them in `configs/antena-play.json` under `auth.username` and `auth.password` (this avoids putting credentials in shell history).
- Do not commit real credentials into git; keep `configs/` only on the VPS and mount it into the container.
- Then refresh tokens via API:
	- `curl -sS -X POST -H 'content-type: application/json' -d '{}' http://localhost:8090/antena-play/login`
- Then update channels:
	- `curl -sS http://localhost:8090/antena-play/updatechannels`
- Validate:
	- `curl -sS http://localhost:8090/antena-play/live | head`

## Permissions

Deno needs the following permissions to run this project:

- `--allow-read`: Allows the application to read files.
- `--allow-write`: Allows the application to write files.
- `--allow-env`: Allows the application to access environment variables.
- `--allow-net`: Allows the application to make network requests.

## Contributing

If you'd like to contribute to this project, please fork the repository and
create a pull request with your changes.

## License

This project is licensed under the MIT license. See the [LICENSE](LICENSE) file
for more details.
