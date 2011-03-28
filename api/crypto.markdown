## Crypto 加密模块

Use `require('crypto')` to access this module.

使用`require('crypto')`调用加密模块。

The crypto module requires OpenSSL to be available on the underlying platform.
It offers a way of encapsulating secure credentials to be used as part
of a secure HTTPS net or http connection.

加密模块需要底层系统提供OpenSSL的支持。它提供了一种安全凭证的封装方式，用于HTTPS安全网络以及普通HTTP连接。

It also offers a set of wrappers for OpenSSL's hash, hmac, cipher, decipher, sign and verify methods.

该模块还提供了一套针对OpenSSL的hash（哈希），hmac（密钥哈希），cipher（编码），decipher（解码），sign（签名）以及verify（验证）等方法的封装。

### crypto.createCredentials(details)

Creates a credentials object, with the optional details being a dictionary with keys:

创建一个凭证对象，可选参数details为一个带键值的字典：

* `key` : a string holding the PEM encoded private key

  `key`：为字符串型，PEM编码的私钥。

* `cert` : a string holding the PEM encoded certificate

  `cert`：为字符串型，PEM编码的认证证书。

* `ca` : either a string or list of strings of PEM encoded CA certificates to trust.

  `ca`：字符串形式的PEM编码可信CA证书，或证书列表。

If no 'ca' details are given, then node.js will use the default publicly trusted list of CAs as given in
<http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt>.

如果没有给出'ca'的详细内容，那么node.js将会使用默认的公开受信任列表，该表位于<http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt>。


### crypto.createHash(algorithm)

Creates and returns a hash object, a cryptographic hash with the given algorithm
which can be used to generate hash digests.

创建并返回一个hash对象，它是一个指定算法的加密hash，用于生成hash摘要。

`algorithm` is dependent on the available algorithms supported by the version
of OpenSSL on the platform. Examples are `'sha1'`, `'md5'`, `'sha256'`, `'sha512'`, etc.
On recent releases, `openssl list-message-digest-algorithms` will display the available digest algorithms.

参数`algorithm`可选择系统上安装的OpenSSL版本所支持的算法。例如：`'sha1'`, `'md5'`, `'sha256'`, `'sha512'`等。在近期发行的版本中，`openssl list-message-digest-algorithms`会显示这些可用的摘要算法。

### hash.update(data)

Updates the hash content with the given `data`.
This can be called many times with new data as it is streamed.

更新hash的内容为指定的`data`。当使用流数据时可能会多次调用该方法。

### hash.digest(encoding='binary')

Calculates the digest of all of the passed data to be hashed.
The `encoding` can be `'hex'`, `'binary'` or `'base64'`.

计算所有传入数据的hash摘要。参数`encoding`（编码方式）可以为`'hex'`, `'binary'` 或者`'base64'`。

### crypto.createHmac(algorithm, key)

Creates and returns a hmac object, a cryptographic hmac with the given algorithm and key.

创建并返回一个hmac对象，它是一个指定算法和密钥的加密hmac。

`algorithm` is dependent on the available algorithms supported by OpenSSL - see createHash above.
`key` is the hmac key to be used.

参数`algorithm`可选择OpenSSL支持的算法 - 参见上文的createHash。参数`key`为hmac所使用的密钥。

### hmac.update(data)

Update the hmac content with the given `data`.
This can be called many times with new data as it is streamed.

更新hmac的内容为指定的`data`。当使用流数据时可能会多次调用该方法。

### hmac.digest(encoding='binary')

Calculates the digest of all of the passed data to the hmac.
The `encoding` can be `'hex'`, `'binary'` or `'base64'`.

计算所有传入数据的hmac摘要。参数`encoding`（编码方式）可以为`'hex'`, `'binary'` 或者`'base64'`。

### crypto.createCipher(algorithm, key)

Creates and returns a cipher object, with the given algorithm and key.

使用指定的算法和密钥创建并返回一个cipher对象。

`algorithm` is dependent on OpenSSL, examples are `'aes192'`, etc.
On recent releases, `openssl list-cipher-algorithms` will display the available cipher algorithms.

参数`algorithm`可选择OpenSSL支持的算法，例如`'aes192'`等。在最近的发行版中，`openssl list-cipher-algorithms`会显示可用的加密的算法。

