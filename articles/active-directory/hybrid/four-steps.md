---
title: Quattro passaggi per una base di gestione avanzata delle identità con Azure Active Directory
description: Questo argomento descrive quattro passaggi ai clienti di identità ibrida possono richiedere per creare una base di gestione avanzata delle identità.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: a20a1a1009949aa2e6de8586040e918ae15c8d39
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655933"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Quattro passaggi per una base di gestione avanzata delle identità con Azure Active Directory

Gestione dell'accesso alle App e dati può si basano più sulle strategie di limite di sicurezza rete tradizionali, ad esempio firewall e reti perimetrali a causa lo spostamento rapido delle app nel cloud. Ora le organizzazioni devono considerare attendibile la soluzione di identità per controllare chi e cosa ha accesso alle App e dati dell'organizzazione. Altre organizzazioni consentono ai dipendenti di usare i propri dispositivi al lavoro e usare i dispositivi da qualsiasi luogo che possano connettersi a Internet. Garantire che tali dispositivi siano conformi e sicuro è diventata una parte importante di un'organizzazione sceglie di implementare la soluzione di identità. Nell'area di lavoro digitale di oggi, [identità sia il piano di controllo primario](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) delle organizzazioni che passano al cloud.

Adozione di una soluzione di identità ibrida di Azure Active Directory (Azure AD), le organizzazioni ottenere l'accesso alle funzionalità premium che della produttività mediante l'automazione, la delega, self-service e single sign-on di funzionalità di sblocco. Consente i ruoli di lavoro accedere alle risorse aziendali ovunque si trovino a svolgere il proprio lavoro, consentendo al team IT per gestire tale accesso, garantendo che le persone giuste hanno diritti di accesso corretti per le risorse appropriate per stabilire una produttività sicura.

Basato su quanto appreso, questo elenco di controllo delle procedure consigliate consente di distribuire rapidamente le azioni consigliate per compilare un *sicuro* foundation identità all'interno dell'organizzazione:

* Connettersi facilmente alle App
* Stabilire automaticamente una sola identità per ogni utente
* Consentire agli utenti in modo sicuro
* Rendere operative le tue analisi

## <a name="step-1---connect-to-apps-easily"></a>Passaggio 1: connettersi facilmente alle App

Connettendo le tue App con Azure AD, è possibile migliorare la produttività degli utenti finali e la sicurezza abilitando l'accesso single sign-on (SSO) ed effettuare il provisioning degli utenti. Gestendo le tue App in un'unica posizione, Azure AD, è possibile ridurre al minimo il sovraccarico amministrativo e ottenere un singolo punto di controllo per i criteri di sicurezza e conformità.

Questa sezione descrive le opzioni per la gestione dell'accesso utente alle App, abilitare accesso remoto sicuro alle App interne e i vantaggi della migrazione delle App in Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Rendere disponibili le applicazioni agli utenti senza problemi

