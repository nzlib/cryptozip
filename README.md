## Test

```
const { zip, unzip } = require('czib');
const fs = require('fs').promises;

async function test() {
  console.log('Starting nzlite.js tests...\n');

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
