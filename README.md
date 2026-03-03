# Nina Science Scheduler Documentation

User documentation for the Nina Science Scheduler system.

**View the documentation:** https://first-light-systems.github.io/nina-scheduler-docs/

## About

This repository contains the user-facing documentation for the Nina Science Scheduler, a multi-observatory coordination system for astronomical observations.

## Building Locally

```bash
pip install mkdocs mkdocs-material
mkdocs serve
```

Then open `http://localhost:8000`.

To access from another desktop on your Tailscale VPN use the command `mkdocs serve -a 0.0.0.0:8000` then open `http://tailscale-ip-addr:8000` where tailscale-ip-addr is the IP of the node that mkdics was executed on.

## License

Copyright 2025-2026 First Light Observatory Systems LLC
