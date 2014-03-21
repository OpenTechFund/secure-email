Eine zunehmende Zahl an Projekten arbeiten an einer neuen Generation für sichere Emails oder E-Mail-artiger Kommunikation. Dieser Bericht stellt die Projekte vor und vergleicht ihre Ansätze. Bitte hilf uns fehlende Details zu ergänzen und alle Fehler zu korrigieren. Um an diesem Dokument mitzuarbeiten, verwende die fork & pull-Funtkionalität von github.

Contents:

1. [Allgemeine Probleme](#common-problems)
  1. [Schlüsselverwaltung](#key-management)
  1. [Metadatenschutz](#metadata-protection)
  1. [Forward Secrecy](#forward-secrecy)
  1. [Datenverfügbarkeit](#data-availability)
  1. [Sichere Authentifizierung](#secure-authentication)
1. [Webmail](#web-mail)
  1. [Mega](#mega)
  1. [PrivateSky](#privatesky)
  1. [Scramble](#scramble)
  1. [Whiteout](#whiteout)
1. [Browser-Erweiterungen](#browser-extensions)
  1. [Mailvelope](#mailvelope)
1. [Mailprogramme](#mail-clients)
  1. [Bitmail](#bitmail)
  1. [Mailpile](#mailpile)
  1. [Parley](#parley)
1. [selbst-betriebene E-Mailserver](#self-hosted-email)
  1. [Dark Mail Alliance](#self-hosted-dark-mail)
  1. [FreedomBox](#freedombox)
  1. [Mailpile](#self-hosted-mailpile)
1. [Emailinfrastruktur](#email-infrastructure)
  1. [Dark Mail Alliance](#dark-mail-alliance)
  1. [LEAP Encryption Access Project](#leap)
1. [Post-E-Mail-Alternativen](#post-email-alternatives)
  1. [Bitmessage](#bitmessage)
  1. [Cables](#cables)
  1. [Dark Mail Alliance](#p2p-dark-mail-alliance)
  1. [FlowingMail](#flowingmail)
  1. [Goldbug](#goldbug)
  1. [Pond](#pond)
1. [unsortiert](#unclassified)
  1. [Startmail](#startmail)
1. [verwandte Arbeiten](#related-works)

<a name="common-problems"></a>Allgemeine Probleme
===========================================================

Alle hier aufgelisteten Technologien sind mit ähnlichen Problemen konfrontiert, wenn es darum geht, E-Mail (oder E-mail-artige Kommunikation) sicher und einfach nutzbar zu machen. Diese Probleme sind schwer und widerstanden einfachen Lösungen, denn es gibt keine schnellen technologischen Fehlerkorrekturen: das Problem besteht in der komplexen Interaktion zwischen Bedienerfahrung, Infrastrukturen der realen Welt und sicherheit. Obwohl bisher kein Konsens hergestellt werden konnte, wie diesen Problemen am besten zu begegnen ist, reflektiert die Vielfalt der hier dargestellten Projekte ein anschwellendes Interesse in diesem Bereich und ermuntert den Geist, zu experimentieren.

<a name="key-management"></a>Schlüsselverwaltung
-----------------------------------------------------------

Alle Projekte in diesem Bericht benutzen PKI-Verschlüsselung, um eine vertrauliche Nachricht zur vorgesehenen Empfängerin zu senden und um die Urheberin der Nachricht zu verifizieren. Unglücklicherweise sind Verschlüsselungsverfahren mit öffentlichen und privaten Schlüsseln kompliziert, selbst für erfahrene Nutzerinnen. Die meisten Konzepte sind verwirrend für die meisten Nutzerinnen: öffentliche versus private Schlüssel, Schlüsselsignierung, Wiederrufen von Schlüsseln, Signierungsschlüssel versus Verschlüsselungsschlüssel, Bitlänge usw.

Traditionell verlässt sich Emailverschlüsselung entweder auf X.509-Zertifikatautoriäten (CA) oder einem dezentralen Vertrauensnetz ("Web of Trust", WoT) zur Validierung von Schlüsseln (um zu authentifizieren, dass eine bestimmte Person einen bestimmten Schlüssel besitzt). Kürzlich gelangten beide Systeme unter heftige Kritk. Wiederholte Sicherheitsprobleme vieler Zertifikatautoritäten zeigten ernste Probleme des CA-Systems. Zudem verstehen wir im Zeitalter von sozialer Netzwerkanalyse und der Sensitivät sozialer Graphen, dass die Preisgabe von Metadaten durch ein "Web of Trust" unter Sicherheitsaspekten nicht mehr akzeptabel ist.

An diesem Punkt stehen wir heute: es gibt PKI-Technologie, die außerordentlich schwierig für gewöhnliche Anwender ist, und unsere Methoden der Schlüsselvalidierung wurden diskreditiert. Die hier aufgelisteten Projekte wollen diese Lücke füllen, indem sie die Benutzung von PKI-Verschlüsselung vereinfachen. Dazu zählen folgende vier Elemente:

* Schlüsselsuche: Es gibt keinen allgemein gebräuchlichen Standard zum Finden von öffentlichen Schlüsseln für eine bestimmte E-Mail-Adresse. Alle aufgeführten Projekte, die OpenPGP benutzen, zielen ursprünglich darauf ab, öffentliche Schlüsselserver als Interimslösung zu benutzen, obwohl diese Infrastruktur nicht für diese Art der Benutzung entwickelt wurde.
* Schlüsselvalidierung: Wenn nicht Zertifikatautoritäten oder Vertrauensnetze, was dann? Fast jedes Projekt hier benutzt Vertrauen-bei-erstem-Kontakt ("Trust On First Use", TOFU) in der ein oder anderen Weise. Mit TOFU wird einem erstmals benutzten Schlüssel automatisch vertraut. Das funktioniert prima für alle, die garantiert kein Opfer von Angriffen sind, aber ist ansonsten höchst verwundbar.
* Schlüsselverfügbarkeit: Fast jeder Versuch, das Problem der Schlüsselvalidierung zu lösen, mündet in ein Problem der Schlüsselverfügbarkeit, denn sobald ein Schlüssel erfolgreich validiert wurde, muss sicher gestellt werden, dass dieser Schlüssel auf allen möglichen Geräten verfügbar ist, auf denen Nachrichten gesendet oder empfangen werden sollen.
* Schlüsselwiederrufung: Was passiert, wenn ein privater Schlüssel verloren gegangen ist und ein neuer privater Schlüssel erstellt werden muss? Keines dieser Projekte hat eine Antwort, wie damit in einer Post-CA- und Post-WoT-Welt umgegangen werden soll.

<a name="metadata-protection"></a>Metadatenschutz
-----------------------------------------------------------

Traditionelle Methoden für sichere E-Mail geben Metadaten preis. Jetzt wissen wir, dass Metadaten oft sensitiver als der Nachrichteninhalt ist: Metadaten sind strukturierte Daten, leicht auf ewig speicherbar und Ziel mächtiger und enthüllender Analysemethoden.

Metadatenschutz jedoch ist **schwierig**. Um Metadaten zu schützen, muss das Protokoll der Nachrichtenzustellung Sender und Empfänger vor allen Übermittlungsknoten verstecken. Das ist nicht möglich, mit dem traditionellen Emailtransortprotokoll, obwohl es wahrscheinlich Huckepack mit einem zusätzlichen (nicht abwärtskompatiblen) Protokoll als zusätzliche Ebene über dem traditionellen Emailtransportprotokoll zu erreichen ist, Metadaten zu schützen.

Alternativ lehnen einige Projekte traditionellen Emailtransport komplett ab. Diese dezentralen P2P-Ansätze für Metadatenschutz teilen sich in drei Gruppen: (1) direkter Transport der Nachricht vom Sendegerät zum Gerät der Empfängerin; (2) Transport der Nachricht durch ein Freundenetzwerk; (3) Versenden der Nachricht an alle. Die ersten beiden Ansätze schützen Metadaten, sind aber anfällig für Analysen des Netzwerkverkehrs, was eben diese Metadaten enthüllen kann. Die dritte Lösung hat ernsthafte Skalierungsprobleme. (Pond nutzt eine vierte Methode, mehr dazu weiter unten).

Alle Methoden zum Metadatenschutz erwarten außerdem eine Zunahme von Spam (denn eine der primären Methoden zur Spamvermeidung ist die Analyse von Metadaten). Daher machen es manche Systeme mit starkem Metadatenschutz unmöglich, Nachrichten mit Unbekannten auszutauschen. Das eliminiert Spam sehr erfolgreich, kann aber auf lange Sicht die Rolle von E-Mail nicht ersetzen.

<a name="forward-secrecy"></a>Forwärtssicherheit ("Forward Secrecy")
-----------------------------------------------------------

"Forward secrecy" ist eine Sicherheitseigentschaft, wodurch Anfreifern nicht in der Lage sind, verschlüsselte Daten mitzuspeichern und später zu entschlüsseln, sobald der private Schlüssel zur Verfügung steht. Ohne Vorwärtssicherheit könnte ein Angreifer außerdem Nachrichten speichern und warten, bis Computer leistungsfähig genug sind, um die Verschlüsselung mittels Bruteforce zu brechen. Tradinioelle Transport- (SSL/TLS) oder E-Mail-Verschlüsselung (PGP/GPG) bieten keine Vorwärtssicherheit.

Alle Methoden für Vorwärtssicherheit beinhalten einen Prozess, bei dem beide Parteien einen kurzlebigen Schlüssel, der nur kurz, z.B. für die jeweils aktuelle Kommunikationssitzung verwendet wird. In vielen Fällen wird dieser Schlüssel für jede Nachricht neu  erzeugt. Traditionelle Methoden von Forwärtssicherheit sind daher für die asynchrone Natur von E-Mail-Kommunikation ungeeignet, denn für E-Mail muss es möglich sein, Nachrichten zu schicken, auch wenn die Gegenseite gerade nicht online ist, um einen Schlüssel auszuhandeln.

Es gibt verschiedene experimentelle (und komplizierte) Protokolle, die versuchen, Vorwärtssicherheit für asynchrone Kommunikation zu ermöglichen, aber keines davon konnte sich bisher als Standard etablieren. Ein anderer möglicher Ansatz ist traditionelle Verschlüsselung ohne Garantie für Vorwärtssicherheit, der sich statt dessen automatischer Schlüsselsuche und -validierung bedient, um Schlüssel regelmäßig zu tauschen. Auf diese Art und Weise könnten private Schlüssel alle paar Tage ausgetauscht werden, um eine krude Form der Forwärtssicherheit zu erreichen.

<a name="data-availability"></a>Datenverfügbarkeit
-----------------------------------------------------------

NutzerInnen verlangen heute Datenverfügbarkeit: sie wollen auf ihre Daten und Nachrichten von jedem ihrer Geräte zugreifen, wo immer und wann auch immer sie wollen. Viel wichtiger ist, sie wollen nicht mit dem Verlust eines Gerätes alles Daten verlieren. Für unsichere Kommunikation, ist die Lösung schlicht: alle Daten hocken in der Cloud. Für sichere Kommunikation ist allerdings kein bewährtes System bekannt. Wie oben ausgeführt, ist das Problem der Schlüsselverwaltung also eigentlich ein Datenverfügbarkeitsproblem.

Die meisten der aufgeführten Projekte haben die Lösung dieses Problems der Datenverfügbarkeit auf die Zukunft verschoben. Einige nutzen IMAP, um Daten zu synchronisieren, oder entwickelten ein eigenes sicheres Synchronisationsprotokoll.

<a name="secure-authentication"></a>Sichere Authentifizierung
-----------------------------------------------------------

Für Projekte, die einen zentralen Dienst benutzen, besteht das Hauptproblem in der Authentifizierung gegenüber dem Dienstanbieter ohne das Passwort preiszugeben (da das Passwort wahrscheinlich auch genutzt wird, um auf den privaten Schlüssel und andere sichere Speicher zuzugreifen, ist es wichtig, dass der Dienstanbieter keinen Klartextzugang wie bei typischen Passwortmechanismen erhält). Mögliche Methoden sind:

* sichere Paswörter. Die Anwendung könnte ein Passwort zur Authentifizierung und ein anderes zur Sicherung von Geheimnissen benutzen.
* Verwendung einer Prüfsumme des Passworts an den Server, anstelle des Passworts. Das könnte funktionieren, aunthentifiziert allerdings nicht den Server (ein MITM-Angreifer könnte immer mit einer Bestätigung antworten), und ist außerdem anfällig für Bruteforce-Wörterbuch-Angriffe.
* Nutzen von sicheren entfernten Passwörtern ("Secure Remote Password", SRP), eine Art kryptographischer "Null-Wissen-Beweis" (zero knowledge proof) entwickelt für Passwortauthentifzierung, wobei sich Client und Server gegenseitig authentifzieren. SRP gibt es bereits eine Weile und ist ganz gut analysiert, allerdings dennoch anfällig für Bruteforce-Wörterbuch-Angriffe (wenn auch weit weniger als traditionelle Passwortmechanismen.
* Signiererung eines Geheimnis des Servers mit dem geheimen Schlüssel des Clients. Das hat den Vorteil, dass es fast unanfällig für Bruteforce-Angriffe ist, allerdings anfällig für betrügerische Server und setzt voraus, dass das Client-Gerät den privaten Schlüssel besitzt.

Bisher hat sich kein einheitlicher Standard etabliert, obwohl es SRP bereits recht lange gibt.

<a name="web-mail"></a>Webmail
===========================================================

Die meisten Nutzer sind vertraut mit dem unglauglichem Komfort, E-Mails im Browser zu lesen: von jedem Gerät ohne Sorge über Synchronisierung. Entwickler allerdings haben es schwer, diese System wirklich sicher zu machen. Diese Probleme sind überwindbar, aber nicht so einfach zu lösen.

Zunächst wird die Anwendung jedes Mal von Webserver geladen und der Client könnte entweder durch den Anbieter oder auf dem Weg mit einer Hintertür  versehen werden. Um das zu verhindern, könnte der Code von einem vertrauenswürdigen Drittanbieter geladen werden. Dafür gibt es zwei Wege: 

1. App Store: Die meisten Browser unterstützen Erweiterungen in der Form von "Browser-Anwendungen". Diese werden aus einer zentralen Stelle geladen und auf dem Gerät installiert. In diesem Fall ist der Drittanbieter, der "App Store". Daher muss der Nutzer diesem Server vertrauen, dass die ausgelieferte Anwendung sicher ist. Das ist zum Beispiel der Ansatz von [cryptocat](https://crypto.cat).
2. Drittanbieter: Es gibt zwei fortgeschrittene Mechanismen, um Webanwendungen von einer Seite zu laden und auf Daten von einer anderen zugreifen zu lassen. Eine ist CORS ([Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)) und die andere ist der [window.postMessage command](https://developer.mozilla.org/en-US/docs/Web/API/window.postMessage) von HTML5. Mit jeder der beiden Methoden lässt sich die Anwendung von beliebiger Stelle laden, oder sie ist lokal wie bei [Unhosted](https://unhosted.org).

Zweitens, auch wenn die Anwendung von einer vertrauenswürdigen Stelle geladen wird, sind Browser keine besonders gute Umgebung für sensible Daten: es gibt Möglichkeiten für eine browserinterne Anwendung, um Daten preiszugeben, und Browser sind im allgemeinen voller Sicherheitslöcher (es ist ein ziemlich schwieriges Problem, unbekannten Code lokal in einer sicheren Sandbox auszuführen). Zugegeben, Browserentwickler sind oftmals wachsam, um Sicherheitslücken zu schließen (je nach dem, wen du fragst), aber eine Browserumgebung ist weit entfernt, sicher zu sein. Es ist allerdings die komfortabelste Umgebung.

Drittes, Entwickler web-basierter sicherer E-Mail sind mit einer weiteren Herausforderung im Umgang mit Offline-Daten und Caching konfrontiert. Moderne HTML5-Anwendungen speichern eine Menge Daten lokal auf dem Gerät des Nutzers mittels localStorage. Allerdings ist dieser Bereich (noch) nicht verschlüsselt. Eine sichere web-basierte E-Mail-Anwendung sollte daher entweder anbieten, gar keine Daten local zu speichern, oder eine Verschlüsselung aller einzelnen Objekte im localStorage implementieren, ein recht ineffizienter Prozess. Selbst das temporäre Speichern von Schlüsseln in kurzlebigen Sitzungsspeichern ist problematisch, denn diese können später leicht von der Platte gelesen werden.

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

1. Insecure service providers: A mail client cannot address many of the core problems with email security when used with a traditional email provider. For example, metadata will not be protected in storage or transit, and the provider cannot aid in key discovery or validation. Most importantly, many existing mail providers are highly vulnerable, since few rely on DNSSEC for their MX records or validate their StartTLS connections for mail relay (when they even bother to enable StartTLS). A traditional email provider also requires authentication via password that is seen by the provider in clear text, and might be recorded by them.
1. Install a new app: As with many of the other approaches, the custom mail client approach requires that users download and install a specialized application on their device before they can use it.

Ultimately, the custom mail client approach is probably not a good strategy for secure email, but may be an excellent strategy for weening users away from email and to a different and more secure protocol. Most of the projects in this section see email support as a gateway to ease the transition to something that can replace email.

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

Mailpile is an email client designed to quickly handle large amounts of email and also support user-friendly encryption. The initial focus is on email, with plans to eventually support post-email protocols like bitmessage, flowingmail, or darkmail. Also, the developers hope to add support for XMPP-based chat in the future.

**Keys:** Mailpile email encryption is based on OpenPGP (it uses your GPG keyring). Key discovery will be handled using OpenPGP keyservers and including public keys as attachments to outgoing email. Public keys are trusted on first use, with plans for validation via DANE and manual fingerprint verification (future support for a p2p protocol might include additional methods, such as Certificate Transparency or Short Authentication Strings). Currently, keys are not backed up.

**Application:** Mailpile UI is written using HTML5 and Javascript, running against a self-hosted Python application (that typically lives locally on the device, but might be running on your own server).

**Limitations:** Mailpile does not currently have a scheme for recovery if your device is destroyed or a method for securely synchronizing keys among devices. The search index of email stores words as hashes, but the domain of words in written language is small enough that this likely will not afford proper protection should an attacker gain access to the disk.

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

Self-hosted email is an intriguing "legal hack", albeit one that has not been tested in the courts and faces many technical challenges.

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

<a name="email-infrastructure"></a>Email Infrastructure
===========================================================

The "infrastructure" projects give a service provider the opportunity to offer secure email accounts to end-users. By modifying how both email clients and email servers work, these projects have the potential to deploy greater security measures than are possible with a client-only approach. For example:

* Encrypted relay: A secure email provider is able to support, and enforce, encrypted transport when relaying mail to other providers. This is an important mechanism for preventing mass surveillance of metadata (which is otherwise not protected by OpenPGP client-side encryption of message contents).
* Easier key management: A secure email provider can endorse the public keys of its users, and provide assistance to various schemes for automatic validation. Additionally, a secure email provider, coupled with a custom client, can make it easy to securely manage and back up the essential private keys which are otherwise cumbersome for most users to manage.
* Invisible upgrade to better protocols: A secure email provider has the potential to support multiple protocols bound to a single user@domain address, allowing automatic and invisible upgrades to more secure post-email protocols when both parties detect the capability.
* A return to federation: The recent concentration of email to a few giant providers greatly reduces the health and resiliency of email as an open protocol, since now only a few players essentially monopolize the medium. Projects that seek to make it easier to offer secure email as a service have the potential to reverse this trend.
* Secure DNS: A secure provider can support DNSSEC and DANE, while most other email providers are unlikely to anytime soon. This is very important, because it is easy to hijack the MX records of a domain without DNSSEC.

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

Most of these challenges have possible technological solutions that might make peer-to-peer approaches the most attractive option in the long run. For this reason, it is important that research continue in this area. However, [in the long run we are all dead](https://en.wikiquote.org/wiki/John_Maynard_Keynes) and peer-to-peer approaches face serious hurdles before they can achieve the kind of user experience demanded today.

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
