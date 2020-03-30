---
title: Quattro passaggi per una solida base di identità - Azure ADFour steps to a strong identity foundation - Azure AD
description: In questo argomento vengono descritti quattro passaggi che i clienti di identità ibrida possono eseguire per creare una base di identità sicura.
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
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206786"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Quattro passaggi per una solida base di identità con Azure Active Directory

La gestione dell'accesso ad app e dati non può più basarsi sulle tradizionali strategie di sicurezza di rete, ad esempio reti perimetrali e firewall, a causa del rapido spostamento delle app nel cloud. Ora le organizzazioni devono fidarsi della propria soluzione di identità per controllare chi e cosa ha accesso alle app e ai dati dell'organizzazione. Più organizzazioni consentono ai dipendenti di portare i propri dispositivi al lavoro e di utilizzare i propri dispositivi da qualsiasi luogo in cui possano connettersi a Internet. Garantire che tali dispositivi siano conformi e sicuri è diventato una considerazione importante nella soluzione di identità che un'organizzazione sceglie di implementare. Nell'ambiente di lavoro digitale di oggi, [l'identità è il piano](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) di controllo principale di qualsiasi organizzazione che si sposta verso il cloud.

Nell'adozione di una soluzione di identità ibrida di Azure Active Directory (Azure AD), le organizzazioni ottengono l'accesso a funzionalità premium che sbloccano la produttività tramite funzionalità di automazione, delega, self-service e single sign-on. Consente ai lavoratori di accedere alle risorse aziendali da qualsiasi luogo equiberate, consentendo al team IT di controllare tale accesso garantendo alle persone giuste l'accesso alle risorse giuste per stabilire una produttività sicura.

In base ai nostri apprendimenti, questo elenco di controllo delle procedure consigliate ti aiuterà a distribuire rapidamente le azioni consigliate per creare una *solida* base di identità nella tua organizzazione:

* Connettiti facilmente alle app
* Stabilire automaticamente un'identità per ogni utente
* Potenzia i tuoi utenti in modo sicuro
* Funzionamento delle tue intuizioni

## <a name="step-1---connect-to-apps-easily"></a>Passaggio 1 - Connettersi facilmente alle app

Connettendo le app ad Azure AD, è possibile migliorare la produttività e la sicurezza degli utenti finali abilitando l'accesso Single Sign-On (SSO) ed eseguire il provisioning degli utenti. La gestione delle app in un'unica posizione, Azure AD, consente di ridurre al minimo l'overhead amministrativo e di ottenere un singolo punto di controllo per i criteri di sicurezza e conformità.

Questa sezione illustra le opzioni per la gestione dell'accesso degli utenti alle app, l'abilitazione dell'accesso remoto sicuro alle app interne e i vantaggi della migrazione delle app ad Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Rendere le app disponibili per gli utenti senza problemi

Azure AD consente agli amministratori di [aggiungere applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) alla raccolta di applicazioni aziendali nel portale di [Azure.](https://portal.azure.com/) L'aggiunta di applicazioni alla raccolta di applicazioni aziendali semplifica la configurazione delle applicazioni per l'uso di Azure AD come provider di identità. Consente inoltre di gestire l'accesso utente all'applicazione con criteri di accesso condizionale e di configurare Single Sign-On (SSO) per le applicazioni in modo che gli utenti non debbano immettere ripetutamente le password e abbiano eseguito automaticamente l'accesso sia in locale che in locale applicazioni basate su cloud.

Dopo aver aggiunto le applicazioni alla raccolta di Azure AD, gli utenti possono visualizzare le app assegnate e cercare e richiedere altre app in base alle esigenze. Azure AD offre [diversi metodi](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) per gli utenti di accedere alle app:Azure AD provides several methods to users to access their apps:

* Pannello di accesso/App personali
* Icona di avvio delle app di Office 365
* Accesso diretto alle applicazioni federate
* Collegamenti diretti Single Sign-On

Per altre informazioni sull'accesso degli utenti alle app, vedere **Passaggio 3 - Acquisire gli utenti** in questo articolo.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Eseguire la migrazione di app da Active Directory Federation Services ad Azure ADMigrate apps from Active Directory Federation Services to Azure AD

La migrazione della configurazione Single Sign-On da Active Directory Federation Services (ADFS) ad Azure AD consente funzionalità aggiuntive in materia di sicurezza, gestibilità e collaborazione più coerenti. Per ottenere risultati ottimali, è consigliabile eseguire la migrazione delle app da ADFS ad Azure AD. L'assegnazione dell'autenticazione e dell'autorizzazione dell'applicazione in Azure AD offre i vantaggi seguenti:Portare l'autenticazione e l'autorizzazione dell'applicazione in Azure AD offre i vantaggi seguenti:Bringing your application authentication and authorization to

* Gestione dei costi
* Gestione dei rischi
* Aumento della produttività
* Affrontare la conformità e la governance

Per altre informazioni, vedere il white paper Migrazione delle applicazioni ad Azure Active Directory.To learn more, see [the Migrating Your Applications to Azure Active Directory](https://aka.ms/migrateapps/whitepaper) whitepaper.

### <a name="enable-secure-remote-access-to-apps"></a>Abilitare l'accesso remoto sicuro alle appEnable secure remote access to apps

[Il proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) di Azure AD offre una soluzione semplice per le organizzazioni per pubblicare app locali nel cloud per gli utenti remoti che devono accedere alle app interne in modo sicuro. Dopo un Single Sign-On in Azure AD, gli utenti possono accedere alle applicazioni cloud e locali tramite URL esterni o un portale interno delle applicazioni.

Il proxy di applicazione di Azure AD offre i vantaggi seguenti:Azure AD Application Proxy offers the following benefits:

* Estensione di Azure AD a risorse locali
  * Sicurezza e protezione a livello di scalabilità
  * Funzionalità come accesso condizionale e multi-factor Authentication facili da abilitare
* Nessun componente nella rete perimetrale, ad esempio VPN e soluzioni proxy inverse tradizionali
* Nessuna connessione in ingresso necessaria
* Single Sign-On (SSO) su dispositivi, risorse e app nel cloud e in locale
* Consente agli utenti finali di essere produttivi in qualsiasi momento e ovunque

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Scopri Shadow IT con Microsoft Cloud App Security

Nelle aziende moderne i reparti IT spesso non sono consapevoli di tutte le applicazioni cloud usate dagli utenti per svolgere il proprio lavoro. Quando agli amministratori IT viene chiesto quante app cloud pensano che i dipendenti utilizzino, in media dicono 30 o 40. In realtà, la media è superiore a 1.000 app separate utilizzate dai dipendenti dell'organizzazione. L'80% dei dipendenti utilizza app non approvate che nessuno ha esaminato e che potrebbero non essere conformi ai criteri di sicurezza e conformità.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) consente di identificare le app utili che sono popolari tra gli utenti che l'IT può sanzionare e aggiungere alla raccolta di applicazioni aziendali in modo che gli utenti traggano vantaggio da funzionalità quali SSO e accesso condizionale.

<em>"Cloud**App Security** ci aiuta a garantire che le nostre persone utilizzino correttamente le nostre applicazioni cloud e SaaS, in modo da supportare le politiche di sicurezza fondamentali che aiutano a proteggere Accenture."</em> --- [John Blasi, Amministratore Delegato, Sicurezza delle informazioni, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Oltre a rilevare l'IT shadow, MCAS può anche determinare il livello di rischio delle app, impedire l'accesso non autorizzato ai dati aziendali, la possibile perdita di dati e altri rischi per la sicurezza inerenti alle applicazioni.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Passaggio 2 - Stabilire automaticamente un'identità per ogni utenteStep 2 - Establish one identity for every user automatically

Riunire le directory locali e basate su cloud in una soluzione di identità ibrida di Azure AD consentirà di riutilizzare l'investimento di Active Directory locale esistente eseguendo il provisioning delle identità esistenti nel cloud. La soluzione esegue la sincronizzazione delle identità locali con Azure AD, mentre il software IT mantiene Active Directory locale in esecuzione con qualsiasi soluzione di governance esistente come fonte primaria di verità per le identità. La soluzione di identità ibrida di Azure AD di Microsoft si estende su funzionalità locali e basate su cloud, creando un'identità utente comune per l'autenticazione e l'autorizzazione a tutte le risorse indipendentemente dalla posizione.

L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi e impedisce agli utenti di usare più account tra app e servizi fornendo un'identità comune per l'accesso alle risorse cloud e locali. L'utilizzo di più account è un punto di dolore sia per gli utenti finali che per l'IT. Dal punto di vista dell'utente finale, avere più account significa dover ricordare più password. Per evitare questo problema, molti utenti riutilizzano la stessa password per ogni account, il che non è valido dal punto di vista della sicurezza. Dal punto di vista IT, il riutilizzo spesso comporta un abitatire maggiore reimpostazione della password e costi dell'helpdesk insieme ai reclami degli utenti finali.

Azure AD Connect è lo strumento usato per sincronizzare le identità locali con Azure AD, che può quindi essere usato per accedere alle applicazioni cloud. Una volta che le identità sono in Azure AD, possono eseguire il provisioning per le applicazioni SaaS come Salesforce o Concur.Once the identities are in Azure AD, they can provision to SaaS applications like Salesforce or Concur.

In questa sezione sono elencati i consigli per fornire disponibilità elevata, l'autenticazione moderna per il cloud e la riduzione del footprint locale.

> [!NOTE]
> Per altre informazioni su Azure AD Connect, vedere [Che cos'è](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) la sincronizzazione di Azure AD Connect?

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Configurare un server di gestione temporanea per Azure AD Connect e mantenerlo aggiornato

Azure AD Connect svolge un ruolo chiave nel processo di provisioning. Se il server di sincronizzazione non è in linea per qualsiasi motivo, le modifiche apportate all'ambiente locale non verranno aggiornate nel cloud e causano problemi di accesso agli utenti. È importante definire una strategia di failover che consenta agli amministratori di riprendere rapidamente la sincronizzazione dopo la disconnessione del server di sincronizzazione.

Per garantire la disponibilità elevata nel caso in cui il server Azure AD Connect primario non sia in linea, è consigliabile distribuire un server di gestione temporanea separato per Azure AD Connect.To provide high availability in the event your primary Azure AD Connect server goes offline, it's recommended that you deploy a separate [staging server](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) for Azure AD Connect. La distribuzione di un server consente all'amministratore di "promuovere" il server di staging all'ambiente di produzione tramite un semplice commutatore di configurazione. La configurazione di un server standby in modalità di gestione temporanea consente inoltre di testare e distribuire nuove modifiche alla configurazione e di introdurre un nuovo server se si rimuove la rimozione delle autorizzazioni di quello precedente.

> [!TIP]
> Azure AD Connect viene aggiornato regolarmente. Pertanto, è consigliabile mantenere aggiornato il server di gestione temporanea per sfruttare i miglioramenti delle prestazioni, le correzioni di bug e le nuove funzionalità fornite da ogni nuova versione.

### <a name="enable-cloud-authentication"></a>Abilitare l'autenticazione cloudEnable cloud authentication

Le organizzazioni con Active Directory locale devono estendere la propria directory ad Azure AD usando Azure AD Connect e configurare il metodo di autenticazione appropriato. [La scelta del metodo](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) di autenticazione corretto per l'organizzazione è il primo passaggio del percorso di spostamento delle app nel cloud. È un componente fondamentale poiché controlla l'accesso a tutti i dati e le risorse cloud.

Il metodo più semplice e consigliato per abilitare l'autenticazione cloud per gli oggetti directory locali in Azure AD consiste nell'abilitare la [sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). In alternativa, alcune organizzazioni possono prendere in considerazione l'abilitazione [dell'autenticazione pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Che tu scelga PHS o PTA, non dimenticare di abilitare [Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) per consentire agli utenti di accedere alle app cloud senza immettere costantemente il nome utente e la password nell'app quando usi i dispositivi Windows 7 e 8 nella tua rete aziendale. Senza Single Sign-On, gli utenti devono ricordare le password specifiche dell'applicazione e accedere a ogni applicazione. Analogamente, il personale IT deve creare e aggiornare gli account utente per ogni applicazione, ad esempio Office 365, Box e Salesforce. Gli utenti devono ricordare le password, oltre a dedicare il tempo ad accedere a ogni applicazione. Fornire un meccanismo Single Sign-On standardizzato all'intera azienda è fondamentale per la migliore esperienza utente, la riduzione del rischio, la capacità di reporte e la governance.

Per le organizzazioni che usano già ADFS o un altro provider di autenticazione locale, il passaggio ad Azure AD come provider di identità può ridurre la complessità e migliorare la disponibilità. A meno che non si disponga di casi d'uso specifici per l'utilizzo della federazione, è consigliabile eseguire la migrazione dall'autenticazione federata a PHS e Seamless SSO o PTA e Seamless SSO per sfruttare i vantaggi di una riduzione dell'ingombro locale e della flessibilità offerta dal cloud migliori esperienze utente. Per altre informazioni, vedere [Eseguire la migrazione dalla sincronizzazione dell'hash della federazione e delle password per Azure Active Directory.For](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)more information, see Migrate from federation to password hash synchronization for Azure Active Directory.

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Abilitare il deprovisioning automatico degli account

L'abilitazione del provisioning e del deprovisioning automatici per le applicazioni è la strategia migliore per gestire il ciclo di vita delle identità su più sistemi. Azure AD supporta il provisioning e il [deprovisioning automatici basati](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) su criteri degli account utente in un'ampia gamma di applicazioni SaaS più popolari, ad esempio ServiceNow e Salesforce, e altre che implementano il [protocollo SCIM 2.0.](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) A differenza delle soluzioni di provisioning tradizionali, che richiedono codice personalizzato o caricamento manuale di file CSV, il servizio di provisioning è ospitato nel cloud e offre connettori preintegrati che possono essere impostati e gestiti tramite il portale di Azure.Unlike traditional provisioning solutions, which require custom code or manual uploading of CSV files, the provisioning service is hosted in the cloud, and features pre-integrated connectors that can be set up and managed using the Azure portal. Uno dei principali vantaggi del deprovisioning automatico è che aiuta a proteggere l'organizzazione rimuovendo istantaneamente le identità degli utenti dalle principali app SaaS quando lasciano l'organizzazione.

Per altre informazioni sul provisioning automatico degli account utente e sul relativo funzionamento, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Fase 3 - Potenzia gli utenti in modo sicuro

Nell'ambiente di lavoro digitale di oggi, è importante bilanciare la sicurezza con la produttività. Tuttavia, gli utenti finali spesso spingono indietro sulle misure di sicurezza che rallentano la produttività e l'accesso alle app cloud. Per risolvere questo problema, Azure AD offre funzionalità self-service che consentono agli utenti di rimanere produttivi riducendo al minimo l'overhead amministrativo.

In questa sezione sono elencati i suggerimenti per rimuovere l'attrito dall'organizzazione consentendo agli utenti di rimanere vigili.

### <a name="enable-self-service-password-reset-for-all-users"></a>Abilitare la reimpostazione della password self-service per tutti gli utentiEnable Self-Service Password Reset for all users

La [reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) di Azure offre agli amministratori IT un semplice mezzo per consentire agli utenti di reimpostare e sbloccare le password o gli account senza l'intervento dell'amministratore. Il sistema include report dettagliati per tenere traccia del momento in cui gli utenti accedono al sistema, oltre a notifiche per segnalare usi impropri.

Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password. Tuttavia, quando si abilita l'integrazione di Azure AD Connect [in locale,](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)è anche possibile separare queste due operazioni, che consentono agli utenti di sbloccare l'account senza dover reimpostare la password.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Verificare che tutti gli utenti siano registrati per l'autenticazione a più fattori e SSPREnsure all users are registered for MFA and SSPR

Azure fornisce report che possono essere usati dall'utente e dall'organizzazione per garantire che gli utenti siano registrati per l'autenticazione a più fattori e SSPR. Gli utenti che non si sono registrati potrebbero dover essere istruiti sul processo.

Il report di [accesso](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) MFA include informazioni sull'utilizzo dell'autenticazione a più fattori e fornisce informazioni dettagliate sul funzionamento dell'autenticazione a più fattori nell'organizzazione. Avere accesso all'attività di accesso (e controlli e rilevamenti dei rischi) per Azure AD è fondamentale per la risoluzione dei problemi, l'analisi dei dati di utilizzo e le indagini forensi.

Analogamente, il [report Gestione password self-service](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) può essere utilizzato per determinare chi ha (o non ha) registrato per SSPR.

### <a name="self-service-app-management"></a>Gestione delle app self-service

Prima che gli utenti possano individuare autonomamente le applicazioni dal pannello di accesso, è necessario abilitare [l'accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) a tutte le applicazioni che si desidera consentire agli utenti di individuare autonomamente e richiedere l'accesso. L'accesso alle applicazioni self-service è un ottimo modo per consentire agli utenti di individuare autonomamente le applicazioni e, facoltativamente, consentire al gruppo di business di approvare l'accesso a tali applicazioni. È possibile consentire al gruppo di business di gestire le credenziali assegnate agli utenti per [le applicazioni Single Sign-On Password](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) direttamente dai relativi pannelli di accesso.

### <a name="self-service-group-management"></a>Gestione di gruppi self-service

L'assegnazione di utenti alle applicazioni è mappata al meglio quando si utilizzano i gruppi, perché consentono una grande flessibilità e capacità di gestione su larga scala:

* Basato su attributi tramite l'appartenenza a gruppi dinamiciAttribute-based using dynamic group membership
* Delega ai proprietari di app

Azure AD offre la possibilità di gestire l'accesso alle risorse con gruppi di sicurezza e gruppi di Office 365. Questi gruppi possono essere gestiti dal proprietario di un gruppo che può approvare o rifiutare le richieste di appartenenza e delegare il controllo dell'appartenenza al gruppo. Nota come gestione dei [gruppi self-service](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), questa funzionalità consente di risparmiare tempo consentendo ai proprietari dei gruppi a cui non è assegnato un ruolo amministrativo di creare e gestire i gruppi senza dover fare affidamento sugli amministratori per gestire le richieste.

## <a name="step-4---operationalize-your-insights"></a>Passaggio 4 - Rendere operative le informazioni

Il controllo e la registrazione degli eventi correlati alla sicurezza e degli avvisi correlati sono componenti essenziali di una strategia efficiente per garantire che gli utenti rimangano produttivi e che l'organizzazione sia sicura. I registri e i report di sicurezza possono aiutare a rispondere a domande quali:

* Stai usando quello per cui stai pagando?
* C'è qualcosa di sospetto o dannoso accadendo nel mio inquilino?
* Chi è stato colpito durante un incidente di sicurezza?

I report e i log di sicurezza offrono un record elettronico delle attività sospette e aiutano a rilevare criteri che possono indicare tentativi esterni di penetrazione nella rete e attacchi interni. È possibile utilizzare il controllo per monitorare l'attività degli utenti, documentare la conformità alle normative, eseguire analisi forensi e altro ancora. Gli avvisi forniscono notifiche di eventi di sicurezza.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Assegnare ruoli di amministratore con privilegi minimi per le operazioniAssign least privileged admin roles for operations

Quando pensi al tuo approccio alle operazioni, ci sono un paio di livelli di amministrazione da considerare. Il primo livello pone l'onere dell'amministrazione ai propri amministratori globali. L'utilizzo sempre del ruolo di amministratore globale potrebbe essere appropriato per le società più piccole. Ma per le organizzazioni più grandi con personale dell'help desk e amministratori responsabili di attività specifiche, l'assegnazione del ruolo di amministratore globale può rappresentare un rischio per la sicurezza in quanto fornisce a tali utenti la possibilità di gestire attività che si trovano al di sopra e al di là quello che dovrebbero essere in grado di fare.

In questo caso, è necessario considerare il livello successivo di amministrazione. Usando Azure AD, è possibile designare gli utenti finali come "amministratori limitati" che possono gestire le attività in ruoli con privilegi inferiori. Ad esempio, è possibile assegnare al personale dell'help desk il ruolo di lettore di [sicurezza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) per fornire loro la possibilità di gestire le funzionalità relative alla sicurezza con accesso in sola lettura. O forse ha senso assegnare il ruolo di amministratore di [autenticazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) ai singoli utenti per dare loro la possibilità di reimpostare le credenziali non password o leggere e configurare Integrità dei servizi di Azure.Or perhaps it makes sense to assign the authentication administrator role to individuals to give them the ability to reset non-password credentials or read and configure Azure Service Health.

Per altre informazioni, vedere Autorizzazioni del [ruolo di amministratore in Azure Active Directory.To](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)learn more, see Administrator role permissions in Azure Active Directory .

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorare i componenti ibridi (sincronizzazione di Azure AD Connect, AD FS) usando Azure AD Connect HealthMonitor hybrid components (Azure AD Connect sync, AD FS) using Azure AD Connect Health

Azure AD Connect e AD FS sono componenti critici che possono potenzialmente interrompere la gestione e l'autenticazione del ciclo di vita e, in ultima analisi, causare interruzioni. Pertanto, è consigliabile distribuire Azure AD Connect Health per il monitoraggio e la creazione di report di questi componenti.

Per altre informazioni, vedere [Monitorare ADFS usando Azure AD Connect Health.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Usare Monitoraggio di Azure per raccogliere i log dei dati per l'analisiUse Azure Monitor to collect data logs for analytics

[Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) è un portale di monitoraggio unificato per tutti i log di Azure AD, che fornisce informazioni approfondite, analisi avanzate e apprendimento automatico intelligente. Con Monitoraggio di Azure è possibile usare metriche e log all'interno del portale e tramite API per ottenere maggiore visibilità sullo stato e sulle prestazioni delle risorse. Consente un'unica esperienza di riquadro del vetro all'interno del portale, consentendo al contempo un'ampia gamma di integrazioni di prodotti tramite API e opzioni di esportazione dei dati che supportano i sistemi SIEM di terze parti tradizionali. Monitoraggio di Azure offre inoltre la possibilità di configurare regole di avviso per ricevere una notifica o di eseguire azioni automatizzate sui problemi che influiscono sulle risorse.

![Monitoraggio di Azure](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Crea dashboard personalizzati per la tua leadership e il tuo giorno per giorno

Le organizzazioni che non dispongono di una soluzione SIEM possono scaricare il pacchetto di contenuto di [Power BI](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) per Azure AD. Il pacchetto di contenuto di Power BI contiene report predefiniti che consentono di comprendere in che modo gli utenti adottano e usano le funzionalità di Azure AD, che consentono di ottenere informazioni dettagliate su tutte le attività all'interno della directory. Puoi anche creare il tuo [dashboard personalizzato](https://docs.microsoft.com/power-bi/service-dashboards) e condividerlo con il tuo team di leadership per creare rapporti sulle attività quotidiane. Le dashboard sono un ottimo modo per monitorare la tua attività e visualizzare tutte le metriche più importanti a colpo d'occhio. Le visualizzazioni in un dashboard potrebbero provenire da uno o più set di dati e report sottostanti. Un dashboard combina i dati locali e quelli del cloud, offrendo una visualizzazione consolidata indipendentemente dalla posizione dei dati.

![Dashboard personalizzato di Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Comprendere i driver di chiamata di supporto

Quando si implementa una soluzione di identità ibrida come descritto in questo articolo, è consigliabile notare una riduzione delle chiamate di supporto. Problemi comuni come password dimenticate e blocchi degli account vengono attenuati implementando la reimpostazione della password self-service di Azure, mentre l'abilitazione dell'accesso alle applicazioni self-service consente agli utenti di auto-individuare e richiedere l'accesso alle applicazioni senza affidarsi personale IT.

Se non si osserva una riduzione delle chiamate di supporto, è consigliabile analizzare i driver di chiamata di supporto nel tentativo di verificare se SSPR o l'accesso all'applicazione self-service è stato configurato correttamente o se sono presenti altri nuovi problemi che possono essere sistematicamente Affrontato.

*"Nel nostro percorso di trasformazione digitale, avevamo bisogno di un fornitore affidabile di gestione di identità e accessi per facilitare l'integrazione senza soluzione di continuità ma sicura tra noi, partner e fornitori di servizi cloud, per un ecosistema efficace; Azure AD era l'opzione migliore che ci offriva le funzionalità e la visibilità necessarie che ci hanno permesso di rilevare e rispondere ai rischi".* --- [Yazan Almasri, Direttore della Sicurezza Informativa Globale, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitora il tuo utilizzo delle app per ottenere informazioni dettagliate

Oltre a individuare Shadow IT, il monitoraggio dell'utilizzo delle app in tutta l'organizzazione tramite [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) può aiutare la tua organizzazione mentre ti muovi per sfruttare appieno la promessa delle applicazioni cloud. Può aiutarti a mantenere il controllo delle tue risorse grazie a una migliore visibilità dell'attività e ad aumentare la protezione dei dati critici nelle applicazioni cloud. Il monitoraggio dell'utilizzo delle app nell'organizzazione tramite MCAS consente di rispondere alle domande seguenti:Monitoring app app usage in your organization using MCAS can help you answer the following questions:

* Quali app non approvate utilizzano i dipendenti per archiviare i dati?
* Dove e quando vengono archiviati i dati sensibili nel cloud?
* Chi accede ai dati sensibili nel cloud?

*"Con Cloud App Security, siamo in grado di individuare rapidamente le anomalie e agire."* --- [Eric LePenske, Senior Manager, Information Security, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Riepilogo

Esistono molti aspetti per l'implementazione di una soluzione di identità ibrida, ma questo elenco di controllo in quattro passaggi consente di eseguire rapidamente un'infrastruttura di identità che consentirà agli utenti di essere più produttivi e sicuri.

* Connettiti facilmente alle app
* Stabilire automaticamente un'identità per ogni utente
* Potenzia i tuoi utenti in modo sicuro
* Funzionamento delle tue intuizioni

Ci auguriamo che questo documento sia una tabella di marcia utile per stabilire una solida base di identità per la tua organizzazione.

## <a name="identity-checklist"></a>Elenco di controllo dell'identità

È consigliabile stampare l'elenco di controllo seguente per riferimento quando si inizia il percorso verso una base di identità più solida nell'organizzazione.

### <a name="today"></a>Oggi

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Reimpostazione della password self-service pilota per un gruppo|
||Monitorare i componenti ibridi usando Azure AD Connect HealthMonitor hybrid components using Azure AD Connect Health|
||Assegnare ruoli di amministratore con privilegi minimi per l'operazioneAssign least privileged admin roles for operation|
||Scopri Shadow IT con Microsoft Cloud App Security|
||Usare Monitoraggio di Azure per raccogliere i log dei dati per l'analisiUse Azure Monitor to collect data logs for analysis|

### <a name="next-two-weeks"></a>Le prossime due settimane

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Rendere un'app disponibile per gli utenti|
||Eseguire il provisioning pilota di Azure AD per un'app SaaS scelta|
||Configurare un server di gestione temporanea per Azure AD Connect e mantenerlo aggiornato|
||Avviare la migrazione di app da ADFS ad Azure ADStart migrating apps from ADFS to Azure AD|
||Crea dashboard personalizzati per la tua leadership e il tuo giorno per giorno|

### <a name="next-month"></a>Il mese prossimo

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Monitora il tuo utilizzo delle app per ottenere informazioni dettagliate|
||Accesso remoto sicuro per il pilota alle app|
||Verificare che tutti gli utenti siano registrati per l'autenticazione a più fattori e SSPREnsure all users are registered for MFA and SSPR|
||Abilitare l'autenticazione cloudEnable cloud authentication|

### <a name="next-three-months"></a>Prossimi tre mesi

|Dopo aver completato l'operazione,|Elemento|
|:-|:-|
||Abilitare la gestione delle app self-serviceEnable self-service app management|
||Abilitare la gestione dei gruppi self-serviceEnable self-service group management|
||Monitora il tuo utilizzo delle app per ottenere informazioni dettagliate|
||Comprendere i driver di chiamata di supporto|

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aumentare la posizione sicura utilizzando le funzionalità di Azure Active Directory e questo elenco di controllo in cinque [passaggi: cinque passaggi per la protezione dell'infrastruttura di identità.](https://aka.ms/securitysteps)

Informazioni su come le funzionalità di identità in Azure AD consentono di accelerare la transizione alla gestione gestita dal cloud fornendo le soluzioni e le funzionalità che consentono alle organizzazioni di adottare e spostare una maggiore gestione delle identità dai sistemi locali tradizionali ad Azure AD: [in che modo Azure AD offre la gestione gestita dal cloud per i](https://aka.ms/cloudgoverned)carichi di lavoro locali.
