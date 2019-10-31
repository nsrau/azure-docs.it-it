---
title: Pianificare una distribuzione del pannello di accesso Azure Active Directory
description: Linee guida per la distribuzione del pannello di accesso Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99c52f65bdec2b164cca86a6346d8865d210cf38
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176054"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Pianificare una distribuzione del pannello di accesso Azure Active Directory

Il pannello di accesso Azure Active Directory (Azure AD) è un portale basato sul Web che consente di ridurre i costi di supporto, aumentare la produttività e la sicurezza e ridurre la frustrazione degli utenti. Il sistema include report dettagliati che consentono di tenere traccia del momento in cui si accede al sistema e di informare gli amministratori di abusi o abusi.

Tramite il pannello di accesso Azure AD è possibile:

* Individuare e accedere a tutte le risorse Azure AD connesse della propria azienda, ad esempio le applicazioni
* Richiedere l'accesso alle nuove app e ai gruppi
* Gestire l'accesso a queste risorse per altri utenti
* Gestire le impostazioni di reimpostazione delle password self-service e di Azure Multi-Factor Authentication
* Gestire i dispositivi

Consente inoltre agli amministratori di gestire:

* Condizioni del servizio
* Organizzazioni
* Verifiche di accesso


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Vantaggi dell'integrazione del pannello di accesso Azure AD

Il pannello di accesso Azure AD offre vantaggi per le aziende nei modi seguenti:

**Offre un'esperienza utente intuitiva**: il pannello di accesso offre un'unica piattaforma per tutte le applicazioni connesse ad Azure Single Sign-on (SSO). Si dispone di un portale unificato per trovare le impostazioni esistenti e le nuove funzionalità, ad esempio la gestione dei gruppi e la reimpostazione della password self-service, man mano che vengono aggiunti. L'esperienza intuitiva consente agli utenti di tornare a lavorare più rapidamente ed essere più produttivi, riducendo al tempo stesso la loro frustrazione.

**Aumenta la produttività**: per tutte le applicazioni utente nel pannello di accesso è abilitato SSO. L'abilitazione dell'accesso SSO tra applicazioni aziendali e Office 365 crea un'esperienza di accesso superiore riducendo o eliminando ulteriori richieste di accesso. Il pannello di accesso usa l'appartenenza dinamica e self-service e migliora la sicurezza complessiva del sistema di gestione delle identità. Questa operazione viene eseguita garantendo che gli utenti giusti gestiscano l'accesso alle applicazioni. Il pannello di accesso funge da pagina di destinazione coerente per individuare rapidamente le risorse e continuare le attività di lavoro.

**Gestione dei costi**: l'abilitazione del pannello di accesso con Azure ad può essere utile per la dimissione delle infrastrutture locali. Riduce i costi di supporto fornendo un portale coerente per trovare tutte le app, richiedere l'accesso alle risorse e gestire gli account.

**Aumenta la flessibilità e la sicurezza**: il pannello di accesso consente di accedere alla sicurezza e alla flessibilità offerte da una piattaforma cloud. Gli amministratori possono facilmente modificare le impostazioni per le applicazioni e le risorse e possono soddisfare i nuovi requisiti di sicurezza senza influire sugli utenti.

**Consente di monitorare e controllare l'utilizzo affidabili**: il controllo e il rilevamento dell'utilizzo per tutte le funzionalità utente consentono di capire quando gli utenti usano le risorse e garantiscono la valutazione della sicurezza.

### <a name="licensing-considerations"></a>Considerazioni sulle licenze

