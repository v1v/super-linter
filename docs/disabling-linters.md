# Disabling linters and Rules
Linters can often require additional configuration to ensure they work with your codebase and your team's coding style, to avoid flagging false-positives. The **GitHub Super-Linter** has set up some default configurations for each linter which should work reasonably well with common code bases, but many of the linters can be configured to disable certain rules or configure the rules to ignore certain pieces of codes.

To run with your own configuration for a linter, copy the relevant [`TEMPLATE` configuration file for the linter you are using from this repo](https://github.com/github/super-linter/tree/master/TEMPLATES) into the `.github/linters` folder in your own repository, and then edit it to modify, disable - or even add - rules and configuration to suit how you want your code checked.

How the changes are made differ for each linter, and also how much the **Github Super-Linter** has decided to change the linter's defaults. So, for some linters (e.g. [pylint for python](https://github.com/github/super-linter/blob/master/TEMPLATES/.python-lint)), there may be a large configuration file. For others (e.g. [stylelint for CSS](https://github.com/github/super-linter/blob/master/TEMPLATES/.stylelintrc.json)) the default configuration file may initially be nearly empty. And for some (e.g. StandardJS) it may not be possible to change configuration at all so there is no Template file.

Where a configuration file exists in your repo, it will be used in preference to the default one in the **GitHub Super-Linter** `TEMPLATES` directory (not in addition to it), and where one doesn't exist the `TEMPLATES` version will be used. So you should copy the complete configuration file you require to change from the `TEMPLATES` directory and not just the lines of config you want to change.

It is possible to have custom configurations for some linters, and continue to use the default from `TEMPLATES` directory for others, so if you use `Python` and `JavaScript` and only need to tweak the `Python` rules, then you only need to have a custom configuration for *pylint* and continue to use the default `TEMPLATE` from the main repo for *ESLint*, for example.

For some linters it is also possible to override rules on a case by case level with directives in your code. Where this is possible we try to note how to do this in the specific linter sections below, but the official linter documentation will likely give more detail on this.

## Table of Linters
- [Ruby](#ruby)
- [Shell](#shell)
- [Ansible](#ansible)
- [YAML](#yaml)
- [AWS CloudFormation templates](#cfn)
- [Python](#python3)
- [JSON](#json)
- [Markdown](#markdown)
- [Perl](#perl)
- [Raku](#raku)
- [PHP](#php)
- [XML](#xml)
- [Coffeescript](#coffeescript)
- [Javascript Eslint](#javascript-eslint)
- [Javascript Standard](#javascript-standard)
- [Typescript Eslint](#typescript-eslint)
- [Typescript Standard](#typescript-standard)
- [Golang](#golang)
- [Dockerfile](#dockerfile)
- [Terraform](#terraform)
- [CSS](#css)
- [ENV](#dotenv-linter)
- [Kotlin](#kotlin)
- [OpenAPI](#openapi)
- [Protocol Buffers](#protocol-buffers)
- [EDITORCONFIG-CHECKER](#editorconfig-checker)
- [HTML](#html)

<!-- toc -->

--------------------------------------------------------------------------------

## Ruby
- [RuboCop](https://github.com/rubocop-hq/rubocop)

### RuboCop Config file
- `.github/linters/.ruby-lint.yml`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.ruby-lint.yml`
- **Note:** We use the Default **GitHub** Rule set from [RuboCop-GitHub](https://github.com/github/rubocop-github)

### RuboCop disable single line
```ruby
method(argument) # rubocop:disable SomeRule, SomeOtherRule
```

### RuboCop disable code block
```ruby
# rubocop:disable
This is a long line
var="this is some other stuff"
# rubocop:enable
```

### RuboCop disable entire file
If you need to ignore an entire file, you can update the `.github/linters/.ruby-lint.yml` to ignore certain files and locations

```yml
inherit_from:
  - .rubocop_todo.yml
  - .rubocop_app_overrides.yml

inherit_mode:
  merge:
    - Exclude

Rails:
  Enabled: true

AllCops:
  TargetRubyVersion: 2.5.1
  EnabledByDefault: true
  Exclude:
    - 'db/**/*'
    - 'config/**/*'
    - 'script/**/*'
    - 'bin/{rails,rake}'
    - !ruby/regexp /old_and_unused\.rb$/
```

--------------------------------------------------------------------------------

## Shell
- [Shellcheck](https://github.com/koalaman/shellcheck)

### Shellcheck Config file
- There is no top level *configuration file* available at this time

### Shellcheck disable single line
```bash
echo "Terrible stuff" # shellcheck disable=SC2059,SC2086
```

### Shellcheck disable code block
```bash
# shellcheck disable=SC2059,SC2086
echo "some hot garbage"
echo "More garbage code"
```

### Shellcheck disable entire file
- **Note:** The disable must be on the second line of the code right after the shebang
```bash
#!/bin/sh
# shellcheck disable=SC2059,SC1084

echo "stuff"
moreThings()
```

--------------------------------------------------------------------------------

## Ansible
- [ansible-lint](https://github.com/ansible/ansible-lint)

### Ansible-lint Config file
- `.github/linters/.ansible-lint.yml`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.ansible-lint.yml`

### Ansible-lint disable single line
```yml
- name: this would typically fire GitHasVersionRule 401 and BecomeUserWithoutBecomeRule 501
  become_user: alice  # noqa 401 501
  git: src=/path/to/git/repo dest=checkout
```
### Ansible-lint disable code block
```yml
- name: this would typically fire GitHasVersionRule 401
  git: src=/path/to/git/repo dest=checkout
  tags:
  - skip_ansible_lint
```

### Ansible-lint disable entire file
```yml
- name: this would typically fire GitHasVersionRule 401
  git: src=/path/to/git/repo dest=checkout
  tags:
  - skip_ansible_lint
```
--------------------------------------------------------------------------------

## YAML
- [YamlLint](https://github.com/adrienverge/yamllint)

### Yamllint Config file
- `.github/linters/.yaml-lint.yml`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.yaml-lint.yml`

### Yamllint disable single line
```yml
This line is waaaaaaaaaay too long  # yamllint disable-line
```

### Yamllint disable code block
```yml
# yamllint disable rule:colons
- Key         : value
  dolor       : sit,
  foo         : bar
# yamllint enable
```

### Yamllint disable entire file
If you need to ignore an entire file, you can update the `.github/linters/.yaml-lint.yml` to ignore certain files and locations
```yml
# For all rules
ignore: |
  *.dont-lint-me.yaml
  /bin/
  !/bin/*.lint-me-anyway.yaml

rules:
  key-duplicates:
    ignore: |
      generated
      *.template.yaml
  trailing-spaces:
    ignore: |
      *.ignore-trailing-spaces.yaml
      /ascii-art/*
```

--------------------------------------------------------------------------------

## Python3
- [pylint](https://www.pylint.org/)

### Pylint Config file
- `.github/linters/.python-lint`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.python-lint`

### Pylint disable single line
```python
global VAR # pylint: disable=global-statement
```

### Pylint disable code block
```python
"""pylint option block-disable"""

__revision__ = None

class Foo(object):
    """block-disable test"""

    def __init__(self):
        pass

    def meth1(self, arg):
        """this issues a message"""
        print(self)

    def meth2(self, arg):
        """and this one not"""
        # pylint: disable=unused-argument
        print(self\
              + "foo")

    def meth3(self):
        """test one line disabling"""
        # no error
        print(self.baz) # pylint: disable=no-member
        # error
        print(self.baz)
```

### Pylint disable entire file
```python
#!/bin/python3
# pylint: skip-file

var = "terrible code down here..."
```

--------------------------------------------------------------------------------

## AWS CloudFormation templates
- [cfn-lint](https://github.com/aws-cloudformation/cfn-python-lint/)

### cfn-lint Config file
- `.github/linters/.cfnlintrc.yml`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.cfnlintrc.yml`

### cfn-lint disable single line
- There is currently **No** way to disable rules inline of the file(s)

### cfn-lint disable code block
You can disable both [template](https://github.com/aws-cloudformation/cfn-python-lint/#template-based-metadata) or [resource](https://github.com/aws-cloudformation/cfn-python-lint/#resource-based-metadata) via [metadata](https://github.com/aws-cloudformation/cfn-python-lint/#metadata):
```yaml
Resources:
  myInstance:
    Type: AWS::EC2::Instance
    Metadata:
      cfn-lint:
        config:
          ignore_checks:
          - E3030
    Properties:
      InstanceType: nt.x4superlarge
      ImageId: ami-abc1234
```

### cfn-lint disable entire file
If you need to ignore an entire file, you can update the `.github/linters/.cfnlintrc.yml` to ignore certain files and locations
```yaml
ignore_templates:
- codebuild.yaml
```

--------------------------------------------------------------------------------

## JSON
- [jsonlint](https://github.com/zaach/jsonlint)

### JsonLint Config file
- There is no top level *configuration file* available at this time

### JsonLint disable single line
- There is currently **No** way to disable rules inline of the file(s)

### JsonLint disable code block
- There is currently **No** way to disable rules inline of the file(s)

### JsonLint disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## Markdown
- [markdownlint-cli](https://github.com/igorshubovych/markdownlint-cli#readme)
- [markdownlint rule documentation](https://github.com/DavidAnson/markdownlint/blob/main/doc/Rules.md)
- [markdownlint inline comment syntax](https://github.com/DavidAnson/markdownlint#configuration)

### markdownlint Config file
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.markdown-lint.yml`

### markdownlint disable single line
```markdown
## Here is some document
Here is some random data
<!-- markdownlint-disable -->
any violation you want
<!-- markdownlint-restore -->
Here is more data
```
### markdownlint disable code block
```markdown
## Here is some document
Here is some random data
<!-- markdownlint-disable -->
any violations you want
<!-- markdownlint-restore -->
Here is more data
```

### markdownlint disable entire file
- You can encapsulate the entire file with the *code block format* to disable an entire file from being parsed

--------------------------------------------------------------------------------

## Perl
- [perl](https://pkgs.alpinelinux.org/package/edge/main/x86/perl)

### Perl Config file
- There is no top level *configuration file* available at this time

### Perl disable single line
- There is currently **No** way to disable rules inline of the file(s)

### Perl disable code block
- There is currently **No** way to disable rules inline of the file(s)

### Perl disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## Raku
- [raku](https://raku.org)

### Raku Config file
- There is no top level *configuration file* available at this time

### Raku disable single line
- There is currently **No** way to disable rules inline of the file(s)

### Raku disable code block
- There is currently **No** way to disable rules inline of the file(s)

### Raku disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------
--------------------------------------------------------------------------------

## PHP
- [PHP](https://www.php.net/)

### PHP Config file
- There is no top level *configuration file* available at this time

### PHP disable single line
- There is currently **No** way to disable rules inline of the file(s)

### PHP disable code block
- There is currently **No** way to disable rules inline of the file(s)

### PHP disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## XML
- [XML](http://xmlsoft.org/)

### LibXML Config file
- There is no top level *configuration file* available at this time

### LibXML disable single line
- There is currently **No** way to disable rules inline of the file(s)

### LibXML disable code block
- There is currently **No** way to disable rules inline of the file(s)

### LibXML disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## Coffeescript
- [coffeelint](https://coffeelint.github.io/)

### coffeelint Config file
- `.github/linters/.coffee-lint.yml`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.coffee.yml`

### coffeelint disable single line
```Coffeescript
# coffeelint: disable=max_line_length
foo = "some/huge/line/string/with/embed/#{values}.that/surpasses/the/max/column/width"
# coffeelint: enable=max_line_length
```

### coffeelint disable code block
```Coffeescript
# coffeelint: disable
foo = "some/huge/line/string/with/embed/#{values}.that/surpasses/the/max/column/width"
bar = "some/huge/line/string/with/embed/#{values}.that/surpasses/the/max/column/width"
baz = "some/huge/line/string/with/embed/#{values}.that/surpasses/the/max/column/width"
taz = "some/huge/line/string/with/embed/#{values}.that/surpasses/the/max/column/width"
# coffeelint: enable
```

### coffeelint disable entire file
- You can encapsulate the entire file with the *code block format* to disable an entire file from being parsed

--------------------------------------------------------------------------------

## Javascript eslint
- [eslint](https://eslint.org/)

### Javascript eslint Config file
- `.github/linters/.eslintrc.yml`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.eslintrc.yml`

### Javascript eslint disable single line
```javascript
var thing = new Thing(); // eslint-disable-line no-use-before-define
thing.sayHello();

function Thing() {

     this.sayHello = function() { console.log("hello"); };

}
```

### Javascript eslint disable code block
```javascript
/*eslint-disable */

//suppress all warnings between comments
alert('foo')

/*eslint-enable */
```
### Javascript eslint disable entire file
- Place at the top of the file:
```javascript
/* eslint-disable */
```

--------------------------------------------------------------------------------

## Javascript standard
- [standard js](https://standardjs.com/)

### Javascript standard Config file
- There is no top level *configuration file* available at this time

### Javascript standard disable single line
- There is currently **No** way to disable rules inline of the file(s)

### Javascript standard disable code block
- There is currently **No** way to disable rules inline of the file(s)

### Javascript standard disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## Typescript eslint
- [eslint](https://eslint.org/)

### Typescript eslint Config file
- `.github/linters/.eslintrc.yml`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.eslintrc.yml`

### Typescript eslint disable single line
```typescript
var thing = new Thing(); // eslint-disable-line no-use-before-define
thing.sayHello();

function Thing() {

     this.sayHello = function() { console.log("hello"); };

}
```

### Typescript eslint disable code block
```typescript
/*eslint-disable */

//suppress all warnings between comments
alert('foo')

/*eslint-enable */
```
### Typescript eslint disable entire file
```typescript
/* eslint-disable */
```

--------------------------------------------------------------------------------

## Typescript standard
- [standardjs](https://standardjs.com/)

### Typescript standard Config file
- There is no top level *configuration file* available at this time

### Typescript standard disable single line
- There is currently **No** way to disable rules inline of the file(s)

### Typescript standard disable code block
- There is currently **No** way to disable rules inline of the file(s)

### Typescript standard disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## Golang
- [golangci-lint](https://github.com/golangci/golangci-lint)

### golangci-lint standard Config file
- `.github/linters/.golangci.yml`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.golangci.yml`

### golangci-lint disable single line
- There is currently **No** way to disable rules inline of the file(s)

### golangci-lint disable code block
- There is currently **No** way to disable rules inline of the file(s)

### golangci-lint disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## Dockerfile
- [dockerfilelint](https://github.com/replicatedhq/dockerfilelint.git)

### Dockerfilelint standard Config file
- `.github/linters/.dockerfilelintrc`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.dockerfilelintrc`

### Dockerfilelint disable single line
- There is currently **No** way to disable rules inline of the file(s)

### Dockerfilelint disable code block
- There is currently **No** way to disable rules inline of the file(s)

### Dockerfilelint disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## Terraform
- [tflint](https://github.com/terraform-linters/tflint)

### tflint standard Config file
- `.github/linters/.tflint.hcl`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.tflint.hcl`

### tflint disable single line
- There is currently **No** way to disable rules inline of the file(s)

### tflint disable code block
- There is currently **No** way to disable rules inline of the file(s)

### tflint disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## CSS
- [stylelint](https://stylelint.io/)

### stylelint standard Config file
- `.github/linters/.stylelintrc.json`

### stylelint disable single line
```css
#id {
  /* stylelint-disable-next-line declaration-no-important */
  color: pink !important;
}
```

### stylelint disable code block
```css
/* stylelint-disable */
a {}
/* stylelint-enable */
```

### stylelint disable entire file
- You can disable entire files with the `ignoreFiles` property in `.stylelintrc.json`
```json
{
  "ignoreFiles": [
    "styles/ignored/wildcards/*.css",
    "styles/ignored/specific-file.css"
   ]
}
```

--------------------------------------------------------------------------------

## ENV
- [dotenv-linter](https://github.com/dotenv-linter/dotenv-linter)

### dotenv-linter Config file
- There is no top level *configuration file* available at this time

### dotenv-linter disable single line
```env
# Comment line will be ignored
```

### dotenv-linter disable code block
- There is currently **No** way to disable rules inline of the file(s)

### dotenv-linter disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## Kotlin
- [ktlint](https://github.com/pinterest/ktlint)

### ktlint Config file
- There is no top level *configuration file* available at this time

### ktlint disable single line
```kotlin
import package.* // ktlint-disable no-wildcard-imports
```

### ktlint disable code block
```kotlin
/* ktlint-disable no-wildcard-imports */
import package.a.*
import package.b.*
/* ktlint-enable no-wildcard-imports */
```

### ktlint disable entire file
- There is currently **No** way to disable rules inline of the file(s)

--------------------------------------------------------------------------------

## Dart
- [dartanalyzer](https://dart.dev/tools/dartanalyzer)

### dartanalyzer standard Config file
- `.github/linters/.dart-lint.yml`
- You can pass multiple rules and overwrite default rules
- File should be located at: `.github/linters/.dart-lint.yml`

### dartanalyzer disable single line
```dart
int x = ''; // ignore: invalid_assignment
```

### dartanalyzer disable code block
- You can make [rule exceptions](https://dart.dev/guides/language/analysis-options#excluding-code-from-analysis) for the entire file.
```dart
// ignore_for_file: unused_import, unused_local_variable
```

### dartanalyzer disable entire file
- You can disable entire files with the `analyzer.exclude` property in `.dart-lint.yml`
```dart
analyzer:
  exclude:
    - file
```

--------------------------------------------------------------------------------

## OpenAPI
- [spectral](https://github.com/stoplightio/spectral)

### OpenAPI Config file
- `.github/linters/.openapirc.yml`
- You can add, extend, and disable rules
- Documentation at [Spectral Custom Rulesets](https://stoplight.io/p/docs/gh/stoplightio/spectral/docs/guides/4-custom-rulesets.md)
- File should be located at: `.github/linters/.openapirc.yml`

### OpenAPI disable single line
- There is currently **No** way to disable rules inline of the file(s)

### OpenAPI disable code block
- There is currently **No** way to disable rules inline of the file(s)

### OpenAPI disable entire file
- There is currently **No** way to disable rules inline of the file(s)
- However, you can make [rule exceptions](https://stoplight.io/p/docs/gh/stoplightio/spectral/docs/guides/6-exceptions.md?srn=gh/stoplightio/spectral/docs/guides/6-exceptions.md) in the config for individual file(s).

--------------------------------------------------------------------------------

## Protocol Buffers

- [protolint](https://github.com/yoheimuta/protolint)

### protolint Config file

- `.github/linters/.protolintrc.yml`
- You can add, extend, and disable rules
- Documentation at [Rules](https://github.com/yoheimuta/protolint#rules) and [Configuring](https://github.com/yoheimuta/protolint#configuring)

### protolint disable single line

```protobuf
enum Foo {
  // protolint:disable:next ENUM_FIELD_NAMES_UPPER_SNAKE_CASE
  firstValue = 0;
  second_value = 1;  // protolint:disable:this ENUM_FIELD_NAMES_UPPER_SNAKE_CASE
  THIRD_VALUE = 2;
}
```

### protolint disable code block

```protobuf
// protolint:disable ENUM_FIELD_NAMES_UPPER_SNAKE_CASE
enum Foo {
  firstValue = 0;
  second_value = 1;
  THIRD_VALUE = 2;
}
// protolint:enable ENUM_FIELD_NAMES_UPPER_SNAKE_CASE
```

### protolint disable entire file

- You can disable entire files with the `lint.files.exclude` property in `.protolintrc.yml`

```yaml
# Lint directives.
lint:
  # Linter files to walk.
  files:
    # The specific files to exclude.
    exclude:
      - path/to/file
```

## Clojure
- [clj-kondo](https://github.com/borkdude/clj-kondo)
- Since clj-kondo approaches static analysis in a very Clojure way, it is advised to read the [configuration docs](https://github.com/borkdude/clj-kondo/blob/master/doc/config.md)

### clj-kondo standard Config file
- `.github/linters/.clj-kondo/config.edn`

### clj-kondo disable single line
- There is currently **No** way to disable rules in a single line

### clj-kondo disable code block
- There is currently **No** way to disable rules in a code block

### clj-kondo disable entire file
```clojure
{:output {:exclude-files ["path/to/file"]}}

## EDITORCONFIG-CHECKER
- [editorconfig-checker](https://github.com/editorconfig-checker/editorconfig-checker)

--------------------------------------------------------------------------------

### editorconfig-checker Config file
- `.github/linters/.ecrc`
- This linter will also use the [`.editorconfig`](https://editorconfig.org/) of your project

### editorconfig-checker disable single line
-
```js
<LINE> // editorconfig-checker-disable-line
```

### editorconfig-checker disable code block
- There is currently **No** way to disable rules inline of the file(s)

### editorconfig-checker disable entire file
-
```js
// editorconfig-checker-disable-file
```
- You can disable entire files with the `Exclude` property in `.ecrc`
```json
{
  "Exclude": [
    "path/to/file",
    "^regular\\/expression\\.ext$"
   ]
}
```

## HTML
- [htmlhint](https://htmlhint.com/)

### htmlhint standard Config file
- `.github/linters/.htmlhintrc`

### htmlhint disable single line
- There is currently **No** way to disable rules in a single line

### htmlhint disable code block
- There is currently **No** way to disable rules in a code block

### htmlhint disable entire file
- There is currently **No** way to disable rules in an entire file
