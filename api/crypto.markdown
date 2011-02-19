## Crypto
## 加密

Use `require('crypto')` to access this module.

使用`require('crypto')`调用加密模块。

The crypto module requires OpenSSL to be available on the underlying platform.
It offers a way of encapsulating secure credentials to be used as part
of a secure HTTPS net or http connection.

保密模块在底层平台上需要使用OpenSSL协议。它提供了一种安全认证的封装方式，该方式常作为HTTPS安全网络以及普通HTTP连接的一部分。

It also offers a set of wrappers for OpenSSL's hash, hmac, cipher, decipher, sign and verify methods.

该模块还提供了了一套基于OpenSSL的hash（哈希编码），hmac（），cipher（编码），decipher（解码），sign（登录）以及verify（验证）等方法的封装。

### crypto.createCredentials(details)

Creates a credentials object, with the optional details being a dictionary with keys:

创建一个凭证（credentials）对象，它可选参数details为一个带键值的字典（with the optional details being a dictionary with keys）：

* `key` : a string holding the PEM encoded private key

* `key`：为字符串型，PEM编码的私有键。

* `cert` : a string holding the PEM encoded certificate

* `cert` ：为字符串型，PEM编码的认证证书。

* `ca` : either a string or list of strings of PEM encoded CA certificates to trust.

* `ca`：可以是字符串，也可以是字符串队列，PEM编码的CA认证证书。

If no 'ca' details are given, then node.js will use the default publicly trusted list of CAs as given in
<http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt>.

如果没有给出'ca'的详细内容，那么node.js将会使用默认的公开受信任列表，该表位于<http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt>。


### crypto.createHash(algorithm)

Creates and returns a hash object, a cryptographic hash with the given algorithm
which can be used to generate hash digests.

创建并返回一个hash对象，它是一个加密hash对象，由用于生成hash码的指定算法创建。

`algorithm` is dependent on the available algorithms supported by the version
of OpenSSL on the platform. Examples are `'sha1'`, `'md5'`, `'sha256'`, `'sha512'`, etc.
On recent releases, `openssl list-message-digest-algorithms` will display the available digest algorithms.

参数`algorithm`依赖于已有的算法，需要系统内版本的OpenSSL的支持。例如：`'sha1'`, `'md5'`, `'sha256'`, `'sha512'`等。在近期发行的版本中，`openssl list-message-digest-algorithms`会显示这些可用的数字算法。

### hash.update(data)

Updates the hash content with the given `data`.
This can be called many times with new data as it is streamed.

更新指定`data`的hash码内容。
在数据流传输期间，会经常调用该方法。

### hash.digest(encoding='binary')

Calculates the digest of all of the passed data to be hashed.
The `encoding` can be `'hex'`, `'binary'` or `'base64'`.

把所有传送来的数据（ digest ）进行hash计算。参数`encoding`（编码方式）可以为`'hex'`, `'binary'` 或者`'base64'`。

### crypto.createHmac(algorithm, key)

Creates and returns a hmac object, a cryptographic hmac with the given algorithm and key.

创建并返回一个hmac对象，该加密的hmac对象使用指定的算法和键值生成。

`algorithm` is dependent on the available algorithms supported by OpenSSL - see createHash above.
`key` is the hmac key to be used.

参数`algorithm`依赖于已有的算法，需要系统内版本的OpenSSL的支持。可见上稳重的createHash。
`key`为需使用的hmac键值。

### hmac.update(data)

Update the hmac content with the given `data`.
This can be called many times with new data as it is streamed.

对给定的`data`更新hmac内容。
在数据流传输期间，会经常调用该方法。

### hmac.digest(encoding='binary')

Calculates the digest of all of the passed data to the hmac.
The `encoding` can be `'hex'`, `'binary'` or `'base64'`.

把所有传送来的数据（digest ）进行hmac计算。参数`encoding`（编码方式）可以为`'hex'`, `'binary'` 或者`'base64'`。

### crypto.createCipher(algorithm, key)

Creates and returns a cipher object, with the given algorithm and key.

使用指定的算法和键值创建并返回一个cipher对象。

`algorithm` is dependent on OpenSSL, examples are `'aes192'`, etc.
On recent releases, `openssl list-cipher-algorithms` will display the available cipher algorithms.

`algorithm`依赖于OpenSSL，例如，`'aes192'`等。在最近的发行版中，`openssl list-cipher-algorithms`会显示可用的cipher的算法。

### cipher.update(data, input_encoding='binary', output_encoding='binary')

Updates the cipher with `data`, the encoding of which is given in `input_encoding`
and can be `'utf8'`, `'ascii'` or `'binary'`. The `output_encoding` specifies
the output format of the enciphered data, and can be `'binary'`, `'base64'` or `'hex'`.

Returns the enciphered contents, and can be called many times with new data as it is streamed.

### cipher.final(output_encoding='binary')

Returns any remaining enciphered contents, with `output_encoding` being one of: `'binary'`, `'ascii'` or `'utf8'`.

### crypto.createDecipher(algorithm, key)

Creates and returns a decipher object, with the given algorithm and key.
This is the mirror of the cipher object above.

### decipher.update(data, input_encoding='binary', output_encoding='binary')

Updates the decipher with `data`, which is encoded in `'binary'`, `'base64'` or `'hex'`.
The `output_decoding` specifies in what format to return the deciphered plaintext: `'binary'`, `'ascii'` or `'utf8'`.

### decipher.final(output_encoding='binary')

Returns any remaining plaintext which is deciphered,
with `output_encoding' being one of: `'binary'`, `'ascii'` or `'utf8'`.


### crypto.createSign(algorithm)

Creates and returns a signing object, with the given algorithm.
On recent OpenSSL releases, `openssl list-public-key-algorithms` will display
the available signing algorithms. Examples are `'RSA-SHA256'`.

### signer.update(data)

Updates the signer object with data.
This can be called many times with new data as it is streamed.

### signer.sign(private_key, output_format='binary')

Calculates the signature on all the updated data passed through the signer.
`private_key` is a string containing the PEM encoded private key for signing.

Returns the signature in `output_format` which can be `'binary'`, `'hex'` or `'base64'`.

### crypto.createVerify(algorithm)

Creates and returns a verification object, with the given algorithm.
This is the mirror of the signing object above.

### verifier.update(data)

Updates the verifier object with data.
This can be called many times with new data as it is streamed.

### verifier.verify(cert, signature, signature_format='binary')

Verifies the signed data by using the `cert` which is a string containing
the PEM encoded public key, and `signature`, which is the previously calculates
signature for the data, in the `signature_format` which can be `'binary'`, `'hex'` or `'base64'`.

Returns true or false depending on the validity of the signature for the data and public key.
