# @stackline/remark-message-control

> remark plugin to enable, disable, and ignore messages with remark.

[![npm version](https://img.shields.io/npm/v/@stackline/remark-message-control.svg?style=flat-square)](https://www.npmjs.com/package/@stackline/remark-message-control)
[![license](https://img.shields.io/npm/l/@stackline/remark-message-control.svg?style=flat-square)](https://github.com/alexandroit/stackline-remark-message-control)
[![GitHub repository](https://img.shields.io/badge/GitHub-repository-181717?style=flat-square&logo=github)](https://github.com/alexandroit/stackline-remark-message-control)
[![Docs](https://img.shields.io/badge/docs-alexandro.net-0f766e?style=flat-square)](https://alexandro.net/docs/vanilla/remark-message-control/)
[![Reddit community](https://img.shields.io/badge/community-r%2FStackline-ff4500?style=flat-square&logo=reddit&logoColor=white)](https://www.reddit.com/r/Stackline/)

**[Documentation](https://alexandro.net/docs/vanilla/remark-message-control/)** | **[npm](https://www.npmjs.com/package/@stackline/remark-message-control)** | **[Issues](https://github.com/alexandroit/stackline-remark-message-control/issues)** | **[Repository](https://github.com/alexandroit/stackline-remark-message-control)**

**Current package version:** `1.0.2`

---

## Why this package?

`@stackline/remark-message-control` is the Stackline-maintained distribution of `remark-message-control@7.1.1`. It is an independent continuation of [remark-message-control](https://github.com/remarkjs/remark-message-control); original authors and licenses remain credited below.

## Compatibility

| Item | Value |
| :--- | :--- |
| Package | `@stackline/remark-message-control@1.0.2` |
| API target | `remark-message-control@7.1.1` |
| Supported Node.js | `See supported framework requirements` |
| License | `MIT` |
| Module type | `module` |
| Main entry | `index.js` |
| Types | `index.d.ts` |
| Runtime dependencies | `vfile, unified, @types/mdast, mdast-comment-marker, unified-message-control` |

## Installation

```bash
npm install @stackline/remark-message-control
```

Preserve existing imports and plugin resolution with an npm alias:

```bash
npm install remark-message-control@npm:@stackline/remark-message-control
```

## Usage and API reference

### remark-message-control


**[remark][]** plugin to enable, disable, and ignore messages.

## Contents

*   [What is this?](#what-is-this)
*   [When should I use this?](#when-should-i-use-this)
*   [Install](#install)
*   [Use](#use)
*   [API](#api)
    *   [`unified().use(remarkMessageControl, options)`](#unifieduseremarkmessagecontrol-options)
*   [Syntax](#syntax)
*   [Types](#types)
*   [Compatibility](#compatibility)
*   [Security](#security)
*   [Related](#related)
*   [Contribute](#contribute)
*   [License](#license)

## What is this?

This package is a [unified][] ([remark][]) plugin that lets authors write
comments in markdown to show and hide messages.

**unified** is a project that transforms content with abstract syntax trees
(ASTs).
**remark** adds support for markdown to unified.
**mdast** is the markdown AST that remark uses.
remark plugins can inspect the tree and emit warnings and other messages.
This is a remark plugin that gives authors the ability to configure those
messages from markdown.

## When should I use this?

You can use this package when you’re building a linter such as
[`remark-lint`][remark-lint].
But you probably don’t need to, because `remark-lint` already exists and it uses
this package.

## Install

This package is [ESM only](https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c).
In Node.js (version 12.20+, 14.14+, or 16.0+), install with [npm][]:

```sh
npm install @stackline/remark-message-control
```

In Deno with [Skypack][]:

```js
import remarkMessageControl from 'https://cdn.skypack.dev/remark-message-control@7?dts'
```

In browsers with [Skypack][]:

```html
<script type="module">
  import remarkMessageControl from 'https://cdn.skypack.dev/remark-message-control@7?min'
</script>
```

## Use

Say we have the following file `example.md`:

```markdown


## Heading
```

And our module `example.js` looks as follows:

```js
import {read} from 'to-vfile'
import {reporter} from 'vfile-reporter'
import {remark} from 'remark'
import remarkMessageControl from '@stackline/remark-message-control'

main()

async function main() {
  const file = await remark()
    .use(() => (tree, file) => {
      file.message('Whoops!', tree.children[1], 'foo:thing')
    })
    .use(remarkMessageControl, {name: 'foo'})
    .process(await read('example.md'))

  console.error(reporter(file))
}
```

Now running `node example.js` yields:

```markdown
example.md: no issues found
```

## API

This package exports no identifiers.
The default export is `remarkMessageControl`.

### `unified().use(remarkMessageControl, options)`

Plugin to enable, disable, and ignore messages.

##### `options`

Configuration (required).

###### `options.name`

Name of markers that can control the message sources (`string`).

For example, `{name: 'alpha'}` controls `alpha` markers:

```markdown

```

###### `options.known`

List of allowed `ruleId`s (`Array<string>`, optional).
When given, a warning is shown when someone tries to control an unknown rule.

For example, `{name: 'alpha', known: ['bravo']}` results in a warning if
`charlie` is configured:

```markdown

```

###### `options.reset`

Whether to treat all messages as turned off initially (`boolean`, default:
`false`).

###### `options.enable`

List of `ruleId`s to initially turn on if `reset: true` (`Array<string>`,
optional).
All rules are turned on by default (`reset: false`).

###### `options.disable`

List of `ruleId`s to turn on if `reset: false` (`Array<string>`, optional).

###### `options.sources`

Sources that can be controlled with `name` markers (`string` or
`Array<string>`, default: `options.name`).



<a name="markers"></a>

## Syntax

This plugin looks for comments in markdown (MDX is also supported).
If the first word in those comments does not match `options.name`, the comment
is skipped.
The second word is expected to be `disable`, `enable`, or `ignore`.
Further words are rule identifiers of messages which are configurated.

In EBNF, the grammar looks as follows:

<pre><code class=language-ebnf><a id=s-marker href=#s-marker>marker</a> ::= <a href=#s-identifier>identifier</a> <a href=#s-whitespace>whitespace</a>+ <a href=#s-keyword>keyword</a> <a href=#s-ruleidentifiers>ruleIdentifiers</a>?
<a id=s-identifier href=#s-identifier>identifier</a> ::= <a href=#s-word>word</a>+ /* restriction: must match `options.name` */
<a id=s-keyword href=#s-keyword>keyword</a> ::= 'enable' | 'disable' | 'ignore'
<a id=s-ruleidentifiers href=#s-ruleidentifiers>ruleIdentifiers</a> ::= <a href=#s-word>word</a>+ (<a href=#s-whitespace>whitespace</a>+ <a href=#s-word>word</a>+)*
<a id=s-whitespace href=#s-whitespace>whitespace</a> ::= ' ' | '\t' | '\r' | '\n' | '\f'
<a id=s-word href=#s-word>word</a> ::= <a href=#s-letter>letter</a> | <a href=#s-digit>digit</a> | <a href=#s-punctuation>punctuation</a>
<a id=s-letter href=#s-letter>letter</a> ::= <a href=#s-letterlowercase>letterLowercase</a> | <a href=#s-letteruppercase>letterUppercase</a>
<a id=s-punctuation href=#s-punctuation>punctuation</a> ::= '-' | '_'
<a id=s-letterlowercase href=#s-letterlowercase>letterLowercase</a> ::= 'a' | 'b' | 'c' | 'd' | 'e' | 'f' | 'g' | 'h' | 'i' | 'j' | 'k' | 'l' | 'm' | 'n' | 'o' | 'p' | 'q' | 'r' | 's' | 't' | 'u' | 'v' | 'w' | 'x' | 'y' | 'z'
<a id=s-letteruppercase href=#s-letteruppercase>letterUppercase</a> ::= 'A' | 'B' | 'C' | 'D' | 'E' | 'F' | 'G' | 'H' | 'I' | 'J' | 'K' | 'L' | 'M' | 'N' | 'O' | 'P' | 'Q' | 'R' | 'S' | 'T' | 'U' | 'V' | 'W' | 'X' | 'Y' | 'Z'
<a id=s-digit href=#s-digit>digit</a> ::= '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
</code></pre>

Which keyword is used defines how messages with those rule identifiers are
handled:

###### `disable`

The **disable** keyword turns off all messages of the given rule identifiers.
When without identifiers, all messages are turned off.

For example, to turn off certain messages:

```markdown


*   **foo**

A paragraph, and now another list.

  * __bar__
```

###### `enable`

The **enable** keyword turns on all messages of the given rule identifiers.
When without identifiers, all messages are turned on.

For example, to enable certain messages:

```markdown


**foo** and __bar__.
```

###### `ignore`

The **ignore** keyword turns off all messages of the given `ruleId`s occurring
in the following node.
When without `ruleId`s, all messages are ignored.

Messages are turned on again after the end of the following node.

For example, to turn off certain messages for the next node:

```markdown


*   **foo**
  * __bar__
```

## Types

This package is fully typed with [TypeScript][].
An extra `Options` type is exported which models the interface of the accepted
options.

## Compatibility

Projects maintained by the unified collective are compatible with all maintained
versions of Node.js.
As of now, that is Node.js 12.20+, 14.14+, and 16.0+.
Our projects sometimes work with older versions, but this is not guaranteed.

This plugin works with `unified` version 6+ and `remark` version 7+.

## Security

Use of `remark-message-control` does not involve **[rehype][]** (**[hast][]**)
or user content so there are no openings for [cross-site scripting (XSS)][xss]
attacks.
Messages may be hidden from user content though, causing builds to fail or pass,
or changing a report.

## Related

*   [`remark-lint`][remark-lint]
    — plugin to lint code style
*   [`mdast-comment-marker`](https://github.com/syntax-tree/mdast-comment-marker)
    — mdast utility to parse comment markers

## Contribute

See [`contributing.md`][contributing] in [`remarkjs/.github`][health] for ways
to get started.
See [`support.md`][support] for ways to get help.

This project has a [code of conduct][coc].
By interacting with this repository, organization, or community you agree to
abide by its terms.

## License

[MIT][license] © [Titus Wormer][author]



[build-badge]: https://github.com/remarkjs/remark-message-control/workflows/main/badge.svg

[build]: https://github.com/remarkjs/remark-message-control/actions

[coverage-badge]: https://img.shields.io/codecov/c/github/remarkjs/remark-message-control.svg

[coverage]: https://codecov.io/github/remarkjs/remark-message-control

[downloads-badge]: https://img.shields.io/npm/dm/remark-message-control.svg

[downloads]: https://www.npmjs.com/package/remark-message-control

[size-badge]: https://img.shields.io/bundlephobia/minzip/remark-message-control.svg

[size]: https://bundlephobia.com/result?p=remark-message-control

[sponsors-badge]: https://opencollective.com/unified/sponsors/badge.svg

[backers-badge]: https://opencollective.com/unified/backers/badge.svg

[collective]: https://opencollective.com/unified

[chat-badge]: https://img.shields.io/badge/chat-discussions-success.svg

[chat]: https://github.com/remarkjs/remark/discussions

[npm]: https://docs.npmjs.com/cli/install

[skypack]: https://www.skypack.dev

[health]: https://github.com/remarkjs/.github

[contributing]: https://github.com/remarkjs/.github/blob/HEAD/contributing.md

[support]: https://github.com/remarkjs/.github/blob/HEAD/support.md

[coc]: https://github.com/remarkjs/.github/blob/HEAD/code-of-conduct.md

[license]: license

[author]: https://wooorm.com

[remark]: https://github.com/remarkjs/remark

[unified]: https://github.com/unifiedjs/unified

[typescript]: https://www.typescriptlang.org

[xss]: https://en.wikipedia.org/wiki/Cross-site_scripting

[rehype]: https://github.com/rehypejs/rehype

[hast]: https://github.com/syntax-tree/hast

[remark-lint]: https://github.com/remarkjs/remark-lint

## Credits and original authors

- Original project: [remark-message-control](https://github.com/remarkjs/remark-message-control).
- Titus Wormer.
- Christian Murphy.
- Copyright (c) 2016 Titus Wormer <tituswormer@gmail.com>.
- Stackline maintenance: [Alexandro Paixao Marques](https://www.linkedin.com/in/aleinfo/) and [Stackline contributors](https://github.com/alexandroit).

Original copyright, license notices and contributor acknowledgements remain part of this distribution. Stackline maintenance does not replace authorship of the original work.

## Community and Links

- [Stackline website](https://alexandro.net/)
- [GitHub projects](https://github.com/alexandroit)
- [npm packages](https://www.npmjs.com/~alex360qc)
- [Reddit community — r/Stackline](https://www.reddit.com/r/Stackline/)
- [Maintainer LinkedIn](https://www.linkedin.com/in/aleinfo/)

Use this repository's issue tracker for reproducible bugs and feature requests. Join r/Stackline for examples, usage questions and release discussions.
