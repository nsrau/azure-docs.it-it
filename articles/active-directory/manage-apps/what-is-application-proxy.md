---
title: Pubblicare app locali con Azure AD Application Proxy
description: Questo articolo illustra i motivi per cui usare Application Proxy per pubblicare all'esterno applicazioni Web locali per gli utenti remoti. Descrive inoltre l'architettura di Application Proxy, i connettori, i metodi di autenticazione e i vantaggi offerti per la sicurezza.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66403a18be8337939d457c061b07de948c3e34e8
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66731000"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Uso di Azure AD Application Proxy per pubblicare app locali per gli utenti remoti

Azure Active Directory (Azure AD) offre molte funzionalità per la protezione di utenti, app e dati nel cloud e nell'ambiente locale. In particolare, la funzionalità Azure AD Application Proxy può essere implementata da professionisti IT che vogliono pubblicare all'esterno applicazioni Web locali. Con questa funzionalità, gli utenti remoti possono accedere alle app interne in modo sicuro.

La possibilità di accedere in modo sicuro alle app interne dall'esterno della rete risulta ancora più utile nell'ambiente di lavoro moderno. Con la diffusione degli scenari di tipo BYOD (Bring Your Own Device) e dei dispositivi mobili, i professionisti IT sono chiamati a soddisfare due obiettivi:

* Garantire la produttività degli utenti finali sempre e ovunque
* Assicurare la protezione costante degli asset aziendali

Molte organizzazioni ritengono di poter tenere tutto al sicuro e sotto controllo mantenendo le risorse entro i limiti della rete aziendale. Al giorno d'oggi, tuttavia, l'ambiente di lavoro digitale ha esteso questi limiti, con dispositivi mobili, risorse e servizi gestiti nel cloud. La sfida attuale è quella di gestire la complessità legata alla protezione delle identità degli utenti e dei dati archiviati su dispositivi e app.

Probabilmente si sta già usando Azure AD per gestire gli utenti nel cloud che devono accedere a Office 365, ad altre applicazioni SaaS e anche ad app Web ospitate in locale. Se si ha già Azure AD, è possibile sfruttarlo come singolo piano di controllo per consentire un accesso semplice e sicuro alle applicazioni in locale. Se invece si sta ancora valutando l'opportunità di passare al cloud, è possibile iniziare il proprio percorso verso il cloud implementando Application Proxy, compiendo così il primo passo verso la creazione di una solida base di gestione delle identità.

L'elenco seguente illustra alcune delle opportunità offerte dall'implementazione di Application Proxy in uno scenario di coesistenza ibrida:

* Pubblicazione delle app Web locali all'esterno in modo semplice senza una rete perimetrale
* Supporto per l'accesso SSO (Single Sign-On) a dispositivi, risorse e app nel cloud e nell'ambiente locale
* Supporto per l'autenticazione a più fattori per le app nel cloud e nell'ambiente locale
* Possibilità di sfruttare rapidamente le funzionalità del cloud con la sicurezza della piattaforma Microsoft Cloud
* Gestione centralizzata degli account utente
* Controllo centralizzato delle identità e della sicurezza
* Aggiunta o rimozione automatiche dell'accesso utente alle applicazioni in base all'appartenenza a gruppi

Questo articolo illustra l'esperienza di accesso SSO offerta agli utenti remoti da Azure AD e Application Proxy. Gli utenti possono connettersi in modo sicuro alle app locali senza una VPN o server dual-homed e regole del firewall. L'articolo contiene informazioni utili per comprendere come Application Proxy offre la possibilità di sfruttare le funzionalità e i vantaggi della sicurezza del cloud per le applicazioni Web locali. Descrive inoltre l'architettura e le topologie che è possibile implementare.

## <a name="remote-access-in-the-past"></a>L'accesso remoto in passato

In passato, il piano di controllo per proteggere le risorse interne da utenti malintenzionati, facilitando l'accesso degli utenti remoti, era incentrato sull'uso della rete perimetrale. Tuttavia, le soluzioni con VPN e proxy inverso distribuite nella rete perimetrale usata dai client esterni per accedere alle risorse aziendali non sono adatte all'ambiente cloud. Le soluzioni di questo tipo presentano in genere gli svantaggi seguenti:

