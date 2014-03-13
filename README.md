There are an increasing number of projects working on next generation secure email or email-like communication. This is an initial draft report highlighting the projects and comparing the approaches. Please help us fill in the missing details and correct any inaccuracies. To contribute to this document, fork this repository and issue a pull request.

Contents:

1. [Common Problems](#common-problems)
  1. [Key Management](#key-management)
  1. [Metadata Protection](#metadata-protection)
  1. [Forward Secrecy](#forward-secrecy)
  1. [Data Availability](#data-availability)
  1. [Secure Authentication](#secure-authentication)
1. [Web Mail](#web-mail)
  1. [Mega](#mega)
  1. [PrivateSky](#privatesky)
  1. [Scramble](#scramble)
  1. [Whiteout](#whiteout)
1. [Browser Extensions](#browser-extensions)
  1. [Mailvelope](#mailvelope)
1. [Mail Clients](#mail-clients)
  1. [Bitmail](#bitmail)
  1. [Mailpile](#mailpile)
  1. [Parley](#parley)
1. [Self-Hosted Email](#self-hosted-email)
  1. [Dark Mail Alliance](#self-hosted-dark-mail)
  1. [FreedomBox](#freedombox)
  1. [Mailpile](#self-hosted-mailpile)
  1. [kinko](#kinko)
1. [Email Infrastructure](#email-infrastructure)
  1. [Dark Mail Alliance](#dark-mail-alliance)
  1. [LEAP Encryption Access Project](#leap)
1. [Post-email alternatives](#post-email-alternatives)
  1. [Bitmessage](#bitmessage)
  1. [Cables](#cables)
  1. [Dark Mail Alliance](#p2p-dark-mail-alliance)
  1. [FlowingMail](#flowingmail)
  1. [Goldbug](#goldbug)
  1. [Pond](#pond)
1. [Unclassified](#unclassified)
  1. [Startmail](#startmail)
1. [Related Works](#related-works)

<a name="common-problems"></a>Common Problems
===========================================================

All of the technologies listed here face a common set of problems when trying to make email (or email-like communication) secure and easy to use. These problems are hard, and have defied easy solutions, because there are no quick technological fixes: at issue is the complex interaction between user experience, real world infrastructure, and security. Although no consensus has yet emerged on how best to tackle any of these problems, the diversity of projects listed in this report reflect an surge of interest in this area and an encouraging spirit of experimentation.

<a name="key-management"></a>Key Management
-----------------------------------------------------------

All the projects in this report use public-key encryption to allow a user to send a confidential message to the intendant recipient, and for the recipient to verify the authorship of the message. Unfortunately, public-key encryption is notoriously difficult to use properly, even for advanced users. The very concepts are confusing for most users: public key versus private key, key signing, key revocation, signing keys versus encryption keys, bit length, and so on.

Traditionally, public key cryptography for email has relied on either the X.509 Certificate Authority (CA) system or a decentralized "Web of Trust" (WoT) for key validation (authenticating that a particular person owns a particular key). Recently, both schemes have come under intense criticism. Repeated security lapses at many of the Certificate Authorities have revealed serious flaws in the CA system. On the other hand, in an age where we better understand the power of social network analysis and the sensitivity of the social graph, the exposure of metadata by a "Web of Trust" is no longer acceptable from a security standpoint.

This is where we are now: we have public key technology that is excessively difficult for the common user, and our only methods of key validation have fallen into disrepute. The projects listed here have plunged into this void, attempting to simplify the usage of public-key cryptography. These efforts have four elements:

* Key discovery: There is no commonly used standard for discovering the public key attached to a particular email address. All the projects here that use OpenPGP intend to initially use, as a stop-gap measure, the OpenPGP keyservers for key discovery, although the keyserver infrastructure was not designed to be used in this way.
* Key validation: If not Certificate Authorities or Web of Trust, what then? Nearly every project here uses Trust On First Use (TOFU) in one way or another. With TOFU, a key is assumed to be the right key the first time it is used. TOFU can work well for long term associations and for people who are not being targeted for attack, but its security relies on the security of the discovery transport and the application's ability to retain a memory of discovered keys. TOFU can break down in many real-world situations where a user might need to generate new keys or securely communicate with a new contact. The projects here are experimenting with TOFU in different ways, and these problems can likely be mitigated by combining TOFU with other measures.
* Key availability: Almost every attempt to solve the key validation problem turns into a key availability problem, because once you have validated a public key, you need to make sure that this validation is available to the user on all the possible devices they might want to send or receive messages on.
* Key revocation: What happens when a private key is lost, and a user want to issue a new public key? None of the projects in this report have an answer for how to deal with this in a post-CA and post-WoT world.

<a name="metadata-protection"></a>Metadata Protection
-----------------------------------------------------------

Traditional schemes for secure email have left metadata exposed. We now know that metadata is often more sensitive than message content: metadata is structured data, easily stored forever, and subject to powerful techniques of social network analysis that can can be incredibly revealing.

Metadata protection, however, is **hard**. In order to protect metadata, the message routing protocol must hide the sender and recipient from all the intermediaries responsible for relaying the message. This is not possible with the traditional protocol for email transport, although it will probably be possible to piggyback additional (non-backward compatible) protocols on top of traditional email transport in order to achieve metadata protection.

Alternately, some projects reject traditional email transport entirely. These decentralized peer-to-peer approaches to metadata protection generally fall into four camps: (1) directly relay the message from sender's device to recipients device; (2) relay messages through a network of friends; (3) broadcast messages to everyone; (4) relay messages through an anonymization network such as Tor. The first two approaches protect metadata, but at the expense of increasing vulnerability to traffic analysis that could reveal the same metadata. The third solution faces serious problems of scalability. Pond uses the fourth method, discussed below.

All schemes for metadata protection face the prospect of increasing Spam (since one of the primary methods used to prevent Spam is analysis of metadata). This is why some schemes with strong metadata protection make it impossible to send or receive messages to anyone you are not already in contact with. This works brilliantly for reducing Spam, but is unlikely to be a viable long term strategy for entirely replacing the utility of email.

<a name="forward-secrecy"></a>Forward Secrecy
-----------------------------------------------------------

Forward secrecy is a security property that prevents an attacker from saving messages today and then later decrypting these messages once they have captured the user's private key. Without forward secrecy, an attacker might also be able to capture messages today and simply wait for computers to become powerful enough to crack the encryption by brute force. Traditional email encryption offers no forward secrecy.

All methods for forward secrecy involve a process where two parties negotiate an ephemeral key that is used for a short period of time to secure their communication. In many cases, the ephemeral key is generated anew for every single message. Traditional schemes for forward secrecy are incompatible with the asynchronous nature of email communication, since with email you still need to be able to send someone a message even if they are not online and ephemeral key generation requires a back and forth exchange between both parties.

There are several new experimental (and tricky) protocols that attempt to achieve both forward secrecy and support for asynchronous communication, but none have yet emerged as a standard. Another possible approach is to use traditional encryption with no support for forward secrecy but instead rely on a scheme for automatic key discovery and validation in order to frequently rotate keys. This way, a user could throw away their private key every few days, achieving a very crude form of forward secrecy.

<a name="data-availability"></a>Data Availability
-----------------------------------------------------------

Users today demand data availability: they want to be able to access their messages and send messages from any device they choose, wherever they choose, and whenever they choose. Most importantly, they don't want the lose of any particular device to result in a loss of all their data. For insecure communication, achieving data availability is dead simple: simply store everything in the cloud. For secure communication, however, we have no proven solutions to this problem. As noted above, the key management problem is also really a data availability problem.

Most of the projects here have postponed dealing with the data availability problem. A few have used IMAP to synchronize data or developed their own secure synchronization protocol.

<a name="secure-authentication"></a>Secure Authentication
-----------------------------------------------------------

For those projects that make use of a service provider, one of the key problems is how to authenticate securely with the service provider without revealing the password (since the password is probably also used to encrypt the private key and other secure storage, so it is important that the service provider does not have cleartext access as with typical password authentication schemes). The possible schemes include:

* Separate passwords. The application can use one password for authentication and a separate password for securing secrets.
* Pre-hash the password on the client before sending it to the server. This method can work, although it does not also authenticate the server (an impostor server can always reply with a success message), and is still vulnerable to brute force dictionary attacks.
* Use Secure Remote Password (SRP), a type of cryptographic zero-knowledge proof designed for password authentication in which the client and server mutually authenticate. SRP has been around a while, and is fairly well analyzed, but it is still vulnerable to brute force dictionary attacks (albeit much less than traditionally password schemes).
* Sign a challenge from the server with the user's private key. This has the advantage of being nearly impossible to brute force attack, but is vulnerable to impostor server providers and requires that the user's device has the private key.

No consensus or standard has yet emerged, although SRP has been around a while.

<a name="web-mail"></a>Web Mail
===========================================================

Most users are familiar with web-based email and the incredible convenience it offers: you can access your email from any device, and you don't need to worry about data synchronization. Developers of web-based email faces several difficult challenges when attempting to make a truly secure application. These challenges can be overcome, but not easily.

First, because the web application is loaded from the web server each time you use it, the service provider could be targeting a version of the client to you that includes a backdoor. To overcome this vulnerability, it is possible to load the code for the web application from a third party. There are two ways of doing this:

1. App Store: Most web browsers support special extensions in the form of "Browser Applications". These are loaded from some kind of app store and installed on the user's device. In this case, the third party that provides the application is the app store. Therefore, the user is then relying on the app store to furnish them with a secure version of the app. For example, this is the approach taken by [cryptocat](https://crypto.cat).
2. Third Party: There are two advanced mechanisms to allow a web application to be loaded from one website and allow it to access data from another website. One is called CORS ([Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)) and the other is HTML5's [window.postMessage command](https://developer.mozilla.org/en-US/docs/Web/API/window.postMessage). With either method, anyone can be the third party furnishing the application, or it can be self hosted. For example, this is the approach taken by [Unhosted](https://unhosted.org).

Second, even if the application is loaded from a trusted third party, web browsers are not an ideal environment for sensitive data: there are many ways for an in-browser application to leak data and web browsers are notoriously prone to security holes (it is a very difficult problem to be able to run untrusted code locally in a secure sandbox). To their credit, the browser developers are often vigilant about fixing these holes (depending on who you ask), but the browser environment is far from a secure computing environment. It continues to be, however, the most convenient environment.

Third, developers of web-based secure email face an additional challenge when dealing with offline data or data caching. Modern HTML5 apps typically store a lot of data locally on the user's device using the localStorage facility. Currently, however, no browser stores this encrypted. A secure web-based email application must either choose to not support any local storage, or develop a scheme for individually encrypting each object put in localStorage, a process which is very inefficient. Even storing keys temporarily in short lived session storage is problematic, since these can be easily read from disk later.

<a name="mega"></a>Mega
-----------------------------------------------------------

[mega.co.nz](https://mega.co.nz)

The relaunch of Mega has featured client-side encryption via a javascript application running in the browser. Mega has announced plans to extend their offerings to include email service with a similar design. No details are yet forthcoming. In interviews, Mega has said the javascript running in the browser will be open source, but the server component will be proprietary.

<a name="privatesky"></a>PrivateSky
-----------------------------------------------------------

PrivateSky was a secure web-based email service that chose to shut down because their design was not compatible with UK law. Many in the press have [said GCHQ forced the closure](http://www.ibtimes.co.uk/articles/529392/20131211/gchq-forced-privatesky-secure-email-service-offline.htm), which the [company refutes](http://www.certivox.com/blog/bid/359788/The-real-story-on-the-PrivateSky-takedown).

<a name="scramble"></a>Scramble
-----------------------------------------------------------

[scramble.io](https://scramble.io)

Scramble is a OpenPGP email application that can be loaded from a website (with plans to add app store support). Additionally, you can sign up for email service from scramble.io.

**Keys:** Private keys are generated in the browser app, encrypted with the user's passphrase, and then stored on the server. The server never sees the user's passphrase (password is hashed using scrypt before sent to the server during account creation and authentication). The master storage secret (symmetric key) used to encrypt keys is stored in the browser's sessionStorage, which is erased when the user logs out. Keys are validated using notaries.

**Infrastructure:** Scramble uses a system of network perspectives to discover and validate public keys. The client will come with a list of pre-blessed notaries that can be used to query for public keys. If the notaries agree, the client will consider the key to be validated.

**Application:** Currently, Scramble is a traditional HTML5 javascript application loaded from the website. In the future, Scramble will also be an installable browser app.

* Written in: Go, Javascript
* Source code: https://github.com/dcposch/scramble
* Design documentation: https://github.com/dcposch/scramble/wiki/Scramble-Protocol
* License: LGPL
* Platforms: Windows, Mac, Linux (with Android planned).

<a name="whiteout"></a>Whiteout
-----------------------------------------------------------

[whiteout.io](https://whiteout.io)

Whiteout is a commercial service featuring an HTML5-based OpenPGP email client that is loaded from the web.

* Written in: Javascript
* Source code: https://github.com/whiteout-io/mail-html5
* License: proprietary, but the code is available for inspection.

<a name="browser-extensions"></a>Browser Extensions
===========================================================

A browser extension modifies the behavior of the web browser (not to be confused with a browser application, which has far fewer permissions and consists of a self-contained application). Browser extensions are able to modify how the users interacts with a variety of websites. Browser extensions share many of the same advantages and disadvantages of [web mail approaches](#webmail).

<a name="mailvelope"></a>Mailvelope
-----------------------------------------------------------

[mailvelope.com](http://mailvelope.com)

Mailvelope is a browser extension that allows you to use OpenPGP email with traditional web-mail providers like Gmail, Yahoo, and Outlook.com.

**Keys:** The private key is generated for you, password protected, and stored in the browser's local storage (along with public keys). In the future, the plan is to support automatic discovery and validation of public keys using OpenPGP keyservers and message footers.

**Application:** When the extension detects you have opened a web page from a supported web-mail provider such as Gmail, it offers the user the opportunity to encrypt what you type in the compose window and decrypt messages you receive.

**Limitations:** Because of an inherent limitation in the way Mailvelope can interface with web-mail, it is not able to send OpenPGP/MIME (although it can read it fine). As mentioned elsewhere, browser storage is not a particular ideal place to be storing keys. When a web-mail provider changes their UI (or API if they happen to have one), the extension must be updated to handle the new format.

* Contact: info@mailvelope.com
* Written in: Javascript
* Source code: https://github.com/toberndo/mailvelope
* Design documentation: http://www.mailvelope.com/help
* License: AGPL
* Platforms: Windows, Mac, Linux (with Android planned).

<a name="mail-clients"></a>Mail Clients
===========================================================

An email client, or MUA (Mail User Agent), provides a user interface to access email from any service provider. Traditional examples of email clients include Thunderbird or Microsoft Outlook (although both these application include a lot of other functionality as well). Nearly all email clients communicate with the email service provider using IMAP or POP and SMTP, although some also support local mailboxes in mbox or Maildir format.

There are two primary advantages to the mail client approach:

1. Existing accounts: By using a custom secure mail client, a user can continue to use their existing email accounts.
1. Tailored UI: A custom client has the potential to rethink the email user experience in order to better convey security related details to the user.

The mail client approach, however, also has several disadvantages:

1. Insecure service providers: A mail client cannot address many of the core problems with email security when used with a traditional email provider. For example, metadata will not be protected in storage or transit, and the provider cannot aid in key discovery or validation. Most importantly, many existing mail providers are highly vulnerable, since few rely on DNSSEC for their MX records or validate their StartTLS connections for mail relay (when they even bother to enable StartTLS). A traditional email provider also requires authentication via password that is seen by the provider in clear text, and might be recorded by them. Finally, most service providers retain significant personally identifiable information, such as IP address of clients.
1. Install a new app: As with many of the other approaches, the custom mail client approach requires that users download and install a specialized application on their device before they can use it.

Ultimately, the level of email security that is possible with the custom mail client approach will always be limited. However, custom email clients may be an excellent strategy for gradually weening users away from email and to a different and more secure protocol. Most of the projects in this section see email support as a gateway to ease the transition to something that can replace email.

<a name="bitmail"></a>Bitmail
-----------------------------------------------------------

[bitmail.sf.net](http://bitmail.sf.net)

Bitmail is a desktop application that provides a user interface for traditional IMAP-based mail, but also supports a custom peer-to-peer protocol for relaying email through a network of friends. Bitmail will support both OpenPGP and S/MIME.

**Keys:** Keys are validated using a shared secret or fingerprint validation. Public keys are discovered over the p2p network. Keys are stored locally in an encrypted database.

**Routing:** Bitmail uses an "echo protocol" for p2p message routing, where every message is sent to every neighbor.

**Application:** Bitmail uses the Qt library for cross platform UI.

There are also plans to include a Bitmail MUA extension.

* Written in: C
* Source code: http://sourceforge.net/projects/bitmail/files
* Design documentation: http://sourceforge.net/p/bitmail/code/HEAD/tree/branches/BitMail.06.2088_2013-11-03/BitMail/branches/BitMail/Documentation/
* License: GPL v2
* Platforms: Windows (with Mac and Linux planned).

Note: I am unclear which of the previous features are planned and which are currently working.

<a name="mailpile"></a>Mailpile
-----------------------------------------------------------

[mailpile.is](http://mailpile.is)

Mailpile is an email client designed to quickly handle large amounts of email and also support user-friendly encryption. The initial focus is on email, with plans to eventually support post-email protocols like bitmessage, flowingmail, or darkmail. Also, the developers hope to add support for XMPP-based chat in the future. Since the Mozilla foundation has not committed the resources necessary to keep Thunderbird contemporary, the Mailpile initiative holds a lot of promise as a cross-platform mail client that seeks to redesign how we interact with email.

**Keys:** Mailpile email encryption is based on OpenPGP (it uses your GPG keyring). Key discovery will be handled using OpenPGP keyservers and including public keys as attachments to outgoing email. Public keys are trusted on first use, with plans for validation via DANE and manual fingerprint verification (future support for a p2p protocol might include additional methods, such as Certificate Transparency or Short Authentication Strings). Currently, keys are not backed up.

**Application:** Mailpile UI is written using HTML5 and Javascript, running against a self-hosted Python application (that typically lives locally on the device, but might be running on your own server).

**Limitations:** Mailpile does not currently have a scheme for recovery if your device is destroyed or a method for securely synchronizing keys among devices. Although the search index is stored encrypted on disk (if the user already has GPG installed and a key pair generated), it is encrypted in a way that requires the index to be loaded entirely into memory. Mailpile is under very active development, so these and other issues may change in the near future.

* Written in: Python, Javascript
* Source code: http://github.com/pagekite/mailpile
* Design documentation: http://github.com/pagekite/mailpile
* License: AGPL & Apache
* Platforms: Windows, Mac, Linux (with Android and iOS planned).
* Contact: team@mailpile.is

<a name="parley"></a>Parley
-----------------------------------------------------------

[parley.co](https://parley.co)

Parley is a desktop mail client with a UI written using HTML5 and Javascript, with a local backend written in Python.

**Keys:** Although Parley can be used with any service provider, the Parley servers are used to publish public keys and back up client-encrypted private keys. For key discovery, Parley uses a central repository and the OpenPGP keyservers. For key validation, Parley relies on trust on first use and Parley key endorsement.

**Infrastructure:** All users of the Parley client also sign up for the Parley service, but they use their existing email account. The Parley server acts as a proxy that uses [context.io](http://context.io) for email storage (context.io is a commercial service that provides a REST API for a traditional IMAP account). The Parley server also handles key discovery, validation, and backup. Both the client and server are released as free software.

**Application:** Parley is currently bundled into an executable using [Appcelerator](http://www.appcelerator.com/). The Parley client does not speak IMAP or SMTP directly. Rather, uses the email REST API of context.io.

**Limitations:** All user email is stored by context.io, albeit in OpenPGP format. Metadata is exposed to context.io, however (in addition to your service provider).

* Written in: Python, Javascript
* Source code: https://github.com/blackchair/parley
* Design documentation: https://parley.co/#how-it-works
* License: BSD
* Platforms: Windows, Mac, Linux (with Android and iOS planned).

<a name="self-hosted-email"></a>Self-Hosted Email
===========================================================

Traditionally, email is a federated protocol: when you send an email it travels from your computer, to the server of your email provider, to the server of the recipient's provider, and finally to the recipient's computer. The key idea with self-hosted email is to cut out the middleman and run your own email server.

In the United States, much of the interest in self-hosted email is driven by the Supreme Court's current (and particularly odd) interpretation of the 4th amendment called the ["Third-Party Doctrine"](https://en.wikipedia.org/wiki/Third-Party_Doctrine). Essentially, you have much weaker privacy protections in the US if you entrust any of your data to a third party. Additionally, the Court has so far afforded much greater protections to items physically inside your home. "Aha!" say the hackers and the lawyers, "we will just put email in the home."

Unfortunately, it is not so simple. There are some major challenges to putting email servers in everyone's home:

* **Delegated reputation**: The current email infrastructure is essentially a system of delegated reputation. In order to be able to send mail to most providers and not have a large percentage of it marked as Spam, a service provider must gradually build up a good reputation. Users are able to send mail because their provider has cultivated this reputation and maintained it by closing abusive accounts. It is certainly possible to run an email provider with a single user, but it is much harder to build up a good reputation. Also, many email providers block all relay attempts from IP addresses that have been flagged as "home" addresses, on the (probable) assumption that the message is coming from a virus and not a legitimate email server.
* **Servers are on a hostile network**: Because a server needs to have open ports that are publicly accessible from the internet at all times, running one is much trickier than a simple desktop computer. It is much more critical to make sure security upgrades are applied in a timely manner, and that you are able to respond to external attacks, such as "Spam Bombs". Any publicly addressable IP that is put on the open internet will be continually probed for vulnerabilities. Self-hosting will probably work great for a protocol like Pond, where there are strict restrictions on who may deliver incoming messages. Email, however, is a protocol that is wide open and prone to abuse.
* **Sysadmins are not robots**: No one has yet figured out how to make self-healing servers that don't require a skilled sysadmin to keep them healthy. Once someone does, a lot of sysadmins will be out of work, but they are presently not very worried. There are many things that commonly go wrong with servers, such as upgrades failing, drives filling up, daemons crashing, memory leaks, hardware failures, and so on.
* **Does not address the important problems**: Moving the physical location of a device does nothing to solve the hard problems associated with easy-to-use email security (such as data availability and key validation). Some of the approaches to these problems rely on service provider infrastructure that would be infeasible to self host.
* **DNS is hard**: One of the important security problems with traditional email is the vulnerability MX DNS records. Doing DNS correctly is hard, and not something that can be expected of the common user.

Self-hosted email is an intriguing "legal hack", albeit one that faces many technical challenges.

<a name="self-hosted-dark-mail"></a>Dark Mail Alliance
-----------------------------------------------------------

The Dark Mail Alliance has said they want to support self-hosting for the server component of the system. No details yet.

<a name="freedombox"></a>FreedomBox
-----------------------------------------------------------

[freedomboxfoundation.org](https://freedomboxfoundation.org)

From its early conception, part of FreedomBox was "email and telecommunications that protects privacy and resists eavesdropping". Email, however, is not currently being worked on as part of FreedomBox. (as far as I can tell).

<a name="self-hosted-mailpile"></a>Mailpile
-----------------------------------------------------------

Although Mailpile is primarily a mail client, the background Python component can read the Maildir format for email. This means you could install Mailpile on your own server running a Mail Transfer Agent (MTA) like postfix or qmail. You would then access your mail remotely by connecting to your server via a web browser.

<a name="kinko"></a>kinko
-----------------------------------------------------------

[kinko](https://kinko.me) implements an en/decrypting SMTP- and IMAP-proxy on ARM-class hardware, the kinko box. Emails are synced from the users' email accounts via IMAP to the box and are stored in plaintext in a secure storage area on the box. The kinko box also includes a webmailer to be able to use email with the browser.

Connections to the kinko box are secured by TLS using a private key only known to the box itself. Furthermore, the kinko box is tunnelled to a public internet location. Consequently, users can access secure email from everywhere, using IMAP compatible email clients and/or browsers, including mobile clients.

kinko uses GnuPG for encryption, with the addition of encrypting the email subject. Further additions should allow "Post-email alternatives" (a la bitmessage) to be used with the email clients that users are using today already. Other, privacy-related additions are planned as well.

**Key discovery and validation:** Users can upload existing PGP keyrings. PGP keys are discovered via email
addresses, email content, and PGP key servers. Keys are trusted on first use (but this policy can be changed 
to explicit fingerprint validation.) 

**Project status:** An alpha prototype exists. We are preparing for the release of a beta package in Q2/2014.

**Languages:** The kinko base system is implemented in ruby and shell, with minor portions in native code.
Applications can be implemented in more or less any language. 

**Webmail:** The currently included webmail application is roundcube webmail. That might change in the future. 

**Licenses:** All portions of the kinko system will be released under the AGPL license. (Included 3rd party
applications will use their respective open source licenses). The hardware is open sourced as 
per [olimex](https://www.olimex.com/wiki/A10-OLinuXino-LIME).

<a name="email-infrastructure"></a>Email Infrastructure
===========================================================

The "infrastructure" projects give a service provider the opportunity to offer secure email accounts to end-users. By modifying how both email clients and email servers work, these projects have the potential to deploy greater security measures than are possible with a client-only approach. For example:

* Encrypted relay: A secure email provider is able to support, and enforce, encrypted transport when relaying mail to other providers. This is an important mechanism for preventing mass surveillance of metadata (which is otherwise not protected by OpenPGP client-side encryption of message contents).
* Easier key management: A secure email provider can endorse the public keys of its users, and provide assistance to various schemes for automatic validation. Additionally, a secure email provider, coupled with a custom client, can make it easy to securely manage and back up the essential private keys which are otherwise cumbersome for most users to manage.
* Invisible upgrade to better protocols: A secure email provider has the potential to support multiple protocols bound to a single user@domain address, allowing automatic and invisible upgrades to more secure post-email protocols when both parties detect the capability.
* A return to federation: The recent concentration of email to a few giant providers greatly reduces the health and resiliency of email as an open protocol, since now only a few players essentially monopolize the medium. Projects that seek to make it easier to offer secure email as a service have the potential to reverse this trend.
* Secure DNS: A secure provider can support DNSSEC and DANE, while most other email providers are unlikely to anytime soon. This is very important, because it is easy to hijack the MX records of a domain without DNSSEC.
* Minimal data retention: A service provider that follows "best practices" will choose to retain less personally identifiable information on their users, such as their home IP addresses.

The goal of both projects in this category is to build systems where the service provider is untrusted and cannot compromise the security of its users.

Despite the potential of this approach, there are several unknown factors that might limit its appeal:

* In order to benefit from a more secure provider, a user will need to switch their email account and email address, a very high barrier to adoption.
* Where once there were many ISPs that offered email service, it is no longer clear if there is either the demand to sustain many email providers or the supply of providers interested in offering email as a service.
* Users must download and install a custom application.

<a name="dark-mail-alliance"></a>Dark Mail Alliance
-----------------------------------------------------------

[darkmail.info](https://darkmail.info)

The Dark Mail Alliance will include both a client application and server software. The plan is to support traditional encrypted email (both OpenPGP and S/MIME), a new federated email-like protocol adapted from SilentCircle's instant message protocol (SCIMP), and a pure peer-to-peer messaging protocol. Both the client and server will be made available as free software.

**Keys:** Key pairs will be generated on the user's device and uploaded to the service provider. [Certificate Transparency](http://certificate-transparency.org) will be used to automatically validate the service provider's endorsement of these public keys. Dark Mail additionally plans to support fingerprint confirmation, short authentication strings, and shared secret for manual key validation. Automatic discovery of public keys will happen using DNS, HTTPS, and via the messages themselves.

**Routing:** The post-email messaging protocol promises to have forward secrecy and protection from metadata analysis (details have not yet been posted, and SCIMP does not currently support meta-data protection). Dark Mail Alliance plans to additionally support pure peer-to-peer messaging using a key fingerprint as the user identifier.

**Infrastructure:** Dark Mail plans to support three types of architectures: traditional client/server, self-hosted, and pure peer-to-peer. No details yet on how these will work.

**Application:** The client application will work with any existing MUA by exposing a local IMAP/SMTP server that the MUA can connect to.

**Limitations:** Dark Mail has not yet released any code or design documents. However, they certainly have the resources to carry out their plans.

* Written in: C
* Source code: none yet
* Design documentation: none yet
* License: planned to be OSI-compatible
* Platforms: initially Android and iOS, followed by Windows, OS X, Linux, and Windows Phone.
* Contact: press@darkmail.info

<a name="leap"></a>LEAP Encryption Access Project
-----------------------------------------------------------

[leap.se](https://leap.se)

LEAP includes both a client application and turn-key system to automate the process of running a secure service provider. Currently, this includes user registration and management, help tickets, billing, VPN service, and secure email service. The secure email service is based on OpenPGP.

**Keys:** Key pairs are generated on the user's device. Keys, and all user data, are stored in a client-encrypted database that is synchronized among the user's devices and backed up to the service provider. Keys are automatically validated using a combination of provider endorsement and network perspective (coming soon). Keys are discovered via the OpenPGP keyservers, the OpenPGP header, email footers, and a custom HTTP-based discovery protocol.

**Infrastructure:** LEAP follows a traditional federated client/server architecture. The client is designed to work with any LEAP-compatible service provider (with plans to support legacy IMAP providers in the future). For security reasons, users are encouraged to get the application from LEAP and not their service provider.

**Application:** The client application works with any existing MUA by exposing a local IMAP/SMTP server that the MUA can connect to. There is a Thunderbird extension to automate configuration of the account in Thunderbird. The client application communicates with the service provider using a custom protocol for synchronizing encrypted databases. The application is a very small C program that launches the Python code. The user interface is written using Qt.

**Limitations:** In the current implementation, security properties of forward secrecy and metadata production are not end-to-end. Instead, the client relies on the service provider to ensure these properties. This limitation is due to some inherent limitations in the existing protocols for secure email. As with many of the other projects, LEAP's plan is to invisibly upgrade to a post-email protocol when possible in order to overcome these limitations.

* Written in: Python
* Source code: https://leap.se/source
* Design documentation: https://leap.se/docs
* License: mostly GPL v3, some MIT and AGPL.

<a name="post-email-alternatives"></a>Post-email alternatives
===========================================================

There are several projects to create alternatives to email that are more secure yet still email-like.

These projects share some common advantages:

1. **Trust no one:** These projects share an approach that treats the network, and all parties on the network, as potentially hostile and not to be trusted. With this approach, a user's security can only be betrayed if their own device is compromised or the software is flawed or tampered with, but the user is protected from attacks against any service provider (because there typically is not one).
1. **Fingerprint as identifier:** All these projects also use the fingerprint of the user's public key as the unique routing identifier for a user, allowing for decentralized and unique names. This neatly solves the problem of validating public keys, because every identifier basically *is* a key, so there is no need to establish a mapping from an identifier to a key.

Except for Pond, all these alternatives take a pure peer-to-peer approach. As such, they face particular challenges:

1. **The "Natural" Network**: Many advocates of peer-to-peer networking advance the notion that decentralized networks are the most efficient networks and are found everywhere in nature (in the neurons in our brain, in how mold grows, in how insects communicate, etc). This notion is only partially true. Networks are found in nature, but these network are not radically decentralized. Instead, natural networks tend to follow a power law distribution (aka "[scale free networks](https://en.wikipedia.org/wiki/Scale-free_network)"), where there is a high degree of partial centralization that balances "brokerage" (ability to communicate far in the network) with "closure" (ability to communicate close in the network). Thus, in practice, digital networks rely on "super hubs" that process most of the traffic. These hubs need to be maintained and hosted by someone, often at great expense (and making the network much more vulnerable to Sybil attacks).
1. **The Internet:** Sadly, the physical internet infrastructure is actually very polycentric rather than decentralized (more akin to a tree than a spider's web). One reason for the rise of cloud computing is that resources are much cheaper near the core of the internet than near the periphery. Technical strategies that attempt to leverage the periphery will always be disadvantaged from an efficiency standpoint.
1. **Traffic Analysis:** Most of the peer-to-peer approaches directly relay messages from sender's device to recipient's device, or route messages through the participant's contacts. Such an approach to message routing makes it potentially very easy for a network observer to map the network of associations, even if the message protocol otherwise offers very strong metadata protection.
1. **Sybil Attacks:** By their nature, peer-to-peer networks do not have a method of blocking participation in the network. This makes them potentially very vulnerable to [Sybil attacks](https://en.wikipedia.org/wiki/Sybil_attack), where an attacker creates a very large number of fake participants in order to control the network or reveal the identity of other network participants.
1. **Mobile:** Peer-to-peer networks are resource intensive, typically with every node in the network responsible for continually relaying traffic and keeping the network healthy. Unfortunately, this kind of thing is murder on the battery life of a mobile device, and requires a lot of extra network traffic.
1. **Identifiers**: Using key fingerprints as unique identifiers has some advantages, but it also makes user identifiers impossible to remember. There is a lot of utility in the convenience of memorable username handles, as evidence in the use of email addresses and twitter handles.
1. **Data Availability**: Unless also paired with a cloud component, peer-to-peer networks have much lower data availability than other approaches. For example, it takes much longer to update message deliveries from a peer network than from a server, particularly when the device has been offline for a while. Also, if a device is lost or destroyed, generally the user loses all their data.

Most of these challenges have possible technological solutions that might make peer-to-peer approaches the most attractive option in the long run. For example, researchers may discover ways to make p2p networks less battery intensive. For this reason, it is important that research continue in this area. However, [in the long run we are all dead](https://en.wikiquote.org/wiki/John_Maynard_Keynes) and peer-to-peer approaches face serious hurdles before they can achieve the kind of user experience demanded today.

<a name="bitmessage"></a>Bitmessage
-----------------------------------------------------------

[Bitmessage](https://bitmessage.org)

Bitmessage is a peer-to-peer email-like communication protocol. It is totally decentralized and places no trust on any organization for services or validation.

Advantages:

* resistant to metadata analysis
* relatively easy to use
* works and is actively used by many people.

Disadvantages:

* no forward secrecy
* unsolved scaling issues: all messages are broadcast to everyone
* because there is no forward secrecy, it is especially problematic that anyone can grab an encrypted copy of any message in the system. This means if the private key is ever compromised, then all the past messages can be decrypted easily by anyone using the system.
* relies on proof of work for spam prevention, which is probably not actually that preventative (spammers often steal CPU anyway).

<a name="cables"></a>Cables
-----------------------------------------------------------

https://github.com/mkdesu/cables

* Written in: C, Bash
* License: GPL v2

<a name="p2p-dark-mail-alliance"></a>Dark Mail Alliance
-----------------------------------------------------------

The Dark Mail Alliance plans to incorporate traditional email, a federated email alternative, and a second email alternative that is pure peer-to-peer. Details are not yet forthwith.

<a name="flowingmail"></a>FlowingMail
-----------------------------------------------------------

[FlowingMail](http://flowingmail.com)

P2P secure, encrypted email system.

<a name="goldbug"></a>Goldbug
-----------------------------------------------------------

http://goldbug.sf.net

* Written in: C++, Qt
* License: BSD

<a name="pond"></a>Pond
-----------------------------------------------------------

[pond.imperialviolet.org](https://pond.imperialviolet.org/)

Pond is an email-like messaging application with several unique architectural and cryptographic features that make it stand out in the field.

**Message Encryption**: Pond uses [Axolotl](https://github.com/trevp/axolotl/wiki) for asynchronous forward secret messages where the key is frequently ratcheted (akin to OTR, but more robust).

**Routing**: Pond uses a unique architecture where every user relies on a service provider for receiving messages, but sent messages are delivered directly to the recipient's server (over Tor). This allows for strong metadata protection, but does not suffer from the other problems that peer-to-peer systems typically do. In order to prevent excessive Spam under this scheme, Pond uses a clever system of group signatures to allow the server to check if a sender is authorized to deliver to a particular user without leaking any information to the server.

**Keys**: Pond uses Panda, a system for secure peer validation using short authentication strings.

Pond's advantages include:

* Very high security: forward secrecy, metadata protection, resistant to traffic analysis.
* Pond hybrid federated and peer-to-peer approach is cool and holds a lot of promise.
* Written in Go, and thus probably has many fewer security flaws than programs written in C or C++.
* Pond is written by Adam Langley, an extremely well respected crypto-engineer.

Pond's disadvantages include:

* Uses non-human memorial unique identifiers, although this is not a necessary element of the design.
* Requires that you set up contacts in advance before you can communicate with them (via a Short Authentication String or full public key exchange).
* Pond is still very difficult to install and use.

Pond is an exciting experiment in how you could build a very secure post-email protocol. Although Pond currently uses non-human identifiers, Pond could be easily modified to use traditional email username@domain.org identifiers (because it relies on service providers for message reception). The requirement in Pond that both parties pre-exchange keys could also be modified to allow users to set up addresses that could receive messages from anyone, albeit at the cost of likely Spam. Currently, Pond uses Tor to anonymize message routing, but the Tor network was designed for low-latency. Pond could potentially use a more secure anonymization network that was designed for higher-latency asynchronous messages.

Ultimately, Pond's unique design makes it a very strong candidate for incorporation into a messaging application that could automatically upgrade from email to Pond should it detect that both parties support it.

* Written in: Go
* Source code: https://github.com/agl/pond
* License: BSD
* Platforms: anything you can compile Go on (for command line interface) or anything you can compile Go + Gtk (for GUI interface).

<a name="unclassified"></a>Unclassified
===========================================================

<a name="startmail"></a>Startmail
-----------------------------------------------------------

[startmail.com](http://startmail.com)

The makers of the secure search engine [startpage.com](https://startpage.com) have announced they will be providing secure email service. No details are available yet, other than the service will be compatible with OpenPGP email.

<a name="related-works"></a>Related Works
===========================================================

There are many technologies that don't belong in this document because they either (a) are not trying to make encrypted email-like communication easier, (b) use some kind of weird proprietary escrow system, or (c) we just don't know enough about them yet. Here is a place to store links to such projects.

* [Virtru](https://www.virtru.com) has a secure email product that relies on a centralized key escrow. For details, see [here](http://www.theregister.co.uk/Print/2014/01/24/ex_nsa_cloud_guru_email_privacy_startup) and [here](https://www.virtru.com/how-virtru-works).
* [OpenCom](http://opencom.io) is a secure email and email-like communication in the planning stages.
* [Ubiquitous Encrypted Email](https://github.com/tomrittervg/uee) is a protocol draft for standards that could lead to universal adoption of encrypted email.