Il pannello di accesso è gratuito e non richiede licenze da usare a livello di base. Tuttavia, il numero di oggetti nella directory e le funzionalità aggiuntive che si desidera distribuire possono richiedere licenze aggiuntive. Alcuni scenari comuni di Azure AD con requisiti di licenza includono le funzionalità di sicurezza seguenti:

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Appartenenza basata sui gruppi](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Vedere la [Guida completa alle licenze per Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Prerequisiti per la distribuzione del pannello di accesso Azure AD

Prima di iniziare questo progetto, completare i prerequisiti seguenti:

* [Integra applicazione SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Gestire Azure AD infrastruttura di utenti e gruppi](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Pianificare la distribuzione del pannello di accesso Azure AD

La tabella seguente descrive i casi d'uso principali per una distribuzione del pannello di accesso:

| Area| Description |
| - | - |
| Accesso| Il portale del pannello di accesso è accessibile dai dispositivi personali e aziendali all'interno della rete aziendale. |
|Accesso | Il portale del pannello di accesso è accessibile dai dispositivi aziendali all'esterno della rete aziendale. |
| Controllo| I dati di utilizzo vengono scaricati nei sistemi aziendali almeno ogni 29 giorni. |
| Governance| Il ciclo di vita delle assegnazioni degli utenti a gruppi e applicazioni connesse Azure AD viene definito e monitorato. |
| Sicurezza| L'accesso alle risorse viene controllato tramite le assegnazioni di utenti e gruppi. Solo gli utenti autorizzati possono gestire l'accesso alle risorse. |
| Performance| Le sequenze temporali di propagazione dell'assegnazione di accesso vengono documentate e monitorate |
| Esperienza utente| Gli utenti sono a conoscenza delle funzionalità del pannello di accesso e del modo in cui usarle.|
| Esperienza utente| Gli utenti possono gestire l'accesso alle applicazioni e ai gruppi.|
| Esperienza utente| Gli utenti possono gestire i propri account. |
| Esperienza utente| Gli utenti sono consapevoli della compatibilità del browser. |
| Supporto| Gli utenti possono trovare il supporto per i problemi del pannello di accesso. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Procedure consigliate per la distribuzione del pannello di accesso Azure AD

La funzionalità del pannello di accesso può essere abilitata gradualmente. Si consiglia l'ordine di distribuzione seguente:

1. App personali
   * Utilità di avvio app
   * Gestione self-service delle app
   * Integrazione con Microsoft Office 365

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

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Pianificare le configurazioni per il pannello di accesso Azure AD

La tabella seguente elenca diverse configurazioni importanti del pannello di accesso e i valori tipici che è possibile usare:

| Configurazione| Valori tipici |
| - | - |
| Determinare i gruppi pilota| Identificare il gruppo di sicurezza Azure AD da usare e assicurarsi che tutti i membri pilota facciano parte del gruppo. |
| Determinare il gruppo o i gruppi da abilitare per la produzione.| Identificare i gruppi di sicurezza Azure AD o i gruppi di Active Directory sincronizzati con Azure AD, da usare. Assicurarsi che tutti i membri pilota facciano parte del gruppo. |
| Consenti agli utenti di usare SSO per determinati tipi di applicazioni| SSO federato, OAuth, SSO con password, proxy applicazione |
| Consenti agli utenti di usare la reimpostazione della password self-service | SÌ |
| Consenti agli utenti di usare Multi-Factor Authentication| SÌ |
| Consenti agli utenti di usare la gestione dei gruppi in modalità self-service per determinati tipi di gruppi| Gruppi di sicurezza, gruppi di Office 365 |
| Consenti agli utenti di usare la gestione self-service delle app| SÌ |
| Consenti agli utenti di usare le verifiche di accesso| SÌ |

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

Sebbene il pannello di accesso non crei in genere problemi dell'utente, è importante prepararsi. Creare guide e un elenco di tutte le risorse per il personale di supporto prima del lancio.

#### <a name="communications-templates"></a>Modelli di comunicazione

Microsoft offre [modelli personalizzabili per i messaggi di posta elettronica e altre comunicazioni](https://aka.ms/APTemplates) per il pannello di accesso. È possibile adattare questi asset per l'uso in altri canali di comunicazione in base alle impostazioni cultura aziendali.

## <a name="plan-your-sso-configuration"></a>Pianificare la configurazione di SSO

Quando un utente accede a un'applicazione, viene eseguito un processo di autenticazione e sono necessari per dimostrare l'identità. Senza SSO, viene archiviata una password nell'applicazione e l'utente deve essere a conoscenza della password. Con SSO, le credenziali degli utenti vengono passate all'applicazione, quindi non è necessario immettere nuovamente le password per ogni applicazione.

Per avviare applicazioni in app personali, è necessario abilitare SSO.

Azure AD supporta tre diversi modi per abilitare [Single Sign-on nelle applicazioni](what-is-single-sign-on.md):

* **Single Sign-On federato** 
    * Consente a un'applicazione di reindirizzare a Azure AD per l'autenticazione utente, anziché richiedere una password. 
    * È supportato per le applicazioni che usano protocolli, ad esempio SAML 2,0, WS-Federation o OpenID Connect, ed è la modalità più avanzata di Single Sign-On.

* **Single Sign-On basato su password** 
    * Abilita l'archiviazione e la riproduzione delle password delle applicazioni protette usando un'estensione del browser Web o un'app per dispositivi mobili. 
    * Sfrutta il processo di accesso esistente fornito dall'applicazione, ma consente a un amministratore di gestire le password. Non è necessario che l'utente conosca la password.

* **Single Sign-On esistente** 
    * Consente Azure AD di sfruttare i vantaggi di qualsiasi Single Sign-On esistente configurato per l'applicazione.
    * Consente di collegare queste applicazioni ai portali del pannello di accesso di Office 365 o Azure AD. 
    * Abilita la creazione di report aggiuntivi in Azure AD quando vengono avviate le applicazioni. 
    * Include l'uso di applicazione Azure proxy e la modalità di Single Sign-On collegata.

Informazioni su come configurare la modalità SSO di un'applicazione qui: [Single Sign-on per le applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Per un'esperienza ottimale con la pagina App personali, iniziare con l'integrazione delle applicazioni cloud disponibili per SSO federato. L'accesso Single Sign-on federato consente agli utenti di disporre di un'esperienza con un clic coerente nell'app che avvia le superfici e tende a essere più affidabile nel controllo della configurazione.

Usare SSO federato con Azure AD (OpenID Connect/SAML) quando un'applicazione la supporta, anziché SSO basato su password e ADFS.

Per altre informazioni su come distribuire e configurare le applicazioni SaaS, vedere il [piano di distribuzione SSO Saas](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Pianificare la distribuzione dell'estensione del browser My Apps

Quando gli utenti accedono alle applicazioni SSO basate su password, devono installare e usare l'estensione per l'accesso sicuro alle app personali. L'estensione esegue uno script che trasmette la password al modulo di accesso dell'applicazione. Agli utenti viene richiesto di installare l'estensione quando avviano per la prima volta l'applicazione SSO basata su password. Altre informazioni sull'estensione sono disponibili in questa documentazione sull' [installazione dell'estensione del browser del pannello di accesso](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Se è necessario integrare applicazioni SSO basate su password, è necessario definire un meccanismo per distribuire l'estensione su larga scala con i [browser supportati](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Le opzioni includono:

* [Criteri di gruppo per Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [System Center Configuration Manager (SCCM) per Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [Download e configurazione basati sull'utente per Chrome, Firefox, Microsoft Edge o IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Altre informazioni: [come configurare la Single Sign-on delle password](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Anche gli utenti che non usano applicazioni SSO basate su password traggono vantaggio dall'estensione. Questi vantaggi includono la possibilità di avviare qualsiasi app dalla relativa barra di ricerca, trovare l'accesso alle applicazioni utilizzate di recente e avere un collegamento alla pagina App personali.

Ecco cosa vedrà l'utente quando si avvia un'applicazione SSO basata su password per la prima volta:

![Screenshot della schermata di installazione dell'estensione del browser delle app ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Pianificare l'accesso mobile

Un browser protetto con i criteri di Intune (Microsoft Edge o Intune Managed Browser) è necessario per gli utenti mobili che avviano applicazioni SSO basate su password. Un browser protetto da criteri consente di trasferire la password salvata per l'applicazione. Microsoft Edge o Managed browser fornisce un set di funzionalità di protezione dei dati Web. È anche possibile usare Microsoft Edge per gli scenari aziendali nei dispositivi iOS e Android. Microsoft Edge supporta gli stessi scenari di gestione del Intune Managed Browser e migliora l'esperienza utente. Altre informazioni: [gestire l'accesso Web usando un browser protetto da criteri Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Pianificare la distribuzione delle app personali

La base del pannello di accesso è l'avvio dell'applicazione App personali, a cui gli utenti accedono in [https://myapps.microsoft.com](https://myapps.microsoft.com/). Le pagine My Apps forniscono agli utenti un'unica posizione per iniziare il lavoro e ottenere le applicazioni necessarie. Qui, gli utenti trovano un elenco di tutte le applicazioni a cui hanno accesso Single Sign-On. 

![Screenshot del pannello app](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Le stesse applicazioni verranno visualizzate nell'utilità di avvio delle app di Office 365 quando gli utenti usano il portale di Office 365.

Pianificare l'ordine in cui si aggiungono le applicazioni all'utilità di avvio delle app personali e decidere se eseguirne il rollup gradualmente o in una sola volta. A tale scopo, creare un inventario delle applicazioni che elenchi il tipo di autenticazione e qualsiasi integrazione SSO esistente per ogni applicazione.

#### <a name="add-applications-to-the-my-apps-panel"></a>Aggiungere applicazioni al pannello app personali

È possibile aggiungere qualsiasi applicazione Azure AD abilitata per l'accesso SSO all'utilità di avvio app personali. Altre applicazioni vengono aggiunte utilizzando l'opzione collegata SSO. È possibile configurare un riquadro dell'applicazione che si collega all'URL dell'applicazione Web esistente. SSO collegato consente di iniziare a indirizzare gli utenti al portale app personali senza eseguire la migrazione di tutte le applicazioni a Azure AD SSO. È possibile spostarsi gradualmente in Azure AD applicazioni configurate con SSO senza compromettere l'esperienza degli utenti.

#### <a name="use-my-apps-workspaces-preview"></a>Usare le aree di lavoro app personali (anteprima)

Per impostazione predefinita, tutte le applicazioni sono elencate insieme in un'unica pagina. Tuttavia, è possibile usare le aree di lavoro per raggruppare le applicazioni correlate e presentarle in una scheda separata, rendendole più facili da trovare. Ad esempio, è possibile utilizzare le aree di lavoro per creare raggruppamenti logici di applicazioni per ruoli, attività, progetti e così via specifici. Per informazioni, vedere [come usare le aree di lavoro app personali per personalizzare i pannelli di accesso utente (anteprima)](access-panel-workspaces.md). 

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

È possibile consentire agli utenti di creare e gestire i propri gruppi di sicurezza o gruppi di Office 365 in Azure AD. Il proprietario del gruppo può approvare o rifiutare le richieste di appartenenza e delegare il controllo dell'appartenenza al gruppo. Le funzionalità di gestione dei gruppi self-service non sono disponibili per gruppi di sicurezza abilitati alla posta elettronica o liste di distribuzione.

Per pianificare l'appartenenza al gruppo self-service, determinare se si consentirà a tutti gli utenti dell'organizzazione di creare e gestire gruppi o solo un subset di utenti. Se si consente un subset di utenti, è necessario configurare un gruppo a cui aggiungere tali utenti. Per informazioni dettagliate sull'abilitazione di questi scenari, vedere [configurare la gestione dei gruppi in modalità self-service in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) .

## <a name="plan-reporting-and-auditing"></a>Pianificare la creazione di report e il controllo

Azure AD fornisce [report che offrono informazioni tecniche e aziendali](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Collaborare con i proprietari delle applicazioni aziendali e tecniche per assumere la proprietà di questi report e per utilizzarli a intervalli regolari. Nella tabella seguente vengono forniti alcuni esempi di scenari di Reporting tipici.

|   | per gestire i rischi.| Aumentare la produttività| Governance e conformità |
|  - |- | - | - |
| Tipi di report|  Autorizzazioni e utilizzo dell'applicazione| Attività di provisioning dell'account| Esaminare gli utenti che accedono alle applicazioni |
| Azioni potenziali| Controllare l'accesso; revoca autorizzazioni| Correggere gli errori di provisioning| Revoca l'accesso |

Azure AD mantiene la maggior parte dei dati di controllo per 30 giorni. I dati sono disponibili tramite il portale di amministrazione di Azure o l'API da scaricare nei sistemi di analisi.

#### <a name="auditing"></a>Controllo

I log di controllo per l'accesso alle applicazioni sono disponibili per 30 giorni. Se il controllo della sicurezza all'interno dell'organizzazione richiede un periodo di conservazione più lungo, i log devono essere esportati in uno strumento di gestione e gestione delle informazioni di sicurezza (SIEM), ad esempio Splunk o ArcSight.

Per il controllo, la creazione di report e i backup di ripristino di emergenza, documentare la frequenza di download richiesta, il sistema di destinazione e chi è responsabile della gestione di ogni backup. Potrebbero non essere necessari backup distinti per il controllo e la creazione di report. Il backup del ripristino di emergenza deve essere un'entità separata.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Distribuire le applicazioni nel pannello app personali degli utenti

Dopo la configurazione di un'applicazione per SSO, ai gruppi viene assegnato l'accesso. Gli utenti dei gruppi assegnati avranno accesso e vedranno l'applicazione nelle app personali e nell'utilità di avvio delle app di Office 365.

Vedere [assegnare utenti e gruppi a un'applicazione in Active Directory](methods-for-assigning-users-and-groups.md).

Se durante il test o la distribuzione si vuole aggiungere i gruppi ma non consentire le applicazioni da visualizzare nelle app personali, vedere [nascondere un'applicazione dall'esperienza utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Distribuisci applicazioni Microsoft Office 365 in app personali

Per le applicazioni Office 365, gli utenti ricevono una copia di Office in base alle licenze assegnate. Un prerequisito per l'accesso alle applicazioni di Office è costituito dagli utenti a cui vengono assegnate le licenze corrette legate alle applicazioni di Office. Quando si assegna una licenza a un utente, vengono visualizzate automaticamente le applicazioni associate alla licenza nella pagina App personali e nell'utilità di avvio delle app di Office 365.

Se si vuole nascondere un set di applicazioni di Office dagli utenti, è disponibile un'opzione che consente di nascondere le app dal portale delle app personali, pur continuando ad accedere dal portale di Office 365. Trovare queste impostazioni nella parte relativa alle impostazioni utente dell'applicazione. Altre informazioni: [nascondere un'applicazione dall'esperienza dell'utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Screenshot della configurazione di come nascondere le applicazioni](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Distribuire le funzionalità self-service delle applicazioni

L'accesso alle applicazioni self-service consente agli utenti di individuare autonomamente e richiedere l'accesso alle applicazioni. Gli utenti hanno la libertà di accedere alle app necessarie senza passare a un gruppo IT ogni volta per richiedere l'accesso. Quando un utente richiede l'accesso ed è approvato, in modo automatico o manuale da un proprietario dell'app, viene aggiunto a un gruppo nel back-end. La creazione di report è abilitata su chi ha richiesto, approvato o rimosso l'accesso e consente di controllare la gestione dei ruoli assegnati.

È possibile delegare l'approvazione delle richieste di accesso alle applicazioni ai responsabili approvazione aziendali. Il responsabile approvazione aziendale può impostare le password di accesso all'app dalla pagina App personali del responsabile approvazione business.

Altre informazioni: [come usare l'accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![Screenshot della configurazione della gestione di applicazioni self-service](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Convalidare la distribuzione

Assicurarsi che la distribuzione del pannello di accesso sia testata accuratamente e che sia presente un piano di rollback.

I test seguenti devono essere eseguiti con i dispositivi di proprietà dell'azienda e i dispositivi personali. Questi test case dovrebbero anche riflettere i casi d'uso aziendali. Di seguito sono riportati alcuni casi basati sui requisiti aziendali di esempio di questo documento e sugli scenari tecnici tipici. Aggiungere altri utenti specifici per le proprie esigenze.

#### <a name="application-sso-access-test-case-examples"></a>Esempi di accesso SSO applicazione test case:


| Business case| Risultato previsto |
| - | -|
| L'utente accede al portale app personali| L'utente può accedere e visualizzare le applicazioni |
| L'utente avvia un'applicazione SSO federata| L'utente viene connesso automaticamente all'applicazione |
| L'utente avvia un'applicazione con password SSO per la prima volta| L'utente deve installare l'estensione My Apps |
| L'utente avvia un'applicazione con password SSO una volta successiva| L'utente viene connesso automaticamente all'applicazione |
| L'utente avvia un'app dal portale di Office 365| L'utente viene connesso automaticamente all'applicazione |
| L'utente avvia un'app dal Managed Browser| L'utente viene connesso automaticamente all'applicazione |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Esempi di funzionalità self-service per le applicazioni test case


| Business case| Risultato previsto |
| - | - |
| L'utente può gestire l'appartenenza all'applicazione| L'utente può aggiungere/rimuovere membri che hanno accesso all'app |
| L'utente può modificare l'applicazione| L'utente può modificare la descrizione e le credenziali dell'applicazione per le applicazioni SSO con password |

### <a name="rollback-steps"></a>Procedura di ripristino dello stato precedente

È importante pianificare le operazioni da eseguire se la distribuzione non viene completata come previsto. Se la configurazione SSO ha esito negativo durante la distribuzione, è necessario comprendere come [risolvere i problemi relativi a SSO](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) e ridurre l'effetto sugli utenti. In casi estremi, potrebbe essere necessario eseguire il [rollback di SSO](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Gestire l'implementazione

È consigliabile utilizzare il ruolo con privilegi minimi per eseguire un'attività obbligatoria all'interno Azure Active Directory. [Esaminare i diversi ruoli disponibili](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e scegliere quello più adatto per soddisfare le proprie esigenze per ogni utente di questa applicazione. È possibile che alcuni ruoli debbano essere applicati temporaneamente e rimossi al termine della distribuzione.

| Utenti tipo| Ruoli| Ruolo Azure AD  |
| - | -| -|
| Amministratore helpdesk| Supporto di livello 1| Nessuno |
| Amministratore identità| Configurare ed eseguire il debug quando i problemi hanno effetto Azure AD| Amministratore globale |
| Amministratore applicazione| Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni| Nessuno |
| Amministratori dell'infrastruttura| Proprietario del rollover del certificato| Amministratore globale |
| Proprietario/stakeholder aziendale| Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni| Nessuno |

È possibile usare [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) per gestire i ruoli per fornire controllo, controllo e verifica di accesso aggiuntivi per gli utenti con autorizzazioni di directory.

### <a name="troubleshoot-access-panel-issues"></a>Risolvere i problemi del pannello di accesso

Creare guide per la risoluzione dei problemi per l'organizzazione di supporto con scenari comuni, che fanno riferimento alla documentazione Microsoft nelle loro soluzioni. Potrebbe essere necessario creare guide che interrompano il supporto nei livelli utilizzati dall'organizzazione.

Per informazioni di riferimento, vedere la guida alla risoluzione dei problemi:

[Applicazioni non visualizzate](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Visualizzazione di applicazioni impreviste](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[L'utente non può accedere al pannello di accesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemi con l'accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemi relativi all'estensione del browser](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Passaggi successivi

[Pianificare una distribuzione di Azure Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)
