There are an increasing number of projects working on next generation secure email or email-like communication. This is an initial draft report highlighting the projects and comparing the approaches. Please help us fill in the missing details and correct any inaccuracies. To contribute to this document, please fork this repository and issue a pull request.

Web Services
============================================

Most users are familiar with web-based email and the incredible convenience it offers: you can access your email from any device, and you don't need to worry about data synchronization. Developers of web-based email faces several difficult challenges when attempting to make a truly secure application. These challenges can be overcome, but not easily.

First, because the web application is loaded from the web server each time you use it, the service provider could be targeting a version of the client to you that includes a backdoor. To overcome this vulnerability, it is possible to load the code for the web application from a third party. There are two ways of doing this:

1. App Store: Most web browsers support special extensions in the form of "Browser Applications". These are loaded from some kind of app store and installed on the user's device. In this case, the third party that provides the application is the app store. Therefore, the user is then relying on the app store to furnish them with a secure version of the app. For example, this is the approach taken by [cryptocat](https://crypto.cat).
2. Third Party: There are two advanced mechanisms to allow a web application to be loaded from one website and allow it to access data from another website. One is called CORS ([Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)) and the other is HTML5's [window.postMessage command](https://developer.mozilla.org/en-US/docs/Web/API/window.postMessage). With either method, anyone can be the third party furnishing the application, or it can be self hosted. For example, this is the approach taken by [Unhosted](https://unhosted.org).

Second, even if the application is loaded from a trusted third party, web browsers are not an ideal environment for sensitive data: there are many ways for an in-browser application to leak data and web browsers are notoriously prone to security holes (it is a very difficult problem to be able to run untrusted code locally in a secure sandbox). To their credit, the browser developers are often vigilant about fixing these holes (depending on who you ask), but the browser environment is far from a secure computing environment. It continues to be, however, the most convenient environment.

Third, developers of web-based secure email face an additional challenge when dealing with offline data or data caching. Modern HTML5 apps typically store a lot of data locally on the user's device using the localStorage facility. Currently, however, no browser stores this encrypted. A secure web-based email application must either choose to not support any local storage, of develop a scheme for individually encrypting each object put in localStorage, a process which is very inefficient. Even storing keys temporarily in short lived session storage is problematic, since these can be easily read from disk later.

Mega
--------------------------------------------

