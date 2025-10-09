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



## Testing Program

```
const { zip, unzip } = require('czib');
const fs = require('fs').promises;

async function test() {
  console.log('Starting czib tests...\n');

  // Test 1: In-memory compression and encryption
  try {
    const inputText = 'Hello, this is a test string for compression and encryption!';
    const password = 'mySecurePassword123';
 
    console.log('Test 1: In-memory compression and encryption');
    const compressedEncrypted = await zip(inputText, password, { encrypt: true });
    console.log('Compressed and encrypted data length:', compressedEncrypted.length);
    console.log('IV (hex):', compressedEncrypted.slice(0, 16).toString('hex'));
 
    const decryptedDecompressed = await unzip(compressedEncrypted, password, { encrypt: true });
    const resultText = decryptedDecompressed.toString();
    console.log('Decrypted and decompressed result:', resultText);
    console.log('Test 1 Passed:', resultText === inputText, '\n');
  } catch (error) {
    console.error('Test 1 Failed:', error.message);
    console.error('Error stack:', error.stack);
  }

  // Test 2: File-based compression and encryption
  try {
    const inputFile = 'test_input.txt';
    const outputFile = 'test_output.bin';
    const password = 'mySecurePassword123';
    const inputContent = 'This is a test file content for file-based compression and encryption.';
 
    // Create a test input file
    await fs.writeFile(inputFile, inputContent);
    console.log('Test 2: File-based compression and encryption');
    console.log('Input file created:', inputFile);
 
    // Verify input file
    const inputStats = await fs.stat(inputFile);
    console.log('Input file size:', inputStats.size, 'bytes');
 
    await zip({ file: inputFile }, password, { encrypt: true, outputFile });
    console.log('Compressed and encrypted file created:', outputFile);
 
    // Verify output file and inspect contents
    const outputStats = await fs.stat(outputFile);
    console.log('Output file size:', outputStats.size, 'bytes');
    const fileContent = await fs.readFile(outputFile);
    console.log('Output file content (hex):', fileContent.toString('hex'));
    const iv = fileContent.slice(0, 16);
    console.log('IV (hex):', iv.toString('hex'));
 
    const decryptedOutputFile = 'test_decrypted.txt';
    await unzip({ file: outputFile }, password, { encrypt: true, outputFile: decryptedOutputFile });
    const resultContent = await fs.readFile(decryptedOutputFile, 'utf8');
    console.log('Decrypted and decompressed file content:', resultContent);
    console.log('Test 2 Passed:', resultContent === inputContent, '\n');
 
    // Clean up
    await fs.unlink(inputFile);
    await fs.unlink(outputFile);
    await fs.unlink(decryptedOutputFile);
  } catch (error) {
    console.error('Test 2 Failed:', error.message);
    console.error('Error stack:', error.stack);
  }

  // Test 3: Compression without encryption
  try {
    const inputText = 'Testing compression without encryption.';
 
    console.log('Test 3: In-memory compression without encryption');
    const compressed = await zip(inputText, null, { encrypt: false });
    console.log('Compressed data length:', compressed.length);
 
    const decompressed = await unzip(compressed, null, { encrypt: false });
    const resultText = decompressed.toString();
    console.log('Decompressed result:', resultText);
    console.log('Test 3 Passed:', resultText === inputText, '\n');
  } catch (error) {
    console.error('Test 3 Failed:', error.message);
    console.error('Error stack:', error.stack);
  }

  console.log('All tests completed.');
}

test().catch((error) => console.error('Test suite failed:', error));
```
