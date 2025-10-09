# czib

[![NPM Version](https://img.shields.io/npm/v/czib.svg)](https://www.npmjs.com/package/czib) [![Node.js Version](https://img.shields.io/node/v/czib.svg)](https://nodejs.org/) [![License](https://img.shields.io/npm/l/czib.svg)](https://github.com/yourusername/czib/blob/main/LICENSE)

A lightweight Node.js library for compressing and optionally encrypting data or files using gzip and AES-256-CBC encryption. It supports both in-memory and file-based operations with efficient streaming, ideal for handling large datasets.

Built with Node.js core modules (`fs`, `zlib`, `crypto`, `stream`)—no external dependencies required.

## Features

- **Gzip Compression**: Reduces data size efficiently.
- **AES-256-CBC Encryption**: Optional encryption with password-derived keys (scrypt).
- **Input/Output Flexibility**: Supports strings, Buffers, or file paths.
- **Streaming Support**: Memory-efficient processing for large files.
- **Async/Await**: Promise-based API for modern JavaScript.
- **Secure IV**: Randomly generated and prepended to encrypted output for decryption.

**Security Note**: Uses a fixed salt (`'salt'`) for key derivation. For production, consider customizing the salt or key derivation method. Always use strong passwords.

## Installation

```bash
npm install czib
```

Requires Node.js >= 14 (for `stream.pipeline` and `crypto.scryptSync`).

## Usage

### Import

```javascript
const { zip, unzip } = require('czib');
```

### Compressing and Encrypting

#### In-Memory

```javascript
(async () => {
  const data = 'Sample text to compress and encrypt.';
  const password = 'securepassword123';
  try {
    const result = await zip(data, password, { encrypt: true });
    console.log('Output (hex):', result.toString('hex'));
  } catch (err) {
    console.error('Error:', err.message);
  }
})();
```

#### File-Based

```javascript
(async () => {
  const input = { file: 'input.txt' };
  const password = 'securepassword123';
  try {
    await zip(input, password, { encrypt: true, outputFile: 'output.czib' });
    console.log('Compressed and encrypted to output.czib');
  } catch (err) {
    console.error('Error:', err.message);
  }
})();
```

- Set `encrypt: false` to skip encryption (no password needed).
- Output is a Buffer (in-memory) or written to `outputFile`.

### Decompressing and Decrypting

#### In-Memory

```javascript
(async () => {
  const encryptedData = /* Buffer from zip */;
  const password = 'securepassword123';
  try {
    const result = await unzip(encryptedData, password, { encrypt: true });
    console.log('Decompressed:', result.toString());
  } catch (err) {
    console.error('Error:', err.message);
  }
})();
```

#### File-Based

```javascript
(async () => {
  const input = { file: 'output.czib' };
  const password = 'securepassword123';
  try {
    await unzip(input, password, { encrypt: true, outputFile: 'recovered.txt' });
    console.log('Decompressed and decrypted to recovered.txt');
  } catch (err) {
    console.error('Error:', err.message);
  }
})();
```

- For encrypted inputs, the first 16 bytes are the IV.
- Use same `encrypt` setting and password as during `zip`.

## API Reference

### `zip(input, password, options = {})`

- **input**: `string | Buffer | { file: string }` – Data or file path object.
- **password**: `string` – Required if `options.encrypt` is `true`.
- **options**:
  - `encrypt`: `boolean` (default
