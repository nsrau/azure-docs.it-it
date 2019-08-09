---
title: Pianificare una distribuzione Azure Active Directory Application Proxy
description: Guida end-to-end per la pianificazione della distribuzione del proxy di applicazione all'interno dell'organizzazione
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: cd19d1e0cdfa1b160734b23d7f50310948ded80d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879906"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Pianificare una distribuzione del proxy di applicazione Azure AD

Il proxy di applicazione Azure Active Directory (Azure AD) è una soluzione di accesso remoto sicura e conveniente per le applicazioni locali. Fornisce un percorso di transizione immediato per le organizzazioni "cloud First" per gestire l'accesso alle applicazioni locali legacy che non sono ancora in grado di usare i protocolli moderni. Per ulteriori informazioni introduttive, vedere [che cos'è il proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Il proxy di applicazione è consigliato per concedere agli utenti remoti l'accesso alle risorse interne. Il proxy di applicazione sostituisce la necessità di una VPN o di un proxy inverso per questi casi d'uso di accesso remoto. Non è destinato agli utenti che si trovano nella rete aziendale. Questi utenti che utilizzano il proxy di applicazione per l'accesso alla Intranet potrebbero riscontrare problemi di prestazioni indesiderati.

Questo articolo include le risorse necessarie per pianificare, utilizzare e gestire Azure AD proxy di applicazione. 

## <a name="plan-your-implementation"></a>Pianificare l'implementazione

La sezione seguente offre una panoramica generale degli elementi principali della pianificazione che consentiranno di configurare per un'esperienza di distribuzione efficiente. 

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare l'implementazione, è necessario soddisfare i prerequisiti seguenti. In questa [esercitazione](application-proxy-add-on-premises-application.md)è possibile visualizzare altre informazioni sulla configurazione dell'ambiente, inclusi questi prerequisiti.

* **Connettori**: I connettori sono agenti leggeri su cui è possibile eseguire la distribuzione:
   * Hardware fisico locale
   * Una macchina virtuale ospitata in qualsiasi soluzione hypervisor
   * Una macchina virtuale ospitata in Azure per abilitare la connessione in uscita al servizio proxy di applicazione.

* Per una panoramica più dettagliata, vedere informazioni sui [connettori Proxy app Azure ad](application-proxy-connectors.md) .

     * I computer del connettore devono [essere abilitati per TLS 1,2 prima di](application-proxy-add-on-premises-application.md) installare i connettori.

     * Se possibile, distribuire i connettori nella [stessa rete e nello stesso](application-proxy-network-topology.md) segmento dei server applicazioni Web back-end. È consigliabile distribuire i connettori dopo aver completato l'individuazione delle applicazioni.
     * È consigliabile che ogni gruppo di connettori disponga di almeno due connettori per garantire disponibilità elevata e scalabilità. La presenza di tre connettori è ottimale nel caso in cui sia necessario servire un computer in qualsiasi momento. Esaminare la [tabella](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) relativa alla capacità del connettore per determinare il tipo di computer in cui installare i connettori. Maggiore è il numero di buffer e l'esecuzione del connettore sarà maggiore.

* **Impostazioni di accesso alla rete**: Azure AD connettori del proxy [di applicazione si connettono ad Azure tramite HTTPS (porta tcp 443) e http (porta tcp 80)](application-proxy-add-on-premises-application.md). 

   * Il traffico TLS del connettore di terminazione non è supportato e impedirà ai connettori di stabilire un canale sicuro con i rispettivi endpoint proxy del app Azure.

   * Evitare tutte le forme di ispezione inline sulle comunicazioni TLS in uscita tra i connettori e Azure. L'ispezione interna tra un connettore e le applicazioni back-end è possibile, ma potrebbe influire negativamente sull'esperienza utente e, di conseguenza, non è consigliabile.

   * Anche il bilanciamento del carico dei connettori non è supportato o anche necessario.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerazioni importanti prima della configurazione di Azure AD proxy di applicazione

Per configurare e implementare Azure AD proxy di applicazione, è necessario soddisfare i requisiti di base seguenti.

*  Onboarding di **Azure**: Prima di distribuire il proxy di applicazione, le identità utente devono essere sincronizzate da una directory locale o create direttamente all'interno dei tenant Azure AD. La sincronizzazione delle identità consente ad Azure AD di preautenticare gli utenti prima di concedere loro l'accesso alle applicazioni pubblicate tramite proxy di applicazione e di ottenere le informazioni degli ID utente necessarie per eseguire l'accesso Single Sign-On (SSO).