[mega.co.nz](https://mega.co.nz)

The relaunch of Mega has featured client-side encryption via a javascript application running in the browser. Mega has announced plans to extend their offerings to include email service with a similar design. No details are yet forthcoming. In interviews, Mega has said the javascript running in the browser will be open source, but the server component will be proprietary.

PrivateSky
--------------------------------------------

PrivateSky was a secure web-based email service that chose to shut down because their design was not compatible with UK law. Many in the press have [said GCHQ forced the closure](http://www.ibtimes.co.uk/articles/529392/20131211/gchq-forced-privatesky-secure-email-service-offline.htm), which the [company refutes](http://www.certivox.com/blog/bid/359788/The-real-story-on-the-PrivateSky-takedown).

Scramble
--------------------------------------------

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

Whiteout
--------------------------------------------

[whiteout.io](https://whiteout.io)

Whiteout is a commercial service featuring an HTML5-based OpenPGP email client that is loaded from the web.

* Written in: Javascript
* Source code: https://github.com/whiteout-io/mail-html5
* License: proprietary, but the code is available for inspection.

Browser Extensions
============================================

Mailvelope
--------------------------------------------

[mailvelope.com](http://mailvelope.com)

Mailvelope is a browser extension that allows you to use OpenPGP email with traditional web-mail providers like Gmail, Yahoo, and Outlook.com. The private key is generated for you, password protected, and stored in the browser's local storage (along with public keys). In the future, the plan is to support automatic discovery and validation of public keys using OpenPGP keyservers and message footers.

Because of an inherent limitation in the way Mailvelope can interface with web-mail, it is not able to send OpenPGP/MIME (although it can read it fine).

* Contact: info@mailvelope.com
* Written in: Javascript
* Source code: https://github.com/toberndo/mailvelope
* Design documentation: http://www.mailvelope.com/help
* License: AGPL
* Platforms: Windows, Mac, Linux (with Android planned).


Mail Clients
============================================

An email client, or MUA (Mail User Agent), provides a user interface to access email from any service provider. Traditional examples of email clients include Thunderbird or Microsoft Outlook (although both these application include a lot of other functionality as well). Nearly all email clients communicate with the email service provider using IMAP and SMTP, although some also support local mailboxes in mbox or Maildir format.

Bitmail
--------------------------------------------

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

Mailpile
---------------------------------------------

[mailpile.is](http://mailpile.is)

Mailpile is an email client designed to quickly handle large amounts of email and also support user-friendly encryption. The initial focus is on email, with plans to eventually support post-email protocols like bitmessage, flowingmail, or darkmail. Also, the developers hope to add support for XMPP-based chat in the future.

**Keys:** Mailpile email encryption is based on OpenPGP (it uses your GPG keyring). Key discovery will be handled using OpenPGP keyservers and including public keys as attachments to outgoing email. Public keys are trusted on first use, with plans for validation via DANE and manual fingerprint verification (future support for a p2p protocol might include additional methods, such as Certificate Transparency or Short Authentication Strings). Currently, keys are not backed up.

**Application:** Mailpile UI is written using HTML5 and Javascript, running against a self-hosted Python application (that typically lives locally on the device, but might be running on your own server).

* Written in: Python, Javascript
* Source code: http://github.com/pagekite/mailpile
* Design documentation: http://github.com/pagekite/mailpile
* License: AGPL & Apache
* Platforms: Windows, Mac, Linux (with Android and iOS planned).
* Contact: team@mailpile.is

Parley
--------------------------------------------

[parley.co](https://parley.co)

Parley is a desktop mail client with a UI written using HTML5 and Javascript, with a local backend written in Python.

**Keys:** Although Parley can be used with any service provider, the Parley servers are used to publish public keys and back up client-encrypted private keys. For key discovery, Parley uses a central repository and the OpenPGP keyservers. For key validation, Parley relies on trust on first use and Parley key endorsement.

**Infrastructure:** All users of the Parley client also sign up for the Parley service, but they use their existing email account. The Parley server acts as a proxy that uses [context.io](http://context.io) for email storage (context.io is a commercial service that provides a REST API for a traditional IMAP account). The Parley server also handles key discovery, validation, and backup. Both the client and server are released as free software.

**Application:** Parley is currently bundles into an executable using [Appcelerator](http://www.appcelerator.com/). The Parley client does not speak IMAP or SMTP directly. Rather, uses the email REST API of context.io.

* Written in: Python, Javascript
* Source code: https://github.com/blackchair/parley
* Design documentation: https://parley.co/#how-it-works
* License: BSD
* Platforms: Windows, Mac, Linux (with Android and iOS planned).

Self-Hosted Email
============================================

FreedomBox
--------------------------------------------

[freedomboxfoundation.org](https://freedomboxfoundation.org)

From its early conception, part of FreedomBox was "email and telecommunications that protects privacy and resists eavesdropping". Email, however, is not currently being worked on as part of FreedomBox (as far as I can tell).

Mailpile
--------------------------------------------

Although Mailpile is primarily a mail client, the background Python component can read the Maildir format for email. This means you could install Mailpile on your own server running a Mail Transfer Agent (MTA) like postfix or qmail. You would then access your mail remotely by connecting to your server via a web browser.


Email Infrastructure
============================================

Dark Mail Alliance
--------------------------------------------

[darkmail.info](https://darkmail.info)

The Dark Mail Alliance includes both a client application and server software. The plan is to support OpenPGP, S/MIME, and some new email-like protocol adapted from SilentCircle's instant message protocol (SCIMP). Both the client and server will be made available as free software.

**Keys:** Key pairs are generated on the user's device and uploaded to their service provider. [Certificate Transparency](http://certificate-transparency.org) will be used to automatically validate the service provider's endorsement of these public keys. Dark Mail additionally plans to support fingerprint confirmation, short authentication strings, and shared secret for manual key validation. Dark Mail plans to support discovery of public keys through DNS, HTTPS, and attached to messages.

**Routing:** The post-email messaging protocol promises to have forward secrecy and protection from metadata analysis (details have not yet been posted, and SCIMP does not currently support meta-data protection). Dark Mail Alliance plans to additionally support pure peer-to-peer messaging using a key fingerprint as the user identifier (supporting both Tor .onion and i2p routing addresses).

**Infrastructure:** Dark Mail plans to support traditional client/server architecture, self-hosted architecture, and pure peer-to-peer architecture.

**Application:** The client application will work with any existing MUA by exposing a local IMAP/SMTP server that the MUA can connect to.

* Written in: C
* Source code: none yet
* Design documentation: none yet
* License: planned to be OSI-compatible
* Platforms: initially Android and iOS, followed by Windows, OS X, Linux, and Windows Phone.
* Contact: press@darkmail.info

LEAP Encryption Access Project
--------------------------------------------

[leap.se](https://leap.se)

LEAP includes both a client application and turn-key system to automate the process of running a secure service provider. Currently, this includes user registration and management, help tickets, billing, VPN service, and secure email service. The secure email service is based on OpenPGP.

**Keys:** Key pairs are generated on the user's device. Private keys, and discovered public keys, are stored in an encrypted database that is synchronized among the user's devices and backed up to the service provider. Keys are automatically validated using a combination of provider endorsement, and network perspective (coming soon). Keys are discovered via the OpenPGP keyservers, the OpenPGP header, email footers, and a custom HTTP based discovery protocol.

**Infrastructure:** LEAP follows a traditional federated client/server architecture. The client is designed to work with any LEAP-compatible service provider. Users are encouraged to get the application from LEAP and not their service provider.

**Application:** The client application works with any existing MUA by exposing a local IMAP/SMTP server that the MUA can connect to. There is a Thunderbird extension to automate configuration of the account in Thunderbird. The client application communicates with the service provider using a custom protocol for synchronizing encrypted databases. The application is a very small C program that launches the Python code. The user interface is written using Qt.

* Written in: Python, C
* Source code: https://leap.se/source
* Design documentation: https://leap.se/docs
* License: mostly GPL v3, some MIT and AGPL.

Unclassified
============================================

Startmail
--------------------------------------------

[startmail.com](http://startmail.com)

The makers of the secure search engine [startpage.com](https://startpage.com) have announced they will be providing secure email service. No details are available yet, other than the service will be compatible with OpenPGP email.

Post-email alternatives
================================

There are several projects to create alternatives to email that are more secure yet still email-like.

These approaches share many similarities:

* They all use fingerprints as unique identifiers.
* Most use some form of a Distribute Hash Table for routing.
* All have a design goal of trusting no one.

Except for Pond, all these alternatives take a pure peer-to-peer approach. As such, they face particular challenges regarding traffic analysis, Sybil attacks, and performance in mobile situations. Since these projects use fingerprints as identifiers, they also may face increased barriers to user adoption.

On the other hand, these post-email alternatives are not vulnerable to a compromised or nefarious service provider.

Bitmessage
--------------------------------------------

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

Dark Mail Alliance
--------------------------------------------

The Dark Mail Alliance plans to incorporate traditional email, a federated email alternative, and a second email alternative that is pure peer-to-peer. Details are not yet forthwith.

FlowingMail
--------------------------------------------

[FlowingMail](http://flowingmail.com)

P2P secure, encrypted email system.

Pond
--------------------------------------------

[pond.imperialviolet.org](https://pond.imperialviolet.org/)

Pond is an email-like messaging application with several unique architectural and cryptographic features that make it stand out in the field.

* Pond uses Axolotl for asynchronous forward secret messages where the key is frequently ratcheted (akin to OTR, but more robust).
* Pond uses a unique architecture where every user relies on a service provider for receiving messages, but sent messages are delivered directly to the recipient's server (over Tor). This allows for strong metadata protection, but does not suffer from the other problems that peer-to-peer systems typically do. In order to prevent excessive spam under this scheme, Pond uses a clever system of group signatures to allow the server to check if a sender is authorized to deliver to a particular user without leaking any information to the server.

* Written in: Go
* License: BSD
* Source code: https://github.com/agl/pond

Advantages:

* Very high security: forward secrecy, metadata protection, likely traffic analysis.
* Pond hybrid federated and peer-to-peer approach is cool and holds a lot of promise.
* Written in Go, and thus probably has much less security flaws than programs written in C or C++.
* Pond is written by Adam Langley, an extremely well respected crypto-engineer.

Disadvantages:

* Uses non-human memorial unique identifiers, although this is not a necessary element of the design. Because there are service providers, it could be made to work with username@domain.org identifiers.
* Requires that you set up contacts in advance before you can communicate with them (via a Short Authentication String).
* Pond is still very difficult to install and use.

Goldbug
--------------------------------------------

http://goldbug.sf.net

* Written in: C++, Qt
* License: BSD

Cables
--------------------------------------------

https://github.com/mkdesu/cables

* Written in: C, Bash
* License: GPL v2

Related Systems
============================================

There are many technologies that don't belong in this document because they either (a) are not trying to make encrypted email-like communication easier, (b) use some kind of weird proprietary escrow system, or (c) we just don't know enough about them yet. Here is a place to store links to such projects.

* [Virtru](https://www.virtru.com) has a secure email product that relies on a centralized key escrow. For details, see [here](http://www.theregister.co.uk/Print/2014/01/24/ex_nsa_cloud_guru_email_privacy_startup) and [here](https://www.virtru.com/how-virtru-works).
* [OpenCom](http://opencom.io) is a secure email and email-like communication in the planning stages.
* [Ubiquitous Encrypted Email](https://github.com/tomrittervg/uee) is a protocol draft for standards that could lead to universal adoption of encrypted email.