Azure AD consente agli amministratori [aggiungere le applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) alla raccolta di applicazioni aziendali nel [portale di Azure](https://portal.azure.com/). Aggiunta di applicazioni per la raccolta di applicazioni aziendali rende più semplice per configurare le applicazioni di usare Azure AD come provider di identità. Consente inoltre di gestire l'accesso utente all'applicazione con criteri di accesso condizionale e configurare il single sign-on (SSO) alle applicazioni in modo che gli utenti non devono immettere ripetutamente le proprie password e l'accedono automaticamente sia in locale e applicazioni basate su cloud.

Dopo che le applicazioni vengono aggiunti alla raccolta di Azure AD, gli utenti possono visualizzare le app che a loro assegnate e cercare e richiedono ad altre App in base alle esigenze. Azure AD offre [diversi metodi](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) per gli utenti accedere alle App:

* Pannello di accesso / App personali
* Icona di avvio delle app Office 365
* Accesso diretto alle applicazioni federate
* Collegamenti diretti Single Sign-On

Per altre informazioni sull'accesso utente alle App, vedere **passaggio 3: consentire agli utenti** in questo articolo.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Eseguire la migrazione di App da Active Directory Federation Services ad Azure AD

Migrazione configurazione single sign-on da Active Directory Federation Services (ADFS) in Azure AD offre funzionalità aggiuntive nella sicurezza, facilità di gestione più coerente e collaborazione. Per ottenere risultati ottimali, si consiglia di migrare le app da AD FS con Azure AD. Riportare l'autenticazione dell'applicazione e l'autorizzazione ad Azure AD offre i vantaggi seguenti:

* Gestione dei costi
* Gestione dei rischi
* Aumento della produttività
* Governance e problemi di conformità

Per altre informazioni, vedere la [Migrating Your Applications ad Azure Active Directory](https://aka.ms/migrateapps/whitepaper) white paper.

### <a name="enable-secure-remote-access-to-apps"></a>Abilitare l'accesso remoto sicuro alle App

[Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) offre una soluzione semplice alle organizzazioni di pubblicare applicazioni locali nel cloud per gli utenti remoti che devono accedere alle App interne in modo sicuro. Dopo un single sign-on ad Azure AD, gli utenti possono accedere le applicazioni cloud e locali mediante gli URL esterni o un portale interno dell'applicazione.

Proxy applicazione Azure AD offre i vantaggi seguenti:

* Estensione di Azure AD a risorse locali
  * Sicurezza e protezione a livello di scalabilità
  * Le funzionalità come l'accesso condizionale e multi-Factor Authentication che sono facili da abilitare
* Nessun componente all'interno della rete perimetrale, ad esempio VPN e le soluzioni tradizionali di proxy inverso
* Nessuna connessione in ingresso necessaria
* Single sign-on (SSO) tra i dispositivi, risorse e le app nel cloud e locali
* Consente agli utenti finali di essere produttivi in qualsiasi momento e ovunque

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Scoprire Shadow IT con Microsoft Cloud App Security

Nelle aziende moderne, i reparti IT spesso non sono a conoscenza di tutte le applicazioni cloud usate dagli utenti per svolgere il proprio lavoro. Quando gli amministratori IT vengono richiesto il numero di cloud App ritengono inoltre che i dipendenti usano, in Media sono ad esempio 30 o 40. In realtà, la media è vengono usate dai dipendenti dell'organizzazione oltre 1.000 App separate. l'80% dei dipendenti di usare le app non approvate che nessuno ha esaminato e potrebbe non essere conforme ai criteri di sicurezza e conformità.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) consente di identificare utili App usate di frequente con gli utenti che possa approvare e aggiungere alla raccolta di applicazioni aziendali in modo che gli utenti traggono vantaggio dalle funzionalità quali l'accesso SSO e l'accesso condizionale.

*"* * Cloud App Security** ci aiuta a garantire che i membri del team sono correttamente tramite il cloud e applicazioni SaaS, in modo da supportare i criteri di sicurezza fondamentali che consentono di proteggere Accenture." *--- [John Blasi, la gestione di Accenture Director, la sicurezza delle informazioni,](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Oltre a rilevare shadow IT, MCAS può determinare il livello di rischio delle App, impedire accessi non autorizzati ai dati aziendali, possibili perdite di dati e altri rischi di sicurezza inerenti alle applicazioni.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Passaggio 2: stabilire automaticamente una sola identità per ogni utente

Riunisce in locale e directory basato sul cloud in una soluzione di identità ibrida di Azure AD consentirà di riutilizzare l'investimento esistente di Active Directory in locale eseguendo il provisioning delle identità esistenti nel cloud. La soluzione consente di sincronizzare le identità locali con Azure AD, mentre mantiene IT locali Active Directory in esecuzione con le soluzioni di governance esistenti come la principale fonte di verità per le identità. Soluzione con identità ibrida di Microsoft Azure AD si estende in locale e le funzionalità basate su cloud, la creazione di un'identità utente comune per l'autenticazione e autorizzazione a tutte le risorse, indipendentemente dalla loro posizione.

L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi e impedisce agli utenti di usare più account per le App e servizi, fornendo un'identità comune per l'accesso a cloud e alle risorse locali. Usare più account è il punto debole per gli utenti finali e IT nello stesso modo. Dal punto di vista dell'utente finale, se si dispone più account mezzo dover ricordare più password. Per evitare questo problema, molti utenti di riutilizzare la stessa password per ogni account, ovvero non valida da un punto di vista della sicurezza. Dal punto di vista IT, riutilizzo spesso più la reimpostazione della password e i costi di help desk insieme le segnalazioni degli utenti finali.

Azure AD Connect è lo strumento utilizzato per sincronizzare le identità locali ad Azure AD, che quindi può essere utilizzato per accedere alle applicazioni cloud. Quando le identità sono in Azure AD, è possibile eseguire il provisioning verso applicazioni SaaS come Salesforce o Concur.

In questa sezione sono elencati le raccomandazioni per garantire un'elevata disponibilità, l'autenticazione moderna per il cloud e per ridurre il footprint locale.

> [!NOTE]
> Per altre informazioni su Azure AD Connect, vedere [che cos'è sincronizzazione Azure AD Connect?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Configurare un server di gestione temporanea di Azure AD Connect e mantenerlo aggiornato

Azure AD Connect svolge un ruolo fondamentale nel processo di provisioning. Se il Server di sincronizzazione risulta offline per qualsiasi motivo, le modifiche in locale non verrà aggiornate nel cloud e causano problemi di accesso agli utenti. È importante definire una strategia di failover che consente agli amministratori di riprendere rapidamente la sincronizzazione dopo che il server di sincronizzazione viene portato offline.

Per fornire disponibilità elevata nell'evento di Azure primaria server AD Connect è offline, è consigliabile distribuire un oggetto separato [server di gestione temporanea](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) per Azure AD Connect. Distribuzione di un server consente all'amministratore di "innalzate di livello" il server di gestione temporanea alla produzione per una semplice modifica alla configurazione. Presenza di un server di standby configurato in modalità di gestione temporanea consente anche di testare e distribuire di nuove modifiche alla configurazione e introdurre un nuovo server se rimuovere quello vecchio.

> [!TIP]
> Azure AD Connect viene aggiornato a intervalli regolari. Pertanto, è consigliabile mantenere il server di gestione temporanea corrente per poter sfruttare i miglioramenti delle prestazioni, correzioni di bug e nuove funzionalità che fornisce tutte le nuove versioni.

### <a name="enable-cloud-authentication"></a>Abilitare l'autenticazione cloud

Le organizzazioni con un'istanza locale di Active Directory devono estendere le directory ad Azure AD con Azure AD Connect e configurare il metodo di autenticazione appropriato. [Scelta del metodo di autenticazione corretto](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) per l'organizzazione è il primo passaggio del percorso di spostamento delle app nel cloud. È un componente fondamentale perché controlla l'accesso a tutti i dati nel cloud e risorse.

Il metodo più semplice e consigliato per abilitare l'autenticazione cloud per gli oggetti directory in locale in Azure AD consiste nell'abilitare [sincronizzazione dell'Hash delle Password](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). In alternativa, alcune organizzazioni potrebbero provare ad abilitare [autenticazione pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Se si sceglie PHS o PTA, non dimenticare di attivare [Seamless Single Sign-on](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) per consentire agli utenti di accedere alle App cloud senza dover costantemente immettere il nome utente e la password nell'app quando si usa Windows 7 e 8 dispositivi in aziendale rete. Senza single sign-on, gli utenti devono ricordare le password specifiche dell'applicazione e accedere a ogni applicazione. Analogamente, il personale IT doveva creare e aggiornare gli account utente per ogni applicazione, ad esempio Office 365, Box e Salesforce. Gli utenti devono ricordare le password, oltre a impiegare il tempo necessario per accedere a ogni applicazione. Fornire un standardizzato single sign-on meccanismo a tutta l'azienda è essenziale per un'esperienza utente ottimale, la riduzione del rischio, possibilità di segnalare e governance.

Per le organizzazioni che già usano AD FS o un altro provider di autenticazione in locale, lo spostamento in Azure AD come provider di identità può ridurre la complessità e migliorare la disponibilità. A meno che non si dispone di casi d'uso specifici per l'utilizzo della federazione, è consigliabile eseguire la migrazione dall'autenticazione federata PHS e l'accesso SSO facile o PTA e l'accesso SSO facile per sfruttare i vantaggi di un footprint ridotto in locale e la flessibilità che offerte cloud esperienza utente migliorata. Per altre informazioni, vedere [eseguire la migrazione da federazione a sincronizzazione dell'hash delle password per Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Abilita deprovisioning automatici degli account

Abilitare il provisioning automatizzato e il deprovisioning utenti in applicazioni è la strategia ottimale per che controllano il ciclo di vita delle identità tra più sistemi. Azure AD supporta [automatizzato e basato sui criteri di provisioning e deprovisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) degli account utente per un'ampia gamma di applicazioni SaaS comuni, ad esempio ServiceNow e Salesforce e altri che implementano il [SCIM 2.0 protocollo](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). A differenza delle tradizionali soluzioni di provisioning, che richiede codice personalizzato o il caricamento manuale di file CSV, il servizio di provisioning è ospitato nel cloud e le funzionalità di pre-integrate connettori che possono essere configurati e gestita nel portale di Azure. Dei vantaggi principali del deprovisioning automatico è che consente di proteggere l'organizzazione, rimuovendo immediatamente le identità degli utenti dalle App SaaS principali quando gli utenti lasciano l'organizzazione.

Per altre informazioni sul provisioning automatico degli account utente e sul relativo funzionamento, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Passaggio 3: consentire agli utenti in modo sicuro

Nell'area di lavoro digitale di oggi, è importante bilanciare la protezione con la produttività. Tuttavia, gli utenti finali spesso reinserire le misure di sicurezza che rallentano la loro produttività e l'accesso alle App cloud. Per risolvere questo problema, Azure AD offre funzionalità self-service che consentono agli utenti di rimanere produttivi, riducendo al minimo il sovraccarico amministrativo.

In questa sezione vengono elencati i consigli per la rimozione dei problemi della propria organizzazione per consentire agli utenti pur rimanendo attenzione.

### <a name="enable-self-service-password-reset-for-all-users"></a>Abilitare la reimpostazione della Password Self-Service per tutti gli utenti

Di Azure [reimpostazione delle password self-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) offre una modalità semplice per gli amministratori IT consentire agli utenti di reimpostare e sbloccare le password o account senza l'intervento dell'amministratore. Il sistema include report dettagliati per tenere traccia del momento in cui gli utenti accedono al sistema, oltre a notifiche per segnalare usi impropri.

Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password. Tuttavia, quando si abilita Azure AD Connect [integrazione locale](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), hai anche la possibilità di separare queste due operazioni, che consentono agli utenti di sbloccare l'account senza reimpostare la password.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Verificare che tutti gli utenti sono registrati per MFA e SSPR

Azure fornisce report che può essere utilizzato dall'utente e l'organizzazione per assicurarsi che gli utenti sono registrati per MFA e SSPR. Gli utenti che non hanno registrato potrebbero dover essere di educato sul processo.

L'autenticazione a più fattori [report degli accessi](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) include informazioni sull'utilizzo dell'autenticazione a più fattori e offrono informazioni dettagliate come autenticazione a più fattori funziona all'interno dell'organizzazione. Che possono accedere a accesso attività (e i controlli e gli eventi di rischio) per Azure AD è essenziale per la risoluzione dei problemi, analitica di utilizzo e indagini forensi.

Analogamente, il [report di gestione delle Password Self-Service](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) può essere utilizzato per determinare chi ha (o non è ancora) registrato per SSPR.

### <a name="self-service-app-management"></a>Gestione delle app con self-service

Prima che gli utenti possano da soli individuare le applicazioni dal Pannello di accesso, è necessario abilitare [accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) per tutte le applicazioni che si vuole consentire agli utenti di individuare da soli e richiedere l'accesso alla. Accesso alle applicazioni self-service è un ottimo modo per consentire agli utenti di individuare da soli le applicazioni e, facoltativamente, consentire al gruppo aziendale di approvare l'accesso a tali applicazioni. È possibile consentire la categoria di gestire le credenziali assegnate agli utenti di business [Password Single Sign-in applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) direttamente dal Pannello di accesso.

### <a name="self-service-group-management"></a>Gestione di gruppi self-service

Assegnazione di utenti alle applicazioni è meglio mappata quando si usano i gruppi, in quanto consentono grande flessibilità e possibilità di gestire su larga scala:

* Tramite l'appartenenza al gruppo dinamico basato sugli attributi
* Delega ai proprietari di app

Azure AD offre la possibilità di gestire l'accesso alle risorse con gruppi di sicurezza e gruppi di Office 365. Questi gruppi possono essere gestiti dal proprietario di un gruppo che possa approvare o negare le richieste di appartenenza e delegare il controllo dell'appartenenza al gruppo. Detta [gestione dei gruppi self-service](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), questa funzionalità consente di risparmiare tempo, consentendo i proprietari del gruppo che non è assegnati un ruolo amministrativo per creare e gestire i gruppi senza dover fare affidamento su amministratori per gestire le proprie richieste.

## <a name="step-4---operationalize-your-insights"></a>Passaggio 4 - rendere operative le tue analisi

Controllo e registrazione degli eventi correlati alla sicurezza e gli avvisi correlati sono componenti essenziali di una strategia efficiente per assicurarsi che gli utenti di rimanere produttivi e l'organizzazione sia protetta. I log di sicurezza e i report possono essere utili, ad esempio Rispondi alla domanda:

* Si sta utilizzando ciò che sta effettuando il pagamento per?
* È possibile alcuna operazione dannoso eseguite nel tenant o sospetta?
* Chi ha subito l'impatto durante un evento imprevisto della sicurezza?

I report e i log di sicurezza offrono un record elettronico delle attività sospette e aiutano a rilevare criteri che possono indicare tentativi esterni di penetrazione nella rete e attacchi interni. È possibile usare il controllo per monitorare l'attività di utenti, la conformità alle normative di documento, eseguire analisi forensi e altro ancora. Gli avvisi forniscono notifiche di eventi di sicurezza.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Assegnare ruoli di amministratore con privilegi minimi per le operazioni

Quando si esaminano l'approccio adottato per le operazioni, esistono un paio di livelli di amministrazione da prendere in considerazione. Il primo livello colloca il carico di lavoro amministrazione nella finestra di amministratori globali. Usa sempre il ruolo di amministratore globale, potrebbe essere appropriato per piccole imprese. Ma per le organizzazioni più grandi con personale dell'help desk e agli amministratori responsabili di attività specifiche, assegnare il ruolo di amministratore globale può essere un rischio per la sicurezza poiché viene fornita alle persone che con la possibilità di gestire le attività che sono conclusioni e approfondimenti Ciò che devono essere in grado di procedere.

In questo caso, è consigliabile il livello successivo di amministrazione. Uso di Azure AD, è possibile designare gli utenti finali come "limitato administrators", che possono gestire le attività nei ruoli con meno privilegi. Ad esempio, è possibile assegnare l'intervento dell'utente personale di assistenza il [lettura per la sicurezza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) ruolo fornire loro la possibilità di gestire funzionalità relative alla sicurezza con accesso in sola lettura. O forse è opportuno assegnare il [amministratore authentication](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) ruoli a singoli utenti agli utenti la possibilità di reimpostare le credenziali della password non o leggere e configurare Azure Service Health.

Per altre informazioni, vedere [le autorizzazioni del ruolo amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorare i componenti di hybrid (sincronizzazione di Azure AD Connect, AD FS) con Azure AD Connect Health

Azure AD Connect e AD FS sono componenti critici che possono causare il malfunzionamento di autenticazione e la gestione del ciclo di vita e alla fine determinare interruzioni dei servizi. Pertanto, è consigliabile distribuire Azure AD Connect Health per il monitoraggio e reporting di questi componenti.

Per altre informazioni, visita read [monitorare AD FS con Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Usare monitoraggio di Azure per raccogliere i log dei dati di analitica

[Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) è un portale di monitoraggio unificato per tutti i log di Azure AD, che fornisce approfondite, advanced analitica e apprendimento automatico intelligente. Monitoraggio di Azure, è possibile usare le metriche e i log all'interno del portale e tramite le API per ottenere maggiore visibilità sullo stato e le prestazioni delle tue risorse. In questo modo un unico riquadro dell'esperienza di ingrandimento all'interno del portale durante l'abilitazione di un'ampia gamma delle integrazioni dei prodotti tramite opzioni di esportazione di dati e API che supportano i tradizionali sistemi SIEM di terze parti. Monitoraggio di Azure offre anche la possibilità di configurare le regole di avviso per ricevere una notifica o intraprendere azioni automatiche sui problemi che influiscono sulle risorse.

![Monitoraggio di Azure](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Creare dashboard personalizzati per la leadership e il giorno per giorno

Le organizzazioni che non dispongono di una soluzione SIEM è possono scaricare il [pacchetto di contenuto di Power BI](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) per Azure AD. Power BI content pack contiene i report predefiniti che consentono di comprendere come gli utenti adottano e usano le funzionalità di Azure AD, che consente di ottenere informazioni approfondite tutte le attività all'interno della directory. È anche possibile creare una propria [dashboard personalizzato](https://docs.microsoft.com/power-bi/service-dashboards) e condividere con il team di leadership per segnalare le attività quotidiane. I dashboard sono un ottimo modo per monitorare l'azienda e visualizzare tutte le metriche più importanti a colpo. Le visualizzazioni in un dashboard possono provenire da un set di dati sottostante o a numerose colonne e da report sottostanti o molti. Un dashboard combina in locale e cloud dei dati, offrendo una visualizzazione consolidata indipendentemente da dove si trovano i dati.

![Dashboard personalizzato di Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Comprendere i driver di chiamata di supporto

Quando si implementa una soluzione di identità ibrida, come descritto in questo articolo, si dovrebbe notare una riduzione in definitiva nelle chiamate alla assistenza. Problemi comuni, ad esempio le password dimenticate e i blocchi degli account vengano risolti implementando la reimpostazione della password self-service di Azure, consentendo l'accesso alle applicazioni self-service consente agli utenti di individuare da soli e richiedere l'accesso alle applicazioni senza dover ricorrere a il personale IT.

Se si non osserva una riduzione delle chiamate al supporto tecnico, è consigliabile analizzare i driver di chiamata di supporto nel tentativo di confermare se SSPR o accesso alle applicazioni self-service è stata configurata correttamente o se sono presenti altri nuovi problemi che possono essere sistematicamente una risposta.

*"Nel nostro viaggio di trasformazione digitale, è necessaria un'identità affidabile e provider di gestione di accesso per semplificare l'integrazione senza problemi ancora sicuro tra Stati Uniti, partner e provider di servizi cloud, per un ecosistema; efficace Azure AD è la migliore opzione che ci offre le funzionalità necessarie e visibilità che ci ha permesso di rilevare e rispondere ai rischi."* --- [Aramex Yazan Almasri, direttore di sicurezza le informazioni globali,](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitorare l'utilizzo delle App per ottenere informazioni dettagliate

Oltre a individuare Shadow IT, monitoraggio dell'utilizzo delle app tra l'organizzazione Usa [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) può aiutare l'azienda quando si spostano sfruttare appieno il potenziale delle applicazioni cloud. Consente di rimanere in controllo degli asset tramite una migliore visibilità nelle attività e aumentare la protezione dei dati critici nelle applicazioni cloud. Monitoraggio dell'utilizzo delle app nella propria organizzazione usando MCAS consentono di rispondere alle domande seguenti:

* Quali App non approvate per archiviare i dati in utilizza dipendenti?
* Dove e quando i dati sensibili vengono archiviati in cloud?
* Chi accede a dati sensibili nel cloud?

*"Con Cloud App Security, è possibile rapidamente anomalie e intervenire."* --- [Eric LePenske, Senior Manager, la sicurezza delle informazioni, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Riepilogo

Numerosi aspetti relativi all'implementazione di una soluzione di identità ibrida, ma questo elenco di controllo di quattro passaggi consentono di eseguire rapidamente un'infrastruttura di identità che consentirà agli utenti di essere più produttivi e protetto.

* Connettersi facilmente alle App
* Stabilire automaticamente una sola identità per ogni utente
* Consentire agli utenti in modo sicuro
* Rendere operative le tue analisi

Ci auguriamo che questo documento è una Guida di orientamento utile per stabilire una base di gestione avanzata delle identità per l'organizzazione.

## <a name="identity-checklist"></a>Elenco di controllo di identità

È consigliabile stampare l'elenco di controllo seguente come riferimento quando si inizia il tuo viaggio verso una base più solida di identità all'interno dell'organizzazione.

### <a name="today"></a>Oggi

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Pilota Self - Service di servizio di reimpostazione delle Password (SSPR) per un gruppo|
||Monitorare le componenti ibrida con Azure AD Connect Health|
||Assegnare ruoli di amministratore con privilegi minimi per l'operazione|
||Scoprire Shadow IT con Microsoft Cloud App Security|
||Usare monitoraggio di Azure per raccogliere i log dei dati per l'analisi|

### <a name="next-two-weeks"></a>Prossime due settimane

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Rendere disponibile un'app per gli utenti|
||Distribuzione pilota di provisioning di Azure AD per un'app SaaS di scelta|
||Configurare un server di gestione temporanea di Azure AD Connect e mantenerlo aggiornato|
||Avviare la migrazione di App da ad FS con Azure AD|
||Creare dashboard personalizzati per la leadership e il giorno per giorno|

### <a name="next-month"></a>Mese successivo

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Monitorare l'utilizzo delle App per ottenere informazioni dettagliate|
||Pilota accesso remoto sicuro alle App|
||Verificare che tutti gli utenti sono registrati per MFA e SSPR|
||Abilitare l'autenticazione cloud|

### <a name="next-three-months"></a>Prossimi tre mesi

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Abilitare la gestione self-service di app|
||Abilita gestione gruppi self-service|
||Monitorare l'utilizzo delle App per ottenere informazioni dettagliate|
||Comprendere i driver di chiamata di supporto|

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come è possibile aumentare il comportamento sicuro usando le funzionalità di Azure Active Directory e questo elenco di controllo - cinque passaggi [cinque passaggi per la protezione dell'infrastruttura di identità](https://aka.ms/securitysteps).

Informazioni su come le funzionalità di identità in Azure AD consente di accelerare la transizione al cloud di gestione regolamentati, fornendo le soluzioni e funzionalità che consentono alle organizzazioni di adottare rapidamente e spostare più la gestione di identità che spaziano da sistemi in Azure AD - locali [modo in cui Azure AD offre disciplinato gestione Cloud per i carichi di lavoro On-Premises](https://aka.ms/cloudgoverned).
