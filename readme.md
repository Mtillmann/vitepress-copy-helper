# vitepress-copy-helper

This plugin lets you add a copy button to your single-backtick-code-blocks and general purpose copy buttons.

## Installation

```bash
npm install --save-dev vitepress-copy-helper
```

## Usage

In `.vitepress/theme/index.js`:

```js
import CopyButton from 'vitepress-copy-helper';
import 'vitepress-copy-helper/style.css'
```
then, in the `enhanceApp` function, add:
```js
app.component('C', CopyButton),
```
`C` is the name of the component you will use in your markdown files, you can change it to whatever you want.

## Default Settings

To override the default settings, change the import to:
```js
import {default as CopyButton, defaultSettings} from 'vitepress-copy-helper'
import 'vitepress-copy-helper/style.css'
```
Just change the properties of `defaultSettings` to your liking:

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| position | string | 'auto' | The position of the button, relative to the target  `code`-element. `auto`, `start`, `end` |
| message | string | 'copied' | The message that will be displayed when the button is clicked |
| label | string | null | The label of the button, if `null`, no label is rendered |
| classes | string | 'copy-btn' | The classes that will be added to the button |
| target | string | 'auto' | Controls which `code`-element the button attaches to when wedged between `code`-elements |
| preferSibling | string | 'previous' | Controls which `code`-element is picked when button is wedged and `target` is `auto`. `previous`, `next` |

## Examples

[Look here for usage examples](https://mtillmann.blog/posts/simple-copy-button-helper-for-vitepress.html)

---

## Acknowledgements

`copyToClipboard.js` is taken from [vitepress core](https://github.com/vuejs/vitepress/blob/006fd800956de5f12f63980e854239c240a70203/src/client/app/composables/copyCode.ts#L45C1-L87C2). 

Package is based on [@briancbarrow's](https://github.com/briancbarrow) excellent [boilerplate project](https://github.com/briancbarrow/vue-component-library-2023)!
