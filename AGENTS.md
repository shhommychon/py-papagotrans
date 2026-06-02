# AGENTS.md

## Project

Build `py-papagotrans`, a lightweight Python translation library inspired by `googletrans`.

Goal: provide a simple, no-configuration, no-API-key interface for Papago-style translation when technically and legally feasible, while keeping the project small, maintainable, and respectful of service limits.

## Core Principles

* Keep the public API close to `googletrans` where practical.
  * Follow the target API and package structure of `googletrans`, which is currently forked directly into this repository.
* No API keys, config files, accounts, browser automation, or paid-service assumptions.
* Do not implement auth bypasses, CAPTCHA bypasses, fingerprint evasion, proxy rotation, or aggressive scraping.
* Use only publicly reachable endpoints or browser-observable request flows that do not require login.
* Add conservative rate limiting, retries, timeouts, and clear error messages.
* Prefer clean, typed, testable Python over clever hacks.
* Keep dependencies minimal.



## Package Structure

Use a clean layout:

```text
py_papagotrans/
  __init__.py
  client.py
  models.py
  constants.py
  exceptions.py
  utils.py
tests/
README.md
pyproject.toml
```

## Implementation Guidelines

* `Translator` should be the main user-facing class.
* Use `httpx` unless there is a strong reason not to.
* Define result objects with dataclasses or lightweight typed classes.
* Normalize language codes.
* Validate inputs early.
* Expose meaningful exceptions:
  * `PapagoTransError`
  * `NetworkError`
  * `RateLimitError`
  * `ParseError`
  * `UnsupportedLanguageError`
* Never hardcode fragile magic without comments.
* If an upstream response format changes, fail clearly instead of returning wrong translations.
* Include unit tests for parsing, language normalization, batching, and error handling.
* Network tests should be optional and skipped by default.

### Related Projects

The following repositories are useful references when reviewing prior Papago-related open-source work and existing package-name usage on PyPI. They are listed for historical and implementation research purposes only; Py-PapagoTrans does not inherit maintenance status, behavior, or API compatibility from these projects.

These repositories are included in the current repository as Git submodules under a reference directory so their source code can be reviewed locally without treating them as runtime dependencies.

* `stunstunstun/papago`
  * Last commit: Jan 2, 2018, 6:32 AM GMT+9
  * Python Papago translation library.
  * Uses the `papago` package name on PyPI (`pip install papago`).
* `todayears/papagotrans`
  * Last commit: Jul 11, 2017, 4:44 PM GMT+9
  * Python Papago translation library and fork of `stunstunstun/papago`.
  * Uses the `papagotrans` package name on PyPI (`pip install papagotrans`).
* `Beomi/pypapago`
  * Status: public archive
  * Last commit: Sep 3, 2020, 6:10 PM GMT+9
  * Python Papago translation library.
  * Uses the `pypapago` package name on PyPI (`pip install pypapago`).
* `krtk-dev/translators`
  * Last commit: May 4, 2024, 10:31 PM GMT+9
  * React-based comparison page for Google, Papago, and Kakao translation results.
  * Useful as a reference for studying how Papago translation requests are obtained and compared in a browser-based application.
* `Klypse/PentaGo`
  * Last commit: Jul 4, 2025, 11:17 PM GMT+9
  * Unofficial Python Papago API/library using reverse-engineered web endpoints.
  * Uses the `pentago` package name on PyPI (`pip install pentago`).
  * More recent than the other Papago Python package references, and useful for studying current async request handling, language support, pronunciation fields, honorific handling, and dynamic request-header/key regeneration behavior.

## Safety and Service Respect

* Add default request delay / throttling.
* Add a clear User-Agent identifying this open-source library.
* Do not add code intended to hide automation.
* Do not flood endpoints.
* Do not collect or log user translation text unless explicitly needed for debugging, and keep debug logging opt-in.
* Document that unofficial endpoints may break or be restricted.

## Development Style

* Small commits.
* Type hints everywhere reasonable.
* Format with `ruff` or `black`.
* Keep public API stable.
* Avoid large abstractions until needed.
* Prefer simple readable code over premature extensibility.

### Do Not Do

* Do not copy old abandoned Papago packages blindly.
* Do not require Naver developer keys.
* Do not implement login-based flows.
* Do not add proxy rotation.
* Do not bypass CAPTCHA, anti-bot systems, or access controls.
* Do not make claims that the library is officially supported by Naver.

### String Quote Rules

The user does not write code directly and has a strict preference for C-style string quote conventions. Follow these rules consistently in all generated code:

* Single quotes (`'`) are used only for:

  * Empty strings, for example `''`
  * Single-character strings, for example `'c'`

* Double quotes (`"`) are used for:

  * All ordinary strings with length 2 or more, for example `"hello"`
  * Module names, file paths, messages, dictionary keys, and string constants unless they are empty or single-character strings

This rule must be followed strictly so the user does not have to spend time manually correcting quote style.

## Success Criteria

A user should be able to install the package, create a `Translator`, translate short text, detect language when supported, receive typed results, and get clear errors when the upstream behavior changes.
