# DESIGN.md — Project 1 EC2 REST Service

Architecture
- Single EC2 instance (Amazon Linux 2, t3.micro).
- Node.js with Express runs service logic.
- `morgan` middleware logs HTTP requests.
- Managed via `systemd` for automatic restart and boot persistence.

API Contract
- `GET /convert?lbs=<number>`
  - Response (200): JSON { lbs, kg, formula }
  - 400: missing or not numeric
  - 422: negative or not finite

Conversion Logic
- Formula: `kg = lbs * 0.45359237`
- Rounded to 3 decimals: `Math.round(value * 1000) / 1000`

Error Handling
- Missing query param → 400
- Non-numeric input → 400
- Negative numbers → 422
- Infinity / NaN → 422

Logging
- All requests logged in Apache combined format using `morgan`.
- Logs collected by systemd (`journalctl -u p1`).

Deployment & Reliability
- Service managed by `systemd`.
- `Restart=always` ensures auto-restart if it crashes.
- `WantedBy=multi-user.target` ensures startup on reboot.
