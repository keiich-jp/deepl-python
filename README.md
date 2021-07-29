# DeepL Python Library

<a href="https://github.com/DeepLcom/deepl-python/blob/main/LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blueviolet.svg"></a>
<a href="https://github.com/DeepLcom/deepl-python"><img alt="Code style: black" src="https://img.shields.io/badge/code%20style-black-000000.svg"></a>
<!-- TODO: CI test & coverage result, documentation build, PyPI version -->

The [DeepL API](https://www.deepl.com/docs-api?utm_source=github&utm_medium=github-python-readme) is a language translation API that allows other computer programs to send texts and documents to DeepL's servers and receive high-quality translations. This opens a whole universe of opportunities for developers: any translation product you can imagine can now be built on top of DeepL's best-in-class translation technology.

The DeepL Python library offers a convenient way for applications written in Python to interact with the DeepL API. All functions of the DeepL API are supported.

## Getting an authentication key 

To use the DeepL Python Library, you'll need an API authentication key. To get a key, [please create an account here](https://www.deepl.com/pro?utm_source=github&utm_medium=github-python-readme#developer). You can translate up to 500,000 characters/month for free. 

## Installation
To install dependencies with a source checkout, use poetry:
```shell
poetry install
```

Note: later this package will be on PyPI, and then `pip install <name to be confirmed>` will be the preferred installation method.

### Requirements
The library is tested with Python versions 3.6 to 3.9. 

The `requests` module is used to perform the HTTP requests.

## Usage

```python
import deepl

# Create a Translator object providing your DeepL API authentication key
translator = deepl.Translator("YOUR_AUTH_KEY")

# Translate text into a target language, in this case, French
result = translator.translate_text("Hello, world!", target_lang="FR")
print(result)  # "Bonjour, le monde !"
# Note: printing or converting the result to a string uses the output text

# Translate multiple texts into British English
result = translator.translate_text(["お元気ですか？", "¿Cómo estás?"], target_lang="EN-GB")
print(result[0].text)  # "How are you?"
print(result[0].detected_source_lang)  # "JA"
print(result[1].text)  # "How are you?"
print(result[1].detected_source_lang)  # "ES"

# Translating documents
translator.translate_document_from_filepath(
    "Instruction Manual.docx",
    "Bedienungsanleitlung.docx",
    target_lang="DE",
    formality="more"
)

# Check account usage
usage = translator.get_usage()
if usage.character.limit_exceeded:
    print("Character limit exceeded.")

# Source and target languages
for language in translator.get_source_languages():
    print(f"{language.code} ({language.name})")  # Example: "DE (German)"

num_languages = sum([language.supports_formality
                     for language in translator.get_target_languages()])
print(f"{num_languages} target languages support formality parameter")
```
### Logging
Logging can be enabled to see the HTTP-requests sent and responses received by the library. Enable and control logging
using Python's logging module, for example:
```python
import logging
logging.basicConfig()
logging.getLogger('deepl').setLevel(logging.DEBUG)
```

### Exceptions
All module functions may raise `deepl.DeepLException` or one of its subclasses.
If invalid arguments are provided, they may raise the standard exceptions `ValueError` and `TypeError`. 

## Command Line Interface
The library can be run on the command line supporting all API functions. Use the `--help` option for 
usage information:
```shell
python3 -m deepl --help
```
The CLI requires your DeepL authentication key specified either as the `DEEPL_AUTH_KEY` environment variable, or using
the `--auth-key` option, for example:
```shell
python3 -m deepl --auth-key=YOUR_AUTH_KEY usage
```
Note that the `--auth-key` argument must appear *before* the command argument. The recognized commands are:

| Command   | Description                                            |
| :-------- | :----------------------------------------------------- |
| text      | translate text(s)                                      |
| document  | translate document(s)                                  |
| usage     | print usage information for the current billing period |
| languages | print available languages                              |

For example, to translate text:
```shell
python3 -m deepl --auth-key=YOUR_AUTH_KEY text --to=DE "Text to be translated."
```
Wrap text arguments in quotes to prevent the shell from splitting sentences into words.

## Development
The test suite depends on [deepl-mockserver](tests/mockserver). Run it in another terminal while executing the tests, using port 3000. Set the mock-server listening port using the environment variable `DEEPL_MOCK_SERVER_PORT`. 

Execute the tests using `tox`.

### Issues
If you experience problems using the library, or would like to request a new feature, please open an [issue](/issues). 
