# Beszel Agent for Home Assistant

Monitor your Home Assistant system with Beszel. This app runs the Beszel agent and reports stats to your Beszel Hub.

## What it monitors:

- CPU, memory, disk, and network usage
- Home Assistant Apps (via Docker API)
- Historical data with trends

## Watchdog and Healthcheck

This app uses two internal ports:

- `45876/tcp` for the Beszel agent
- `45877/tcp` for a lightweight HTTP watchdog endpoint

## Connecting to a Hub with a self-signed certificate

This fork does not mount Home Assistant's `/ssl` folder (it can hold Home Assistant's own TLS private keys, and the homelab hub is plain HTTP on the LAN), so `CA_CERT_FILE` is not available here. Use a plain-HTTP or publicly trusted `hub_url`.

## Installation and Setup

Follow the [Installation and Setup Guide](https://github.com/vineetchoudhary/home-assistant-beszel-agent/blob/main/docs/INSTALLATION.md) to install the app.


## Need Help?

- [Report issues on GitHub](https://github.com/vineetchoudhary/home-assistant-beszel-agent/issues)
- [Check out Beszel docs](https://github.com/henrygd/beszel)

## License

MIT - see [LICENSE](https://github.com/vineetchoudhary/home-assistant-beszel-agent/blob/main/LICENSE)
