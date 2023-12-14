# vitepress-copy-helper

This plugin lets you add a copy button to your single-backtick-code-blocks and general purpose copy buttons.

## Installation

```bash
npm install --save-dev vitepress-copy-helper
```

In `.vitepress/theme/index.js`:

```js
import {default as CopyButton, copyHelperDefaultSettings} from 'vitepress-copy-helper/CopyButton.vue'
```