# anki-apkg-export

[![Build Status](https://travis-ci.org/ewnd9/anki-apkg-export.svg?branch=master)](https://travis-ci.org/ewnd9/anki-apkg-export)

Universal module for generating decks for Anki.

Port of the Ruby gem https://github.com/albertzak/anki2

## Install

```
$ npm install anki-apkg-export --save
```

## Usage

### server

```js
const fs = require('fs');
const AnkiExport = require('anki-apkg-export').default;

const apkg = new AnkiExport('deck-name');

apkg.addMedia('anki.png', fs.readFileSync('anki.png'));

apkg.addCard('card #1 front', 'card #1 back');
apkg.addCard('card #2 front', 'card #2 back', { tags: ['nice', 'better card'] });
apkg.addCard('card #3 with image <img src="anki.png" />', 'card #3 back');

apkg
  .save()
  .then(zip => {
    fs.writeFileSync('./output.apkg', zip, 'binary');
    console.log(`Package has been generated: output.pkg`);
  })
  .catch(err => console.log(err.stack || err));
```

### browser

Intended to be used with [`webpack`](https://github.com/webpack/webpack)

```js
const webpack = require('webpack');

module.exports = {
  entry: './index.js',
  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel'
      },
    ]
  },
  plugins: [
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: JSON.stringify(process.env.NODE_ENV || 'development')
      },
    })
  ],
  output: {
    path: __dirname,
    filename: 'bundle.js'
  }
};
```

Required loaders:

- [`script-loader`](https://github.com/webpack/script-loader)
- [`raw-loader`](https://github.com/webpack/raw-loader)

```js
import { saveAs } from 'filesaverjs';
import AnkiExport from 'anki-apkg-export';

const apkg = new AnkiExport('deck-name');

// could be a File from <input /> or a Blob from fetch
// take a look at the example folder for a complete overview
apkg.addMedia('anki.png', file);

apkg.addCard('card #1 front', 'card #1 back');
apkg.addCard('card #2 front', 'card #2 back', { tags: ['nice', 'better card'] });
apkg.addCard('card #3 with image <img src="anki.png" />', 'card #3 back');

apkg
  .save()
  .then(zip => {
    const zip = apkg.save();
    saveAs(zip, 'output.apkg');
  })
  .catch(err => console.log(err.stack || err));
```

## Examples

- [server from above](examples/server)
- [browser from above](examples/browser)
- [browser usage with media attachments via ajax](examples/browser-media-ajax)
- [browser usage with media attachments via <form />](examples/browser-media-file-input)

## Changelog

- `v3.1.0` - make setting APP_ENV optional
- `v3.0.0` - add tags, ES6 refactor (breaking)
- `v2.0.0` - add media support, update jszip dependency (breaking)
- `v1.0.0` - initial rewrite

## Related

- [apkg format documentation](http://decks.wikia.com/wiki/Anki_APKG_format_documentation)
- [anki-apkg-export-cli](https://github.com/ewnd9/anki-apkg-export-cli) - CLI for this module
- [anki-apkg-export-app](https://github.com/ewnd9/anki-apkg-export-app) - Simple web app to generate cards online

## License

MIT © [ewnd9](http://ewnd9.com)
