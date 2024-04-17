# SMTP General information

SMTP is defined by the IETF on [RFC 2821](https://www.ietf.org/rfc/rfc2821.txt)

>**NOTE:** The Internet Engineering Task Force (IETF), founded in 1986, is the premier standards development organization (SDO) for the Internet. The IETF (Internet Engineering Task Force) publishes its technical documentation as RFCs, an acronym for their historical title *Requests for Comments*. They define the Internet's technical foundations, such as addressing, routing and transport technologies. They recommend operational best practice and specify application protocols that are used to deliver services used by billions of people every day.
>
> RFCs are freely available and software developers, hardware manufacturers, and network operators around the world voluntarily implement and adopt the technical specifications described by RFCs.
>
> RFCs are sequentially numbered, starting with RFC 1 published in 1969 (the RFC series predates the IETF). Today, there are more than 9000 individually numbered documents in the series. 

Basic SMTP structure (from RFC 2821):

```
               +----------+                +----------+
   +------+    |          |                |          |
   | User |<-->|          |      SMTP      |          |
   +------+    |  Client- |Commands/Replies| Server-  |
   +------+    |   SMTP   |<-------------->|    SMTP  |    +------+
   | File |<-->|          |    and Mail    |          |<-->| File |
   |System|    |          |                |          |    |System|
   +------+    +----------+                +----------+    +------+
                SMTP client                SMTP server

```


# SMTP Ports

- Port 25
- Port 587
- Port 465
- Port 2525

Port 25 was initially designed for mail transfer, not submission. Hence ISPs often block this port by default.

User-level email clients typically use SMTP only for sending messages to a mail server for relaying, and typically submit outgoing email to the mail server on port 587 or 465 per RFC 8314

SMTP servers commonly **but not systematically** use the Transmission Control Protocol on port number 25 (for plaintext) and 587 (for encrypted communications).

Email is submitted by a mail client (mail user agent, MUA) to a mail server (mail submission agent, MSA) using SMTP on TCP port 587. Most mailbox providers still allow submission on traditional port 25.

Ports
Communication between mail servers generally uses the standard TCP port 25 designated for SMTP.

Mail clients however generally don't use this, instead using specific "submission" ports. Mail services generally accept email submission from clients on one of:

- 587 (Submission), as formalized in RFC 6409 (previously RFC 2476)
- 465 This port was deprecated after RFC 2487, until the issue of RFC 8314.
- Port 2525 and others may be used by some individual providers, but have never been officially supported.

Many Internet service providers now block all outgoing port 25 traffic from their customers. Mainly as an anti-spam measure, but also to cure for the higher cost they have when leaving it open, perhaps by charging more from the few customers that require it open.

The port that uses StartTLS most often is port 587. It often requires email clients to use StartTLS to send mail.
Port 465 is the second most commonly used port for StartTLS.
Other ports used to send encrypted mail are 25, and 2525.

Port 587 is the well-known port for submitting mail to a server, frequently (but not required to be) encrypted using STARTTLS. Some email service providers allow their customers to use the SMTPS protocol to access a TLS-encrypted version of the "submission" service on port 465.

>**NOTE**: In early 1997, the Internet Assigned Numbers Authority registered port 465 for smtps. Late 1998 this was revoked when STARTTLS was standardized. With STARTTLS, the same port can be used with or without TLS. The use of well-known ports for mail exchanges communicating with SMTP was discussed in particular at the time.
> 
> Source: [Wikipedia / SMTPS](https://en.wikipedia.org/wiki/SMTPS)

# StartTLS

## Introduction

While webmail obeys the earlier HTTP disposition of having separate ports for encrypt and plain text sessions, mail protocols use the STARTTLS technique, thereby allowing encryption to start on an already established TCP connection. While RFC 2595 used to discourage the use of the previously established ports 995 and 993, RFC 8314 promotes the use of implicit TLS when available.

[Source - Wikipedia - Email client port numbers](https://en.wikipedia.org/wiki/Email_client#Port_numbers)


## Definition

SMTP is not secured by default, which means that if you were to send email over SMTP without StartTLS the email could be intercepted and easily interpreted. This is especially worrisome when sending sensitive, personal information like usernames, passwords, or bank information. 

Without StartTLS, your personal information is at risk of being stolen. 

STARTTLS is used with SMTP, on any ports.

There are 2 definitions of TLS strategies:

- Opportunistic TLS

- Enforced TLS


**Opportunistic TLS** (also called *Explicit TLS*) allows the email client to deliver on the highest encryption level the target server accepts. If the target server does not accept TLS, the email client will use an unencrypted connection => we use the same port for both encrypted and plain text email.

**Enforced TLS** (also called *Implicit TLS* ) requires the mail to be sent over a secure connection. If the connection is not encrypted, the mail will be blocked from sending => much more secured ! But can drop some e-mails if the client or the sending party does not issue a STARTTLS command.



## Testing StartTLS

To see if the target SMTP server supports STARTTLS, you can simply use Telnet with the "EHLO" command (stands for Extended HELO):

```batch
$ telnet smtp.contoso.ca 25
Trying 10.13.12.45...
Connected to smtp.contoso.ca.
Escape character is '^]'.
220 SG ESMTP service ready at ismtpd0017p1las1.sendgrid.net
EHLO
250-smtp.sendgrid.net
250-8BITMIME
250-PIPELINING
250-SIZE 31457280
250-STARTTLS
250-AUTH PLAIN LOGIN
250 AUTH=PLAIN LOGIN
```

To manually use STARTTLS, you must use another command or utility such as OpenSSL.exe like the following examples:

```batch
openssl s_client -starttls smtp -4 -connect smtp.contoso.ca:587 -crlf -ign_eof
```

> Note: the -4 switch is used above to force IPv4 protocol (instead of ipv6)



