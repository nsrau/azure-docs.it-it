---
title: Quattro passaggi per una solida base di identità con Azure Active Directory
description: Questo argomento descrive quattro passaggi che i clienti di identità ibrida possono intraprendere per creare una base di identità avanzata.
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
ms.openlocfilehash: d2cd080602ec879affb3ece978f14d5afc33ef9d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173094"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Quattro passaggi per una solida base di identità con Azure Active Directory

La gestione dell'accesso alle app e ai dati non può più basarsi sulle strategie di limite di sicurezza di rete tradizionali, ad esempio le reti perimetrali e i firewall, grazie al rapido spostamento delle app nel cloud. Ora le organizzazioni devono considerare attendibile la propria soluzione di identità per controllare chi ha accesso alle app e ai dati dell'organizzazione. Un numero maggiore di organizzazioni consente ai dipendenti di usare i propri dispositivi per lavorare e di usare i propri dispositivi ovunque possano connettersi a Internet. Verificare che questi dispositivi siano conformi e sicuri siano diventati una considerazione importante nella soluzione di identità che un'organizzazione sceglie di implementare. Nell'area di lavoro digitale odierna, [l'identità è il piano di controllo primario](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) di qualsiasi organizzazione che passa al cloud.

Quando si adotta una soluzione di identità ibrida Azure Active Directory (Azure AD), le organizzazioni ottengono l'accesso alle funzionalità Premium che consentono di sbloccare la produttività tramite funzionalità di automazione, delega, self-service e Single Sign-On. Consente ai dipendenti di accedere alle risorse aziendali da qualsiasi luogo che debbano svolgere il proprio lavoro consentendo al team IT di governare tale accesso garantendo che gli utenti giusti dispongano del diritto di accesso alle risorse appropriate per stabilire una produttività sicura.

In base alle nostre informazioni, questo elenco di controllo delle procedure consigliate ti aiuterà a distribuire rapidamente le azioni consigliate per creare una *solida* identità di base nell'organizzazione:

* Connetti facilmente alle app
* Stabilire automaticamente un'identità per ogni utente
* Consentire agli utenti in modo sicuro
* Rendere operativo informazioni dettagliate

## <a name="step-1---connect-to-apps-easily"></a>Passaggio 1: connettersi facilmente alle app

Connettendo le app con Azure AD, è possibile migliorare la produttività e la protezione degli utenti finali abilitando Single Sign-On (SSO) ed effettuando il provisioning degli utenti. Gestendo le app in un'unica posizione, Azure AD, è possibile ridurre al minimo il sovraccarico amministrativo e ottenere un singolo punto di controllo per i criteri di sicurezza e conformità.

Questa sezione illustra le opzioni per la gestione dell'accesso degli utenti alle app, l'abilitazione dell'accesso remoto sicuro alle app interne e i vantaggi della migrazione delle app a Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Rendere le app disponibili per gli utenti in modo uniforme

Azure AD consente agli amministratori di [aggiungere applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) alla raccolta di applicazioni aziendali nel [portale di Azure](https://portal.azure.com/). L'aggiunta di applicazioni alla raccolta di applicazioni aziendali semplifica la configurazione delle applicazioni per l'uso di Azure AD come provider di identità. Consente inoltre di gestire l'accesso degli utenti all'applicazione con criteri di accesso condizionale e di configurare Single Sign-On (SSO) per le applicazioni in modo che gli utenti non debbano immettere le password ripetutamente e vengano automaticamente firmate in locale e applicazioni basate su cloud.

Una volta aggiunte le applicazioni alla raccolta di Azure AD, gli utenti possono visualizzare le app a loro assegnate e cercare e richiedere altre app in base alle esigenze. Azure AD offre [diversi metodi](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) per consentire agli utenti di accedere alle app:

* Pannello di accesso/app personali
* Utilità di avvio app di Office 365
* Accesso diretto alle applicazioni federate
* Collegamenti diretti Single Sign-On

Per altre informazioni sull'accesso degli utenti alle app, vedere **il passaggio 3: consentire agli utenti** di questo articolo.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Eseguire la migrazione di app da Active Directory Federation Services a Azure AD

La migrazione di Single Sign-On configurazione da Active Directory Federation Services (ADFS) a Azure AD offre funzionalità aggiuntive per la sicurezza, una gestione più coerente e una collaborazione. Per risultati ottimali, è consigliabile eseguire la migrazione delle app da AD FS a Azure AD. L'autenticazione e l'autorizzazione dell'applicazione per Azure AD offrono i vantaggi seguenti:

* Gestione dei costi
* Gestione dei rischi
* Aumento della produttività
* Risoluzione di conformità e governance

Per altre informazioni, vedere il white paper relativo alla [migrazione delle applicazioni a Azure Active Directory](https://aka.ms/migrateapps/whitepaper) .

### <a name="enable-secure-remote-access-to-apps"></a>Abilitare l'accesso remoto sicuro alle app

[Azure ad proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) fornisce una soluzione semplice per le organizzazioni per la pubblicazione di applicazioni locali nel cloud per gli utenti remoti che necessitano dell'accesso alle app interne in modo sicuro. Dopo un Single Sign-On Azure AD, gli utenti possono accedere alle applicazioni cloud e locali tramite URL esterni o un portale di applicazioni interno.

Azure AD proxy di applicazione offre i vantaggi seguenti:

* Estensione di Azure AD a risorse locali
  * Sicurezza e protezione a livello di scalabilità
  * Funzionalità quali l'accesso condizionale e Multi-Factor Authentication facili da abilitare
* Nessun componente nella rete perimetrale, ad esempio soluzioni VPN e proxy inverso tradizionali
* Nessuna connessione in ingresso necessaria
* Single Sign-on (SSO) tra dispositivi, risorse e app nel cloud e in locale
* Consentire agli utenti finali di essere produttivi in qualsiasi momento e ovunque

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Individuare Shadow IT con Microsoft Cloud App Security

Nelle aziende moderne, i reparti IT spesso non sono a conoscenza di tutte le applicazioni cloud usate dagli utenti per svolgere il proprio lavoro. Quando gli amministratori IT vengono invitati a quante app Cloud pensano che i dipendenti usino, in media si dicono 30 o 40. In realtà, la media è superiore a 1.000 app separate usate dai dipendenti dell'organizzazione. il 80% dei dipendenti USA app non approvate che nessuno ha esaminato e che potrebbero non essere conformi ai criteri di sicurezza e conformità.

[Microsoft cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) consente di identificare le app utili più diffuse con gli utenti che potrebbero approvare e aggiungere alla raccolta di applicazioni aziendali in modo che gli utenti possano trarre vantaggio dalle funzionalità quali SSO e accesso condizionale.

<em>"**Cloud app Security** ci aiuta a garantire che gli utenti utilizzino correttamente le nostre applicazioni cloud e Saas, in modo da supportare i criteri di sicurezza di base che consentono di proteggere Accenture".</em> --- [John Blasi, Managing Director, Information Security, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Oltre a rilevare shadow IT, MCAS può anche determinare il livello di rischio delle app, impedire l'accesso non autorizzato ai dati aziendali, possibili perdite di dati e altri rischi per la sicurezza inerenti alle applicazioni.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Passaggio 2: stabilire automaticamente un'identità per ogni utente

La possibilità di riunire directory locali e basate sul cloud in una soluzione di identità ibrida Azure AD consentirà di riutilizzare l'investimento Active Directory locale esistente effettuando il provisioning delle identità esistenti nel cloud. La soluzione sincronizza le identità locali con Azure AD, mentre mantiene l'Active Directory locale in esecuzione con qualsiasi soluzione di governance esistente come origine primaria della verità per le identità. La soluzione di identità ibrida Azure AD Microsoft si estende sulle funzionalità locali e basate sul cloud, creando un'identità utente comune per l'autenticazione e l'autorizzazione per tutte le risorse, indipendentemente dalla loro posizione.

L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi e impedisce agli utenti di usare più account tra app e servizi fornendo un'identità comune per accedere alle risorse cloud e locali. L'uso di più account è un punto problematico per gli utenti finali e per lo stesso. Dal punto di vista dell'utente finale, la presenza di più account implica la necessità di ricordare più password. Per evitare questo problema, molti utenti riutilizzeranno la stessa password per ogni account, il che non è valido dal punto di vista della sicurezza. Dal punto di vista IT, il riutilizzo spesso comporta la reimpostazione della password e i costi del supporto tecnico insieme ai reclami degli utenti finali.

Azure AD Connect è lo strumento usato per sincronizzare le identità locali con Azure AD, che può quindi essere usato per accedere alle applicazioni cloud. Una volta che le identità si trovano in Azure AD, è possibile effettuare il provisioning in applicazioni SaaS come Salesforce o Concord.

In questa sezione vengono elencate le raccomandazioni per fornire disponibilità elevata, autenticazione moderna per il cloud e riduzione del footprint locale.

> [!NOTE]
> Per ulteriori informazioni su Azure AD Connect, vedere [che cos'è Azure ad Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Configurare un server di staging per Azure AD Connect e mantenerlo aggiornato

Azure AD Connect svolge un ruolo chiave nel processo di provisioning. Se il server di sincronizzazione passa alla modalità offline per qualsiasi motivo, le modifiche apportate al sito locale non verranno aggiornate nel cloud e si verificheranno problemi di accesso agli utenti. È importante definire una strategia di failover che consenta agli amministratori di riprendere rapidamente la sincronizzazione dopo che il server di sincronizzazione passa alla modalità offline.

Per garantire un'elevata disponibilità nel caso in cui il server di Azure AD Connect primario passa alla modalità offline, è consigliabile distribuire un [server di gestione temporanea](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) separato per Azure ad Connect. La distribuzione di un server consente all'amministratore di "alzare di livello" il server di gestione temporanea alla produzione mediante una semplice opzione di configurazione. La presenza di un server di standby configurato in modalità di staging consente anche di testare e distribuire le nuove modifiche di configurazione e di introdurre un nuovo server se si rimuove le autorizzazioni precedenti.

> [!TIP]
> Azure AD Connect viene aggiornato a intervalli regolari. Pertanto, è consigliabile che il server di gestione temporanea venga aggiornato per sfruttare i miglioramenti delle prestazioni, le correzioni di bug e le nuove funzionalità disponibili in ogni nuova versione.

### <a name="enable-cloud-authentication"></a>Abilitare l'autenticazione cloud

Le organizzazioni con Active Directory locale devono estendere la directory ai Azure AD utilizzando Azure AD Connect e configurare il metodo di autenticazione appropriato. La [scelta del metodo di autenticazione corretto](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) per l'organizzazione è il primo passaggio per lo spostamento delle app nel cloud. Si tratta di un componente essenziale perché controlla l'accesso a tutti i dati e le risorse del cloud.

Il metodo più semplice e consigliato per abilitare l'autenticazione cloud per gli oggetti directory locali in Azure AD consiste nell'abilitare la [sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (pH). In alternativa, alcune organizzazioni possono prendere in considerazione l'abilitazione dell' [autenticazione pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Se si sceglie pH o PTA, non dimenticare di abilitare l'accesso [Single Sign-on](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) facile per consentire agli utenti di accedere alle app cloud senza immettere costantemente il nome utente e la password nell'app quando si usano dispositivi Windows 7 e 8 nella rete aziendale. Senza Single Sign-On, gli utenti devono ricordare le password specifiche dell'applicazione e accedere a ogni applicazione. Allo stesso modo, il personale IT deve creare e aggiornare gli account utente per ogni applicazione, ad esempio Office 365, box e Salesforce. Gli utenti devono ricordare le proprie password, oltre a dedicare il tempo necessario per accedere a ogni applicazione. Fornire un meccanismo di Single Sign-On standardizzato per l'intera organizzazione è fondamentale per ottimizzare l'esperienza utente, la riduzione del rischio, la possibilità di creare report e governance.

Per le organizzazioni che usano già AD FS o un altro provider di autenticazione locale, il passaggio a Azure AD come provider di identità può ridurre la complessità e migliorare la disponibilità. A meno che non si disponga di casi d'uso specifici per l'uso della Federazione, è consigliabile eseguire la migrazione dall'autenticazione federata a pH e seamless SSO o PTA e a seamless SSO per sfruttare i vantaggi di un footprint locale ridotto e la flessibilità offerta dal cloud con esperienza utente migliorata. Per ulteriori informazioni, vedere [eseguire la migrazione dalla Federazione alla sincronizzazione dell'hash delle password per Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Abilitare il deprovisioning automatico degli account

L'abilitazione del provisioning e deprovisioning automatizzato per le applicazioni è la strategia migliore per gestire il ciclo di vita delle identità in più sistemi. Azure AD supporta il [provisioning automatizzato basato su criteri e il deprovisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) degli account utente in un'ampia gamma di applicazioni SaaS comuni, ad esempio ServiceNow e Salesforce, e altre che implementano il [protocollo scim 2,0](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). Diversamente dalle soluzioni di provisioning tradizionali, che richiedono codice personalizzato o caricamento manuale dei file CSV, il servizio di provisioning è ospitato nel cloud e include connettori preintegrati che possono essere configurati e gestiti usando il portale di Azure. Un vantaggio fondamentale del deprovisioning automatico è che aiuta a proteggere l'organizzazione rimuovendo immediatamente le identità degli utenti dalle principali app SaaS quando lasciano l'organizzazione.

Per altre informazioni sul provisioning automatico degli account utente e sul relativo funzionamento, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Passaggio 3: consentire agli utenti in modo sicuro

Nell'area di lavoro digitale odierna è importante bilanciare la sicurezza con la produttività. Tuttavia, gli utenti finali spesso eseguono il push sulle misure di sicurezza che rallentano la produttività e l'accesso alle app cloud. Per risolvere questo problema, Azure AD offre funzionalità self-service che consentono agli utenti di rimanere produttivi riducendo al minimo il sovraccarico amministrativo.

Questa sezione elenca i consigli per la rimozione dell'attrito dall'organizzazione grazie alla possibilità per gli utenti di rimanere vigili.

### <a name="enable-self-service-password-reset-for-all-users"></a>Abilitare la reimpostazione self-service delle password per tutti gli utenti

La [reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) di Azure (SSPR) offre agli amministratori IT un metodo semplice per consentire agli utenti di reimpostare e sbloccare le password o gli account senza l'intervento dell'amministratore. Il sistema include report dettagliati per tenere traccia del momento in cui gli utenti accedono al sistema, oltre a notifiche per segnalare usi impropri.

Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password. Tuttavia, quando si Abilita l' [integrazione Azure ad Connect in locale](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), è anche possibile separare le due operazioni, che consentono agli utenti di sbloccare l'account senza dover reimpostare la password.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Verificare che tutti gli utenti siano registrati per l'autenticazione a più fattori e SSPR

Azure fornisce report che possono essere usati dall'utente e dall'organizzazione per assicurarsi che gli utenti siano registrati per l'autenticazione a più fattori e SSPR. Per gli utenti che non hanno eseguito la registrazione potrebbe essere necessario istruire il processo.

Il [report degli accessi](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) a più fattori include informazioni sull'utilizzo dell'autenticazione a più fattori e fornisce informazioni approfondite sul funzionamento dell'autenticazione a più fattori nell'organizzazione. La possibilità di accedere alle attività di accesso (e ai controlli e ai rilevamenti dei rischi) per Azure AD è fondamentale per la risoluzione dei problemi, l'analisi dell'utilizzo e le indagini forensi.

Analogamente, è possibile usare il [report di gestione delle password self-service](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) per determinare chi ha o non è registrato per sspr.

### <a name="self-service-app-management"></a>Gestione self-service delle app

Prima che gli utenti possano individuare autonomamente le applicazioni dal pannello di accesso, è necessario abilitare [l'accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) alle applicazioni per le quali si desidera consentire agli utenti di individuare autonomamente e richiedere l'accesso. L'accesso alle applicazioni self-service è un ottimo modo per consentire agli utenti di individuare autonomamente le applicazioni e, facoltativamente, consentire al gruppo aziendale di approvare l'accesso a tali applicazioni. È possibile consentire al gruppo aziendale di gestire le credenziali assegnate a tali utenti per le applicazioni con accesso [Single Sign-on tramite password](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) direttamente dai rispettivi pannelli di accesso.

### <a name="self-service-group-management"></a>Gestione di gruppi self-service

Quando si usano i gruppi, è consigliabile assegnare utenti alle applicazioni, in quanto consentono una grande flessibilità e capacità di gestione su larga scala:

* Basata su attributi mediante l'appartenenza dinamica ai gruppi
* Delega ai proprietari dell'app

Azure AD offre la possibilità di gestire l'accesso alle risorse con gruppi di sicurezza e gruppi di Office 365. Questi gruppi possono essere gestiti da un proprietario del gruppo che può approvare o rifiutare le richieste di appartenenza e delegare il controllo dell'appartenenza al gruppo. Noto come [gestione dei gruppi self-service](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), questa funzionalità consente di risparmiare tempo consentendo ai proprietari del gruppo a cui non è assegnato un ruolo amministrativo di creare e gestire gruppi senza dover affidarsi agli amministratori per gestire le richieste.

## <a name="step-4---operationalize-your-insights"></a>Passaggio 4: rendere operativo informazioni dettagliate

Il controllo e la registrazione degli eventi correlati alla sicurezza e degli avvisi correlati sono componenti essenziali di una strategia efficace per garantire che gli utenti rimangano produttivi e che l'organizzazione sia protetta. I registri di sicurezza e i report possono essere utili per rispondere a domande come le seguenti:

* Stai usando quello che stai pagando?
* C'è qualcosa di sospetto o dannoso nel tenant?
* Chi ha avuto effetto durante un evento imprevisto della sicurezza?

I report e i log di sicurezza offrono un record elettronico delle attività sospette e aiutano a rilevare criteri che possono indicare tentativi esterni di penetrazione nella rete e attacchi interni. È possibile utilizzare il controllo per monitorare le attività degli utenti, documentare la conformità alle normative, eseguire analisi forensi e altro ancora. Gli avvisi forniscono notifiche di eventi di sicurezza.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Assegnare ruoli di amministratore con privilegi minimi per le operazioni

Quando si pensa all'approccio alle operazioni, è necessario prendere in considerazione un paio di livelli di amministrazione. Il primo livello pone il carico di amministrazione per gli amministratori globali. Usare sempre il ruolo di amministratore globale, potrebbe essere appropriato per le aziende più piccole. Tuttavia, per le organizzazioni di grandi dimensioni con help desk personale e amministratori responsabili di attività specifiche, l'assegnazione del ruolo di amministratore globale può costituire un rischio per la sicurezza, poiché fornisce a tali utenti la possibilità di gestire le attività oltre cosa dovrebbe essere in grado di eseguire.

In questo caso, è necessario prendere in considerazione il livello di amministrazione successivo. Utilizzando Azure AD, è possibile designare gli utenti finali come "amministratori limitati" che possono gestire le attività nei ruoli con privilegi di minore entità. Ad esempio, è possibile assegnare al personale help desk il ruolo di [lettore di sicurezza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) per fornire loro la possibilità di gestire le funzionalità relative alla sicurezza con accesso in sola lettura. O forse è opportuno assegnare il ruolo di [amministratore dell'autenticazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) a singoli utenti per concedere loro la possibilità di reimpostare le credenziali non password o leggere e configurare l'integrità dei servizi di Azure.

Per altre informazioni, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorare i componenti ibridi (Azure AD Connect Sync, AD FS) usando Azure AD Connect Health

Azure AD Connect e AD FS sono componenti fondamentali che possono potenzialmente interrompere la gestione del ciclo di vita e l'autenticazione e infine causare interruzioni. Pertanto, è necessario distribuire Azure AD Connect Health per il monitoraggio e la creazione di report di questi componenti.

Per altre informazioni, vedere [ad FS di monitoraggio con Azure ad Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Usare monitoraggio di Azure per raccogliere i log di dati per l'analisi

[Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) è un portale di monitoraggio unificato per tutti i log di Azure ad, che fornisce informazioni approfondite, analisi avanzate e smart machine learning. Con monitoraggio di Azure è possibile usare le metriche e i log nel portale e tramite le API per ottenere una maggiore visibilità sullo stato e sulle prestazioni delle risorse. Consente un unico riquadro di esperienza in vetro all'interno del portale, abilitando un'ampia gamma di integrazioni di prodotti tramite API ed opzioni di esportazione dei dati che supportano i tradizionali sistemi SIEM di terze parti. Monitoraggio di Azure offre inoltre la possibilità di configurare le regole di avviso per ricevere notifiche o eseguire azioni automatiche sui problemi che influiscano sulle risorse.

![Monitoraggio di Azure](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Crea dashboard personalizzati per la tua leadership e il giorno a giorno

Le organizzazioni che non dispongono di una soluzione SIEM possono scaricare il [pacchetto di contenuto Power bi](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) per Azure ad. Il pacchetto di contenuto Power BI contiene report predefiniti che consentono di comprendere in che modo gli utenti adottano e usano le funzionalità di Azure AD, che consente di ottenere informazioni dettagliate su tutte le attività all'interno della directory. È anche possibile creare [dashboard personalizzati](https://docs.microsoft.com/power-bi/service-dashboards) e condividerli con il team di leadership per segnalare le attività quotidiane. I dashboard sono un ottimo modo per monitorare l'azienda e visualizzare a colpo d'occhio tutte le metriche più importanti. Le visualizzazioni in un dashboard possono provenire da un set di dati sottostante o da molti e da un report sottostante o molti. Un dashboard combina i dati locali e cloud, offrendo una visualizzazione consolidata indipendentemente da dove si trovano i dati.

![Power BI Dashboard personalizzato](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Informazioni sui driver delle chiamate di supporto

Quando si implementa una soluzione di identità ibrida come descritto in questo articolo, è necessario notare una riduzione delle chiamate al supporto tecnico. Problemi comuni, ad esempio le password dimenticate e i blocchi degli account, vengono attenuati implementando la reimpostazione della password self-service di Azure, abilitando l'accesso alle applicazioni self-service consente agli utenti di individuare autonomamente e richiedere l'accesso alle applicazioni senza basarsi per il personale IT.

Se non si osserva una riduzione delle chiamate al supporto tecnico, è consigliabile analizzare i driver delle chiamate di supporto nel tentativo di verificare se la configurazione di SSPR o dell'accesso alle applicazioni self-service è stata eseguita correttamente o se sono presenti altri nuovi problemi che possono essere sistematicamente affrontato.

*"Nel nostro percorso di trasformazione digitale abbiamo bisogno di un provider di gestione delle identità e degli accessi affidabile per semplificare l'integrazione senza problemi tra US, partner e provider di servizi cloud, per un ecosistema efficace; Azure AD è stata l'opzione migliore che offre le funzionalità e la visibilità necessarie che ci hanno permesso di rilevare e rispondere ai rischi ".* --- [ALMASRI, Global Information Security Director, ARAMEX](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitora l'utilizzo delle app per ottenere informazioni dettagliate

Oltre ad individuare Shadow IT, il monitoraggio dell'utilizzo delle app nell'organizzazione tramite [Microsoft cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) può aiutare la tua organizzazione a sfruttare al meglio le potenzialità delle applicazioni cloud. Consente di ottenere il controllo delle risorse grazie a una maggiore visibilità delle attività e di aumentare la protezione dei dati critici nelle applicazioni cloud. Il monitoraggio dell'utilizzo delle app nell'organizzazione tramite MCAS può essere utile per rispondere alle domande seguenti:

* Quali app non autorizzate sono i dipendenti che usano per archiviare i dati?
* Dove e quando vengono archiviati i dati sensibili nel cloud?
* Chi sta accedendo ai dati sensibili nel cloud?

*"Con Cloud App Security, possiamo individuare rapidamente le anomalie e intervenire".* --- [Eric LePenske, Senior Manager, Information Security, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Summary

Esistono molti aspetti per implementare una soluzione di identità ibrida, ma questo elenco di controllo in quattro passaggi consente di realizzare rapidamente un'infrastruttura di identità che consentirà agli utenti di essere più produttivi e protetti.

* Connetti facilmente alle app
* Stabilire automaticamente un'identità per ogni utente
* Consentire agli utenti in modo sicuro
* Rendere operativo informazioni dettagliate

Ci auguriamo che questo documento sia una guida utile per la creazione di una solida base di identità per la tua organizzazione.

## <a name="identity-checklist"></a>Elenco di controllo identità

Si consiglia di stampare l'elenco di controllo seguente per informazioni di riferimento quando si inizia il percorso a una base di identità più solida nell'organizzazione.

### <a name="today"></a>Oggi

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Reimpostazione della password self-service (SSPR) pilota per un gruppo|
||Monitorare i componenti ibridi usando Azure AD Connect Health|
||Assegnare i ruoli di amministratore con privilegi minimi per l'operazione|
||Individuare Shadow IT con Microsoft Cloud App Security|
||Usare monitoraggio di Azure per raccogliere i log di dati per l'analisi|

### <a name="next-two-weeks"></a>Prossime due settimane

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Rendere disponibile un'app per gli utenti|
||Provisioning di Azure AD pilota per un'app SaaS preferita|
||Configurare un server di gestione temporanea per Azure AD Connect e mantenerlo aggiornato|
||Avviare la migrazione di app da ADFS a Azure AD|
||Crea dashboard personalizzati per la tua leadership e il giorno a giorno|

### <a name="next-month"></a>Mese prossimo

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Monitora l'utilizzo delle app per ottenere informazioni dettagliate|
||Pilota sicuro per l'accesso remoto alle app|
||Verificare che tutti gli utenti siano registrati per l'autenticazione a più fattori e SSPR|
||Abilitare l'autenticazione cloud|

### <a name="next-three-months"></a>Prossimi tre mesi

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Abilita gestione self-service delle app|
||Abilitare la gestione dei gruppi self-service|
||Monitora l'utilizzo delle app per ottenere informazioni dettagliate|
||Informazioni sui driver delle chiamate di supporto|

## <a name="next-steps"></a>Passaggi successivi

Scopri come aumentare la tua postura sicura usando le funzionalità di Azure Active Directory e questo elenco di controllo in cinque [passaggi, cinque passaggi per la sicurezza dell'infrastruttura di identità](https://aka.ms/securitysteps).

Scopri come le funzionalità di identità in Azure AD possono aiutarti ad accelerare la transizione alla gestione governata dal cloud fornendo le soluzioni e le funzionalità che consentono alle organizzazioni di adottare e spostare rapidamente una maggiore gestione delle identità dalla tradizionale sistemi locali per Azure AD il modo in cui [Azure ad offre la gestione gestita dal cloud per i carichi di lavoro locali](https://aka.ms/cloudgoverned).
