# Cryptography-Encryption-VPN-Security-Lab

## Objective

The Cryptography, Encryption & VPN Security Lab focused on applying fundamental cryptographic techniques used to protect data confidentiality, integrity, authenticity, and network communications.

Using **Kali Linux and OpenSSL**, I performed symmetric encryption, cryptographic hashing, HMAC authentication, RSA asymmetric encryption, digital signing, and X.509 certificate operations. I also used **Wireshark** to analyze network traffic and examine the visibility of DNS, HTTP, TLS, and TCP communications.

This lab provided hands-on experience connecting individual cryptographic technologies to broader security concepts such as **Public Key Infrastructure (PKI), digital signatures, certificate trust, VPNs, IPsec, and secure communications**.

---

## Skills Learned

- Implemented AES-256-CBC symmetric encryption and decryption.
- Generated SHA-256 and MD5 cryptographic hashes.
- Demonstrated how file modifications affect cryptographic hashes.
- Generated and analyzed HMAC-SHA256 authentication values.
- Created RSA-2048 public and private key pairs.
- Performed RSA public-key encryption and private-key decryption.
- Created and verified RSA digital signatures.
- Demonstrated digital-signature failure after data modification.
- Generated and inspected a self-signed X.509 certificate.
- Developed an understanding of PKI and certificate-based trust.
- Captured and analyzed DNS, HTTP, TLS, and TCP network traffic.
- Strengthened understanding of VPN, IPsec, encryption, integrity, authentication, and non-repudiation.

---

## Tools Used

- **Kali Linux** — Primary operating environment for cryptographic operations and network analysis.
- **OpenSSL** — Used for AES encryption, hashing, HMAC, RSA, digital signatures, and X.509 certificates.
- **Wireshark** — Used to capture and analyze network traffic.
- **SHA-256** — Used for cryptographic integrity verification.
- **MD5** — Examined as a legacy hashing algorithm for comparison.
- **RSA** — Used for asymmetric encryption and digital signatures.
- **AES-256-CBC** — Used to demonstrate symmetric encryption and confidentiality.

---

# Steps

## 1. Created the Cryptography Lab Environment

I began by creating a dedicated directory for the cryptographic exercises.

```bash
mkdir -p ~/lab3_crypto
cd ~/lab3_crypto
```

I then created a plaintext file containing a test message:

```bash
echo "This is a secret message for Lab 3." > plaintext.txt
cat plaintext.txt
```

<img width="867" height="420" alt="image" src="https://github.com/user-attachments/assets/a3741fbd-42fc-4598-bfdb-3ed284a0ff96" />


**Figure 1 — Plaintext file creation.** A readable test message was created to serve as the original data for the encryption, hashing, and integrity exercises.

The file represented information in its original **plaintext** form before cryptographic protection was applied.

---

## 2. AES-256 Symmetric Encryption

I encrypted the plaintext file using **AES-256-CBC** through OpenSSL.

```bash
openssl enc -aes-256-cbc -salt -pbkdf2 -in plaintext.txt -out ciphertext.bin
```

The resulting ciphertext was examined using:

```bash
xxd ciphertext.bin | head
file ciphertext.bin
```

<img width="970" height="431" alt="image" src="https://github.com/user-attachments/assets/e8614ffe-32b2-4fce-abf0-0a5f5e89af7e" />


**Figure 2 — AES-256-CBC encryption.** The original readable plaintext was transformed into encrypted binary data.

AES is a **symmetric encryption algorithm**, meaning the same secret is used during encryption and decryption.

This exercise demonstrated **confidentiality**, because the original message was no longer directly readable after encryption.

---

## 3. AES Decryption and Verification

I decrypted the ciphertext using:

```bash
openssl enc -aes-256-cbc -d -pbkdf2 -in ciphertext.bin -out decrypted.txt
```

I then compared the decrypted file with the original plaintext:

```bash
diff plaintext.txt decrypted.txt
```

Finally, I displayed the recovered message:

```bash
cat decrypted.txt
```