### cipher.update(data, input_encoding='binary', output_encoding='binary')

Updates the cipher with `data`, the encoding of which is given in `input_encoding`
and can be `'utf8'`, `'ascii'` or `'binary'`. The `output_encoding` specifies
the output format of the enciphered data, and can be `'binary'`, `'base64'` or `'hex'`.

使用参数`data`更新要加密的内容，其编码方式由参数`input_encoding`指定，可以为 `'utf8'`, `'ascii'`或者`'binary'`。参数`output_encoding`指定了已加密内容的输出编码方式，可以为 `'binary'`, `'base64'`或`'hex'`。

Returns the enciphered contents, and can be called many times with new data as it is streamed.

返回已加密的内容，当使用流数据时可能会多次调用该方法。

### cipher.final(output_encoding='binary')

Returns any remaining enciphered contents, with `output_encoding` being one of: `'binary'`, `'ascii'` or `'utf8'`.

返回所有剩余的加密内容，`output_encoding`输出编码为`'binary'`, `'ascii'`或`'utf8'`其中之一。

### crypto.createDecipher(algorithm, key)

Creates and returns a decipher object, with the given algorithm and key.
This is the mirror of the cipher object above.

使用给定的算法和密钥创建并返回一个解密对象。该对象为上述加密对象的反向运算。

### decipher.update(data, input_encoding='binary', output_encoding='binary')

Updates the decipher with `data`, which is encoded in `'binary'`, `'base64'` or `'hex'`.
The `output_decoding` specifies in what format to return the deciphered plaintext: `'binary'`, `'ascii'` or `'utf8'`.

使用参数`data`更新要解密的内容，其编码方式为`'binary'`，`'base64'`或`'hex'`。参数`output_encoding`指定了已解密的明文内容的输出编码方式，可以为 `'binary'`，`'ascii'`或`'utf8'`。

### decipher.final(output_encoding='binary')

Returns any remaining plaintext which is deciphered,
with `output_encoding' being one of: `'binary'`, `'ascii'` or `'utf8'`.

返回全部剩余的已解密的明文，其`output_encoding' 为`'binary'`, `'ascii'`或`'utf8'`其中之一。

### crypto.createSign(algorithm)

Creates and returns a signing object, with the given algorithm.
On recent OpenSSL releases, `openssl list-public-key-algorithms` will display
the available signing algorithms. Examples are `'RSA-SHA256'`.

使用给定的算法创建并返回一个签名器对象。在现有的OpenSSL发行版中，`openssl list-public-key-algorithms`会显示可用的签名算法，例如：`'RSA-SHA256'`。

### signer.update(data)

Updates the signer object with data.
This can be called many times with new data as it is streamed.

使用data参数更新签名器对象。当使用流数据时可能会多次调用该方法。

### signer.sign(private_key, output_format='binary')

Calculates the signature on all the updated data passed through the signer.
`private_key` is a string containing the PEM encoded private key for signing.

对所有传入签名器的数据计算其签名。`private_key`为字符串，它包含了PEM编码的用于签名的私钥。

Returns the signature in `output_format` which can be `'binary'`, `'hex'` or `'base64'`.

返回签名，其`output_format`输出可以为`'binary'`, `'hex'` 或者`'base64'`。

### crypto.createVerify(algorithm)

Creates and returns a verification object, with the given algorithm.
This is the mirror of the signing object above.

使用给定算法创建并返回一个验证器对象。它是上述签名器对象的反向运算。

### verifier.update(data)

Updates the verifier object with data.
This can be called many times with new data as it is streamed.

使用data参数更新验证器对象。当使用流数据时可能会多次调用该方法。

### verifier.verify(cert, signature, signature_format='binary')

Verifies the signed data by using the `cert` which is a string containing
the PEM encoded public key, and `signature`, which is the previously calculates
signature for the data, in the `signature_format` which can be `'binary'`, `'hex'` or `'base64'`.

使用参数`cert`和`signature`验证已签名的数据，`cert`为经过PEM编码的公钥字符串，`signature`为之前已计算的数据的签名，`signature_format`可以为`'binary'`，`'hex'` 或者`'base64'`。

Returns true or false depending on the validity of the signature for the data and public key.

根据对数据和公钥进行签名有效性验证的结果，返回true或者false。