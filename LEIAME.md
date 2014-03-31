Há um número crescente de projetos trabalhando na próxima geração de e-mail seguro ou de comunicação semelhante ao e-mail. Esse é um rascunho inicial de um relatório destacando os projetos e comparando as suas abordagens. Por favor, ajude-nos a completar os detalhes que faltam e corrijam qualquer imprecisão. Para contribuir com esse documento, faça um fork desse repositório e faça pull request.

Conteúdo:

1. [Problemas Comuns](#common-problems)
  1. [Gerenciamento de Chave](#key-management)
  1. [Proteção dos Metadados](#metadata-protection)
  1. [Forward Secrecy](#forward-secrecy)
  1. [Disponibilidade de dados](#data-availability)
  1. [Autenticação Segura](#secure-authentication)
1. [E-mail Web](#web-mail)
  1. [Mega](#mega)
  1. [PrivateSky](#privatesky)
  1. [Scramble](#scramble)
  1. [Whiteout](#whiteout)
1. [Extensões de Navegador](#browser-extensions)
  1. [Mailvelope](#mailvelope)
1. [Clientes de E-Mail](#mail-clients)
  1. [Bitmail](#bitmail)
  1. [Mailpile](#mailpile)
  1. [Parley](#parley)
1. [E-mail Auto hospedado](#self-hosted-email)
  1. [Dark Mail Alliance](#self-hosted-dark-mail)
  1. [FreedomBox](#freedombox)
  1. [Mailpile](#self-hosted-mailpile)
  1. [kinko](#kinko)
1. [Infra estrutura de E-mail](#email-infrastructure)
  1. [Dark Mail Alliance](#dark-mail-alliance)
  1. [LEAP Encryption Access Project](#leap)
1. [Alternativas pós-email](#post-email-alternatives)
  1. [Bitmessage](#bitmessage)
  1. [Cables](#cables)
  1. [Dark Mail Alliance](#p2p-dark-mail-alliance)
  1. [FlowingMail](#flowingmail)
  1. [Goldbug](#goldbug)
  1. [Pond](#pond)
1. [Não classificado](#unclassified)
  1. [Startmail](#startmail)
1. [Trabalhos Relacionados](#related-works)

<a name="problemas-comuns"></a>Problemas Comuns
===========================================================

Todas as tecnologias listadas aqui enfrentam um conjunto comum de problemas quando tentam fazer um e-mail (ou uma comunicação parecida com o e-mail) seguro e fácil de usar. Esses problemas são difíceis e desafiam soluções fáceis, pois não há soluções tecnológicas rápidas: a questão é uma interação complexa entre a experiência de usuário, a infraestrutura do mundo real e a segurança. Embora nenhum consenso tenha surgido sobre a melhor forma de resolver qualquer um desses problemas, a diversidade de projetos listados nesse relatório reflete uma onda de interesse nessa área e um espírito encorajador de experimentação.

<a name="key-management"></a>Gerenciamento de Chave
-----------------------------------------------------------

Todos os projetos nesse relatório usam criptografia de chave pública para permitir que o usuário envie uma mensagem confidencial para o destinatário desejado e para o destinatário verificar a autoria da mensagem. Infelizmente, a criptografia de chave pública é notoriamente difícil de se usar corretamente, mesmo para os usuários avançados. Os próprios conceitos são confusos para a maioria dos usuários: chave pública versus chave privada, assinatura de chave, revogação de chave, chaves de assinatura versus chaves de criptografia, tamanho da chave e por aí vai.

Tradicionalmente, a criptografia de chave pública para e-mail se baseou ou no sistema de Autoridade Certificadora X.509 (Certificate Authority, CA) ou na descentralizada "Teia de Confiança" (Web of Trust - WoT) para a validação da chave (para autenticar que uma determinada pessoa é dona de uma chave específica). Recentemente, ambos esquemas têm estado sob intensa crítica. Repetidos lapsos de segurança em várias Autoridades Certificadoras tem revelado sérias falhas no seu sistema. Por outro lado, numa era onde nós entendemos melhor o poder das análises das redes sociais e quão sensível é um grafo social, a exposição de metadados pela "Web of Trust" não é mais aceitável do ponto de vista da segurança.

Isto é o que temos agora: nós temos tecnologia de chave pública que é excessivamente difícil para o usuário comum e os nossos únicos métodos de validação de chave caíram em descrédito. Os projetos listados aqui mergulharam nesse vácuo, tentando simplificar o uso da criptografia de chave pública. Esses esforços possuem quatro elementos:

* Descoberta de chave: Não há nenhum padrão comum usado para descobrir a chave pública vinculada a um determinado endereço de e-mail. Todos os projetos aqui que usam OpenPGP pretendem usar, como uma medida paliativa, os servidores de chave OpenPGP para a descoberta de chave, embora a infraestrutura do servidor de chaves não foi projetada para ser usada desse modo.

* Validação de chave: Se não é a Autoridade Certificadora ou a Teia de Confiança, então é o que? Quase todos projetos aqui usam o Trust On First Use - TOFU (Confiança no Primeiro Uso) de um jeito ou de outro. Com TOFU, uma chave é assumida ser a chave certa na primeira vez que ela for usada. TOFU pode trabalhar bem para associações de longo prazo e para pessoas que não estão sendo alvos de ataques dirigidos, mas a sua segurança baseia-se na segurança da descoberta de transporte e na habilidade da aplicação de reter uma memória de chaves descobertas. TOFU pode falhar em muitas situações no mundo real, onde um usuário pode necessitar gerar novas chaves ou se comunicar seguramente com um novo contato. Os projetos aqui estão experimentando o TOFU em diferentes formas e esses problemas podem ser mitigados combinando TOFU com outras medidas.

* Disponibilidade da Chave: Quase todas as tentativas de resolver o problema de validação da chave torna-se um problema de disponibilidade da chave, pois uma vez validada uma chave pública, você precisa ter certeza que essa validação está disponível para o usuário em todos os dispositivos que ele quiser enviar ou receber mensagens.

* Revogação da chave: O que acontece quando uma chave privada é perdida e o usuário quer emitir uma nova chave pública? Nenhum dos projetos nesse relatório possui uma resposta de como lidar com isso num mundo pós-Autoridade Certificadora e pós-Teia de Confiança.

<a name="metadata-protection"></a>Proteção dos Metadados
-----------------------------------------------------------

Os esquemas tradicionais para e-mail seguro deixaram os metadados expostos. Nós agora sabemos que o metadado é frequentemente mais sensível que o conteúdo da mensagem: metadado é a estrutura do dado, facilmente armazenado para sempre e sujeito a técnicas poderosas de análises de redes sociais que podem ser incrivelmente reveladoras.

A proteção dos metadados, no entanto, é **difícil**. Para proteger o metadado, o protocolo de tráfego da mensagem deve esconder o remetente e o destinatário de todos os intermediários responsáveis em encaminhar a mensagem. Isso não é possível com os protocolos tradicionais para o transporte de e-mail, no entanto, será provavelmente possível se sobrepor adicionando outros protocolos (não retro-compatíveis) em cima do transporte tradicional de e-mail, afim de assegurar a proteção dos metadados.

Como alternativa, alguns projetos rejeitam inteiramente o transporte de e-mail. Essas abordagens descentralizadas de peer-to-peer para a proteção de metadados geralmente caem em quatro campos: (1) transmitem diretamente a mensagem do dispositivo do remetente para o dispositivo do destinatário; (2) transmitem as mensagens através de uma rede de amigos; (3) transmitem as mensagens em broadcast para todo mundo; (4) transmitem as mensagens através de redes anônimas como o Tor. As duas primeiras abordagens protegem o metadado, mas às custas do aumento da vulnerabilidade para análise de tráfego que poderia revelar os mesmos metadados. A terceira solução enfrenta sérios problemas de escala. Pond usa um quarto método, discutido abaixo.

Todos os esquemas de proteção dos metadados se deparam com a possibilidade de aumento do Spam (uma vez que um dos principais métodos usados para previnir Spam é a análise dos metadados). Essa é a razão de alguns esquemas com forte proteção aos metadados tornam impossível enviar ou receber mensagens de qualquer pessoa que você não teve contato anterior. Isso funciona brilhantemente para reduzir Spam, mas é improvável ser viável como uma estratégia a longo prazo para substituir totalmente a utilidade do e-mail.

<a name="forward-secrecy"></a>Forward Secrecy
-----------------------------------------------------------

Forward secrecy (sigilo futuro ou antecipado) é uma propriedade da segurança que previne um atacante de salvar as mensagens hoje e então depois decifrar essas mensagens quando tiver capturado a chave privada do usuário. Sem forward secrecy, um atacante pode também ser capaz de capturar mensagens hoje e simplesmente esperar os computadores serem poderosos o suficiente para quebrar a criptografia por força bruta. A tradicional criptografia de e-mail não oferece forward secrecy.

Todos métodos para forward secrecy envolvem um processo onde duas partes negociam uma chave efêmera que é usada por um período curto de tempo para tornar segura sua comunicação. Em muitos casos, a chave efêmera é gerada a cada nova mensagem. Os esquemas tradicionais para forward secrecy são incompatíveis com a natureza assíncrona da comunicação de e-mail, já que com o e-mail você ainda precisa ser capaz de enviar uma mensagem para uma pessoa mesmo se ela não estiver online e a geração de chave efêmera requer uma troca de ida e volta entre as duas partes.

Há vários novos protocolos experimentais (e complicados) que tentam realizar tanto forward secrecy como suporte para comunicação assíncrona, mas nenhum ainda emergiu como um padrão. Uma outra possível abordagem é usar a criptografia tradicional sem suporte para forward secrecy, mas, ao invés disso, apoiar-se num esquema de descoberta automática de chave e validação de modo a rotacionar frequentemente as chaves. Desta forma, um usuário poderia jogar fora sua chave privada a cada poucos dias, atingindo uma forma muito bruta de forward secrecy.

<a name="data-availability"></a>Disponibilidade de dados
-----------------------------------------------------------

Os usuários hoje demandam disponibilidade de dados: eles querem ser capazes de acessar suas mensagens e enviá-las a partir de qualquer dispositivo que escolherem, de onde quiserem e quando quiserem. E o mais importante, não querem que caso percam um dispositivo em particular, resulte na perda de dados. Para uma comunicação insegura, conseguir disponibilidade de dados é muito fácil: simplesmente armazene tudo na nuvem. Para uma comunicação segura, no entanto, nós não temos soluções comprovadas para esse problema. Como observado acima, o problema de gereciamento de chave é também um problema de disponibilidade de dados.

A maioria dos projetos aqui adiaram lidar com o problema da disponibilidade de dados. Alguns poucos usaram o IMAP para sincronizar dados ou para desenvolver seus próprios protocolos de sincronização segura.

<a name="secure-authentication"></a>Autenticação Segura
-----------------------------------------------------------

Para aqueles projetos que fazem uso de um provedor de serviço, um dos problemas principais é como autenticar seguramente com o provedor de serviço sem revelar a senha (uma vez que a senha é provavelmente também usada para criptografar a chave privada e outro armazenamento seguro, então, é importante que o provedor de serviço não possua acesso em texto puro como nos esquemas típicos de autenticação de senha). Os esquemas possíveis incluem:

* Senhas separadas. A aplicação pode usar uma senha para autenticação e uma senha separada para proteger os segredos.
* Pre-hash da senha no cliente antes de enviá-la para o servidor. Esse método pode funcionar, embora não autentique também o servidor (um servidor impostor pode sempre responder com uma mensagem de sucesso) e está vulnerável a ataques de força bruta com dicionário.
* Usar Secure Remote Password (SRP), um tipo de prova criptográfica de conhecimento-zero (zero knowledge proof) projetada para a autenticação de senha, na qual o cliente e o servidor se autenticam mutuamente. O SRP existe há algum tempo e é muito bem analisado, mas é ainda vulnerável a ataques de força bruta com dicionário (embora muito menos que os esquemas tradicionais de senha).
* Assine um desafio do servidor com a chave privada do usuário. Isso têm a vantagem de ser quase impossível a um ataque de força bruta, mas é vulnerável do provedor de serviço ser um impostor e requer que o dispositivo do usuário tenha a chave privada.

Nenhum consenso ou padrão emergiu ainda, embora o SRP esteja disponível há algum tempo.

<a name="web-mail"></a>Webmail
===========================================================

A maioria dos usuários são familiarizados com o e-mail acessado pela web e na incrível conveniência que oferece: você pode acessar seu e-mail de qualquer dispositivo e não precisa se preocupar com a sincronização dos dados. Os desenvolvedores de webmail enfrentam várias desafios difíceis quando tentam fazer uma aplicação verdadeiramente segura. Esses desafios podem ser superados, mas não facilmente.

Primeiro, por que a aplicação web é carregada do servidor web a cada vez que se usa e o provedor de serviço pode direcionar uma versão do cliente para você que inclui um backdoor. Para superar essa vulnerabilidade, é possível carregar o código da aplicação web de um terceiro. Há duas formas de se fazer isso:

1. Loja de aplicativo: A maioria dos navegadores web suportam extensões especiais no formato de "Aplicações do Navegador". Elas são carregadas a partir de algum tipo de loja de aplicativo e instalam no dispositivo do usuário. Nesse caso, o terceiro que provê a aplicação é a loja de aplicativo. Portanto, o usuário passa a depender da loja de aplicativo para fornecer uma versão segura do aplicativo. Por exemplo, essa é a abordagem usada pelo [cryptocat](https://crypto.cat).

2. Terceiros: Há dois mecanismos avançados para permitir uma aplicação web ser carregada de um site e permitir que acesse os dados de um outro site. Uma é chamada CORS ([Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)) e a outra é o [comando window.postMessage](https://developer.mozilla.org/en-US/docs/Web/API/window.postMessage) do HTML5. Em ambos os métodos, qualquer um pode ser um terceiro fornecedor da aplicação ou pode ser auto-hospedada. Por exemplo, essa é a abordagem usada pelo [Unhosted](https://unhosted.org).

Segundo, mesmo se a aplicação é carregada de um terceiro de confiança, os navegadores web não são o ambiente ideal para dados sensíveis: há muitas formas de uma aplicação do navegador vazar dados e os navegadores web são notoriamente propensos a falhas de segurança (é um problema muito difícil de ser capaz de rodar localmente um código não confiável num ambiente seguro). A seu favor, os desenvolvedores de navegadores estão frequentemente vigilantes para arrumar essas falhas (dependendo de quem você conversar), mas o ambiente do navegador está muito longe de um ambiente de computação segura. E continua ser, entretanto, o ambiente mais conveniente.

Terceiro, os desenvolvedores de e-mail seguro para web enfrentam um desafio extra quando lidam com dados offline ou caching ( armazenamento temporário de dados). Aplicativos modernos de HTML5 geralmente armazenam um monte de dados localmente no dispositivo do usuário por meio do armazenamento localStorage. Porém, atualmente, nenhum navegador armazena-o criptografado. Uma aplicação de e-mail web deve ou escolher não suportar nenhum armazenamento local ou desenvolver um esquema para criptografar individulamente cada objeto colocado no localStorage, um processo que é muito ineficiente. Mesmo armazenar chaves temporariamente no armazenamento de sessão de curta duração é problemático, uma vez que estes podem ser facilmente lidos depois no disco rígido.

<a name="mega"></a>Mega
-----------------------------------------------------------

[mega.co.nz](https://mega.co.nz)

O relançamento do Mega teve como destaque a criptografia do lado do cliente por uma aplicação javascript rodando no navegador. O Mega anunciou planos para ampliar suas ofertas para incluir um serviço de e-mail com design similar. Não há maiores detalhes. Em entrevistas, o Mega disse que o javascript que roda no navegador será de código aberto, mas o componente do servidor será proprietário.

<a name="privatesky"></a>PrivateSky
-----------------------------------------------------------

PrivateSky era um serviço web de e-mail seguro  que escolheu encerrar as atividades por que seu design não era compatível com a lei do Reino Unido. Muitos na imprensa disseram que [o GCHQ forçou o fechamento](http://www.ibtimes.co.uk/articles/529392/20131211/gchq-forced-privatesky-secure-email-service-offline.htm), algo que a [empresa refuta](http://www.certivox.com/blog/bid/359788/The-real-story-on-the-PrivateSky-takedown).

<a name="scramble"></a>Scramble
-----------------------------------------------------------

[scramble.io](https://scramble.io)

Scramble é uma aplicação de e-mail OpenPGP que pode ser carregada de um website (há planos de adicionar suporte a loja de aplicativos). Além disso, é possível se inscrever no serviço de e-mail do scramble.io.

**Chaves:** As chaves privadas são geradas no navegador, criptografada com a senha do usuário e então armazenada no servidor. O servidor não vê a senha do usuário (a senha é convertida num hash usando scrypt antes de ser enviada para o servidor durante a criação da conta e a autenticação). A chave mestra de armazenamento (chave simétrica) usada para criptografar as chaves são armazenadas no sessionStorage do navegador, que é apagada quando o usuário se desconecta. As chaves são validadas usando notários.

**Infra estrutura:** Scramble utiliza um sistema de perspectiva de rede para descobrir e validar as chaves públicas. O cliente virá com uma lista de notários predefinidos que podem ser usados para a consulta de chaves públicas. Se os notários aceitarem, o cliente irá considerar a chave para ser validada.

**Aplicação:** Atualmente, Scramble é uma aplicação javascript HTML5 tradicional carregada do website. No futuro, Scramble também será instalável como um aplicativo no navegador.

* Escrito em: Go, Javascript
* Código fonte: https://github.com/dcposch/scramble
* Documentação do projeto: https://github.com/dcposch/scramble/wiki/Scramble-Protocol
* Licença: LGPL
* Plataformas: Windows, Mac, Linux (com planejamento para o Android).

<a name="whiteout"></a>Whiteout
-----------------------------------------------------------

[whiteout.io](https://whiteout.io)

Whiteout é um serviço comercial que apresenta um cliente de e-mail OpenPGP baseado no HTML5 que é carregado pela web.

* Escrito em: Javascript
* Código fonte: https://github.com/whiteout-io/mail-html5
* Licença: proprietário, mas o código está disponível para auditoria.

<a name="browser-extensions"></a>Extensões do Navegador
===========================================================

Uma extensão para o navegador modifica o comportamento de um navegador web (não confundir com uma aplicação do navegador, a qual possui muito menos permissão e consiste numa aplicação independente). A extensão para o navegador é capaz de modificar como o usuário interage com uma variedade de sites. As extensões de navegadores compartilham muitas das mesmas vantagens e desvantagens da [abordagem do webmail](#webmail).

<a name="mailvelope"></a>Mailvelope
-----------------------------------------------------------

[mailvelope.com](http://mailvelope.com)

Mailvelope é uma extensão do navegador que permite você usar um e-mail OpenPGP com provedores webmail tradicionais como Gmail, Yahoo e Outlook.com.

**Chaves:** A chave privada é gerada por você, a senha é protegida e guardada no armazenamento local do navegador (junto com a chave pública). No futuro, o plano é suportar a descoberta automática e validação de chaves públicas usando os servidores de chaves do OpenPGP e rodapés de mensagens.

**Aplicação:** Quando a extensão detecta que possui uma página web aberta de um provedor de webmail suportado como o Gmail, ela oferece ao usuário a oportunidade de criptografar o que é digitado na janela de composição e decifra as mensagens que ele receber.

**Limitações:** Por causa de uma limitação inerente no jeito que o Mailvelope pode interagir com o webmail, ele é incapaz de enviar OpenPGP/MIME (embora ele possa ler). Assim como mencionado anteriormente, o armazenamento do navegador não é particularmente o local ideal para guardar as chaves. Quando um provedor de webmail muda sua UI (ou API se acontecer de ter uma), a extensão deverá ser atualizada para suportar o novo formato.

* Contato: info@mailvelope.com
* Escrito em: Javascript
* Código fonte: https://github.com/toberndo/mailvelope
* Documentação do projeto: http://www.mailvelope.com/help
* Licença: AGPL
* Platformas: Windows, Mac, Linux (com planejamento para Android).

<a name="mail-clients"></a>Clientes de E-mail
===========================================================

Um cliente de e-mail, ou MUA (Mail User Agent), fornece ao usuário uma interface para acessar o e-mail de qualquer provedor de serviço. Os exemplos tradicionais de clientes de e-mail incluem Thunderbird ou Microsoft Outlook (embora ambas aplicações incluem muitas outras funcionalidades). Aproximadamente quase todos clientes de e-mails se comunicam com o provedor de serviço de e-mail usando IMAP ou POP e SMTP, embora alguns também suportam caixas de e-mail local em formato mbox ou Maildir.

Há duas vantagens principais para a abordagem do cliente de e-mail:

1. Contas existentes: Ao usar um cliente de e-mail seguro próprio, um usuário pode continuar usando suas contas de e-mail já existentes.
1. UI Adaptada: Um cliente próprio têm o potencial de repensar a experiência de e-mail do usuário, a fim de melhor comunicar os detalhes relacionados à segurança para o usuário.

A abordagem do cliente de e-mail, no entanto, possui também várias desvantagens:

1. Provedores de serviço inseguro: Um cliente de e-mail não consegue resolver vários dos problemas principais com a segurança do e-mail quando usado com os tradicionais provedores de e-mail. Por exemplo, os metadados não serão protegidos no armazenamento ou no trânsito e o provedor não pode ajudar na descoberta de chave ou validação. Mais importante, muitos dos provedores de e-mails existentes são altamente vulneráveis, uma vez que dependem do DNSSEC para seus registros MX ou validam suas conexões StartTLS para retransmissão do e-mail (quando sequer se preocupam em habilitar StartTLS). Um provedor de e-mail tradicional também requer autenticação por senha que é vista pelo provedor em texto puro e pode ser gravada por eles. Finalmente, a maioria dos provedores de serviços retêm uma quantidade significativa de informação pessoal identificável, como o endereço IP dos clientes.
1. Instalar um novo app: Assim como muitas das outras abordagens, a abordagem do cliente próprio de e-mail requer que os usuários baixem e instalem uma aplicação especializada nos seus dispositivos antes que possam usá-la.

Por fim, o nível da segurança de e-mail possível com a abordagem de cliente próprio de e-mail sempre será limitado. No entanto, esses clientes podem ser uma excelente estratégia para desabituar gradualmente os usuários para sair do e-mail e passar a usar um protocolo diferente e mais seguro. A maior parte dos projetos nessa seção vê o suporte ao e-mail como uma porta de entrada para facilitar uma transição para algo que pode substituir o e-mail.

<a name="bitmail"></a>Bitmail
-----------------------------------------------------------

[bitmail.sf.net](http://bitmail.sf.net)

Bitmail é uma aplicação desktop que proporciona uma interface de usuário para o e-mail tradicional baseado em IMAP, mas também suporta um protocolo próprio peer-to-peer para retransmissão de e-mail através de uma rede de amigos. Bitmail suportará tanto OpenPGP como S/MIME.

**Chaves:** As chaves são validadas usando um segredo compartilhado ou validação por impressão digital. As chaves públicas são descobertas através de uma rede p2p. As chaves são armazenadas localmente num banco de dados criptografado.

**Roteamento:** Bitamil usa um "protocolo de eco" para o roteamento p2p de mensagens, onde cada mensagem é enviada para todos os vizinhos.

**Aplicação:** Bitmail usa a biblioteca Qt para uma interface multi-plataforma.

Há também alguns planos para incluir uma extensão MUA do Bitmail.

* Escrito em: C
* Código fonte: http://sourceforge.net/projects/bitmail/files 
* Documentação do projeto: http://sourceforge.net/p/bitmail/code/HEAD/tree/branches/BitMail.06.2088_2013-11-03/BitMail/branches/BitMail/Documentation/
* Licença: GPL v2
* Platformas: Windows (planejado para Mac e Linux).

Nota: Eu não tenho certeza de quais dos recursos anteriores estão planejados e quais estão funcionando atualmente.

<a name="mailpile"></a>Mailpile
-----------------------------------------------------------

[mailpile.is](http://mailpile.is)

Mailpile é um cliente de e-mail planejado para lidar rapidamente com uma grande quantidade de e-mail e também suporte amigável a criptografia. O foco inicial é no e-mail, com planos de eventualmente suportar protocolos pós-e-mail como bitmessage, flowingmail ou darkmail. Os desenvolvedores esperam adicionar também suporte para bate papo em XMPP no futuro. Já que a fundação Mozilla não têm se comprometido com os recursos necessários para manter o Thunderbird atualizado, a iniciativa Mailpile mantém muitas promessas como um cliente de e-mail multi-plataforma que busca reformular como nós interagimos com o e-mail.

**Chaves:** A criptografia de e-mail do Mailpile é baseada no OpenPGP (ele usa o seu chaveiro GPG). A descoberta da chave será gerenciada usando os servidores de chave OpenPGP e inclui as chaves públicas como anexo nos e-mails enviados. As chaves públicas são confiadas no primeiro uso, com planos de validação via DANE e verificação manual de impressão digital (no futuro terá suporte para um protocolo p2p e poderá incluir mais métodos, como Certificate Transparency ou Short Authentication Strings). Atualmente, não é feito backup das chaves.

**Aplicação:** A interface do Mailpile é escrita usando HTML5 e Javascript, roda sob uma aplicação auto-hospedada em Python (que geralmente roda localmente no dispositivo, mas pode ser executada no seu próprio servidor).

**Limitações:** O Mailpile não possui atualmente um esquema de recuperação se seu dispositivo é destruído ou algum método de sincronização segura das chaves entre os dispositivos. Embora o índice de busca é armazenado criptografado em disco (se o usuário já tiver GPG instalado e um par de chave gerado), é criptografado de uma forma que requer que o índice seja carregado totalmente na memória. O Mailpile está em desenvolvimento muito ativo, então essa e outras questões podem mudar num futuro próximo.

* Escrito em: Python, Javascript
* Código fonte: http://github.com/pagekite/mailpile
* Documentação do projeto: http://github.com/pagekite/mailpile
* Licença: AGPL & Apache
* Platformas: Windows, Mac, Linux (com planejamento para Android e iOS).
* Contato: team@mailpile.is

<a name="parley"></a>Parley
-----------------------------------------------------------

[parley.co](https://parley.co)

Parley é um cliente de e-mail para desktop com uma UI escrita usando HTML5 e Javascript, com um backend local escrito em Python.

**Chaves:** Embora Parley pode ser usado com qualquer provedor de serviço, os servidores Parley são usados para publicar chaves públicas e fazer o backup das chaves privadas criptografadas pelo cliente. Para descobrir a chave, Parley usa um repositório central e os servidores de chaves do OpenPGP. Para validação de chave, Parley baseia-se na confiança no primeiro uso e na aprovação de chave Parley.

**Infra estrutura:** Todos os usuários do cliente Parley também inscrevem-se para o serviço Parley, mas eles usam sua conta de e-mail existente. O servidor Parley atua como um proxy que usa [context.io](http://context.io) para o armazenamento de e-mail (context.io é um serviço comercial que promove uma REST API para uma conta tradicional de IMAP). O servidor Parley também cuida da descoberta de chaves, validação e backup. Tanto o cliente quanto o servidor são liberados em software livre.

**Aplicação:** Parley é atualmente empacotado em um executável usando [Appcelerator](http://www.appcelerator.com/). O cliente Parley não fala diretamente com IMAP ou SMTP. Ao invés disso, usa uma REST API do e-mail do context.io.

**Limitaões:** Todos os e-mails dos usuários são armazenado pelo context.io, ainda que no formato OpenPGP. Mas, o metadado é exposto para o context.io (além do seu provedor de serviço).

* Escrito em: Python, Javascript
* Código fonte: https://github.com/blackchair/parley
* Documentação do projeto: https://parley.co/#how-it-works
* Licença: BSD
* Platformas: Windows, Mac, Linux (com planejamento para Android e iOS).

<a name="self-hosted-email"></a>E-mail auto hospedado
===========================================================

Tradicionalmente, o e-mail é um protocolo federado: quando você manda um e-mail, ele trafega do seu computador, do servidor do seu provedor de e-mail, para o servidor do provedor do destinatário e finalmente para o computador do destinatário. A ideia chave com e-mail auto hospedado é cortar o intermediário e você rodar seu próprio servidor de e-mail.

Nos Estados Unidos, muito do interesse em ter um e-mail auto hospedado é impulsionado pela atual interpretação (particularmente estranha) da Suprema Corte da 4 Emenda chamada ["Third-Party Doctrine"](https://en.wikipedia.org/wiki/Third-Party_Doctrine). Basicamente, você tem proteções de privacidade muito mais fracas nos EUA se você confia seus dados a terceiros. Além disso, a Corte tem até agora proporcionado grande proteção para itens fisicamente localizados dentro das casas das pessoas. "Arrá!", dizem os hackers e os advogados, "nós iremos hospedar nossos e-mails em casa".

Infelizmente, não é assim tão simples. Há vários grandes desafios para colocar servidores de e-mails nas casas de todo mundo:

* **Reputação delegada**: A atual infra estrutura de e-mail é basicamente um sistema de reputação delegada. Para sermos capazes de enviar e-mail para a maioria dos provedores e não ter uma grande porcentagem deles marcados como Spam, um provedor de e-mail deve gradualmente construir uma boa reputação. Os usuários são capazes de enviar e-mail porque seus provedores têm cultivado sua reputação e mantendo-a através do fechamento de contas abusivas. É certamente possível rodar um provedor de e-mail com um único usuário, mas isso é muito mais difícil para construir uma boa reputação. Também muitos provedores bloqueiam todas as tentativas de transmissão de endereços IP que foram marcados como endereços de "casa", na (provável) suposição que a mensagem está vindo por um vírus e não de um legítimo servidor de e-mail.
* **Os servidores estão numa rede hostil**: Pela razão de um servidor precisar ter portas abertas que são publicamente acessíveis pela internet o tempo todo, rodar um servidor é muito mais complicado do que um simples computador desktop. É muito mais crítico ter certeza que as atualizações são aplicada periodicamente e de que você é capaz de responder a ataques externos, como "bombas de spam". Qualquer endereço de IP público que é colocado na internet aberta será continuadamente explorado por vulnerabilidades. A auto-hospedagem irá provavelmente funcionar bem com um protocolo como o Pond, onde há restrições estritas de quem pode entregar as mensagens recebidas. O e-mail, no entanto, é um protocolo amplamente aberto e propenso ao abuso.
* **Sysadmins não são robôs**: Ninguém encontrou um jeito de como fazer servidores auto-arrumáveis que não precisam de um administrador de sistema (sysadmin) hábil para mantê-lo saudável. Quando alguém conseguir, um monte de sysadmins estarão sem emprego, mas no presente momento eles não estão muito preocupados com isso. Há várias coisas que em comum que dão errado nos servidores, como atualizações que dão errado, discos cheios, serviços travando, vazamento de memória, falha de hardware e por aí vai.
* **Não resolve os problemas importantes**: Mover fisicamente a localização do dispositivo não resolve em nada os problemas difíceis associados ao e-mail seguro fácil de usar (como a disponibilidade de dados e a validação de chave). Algumas das abordagens para esses problemas dependem da infraestrutura de provedor de serviço que seria inviável de se auto hospedar.
* **DNS é difícil**: Um dos problemas importantes de segurança com o e-mail tradicional é a vulnerabilidade dos registros MX de DNS. Fazer o DNS corretamente é difícil e não é algo que pode ser esperado do usuário comum.

O e-mail auto hospedado é um "hack legal" intrigante, embora enfrente vários desafios técnicos.

<a name="self-hosted-dark-mail"></a>Dark Mail Alliance
-----------------------------------------------------------

O Dark Mail Alliance afirmou que eles querem suportar a auto hospedagem para o componente de servidor do sistema. Não há detalhes ainda.

<a name="freedombox"></a>FreedomBox
-----------------------------------------------------------

[freedomboxfoundation.org](https://freedomboxfoundation.org)

Da sua concepção inicial, parte do FreedomBox era "e-mail e telecomunicações que protege a privacidade e resiste à espionagem". O e-mail, no entanto, não está atualmente sendo desenvolvido como uma parte do FreedomBox. (até onde posso dizer).

<a name="self-hosted-mailpile"></a>Mailpile
-----------------------------------------------------------

Apesar do Mailpile ser primeiramente um cliente de e-mail, o componente base em Python pode ler o formato Maildir para e-mail. Isso significa que você poderia instalar o Mailpile em seu próprio servidor rodando um Agente de Transporte de E-mail (MTA - Mail Transfer Agent) como postfix ou qmail. Você poderia então acessar o seu e-mail remotamente conectando em seu servidor via navegador web.

<a name="kinko"></a>kinko
-----------------------------------------------------------

[kinko](https://kinko.me) implementa um SMTP criptografado e um proxy IMAP num hardware de classe ARM, a máquina kinko(kinko box). Os e-mails são sincronizados nas contas de e-mail dos usuários via IMAP para a máquina e são guardados em texto puro numa área de armazenamento seguro na máquina. A máquina Kinko também inclui um webmail para ser possível usar o e-mail com o navegador.

As conexões para a máquina kinko são protegidas por TLS usando uma chave privada apenas conhecida pela própria máquina. Além disso, a máquina kinko faz um túnel para uma localização na internet pública. Consequentemente, os usuários podem acessar seu e-mail seguro de qualquer lugar, usando clientes de e-mail compatíveis com IMAP e/ou navegadores, incluindo clientes móveis.

Kinko usa GnuPG para criptografia, com a possibilidade de criptografar o assunto do e-mail. Complementos adicionais devem permitir as "alternativas pós-email" (a la bitmessage) de serem usadas com clientes de e-mail que os usuários já usam hoje. Outros complementos ligados à privacidade estão previstos também.

**Descoberta de chave e validação:** Usuários podem fazer upload dos chaveiros PGP existentes. As chaves PGP são descobertas pelo endereço de e-mail, pelo conteúdo de e-mail e por servidores de chaves PGP. As chaves são confiadas no primeiro uso (mas a política pode ser mudada para validação explícita por impressão digital).

**Status do projeto:** um protótipo alfa está disponível. Nós estamos preparando o lançamento de um pacote beta no segundo trimestre de 2014.

**Linguagens:** O sistema base do kinko é implementado em ruby e shell, com pequenas porções em código nativo.

**Webmail:** A aplicação atual de webmail incluída é o roundcube. É possível que isso mude no futuro. 

**Licenças:** Todo o sistema kinko será liberado sob licença AGPL. (Incluindo as aplicações de terceiros que vamos usar as respectivas licenças de código aberto). O hardware é de código aberto conforme [olimex](https://www.olimex.com/wiki/A10-OLinuXino-LIME).

<a name="email-infrastructure"></a>Infraestrutura de E-mail
===========================================================

Os projetos de "infraestrutura" dão a um provedor de serviço a oportunidade de oferecer contas de e-mails seguro para usuários finais. Ao modificar como ambos clientes e servidores de e-mail funcionam, esses projetos têm o potencial de implementar maiores medidas de segurança do que é possível apenas com a abordagem com o cliente. Por exemplo:

* Transmissão criptografada: Um provedor de e-mail seguro é capaz de suportar, e endossar, o tráfego criptografado quando transmitir o e-mail para outros provedores. Isso é um importante mecanismo para prevenir a vigilância em massa dos metadados (os quais não seriam protegidos pela criptografia de conteúdo da mensagem com o OpenPGP pelo cliente).
* Facilitação do gerenciamento de chave: Um provedor de e-mail seguro pode endossar as chaves públicas dos seus usuários e fornecer assistência para vários esquemas de validação automática, que de outra forma é complicado para os usuários gerenciarem.
* Atualização invisível para protocolos melhores: Um provedor de e-mail seguro possui o potencial para suportar múltiplos protocolos vinculados a um único endereço usuario@dominio, permitindo atualizações automáticas e invisíveis para protocolos pós-email mais seguros quando ambas as partes detectarem a disponibilidade.
* Um retorno para federação: A recente concentração do e-mail em alguns grandes provedores reduz enormemente a saúde e resiliência do e-mail como um protocolo aberto, pois agora apenas uns poucos atores monopolizam o meio. Projetos que buscam tornar mais fácil  oferecer um e-mail seguro como um serviço, tem um grande potencial de mudar essa tendência.
* DNS seguro: Um provedor seguro pode suportar DNSSEC e DANE, enquanto a maioria dos outros provedores de e-mail provavelmente não farão muito em breve. Isso é muito importante, pois é fácil sequestrar os registros MX de um domínio sem DNSSEC.
* Retenção de dados mínima: Um provedor de serviço que segue as "boas práticas" escolherá reter menos informação de identificação pessoal dos seus usuários, como o endereço de IP de suas casas.

O objetivo de ambos projetos nessa categoria é construir sistemas onde o provedor de serviço não é confiável e não consegue comprometer a segurança dos seus usuários.

Apesar do potencial dessa abordagem, há vários fatores desconhecidos que podem limitar o seu atrativo:

* Para se beneficiar de um provedor mais seguro, o usuário precisará mudar sua conta de e-mail e seu endereço de e-mail, uma barreira muito alta para a adoção.
* Onde antes havia vários provedores que ofereciam o serviço de e-mail, não é mais claro se há demanda de usuários para sustentar muitos provedores de e-mail ou para a oferta de provedores interessados em oferecer e-mail como um serviço.
* Os usuários devem fazer o download e instalar uma aplicação própria.

<a name="dark-mail-alliance"></a>Dark Mail Alliance
-----------------------------------------------------------

[darkmail.info](https://darkmail.info)

O Dark Mail Alliance incluirá uma aplicação cliente e um software para servidor. O plano é suportar o tradicional e-mail criptografado (tanto OpenPGP e S/MIME), um novo protocolo federado como o e-mail adaptado do protocolo de mensagem instantânea (SCIMP) do SilentCircle e um protocolo de mensageria peer-to-peer pura. O cliente e o servidor serão ambos disponibilizados como software livre.

**Chaves:** O par de chaves será gerado no dispositivo do usuário e feito upload para o provedor de serviço. A [Transparência de Certificado](http://certificate-transparency.org) será usada para validação automática da aprovação pelo provedor de serviço destas chaves públicas. O Dark Mail planeja futuramente adicionar suporte na confirmação da impressão digital, short authentication strings e o segredo compartilhado para validação manual de chave. A descoberta automática de chaves públicas ocorrerá usando DNS, HTTPS e através das próprias mensagens.

**Roteamento:** O protocolo de mensageria pós-email promete ter forward secrecy e proteção contra análise de metadados (os detalhes ainda não foram publicados e o SCIMP não suporta atualmente proteção dos metadados). O Dark Mail Alliance planeja futuramente suportar mensageria peer-to-peer pura usando a impressão digital da chave como o identificador do usuário.

**Infraestrutura:** O Dark Mail planeja suportar três tipos de arquiteturas: tradicional cliente/servidor, auto hospedagem e peer-to-peer pura. Não há detalhes ainda de como irão funcionar.

**Aplicação:** A aplicação cliente funcionará com qualquer MUA existente ao dispor de um servidor local IMAP/SMTP que o MUA consiga se conectar.

**Limitações:** O Dark Mail não lançou nenhum código ou documentos do projeto. No entanto, eles certamente possuem recursos para executar seus planos.

* Escrito em: C
* Código fonte: nada ainda
* Documentação do projeto: nada ainda
* Licença: planejada ser compatível com OSI
* Platformas: inicialmente Android e iOS, seguido por Windows, OS X, Linux e Windows Phone.
* Contato: press@darkmail.info

<a name="leap"></a>LEAP Encryption Access Project
-----------------------------------------------------------

[leap.se](https://leap.se)

O LEAP inclui tanto uma aplicação cliente como um sistema turn-key (suite completa e automaticamente configurável) para automatizar o processo de rodar um provedor de serviço seguro. Atualmente, isso inclui registro de usuário e gerenciamento, tickets de ajuda, sistema de fatura, serviço de VPN e serviço de e-mail seguro. O serviço de e-mail seguro é baseado em OpenPGP.

**Chaves:** Os pares de chaves são gerados no dispositvo do usuário. As chaves e todos os dados do usuário são armazenados num banco de dados criptografado pelo cliente e é sincronizado entre os dipositivos do usuário e copiado para o provedor de serviço. As chaves são validadas automaticamente usando uma combinação de endossamento pelo provedor e da perspectiva de rede (em breve). As chaves são descobertas via servidores de chaves OpenPGP, pelo cabeçalho OpenPGP, os rodapés de e-mail e um protocolo próprio de descoberta baseado em HTTP.

**Infraestrutura:** O LEAP segue uma arquitetura federada tradicional cliente/servidor. O cliente foi concebido para trabalhar com qualquer provedor de serviço compatível com o LEAP (com planos futuros de suportar antigos provedores de IMAP). Por questões de segurança, os usuários são encorajados a baixar a aplicação do LEAP e não de seu provedor de serviço.

**Aplicação:** A aplicação cliente funciona com qualquer MUA existente ao dispor de um servidor local IMAP/SMTP que o MUA se conectará. Há uma extensão para o Thunderbird para automatizar a configuração da conta. A aplicação cliente se comunica com o provedor de serviço usando um protocolo próprio para sincronizar os bancos de dados criptografados. A aplicação é um pequeno programa em C que inicia o código em Python. A interface de usuário é escrita usando Qt.

**Limitações:** Na atual implementação, as propriedades de segurança de forward secrecy e da produção dos metadados não são ponto a ponto. Ao invés disso, o cliente depende do provedor de serviço para garantir essas propriedades. Isso é devido algumas limitações inerentes aos protocolos existentes para o e-mail seguro. Como em muitos dos outros projetos, o plano do LEAP é atualizar de forma invisível para um protocolo pós-email quando for possível para superar essas limitações.

* Escrito em: Python
* Código fonte: https://leap.se/source
* Documentação do projeto: https://leap.se/docs
* Licença: maior parte GPL v3, algumas em MIT e AGPL.

<a name="post-email-alternatives"></a>Alternativas pós-email
===========================================================

Há vários projetos para criar alternativas ao e-mail que são mais seguros, ainda que parecidos com o e-mail.

Esses projetos compartilham algumas vantagens em comum:

1. **Não confiar em ninguém:** Esses projetos compartilham uma abordagem que tratam a rede e todas as partes envolvidas na rede como potencialmente hostis e não confiáveis. Com essa abordagem, a segurança do usuário só pode ser traída se seu próprio dispositivo estiver comprometido ou o software tiver falhas ou for adulterado, mas o usuário é protegido de ataques contra qualquer provedor de serviço (porque no geral não há um único).
1. **Impressão digital como identificador:** Todos esses projetos também usam a impressão digital da chave pública do usuário como seu identificador único de roteamento para um usuário, permitindo descentralização e nomes únicos. Isso praticamente resolve o problema de validação de chaves, porque basicamente todo identificador *é* uma chave, então não é necessário fazer um mapeamento entre o identificador e sua chave.

Exceto o Pond, todas essas alternativas usam uma abordagem de peer-to-peer pura. Por essa razão, elas enfrentam desafios particulares:

1. **A rede "Natural"**: Muitos dos defensores do avanço das redes peer-to-peer acreditam na noção de que as redes descentralizadas são mais eficientes e achadas em todos os lugares na natureza (nos neurônios em nosso cérebro, na forma como o mofo cresce, como os insetos se comunicam, etc). Essa noção é parcialmente verdadeira. As redes são encontradas na natureza, mas essas redes não são radicalmente descentralizadas. Ao contrário disso, as redes naturais tendem a seguir a lei de potência (também conhecido como "[Redes sem escala](https://en.wikipedia.org/wiki/Scale-free_network)"), onde há um alto grau de centralização parcial de equilibrio entre a "intermediação" (habilidade de se comunicar mais longe numa rede) com a "proximidade" (habilidade de se comunicar próximo numa rede). Desse modo, na prática, as redes digitais dependem de "super hubs" que processam a maior parte do tráfego. Esses hubs precisam ser mantidos e hospedados por alguém, frequentemente com alto custo (e fazendo a rede muito mais vulnerável a ataque Sybil).
1. **A Internet:** Infelizmente, a infraestrutura física da internet é, na verdade, muito mais policêntrica do que descentralizada (mais semelhante a uma árvore do que uma teia de uma aranha). Uma das razões do surgimento da computação na nuvem é que os recursos são muito mais baratos no centro da internet do que nas periferias. As estratégias técnicas que tentam potencializar a periferia sempre estarão em desvantagem do ponto de vista de eficiência.
1. **Análise de Tráfego:** A maior parte das abordagens peer-to-peer transmitem diretamente as mensagens do dispositivo do remetente para o dispositivo do destinatário, ou trafega as mensagens pelos contatos dos participantes. Tal método de tráfego de mensagem torna potencialmente muito fácil para um observador da rede mapear as associações da rede, mesmo se o protocolo de mensagem oferecesse uma proteção muito forte para os metadados.
1. **Ataques Sybil:** Pela sua natureza, redes peer-to-peer não possuem um método para bloquear a participação na rede. Isso torna-as potencialmente muito vulneráveis aos ataques [Sybil attacks](https://en.wikipedia.org/wiki/Sybil_attack), onde um atacante cria uma grande quantidade de número de participantes falsos para controlar a rede ou revelar a identidade de outros participantes da rede.
1. **Móvel:** As redes peer-to-peer consomem muitos recursos, normalmente cada nó na rede é responsável por transmitir continuadamente o tráfego e manter a rede saudável. Infelizmente, esse tipo de coisa consome muita bateria dos dispositivos móveis e requerem consumo de tráfego extra.
1. **Identificadores**: Usando impressões digitais de chaves como identificadores únicos há algumas vantagens, mas também faz os identificadores de usuários serem impossíveis de se memorizar. Há muitas utilidades na conveniência de nome de usuários memoráveis, como se mostra o uso dos endereços de e-mail e twitter.
1. **Disponibilidade de Dados**: Ao menos que for pareado com um componente de nuvem, as redes peer-to-peer tem muito menos disponibilidade de dados do que as outras abordagens. Por exemplo, é muito mais demorado a atualização do recebimento de mensagem de uma rede peer do que de um servidor, particularmente quando o dispositivo estiver offline há algum tempo. E também, se um dispositivo for perdido ou destruído, geralmente o usuário perde todos seus dados.

A maioria desses desafios tem soluções técnicas possíveis que podem fazer o método peer-to-peer a opção mais atraente a longo prazo. Por exemplo, pesquisadores podem descobrir formas de fazer as redes p2p consumirem menos bateria. Por essa razão, é importante que as pesquisas continuem nessa área. No entanto, [a longo prazo estaremos todos mortos](https://en.wikiquote.org/wiki/John_Maynard_Keynes), a abordagem peer-to-peer enfrenta sérias barreiras para atingir o tipo de experiência de usuário demandada hoje.

<a name="bitmessage"></a>Bitmessage
-----------------------------------------------------------

[Bitmessage](https://bitmessage.org)

Bitmessage é um protocolo de comunicação peer-to-peer parecido com e-mail. É totalmente descentralizado e não estabelece nenhuma confiança em nenhuma organização para seus serviços ou validação.

Vantagens:

* resistente a análise de metadados
* relativamente fácil de usar
* funciona e é usada ativamente por muitas pessoas.

Desvantagens:

* não há forward secrecy
* problemas de escala não resolvidos: todas as mensagens são transmitidas para todo mundo
* por não ter forward secrecy, é especialmente problemático que todo mundo consegue pegar uma cópia criptografada de qualquer mensagem no sistema. Isso significa que se a chave privada for alguma vez comprometida, então todo o tráfego de mensagens pode ser decifrado facilmente por qualquer um no sistema.
* depende da prova de trabalho (proof of work) para a prevenção de spam, o que não é realmente preventivo (spammers muitas vezes roubam CPU de algum jeito).

<a name="cables"></a>Cables
-----------------------------------------------------------

(Cables)[https://github.com/mkdesu/cables]

* Escrito em: C, Bash
* Licença: GPL v2

<a name="p2p-dark-mail-alliance"></a>Dark Mail Alliance
-----------------------------------------------------------

O Dark Mail Alliance planeja incorporar o e-mail tradicional, uma alternativa federada de e-mail e uma segunda alternativa ao e-mail que é puro peer-to-peer. Os detalhes ainda não estão disponíveis.

<a name="flowingmail"></a>FlowingMail
-----------------------------------------------------------

[FlowingMail](http://flowingmail.com)

P2P seguro, sistema de e-mail criptografado.

<a name="goldbug"></a>Goldbug
-----------------------------------------------------------

http://goldbug.sf.net

* Escrito em: C++, Qt
* Licença: BSD

<a name="pond"></a>Pond
-----------------------------------------------------------

[pond.imperialviolet.org](https://pond.imperialviolet.org/)

Pond é uma aplicação de mensageria parecida com e-mail com vários elementos arquitetônicos e criptográficos que tornam-o um destaque nesse campo.

**Criptografia de Mensagem**: Pond usa [Axolotl](https://github.com/trevp/axolotl/wiki) para mensagens assíncronas com forward secrecy onde a chave é frequentemente gerada  (semelhante ao OTR, mas mais robusto).

**Tráfego**: Pond usa uma arquitetura única onde cada usuário depende de um provedor de serviço para receber as mensagens, porém, as mensagens enviadas são entregues diretamente para o servidor do destinatário (com Tor). Isso permite uma forte proteção dos metadados, mas não sofre dos outros problemas que geralmente afetam os sistemas peer-to-peer. Para prevenir o excesso de Spam nesse esquema, Pond usa um sistema inteligente de grupos de assinaturas para permitir que o servidor verifique se um emissor está autorizado a entregar a um usuário em particular sem vazamento de qualquer informação do servidor.

**Chaves**: Pond usa Panda, um sistema para validação segura em par usando Short Authentication String.

As vantagens do Pond incluem:

* Alto padrão de segurança: forward secrecy, proteção de metadados, resistente a análise de tráfego.
* A abordagem híbrida (federada e peer-to-peer) é bacana e promissora.
* Escrito em Go, portanto provavelmente possui menos falhas de segurança que programas escrito em C ou C++.
* Pond foi escrito por Adam Langley, um cripto-engenheiro extremamente respeitado.

As desvantagens do Pond incluem:

* Uso de identificadores únicos humanamente não memoráveis, embora isso não seja um elemento necessário do projeto.
* Requer que você tenha estabelecido contato anterior, antes que você possa se comunicar com eles (por Short Authentication String ou troca de chave pública).
* Pond ainda é muito difícil de se instalar e usar.

Pond é um experimento excitante em como você poderia construir um protocolo muito seguro pós-email. Embora atualmente o Pond utilize identificadores não-humanos, o Pond poderia ser facilmente modificado para usar o identificador tradicional de e-mail usuario@dominio (porque ele depende de provedores de serviços para o recebimento de mensagens). O requerimento no Pond que ambas as partes tenham pre-trocado as chaves também poderia ser modificado para permitir que os usuários estabeleçam endereços que eles recebam mensagens de qualquer um, embora isso levaria ao Spam. Atualmente, o Pond usa o Tor para anonimizar o tráfego de mensagens, mas a rede Tor foi planejada para a baixa latência. Pond poderia potencialmente usar uma rede de anonimato mais segura que fosse projetada para mensagens assíncronas de maior latência.

Por fim, o design único do Pond torna-o um candidato muito forte para a incorporação numa aplicação de mensagens que poderia atualizar automaticamente do e-mail para o Pond ao detectar que ambas as partes suportam.

* Escrito em: Go
* Código fonte: https://github.com/agl/pond
* Licença: BSD
* Platformas: qualquer uma que se consiga compilar Go (por interface de linha de comando) ou Go + Gtk (para interface GUI).

<a name="unclassified"></a>Não classificado
===========================================================

<a name="startmail"></a>Startmail
-----------------------------------------------------------

[startmail.com](http://startmail.com)

Os desenvolvedores do mecanismo de busca segura [startpage.com](https://startpage.com) anunciaram que vão fornecer serviço de e-mail seguro. Além do fato de que o serviço será compatível com o e-mail OpenPGP, nenhum outro detalhe foi fornecido.

<a name="related-works"></a>Trabalhos Relacionados
===========================================================

Há muitas tecnologias que não pertencem a esse documento porque ou (a) não estão tentando tornar mais fácil a comunicação criptografada parecida com e-mail, (b) usam alguma forma de proteção proprietária estranha ou (c) nós ainda não sabemos o suficiente sobre eles. Aqui está o lugar para colocar os links para esses projetos.

* [Virtru](https://www.virtru.com) tem um produto de e-mail seguro que depende numa chave escrow centralizada. Para detalhes, veja [aqui](http://www.theregister.co.uk/Print/2014/01/24/ex_nsa_cloud_guru_email_privacy_startup) e [aqui](https://www.virtru.com/how-virtru-works).

* [OpenCom](http://opencom.io) é um e-mail seguro e comunicação parecida com e-mail nos primeiros estágios.
* [Ubiquitous Encrypted Email](https://github.com/tomrittervg/uee) é um rascunho de protocolo para os padrões que poderiam levar à adoção universal do e-mail criptografado.
* [Redecentralize](https://github.com/redecentralize/alternative-internet) possui uma lista de redes descentralizadas, como o Tor.