<img width="967" height="306" alt="image" src="https://github.com/user-attachments/assets/7284adcd-6c0a-447d-94af-0486a7371d8c" />


**Figure 3 — Successful AES decryption.** The encrypted file was successfully restored to its original plaintext form.

The lack of output from `diff` indicated that the original and decrypted files matched.

This demonstrated the complete symmetric encryption process:

```text
Plaintext → Encryption → Ciphertext → Decryption → Plaintext
```

---

## 4. SHA-256 and MD5 Hashing

I generated cryptographic hashes of the plaintext file using **SHA-256** and **MD5**.

```bash
sha256sum plaintext.txt
md5sum plaintext.txt
```

<img width="963" height="219" alt="image" src="https://github.com/user-attachments/assets/3062afd9-3e32-4118-b4f2-748b20048335" />


**Figure 4 — SHA-256 and MD5 hash generation.** Each hashing algorithm generated a fixed-length digest representing the contents of the file.

Unlike encryption, hashing is designed to be a **one-way operation**.

Hashes can be used to verify **data integrity** by determining whether information has changed.

SHA-256 provides stronger collision resistance than MD5, while MD5 is considered unsuitable for modern security-sensitive integrity applications.

---

## 5. Demonstrated Hash Change Detection

I made a small modification to the plaintext file and recalculated its SHA-256 hash.

```bash
echo " " >> plaintext.txt
sha256sum plaintext.txt
```

<img width="972" height="208" alt="image" src="https://github.com/user-attachments/assets/6a3e672e-61d6-4ba3-9711-6228781f4ede" />


**Figure 5 — SHA-256 after modification of the plaintext file.** A small change to the input produced a completely different hash value.

This demonstrated how cryptographic hashes can help detect unauthorized or unexpected modifications to information.

---

## 6. Generated an HMAC

Next, I generated an **HMAC-SHA256** value using a shared secret.

```bash
openssl dgst -sha256 -hmac "Lab3SharedKey" plaintext.txt
```

<img width="972" height="144" alt="image" src="https://github.com/user-attachments/assets/3f469412-c291-4483-92ae-46de0417d7de" />


**Figure 6 — HMAC-SHA256 generation.** The HMAC combined the contents of the message with a shared secret to produce an authentication value.

Unlike a normal cryptographic hash, an HMAC incorporates a secret key.

HMAC can therefore provide:

- **Integrity** — Helps detect whether information has changed.
- **Authentication** — Helps verify that the HMAC was generated by someone possessing the shared secret.

---

## 7. Tested HMAC Tamper Detection

I modified the protected file:

```bash
echo "tamper" >> plaintext.txt
```

I then generated another HMAC:

```bash
openssl dgst -sha256 -hmac "Lab3SharedKey" plaintext.txt
```

<img width="967" height="214" alt="image" src="https://github.com/user-attachments/assets/8780ae99-f7f7-4aff-8020-846fd5602844" />


**Figure 7 — HMAC after modification of the message.** Changing the contents of the file resulted in a different HMAC value.

This demonstrated how HMAC can help detect whether protected information has been modified.

---

## 8. Generated an RSA-2048 Private Key

The lab then transitioned from symmetric cryptography to **asymmetric cryptography**.

I generated a 2048-bit RSA private key:

```bash
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out private.pem
```

<img width="975" height="534" alt="image" src="https://github.com/user-attachments/assets/6f203f0d-1df5-4346-b606-52acb6ba34db" />


**Figure 8 — RSA-2048 private-key generation.** OpenSSL generated the private component of an asymmetric RSA key pair.

Unlike symmetric cryptography, asymmetric cryptography uses two mathematically related keys:

- Public key
- Private key

The private key must remain protected because it is used for sensitive cryptographic operations such as decryption and digital signing.

---

## 9. Generated the RSA Public Key

The corresponding public key was extracted from the private key:

```bash
openssl rsa -in private.pem -pubout -out public.pem
```

The generated files were reviewed using:

```bash
ls -l
```

