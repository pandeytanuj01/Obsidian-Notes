#phishing

**Social engineering** refers to manipulating a user into revealing sensitive information, executing a malicious file and approving a payment. 

The term "social" refers to the human aspect of the attack rather than computers.

## Phishing

Phishing is a subset of social engineering, where attacker sends fraudulent communication via some medium such as emails, SMS, phone call, etc. manipulating user to perform any action compromising their sensitive information or giving attacker access to their personal devices.

Phishing is the most used common and effective way for attackers to gain initial access to target systems.

Attackers uses tools such as `setoolkit` or `GoPhish` for Social Engineering attacks or custom built tools.

The most common method for performing such attack is via email. Attacker's usually have a malicious attachment or a Fake website for credential stealing.

## Phishing Prevention

### Sender Policy Framework (SPF)

It is used to authenticate the sender of an email. An SPF record is a DNS TXT record which consists of all the mail servers who can send an email on behalf of a domain. 

So, whenever a email is being sent, the receiving mail server checks the domain's SPF record to verify whether the sending mail server is authorized to send mails on behalf of the domain.

The delivery of the email depends on the result of SPF record verification.

|    Verification Result | Intended Action                     |
| ---------------------: | ----------------------------------- |
| Pass, Neutral and None | Accept                              |
|    SoftFail, PermError | Flag (Mark as suspicious but allow) |
|        Fail, TempError | Reject (Discard the email)          |

Sample SPF Record:

`v=spf1 ip4:127.0.0.1 include:_spf.google.com -all`

- `v=spf1` Signifies the start of the SPF record
- `ip4:127.0.0.1` Specifies which IP can send mail (IPv4 in this case)
- `include:_spf.google.com` Specifies which domain can send mail
- `-all` Non-authorized emails will be rejected

### DomainKeys Identified Mail (DKIM)

It is used for the authentication of the mail that is being sent. DKIM record is also present in the DNS. 

When an email is being sent, the email server signs the email with its private key, the receiving mail server then checks the DKIM records to retrieve the public key to verify the message. If the signature matches, the email is authentic, otherwise it is either flagged or rejected.

Sample DKIM Record:

`v=DKIM1; k=rsa; p=<public_key>`

- `v=DKIM1` Specifies the version of DKIM being used (optional)
- `k=rsa` The key type. The RSA encryption algorithm is standard
- `p=` This is the public key that will be matched to the private key to verify the DKIM signature

### Domain-Based Messages Authentication, Reporting and Conformance (DMARC)

DMARC ensures that the sender's domain matches the domain verified by the SPF and DKIM. If the alignment fails, DKIM instructs the recipient server on how to handle the email based on the policy defined.

Type of policies:

- **none**
- **quarantine**  - Send to spam
- **reject** - Block the email

Sample DMARC Record:

`v=DMARC1; p=quarantine; rua=mailto:postmaster@website.com`

- `v=DMARC1`: The version of DMARC (required)
- `p=quarantine` The DMARC policy (quarantine = move to the spam folder)
- `rua=mailto:postmaster@website.com` An optional tag. In this case, aggregate reports will be sent to the email specified

DMARC also generates reports to provide visibility.

### Secure/Multipurpose Internet Mail Extensions (S/MIME)

It is a standard protocol for sending digitally signed and encrypted messages.

Two main components of S/MIME are:

- **Digital Signatures**: 
	- Authentication: Confirms the sender's identity through their digital certificate (Public Key)
	- Non-repudiation: Ensures the sender cannot deny sending the message
	- Data Integrity: Detects any changes to the messages after signed.
- **Encryption:** 
	- Confidentiality: Keeps the content private and readable only to intended recipients
	- Data Integrity: Detects any changes during message transaction.

## Technical Defenses

Modern email systems have various methods to detect and block phishing messages before they reach the users:

- **Email Filtering**: Filters the emails based on IP and domain reputation
- **Secure Email Gateways**: Scans the messages to detect impersonation attempts, spoofing and other phishing techniques which other filters might miss.
- **Link Rewriting**: Replaces the suspicious or unknown URLs with safe links which scans and verifies the link upon clicking. E.g. Microsoft Safelinks.
- **Sandboxing**: Tests the URLs or attachments in a isolated and safe environment to check for any malicious behaviour.


