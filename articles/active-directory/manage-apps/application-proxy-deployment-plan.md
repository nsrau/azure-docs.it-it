---
title: Pianificare una distribuzione del proxy di applicazione di Azure Active DirectoryPlan an Azure Active Directory Application Proxy Deployment
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
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330902"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Pianificare una distribuzione del proxy di applicazione di Azure ADPlan an Azure AD Application Proxy deployment

Il proxy di applicazione di Azure Active Directory (Azure AD) è una soluzione di accesso remoto sicura ed economica per le applicazioni locali. Fornisce un percorso di transizione immediata per le organizzazioni "Cloud First" per gestire l'accesso alle applicazioni locali legacy che non sono ancora in grado di utilizzare protocolli moderni. Per ulteriori informazioni introduttive, vedere [Che cos'è il proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)di applicazione .

Il proxy di applicazione è consigliato per consentire agli utenti remoti l'accesso alle risorse interne. Il proxy di applicazione sostituisce la necessità di una VPN o di un proxy inverso per questi casi d'uso di accesso remoto. Non è destinato agli utenti che si trovano nella rete aziendale. Questi utenti che utilizzano il proxy di applicazione per l'accesso Intranet potrebbero verificarsi problemi di prestazioni indesiderate.

Questo articolo include le risorse necessarie per pianificare, utilizzare e gestire il proxy di applicazione di Azure AD. 

## <a name="plan-your-implementation"></a>Pianificare l'implementazione

Nella sezione seguente viene fornita un'ampia panoramica degli elementi di pianificazione principali che verranno impostati per un'esperienza di distribuzione efficiente. 

### <a name="prerequisites"></a>Prerequisiti

È necessario soddisfare i prerequisiti seguenti prima di iniziare l'implementazione. In questa [esercitazione](application-proxy-add-on-premises-application.md)è possibile visualizzare ulteriori informazioni sulla configurazione dell'ambiente, inclusi questi prerequisiti.

* **Connettori**: I connettori sono agenti leggeri che è possibile distribuire in:
   * Hardware fisico locale
   * Una macchina virtuale ospitata all'interno di qualsiasi soluzione di hypervisorA VM hosted within any hypervisor solution
   * Una macchina virtuale ospitata in Azure per abilitare la connessione in uscita al servizio proxy di applicazione.

* Per una panoramica più dettagliata, vedere [Informazioni sui connettori proxy](application-proxy-connectors.md) app di Azure AD.

     * I computer connettore devono [essere abilitati per TLS 1.2](application-proxy-add-on-premises-application.md) prima di installare i connettori.

     * Se possibile, distribuire i connettori nella stessa rete e [nello stesso](application-proxy-network-topology.md) segmento dei server applicazioni Web back-end. È consigliabile distribuire i connettori dopo aver completato l'individuazione delle applicazioni.
     * È consigliabile che ogni gruppo di connettori disponga di almeno due connettori per garantire disponibilità elevata e scalabilità. Avere tre connettori è ottimale nel caso in cui sia necessario servire una macchina in qualsiasi momento. Esaminare la tabella della capacità dei [connettori](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) per decidere il tipo di computer in cui installare i connettori. Più grande è la macchina più buffer e performante sarà il connettore.

* **Impostazioni di accesso**alla rete: i connettori del proxy di applicazione di Azure AD [si connettono ad Azure tramite HTTPS (porta TCP 443) e HTTP (porta TCP 80)](application-proxy-add-on-premises-application.md). 

   * La terminazione del traffico TLS del connettore non è supportata e impedirà ai connettori di stabilire un canale sicuro con i rispettivi endpoint del proxy dell'app di Azure.Terminating connector TLS traffic isn't supported and will prevent connectors from establishing a secure channel with respective Azure App Proxy endpoints.

   * Evitare tutte le forme di ispezione in linea sulle comunicazioni TLS in uscita tra i connettori e Azure.Avoid all forms of inline inspection on outbound TLS communications between connectors and Azure. L'ispezione interna tra un connettore e le applicazioni back-end è possibile, ma potrebbe ridurre l'esperienza utente e, come tale, non è consigliata.

   * Anche il bilanciamento del carico dei connettori stessi non è supportato o anche necessario.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerazioni importanti prima di configurare il proxy di applicazione di Azure ADImportant considerations before configuring Azure AD Application Proxy

Per configurare e implementare il proxy di applicazione di Azure AD, è necessario soddisfare i requisiti di base seguenti.

*  **Onboarding di Azure:** prima di distribuire il proxy di applicazione, le identità utente devono essere sincronizzate da una directory locale o create direttamente all'interno dei tenant di Azure AD. La sincronizzazione delle identità consente ad Azure AD di preautenticare gli utenti prima di concedere loro l'accesso alle applicazioni pubblicate tramite proxy di applicazione e di ottenere le informazioni degli ID utente necessarie per eseguire l'accesso Single Sign-On (SSO).

