# Encryption

A sample encryption helper for common usage. You can also get it from [NLibrary](https://github.com/nicholashew/NLibrary/blob/master/NLibrary/Security/Encryption.cs) in GitHub.

* [Base64](#base64)
* [MD5 Hash](#md5-hash)
* [SHA-1](#sha-1)
* [Data Encryption Standard (DES)](#data-encryption-standard-des)
* [Triple DES (3DES)](#triple-des-3des)
* [Advanced Encryption Standard (AES)](#advanced-encryption-standard-aes)

## Sample Usage

### Base64

```cs
string plainText = "hello world";
string encrypted = Encryption.Base64Encrypt(plainText);
string decrypted = Encryption.Base64Decrypt(encrypted);
Console.WriteLine($"{plainText} | {encrypted} | {decrypted}");
```

### MD5 Hash

The MD5 message-digest algorithm is a widely used hash function producing a 128-bit hash value. Although MD5 was initially designed to be used as a cryptographic hash function, it has been found to suffer from extensive vulnerabilities. [Wikipedia](https://en.wikipedia.org/wiki/MD5)

An MD5 hash is a one-way transaction and as such it is almost impossible to reverse engineer an MD5 hash to retrieve the original string.

```cs
string plainText = "hello world";
string encrypted = Encryption.MD5Hash(plainText);
Console.WriteLine($"{plainText} | {encrypted}");
```

### SHA-1

In cryptography, SHA-1 is a cryptographic hash function which takes an input and produces a 160-bit hash value known as a message digest – typically rendered as a hexadecimal number, 40 digits long. It was designed by the United States National Security Agency, and is a U.S. Federal Information Processing Standard. [Wikipedia](https://en.wikipedia.org/wiki/SHA-1)

```cs
string plainText = "hello world";
string encrypted = Encryption.SHA1Hash(plainText);
Console.WriteLine($"{plainText} | {encrypted}");
```

### Data Encryption Standard (DES)

The Data Encryption Standard is a symmetric-key algorithm for the encryption of electronic data. Although insecure, it was highly influential in the advancement of modern cryptography. [Wikipedia](https://en.wikipedia.org/wiki/Data_Encryption_Standard)

> Note: secret key and vector must be 8 characters length

```cs
string plainText = "hello world";
string secretKey = "secret99"; //must be exact 8 chars length
string initVector = "!@#$%^&*"; //must be exact 8 chars length
string encrypted = Encryption.DesEncrypt(plainText, secretKey, initVector);
string decrypted = Encryption.DesDecrypt(encrypted, secretKey, initVector);
Console.WriteLine($"{plainText} | {encrypted} | {decrypted}");
```

### Triple DES (3DES)

In cryptography, Triple DES, officially the Triple Data Encryption Algorithm, is a symmetric-key block cipher, which applies the DES cipher algorithm three times to each data block. [Wikipedia](https://en.wikipedia.org/wiki/Triple_DES)

> Note: secret key must be 24 characters length and vector must be 8 characters length

```cs
string plainText = "hello world";
string secretKey = "secret99asdfghjkqwerty12"; //must be exact 24 chars length
string initVector = "!@#$%^&*"; //must be exact 8 chars length
string encrypted = Encryption.TripleDESEncrypt(plainText, secretKey, initVector);
string decrypted = Encryption.TripleDESDecrypt(encrypted, secretKey, initVector);
Console.WriteLine($"{plainText} | {encrypted} | {decrypted}");
```

### Advanced Encryption Standard (AES)

The Advanced Encryption Standard, also known by its original name Rijndael, is a specification for the encryption of electronic data established by the U.S. National Institute of Standards and Technology in 2001. [Wikipedia](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)

> Note: AES key accept for three different key lengths:
> * 128 Bit - 16 ASCII characters, 1 byte each
> * 192 Bit - 24 ASCII characters, 1 byte each
> * 256 Bit - 32 ASCII characters, 1 byte each

```cs
string plainText = "hello world";
string key = "!@#$%^&*()1234567890qwer"; //128 Bit
string encrypted = Encryption.AesEncrypt(plainText, key);
string decrypted = Encryption.AesDecrypt(encrypted, key);
Console.WriteLine($"{plainText} | {encrypted} | {decrypted}");
```