<img width="811" height="534" alt="image" src="https://github.com/user-attachments/assets/c4f962f6-23f8-4929-88e4-44c0747dc1bd" />


**Figure 9 — RSA public-key generation.** The resulting `private.pem` and `public.pem` files formed the RSA asymmetric key pair.

The public key can be distributed, while the private key should remain under the control of its owner.

---

## 10. RSA Asymmetric Encryption and Decryption

I created another test message:

```bash
echo "Asymmetric crypto text message" > msg.txt
```

The message was encrypted using the **public key**:

```bash
openssl pkeyutl -encrypt -pubin -inkey public.pem -in msg.txt -out msg.enc
```

The encrypted message was then decrypted using the corresponding **private key**:

```bash
openssl pkeyutl -decrypt -inkey private.pem -in msg.enc -out msg.dec
```

The recovered message was displayed:

```bash
cat msg.dec
```

<img width="975" height="353" alt="image" src="https://github.com/user-attachments/assets/f955d785-4424-4bf3-b447-9f36ddd78258" />


**Figure 10 — RSA public-key encryption and private-key decryption.** The public key protected the message while the corresponding private key recovered the original plaintext.

This demonstrated a fundamental asymmetric cryptography concept:

```text
Public Key → Encryption
Private Key → Decryption
```

---

## 11. Created and Verified a Digital Signature

The RSA key pair was also used to demonstrate **digital signatures**.

The message was signed using the private key:

```bash
openssl dgst -sha256 -sign private.pem -out msg.sig msg.txt
```

The corresponding public key was then used to verify the signature:

```bash
openssl dgst -sha256 -verify public.pem -signature msg.sig msg.txt
```

Successful verification returned:

```text
Verified OK
```

<img width="975" height="379" alt="image" src="https://github.com/user-attachments/assets/fc439985-e04a-46bc-ae66-830bc86a5a7b" />


**Figure 11 — Successful RSA digital-signature verification.** The private key was used to sign the message, while the corresponding public key successfully verified the signature.

Digital signatures support:

- **Integrity**
- **Authentication**
- **Non-repudiation**

---

## 12. Tested Digital Signature Tamper Detection

I modified the signed message:

```bash
echo "tamper" >> msg.txt
```

The original digital signature was then verified against the modified message:

```bash
openssl dgst -sha256 -verify public.pem -signature msg.sig msg.txt
```

The verification failed.

<img width="973" height="288" alt="image" src="https://github.com/user-attachments/assets/9c31cadd-b9a3-41e1-b993-59a67e70edad" />


**Figure 12 — Digital-signature verification failure after message modification.** Changing the signed information caused the original signature to become invalid.

This demonstrated how digital signatures can help identify unauthorized modifications to signed information.

---

## 13. Generated a Self-Signed X.509 Certificate

The next portion of the lab focused on **digital certificates and Public Key Infrastructure (PKI)**.

A self-signed X.509 certificate and RSA key were generated using OpenSSL.

```bash
openssl req -x509 -newkey rsa:2048 -keyout certkey.pem -out cert.pem -days 365 -nodes
```

<img width="973" height="627" alt="image" src="https://github.com/user-attachments/assets/c4ea897f-0b48-482e-bd50-ca3bf3d51596" />


**Figure 13 — Generation of a self-signed X.509 certificate.** The certificate was created using a 2048-bit RSA key and configured with a one-year validity period.

Digital certificates associate an identity with a public key and play an important role in establishing trust between systems.

---

## 14. Inspected the X.509 Certificate

The generated certificate was examined using:

```bash
openssl x509 -in cert.pem -text -noout | head -n 40
```

<img width="964" height="659" alt="image" src="https://github.com/user-attachments/assets/4de5f953-7c3d-4291-a9ac-f103c7a27910" />


**Figure 14 — Inspection of the generated X.509 certificate.** The certificate output displayed its version, serial number, signature algorithm, issuer, validity period, subject, and public-key information.

The certificate showed:

- X.509 Version 3
- SHA-256 with RSA signature
- 2048-bit RSA public key
- Issuer information
- Subject information
- Certificate validity period

Because the certificate was self-signed, the **issuer and subject represented the same entity**.

This demonstrated the basic structure of digital certificates used within **Public Key Infrastructure (PKI)**.

---

# Network Traffic Analysis

## 15. Generated DNS Traffic

To examine network visibility, I generated DNS queries from Kali Linux using `nslookup`.

```bash
nslookup google.com
```

<img width="975" height="457" alt="image" src="https://github.com/user-attachments/assets/f93fad11-e17c-4251-ad6f-f930e7a4a03e" />


**Figure 15 — DNS query generated using nslookup.** The system queried the configured DNS resolver and received multiple IPv4 and IPv6 addresses.

A second DNS query was also generated for additional traffic analysis.

```bash
nslookup example.com
```

<img width="569" height="644" alt="image" src="https://github.com/user-attachments/assets/0788eac4-992d-42ec-97bb-488cde61b1ec" />


**Figure 16 — Additional DNS lookup used to generate network traffic for packet analysis.**

---

## 16. Captured DNS Traffic With Wireshark

Wireshark was used to capture DNS communication generated from the Kali system.

<img width="356" height="178" alt="image" src="https://github.com/user-attachments/assets/f00c0096-1045-41fb-bce9-332f7f974c14" />


**Figure 17 — DNS communication captured in Wireshark.** The capture shows the Kali host communicating directly with the configured DNS resolver.

Another DNS-focused view of the baseline packet capture showed communication between the local system and DNS infrastructure.

<img width="975" height="204" alt="image" src="https://github.com/user-attachments/assets/1d899c02-714a-4558-8f60-1514f39d0323" />


**Figure 18 — DNS traffic within the baseline Wireshark capture.** Packet analysis revealed source and destination addresses, protocol information, and communication direction.

This demonstrated how network monitoring can expose metadata such as:

- Source IP address
- Destination IP address
- Protocol
- Communication direction
- DNS infrastructure

---

## 17. Analyzed HTTP Traffic

Wireshark was filtered for HTTP traffic.

```text
http
```

<img width="975" height="359" alt="image" src="https://github.com/user-attachments/assets/e6bffbe1-43cb-4845-8b41-3815daa32d04" />


**Figure 19 — HTTP traffic identified in Wireshark.** The capture displayed an HTTP request traveling from the local Kali system to a remote server.

HTTP traffic does not provide the same transport encryption as HTTPS, making it important to understand the risks of transmitting sensitive information over unprotected protocols.

---

## 18. Analyzed TLS Traffic

Wireshark was then filtered for TLS traffic.

```text
tls
```

<img width="975" height="418" alt="image" src="https://github.com/user-attachments/assets/918c4cfe-886b-49ec-a9df-1e1afc9f6170" />


**Figure 20 — TLS traffic captured in Wireshark.** The capture identified TLS 1.2 and TLS 1.3 communications between the Kali system and remote servers.

Although the application data was encrypted, Wireshark could still observe network metadata such as:

- Source IP
- Destination IP
- Protocol
- Packet size
- Timing

This demonstrates an important distinction between **encrypting data and hiding network activity**.

---

## 19. Compared TCP Port 80 and Port 443 Traffic

I used Wireshark display filters to examine traffic associated with common web ports.

### TCP Port 80

```text
tcp.port == 80
```

<img width="975" height="432" alt="image" src="https://github.com/user-attachments/assets/8ec24ba0-be25-47d2-b2ca-5bba97e4e627" />


**Figure 21 — TCP port 80 traffic.** The capture displayed TCP connections associated with HTTP communications.

### TCP Port 443

```text
tcp.port == 443
```

<img width="975" height="298" alt="image" src="https://github.com/user-attachments/assets/1beae07c-d977-4a40-b375-99a9d18c38d7" />

<img width="975" height="454" alt="image" src="https://github.com/user-attachments/assets/8537be3c-89da-4869-897a-70d903875a77" />