* **Requisiti di accesso condizionale**: Non è consigliabile usare il proxy di applicazione per l'accesso alla Intranet perché questa operazione aggiunge una latenza che avrà un effetto sugli utenti. Si consiglia di usare il proxy di applicazione con i criteri di pre-autenticazione e di accesso condizionale per l'accesso remoto da Internet.  Un approccio per fornire l'accesso condizionale per l'uso della Intranet consiste nel modernizzare le applicazioni in modo che possano diretly l'autenticazione con AAD. Per altre informazioni, vedere [risorse per la migrazione di applicazioni ad Aad](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) . 

* **Limiti del servizio**: Per evitare il sovraconsumo delle risorse da singoli tenant, sono impostati limiti di limitazione delle richieste per ogni applicazione e tenant. Per visualizzare questi limiti, fare riferimento a [limiti e restrizioni del servizio Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Questi limiti di limitazione sono basati su un benchmark molto superiore al volume di utilizzo tipico e forniscono un ampio buffer per la maggior parte delle distribuzioni.

* **Certificato pubblico**: Se si utilizzano nomi di dominio personalizzati, è necessario ottenere un certificato pubblico rilasciato da un'autorità di certificazione non Microsoft attendibile. A seconda dei requisiti dell'organizzazione, l'ottenimento di un certificato può richiedere del tempo ed è consigliabile iniziare il processo il prima possibile. Applicazione Azure proxy supporta certificati standard, con [caratteri jolly](application-proxy-wildcard.md)o San.

* **Requisiti di dominio**: L'accesso Single Sign-on alle applicazioni pubblicate mediante la delega vincolata Kerberos (delega vincolata Kerberos) richiede che il server che esegue il connettore e il server che esegue l'app siano aggiunti a un dominio e facciano parte dello stesso dominio o domini trusting.
Per informazioni dettagliate sull'argomento, vedere [delega vincolata Kerberos per l'accesso Single Sign-on](application-proxy-configure-single-sign-on-with-kcd.md) con il proxy di applicazione. Il servizio del connettore viene eseguito nel contesto del sistema locale e non deve essere configurato per l'uso di un'identità personalizzata.

