# crytozip.js

A lightweight Node.js utility for **compressing** and **encrypting** files or buffers — and for **decompressing** and **decrypting** them later.  
It wraps Node’s native `zlib` and `crypto` modules to provide a simple async API for secure, stream-based ZIP operations.

---

## 🚀 Features

- 🔒 AES-256-CBC encryption with password protection  
- 🗜️ Gzip compression/decompression  
- 💾 Works with **buffers**, **strings**, or **files**  
- ⚙️ Stream-based design for efficiency  
- 🧩 Promise-based (async/await friendly)  

---

## 📦 Installation

```bash
npm install crytozip
```

*(Or just copy `crytozip.js` into your project if you prefer a standalone file.)*

---

## 🧠 Usage

### Import

```js
const { zip, unzip } = require('./crytozip');
```

---

### 🔐 Compress & Encrypt

Compress a string or file, optionally with a password.

#### Example 1 — In-memory compression with password

```js
const { zip } = require('./crytozip');

(async () => {
  const data = 'Hello Secure World!';
  const zipped = await zip(data, { password: 'mySecret123' });

  console.log('Compressed & encrypted buffer:', zipped);
})();
```

#### Example 2 — Compress a file and save to disk

```js
await zip({ file: 'input.txt' }, { password: 'mypwd', outputFile: 'output.gzenc' });
```

---

### 🔓 Decompress & Decrypt

#### Example 1 — In-memory decryption

```js
const { unzip } = require('./crytozip');

(async () => {
  const decrypted = await unzip(zipped, { password: 'mySecret123' });
  console.log(decrypted.toString());
})();
```

#### Example 2 — Decrypt file to disk

```js
await unzip({ file: 'output.gzenc' }, { password: 'mypwd', outputFile: 'restored.txt' });
```

---

## ⚙️ API Reference

### `zip(input, [options])`

| Parameter | Type | Description |
|------------|------|-------------|
| `input` | `string \| Buffer \| { file: string }` | Input data or path to file to compress |
| `options.password` | `string` *(optional)* | Encrypt with AES-256-CBC using this password |
| `options.outputFile` | `string` *(optional)* | If set, write result to this file |
| **Returns** | `Promise<Buffer \| undefined>` | Returns compressed buffer (if no `outputFile`) or writes to disk |

---

### `unzip(input, [options])`

| Parameter | Type | Description |
|------------|------|-------------|
| `input` | `Buffer \| { file: string }` | Input data or file path to decompress |
| `options.password` | `string` *(optional)* | Decrypt with AES-256-CBC using this password |
| `options.outputFile` | `string` *(optional)* | If set, write result to this file |
| **Returns** | `Promise<Buffer \| undefined>` | Returns decompressed buffer or writes to disk |

---

## 🧩 Notes

- AES-256-CBC encryption is used, with a random IV stored at the start of the file/buffer.  
- When `outputFile` is used, no buffer is returned — data is written directly to disk.  
- The password must be the same for compression and decompression.

---

## 🧪 Example CLI

You can quickly test it in Node REPL or add a simple CLI:

```bash
node -e "const {zip, unzip}=require('./crytozip');(async()=>{await zip({file:'a.txt'},{password:'123',outputFile:'a.gzenc'})})();"
```

---

## 📝 License

MIT © 2025
