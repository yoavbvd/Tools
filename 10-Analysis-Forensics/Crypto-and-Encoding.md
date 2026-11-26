# Crypto, RSA & Encoding

Online and offline tools for decoding, cipher identification, and RSA/CTF crypto challenges. Handy when you recover an encoded blob, a weak RSA key, or an obfuscated script.

## RSA & factoring

| Tool | What it does | Link |
| --- | --- | --- |
| **dCode — RSA Cipher** | French all-in-one cipher & crypto toolkit; the RSA page encrypts/decrypts and recovers keys from `n`, `e`, `c`. Hundreds of other cipher tools too. | https://www.dcode.fr/rsa-cipher |
| **FactorDB** | Database of known integer factorizations — paste an RSA modulus `n` to check if it is already factored (instant key recovery for weak/known moduli). | https://factordb.com |
| **RsaCtfTool** | Automated RSA attacks (weak keys, Wiener, Hastad, common factors) from a public key or `n`/`e`. | https://github.com/RsaCtfTool/RsaCtfTool |

```bash
# RsaCtfTool: try every attack against a public key
python3 RsaCtfTool.py --publickey key.pub --private
```
- `--publickey` — the target public key file. `--private` — attempt to recover and print the private key. Add `--uncipherfile cipher.bin` to also decrypt a ciphertext.

## General decoding / encoding

| Tool | What it does | Link |
| --- | --- | --- |
| **CyberChef** | Browser "Swiss-army knife": chain Base64, hex, XOR, ROT, gzip, JWT, and crypto operations in one recipe. | https://gchq.github.io/CyberChef/ |
| **dCode (home)** | Identify and solve classical ciphers (Caesar, Vigenère, substitution), encodings, and math puzzles. | https://www.dcode.fr |

## JavaScript deobfuscation

| Tool | What it does | Link |
| --- | --- | --- |
| **Obfuscator.io Deobfuscator** | Reverse `obfuscator.io`-style packed JavaScript back to readable source. | https://obf-io.deobfuscate.io/ |
| **de4js** | Multi-method JS unpacker/deobfuscator (eval, packer, array). | https://lelinhtinh.github.io/de4js/ |

> For analyzing malicious documents and macros, see [OLEdump](OLEdump.md) and the [Analysis & Forensics overview](README.md).
> For client-side JS attack/defense (cookies, XSS, tokens), see [Client-Side JavaScript Security](../03-Web/Client-Side-JavaScript-Security.md).