* **Record DNS per gli URL**

   * Prima di usare i domini personalizzati nel proxy di applicazione, è necessario creare un record CNAME in DNS pubblico, consentendo ai client di risolvere l'URL esterno definito personalizzato nell'indirizzo del proxy di applicazione predefinito. La mancata creazione di un record CNAME per un'applicazione che usa un dominio personalizzato impedirà agli utenti remoti di connettersi all'applicazione. I passaggi necessari per aggiungere record CNAME possono variare da provider DNS a provider, quindi è possibile ottenere informazioni su come [gestire i record DNS e i set di record usando il portale di Azure](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Analogamente, gli host del connettore devono essere in grado di risolvere l'URL interno delle applicazioni pubblicate.

* **Diritti e ruoli amministrativi**

   * Per l' **installazione del connettore** sono necessari i diritti di amministratore locale per il server Windows in cui viene installato. Per l'autenticazione e la registrazione dell'istanza del connettore nel tenant di Azure AD, è necessario almeno un ruolo di *amministratore dell'applicazione* . 

   * **Per la pubblicazione e l'amministrazione delle applicazioni** è necessario il ruolo di *amministratore dell'applicazione* . Gli amministratori di applicazioni possono gestire tutte le applicazioni nella directory, incluse le registrazioni, le impostazioni SSO, le assegnazioni di utenti e gruppi e le licenze, le impostazioni del proxy di applicazione e il consenso. Non consente tuttavia di gestire l'accesso condizionale. Il ruolo di *amministratore dell'applicazione cloud* ha tutte le capacità dell'amministratore dell'applicazione, ad eccezione del fatto che non consente la gestione delle impostazioni del proxy di applicazione.

* **Licenze**: Il proxy di applicazione è disponibile tramite la sottoscrizione Azure AD Basic. Per un elenco completo delle opzioni e delle funzionalità di licenza, fare riferimento alla [pagina dei prezzi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) .  

### <a name="application-discovery"></a>Individuazione di applicazioni

Compilare un inventario di tutte le applicazioni nell'ambito pubblicate tramite il proxy di applicazione raccogliendo le seguenti informazioni:

| Tipo di informazioni| Informazioni da raccogliere |
|---|---|
| Tipo di servizio| Ad esempio:  SharePoint, SAP, CRM, applicazione Web personalizzata, API |
| Piattaforma applicativa | Ad esempio:  Windows IIS, Apache in Linux, Tomcat, NGINX |
| Appartenenza al dominio| Nome di dominio completo (FQDN) del server Web |
| Percorso applicazione | Posizione in cui si trova la farm o il server Web nell'infrastruttura |
| Accesso interno | URL esatto utilizzato per accedere all'applicazione internamente. <br> Se una farm, quale tipo di bilanciamento del carico è in uso? <br> Indica se l'applicazione crea contenuto da origini diverse da se stesso.<br> Determinare se l'applicazione funziona su WebSocket. |
| Accesso esterno | Soluzione fornitore che l'applicazione è già esposta all'esterno. <br> URL che si desidera utilizzare per l'accesso esterno. Se SharePoint, verificare che i mapping di accesso alternativi siano configurati in base a [queste indicazioni](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). In caso contrario, sarà necessario definire URL esterni. |
| Certificato pubblico | Se si usa un dominio personalizzato, ottenere un certificato con un nome soggetto corrispondente. Se è presente un certificato, annotare il numero di serie e il percorso da cui è possibile ottenerlo. |
| Tipo di autenticazione| Il tipo di autenticazione supportato dal supporto dell'applicazione, ad esempio di base, l'autenticazione dell'integrazione di Windows, basata su form, basata su intestazioni e attestazioni. <br>Se l'applicazione è configurata per l'esecuzione con un account di dominio specifico, prendere nota del nome di dominio completo (FQDN) dell'account del servizio.<br> Se basato su SAML, l'identificatore e gli URL di risposta. <br> Se basata su intestazione, la soluzione fornitore e il requisito specifico per la gestione del tipo di autenticazione. |
| Nome del gruppo di connettori | Nome logico del gruppo di connettori che verrà designato per fornire il canale e l'accesso SSO a questa applicazione back-end. |
| Accesso a utenti/gruppi | Utenti o gruppi di utenti a cui verrà concesso l'accesso esterno all'applicazione. |
| Requisiti aggiuntivi | Prendere nota di eventuali altri requisiti di accesso remoto o di sicurezza da includere nella pubblicazione dell'applicazione. |

È possibile scaricare il [foglio di calcolo dell'inventario delle applicazioni](https://aka.ms/appdiscovery) per eseguire l'inventario delle app.

### <a name="define-organizational-requirements"></a>Definire i requisiti dell'organizzazione

Di seguito sono elencate le aree per le quali è necessario definire i requisiti aziendali dell'organizzazione. Ogni area contiene esempi di requisiti

 **Accedere**

* Utenti remoti con i dispositivi aggiunti a un dominio o Azure AD aggiunti utenti possono accedere in modo sicuro alle applicazioni pubblicate con accesso Single Sign-on (SSO) semplice.

* Gli utenti remoti con dispositivi personali approvati possono accedere in modo sicuro alle applicazioni pubblicate purché siano registrati in multi-factor authentication e hanno registrato l'app Microsoft Authenticator sul telefono cellulare come metodo di autenticazione.

**Governance** 

* Gli amministratori possono definire e monitorare il ciclo di vita delle assegnazioni degli utenti alle applicazioni pubblicate tramite il proxy di applicazione.

**Sicurezza**

* Solo gli utenti assegnati alle applicazioni tramite l'appartenenza al gruppo o individualmente possono accedere a tali applicazioni.

**Prestazioni**

* Non si verifica alcuna riduzione delle prestazioni dell'applicazione rispetto all'accesso all'applicazione dalla rete interna.

**Esperienza utente**

* Gli utenti sono consapevoli di come accedere alle applicazioni usando URL aziendali comuni su qualsiasi piattaforma per dispositivi.

**Controllo**
* Gli amministratori sono in grado di controllare l'attività di accesso degli utenti.


### <a name="best-practices-for-a-pilot"></a>Procedure consigliate per un progetto pilota

Determinare il tempo e lo sforzo necessari per la completa provvigione di una singola applicazione per l'accesso remoto con Single Sign-on (SSO). A tale scopo, è possibile eseguire un progetto pilota che prende in considerazione l'individuazione iniziale, la pubblicazione e il test generale. L'uso di una semplice applicazione Web basata su IIS già preconfigurata per l'autenticazione integrata di Windows (IWA) consente di stabilire una linea di base, in quanto questa configurazione richiede un impegno minimo per pilotare l'accesso remoto e SSO.

Gli elementi di progettazione seguenti dovrebbero aumentare il successo dell'implementazione pilota direttamente in un tenant di produzione.  

**Gestione connettore**:  

* I connettori svolgono un ruolo fondamentale nella fornitura del canale locale alle applicazioni. L'uso del gruppo di connettori **predefinito** è adeguato per i test pilota iniziali delle applicazioni pubblicate prima di commissionarli nell'ambiente di produzione. Le applicazioni testate possono quindi essere spostate in gruppi di connettori di produzione.

**Gestione delle applicazioni**:

* È molto probabile che i dipendenti ricordino che un URL esterno è familiare e pertinente. Evitare di pubblicare l'applicazione usando i suffissi msappproxy.net o onmicrosoft.com predefiniti. Fornire invece un noto dominio verificato di primo livello, preceduto da un nome host logico, ad esempio *Intranet. < customers_domain >. com*.

* Limitare la visibilità dell'icona dell'applicazione pilota a un gruppo pilota nascondendo l'icona di avvio dal portale app di Azure. Quando è pronto per la produzione, è possibile definire l'ambito dell'app sui rispettivi destinatari, nello stesso tenant di pre-produzione, oppure pubblicare anche l'applicazione nel tenant di produzione.

**Impostazioni di Single Sign-on**: Alcune impostazioni SSO hanno dipendenze specifiche che possono richiedere tempo per la configurazione, quindi evitare ritardi di controllo delle modifiche assicurando che le dipendenze vengano risolte in anticipo. Sono inclusi gli host del connettore di aggiunta al dominio per eseguire l'accesso SSO tramite la delega vincolata Kerberos (delega vincolata Kerberos) e la cura di altre attività che richiedono molto tempo. Ad esempio, la configurazione di un'istanza di accesso al PING, se è necessario un SSO basato su intestazione.

**SSL tra l'host del connettore e l'applicazione di destinazione**: La sicurezza è fondamentale, quindi è consigliabile usare sempre TLS tra l'host del connettore e le applicazioni di destinazione. In particolare, se l'applicazione Web è configurata per l'autenticazione basata su form (FBA), poiché le credenziali utente vengono trasmesse in modo efficace come testo non crittografato.

**Implementare in modo incrementale e testare ogni passaggio**. Eseguire test funzionali di base dopo la pubblicazione di un'applicazione per garantire che tutti i requisiti utente e aziendale siano soddisfatti seguendo le istruzioni seguenti:

1. Verificare e convalidare l'accesso generale all'applicazione Web con la preautenticazione disabilitata.
2. Se l'operazione ha esito positivo, abilitare la pre-autenticazione e assegnare utenti e gruppi. Testare e convalidare l'accesso.
3. Aggiungere quindi il metodo SSO per l'applicazione e testare di nuovo per convalidare l'accesso.
4. Applicare i criteri di accesso condizionale e di autenticazione a più fattori come richiesto. Testare e convalidare l'accesso.

**Strumenti per la risoluzione dei problemi**: Per la risoluzione dei problemi, iniziare sempre convalidando l'accesso all'applicazione pubblicata dal browser nell'host del connettore e verificare che l'applicazione funzioni come previsto. La configurazione più semplice, più facile da determinare la causa principale, si consiglia di provare a riprodurre problemi con una configurazione minima, ad esempio l'uso di un singolo connettore e di nessun SSO. In alcuni casi, gli strumenti di debug Web, ad esempio Fiddler di Telerik, possono risultare indispensabili per la risoluzione dei problemi di accesso o contenuto nelle applicazioni a cui si accede tramite un proxy. Fiddler può anche fungere da proxy per facilitare la traccia e il debug del traffico per le piattaforme mobili, ad esempio iOS e Android, e praticamente qualsiasi elemento che può essere configurato per l'indirizzamento tramite un proxy. Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi](application-proxy-troubleshoot.md) .

## <a name="implement-your-solution"></a>Implementare la soluzione

### <a name="deploy-application-proxy"></a>Distribuire il proxy di applicazione

I passaggi per la distribuzione del proxy dell'applicazione sono trattati in questa [esercitazione per l'aggiunta di un'applicazione locale per l'accesso remoto](application-proxy-add-on-premises-application.md). Se l'installazione non riesce, selezionare risolvere i problemi del **proxy dell'applicazione** nel portale oppure usare la guida alla risoluzione dei [problemi relativi all'installazione del connettore dell'agente proxy di applicazione](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Pubblicare applicazioni tramite il proxy di applicazione

Per la pubblicazione di applicazioni si presuppone che siano stati soddisfatti tutti i prerequisiti e che siano presenti diversi connettori visualizzati come registrati e attivi nella pagina proxy di applicazione.

È anche possibile pubblicare applicazioni usando [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Di seguito sono riportate alcune procedure consigliate da seguire per la pubblicazione di un'applicazione:

* **Usare i gruppi di connettori**: Assegnare un gruppo di connettori designato per la pubblicazione di tutte le rispettive applicazioni. È consigliabile che ogni gruppo di connettori disponga di almeno due connettori per garantire disponibilità elevata e scalabilità. La presenza di tre connettori è ottimale nel caso in cui sia necessario servire un computer in qualsiasi momento. Vedere anche [pubblicare applicazioni in reti e posizioni separate usando i gruppi di connettori](application-proxy-connector-groups.md) per vedere come è possibile usare i gruppi di connettori per segmentare i connettori in base alla rete o alla località.

* **Imposta timeout applicazione back-end**: Questa impostazione è utile negli scenari in cui l'applicazione potrebbe richiedere più di 75 secondi per l'elaborazione di una transazione client. Ad esempio, quando un client invia una query a un'applicazione Web che funge da front-end per un database. Il front-end invia la query al server di database back-end e attende una risposta, ma al momento della ricezione di una risposta, si verifica il timeout del lato client della conversazione. L'impostazione del timeout su Long fornisce 180 secondi per il completamento delle transazioni più lunghe.

* **Usare i tipi di cookie appropriati**

   * **Cookie solo http**: Fornisce sicurezza aggiuntiva con il proxy di applicazione che include il flag HTTPOnly nelle intestazioni di risposta HTTP set-cookie. Questa impostazione consente di attenuare gli exploit come gli attacchi XSS (cross-site scripting). Lasciare questa impostazione su No per i client o gli agenti utente che richiedono l'accesso al cookie di sessione. Ad esempio, il client RDP/MTSC si connette a un Gateway Desktop remoto pubblicato tramite il proxy applicazione.

   * **Cookie protetto**: Quando si imposta un cookie con l'attributo Secure, l'agente utente (app sul lato client) includerà solo il cookie nelle richieste HTTP se la richiesta viene trasmessa su un canale protetto da TLS. Questo consente di ridurre il rischio di compromissione di un cookie su canali di testo non crittografati, pertanto dovrebbe essere abilitato.

   * **Cookie permanente**: Consente di mantenere il cookie di sessione del proxy di applicazione tra le chiusure del browser rimanendo valido finché non scade o non viene eliminato. Usato per gli scenari in cui un'applicazione avanzata, ad esempio Office, accede a un documento all'interno di un'applicazione Web pubblicata, senza che venga richiesta l'autenticazione da parte dell'utente. Abilitare con cautela, tuttavia, poiché i cookie permanenti possono lasciare un servizio a rischio di accesso non autorizzato, se non usato insieme ad altri controlli di compensazione. Questa impostazione deve essere usata solo per le applicazioni meno recenti che non possono condividere cookie tra i processi. È preferibile aggiornare l'applicazione per gestire la condivisione dei cookie tra i processi anziché usare questa impostazione.

* **Converti URL nelle intestazioni**: Questa operazione viene abilitata per gli scenari in cui non è possibile configurare il DNS interno in modo che corrisponda allo spazio dei nomi pubblico dell'organizzazione (a. k. a Split DNS). A meno che l'applicazione non richieda l'intestazione host originale nella richiesta client, lasciare questo valore impostato su Sì. In alternativa è possibile fare in modo che il connettore usi il nome di dominio completo nell'URL interno per il routing del traffico effettivo e il nome di dominio completo nell'URL esterno come intestazione host. Nella maggior parte dei casi questa alternativa deve consentire all'applicazione di funzionare normalmente, quando si accede in modalità remota, ma gli utenti perdono i vantaggi derivanti dalla presenza di una corrispondenza all'interno & URL esterno.

* **Converti gli URL nel corpo dell’applicazione**: Attivare la conversione del collegamento del corpo dell'applicazione per un'app quando si desidera che i collegamenti da tale app vengano tradotti nelle risposte al client. Se abilitata, questa funzione fornisce un tentativo ottimale di conversione di tutti i collegamenti interni individuati dal proxy applicazione nelle risposte HTML e CSS restituite ai client. È utile quando si pubblicano app che contengono collegamenti Absolute hardcoded o NetBIOS ShortName nel contenuto oppure app con contenuto che si collega ad altre applicazioni locali.

Per gli scenari in cui un'app pubblicata si collega ad altre app pubblicate, abilitare la conversione dei collegamenti per ogni applicazione, in modo da avere il controllo dell'esperienza utente a livello di app.

Ad esempio, si supponga di avere tre applicazioni pubblicate mediante il proxy di applicazione e che tutte siano collegate tra loro: Vantaggi, spese e viaggi, oltre a una quarta app, commenti e suggerimenti, che non vengono pubblicati tramite il proxy di applicazione.

![Immagine 1](media/App-proxy-deployment-plan/link-translation.png)

Quando si Abilita la traduzione dei collegamenti per l'app benefits, i collegamenti alle spese e ai viaggi vengono reindirizzati agli URL esterni per tali app, in modo che gli utenti che accedono alle applicazioni dall'esterno della rete aziendale possano accedervi. I collegamenti dalle spese e i viaggi restituiti ai vantaggi non funzionano perché la traduzione dei collegamenti non è stata abilitata per queste due app. Il collegamento ai commenti non viene reindirizzato perché non è presente alcun URL esterno, quindi gli utenti che usano l'app benefits non saranno in grado di accedere all'app feedback dall'esterno della rete aziendale. Vedere informazioni dettagliate sulla [conversione di collegamenti e altre opzioni di reindirizzamento](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Accedere all'applicazione

Sono disponibili diverse opzioni per la gestione dell'accesso alle risorse pubblicate dal proxy di app, quindi scegliere il più appropriato per lo scenario e le esigenze di scalabilità specificati. Gli approcci comuni includono: uso di gruppi locali sincronizzati tramite Azure AD Connect, creazione di gruppi dinamici in Azure AD in base agli attributi utente, uso di gruppi self-service gestiti da un proprietario di risorse o una combinazione di tutti questi. Vedere le risorse collegate per i vantaggi di ognuno di essi.

Il modo più semplice per assegnare agli utenti l'accesso a un'applicazione riguarda le opzioni **utenti e gruppi** dal riquadro a sinistra dell'applicazione pubblicata e l'assegnazione diretta di gruppi o singoli utenti.

![Figura 24](media/App-proxy-deployment-plan/add-user.png)

È anche possibile consentire agli utenti di accedere all'applicazione in modalità self-service assegnando a un gruppo di cui non è attualmente membro e configurando le opzioni self-service.

![Immagine 25](media/App-proxy-deployment-plan/allow-access.png)

Se abilitata, gli utenti saranno in grado di accedere al portale di app Web e richiedere l'accesso ed essere approvati automaticamente e aggiunti al gruppo self-service già consentito o richiedere l'approvazione di un responsabile approvazione designato.

Gli utenti guest possono anche essere [invitati ad accedere alle applicazioni interne pubblicate tramite il proxy di applicazione tramite Azure ad B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Per le applicazioni locali normalmente accessibili in modo anonimo, che non richiedono alcuna autenticazione, potrebbe essere preferibile disabilitare l'opzione che si trova nelle **Proprietà**dell'applicazione.

![Immagine 26](media/App-proxy-deployment-plan/assignment-required.png)


Se si lascia questa opzione impostata su No, gli utenti possono accedere all'applicazione locale tramite App Azure AD proxy senza autorizzazioni, quindi usare con cautela.

Una volta pubblicata, l'applicazione deve essere accessibile digitando il relativo URL esterno in un browser o tramite la relativa [https://myapps.microsoft.com](https://myapps.microsoft.com/)icona all'indirizzo.

### <a name="enable-pre-authentication"></a>Abilita pre-autenticazione

Verificare che l'applicazione sia accessibile tramite il proxy di applicazione che accede tramite l'URL esterno. 

1. Passare a **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni** e scegliere l'app da gestire.

2. Selezionare **Proxy dell'applicazione**.

3. Nel campo **pre-autenticazione** usare l'elenco a discesa per selezionare **Azure Active Directory**e selezionare **Salva**.

Con la pre-autenticazione abilitata, Azure AD consentirà agli utenti di eseguire prima l'autenticazione e, se l'accesso Single Sign-on è configued, l'applicazione back-end verificherà anche l'utente prima che venga concesso l'accesso all'applicazione. Se si modifica la modalità di pre-autenticazione da Passthrough a Azure AD configura anche l'URL esterno con HTTPS, tutte le applicazioni inizialmente configurate per HTTP verranno ora protette con HTTPS.

### <a name="enable-single-sign-on"></a>Abilitazione dell'accesso Single Sign-On

SSO offre la migliore esperienza utente e la massima sicurezza perché gli utenti devono eseguire l'accesso una sola volta per accedere a Azure AD. Una volta che un utente ha eseguito la pre-autenticazione, SSO viene eseguito dal connettore del proxy di applicazione che esegue l'autenticazione all'applicazione locale, per conto dell'utente. L'applicazione back-end elabora l'account di accesso come se fosse l'utente. 

La scelta dell'opzione **PassThrough** consente agli utenti di accedere all'applicazione pubblicata senza che sia necessario eseguire l'autenticazione a Azure ad.

L'esecuzione di SSO è possibile solo se Azure AD è in grado di identificare l'utente che richiede l'accesso a una risorsa, in modo che l'applicazione sia configurata per la pre-autenticazione degli utenti con Azure AD al momento dell'accesso per il funzionamento dell'accesso SSO. in caso contrario, le opzioni SSO verranno disabilitate.

Leggere l' [accesso Single Sign-on alle applicazioni in Azure ad](what-is-single-sign-on.md) per scegliere il metodo SSO più appropriato durante la configurazione delle applicazioni.

###  <a name="working-with-other-types-of-applications"></a>Utilizzo di altri tipi di applicazioni

Azure AD proxy di applicazione può inoltre supportare le applicazioni sviluppate per l'utilizzo della libreria di Autenticazione di Azure AD ([adal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) o Microsoft Authentication Library ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Supporta le app client native utilizzando Azure AD token emessi ricevuti nelle informazioni di intestazione della richiesta client per eseguire la pre-autenticazione per conto degli utenti.

Per informazioni sulle configurazioni disponibili del proxy di applicazione, vedere [pubblicazione di app client native e per dispositivi mobili](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) e [applicazioni basate](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) su attestazioni.

### <a name="use-conditional-access-to-strengthen-security"></a>Usare l'accesso condizionale per rafforzare la sicurezza

La sicurezza delle applicazioni richiede un set avanzato di funzionalità di sicurezza che possono proteggere e rispondere a minacce complesse in locale e nel cloud. Spesso gli utenti malintenzionati ottengono l'accesso alla rete aziendale tramite credenziali utente vulnerabili, predefinite o rubate.  La protezione basata sulle identità Microsoft riduce l'uso delle credenziali rubate gestendo e proteggendo le identità con privilegi e senza privilegi.

Per supportare Azure AD proxy di applicazione, è possibile usare le funzionalità seguenti:

* Accesso condizionale basato su utente e percorso: Proteggere i dati sensibili limitando l'accesso utente in base alla posizione geografica o a un indirizzo IP con [criteri di accesso condizionale basati sulla posizione](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Accesso condizionale basato su dispositivo: Assicurarsi che i dispositivi registrati, approvati e conformi possano accedere ai dati aziendali con [accesso condizionale basato su dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Accesso condizionale basato sull'applicazione: Non è necessario arrestare il lavoro quando un utente non è presente nella rete aziendale. [Proteggi l'accesso al cloud aziendale e alle app locali](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) e Mantieni il controllo con l'accesso condizionale.

* Accesso condizionale basato sul rischio: Proteggi i tuoi dati da hacker malintenzionati con [criteri di accesso condizionale basati sul rischio](https://www.microsoft.com/cloud-platform/conditional-access) che possono essere applicati a tutte le app e a tutti gli utenti, sia in locale che nel cloud.

* Pannello di accesso Azure AD: Con la distribuzione del servizio proxy di applicazione e la pubblicazione sicura delle applicazioni, offrire agli utenti un hub semplice per individuare e accedere a tutte le applicazioni. Aumenta la produttività con le funzionalità self-service, ad esempio la possibilità di richiedere l'accesso a nuove app e gruppi o di gestire l'accesso a queste risorse per conto di altri utenti, tramite il [Pannello di accesso](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Gestire l'implementazione

### <a name="required-roles"></a>Ruoli necessari

Microsoft sostiene il principio di concedere il privilegio meno possibile per eseguire le attività necessarie con Azure AD. [Esaminare i diversi ruoli di Azure disponibili](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e scegliere quello giusto per soddisfare le esigenze di ogni utente. È possibile che alcuni ruoli debbano essere applicati temporaneamente e rimossi al termine della distribuzione.

| Ruolo aziendale| Attività aziendali| Ruoli di Azure AD |
|---|---|---|
| Amministratore del supporto tecnico | In genere, l'utente finale ha segnalato problemi ed esegue attività limitate, ad esempio la modifica delle password degli utenti, l'invalidamento dei token di aggiornamento e il monitoraggio dell'integrità del servizio. | Amministratore supporto tecnico |
| Amministratore identità| Leggere Azure AD accedere ai report e ai log di controllo per eseguire il debug dei problemi correlati al proxy app.| Ruolo con autorizzazioni di lettura per la sicurezza |
| Proprietario dell'applicazione| Creare e gestire tutti gli aspetti delle applicazioni aziendali, le registrazioni di applicazioni e le impostazioni del proxy di applicazione.| Amministratore applicazione |
| Amministratore dell'infrastruttura | Proprietario del rollover del certificato | Amministratore applicazione |

Ridurre al minimo il numero di persone che hanno accesso a informazioni o risorse protette contribuirà a ridurre le probabilità che un attore malintenzionato ottenga l'accesso non autorizzato o un utente autorizzato che influisca inavvertitamente su una risorsa sensibile. 
 
Tuttavia, gli utenti devono comunque eseguire operazioni con privilegi giornalieri, in modo da applicare i criteri di [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) basati su JIT (just-in-Time) per fornire accesso con privilegi su richiesta alle risorse di Azure e Azure ad è l'approccio consigliato per gestire efficacemente l'accesso amministrativo e il controllo.

### <a name="reporting-and-monitoring"></a>Monitoraggio e creazione di report

Azure AD fornisce informazioni aggiuntive sull'utilizzo delle applicazioni e sull'integrità operativa dell'organizzazione tramite i [log di controllo e i report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs). Il proxy di applicazione consente anche di monitorare facilmente i connettori dal portale Azure AD e dai registri eventi di Windows.

#### <a name="application-audit-logs"></a>Log di controllo delle applicazioni

Questi log forniscono informazioni dettagliate sugli accessi alle applicazioni configurate con il proxy di applicazione e il dispositivo e l'utente che accede all'applicazione. I [log di controllo](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs) si trovano nella portale di Azure e nell'API di [controllo](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) per l'esportazione. Inoltre, i [report sull'utilizzo e le informazioni dettagliate](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) sono disponibili anche per l'applicazione.

#### <a name="application-proxy-connector-monitoring"></a>Monitoraggio del connettore del proxy di applicazione

I connettori e il servizio si occupano di tutte le attività che richiedono disponibilità elevata. È possibile monitorare lo stato dei connettori dalla pagina del proxy di applicazione nel portale di Azure AD. Per altre informazioni sul connettore manutenzione, vedere [comprendere Azure ad connettori del proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance).

![Esempio: Connettori del proxy di applicazione Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Registri eventi e contatori delle prestazioni di Windows

I connettori hanno sia log di amministratore che di sessione. I log di amministrazione includono gli eventi principali e i relativi errori. I log di sessione includono tutte le transazioni e i relativi dettagli di elaborazione. I log e i contatori si trovano nei registri eventi di Windows per altre informazioni, vedere [comprendere Azure ad connettori del proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). Seguire questa [esercitazione per configurare le origini dati del registro eventi in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Guida alla risoluzione dei problemi e procedure

Per ulteriori informazioni sui problemi comuni e su come risolverli, vedere la guida alla [risoluzione dei](application-proxy-troubleshoot.md) problemi relativi ai messaggi di errore. 

Gli articoli seguenti illustrano scenari comuni che possono essere usati anche per creare guide per la risoluzione dei problemi per l'organizzazione di supporto. 

* [Problema durante la visualizzazione di una pagina dell'app](application-proxy-page-appearance-broken-problem.md)
* [Il caricamento dell'applicazione richiede troppo tempo](application-proxy-page-load-speed-problem.md)
* [I collegamenti nella pagina dell'applicazione non funzionano](application-proxy-page-links-broken-problem.md)
* [Porte da aprire per l'app](application-proxy-connectivity-ports-how-to.md)
* [Nessun connettore funzionante in un gruppo di connettori per le app](application-proxy-connectivity-no-working-connector.md)
* [Configurare nel portale di amministrazione](application-proxy-config-how-to.md)
* [Configurare l'accesso Single Sign-On all'app](application-proxy-config-sso-how-to.md)
* [Problema durante la creazione di un'app nel portale di amministrazione](application-proxy-config-problem.md)
* [Configurare la delega vincolata di Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurare con PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Non è possibile accedere all'errore dell'applicazione aziendale](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problemi di installazione del connettore dell'agente proxy dell'applicazione](application-proxy-connector-installation-problem.md)
* [Problema di accesso](application-sign-in-problem-on-premises-application-proxy.md)
