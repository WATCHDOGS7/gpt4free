### G4F - Additional Requirements

#### Introduction

You can install requirements partially or completely. So G4F can be used as you wish. You have the following options for this:

#### Source checkout or published release?

For development, follow the [source setup and test guide](git.md). Install extras
from the checkout, for example:

```sh
python -m pip install -e '.[gui,api]'
```

The commands below install the **published PyPI package**, not the cloned code.
Run them in an activated virtual environment. Quote extras so shells such as zsh
do not interpret the square brackets as filename patterns.

#### Options

Install g4f with the dependencies in the `all` extra (local inference uses the separate `local` extra):
```
python -m pip install -U 'g4f[all]'
```
Or install only g4f and the required packages for the OpenaiChat provider:
```
python -m pip install -U 'g4f[openai]'
```
Install required packages for the Interference API:
```
python -m pip install -U 'g4f[api]'
```
Install required packages for the Web UI:
```
python -m pip install -U 'g4f[gui]'
```
Install required packages for uploading / generating images:
```
python -m pip install -U 'g4f[image]'
```
Install required packages for using the webdriver:
```
python -m pip install -U 'g4f[webdriver]'
```
Install required package for proxy support with aiohttp:
```
python -m pip install -U aiohttp_socks
```
Install required package for loading cookies from browser:
```
python -m pip install browser_cookie3
```
Install curl_cffi for better protection from being blocked:
```
python -m pip install curl_cffi
```
Install all packages and uninstall this package for disabling the webdriver:
```
python -m pip uninstall undetected-chromedriver
```

[Return to README](../README.md)