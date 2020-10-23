---
title: Pianificare un Azure Active Directory la distribuzione di app personali
description: Linee guida per la distribuzione di Azure Active Directory app personali
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.openlocfilehash: cc36fccf84807621b8b3a186979ccfd000fe48f3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372481"
---
# <a name="plan-an-azure-active-directory-my-apps-deployment"></a>Pianificare un Azure Active Directory la distribuzione di app personali

Azure Active Directory (Azure AD) app personali è un portale basato sul Web che consente di ridurre i costi di supporto, aumentare la produttività e la sicurezza e ridurre la frustrazione degli utenti. Il sistema include report dettagliati che consentono di tenere traccia del momento in cui si accede al sistema e di informare gli amministratori di abusi o abusi. Per informazioni sull'uso delle app personali dal punto di vista dell'utente finale, vedere [la guida del portale delle app personali](../user-help/my-apps-portal-end-user-access.md).

Utilizzando Azure AD app personali, è possibile:

* Individuare e accedere a tutte le risorse Azure AD connesse della propria azienda, ad esempio le applicazioni
* Richiedere l'accesso alle nuove app e ai gruppi
* Gestire l'accesso a queste risorse per altri utenti
* Gestire le impostazioni di reimpostazione delle password self-service e di Azure Multi-Factor Authentication
* Gestire i dispositivi

Consente inoltre agli amministratori di gestire:

* Condizioni del servizio
* Organizzazioni
* Verifiche di accesso


## <a name="benefits-of-azure-ad-my-apps-integration"></a>Vantaggi dell'integrazione di Azure AD app personali

Azure AD le mie app sono utili per le aziende nei modi seguenti:

**Offre un'esperienza utente intuitiva**: le mie app offrono un'unica piattaforma per tutte le applicazioni connesse ad Azure Single Sign-on (SSO). Si dispone di un portale unificato per trovare le impostazioni esistenti e le nuove funzionalità, ad esempio la gestione dei gruppi e la reimpostazione della password self-service, man mano che vengono aggiunti. L'esperienza intuitiva consente agli utenti di tornare a lavorare più rapidamente ed essere più produttivi, riducendo al tempo stesso la loro frustrazione.

**Aumenta la produttività**: l'accesso SSO è abilitato per tutte le applicazioni utente nelle app personali. L'abilitazione dell'accesso SSO tra applicazioni aziendali e Microsoft 365 crea un'esperienza di accesso superiore riducendo o eliminando ulteriori richieste di accesso. App personali usa l'appartenenza dinamica e self-service e migliora la sicurezza complessiva del sistema di identità. App personali garantisce che le persone giuste gestiscano l'accesso alle applicazioni. App personali funge da pagina di destinazione coerente per individuare rapidamente le risorse e continuare le attività di lavoro.

**Gestione dei costi**: l'abilitazione di app personali con Azure ad può essere utile per la dimissione delle infrastrutture locali. Riduce i costi di supporto fornendo un portale coerente per trovare tutte le app, richiedere l'accesso alle risorse e gestire gli account.

**Aumenta la flessibilità e la sicurezza**: le mie app ti permettono di accedere alla sicurezza e alla flessibilità offerte da una piattaforma cloud. Gli amministratori possono facilmente modificare le impostazioni per le applicazioni e le risorse e possono soddisfare i nuovi requisiti di sicurezza senza influire sugli utenti.

**Consente di monitorare e controllare l'utilizzo affidabili**: il controllo e il rilevamento dell'utilizzo per tutte le funzionalità utente consentono di capire quando gli utenti usano le risorse e garantiscono la valutazione della sicurezza.

### <a name="licensing-considerations"></a>Considerazioni sulla gestione delle licenze