* **Requisiti di accesso condizionale:** non è consigliabile utilizzare il proxy di applicazione per l'accesso Intranet perché ciò aumenta la latenza che avrà un impatto sugli utenti. È consigliabile utilizzare il proxy di applicazione con criteri di preautenticazione e accesso condizionale per l'accesso remoto da Internet.  Un approccio per fornire l'accesso condizionale per l'utilizzo intranet consiste nel modernizzare le applicazioni in modo che possano eseguire direttamente l'autenticazione con AAD. Per altre informazioni, vedere Risorse per la migrazione delle [applicazioni ad AAD.](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) 

* **Limiti del servizio:** per proteggersi dall'utilizzo eccessivo delle risorse da parte di singoli tenant, sono impostati limiti di limitazione per applicazione e tenant. Per visualizzare questi limiti, vedere i limiti e le restrizioni del [servizio Azure AD.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions) Questi limiti di limitazione si basano su un benchmark molto al di sopra del volume di utilizzo tipico e forniscono un ampio buffer per la maggior parte delle distribuzioni.

* **Certificato pubblico**: se si utilizzano nomi di dominio personalizzati, è necessario procurarsi un certificato TLS/SSL. A seconda dei requisiti dell'organizzazione, ottenere un certificato può richiedere del tempo e si consiglia di iniziare il processo il prima possibile. Il proxy di applicazione di Azure supporta certificati standard, [con caratteri jolly](application-proxy-wildcard.md)o basati su SAN. Per altre informazioni, vedere [Configurare domini personalizzati con](application-proxy-configure-custom-domain.md)il proxy di applicazione di Azure AD.

* **Requisiti di dominio:** Single Sign-On per le applicazioni pubblicate utilizzando la delega vincolata Kerberos (KCD) richiede che il server che esegue il connettore e il server che esegue l'app siano aggiunti a un dominio e facciano parte dello stesso dominio o dei domini trusting.
Per informazioni dettagliate sull'argomento, vedere [KCD per l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md) con il proxy di applicazione. Il servizio connettore viene eseguito nel contesto del sistema locale e non deve essere configurato per l'utilizzo di un'identità personalizzata.

* **Record DNS per gli URL**

   * Prima di utilizzare domini personalizzati nel proxy di applicazione è necessario creare un record CNAME nel DNS pubblico, consentendo ai client di risolvere l'URL esterno definito personalizzato nell'indirizzo proxy di applicazione predefinito. La mancata creazione di un record CNAME per un'applicazione che utilizza un dominio personalizzato impedirà agli utenti remoti di connettersi all'applicazione. I passaggi necessari per aggiungere record CNAME possono variare dal provider DNS al provider, pertanto scopri come gestire i record dns e i set di [record tramite il portale](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)di Azure.

   * Analogamente, gli host dei connettori devono essere in grado di risolvere l'URL interno delle applicazioni pubblicate.

* **Diritti e ruoli amministrativi**

   * **L'installazione** del connettore richiede diritti di amministratore locale per il server Windows in cui viene installato. Richiede inoltre un ruolo minimo di *amministratore dell'applicazione* per autenticare e registrare l'istanza del connettore nel tenant di Azure AD. 

   * **La pubblicazione e l'amministrazione** delle applicazioni richiedono il ruolo *amministratore dell'applicazione.* Gli amministratori delle applicazioni possono gestire tutte le applicazioni nella directory, incluse le registrazioni, le impostazioni SSO, le assegnazioni di utenti e gruppi e le licenze, le impostazioni del proxy di applicazione e il consenso. Non consente tuttavia di gestire l'accesso condizionale. Il ruolo *Amministratore applicazione cloud* dispone di tutte le funzionalità dell'Amministratore applicazioni, ad eccezione del fatto che non consente la gestione delle impostazioni del proxy dell'applicazione.

* **Licenze:** il proxy di applicazione è disponibile tramite una sottoscrizione di Azure AD Premium.Licensing : Application Proxy is available through an Azure AD Premium subscription. Fare riferimento alla pagina Prezzi di [Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) per un elenco completo delle opzioni e delle funzionalità di licenza.  

### <a name="application-discovery"></a>Individuazione delle applicazioni

Compilare un inventario di tutte le applicazioni nell'ambito che vengono pubblicate tramite il proxy di applicazione raccogliendo le seguenti informazioni:

| Information Type| Informazioni da raccogliere |
|---|---|
| Tipo di servizio| Ad esempio: SharePoint, SAP, CRM, Applicazione Web personalizzata, API |
| Piattaforma applicativa | Ad esempio: Windows IIS, Apache su Linux, Tomcat, NGINX |
| Appartenenza al dominio| Nome di dominio completo (FQDN) del server Web |
| Posizione dell'applicazione | Posizione del server Web o della farm nell'infrastruttura |
| Accesso interno | Url esatto utilizzato per l'accesso interno all'applicazione. <br> Se una farm, che tipo di bilanciamento del carico è in uso? <br> Se l'applicazione trae contenuto da origini diverse da se stessa.<br> Determinare se l'applicazione opera su WebSockets. |
| Accesso esterno | Soluzione fornitore a cui l'applicazione è già esposta esternamente. <br> URL che si desidera utilizzare per l'accesso esterno. Se SharePoint, verificare che i mapping di accesso alternativo siano configurati in base [alle linee guida.](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings) In caso contrario, sarà necessario definire url esterni. |
| Certificato pubblico | Se si utilizza un dominio personalizzato, procurarsi un certificato con un nome soggetto corrispondente. se esiste un certificato, prendere nota del numero di serie e della posizione da cui è possibile ottenerlo. |
| Tipo di autenticazione| Tipo di autenticazione supportato dal supporto dell'applicazione, ad esempio Basic, Windows Integration Authentication, basata su form, basata sull'intestazione e attestazioni. <br>Se l'applicazione è configurata per l'esecuzione con un account di dominio specifico, prendere nota del nome di dominio completo (FQDN) dell'account del servizio.<br> Se basato su SAML, l'identificatore e gli URL di risposta. <br> Se basata sull'intestazione, la soluzione del fornitore e il requisito specifico per la gestione del tipo di autenticazione. |
| Nome del gruppo di connettori | Nome logico del gruppo di connettori che verrà designato per fornire il condotto e SSO a questa applicazione back-end. |
| Accesso utenti/gruppi | Utenti o gruppi di utenti a cui verrà concesso l'accesso esterno all'applicazione. |
| Requisiti aggiuntivi | Si notino eventuali requisiti di sicurezza o accesso remoto aggiuntivi che devono essere fattorizzati nella pubblicazione dell'applicazione. |

