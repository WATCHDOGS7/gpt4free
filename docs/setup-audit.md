# Source setup audit — 2026-11-10

## Scope and method

Audited revision: `502ed74dcde8e3a0fdab058b5a47f9ab2c43d833`, then the changes in
this pull request. The environment was Linux x86-64, Python 3.12.3, pip 24.0,
Docker Engine 29.1.3, and Docker Compose 2.40.3.

The host already had Git, Python, and Docker. To avoid the workspace's
preinstalled dependencies, the original tracked source was exported with
`git archive HEAD` into a temporary directory, and two fresh virtual environments
were created without system site packages. This exercises a clean checkout's
Python setup, **not** installation on a freshly provisioned operating system.
Package downloads could use the host's download cache. No credentials, browser
profile, or model downloads were used. No live chat-provider requests were sent.

The README's source-install link was followed through `docs/git.md`, first with
`requirements-min.txt`, then with `requirements.txt` in a separate environment.
The existing CI command (`python -m etc.unittest`) was used because the original
guide supplied no test/build command. Docker commands were attempted separately.

## Step-by-step findings

| Step / original instruction | Observed result or missing information | Change / disposition |
| --- | --- | --- |
| Clone, then `cd gpt4free` | Commands are valid; the documented upstream URL need not match a contributor's fork. The audit used a clean source export of the selected fork revision, not today's upstream HEAD. | Explain using the fork URL and running commands from the repository root. |
| Optional virtual environment, then unconditional activation | Prerequisites did not mention Git, pip/venv, OS-managed Python, or Windows shell differences. | Make environment creation part of the source path; document PowerShell, Command Prompt, direct interpreter paths, and Debian/Ubuntu's venv package. |
| `pip install -r requirements-min.txt` | Succeeded; `import g4f` succeeded. Requirements alone do not install the `g4f` console entry point. | Add `-e .`, interpreter-qualified pip commands, and `pip check`. Distinguish checkout installs from PyPI releases. |
| “Create a test.py … import g4f” | No executable verification/build instructions. The CI unit suite succeeded: 26 tests, 8 optional skips. The uninstalled export printed `Version not found`. | Document the unit-test module, expected minimum skips, CLI help, wheel build, and development-version messages. |
| `pip install -r requirements.txt` | Installation and `pip check` succeeded. The unit suite reported success with 5 GUI skips despite Flask being installed. | Investigated rather than accepting the skips as GUI verification; see the next row. |
| Full-dependency GUI tests | `ImportError: cannot import name 'get_error_message' from 'g4f.gui.server.backend'`. The broad import guard hid a stale test import as “gui is not installed.” | Import the helper from its current module, `g4f.gui.server.api`. No assertions were removed or weakened. Four previously skipped tests now pass. |
| README GUI command | Full-dependency GUI started and `/chat/` returned HTTP 200. Chrome is not needed for this startup check. | Add explicit GUI/API extras, loopback binding, URLs, and shutdown instructions. |
| API docs: `python -m g4f.api.run` | Started successfully, but on port **10000**, while the client example used **1337**. | Use `python -m g4f.cli api --bind 127.0.0.1:1337`; document the legacy port. |
| German README: `python -m g4f.api` | Failed: `No module named g4f.api.__main__`. | Use the CLI API launcher and link the shared source/Docker guides. |
| API OpenAI example | Static parsing failed with `IndentationError: unexpected indent`; no OpenAI-client installation step was supplied. | Fix indentation, show `python -m pip install openai`, and give a consistent streaming example. Only syntax was verified, not a live chat request. |
| PyPI extras examples | Unquoted square brackets are shell-pattern syntax in shells such as zsh; installing a release does not install the cloned revision. This was a static documentation finding, not a reproduced zsh failure. | Quote extras and separate release instructions from editable source installs. |
| Contribution guide | Pointed at the obsolete `testing/` directory and omitted setup/test instructions. | Link `etc/testing/` and the source guide; distinguish live-provider scripts from mock-provider unit tests. |
| Docker: `docker pull selenium/node-chrome` | Pull succeeded, but this is not the Dockerfile's `seleniarm/node-chromium` base and does not prepare that build. | Remove the unnecessary pull. |
| Docker: `docker-compose build` | Failed: `docker-compose: command not found`; Compose v2 was installed. | Use `docker compose` in the shared guide. |
| `docker compose config -q` | Succeeded with an obsolete top-level `version` warning. | Remove the obsolete field; configuration validation now succeeds without that warning. |
| `docker compose build` | Failed in the base image's `apt-get install git` with dpkg exit 100: `openssl-provider-legacy` attempts to overwrite `/usr/lib/x86_64-linux-gnu/ossl-modules/legacy.so`, owned by `libssl3t64`. | Document the source-build blocker prominently and direct newcomers to the working Python path. Repairing/migrating the browser base image is outside this documentation/small-script change. |
| `docker compose up` / container endpoint checks | Not attempted after the failed build. | Clearly mark source Docker startup unverified; document the intended GUI, API, and noVNC endpoints rather than claiming they passed. |

## Verification after the changes

The updated minimum path was repeated in another clean source export with the
patch applied and a new virtual environment:

```sh
python3 -m venv venv
source venv/bin/activate
python -m pip install -r requirements-min.txt -e .
python -m pip check
python -m etc.unittest -v
python -m g4f.cli --help
python -m pip wheel --no-deps --wheel-dir dist .
```

Results: dependency check passed, 26 tests completed successfully with 8 expected
optional skips, CLI help succeeded, and `g4f-0.0.0-py3-none-any.whl` was built.
The full-requirements environment also passed 26 tests after the test-import fix,
with only the web-search test skipped (25 passed).

Installing `python -m pip install -e '.[gui,api]'` in the new minimum environment
also succeeded. `pip check` and `g4f --help` passed; the documented loopback GUI
and API commands returned HTTP 200 at `/chat/` and `/v1/models`. The latter
returned a nonempty JSON array of model entries. The extras environment's unit
suite passed with one web-search skip as well. These checks cover local startup,
not successful provider chat responses.

## Remaining limitations

- `duckduckgo-search` resolved to 8.1.1, which no longer supplies
  `duckduckgo_search.duckduckgo_search_async.AsyncDDGS`, used by this revision.
  The full test run therefore still skips web search. This audit does not claim
  to repair external search/provider compatibility or pin a historical service
  integration merely to remove the skip.
- Docker source builds remain blocked as described above. The README's published
  image is a separate release and was not run as a substitute for the checkout.
- Windows, macOS, Android/Pydroid, browser login flows, local-model inference, and
  live provider availability were not exercised. The Android guide is not part
  of the verified desktop setup path.
- The latest PyPI release was not substituted for this old source revision.
  Dependency versions can change; these results describe the audit environment,
  not a locked dependency set or a claim of compatibility with every Python 3.x.

[Return to source setup](git.md) · [Return to README](../README.md)