**Figure 22 — TCP port 443 traffic.** The capture displayed HTTPS/TLS-related communication between the Kali system and remote hosts.

Comparing these captures reinforced the difference between traditional HTTP communication and encrypted HTTPS/TLS communication.

---

# VPN and IPsec Security

The lab also examined how cryptographic technologies are applied to **Virtual Private Networks (VPNs)** and **IPsec**.

IPsec operates at the network layer and can protect IP communications using cryptographic mechanisms.

Two important IPsec components are:

### Authentication Header (AH)

AH provides:

- Integrity
- Authentication

AH does **not** provide confidentiality.

### Encapsulating Security Payload (ESP)

ESP can provide:

- Confidentiality
- Integrity
- Authentication

IPsec can also operate in two major modes:

### Transport Mode

Transport mode protects the IP packet's payload while leaving the original IP header available for routing.

### Tunnel Mode

Tunnel mode encapsulates the original IP packet inside another packet.

Tunnel mode is commonly associated with VPN implementations because it can protect the original packet as it travels across an untrusted network.

---

# Cryptographic Security Comparison

| **Technology** | **Primary Purpose** | **Key Model** |
| --- | --- | --- |
| AES-256 | Confidentiality | Shared Secret |
| SHA-256 | Integrity Verification | No Key |
| HMAC-SHA256 | Integrity + Authentication | Shared Secret |
| RSA Encryption | Asymmetric Cryptography | Public + Private Key |
| Digital Signature | Integrity + Authentication + Non-Repudiation | Public + Private Key |
| X.509 Certificate | Identity + Public Key Trust | PKI |
| TLS | Secure Network Communication | Hybrid Cryptography |
| IPsec | Network-Layer Protection | Multiple Cryptographic Mechanisms |

---

# Security Analysis

This project demonstrated that cryptography is not a single security technology. Different cryptographic mechanisms provide different security properties.

**AES-256** demonstrated confidentiality by converting readable plaintext into ciphertext.

**SHA-256** demonstrated integrity verification by generating a digest that changed when the original information was modified.

**HMAC-SHA256** combined hashing with a shared secret to provide integrity and authentication.

**RSA** demonstrated asymmetric cryptography through separate public and private keys.

**Digital signatures** demonstrated how asymmetric cryptography can provide integrity, authentication, and non-repudiation.

**X.509 certificates** demonstrated how public keys can be associated with identities as part of a PKI.

Finally, **Wireshark network analysis** demonstrated that encryption can protect application data while network metadata may remain visible to network observers.

Together, these exercises demonstrated how multiple cryptographic technologies contribute to secure communications and modern cybersecurity architecture.

---

# Key Security Recommendations

- Use modern encryption algorithms such as AES for protecting sensitive information.
- Use SHA-256 or stronger hashing algorithms for security-sensitive integrity verification.
- Avoid deprecated algorithms such as MD5 for modern security applications.
- Protect symmetric encryption keys and HMAC shared secrets.
- Secure private RSA keys from unauthorized access.
- Use trusted Certificate Authorities for production certificates.
- Monitor certificate expiration and validity.
- Use TLS when transmitting sensitive application data.
- Use VPN technologies when communicating across untrusted networks where appropriate.
- Apply strong key-management practices throughout the cryptographic lifecycle.
- Understand that encrypted communications can still expose network metadata.
- Select cryptographic technologies according to the security property required.

---

# Conclusion

This project provided hands-on experience with several of the cryptographic technologies used to secure modern information systems and network communications.

Using **OpenSSL**, I implemented AES-256 symmetric encryption, SHA-256 hashing, HMAC authentication, RSA-2048 asymmetric cryptography, digital signatures, and X.509 certificates.

Using **Wireshark**, I analyzed DNS, HTTP, TLS, and TCP communications to understand what information remains observable during network transmission.

The lab strengthened my understanding of how **confidentiality, integrity, authentication, non-repudiation, PKI, TLS, VPNs, and IPsec** work together to protect data and communications.