* Costi dell'hardware
* Gestione della sicurezza (applicazione di patch, monitoraggio delle porte e così via)
* Autenticazione degli utenti sul perimetro
* Autenticazione degli utenti ai server Web nella rete perimetrale
* Gestione dell'accesso alla VPN per gli utenti remoti, con la distribuzione e la configurazione del software client VPN, e anche gestione dei server aggiunti a un dominio nella rete perimetrale, che può essere vulnerabile ad attacchi esterni.

Nel mondo attuale incentrato sul cloud, Azure AD offre la soluzione ideale per controllare chi e che cosa entra in una rete. Azure AD Application Proxy si integra con le moderne tecnologie di autenticazione basate sul cloud, come le applicazioni SaaS e i provider di identità. Grazie a questa integrazione, gli utenti possono accedere alle app da qualsiasi luogo. Oltre a essere più adatto all'attuale ambiente di lavoro digitale, Application Proxy offre maggiore sicurezza rispetto alle soluzioni con VPN e proxy inverso ed è più facile da implementare. Gli utenti remoti possono accedere alle applicazioni locali nello stesso modo in cui accedono a Office 365 e alle altre app SaaS integrate in Azure AD. Non è necessario modificare o aggiornare le applicazioni per usare il proxy di applicazione. Inoltre, Application Proxy non richiede l'apertura di connessioni in ingresso attraverso il firewall. Con Application Proxy è tutto più semplice. Basta configurarlo.

## <a name="the-future-of-remote-access"></a>Il futuro dell'accesso remoto

Nell'attuale ambiente di lavoro digitale, gli utenti lavorano ovunque con più dispositivi e app. L'unica costante è l'identità utente. Ecco perché il primo passo da compiere oggi per la protezione di una rete è quello di usare le funzionalità di [gestione delle identità di Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) come piano di controllo della sicurezza. Un modello che usa le identità come piano di controllo è in genere costituito dai componenti seguenti:

* Un provider di identità per tenere traccia degli utenti e delle informazioni correlate.
* Una directory dei dispositivi per gestire un elenco dei dispositivi che hanno accesso alle risorse aziendali. Nella directory sono incluse le informazioni relative ai dispositivi, ad esempio il tipo di dispositivo, l'integrità e così via.
* Un servizio di valutazione dei criteri per determinare se un utente e un dispositivo sono conformi ai criteri stabiliti dagli amministratori della sicurezza.
* La possibilità di concedere o negare l'accesso alle risorse dell'organizzazione.

Con Application Proxy, Azure AD tiene traccia degli utenti che devono accedere alle app Web pubblicate in locale e nel cloud, offrendo un punto di gestione centrale per tali app. Sebbene non sia necessario, è consigliabile anche abilitare l'accesso condizionale di Azure AD. Definendo le condizioni relative alla modalità di autenticazione e accesso degli utenti, è possibile essere più sicuri che le applicazioni siano accessibili solo alle persone autorizzate.

**Nota:** è importante comprendere che Azure AD Application Proxy è stato progettato come alternativa a una soluzione con VPN o proxy inverso per gli utenti mobili (o remoti) che devono accedere alle risorse interne. Non è destinato agli utenti interni alla rete aziendale. Facendo inutilmente uso di Application Proxy, gli utenti interni possono causare problemi di prestazioni imprevisti e indesiderati.

