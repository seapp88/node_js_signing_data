# Node.js signing SHA256 request data Axios

## Install OpenSSL (macOS)

```bash
brew install openssl
```

## Generate Keys

Generating a private key

```bash
openssl genrsa -aes256 -out private_key.pem
```

Now we generate a public key from the private key as follows

```bash
openssl rsa -in private_key.pem -outform PEM -pubout -out public_key.pem
```

Signature data

```js
const fs = require('fs');
const axios = require('axios');
const crypto = require('crypto');

const privateKey = fs.readFileSync('private_key.pem');

axios.interceptors.request.use(function (config) {

    const signer = crypto.createSign('RSA-SHA256');

    signer.update(JSON.stringify(config.data))

    const signature = signer.sign({
        key: privateKey,
        passphrase: '123456789'
    }, 'base64')

    config.headers['X-Request-Signature'] = signature

    return config;
}, function (error) {
    return Promise.reject(error);
});
```

