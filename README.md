⚡ Endpoint Hero
A terminal-first HTTP request tester with a live observability dashboard.
Fire a request from the command line, and Endpoint Hero times it, logs it to a local database, and streams it straight into a dark, hacker-styled dashboard — right next to any webhooks you've captured, complete with one-click cURL replay.
� � �
What it does
Endpoint Hero is a small toolkit for testing and inspecting API calls without leaving your terminal:
cli.py sends an HTTP request of your choice, times it, and prints a breakdown (DNS / TCP / TLS / TTFB / total) — then logs the result to a local server.
server.py is a Flask app that stores every request and webhook in a SQLite database (endpointhero.db) and serves a live-refreshing dashboard at http://localhost:5000.
test_webhook.py fires a sample webhook payload at the server so you can see webhook capture and cURL replay in action.
It's built for quickly poking an endpoint, watching how it behaves, and keeping a running log you can glance back at — handy for debugging APIs, demoing webhooks, or just wanting a nicer alternative to plain curl output.
How it fits together
flowchart LR
    A[cli.py<br/>send a request] -- POST /api/log-request --> S[server.py<br/>Flask + SQLite]
    W[test_webhook.py<br/>or any real webhook] -- POST /api/log-webhook --> S
    S -- writes --> DB[(endpointhero.db)]
    D[Dashboard @ localhost:5000] -- GET /api/data every 1.5s --> S
    DB -.-> D
The CLI and the dashboard don't talk to each other directly — everything passes through the Flask server, which is also the single source of truth in the SQLite database.
Project structure
Endpoint-hero/
├── cli.py             # command-line request runner + timing
├── server.py          # Flask server, SQLite storage, dashboard UI
├── test_webhook.py     # sends a sample webhook for testing capture
├── endpointhero.db     # SQLite database (created automatically)
├── LICENSE
└── README.md
Note: in the current repo these are checked in as cli (1).py and server (1).py. Renaming them to cli.py and server.py will make the commands below match exactly — the examples use the clean names for readability.
Getting started
Prerequisites
Python 3.8+
pip
Install dependencies
pip install flask requests
1. Start the dashboard server
python server.py
This creates endpointhero.db (if it doesn't exist yet) and starts the server at http://localhost:5000 — open that URL in a browser to see the live dashboard.
2. Send a request with the CLI
In a separate terminal:
python cli.py GET https://api.github.com
⚡ ENDPOINT HERO
Sending GET request to https://api.github.com...

✓ Request successful
Status: 200 OK
Time: 312.45 ms

Network Timing
  DNS:  46.87 ms
  TCP:  62.49 ms
  TLS:  78.11 ms
  TTFB: 124.98 ms

💾 Logged to Observability Database.
Any HTTP method works (GET, POST, PUT, DELETE, ...) — the result is printed to your terminal and appears in the dashboard within about a second and a half.
3. Try webhook capture
python test_webhook.py
This posts a sample payload to the server's webhook endpoint, which then shows up in the dashboard's webhook panel along with a ready-to-copy curl command for replaying it.
API reference
The Flask server exposes:
Method
Endpoint
Purpose
GET
/
Serves the live dashboard UI
POST
/api/log-request
Logs a request's method, URL, status, and timing
POST
/api/log-webhook
Logs an incoming webhook's path, headers, and payload
GET
/api/data
Returns the 10 most recent requests and webhooks (JSON)
Since /api/log-webhook is a normal endpoint, you can point real third-party webhooks (Stripe, GitHub, etc.) at it during local development to capture and replay their payloads.
Tech stack
Python — CLI and server logic
Flask — HTTP server and API routes
SQLite3 — local, zero-config storage for logs
Tailwind CSS (via CDN) — dashboard styling
Requests — HTTP client for the CLI and test script
Ideas for extending this
Configurable server port instead of the hardcoded 5000
Filtering/searching past requests in the dashboard rather than just the last 10
Exporting logs (CSV/JSON) for sharing outside the dashboard
Real DNS/TCP/TLS timing (currently the CLI estimates these as percentages of total request time, not measured directly)
Basic auth or a token for the log-webhook endpoint before exposing it beyond localhost
License
Released under the MIT License.
Author
Built by Ananvaya.
bibekbrg