![Azure Active Directory e tutte le app](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Panoramica del funzionamento di Application Proxy

Application Proxy è un servizio di Azure AD che viene configurato nel portale di Azure. Questo servizio consente di pubblicare un endpoint per URL HTTP/HTTPS pubblici esterni nel cloud di Azure, che si connette all'URL di un server applicazioni all'interno dell'organizzazione. Le app Web locali possono essere integrate con Azure AD per supportare l'accesso Single Sign-On. Gli utenti finali possono quindi accedere alle app Web locali nello stesso modo in cui accedono a Office 365 e ad altre app SaaS.

I componenti di questa funzionalità includono il servizio Application Proxy, che viene eseguito nel cloud, il connettore di Application Proxy, un agente semplice che viene eseguito in un server locale, e Azure AD, ovvero il provider di identità. Tutti e tre i componenti interagiscono in modo da offrire all'utente un'esperienza Single Sign-On per accedere alle applicazioni Web locali.

Dopo la procedura di accesso, gli utenti esterni possono accedere alle applicazioni Web locali usando un URL familiare o il [pannello di accesso di App personali](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) dal desktop o dai dispositivi iOS o Mac. Application Proxy può ad esempio consentire l'accesso remoto e Single Sign-On a Desktop remoto, siti SharePoint, Tableau, Qlik, Outlook sul Web e applicazioni line-of-business.

![Architettura di Azure AD Application Proxy](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Authentication

Esistono diversi metodi per configurare un'applicazione per l'accesso Single Sign-On e la scelta del metodo dipende dall'autenticazione usata dall'applicazione. Application Proxy supporta i tipi seguenti di applicazioni:

* Applicazioni Web
* API Web che si vuole esporre ad applicazioni avanzate in dispositivi diversi
* Applicazioni ospitate dietro Gateway Desktop remoto
* App rich client integrate con Active Directory Authentication Library (ADAL)

Application Proxy funziona con le app che usano il protocollo di autenticazione nativo seguente:

* **[Autenticazione integrata di Windows](application-proxy-configure-single-sign-on-with-kcd.md).** Per l'autenticazione integrata di Windows, i connettori di Application Proxy usano la delega vincolata Kerberos per autenticare gli utenti con l'applicazione Kerberos.

Application Proxy supporta anche i protocolli di autenticazione seguenti con integrazione di terze parti o in scenari di configurazione specifici:

* [**Autenticazione basata su intestazione**](application-proxy-configure-single-sign-on-with-ping-access.md). Questo metodo di accesso usa un servizio di autenticazione di terze parti chiamato PingAccess e viene usato quando l'applicazione usa le intestazioni per l'autenticazione. In questo scenario l'autenticazione viene gestita da PingAccess.
* [**Autenticazione basata su moduli o su password**](application-proxy-configure-single-sign-on-password-vaulting.md). Con questo metodo di autenticazione, gli utenti eseguono la procedura di accesso all'applicazione con nome utente e password solo la prima volta. Agli accessi successivi sarà Azure AD a indicare il nome utente e la password all'applicazione. In questo scenario l'autenticazione viene gestita da Azure AD.
* [**Autenticazione SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). L'accesso Single Sign-On basato su SAML è supportato per le applicazioni che usano protocolli SAML 2.0 o WS-Federation. Con l'accesso Single Sign-On SAML, Azure AD esegue l'autenticazione all'applicazione usando l'account Azure AD dell'utente.

Per altre informazioni sui metodi supportati, vedere [Scelta di un metodo di accesso Single Sign-On](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Vantaggi di sicurezza

La soluzione di accesso remoto offerta da Application Proxy e Azure AD offre ai clienti diversi vantaggi per la sicurezza, tra cui:

* **Accesso autenticato**. Application Proxy è particolarmente adatto per pubblicare applicazioni con [preautenticazione](application-proxy-security.md#authenticated-access) al fine di assicurarsi che solo le connessioni autenticate raggiungano la rete. Per le applicazioni pubblicate con preautenticazione, il passaggio di traffico dal servizio Application Proxy all'ambiente locale è consentito solo con un token valido. Per sua natura, la preautenticazione blocca un numero significativo di attacchi mirati, poiché solo le identità autenticate possono accedere all'applicazione back-end.
* **Accesso condizionale**. È possibile applicare controlli più avanzati basati su criteri prima che vengano stabilite connessioni alla rete. L'accesso condizionale consente di definire restrizioni sul tipo di traffico autorizzato ad accedere alle applicazioni back-end. È possibile, ad esempio, creare criteri per definire restrizioni in base alla posizione, al livello di autenticazione e al profilo di rischio dell'utente. Con la progressiva evoluzione dell'accesso condizionale, sono disponibili più controlli per offrire maggiore sicurezza, ad esempio l'integrazione con Microsoft Cloud App Security (MCAS). Questa integrazione consente di configurare un'applicazione locale per il [monitoraggio in tempo reale](application-proxy-integrate-with-microsoft-cloud-application-security.md) sfruttando l'accesso condizionale per monitorare e controllare le sessioni in tempo reale in base a criteri di accesso condizionale.
* **Terminazione del traffico**. Tutto il traffico verso l'applicazione back-end viene terminato nel servizio Application Proxy nel cloud mentre viene ristabilita la sessione con il server back-end. Con questa strategia di connessione, i server back-end non sono esposti al traffico HTTP diretto. Sono protetti in modo più efficace dagli attacchi DoS (Denial of Service) mirati perché il firewall non è soggetto ad attacchi.
* **Tutti gli accessi sono in uscita**. I connettori di Application Proxy usano solo connessioni in uscita verso il servizio Application Proxy nel cloud sulle porte 80 e 443. Senza connessioni in ingresso, non è necessario aprire le porte del firewall per i componenti o le connessioni in ingresso nella rete perimetrale. Tutte le connessioni vengono stabilite in uscita e su un canale sicuro.
* **Intelligenza basata su analisi della sicurezza e Machine Learning (ML)** . Poiché fa parte di Azure Active Directory, Application Proxy può sfruttare [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (per questo servizio è necessaria la [licenza Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)). Azure AD Identity Protection combina l'intelligenza di Machine Learning per la sicurezza con i feed di dati della Microsoft [Digital Crimes Unit](https://news.microsoft.com/stories/cybercrime/index.html) e del [Microsoft Security Response Center](https://www.microsoft.com/msrc) per identificare in modo proattivo gli account compromessi. Identity Protection offre protezione in tempo reale da accessi ad alto rischio. Il servizio prende in considerazione fattori quali l'accesso da dispositivi infetti, attraverso reti anonime o da posizioni atipiche e improbabili per aumentare il profilo di rischio di una sessione. Questo profilo di rischio viene usato per la protezione in tempo reale. Molte di queste segnalazioni ed eventi sono già disponibili tramite un'API per l'integrazione con i sistemi SIEM.

* **Accesso remoto come servizio**. Per abilitare l'accesso remoto non è necessario preoccuparsi di gestire e applicare patch ai server locali. Application Proxy è un servizio Internet di Microsoft ed è quindi sempre possibile ottenere gli aggiornamenti e le patch di sicurezza più recenti. Il software senza patch è tuttora responsabile di un numero elevato di attacchi. Secondo il dipartimento per la sicurezza interna degli Stati Uniti, ben [l'85% degli attacchi mirati può essere evitato](https://www.us-cert.gov/ncas/alerts/TA15-119A). Con questo modello di servizio, non è più necessario sostenere il gravoso carico che comporta la gestione dei server perimetrali né preoccuparsi di applicare le opportune patch.

* **Integrazione di Intune**. Con Intune, il traffico aziendale viene instradato separatamente da quello personale. Application Proxy garantisce che il traffico aziendale sia autenticato. È inoltre possibile usare [Application Proxy insieme alla funzionalità Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) per consentire agli utenti remoti di accedere in modo sicuro a siti Web interni da dispositivi iOS e Android.

### <a name="roadmap-to-the-cloud"></a>Guida di orientamento al cloud

Un altro vantaggio importante offerto dall'implementazione di Application Proxy consiste nell'estensione di Azure AD all'ambiente locale. L'implementazione di Application Proxy è infatti una fase fondamentale del processo di migrazione dell'organizzazione e delle app al cloud. Passando dall'autenticazione locale al cloud, si riduce il footprint locale e si possono sfruttare le funzionalità di gestione delle identità di Azure AD come piano di controllo. Con un intervento minimo di aggiornamento delle applicazioni esistenti, o addirittura senza alcun aggiornamento, sarà possibile accedere alle funzionalità del cloud, come l'accesso Single Sign-On, l'autenticazione a più fattori e la gestione centralizzata. L'installazione dei componenti necessari per Application Proxy è un processo semplice che consente di stabilire un framework di accesso remoto. Inoltre, con il passaggio al cloud, si può usufruire delle funzionalità e degli aggiornamenti più recenti di Azure AD, come la disponibilità elevata e il ripristino di emergenza.

Per altre informazioni sulla migrazione delle app ad Azure AD, vedere il white paper [Migrazione delle applicazioni ad Azure Active Directory](https://aka.ms/migrateapps/whitepaper).

## <a name="architecture"></a>Architettura

Il diagramma seguente illustra in generale come i servizi di autenticazione di Azure AD interagiscono con Application Proxy per offrire agli utenti finali l'accesso Single Sign-On alle applicazioni locali.

![Flusso di autenticazione tramite Azure AD Application Proxy](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Dopo avere eseguito l'accesso all'applicazione tramite un endpoint, l'utente viene reindirizzato alla pagina di accesso di Azure AD. Se sono stati configurati criteri di accesso condizionale, in questa fase vengono verificate condizioni specifiche per determinare la conformità ai requisiti di sicurezza dell'organizzazione.
2. Dopo un accesso corretto, Azure AD invia un token al dispositivo client dell'utente.
3. Il client invia il token al servizio Application Proxy che recupera dal token il nome dell'entità utente (UPN) e il nome dell'entità di sicurezza (SPN).
4. Application Proxy inoltra la richiesta, che viene acquisita dal relativo [connettore](application-proxy-connectors.md).
5. Il connettore esegue le altre operazioni di autenticazione necessarie per conto dell'utente (*facoltative, a seconda del metodo di autenticazione*), richiede l'endpoint interno del server applicazioni e invia la richiesta all'applicazione locale.
6. La risposta del server applicazioni viene inviata al servizio Application Proxy tramite il connettore.
7. La risposta del servizio Application Proxy viene inviata all'utente.

|**Componente**|**Descrizione**|
|:-|:-|
|Endpoint|L'endpoint è un URL o un  [portale per gli utenti finali](end-user-experiences.md). Gli utenti possono raggiungere le applicazioni all'esterno della rete accedendo a un URL esterno. Gli utenti all'interno della rete possono accedere all'applicazione tramite un URL o un portale per gli utenti finali. Quando gli utenti proseguono verso uno di questi endpoint, si autenticano in Azure AD e quindi vengono instradati tramite il connettore all'applicazione locale.|
|Azure AD|Azure AD esegue l'autenticazione usando la directory del tenant archiviata nel cloud.|
|Servizio Application Proxy|Il servizio Application Proxy viene eseguito nel cloud come parte di Azure AD. Il servizio passa il token di accesso dall'utente al connettore Application Proxy. Application Proxy inoltra eventuali intestazioni accessibili nella richiesta e imposta le intestazioni in base al relativo protocollo, all'indirizzo IP del client. Se la richiesta in ingresso per il proxy include già tale intestazione, l'indirizzo IP del client viene aggiunto alla fine dell'elenco delimitato da virgole che rappresenta il valore dell'intestazione.|
|Connettore di Application Proxy|Il connettore è un agente semplice eseguito in un server Windows all'interno della rete. Il connettore gestisce la comunicazione tra il servizio Application Proxy nel cloud e l'applicazione locale. Il connettore usa solo connessioni in uscita, per cui non è necessario aprire porte in ingresso né inserire nulla nella rete perimetrale. I connettori sono senza stato e prelevano le informazioni dal cloud in base alle esigenze. Per altre informazioni sui connettori, ad esempio su come bilanciano il carico ed eseguono l'autenticazione, vedere  [Informazioni sui connettori di Azure AD Application Proxy](application-proxy-connectors.md).|
|Active Directory (AD)|Active Directory viene eseguito in locale per eseguire l'autenticazione per gli account di dominio. Quando si configura l'accesso Single Sign-On, il connettore comunica con AD per eseguire eventuali autenticazioni aggiuntive necessarie.|
|Applicazione locale|Infine, l'utente è in grado di accedere a un'applicazione locale.|

Azure AD Application Proxy è costituito dal servizio Application Proxy basato sul cloud e da un connettore locale. Il connettore rimane in ascolto delle richieste provenienti dal servizio Application Proxy e gestisce le connessioni verso le applicazioni interne. È importante notare che tutte le comunicazioni avvengono su SSL, hanno sempre origine dal connettore e sono indirizzate al servizio Application Proxy. In altre parole, le comunicazioni sono solo in uscita. Il connettore usa un certificato client per l'autenticazione al servizio Application Proxy per tutte le chiamate. L'unica eccezione alla sicurezza della connessione è data dal passaggio di configurazione iniziale in cui viene stabilito il certificato client. Per altri dettagli, vedere le informazioni sul funzionamento [dietro le quinte](application-proxy-security.md#under-the-hood) di Application Proxy.

### <a name="application-proxy-connectors"></a>Connettori di Application Proxy

I [connettori di Application Proxy](application-proxy-connectors.md) sono agenti semplici, distribuiti a livello locale, che facilitano la connessione in uscita verso il servizio Application Proxy nel cloud. I connettori devono essere installati in un server Windows dotato di accesso all'applicazione back-end. Gli utenti si connettono al servizio Application Proxy nel cloud che indirizza il traffico alle app tramite i connettori, come illustrato di seguito.

![Connessioni di rete di Azure AD Application Proxy](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Le operazioni di installazione e registrazione tra un connettore e il servizio Application Proxy vengono eseguite nel modo seguente:

1. L'amministratore IT apre le porte 80 e 443 al traffico in uscita e consente l'accesso agli URL richiesti dal connettore, dal servizio Application Proxy e da Azure AD.
2. L'amministratore accede al portale di Azure e avvia un eseguibile per installare il connettore in un server Windows in locale.
3. Il connettore inizia ad ascoltare le richieste del servizio Application Proxy.
4. L'amministratore aggiunge l'applicazione locale ad Azure AD e configura le impostazioni, ad esempio gli URL che gli utenti devono usare per connettersi alle proprie app.

Per altre informazioni, vedere [Pianificare una distribuzione di Azure AD Application Proxy](application-proxy-deployment-plan.md).

È consigliabile distribuire in ogni caso più connettori per garantire ridondanza e scalabilità. Insieme al servizio, i connettori si occupano di tutte le attività che richiedono disponibilità elevata e possono essere aggiunti o rimossi in modo dinamico. Ogni volta che arriva una nuova richiesta, questa viene indirizzata a uno dei connettori disponibili. Quando un connettore è in esecuzione, rimane attivo quando si connette al servizio. Se un connettore è temporaneamente non disponibile, non risponde a questo traffico. I connettori inutilizzati vengono contrassegnati come inattivi e rimossi dopo 10 giorni di inattività.

Viene inoltre eseguito automaticamente il polling del server per verificare se per i connettori è disponibile una versione più recente. Anche se è possibile eseguire un aggiornamento manuale, i connettori vengono aggiornati automaticamente, purché il servizio Application Proxy Connector Updater sia in esecuzione. In caso di tenant con più connettori, gli aggiornamenti automatici vengono applicati a un connettore per volta in ogni gruppo, al fine di evitare tempo di inattività nell'ambiente in uso.

**Nota**: è possibile monitorare la [pagina della cronologia delle versioni](application-proxy-release-version-history.md) di Application Proxy, per ricevere una notifica del rilascio di aggiornamenti, sottoscrivendo il relativo feed RSS.

Ogni connettore di Application Proxy viene assegnato a un [gruppo di connettori](application-proxy-connector-groups.md). I connettori appartenenti allo stesso gruppo agiscono come singola unità per la disponibilità elevata e il bilanciamento del carico. È possibile creare nuovi gruppi, assegnare connettori a tali gruppi nel portale di Azure e quindi assegnare connettori specifici per gestire determinate applicazioni. È consigliabile avere almeno due connettori in ogni gruppo per garantire disponibilità elevata.

I gruppi di connettori sono utili quando è necessario supportare gli scenari seguenti:

* Pubblicazione di app geografiche
* Segmentazione/isolamento di applicazioni
* Pubblicazione di app Web in esecuzione nel cloud o in locale

Per altre informazioni sulla scelta della posizione dei connettori e sull'ottimizzazione della rete, vedere [Considerazioni relative alla topologia di rete quando si usa Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Altri casi d'uso

Fino a questo momento, la discussione si è concentrata sull'uso di Application Proxy per la pubblicazione di app locali all'esterno della rete, con abilitazione dell'accesso Single Sign-On a tutte le app cloud e locali. È tuttavia opportuno citare altri casi d'uso di Application Proxy, tra cui:

* **Pubblicazione sicura di API REST**. Quando si dispone di logica di business o API eseguite in locale o ospitate su macchine virtuali nel cloud, Application Proxy offre un endpoint pubblico per l'accesso alle API. L'accesso alle API tramite l'endpoint consente di controllare l'autenticazione e l'autorizzazione senza richiedere porte in ingresso. Offre inoltre maggiore sicurezza grazie alle funzionalità di Azure AD Premium, ad esempio l'autenticazione a più fattori e l'accesso condizionale basato su dispositivo per computer desktop e dispositivi iOS, Mac e Android tramite Intune. Per altre informazioni, vedere [Come abilitare le applicazioni client native per l'interazione con applicazioni proxy](application-proxy-configure-native-client-application.md) e [Proteggere un'API usando OAuth 2.0 con Azure Active Directory e Gestione API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Servizi Desktop** **remoto**. Le distribuzioni standard di Servizi Desktop remoto richiedono connessioni in ingresso aperte, mentre la [distribuzione di Servizi Desktop remoto con Application Proxy](application-proxy-integrate-with-remote-desktop-services.md) dispone di una connessione in uscita permanente dal server che esegue il servizio connettore. In questo modo, è possibile offrire un maggior numero di applicazioni agli utenti finali pubblicando le applicazioni locali attraverso Servizi Desktop remoto. È inoltre possibile ridurre la superficie di attacco della distribuzione con un set limitato di controlli di verifica in due passaggi e di accesso condizionale per Servizi Desktop remoto.
* **Pubblicazione di applicazioni che si connettono tramite WebSocket**. Il supporto con [Qlik Sense](application-proxy-qlik.md) è in anteprima pubblica e verrà esteso ad altre app in futuro.
* **Abilitazione di applicazioni client native per l'interazione con le applicazioni proxy**. Oltre che per la pubblicazione di app Web, è possibile usare Azure AD Application Proxy per pubblicare [applicazioni client native](application-proxy-configure-native-client-application.md) configurate con Azure AD Authentication Library (ADAL). Le applicazioni client native si differenziano dalle app Web perché vengono installate in un dispositivo, mentre le app Web sono accessibili tramite un browser.

## <a name="conclusion"></a>Conclusioni

Il modo di lavorare e gli strumenti di lavoro sono in rapida evoluzione. Poiché sempre più dipendenti portano i propri dispositivi al lavoro e fanno ampio uso di applicazioni SaaS (Software as a Service), anche il modo in cui le organizzazioni gestiscono e proteggono i propri dati deve evolversi. Le aziende non operano più esclusivamente all'interno delle proprie mura, protette da una barriera di sicurezza. I dati sono sempre più in movimento e questo fenomeno ha assunto proporzioni senza precedenti, attraverso ambienti locali e cloud. Questa evoluzione ha consentito di incrementare la produttività degli utenti e le opportunità di collaborazione, ma ha reso più impegnativa l'attività di protezione dei dati sensibili.

Indipendentemente dal fatto che si usi Azure AD per gestire gli utenti in uno scenario di coesistenza ibrida o che si sia interessati ad affrontare il passaggio al cloud, l'implementazione di Azure AD Application Proxy può contribuire a ridurre le dimensioni del footprint locale, offrendo l'accesso remoto come servizio.

Iniziando oggi stesso a usare Application Proxy, le organizzazioni potranno usufruire dei vantaggi seguenti:

* Pubblicazione di app locali all'esterno, senza i costi associati alla gestione di una VPN tradizionale, o di altre soluzioni per la pubblicazione Web in locale, e all'approccio basato su rete perimetrale
* Accesso Single Sign-On a tutte le applicazioni, sia Office 365 sia altre app SaaS, incluse le applicazioni locali
* Sicurezza a livello di cloud in cui Azure AD sfrutta i dati di telemetria di Office 365 per impedire l'accesso non autorizzato
* Integrazione di Intune per garantire l'autenticazione del traffico aziendale
* Gestione centralizzata degli account utente
* Aggiornamenti automatici per accertarsi di avere le patch di sicurezza più recenti
* Nuove funzionalità appena rilasciate, tra cui le più recenti per il supporto di Single Sign-On SAML e la gestione più granulare dei cookie delle applicazioni

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla pianificazione, sul funzionamento e sulla gestione di Azure AD Application Proxy, vedere [Pianificare una distribuzione di Azure AD Application Proxy](application-proxy-deployment-plan.md).
* Per pianificare una demo dal vivo oppure ottenere una versione di valutazione gratuita per 90 giorni, vedere [Introduzione a Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