Le mie app sono gratuite e non richiedono licenze da usare a livello di base. Tuttavia, il numero di oggetti nella directory e le funzionalità aggiuntive che si desidera distribuire possono richiedere licenze aggiuntive. Alcuni scenari comuni di Azure AD con requisiti di licenza includono le funzionalità di sicurezza seguenti:

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)
* [Appartenenza basata sui gruppi](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
* [Reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Vedere la [Guida completa alle licenze per Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-azure-ad-my-apps"></a>Prerequisiti per la distribuzione di Azure AD app personali

Prima di iniziare questo progetto, completare i prerequisiti seguenti:

* [Integra applicazione SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)
* [Gestire Azure AD infrastruttura di utenti e gruppi](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-azure-ad-my-apps-deployment"></a>Pianificare Azure AD la distribuzione di app personali

La tabella seguente descrive i casi d'uso principali per la distribuzione di app personali:

| Area| Descrizione |
| - | - |
| Accesso| Il portale delle app è accessibile da dispositivi aziendali e personali all'interno della rete aziendale. |
|Accesso | Il portale delle app è accessibile dai dispositivi aziendali all'esterno della rete aziendale. |
| Controllo| I dati di utilizzo vengono scaricati nei sistemi aziendali almeno ogni 29 giorni. |
| Governance| Il ciclo di vita delle assegnazioni degli utenti a gruppi e applicazioni connesse Azure AD viene definito e monitorato. |
| Security| L'accesso alle risorse viene controllato tramite le assegnazioni di utenti e gruppi. Solo gli utenti autorizzati possono gestire l'accesso alle risorse. |
| Prestazioni| Le sequenze temporali di propagazione dell'assegnazione di accesso vengono documentate e monitorate |
| Esperienza dell'utente| Gli utenti sono a conoscenza delle funzionalità delle app e di come usarle.|
| Esperienza dell'utente| Gli utenti possono gestire l'accesso alle applicazioni e ai gruppi.|
| Esperienza dell'utente| Gli utenti possono gestire i propri account. |
| Esperienza dell'utente| Gli utenti sono consapevoli della compatibilità del browser. |
| Supporto| Gli utenti possono trovare il supporto per i problemi relativi alle app. |


> [!TIP]
> Le mie app possono essere usate con URL interni della società in remoto usando il proxy di applicazione. Per altre informazioni, vedere [esercitazione: aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md).

### <a name="best-practices-for-deploying-azure-ad-my-apps"></a>Procedure consigliate per la distribuzione di Azure AD app personali

La funzionalità delle app personali può essere abilitata gradualmente. Si consiglia l'ordine di distribuzione seguente:

1. App personali
   * Utilità di avvio app
   * Gestione self-service delle app
   * Integrazione di Microsoft 365

1. Individuazione self-service delle app
   * Reimpostazione della password self-service
   * Impostazioni Multi-Factor Authentication
   * Gestione dei dispositivi
   * Condizioni per l'utilizzo
   * Gestisci organizzazioni

1. Gruppi personali
   * Gestione di gruppi self-service
1. Verifiche di accesso
   * Gestione della verifica di accesso

A partire da app personali, gli utenti vengono introdotti nel portale come posizione comune per l'accesso alle risorse. L'aggiunta di individuazione di applicazioni self-service si basa sull'esperienza app personali. I gruppi e le verifiche di accesso si basano sulle funzionalità self-service.

### <a name="plan-configurations-for-azure-my-apps"></a>Pianificare le configurazioni per le app di Azure

La tabella seguente elenca diverse configurazioni importanti delle app personali e i valori tipici che è possibile usare:

| Configurazione| Valori tipici |
| - | - |
| Determinare i gruppi pilota| Identificare il gruppo di sicurezza Azure AD da usare e assicurarsi che tutti i membri pilota facciano parte del gruppo. |
| Determinare il gruppo o i gruppi da abilitare per la produzione.| Identificare i gruppi di sicurezza Azure AD o i gruppi di Active Directory sincronizzati con Azure AD, da usare. Assicurarsi che tutti i membri pilota facciano parte del gruppo. |
| Consenti agli utenti di usare SSO per determinati tipi di applicazioni| SSO federato, OAuth, SSO con password, proxy applicazione |
| Consenti agli utenti di usare la reimpostazione della password self-service | Sì |
| Consenti agli utenti di usare Multi-Factor Authentication| Sì |
| Consenti agli utenti di usare la gestione dei gruppi in modalità self-service per determinati tipi di gruppi| Gruppi di sicurezza, gruppi di Microsoft 365 |
| Consenti agli utenti di usare la gestione self-service delle app| Sì |
| Consenti agli utenti di usare le verifiche di accesso| Sì |

### <a name="plan-consent-strategy"></a>Strategia di consenso del piano

Gli utenti o gli amministratori devono acconsentire alle condizioni per l'utilizzo e ai criteri di privacy di un'applicazione. Se possibile, in base alle regole di business, usare il consenso dell'amministratore, che offre agli utenti un'esperienza migliore.

Per utilizzare il consenso dell'amministratore, è necessario essere un amministratore globale dell'organizzazione e che le applicazioni siano:

* Registrato nell'organizzazione
* Registrato in un'altra organizzazione Azure AD e consentiti in precedenza da almeno un utente

Per altre informazioni, vedere [configurare il modo in cui gli utenti finali acconsentono a un'applicazione in Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, in genere lo fanno a causa di una mancata corrispondenza delle aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere gli stakeholder appropriati](../fundamentals/active-directory-deployment-plans.md) e che i ruoli stakeholder nel progetto siano ben noti.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. Informare in modo proattivo gli utenti su come e quando la loro esperienza cambierà e su come ottenere supporto se necessario.

Sebbene le app in genere non creino problemi degli utenti, è importante prepararsi. Creare guide e un elenco di tutte le risorse per il personale di supporto prima del lancio.

#### <a name="communications-templates"></a>Modelli di comunicazione

Microsoft offre [modelli personalizzabili per i messaggi di posta elettronica e altre comunicazioni](https://aka.ms/APTemplates) per le app personali. È possibile adattare questi asset per l'uso in altri canali di comunicazione in base alle impostazioni cultura aziendali.

## <a name="plan-your-sso-configuration"></a>Pianificare la configurazione di SSO

Quando un utente accede a un'applicazione, viene eseguito un processo di autenticazione e sono necessari per dimostrare l'identità. Senza SSO, viene archiviata una password nell'applicazione e l'utente deve essere a conoscenza della password. Con SSO, le credenziali degli utenti vengono passate all'applicazione, quindi non è necessario immettere nuovamente le password per ogni applicazione.

Per avviare applicazioni in app personali, è necessario abilitare SSO. Azure AD supporta più opzioni SSO. Per altre informazioni, vedere [Opzioni di Single Sign-on in Azure ad](sso-options.md).

> [!NOTE]
> Per altre informazioni sull'uso di Azure AD come provider di identità per un'app, vedere la [serie di guide introduttive sulla gestione delle applicazioni](view-applications-portal.md).

Per un'esperienza ottimale con la pagina App personali, iniziare con l'integrazione delle applicazioni cloud disponibili per SSO federato. L'accesso Single Sign-on federato consente agli utenti di disporre di un'esperienza con un clic coerente nell'app che avvia le superfici e tende a essere più affidabile nel controllo della configurazione.

Usare SSO federato con Azure AD (OpenID Connect/SAML) quando un'applicazione la supporta, anziché SSO basato su password e ADFS.

Per altre informazioni su come distribuire e configurare le applicazioni SaaS, vedere il [piano di distribuzione SSO Saas](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Pianificare la distribuzione dell'estensione del browser My Apps

Quando gli utenti accedono alle applicazioni SSO basate su password, devono installare e usare l'estensione per l'accesso sicuro alle app personali. L'estensione esegue uno script che trasmette la password al modulo di accesso dell'applicazione. Agli utenti viene richiesto di installare l'estensione quando avviano per la prima volta l'applicazione SSO basata su password. Altre informazioni sull'estensione sono disponibili in questa documentazione sull' [installazione dell'estensione del browser app personali](access-panel-extension-problem-installing.md).

Se è necessario integrare applicazioni SSO basate su password, è necessario definire un meccanismo per distribuire l'estensione su larga scala con i [browser supportati](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Le opzioni includono:

* [Criteri di gruppo per Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
* [Configuration Manager per Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
* [Download e configurazione basati sull'utente per Chrome, Firefox, Microsoft Edge o IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Anche gli utenti che non usano applicazioni SSO basate su password traggono vantaggio dall'estensione. Questi vantaggi includono la possibilità di avviare qualsiasi app dalla relativa barra di ricerca, trovare l'accesso alle applicazioni utilizzate di recente e avere un collegamento alla pagina App personali.

#### <a name="plan-for-mobile-access"></a>Pianificare l'accesso mobile

Un browser protetto con i criteri di Intune (Microsoft Edge o Intune Managed Browser) è necessario per gli utenti mobili che avviano applicazioni SSO basate su password. Un browser protetto da criteri consente di trasferire la password salvata per l'applicazione. Microsoft Edge o Managed browser fornisce un set di funzionalità di protezione dei dati Web. È anche possibile usare Microsoft Edge per gli scenari aziendali nei dispositivi iOS e Android. Microsoft Edge supporta gli stessi scenari di gestione del Intune Managed Browser e migliora l'esperienza utente. Altre informazioni: [gestire l'accesso Web usando un browser protetto da criteri Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Pianificare la distribuzione delle app personali

La base delle mie app è il portale dell'utilità di avvio delle applicazioni, a cui gli utenti accedono all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/) . La pagina App personali offre agli utenti un'unica posizione per iniziare il lavoro e ottenere le applicazioni necessarie. Qui, gli utenti trovano un elenco di tutte le applicazioni a cui hanno accesso Single Sign-On. 

> [!NOTE]
> Le stesse applicazioni verranno visualizzate nell'utilità di avvio dell'app Microsoft 365.

Pianificare l'ordine in cui si aggiungono le applicazioni all'utilità di avvio delle app personali e decidere se eseguirne il rollup gradualmente o in una sola volta. A tale scopo, creare un inventario delle applicazioni che elenchi il tipo di autenticazione e qualsiasi integrazione SSO esistente per ogni applicazione.

#### <a name="add-applications-to-the-my-apps-panel"></a>Aggiungere applicazioni al pannello app personali

È possibile aggiungere qualsiasi applicazione Azure AD abilitata per l'accesso SSO all'utilità di avvio app personali. Altre applicazioni vengono aggiunte utilizzando l'opzione collegata SSO. È possibile configurare un riquadro dell'applicazione che si collega all'URL dell'applicazione Web esistente. SSO collegato consente di iniziare a indirizzare gli utenti al portale app personali senza eseguire la migrazione di tutte le applicazioni a Azure AD SSO. È possibile spostarsi gradualmente in Azure AD applicazioni configurate con SSO senza compromettere l'esperienza degli utenti.

#### <a name="use-my-apps-collections"></a>Usare le raccolte di app personali

Per impostazione predefinita, tutte le applicazioni sono elencate insieme in un'unica pagina. È tuttavia possibile usare le raccolte per raggruppare le applicazioni correlate e presentarle in una scheda separata, rendendole più facili da trovare. È ad esempio possibile utilizzare le raccolte per creare raggruppamenti logici di applicazioni per ruoli di processo, attività, progetti e così via specifici. Per informazioni, vedere [come usare le raccolte di app personali](access-panel-collections.md). 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Pianificare se usare app personali o un portale esistente

Gli utenti potrebbero avere già un'applicazione o un portale diverso da app personali. In tal caso, decidere se supportare entrambi i portali o utilizzarne solo uno.

Se un portale esistente è già usato come punto di partenza per gli utenti, è possibile integrare la funzionalità app personali usando gli URL di accesso utente. Gli URL di accesso utente funzionano come collegamenti diretti alle applicazioni disponibili nel portale app personali. Questi URL possono essere incorporati in qualsiasi sito Web esistente. Quando un utente seleziona il collegamento, apre l'applicazione dal portale app personali.

È possibile trovare la proprietà URL accesso utente nell'area **Proprietà** dell'applicazione nella portale di Azure.

![Screenshot del pannello app](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Pianificare l'individuazione e l'accesso alle applicazioni self-service

Quando un set di applicazioni di base viene distribuito nella pagina App personali di un utente, è necessario abilitare le funzionalità di gestione self-service delle app. L'individuazione self-service delle app consente agli utenti di:

* Trova le nuove app da aggiungere alle app personali. 
* Aggiungere app facoltative che potrebbero non essere a conoscenza di cui hanno bisogno durante l'installazione.

I flussi di lavoro di approvazione sono disponibili per l'approvazione esplicita per l'accesso alle applicazioni. Gli utenti che sono responsabili approvazione riceveranno le notifiche nel portale app personali quando ci sono richieste in sospeso per l'accesso all'applicazione.

## <a name="plan-self-service-group-membership"></a>Pianificare l'appartenenza al gruppo self-service 

È possibile consentire agli utenti di creare e gestire i propri gruppi di sicurezza o gruppi di Microsoft 365 in Azure AD. Il proprietario del gruppo può approvare o rifiutare le richieste di appartenenza e delegare il controllo dell'appartenenza al gruppo. Le funzionalità di gestione dei gruppi self-service non sono disponibili per gruppi di sicurezza abilitati alla posta elettronica o liste di distribuzione.

Per pianificare l'appartenenza al gruppo self-service, determinare se si consentirà a tutti gli utenti dell'organizzazione di creare e gestire gruppi o solo un subset di utenti. Se si consente un subset di utenti, è necessario configurare un gruppo a cui aggiungere tali utenti. Per informazioni dettagliate sull'abilitazione di questi scenari, vedere [configurare la gestione dei gruppi in modalità self-service in Azure Active Directory](../enterprise-users/groups-self-service-management.md) .

## <a name="plan-reporting-and-auditing"></a>Pianificare la creazione di report e il controllo

Azure AD fornisce [report che offrono informazioni tecniche e aziendali](../reports-monitoring/overview-reports.md). Collaborare con i proprietari delle applicazioni aziendali e tecniche per assumere la proprietà di questi report e per utilizzarli a intervalli regolari. Nella tabella seguente vengono forniti alcuni esempi di scenari di Reporting tipici.

| Esempio | per gestire i rischi.| Aumentare la produttività| Governance e conformità |
|  - |- | - | - |
| Tipi di report|  Autorizzazioni e utilizzo dell'applicazione| Attività di provisioning dell'account| Esaminare gli utenti che accedono alle applicazioni |
| Azioni potenziali| Controllare l'accesso; revoca autorizzazioni| Correggere gli errori di provisioning| revoca dell'accesso |

Azure AD mantiene la maggior parte dei dati di controllo per 30 giorni. I dati sono disponibili tramite il portale di amministrazione di Azure o l'API da scaricare nei sistemi di analisi.

#### <a name="auditing"></a>Controllo

I log di controllo per l'accesso alle applicazioni sono disponibili per 30 giorni. Se il controllo della sicurezza all'interno dell'organizzazione richiede un periodo di conservazione più lungo, i log devono essere esportati in uno strumento di gestione e gestione delle informazioni di sicurezza (SIEM), ad esempio Splunk o ArcSight.

Per il controllo, la creazione di report e i backup di ripristino di emergenza, documentare la frequenza di download richiesta, il sistema di destinazione e chi è responsabile della gestione di ogni backup. Potrebbero non essere necessari backup distinti per il controllo e la creazione di report. Il backup del ripristino di emergenza deve essere un'entità separata.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Distribuire le applicazioni nel pannello app personali degli utenti

Dopo la configurazione di un'applicazione per SSO, ai gruppi viene assegnato l'accesso. Gli utenti dei gruppi assegnati avranno accesso e vedranno l'applicazione nelle app personali e nell'utilità di avvio dell'app Microsoft 365.

Vedere [assegnare utenti e gruppi a un'applicazione in Active Directory](methods-for-assigning-users-and-groups.md).

Se durante il test o la distribuzione si vuole aggiungere i gruppi ma non consentire le applicazioni da visualizzare nelle app personali, vedere [nascondere un'applicazione dall'esperienza utente in Azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-microsoft-365-applications-to-my-apps"></a>Distribuisci applicazioni Microsoft 365 alle app personali

Per Microsoft 365 applicazioni, gli utenti ricevono una copia di Office in base alle licenze assegnate. Un prerequisito per l'accesso alle applicazioni di Office è costituito dagli utenti a cui vengono assegnate le licenze corrette legate alle applicazioni di Office. Quando si assegna una licenza a un utente, le applicazioni associate alla licenza verranno visualizzate automaticamente nella pagina App personali e nell'utilità di avvio dell'app Microsoft 365.

Se si vuole nascondere un set di applicazioni di Office dagli utenti, è disponibile un'opzione che consente di nascondere le app dal portale delle app personali, pur continuando ad accedere dal portale di Microsoft 365. Altre informazioni: [nascondere un'applicazione dall'esperienza dell'utente in Azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-application-self-service-capabilities"></a>Distribuire le funzionalità self-service delle applicazioni

L'accesso alle applicazioni self-service consente agli utenti di individuare autonomamente e richiedere l'accesso alle applicazioni. Gli utenti hanno la libertà di accedere alle app necessarie senza passare a un gruppo IT ogni volta per richiedere l'accesso. Quando un utente richiede l'accesso ed è approvato, in modo automatico o manuale da un proprietario dell'app, viene aggiunto a un gruppo nel back-end. La creazione di report è abilitata su chi ha richiesto, approvato o rimosso l'accesso e consente di controllare la gestione dei ruoli assegnati.

È possibile delegare l'approvazione delle richieste di accesso alle applicazioni ai responsabili approvazione aziendali. Il responsabile approvazione aziendale può impostare le password di accesso all'app dalla pagina App personali del responsabile approvazione business.

Altre informazioni: [come usare l'accesso alle applicazioni self-service](access-panel-manage-self-service-access.md).

## <a name="validate-your-deployment"></a>Convalidare la distribuzione

Assicurarsi che la distribuzione di app personali sia testata accuratamente e che sia presente un piano di rollback.

I test seguenti devono essere eseguiti con i dispositivi di proprietà dell'azienda e i dispositivi personali. Questi test case dovrebbero anche riflettere i casi d'uso aziendali. Di seguito sono riportati alcuni casi basati sui requisiti aziendali di esempio di questo documento e sugli scenari tecnici tipici. Aggiungere altri utenti specifici per le proprie esigenze.

#### <a name="application-sso-access-test-case-examples"></a>Esempi di accesso SSO applicazione test case:


| Caso aziendale| Risultato previsto |
| - | -|
| L'utente accede al portale app personali| L'utente può accedere e visualizzare le applicazioni |
| L'utente avvia un'applicazione SSO federata| L'utente viene connesso automaticamente all'applicazione |
| L'utente avvia un'applicazione con password SSO per la prima volta| L'utente deve installare l'estensione My Apps |
| L'utente avvia un'applicazione con password SSO una volta successiva| L'utente viene connesso automaticamente all'applicazione |
| L'utente avvia un'app dal portale di Microsoft 365| L'utente viene connesso automaticamente all'applicazione |
| L'utente avvia un'app dal Managed Browser| L'utente viene connesso automaticamente all'applicazione |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Esempi di funzionalità self-service per le applicazioni test case

| Caso aziendale| Risultato previsto |
| - | - |
| L'utente può gestire l'appartenenza all'applicazione| L'utente può aggiungere/rimuovere membri che hanno accesso all'app |
| L'utente può modificare l'applicazione| L'utente può modificare la descrizione e le credenziali dell'applicazione per le applicazioni SSO con password |

### <a name="rollback-steps"></a>Procedura di ripristino dello stato precedente

È importante pianificare le operazioni da eseguire se la distribuzione non viene completata come previsto. Se la configurazione SSO ha esito negativo durante la distribuzione, è necessario comprendere come [risolvere i problemi relativi a SSO](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) e ridurre l'effetto sugli utenti. In casi estremi, potrebbe essere necessario eseguire il [rollback di SSO](../manage-apps/plan-sso-deployment.md#rollback-process).


## <a name="manage-your-implementation"></a>Gestire l'implementazione

Utilizzare il ruolo con privilegi minimi per eseguire un'attività obbligatoria all'interno Azure Active Directory. [Esaminare i diversi ruoli disponibili](../roles/permissions-reference.md) e scegliere quello più adatto per soddisfare le proprie esigenze per ogni utente di questa applicazione. È possibile che alcuni ruoli debbano essere applicati temporaneamente e rimossi al termine della distribuzione.

| Utenti tipo| Ruoli| Ruolo di Azure AD  |
| - | -| -|
| Amministratore helpdesk| Supporto di livello 1| nessuno |
| Amministratore identità| Configurare ed eseguire il debug quando i problemi hanno effetto Azure AD| Amministratore globale |
| Amministratore applicazione| Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni| nessuno |
| Amministratori dell'infrastruttura| Proprietario del rollover del certificato| Amministratore globale |
| Proprietario/stakeholder aziendale| Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni| nessuno |

È possibile usare [Privileged Identity Management](../privileged-identity-management/pim-configure.md) per gestire i ruoli per fornire controllo, controllo e verifica di accesso aggiuntivi per gli utenti con autorizzazioni di directory.

## <a name="next-steps"></a>Passaggi successivi
[Pianificare una distribuzione di Azure Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)
