<img alt="gpt4free logo" src="https://user-images.githubusercontent.com/98614666/233799515-1a7cb6a3-b17f-42c4-956d-8d2a0664466f.png">

### Set up and verify your checkout

Follow the [source setup guide](docs/git.md) before making changes. It covers
prerequisites, a virtual environment, editable installation, and a wheel build.
Run the mock-provider unit suite from the repository root:

```sh
python -m etc.unittest -v
```

Optional dependencies cause documented skips in the minimum environment.
Scripts in `etc/testing/` exercise live providers and are not required for the
minimum setup check. See the [setup audit](docs/setup-audit.md) for known limits.

### Please, follow these steps to contribute:
1. Reverse a website from this list: [sites-to-reverse](https://github.com/xtekky/gpt4free/issues/40)
2. Add it to [./etc/testing](etc/testing)
3. Refactor it and add it to [./g4f](https://github.com/xtekky/gpt4free/tree/main/g4f)

### We will be grateful to see you as a contributor!