Puoi scaricare questo [foglio di calcolo dell'inventario](https://aka.ms/appdiscovery) delle applicazioni per creare l'inventario delle tue app.

### <a name="define-organizational-requirements"></a>Definire i requisiti organizzativi

Di seguito sono riportate le aree in cui è necessario definire i requisiti aziendali dell'organizzazione. Ogni area contiene esempi di requisiti

 **Accesso**

* Utenti remoti con dispositivi aggiunti a un dominio o dispositivi aggiunti ad Azure AD, gli utenti possono accedere alle applicazioni pubblicate in modo sicuro con Single Sign-On (SSO) senza soluzione di continuità.

* Gli utenti remoti con dispositivi personali approvati possono accedere in modo sicuro alle applicazioni pubblicate a condizione che siano registrati nell'autenticazione a più fattori e abbiano registrato l'app Microsoft Authenticator sul proprio telefono cellulare come metodo di autenticazione.

**Governance** 

* Gli amministratori possono definire e monitorare il ciclo di vita delle assegnazioni degli utenti alle applicazioni pubblicate tramite il proxy di applicazione.

**Sicurezza**

* Solo gli utenti assegnati alle applicazioni tramite l'appartenenza al gruppo o singolarmente possono accedere a tali applicazioni.

**Prestazioni**

* Non vi è alcun adine delle prestazioni dell'applicazione rispetto all'accesso all'applicazione dalla rete interna.

**Esperienza utente**

* Gli utenti sono consapevoli di come accedere alle proprie applicazioni utilizzando gli URL aziendali familiari su qualsiasi piattaforma del dispositivo.

**Controllo**
* Gli amministratori sono in grado di controllare l'attività di accesso degli utenti.


### <a name="best-practices-for-a-pilot"></a>Procedure consigliate per un progetto pilota

Determinare la quantità di tempo e fatica necessaria per la piena messa in servizio di una singola applicazione per l'accesso remoto con Single Sign-On (SSO). A tale scopo, eseguire un progetto pilota che ne consideri l'individuazione iniziale, la pubblicazione e i test generali. L'utilizzo di una semplice applicazione Web basata su IIS che è già preconfigurata per l'autenticazione integrata di Windows (IWA) consente di stabilire una linea di base, poiché questa configurazione richiede uno sforzo minimo per pilotare correttamente l'accesso remoto e SSO.

Gli elementi di progettazione seguenti dovrebbero aumentare il successo dell'implementazione pilota direttamente in un tenant di produzione.  

**Gestione connettori**:  

* I connettori svolgono un ruolo chiave nel fornire il condotto locale alle applicazioni. L'utilizzo del gruppo di connettori **predefinito** è adeguato per il test pilota iniziale delle applicazioni pubblicate prima di inserirle nell'ambiente di produzione. Le applicazioni testate correttamente possono quindi essere spostate nei gruppi di connettori di produzione.

**Gestione delle applicazioni**:

* È più probabile che la forza lavoro ricordi che un URL esterno è familiare e pertinente. Evitare di pubblicare l'applicazione utilizzando i suffissi predefiniti msappproxy.net o onmicrosoft.com. Specificare invece un dominio verificato di primo livello familiare, preceduto da un nome host logico, ad esempio *intranet.<, customers_domain>.com*.

* Limitare la visibilità dell'icona dell'applicazione pilota a un gruppo pilota nascondendo ne l'icona di avvio nel portale di Azure MyApps.Restrict visibility of the pilot application's icon to a pilot group by hiding its launch icon form the Azure MyApps portal. Quando si è pronti per la produzione, è possibile definire l'ambito dell'app per il rispettivo gruppo di destinatari di destinazione, nello stesso tenant di pre-produzione o pubblicando anche l'applicazione nel tenant di produzione.

**Impostazioni Single Sign-On:** alcune impostazioni SSO hanno dipendenze specifiche che possono richiedere tempo per la configurazione, pertanto evitare ritardi nel controllo delle modifiche garantendo che le dipendenze vengano risolte in anticipo. Ciò include gli host connettore di aggiunta al dominio per eseguire SSO utilizzando la delega vincolata Kerberos (KCD) e prendersi cura di altre attività che richiedono molto tempo. Ad esempio, Impostazione di un'istanza di PING Access, se è necessario un SSO basato sull'intestazione.

**TLS tra l'host del connettore e l'applicazione**di destinazione : La sicurezza è fondamentale, pertanto TLS tra l'host del connettore e le applicazioni di destinazione deve sempre essere utilizzato. In particolare se l'applicazione web è configurata per l'autenticazione basata su form (FBA), come credenziali utente vengono quindi trasmesse in modo efficace in testo non crittografato.

**Implementare in modo incrementale e testare ogni passaggio**. Eseguire test funzionali di base dopo la pubblicazione di un'applicazione per garantire che tutti i requisiti utente e aziendali siano soddisfatti seguendo le istruzioni riportate di seguito:Conduct basic functional testing after publishing an application to ensure that all user and business requirements are met by following the directions below:

1. Testare e convalidare l'accesso generale all'applicazione Web con la preautenticazione disabilitata.
2. Se la preautenticazione ha esito positivo e assegna utenti e gruppi. Testare e convalidare l'accesso.
3. Aggiungere quindi il metodo SSO per l'applicazione e riprovare a convalidare l'accesso.
4. Applicare i criteri di accesso condizionale e Di autenticazione a più fattori in base alle esigenze. Testare e convalidare l'accesso.

**Strumenti di risoluzione dei problemi**: durante la risoluzione dei problemi, iniziare sempre convalidando l'accesso all'applicazione pubblicata dal browser nell'host del connettore e verificare che l'applicazione funzioni come previsto. Più semplice è la configurazione, più facile da determinare la causa principale, quindi prendere in considerazione il tentativo di riprodurre i problemi con una configurazione minima, ad esempio l'utilizzo di un solo connettore e nessun SSO. In alcuni casi, gli strumenti di debug Web come Fiddler di Telerik possono rivelarsi indispensabili per risolvere problemi di accesso o di contenuto nelle applicazioni a cui si accede tramite un proxy. Fiddler può anche fungere da proxy per aiutare a tracciare ed eseguire il debug del traffico per le piattaforme mobili come iOS e Android e praticamente tutto ciò che può essere configurato per instradare tramite un proxy. Per ulteriori informazioni, consulta la [guida alla risoluzione dei problemi.](application-proxy-troubleshoot.md)

## <a name="implement-your-solution"></a>Implementa la tua soluzione

### <a name="deploy-application-proxy"></a>Distribuire il proxy di applicazioneDeploy Application Proxy

I passaggi per distribuire il proxy di applicazione sono illustrati in questa [esercitazione per l'aggiunta di un'applicazione locale per l'accesso remoto.](application-proxy-add-on-premises-application.md) Se l'installazione non riesce, selezionare **Risoluzione dei problemi** del proxy di applicazione nel portale o utilizzare la guida alla risoluzione dei problemi [per problemi con l'installazione di Application Proxy Agent Connector](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Pubblicare applicazioni tramite il proxy di applicazione

Le applicazioni di pubblicazione presupponeno che siano stati soddisfatti tutti i prerequisiti e che nella pagina Proxy applicazione siano stati soddisfatti diversi connettori registrati e attivi.

È anche possibile pubblicare applicazioni tramite [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Di seguito sono riportate alcune procedure consigliate da seguire per la pubblicazione di un'applicazione:

* **Usa gruppi di connettori**: Assegnare un gruppo di connettori designato per la pubblicazione di ogni rispettiva applicazione. È consigliabile che ogni gruppo di connettori disponga di almeno due connettori per garantire disponibilità elevata e scalabilità. Avere tre connettori è ottimale nel caso in cui sia necessario servire una macchina in qualsiasi momento. Vedere inoltre [Pubblicare applicazioni in reti e posizioni separate utilizzando gruppi](application-proxy-connector-groups.md) di connettori per vedere come è possibile utilizzare anche i gruppi di connettori per segmentare i connettori in base alla rete o alla posizione.

* **Imposta timeout applicazione back-end:** questa impostazione è utile negli scenari in cui l'applicazione potrebbe richiedere più di 75 secondi per elaborare una transazione client. Ad esempio, quando un client invia una query a un'applicazione Web che funge da front-end a un database. Il front-end invia questa query al server di database back-end e attende una risposta, ma quando riceve una risposta, si scade il lato client della conversazione. L'impostazione del timeout su Long fornisce 180 secondi per il completamento delle transazioni più lunghe.

* **Utilizzare tipi di cookie appropriati**

   * **Cookie solo HTTP**: Fornisce ulteriore sicurezza facendo in modo che il proxy di applicazione includa il flag HTTPOnly nelle intestazioni di risposta HTTP set-cookie. Questa impostazione consente di ridurre gli exploit, ad esempio il cross-site scripting (XSS). Lasciare l'opzione impostata su No per i client/agenti utente che richiedono l'accesso al cookie di sessione. Ad esempio, client RDP/MTSC che si connette a un Gateway Desktop remoto pubblicato tramite Proxy app.

   * **Secure Cookie**: Quando un cookie è impostato con l'attributo Secure, l'agente utente (app lato client) includerà il cookie nelle richieste HTTP solo se la richiesta viene trasmessa su un canale protetto TLS. Ciò consente di ridurre il rischio che un cookie venga compromesso su canali di testo non crittografato, pertanto deve essere abilitato.

   * **Cookie persistente**: Consente al cookie di sessione del proxy di applicazione di mantenere una posizione tra le chiusure del browser rimanendo valida fino alla scadenza o all'eliminazione. Utilizzato per scenari in cui un'applicazione avanzata, ad esempio office, accede a un documento all'interno di un'applicazione Web pubblicata, senza che all'utente venga richiesto nuovamente l'autenticazione. Abilitare con cautela, tuttavia, poiché i cookie persistenti possono in ultima analisi lasciare un servizio a rischio di accessi non autorizzati, se non utilizzati insieme ad altri controlli di compensazione. Questa impostazione deve essere utilizzata solo per le applicazioni meno recenti che non possono condividere cookie tra processi. È preferibile aggiornare l'applicazione per gestire la condivisione di cookie tra i processi anziché utilizzare questa impostazione.

* **Traduci URL nelle intestazioni**: questa opzione viene abilitata per gli scenari in cui il DNS interno non può essere configurato in modo da corrispondere allo spazio dei nomi pubblico dell'organizzazione(noto anche come DNS diviso). A meno che l'applicazione non richieda l'intestazione host originale nella richiesta del client, lasciare questo valore impostato su Sì.Unless your application requires the original host header in the client request, leave this value set to Yes. L'alternativa consiste nel fare in modo che il connettore utilizzi il nome di dominio completo nell'URL interno per il routing del traffico effettivo e il nome di dominio completo nell'URL esterno, come intestazione host. Nella maggior parte dei casi questa alternativa dovrebbe consentire all'applicazione di funzionare normalmente, quando si accede in remoto, ma gli utenti perdono i vantaggi di avere una corrispondenza all'interno &URL esterno.

* **Traduci URL nel corpo dell'applicazione:** attiva la conversione dei collegamenti del corpo dell'applicazione per un'app quando vuoi che i collegamenti di tale app vengano tradotti nelle risposte al client. Se abilitata, questa funzione fornisce un tentativo di utilizzo ottimale per tradurre tutti i collegamenti interni trovati da App Proxy nelle risposte HTML e CSS restituite ai client. È utile quando si pubblicano app che contengono collegamenti a nome breve hardcoded assoluto o NetBIOS nel contenuto o app con contenuto collegato ad altre applicazioni locali.

Per gli scenari in cui un'app pubblicata è collegata ad altre app pubblicate, abilitare la conversione dei collegamenti per ogni applicazione in modo da avere il controllo sull'esperienza utente a livello di app.

Si supponga, ad esempio, di avere tre applicazioni pubblicate tramite il proxy di applicazione che si collegano tutte tra loro: Benefit, Spese e Viaggi, oltre a una quarta app, Feedback, che non viene pubblicata tramite il proxy di applicazione.

![Immagine 1](media/App-proxy-deployment-plan/link-translation.png)

Quando si abilita la conversione dei collegamenti per l'app Benefit, i collegamenti a Spese e viaggi vengono reindirizzati agli URL esterni per tali app, in modo che gli utenti che accedono alle applicazioni dall'esterno della rete aziendale possano accedervi. I collegamenti da Spese e Ritorno ai vantaggi non funzionano perché la traduzione dei collegamenti non è stata abilitata per queste due app. Il collegamento a Feedback non viene reindirizzato perché non è presente alcun URL esterno, pertanto gli utenti che utilizzano l'app Benefit non saranno in grado di accedere all'app di feedback dall'esterno della rete aziendale. Vedere informazioni dettagliate sulla traduzione dei collegamenti e altre opzioni di [reindirizzamento](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Accedere all'applicazione

Esistono diverse opzioni per la gestione dell'accesso alle risorse pubblicate da App Proxy, pertanto scegliere la più appropriata per lo scenario e le esigenze di scalabilità. Gli approcci comuni includono: l'uso di gruppi locali sincronizzati tramite Azure AD Connect, la creazione di gruppi dinamici in Azure AD in base agli attributi utente, l'uso di gruppi self-service gestiti da un proprietario della risorsa o una combinazione di tutti questi elementi.Common approaches include: using on-premises groups that are being synced via Azure AD Connect, creating Dynamic Groups in Azure AD based on user attributes, using self-service groups that are managed by a resource owner, or a combination of all these. Vedere le risorse collegate per i vantaggi di ciascuno.

Il modo più semplice per assegnare agli utenti l'accesso a un'applicazione è andare nel riquadro **di** sinistra del riquadro sinistro dell'applicazione pubblicata e assegnare direttamente gruppi o individui.

![Figura 24](media/App-proxy-deployment-plan/add-user.png)

È inoltre possibile consentire agli utenti l'accesso self-service all'applicazione assegnando un gruppo di cui attualmente non sono membri e configurando le opzioni self-service.

![Immagine 25](media/App-proxy-deployment-plan/allow-access.png)

Se abilitata, gli utenti potranno accedere al portale MyApps e richiedere l'accesso e essere approvati automaticamente e aggiunti al gruppo self-service già consentito o richiedere l'approvazione di un approvatore designato.

Gli utenti guest possono anche essere [invitati ad accedere alle applicazioni interne pubblicate tramite il proxy di applicazione tramite Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Per le applicazioni locali che normalmente sono accessibili in modo anonimo, senza richiedere alcuna autenticazione, è preferibile disabilitare l'opzione che si trova nelle **proprietà**dell'applicazione .

![Immagine 26](media/App-proxy-deployment-plan/assignment-required.png)


Lasciando questa opzione impostata su No, gli utenti possono accedere all'applicazione locale tramite il proxy dell'app di Azure AD senza autorizzazioni, quindi usare con cautela.

Una volta pubblicata, l'applicazione deve essere accessibile digitandone l'URL esterno in un browser o tramite l'icona in [https://myapps.microsoft.com](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Abilitare la preautenticazioneEnable pre-authentication

Verificare che l'applicazione sia accessibile tramite il proxy di applicazione che vi accede tramite l'URL esterno. 

1. Passare ad **Applicazioni Azure Active Directory** > **Enterprise** > **Tutte le applicazioni** e scegliere l'app da gestire.

2. Selezionare **Proxy dell'applicazione**.

3. Nel campo **Autenticazione preliminare** usare l'elenco a discesa per selezionare **Azure Active Directory**e selezionare **Salva**.

Con la preautenticazione abilitata, Azure AD ingrandirà prima gli utenti per l'autenticazione e se Single Sign-On è configurato, l'applicazione back-end verificherà anche l'utente prima che venga concesso l'accesso all'applicazione. La modifica della modalità di preautenticazione da Passthrough ad Azure AD configura anche l'URL esterno con HTTPS, pertanto qualsiasi applicazione inizialmente configurata per HTTP verrà ora protetta con HTTPS.

### <a name="enable-single-sign-on"></a>Abilitazione dell'accesso Single Sign-On

SSO offre la migliore esperienza utente possibile e la migliore sicurezza perché gli utenti devono accedere una sola volta quando accedono ad Azure AD. Una volta che un utente ha preautenticato, SSO viene eseguito dal connettore proxy di applicazione che esegue l'autenticazione all'applicazione locale, per conto dell'utente. L'applicazione back-end elabora l'account di accesso come se fosse l'utente stesso. 

La scelta dell'opzione **Passthrough** consente agli utenti di accedere all'applicazione pubblicata senza dover mai eseguire l'autenticazione in Azure AD.

L'esecuzione di SSO è possibile solo se Azure AD può identificare l'utente che richiede l'accesso a una risorsa, pertanto l'applicazione deve essere configurata per preautenticare gli utenti con Azure AD al momento dell'accesso per il funzionamento di SSO, altrimenti le opzioni SSO verranno disabilitate.

Leggere [Single Sign-On to applications in Azure AD](what-is-single-sign-on.md) per scegliere il metodo SSO più appropriato durante la configurazione delle applicazioni.

###  <a name="working-with-other-types-of-applications"></a>Utilizzo di altri tipi di applicazioni

Il proxy di applicazione di Azure AD può anche supportare le applicazioni sviluppate per l'uso della libreria di autenticazione di Azure AD ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) o della libreria di autenticazione Microsoft ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Supporta le app client native utilizzando i token rilasciati di Azure AD ricevuti nelle informazioni di intestazione della richiesta client per eseguire la preautenticazione per conto degli utenti.

Leggere la pubblicazione di [app client native e mobili](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) e di applicazioni basate sulle [attestazioni](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) per informazioni sulle configurazioni disponibili del proxy di applicazione.

### <a name="use-conditional-access-to-strengthen-security"></a>Usare l'accesso condizionale per rafforzare la sicurezzaUse Conditional Access to strengthen security

La sicurezza delle applicazioni richiede un set avanzato di funzionalità di sicurezza in grado di proteggere e rispondere a minacce complesse in locale e nel cloud. Gli utenti malintenzionati ottengono più spesso l'accesso alla rete aziendale tramite credenziali utente deboli, predefinite o rubate.  La sicurezza basata sull'identità Microsoft riduce l'utilizzo delle credenziali rubate gestendo e proteggendo le identità con privilegi e senza privilegi.

Per supportare il proxy di applicazione di Azure AD, è possibile usare le funzionalità seguenti:The following capabilities can be used to support Azure AD Application Proxy:

* Accesso condizionale basato su utenti e posizione: proteggi i dati sensibili limitando l'accesso degli utenti in base alla posizione geografica o a un indirizzo IP con criteri di [accesso condizionale basati](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)sulla posizione.

* Accesso condizionale basato su dispositivo: verificare che solo i dispositivi registrati, approvati e conformi possano accedere ai dati aziendali con [l'accesso condizionale basato su dispositivo.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)

* Accesso condizionale basato su applicazioni: il lavoro non deve interrompersi quando un utente non si è nella rete aziendale. [Proteggi l'accesso alle app cloud e locali aziendali](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) e mantieni il controllo con l'accesso condizionale.

* Accesso condizionale basato sul rischio: proteggere i dati da hacker malintenzionati con criteri di [accesso condizionale basati](https://www.microsoft.com/cloud-platform/conditional-access) sul rischio che possono essere applicati a tutte le app e a tutti gli utenti, sia in locale che nel cloud.

* Pannello di accesso di Azure AD: con il servizio proxy di applicazione distribuito e le applicazioni pubblicate in modo sicuro, offri agli utenti un semplice hub per individuare e accedere a tutte le applicazioni. Aumentare la produttività con funzionalità self-service, ad esempio la possibilità di richiedere l'accesso a nuove app e gruppi o di gestire l'accesso a queste risorse per conto di altri, tramite il pannello di [accesso](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Gestisci la tua implementazione

### <a name="required-roles"></a>Ruoli obbligatori

Microsoft sostiene il principio di concedere il privilegio minimo possibile per eseguire le attività necessarie con Azure AD. [Esaminare i diversi ruoli](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) di Azure disponibili e scegliere quello giusto per soddisfare le esigenze di ogni persona. Potrebbe essere necessario applicare temporaneamente alcuni ruoli e rimuoverli dopo il completamento della distribuzione.

| Ruolo aziendale| Attività aziendali| Ruoli di Azure AD |
|---|---|---|
| Amministratore dell'help desk | In genere limitato alla qualifica all'utente finale di problemi e all'esecuzione di attività limitate, ad esempio la modifica delle password degli utenti, l'invalidazione dei token di aggiornamento e il monitoraggio dell'integrità del servizio. | Amministratore del supporto tecnico |
| Amministratore identità| Leggere i report di accesso di Azure AD e i log di controllo per eseguire il debug dei problemi correlati al proxy dell'app.| Ruolo con autorizzazioni di lettura per la sicurezza |
| Proprietario dell'applicazione| Creare e gestire tutti gli aspetti delle applicazioni aziendali, delle registrazioni delle applicazioni e delle impostazioni del proxy di applicazione.| Amministratore dell'applicazione |
| Amministratore dell'infrastruttura | Proprietario rollover certificato | Amministratore dell'applicazione |

Ridurre al minimo il numero di persone che hanno accesso a informazioni o risorse protette consentirà di ridurre la possibilità che un attore malintenzionato ottenga accessi non autorizzati o che un utente autorizzato influisca inavvertitamente su una risorsa sensibile. 
 
Tuttavia, gli utenti devono comunque eseguire operazioni con privilegi giornalieri, pertanto l'applicazione di criteri di gestione delle [identità con](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) privilegi basati su JIT (Just-In-Time) per fornire l'accesso con privilegi su richiesta alle risorse di Azure e Azure AD è l'approccio consigliato per gestire in modo efficace l'accesso amministrativo e il controllo.

### <a name="reporting-and-monitoring"></a>Monitoraggio e creazione di report

Azure AD offre informazioni aggiuntive sull'utilizzo delle applicazioni e sull'integrità operativa dell'organizzazione tramite [i log di controllo e i report.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) Il proxy di applicazione rende inoltre molto semplice il monitoraggio dei connettori dal portale di Azure AD e dai registri eventi di Windows.Application Proxy also makes easy to monitor connectors from the Azure AD portal and Windows Event Logs.

#### <a name="application-audit-logs"></a>Log di controllo delle applicazioni

Questi log forniscono informazioni dettagliate sugli account di accesso alle applicazioni configurate con il proxy di applicazione e il dispositivo e l'utente che accede all'applicazione. [I log](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) di controllo si trovano nel portale di Azure e nell'API di [controllo](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) per l'esportazione. Inoltre, per l'applicazione sono disponibili [report sull'utilizzo e sulle informazioni dettagliate.](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context)

#### <a name="application-proxy-connector-monitoring"></a>Monitoraggio del connettore proxy di applicazione

I connettori e il servizio si occupano di tutte le attività che richiedono disponibilità elevata. È possibile monitorare lo stato dei connettori dalla pagina Proxy applicazione nel portale di Azure AD. Per altre informazioni sulla manutenzione dei [connettori,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance)vedere Informazioni sui connettori proxy di applicazione di Azure AD.For more information about connector maintainence see Understand Azure AD Application Proxy Connectors .

![Esempio: connettori del proxy di applicazione di Azure ADExample: Azure AD Application Proxy connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Registri eventi di Windows e contatori delle prestazioni

I connettori hanno sia i registri amministrativi che i registri di sessione. I log di amministrazione includono gli eventi principali e i relativi errori. I log di sessione includono tutte le transazioni e i relativi dettagli di elaborazione. Registri e contatori si trovano nei registri eventi di Windows per altre informazioni, vedere [Informazioni sui connettori proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood)di Azure AD. Seguire questa [esercitazione per configurare le origini dati del log](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)eventi in Monitoraggio di Azure.Follow this tutorial to configure event log data sources in Azure Monitor .

### <a name="troubleshooting-guide-and-steps"></a>Guida alla risoluzione dei problemi e passaggi

Scopri di più sui problemi comuni e su come risolverli con la nostra guida alla [risoluzione dei](application-proxy-troubleshoot.md) messaggi di errore. 

Negli articoli seguenti vengono illustrati scenari comuni che possono essere utilizzati anche per creare guide alla risoluzione dei problemi per l'organizzazione di supporto. 

* [Problema durante la visualizzazione di una pagina dell'app](application-proxy-page-appearance-broken-problem.md)
* [Il caricamento dell'applicazione richiede troppo tempo](application-proxy-page-load-speed-problem.md)
* [I collegamenti nella pagina dell'applicazione non funzionano](application-proxy-page-links-broken-problem.md)
* [Porte da aprire per l'app](application-proxy-connectivity-ports-how-to.md)
* [Nessun connettore funzionante in un gruppo di connettori per l'app](application-proxy-connectivity-no-working-connector.md)
* [Configurare nel portale di amministrazione](application-proxy-config-how-to.md)
* [Configurare l'accesso Single Sign-On per l'app](application-proxy-config-sso-how-to.md)
* [Problema durante la creazione di un'app nel portale di amministrazione](application-proxy-config-problem.md)
* [Configurare la delega vincolata di Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurare con PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Errore di accesso all'applicazione aziendale](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problemi di installazione del connettore dell'agente proxy dell'applicazione](application-proxy-connector-installation-problem.md)
* [Problema di accesso](application-sign-in-problem-on-premises-application-proxy.md)
