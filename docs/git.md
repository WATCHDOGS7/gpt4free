# G4F — install and test from source

## Prerequisites

- Git and Python with `pip` and `venv`. Python 3.12 was used for the
  [setup audit](setup-audit.md); the README recommends Python 3.10 or newer.
- Internet access to download dependencies. No provider account, API key,
  Chrome installation, or local model is needed for the minimum test run.
- On Debian/Ubuntu, if `python3 -m venv` reports that `ensurepip` is unavailable,
  install the matching venv package (for the system Python:
  `sudo apt-get install python3-venv`).

## 1. Clone and enter the repository

```sh
git clone https://github.com/xtekky/gpt4free.git
cd gpt4free
```

For a fork, use that fork's clone URL instead. Run all subsequent commands from
this directory, not from inside `g4f/`.

## 2. Create and activate a virtual environment

Use a virtual environment rather than installing into the OS-managed Python.
On macOS/Linux (bash or zsh):

```sh
python3 -m venv venv
source venv/bin/activate
```

On Windows (PowerShell):

```powershell
py -3.12 -m venv venv
.\venv\Scripts\Activate.ps1
```

In Windows Command Prompt, activate with `venv\Scripts\activate.bat` instead.
If activation is unavailable, use `venv/bin/python` (macOS/Linux) or
`venv\Scripts\python.exe` (Windows) in place of `python` below.

## 3. Install the checkout

```sh
python -m pip install -r requirements-min.txt -e .
python -m pip check
```

The editable install (`-e .`) registers this checkout and the `g4f` command.
Installing only a requirements file does not install that command. Do not use
`pip install -U g4f` here: that installs a published release, not your checkout.

## 4. Run the unit tests

```sh
python -m etc.unittest -v
python -m g4f.cli --help
```

The minimum environment runs 26 tests with 8 optional-dependency skips in the
audited revision. `OK (skipped=8)` is a successful minimum run, not proof that
all providers or the GUI work. These tests use mock chat providers; examples in
`etc/testing/` contact external services and are not the setup smoke test.
Version checks can access PyPI/GitHub; a source checkout without release metadata
may report `Version not found`, and an editable development install may report
version `0.0.0`. Neither message means the unit tests failed.

To build a wheel as well (no provider credentials required):

```sh
python -m pip wheel --no-deps --wheel-dir dist .
```

## 5. Optional: run the GUI or API

Install the relevant extras from **this checkout**:

```sh
python -m pip install -e '.[gui,api]'
```

Or install the broader developer requirements (including local-model and browser
packages):

```sh
python -m pip install -r requirements.txt -e .
```

Run one server at a time, or use separate terminals with the environment active:

```sh
python -m g4f.cli gui -host 127.0.0.1 -port 8080
```

Open <http://127.0.0.1:8080/chat/>. For the API:

```sh
python -m g4f.cli api --bind 127.0.0.1:1337
```

Open <http://127.0.0.1:1337/v1/models> to check that the API is responding without
sending a chat request. Stop either server with Ctrl+C. These are local
development servers; do not expose them publicly without appropriate security.

Chat requests, browser-backed providers, web search, and local inference have
additional requirements and depend on external service/package compatibility.
A successful install or HTTP smoke check does not verify those integrations.
See [additional requirements](requirements.md) and the
[audit's known limitations](setup-audit.md).

[Return to README](../README.md)
