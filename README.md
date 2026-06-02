# Py-PapagoTrans

Py-PapagoTrans is a **free** and **unlimited** Python library that provides a small, no-configuration interface for Naver Papago translation.

This project is inspired by `googletrans` and aims to provide a similar developer experience for Papago translation without requiring Naver Developer API keys for simple use cases.

Compatible with Python 3.8+.

## (TODO) Features

- Fast and simple translation interface
- Auto language detection, when supported by the upstream service
- Bulk translations
- Async support
- HTTP/2 support through `httpx`
- Complete type hints
- No API key or configuration required for basic usage

## (TODO) HTTP/2 support

This library uses `httpx` for HTTP requests, so HTTP/2 can be supported when the runtime environment and upstream endpoint support it.

You can check whether HTTP/2 is enabled and working through the internal response object of a translated or detected result, when available:

```python
>>> translator.translate("테스트")._response.http_version
# "HTTP/2"
```

## (TODO) How must this library work

We may wonder why the original `googletrans` library works without a Google Cloud API key, while translation services normally require one.

The developer of `googletrans`, Mr. Suhun Han, mentions that other approaches such as `goslate` no longer work, since Google recently updated its translation service with a ticket mechanism to block crawler programs. But he also mentions that he eventually figured out a way to generate a ticket by reverse-engineering the obfuscated and minified code used by Google to generate such a token, and implemented it on top of Python. He warned that this could be blocked at any time.

We must attempt to follow the same spirit for Papago translation to the extent technically feasible, provided it remains legally permissible. We need to use publicly reachable, browser-observable translation request flows rather than the official paid or key-based APIs.

## (TODO) Installation

<!-- 
Initialize this repository to start developing

```
git remote add community git@github.com:ssut/py-googletrans.git
git fetch community main
git merge --allow-unrelated-histories --no-commit --no-ff community/main
git checkout --ours LICENSE
git checkout --theirs .gitignore
git rm CLAUDE.md CODEX.md GEMINI.md
vi AGENTS.md
git rm -f README.rst
vi README.md
git add LICENSE .gitignore AGENTS.md README.md
git submodule add https://github.com/stunstunstun/papago.git references/stunstunstun-papago
git submodule add https://github.com/todayears/papagotrans.git references/todayears-papagotrans
git submodule add https://github.com/Beomi/pypapago.git references/Beomi-pypapago
git submodule add https://github.com/krtk-dev/translators.git references/krtk-dev-translators
git submodule update --init --recursive
GIT_COMMITTER_DATE="2026-06-03T06:00:00" git commit -m "Merge commit db0567f from community/main" --date="2026-06-03T06:00:00"
```
-->

To install, use `pip` with the package `py-papagotrans`:

```bash
$ pip install py-papagotrans
```

Or download the package and put the `papagotrans` directory into your Python path.

## (TODO) Basic Usage

If the source language is not given, Py-PapagoTrans attempts to detect the source language when supported.

```python
>>> import asyncio
>>> from papagotrans import Translator
>>>
>>> async def translate_text():
...     async with Translator() as translator:
...         result = await translator.translate("안녕하세요.")
...         print(result)  # <Translated src=ko dest=en text=Hello. pronunciation=Hello.>
...
...         result = await translator.translate("안녕하세요.", dest="ja")
...         print(result)  # <Translated src=ko dest=ja text=こんにちは。 pronunciation=Kon'nichiwa.>
...
...         result = await translator.translate("veritas lux mea", src="la")
...         print(result)  # <Translated src=la dest=en text=The truth is my light pronunciation=The truth is my light>
...
>>> asyncio.run(translate_text())
```

## (TODO) Advanced Usage: Bulk Translation

An array can be used to translate a batch of strings in a single method call and a single HTTP session. The exact same method shown above works for arrays as well.

```python
>>> async def translate_bulk():
...     async with Translator() as translator:
...         translations = await translator.translate(["The quick brown fox", "jumps over", "the lazy dog"], dest="ko")
...         for translation in translations:
...             print(translation.origin, " -> ", translation.text)
...             # The quick brown fox  ->  빠른 갈색 여우
...             # jumps over  ->  뛰어넘다
...             # the lazy dog  ->  게으른 개
...
>>> asyncio.run(translate_bulk())
```

## (TODO) Language detection

The `detect` method identifies the language used in a given sentence, when supported.

```python
>>> async def detect_languages():
...     async with Translator() as translator:
...         result = await translator.detect("이 문장은 한글로 쓰여졌습니다.")
...         print(result)  # <Detected lang=ko confidence=0.27>
...
...         result = await translator.detect("この文章は日本語で書かれました。")
...         print(result)  # <Detected lang=ja confidence=0.64>
...
...         result = await translator.detect("This sentence is written in English.")
...         print(result)  # <Detected lang=en confidence=0.22>
...
>>> asyncio.run(detect_languages())
```

## (TODO) Py-PapagoTrans as a command line application

```bash
$ papagotrans -h
usage: papagotrans [-h] [-d DEST] [-s SRC] [-c] text

Python Papago-style translator as a command-line tool

positional arguments:
  text                  The text you want to translate.

optional arguments:
  -h, --help            show this help message and exit
  -d DEST, --dest DEST  The destination language you want to translate.
                        (Default: en)
  -s SRC, --src SRC     The source language you want to translate.
                        (Default: auto)
  -c, --detect          Detect the language of the given text.

$ papagotrans "veritas lux mea" -s la -d en
[la] veritas lux mea
    ->
[en] The truth is my light
[pron.] The truth is my light

$ papagotrans -c "안녕하세요."
[ko, 1] 안녕하세요.
```

## Note on library usage

DISCLAIMER: this is an unofficial library and is not associated with Naver, Papago, or Naver Cloud.

- This library does not use the official Naver Cloud Papago API by default.
- The maximum character limit for a single text depends on the upstream web service and may change at any time.
- Due to limitations of unofficial web-based translation flows, this library does not guarantee stable behavior at all times.
- Use this library only when occasional instability is acceptable.
- **For production, business, or high-volume use, use the official Naver Cloud Papago API.**
- If you receive HTTP 4xx or 5xx errors, the upstream service may have changed, rejected the request, or restricted your client IP address.
