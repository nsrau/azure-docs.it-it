---
title: Pianificare una distribuzione del Pannello di accesso di Azure Active DirectoryPlan an Azure Active Directory Access Panel deployment
description: Indicazioni sulla distribuzione del pannello di accesso di Azure Active DirectoryGuidance on deploying the Azure Active Directory Access Panel
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
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897067"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Pianificare una distribuzione del Pannello di accesso di Azure Active DirectoryPlan an Azure Active Directory Access Panel deployment

Il pannello di accesso di Azure Active Directory (Azure AD) è un portale basato sul Web che consente di ridurre i costi di supporto, aumentare la produttività e la sicurezza e ridurre la frustrazione degli utenti. Il sistema include report dettagliati che tengono traccia dell'accesso al sistema e notificano agli amministratori l'uso improprio o l'abuso.

Usando il pannello di accesso di Azure AD, è possibile:By using the Azure AD Access Panel, you can:

* Individuare e accedere a tutte le risorse connesse ad Azure AD dell'azienda, ad esempio le applicazioni
* Richiedere l'accesso a nuove app e nuovi gruppi
* Gestire l'accesso a queste risorse per altri utenti
* Gestire la reimpostazione delle password self-service e le impostazioni di Azure Multi-Factor AuthenticationManage self-service password resets and Azure Multi-Factor Authentication settings
* Gestire i propri dispositivi

Consente inoltre agli amministratori di gestire:

* Condizioni per l'utilizzo
* Organizzazioni
* Verifiche di accesso


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Vantaggi dell'integrazione del pannello di accesso di Azure ADBenefits of Azure AD Access Panel integration

Il pannello di accesso di Azure AD offre vantaggi alle aziende nei modi seguenti:The Azure AD Access Panel benefits businesses in the following ways:

**Offre un'esperienza utente intuitiva:** il pannello di accesso offre un'unica piattaforma per tutte le applicazioni connesse con SSO (Single Sign-On) di Azure.Provides intuitive user experience : The Access Panel provides you with a single platform for all of your Azure single sign-on (SSO) -connected applications. Si dispone di un portale unificato per trovare le impostazioni esistenti e le nuove funzionalità, ad esempio la gestione dei gruppi e la reimpostazione della password self-service, man mano che vengono aggiunte. L'esperienza intuitiva consente agli utenti di tornare a lavorare più velocemente ed essere più produttivi, riducendo al contempo la loro frustrazione.

**Aumenta la produttività**: tutte le applicazioni utente nel pannello di accesso hanno SSO abilitato. L'abilitazione di SSO tra le applicazioni aziendali e Office 365 crea un'esperienza di accesso superiore riducendo o eliminando richieste di accesso aggiuntive. Il pannello di accesso utilizza l'appartenenza self-service e dinamica e migliora la sicurezza complessiva del sistema di identità. Ciò si assicura che le persone giuste gestiscano l'accesso alle applicazioni. Il pannello di accesso funge da pagina di destinazione coerente per trovare rapidamente le risorse e continuare le attività lavorative.

**Gestisce i costi:** l'abilitazione del pannello di accesso con Azure AD può contribuire alla cessione delle infrastrutture locali. Riduce i costi di supporto fornendo un portale coerente per trovare tutte le app, richiedere l'accesso alle risorse e gestire gli account.

**Aumenta la flessibilità e la sicurezza:** il pannello di accesso consente di accedere alla sicurezza e alla flessibilità offerte da una piattaforma cloud. Gli amministratori possono modificare facilmente le impostazioni di applicazioni e risorse e possono soddisfare nuovi requisiti di sicurezza senza influire sugli utenti.

**Abilita il controllo affidabile e il monitoraggio dell'utilizzo:** il controllo e il monitoraggio dell'utilizzo per tutte le funzionalità degli utenti consentono di sapere quando gli utenti utilizzano le proprie risorse e garantiscono la valutazione della sicurezza.

### <a name="licensing-considerations"></a>Considerazioni sulla gestione delle licenze

Il pannello di accesso è gratuito e non richiede licenze da utilizzare a livello di base. Tuttavia, il numero di oggetti nella directory e le funzionalità aggiuntive che si desidera distribuire possono richiedere licenze aggiuntive. Alcuni scenari comuni di Azure AD con requisiti di licenza includono le funzionalità di sicurezza seguenti:Some common Azure AD scenarios that have licensing requirements include the following security features:

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Appartenenza basata su gruppo](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Vedere la [guida completa alle licenze per Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Prerequisiti per la distribuzione del pannello di accesso di Azure ADPrerequisites for deploying the Azure AD Access Panel

Completare i prerequisiti seguenti prima di iniziare questo progetto:

* [Integrare l'applicazione SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Gestire l'infrastruttura di utenti e gruppi di Azure ADManage Azure AD user and group infrastructure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Pianificare la distribuzione del pannello di accesso di Azure ADPlan the Azure AD Access Panel deployment

Nella tabella seguente vengono descritti i principali casi d'uso per una distribuzione del Pannello di accesso:The following table outlines the key use cases for an Access Panel deployment:

| Area| Descrizione |
| - | - |
| Accesso| Il portale Access Panel è accessibile da dispositivi aziendali e personali all'interno della rete aziendale. |
|Accesso | Il portale Access Panel è accessibile da dispositivi aziendali esterni alla rete aziendale. |
| Controllo| I dati di utilizzo vengono scaricati nei sistemi aziendali almeno ogni 29 giorni. |
| Governance| Il ciclo di vita delle assegnazioni utente alle applicazioni e ai gruppi connessi ad Azure AD viene definito e monitorato. |
| Security| L'accesso alle risorse è controllato tramite assegnazioni di utenti e gruppi. Solo gli utenti autorizzati possono gestire l'accesso alle risorse. |
| Prestazioni| Le sequenze temporali di propagazione delle assegnazioni di accesso sono documentate e monitorate. |
| Esperienza utente| Gli utenti sono a conoscenza delle funzionalità del pannello di accesso e di come utilizzarle.|
| Esperienza utente| Gli utenti possono gestire il loro accesso ad applicazioni e gruppi.|
| Esperienza utente| Gli utenti possono gestire i propri account. |
| Esperienza utente| Gli utenti sono a conoscenza della compatibilità del browser. |
| Supporto| Gli utenti possono trovare il supporto per i problemi del Pannello di accesso. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Procedure consigliate per la distribuzione del pannello di accesso di Azure ADBest practices for deploying the Azure AD Access Panel

La funzionalità del pannello di accesso può essere abilitata gradualmente. È consigliabile il seguente ordine di distribuzione:

1. App personali
   * Icona di avvio delle app
   * Gestione delle app self-service
   * Integrazione con Microsoft Office 365

1. Individuazione delle app self-service
   * Reimpostazione della password self-service
   * Impostazioni di Multi-Factor Authentication
   * Gestione dei dispositivi
   * Condizioni per l'utilizzo
   * Gestire le organizzazioni

1. Gruppi personali
   * Gestione di gruppi self-service
1. Verifiche di accesso
   * Gestione delle revisioni di accesso

A partire da App personali, gli utenti vengono presentati al portale come un luogo comune per l'accesso alle risorse. L'aggiunta dell'individuazione di applicazioni self-service si basa sull'esperienza Delle app personali. I miei gruppi e le recensioni di accesso si basano sulle funzionalità self-service.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Pianificare le configurazioni per il pannello di accesso di Azure ADPlan configurations for the Azure AD Access Panel

Nella tabella seguente sono elencate diverse configurazioni importanti del Pannello di accesso e i valori tipici che è possibile utilizzare:

| Configurazione| Valori tipici |
| - | - |
| Determinare i gruppi pilota| Identificare il gruppo di sicurezza di Azure AD da usare e assicurarsi che tutti i membri pilota facciano parte del gruppo. |
| Determinare il gruppo o i gruppi da abilitare per la produzione.| Identificare i gruppi di sicurezza di Azure AD o i gruppi di Active Directory sincronizzati con Azure AD da usare. Assicurarsi che tutti i membri pilota facciano parte del gruppo. |
| Consentire agli utenti di utilizzare SSO per determinati tipi di applicazioniAllow users to use SSO to certain types of applications| SSO federato, OAuth, Password SSO, proxy app |
| Consentire agli utenti di utilizzare la reimpostazione della password self-service | Sì |
| Consentire agli utenti di utilizzare Multi-Factor Authentication| Sì |
| Consentire agli utenti di utilizzare la gestione dei gruppi self-service per determinati tipi di gruppi| Gruppi di sicurezza, gruppi di Office 365 |
| Consentire agli utenti di usare la gestione delle app self-serviceAllow users to use self-service app management| Sì |
| Consentire agli utenti di utilizzare le verifiche di accessoAllow users to use access reviews| Sì |

### <a name="plan-consent-strategy"></a>Pianificare la strategia di consenso

Gli utenti o gli amministratori devono acconsentire alle condizioni per l'utilizzo e alle norme sulla privacy di qualsiasi applicazione. Se possibile, date le regole aziendali, utilizzare il consenso dell'amministratore, che offre agli utenti un'esperienza migliore.

Per utilizzare il consenso dell'amministratore, è necessario essere un amministratore globale dell'organizzazione e le applicazioni devono essere:

* Registrato nell'organizzazione

* Registrato in un'altra organizzazione di Azure AD e precedentemente consentito da almeno un utente

Per altre informazioni, vedere Configurare il modo in [cui gli utenti finali acconsentono a un'applicazione in Azure Active Directory.For](configure-user-consent.md)more information, see Configure the way users end users consent to an application in Azure Active Directory .

### <a name="engage-the-right-stakeholders"></a>Coinvolgi le parti interessate giuste

Quando i progetti tecnologici falliscono, in genere lo fanno a causa delle aspettative non corrispondenti su impatto, risultati e responsabilità. Per evitare queste insidie, [assicurati di coinvolgere le parti interessate giuste](../fundamentals/active-directory-deployment-plans.md) e che i ruoli degli stakeholder nel progetto siano ben compresi.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di ogni nuovo servizio. Informare in modo proattivo gli utenti come e quando la loro esperienza cambierà e come ottenere supporto, se necessario.

Anche se il pannello di accesso in genere non crea problemi utente, è importante preparare. Creare guide e un elenco di tutte le risorse per il personale di supporto prima del lancio.

#### <a name="communications-templates"></a>Modelli di comunicazione

Microsoft fornisce [modelli personalizzabili per i messaggi](https://aka.ms/APTemplates) di posta elettronica e altre comunicazioni per il pannello di accesso. È possibile adattare queste risorse per l'utilizzo in altri canali di comunicazione in base alla cultura aziendale.

## <a name="plan-your-sso-configuration"></a>Pianificare la configurazione SSO

Quando un utente accede a un'applicazione, passa attraverso un processo di autenticazione e sono tenuti a dimostrare chi sono. Senza SSO, nell'applicazione viene memorizzata una password e all'utente viene richiesto di conoscere la password. Con SSO, le credenziali degli utenti vengono passate all'applicazione, pertanto non è necessario immettere nuovamente le password per ogni applicazione.

Per avviare le applicazioni in App personali, È necessario abilitare SSO.

Azure AD supporta tre modi diversi per abilitare [l'accesso Single Sign-On nelle applicazioni:](what-is-single-sign-on.md)

* **Single Sign-On federato** 
    * Consente a un'applicazione di reindirizzare ad Azure AD per l'autenticazione utente, anziché richiedere una password. 
    * È supportato per le applicazioni che utilizzano protocolli, ad esempio SAML 2.0, WS-Federation o OpenID Connect, ed è la modalità più ricca di Single Sign-On.

* **Single Sign-On basato su password** 
    * Consente l'archiviazione e la riproduzione sicura delle password delle applicazioni tramite un'estensione del browser Web o un'app per dispositivi mobili. 
    * Sfrutta il processo di accesso esistente fornito dall'applicazione, ma consente a un amministratore di gestire le password. All'utente non è richiesto di conoscere la password.

* **Single Sign-On esistente** 
    * Consente ad Azure AD di sfruttare i vantaggi di qualsiasi Single Sign-On esistente configurato per l'applicazione.
    * Consente a queste applicazioni di essere collegate ai portali del pannello di accesso di Office 365 o Azure AD. 
    * Abilita la creazione di report aggiuntivi in Azure AD quando le applicazioni vengono avviate. 
    * Include l'utilizzo del proxy di applicazione di Azure e della modalità Single Sign-On collegata.

Per informazioni su come configurare la modalità SSO di un'applicazione, vedere [Single Sign-On to applications in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Per un'esperienza ottimale con la pagina App personali, iniziare con l'integrazione delle applicazioni cloud disponibili per SSO federato. SSO federato consente agli utenti di avere un'esperienza coerente con un solo clic tra le superfici di avvio dell'app e tende a essere più affidabile nel controllo della configurazione.

Usare SSO federato con Azure AD (OpenID Connect/SAML) quando un'applicazione lo supporta, anziché SSO e ADFS basati su password.

Per ulteriori informazioni su come distribuire e configurare le applicazioni SaaS, vedere il piano di [distribuzione SSO SaaS](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Pianificare la distribuzione dell'estensione del browser App personali

Quando gli utenti accedono alle applicazioni SSO basate su password, devono installare e usare l'estensione accesso sicuro alle app personali. L'estensione esegue uno script che trasmette la password nel modulo di accesso dell'applicazione. Agli utenti viene richiesto di installare l'estensione al primo avvio dell'applicazione SSO basata su password. Ulteriori informazioni sull'estensione sono disponibili in questa documentazione [sull'installazione dell'estensione del browser Pannello](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)di accesso .

Se è necessario integrare applicazioni SSO basate su password, è necessario definire un meccanismo per distribuire l'estensione su larga scala con [i browser supportati.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) Le opzioni includono:

* [Criteri di gruppo per Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Gestione configurazione per Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Download e configurazione guidati dall'utente per Chrome, Firefox, Microsoft Edge o IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Ulteriori informazioni: [Come configurare l'accesso Single Sign-On](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)con password .

Anche gli utenti che non utilizzano applicazioni SSO basate su password traggono vantaggio dall'estensione. Questi vantaggi includono la possibilità di avviare qualsiasi app dalla barra di ricerca, trovare l'accesso alle applicazioni usate di recente e avere un collegamento alla pagina Le mie app.

Ecco cosa vedrà l'utente quando avvia un'applicazione SSO basata su password per la prima volta:

![Screenshot della schermata di installazione dell'estensione del browser App personali ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Pianificare l'accesso mobile

Un browser protetto con criteri di Intune (Microsoft Edge o Intune Managed Browser) è necessario per gli utenti mobili che avviano applicazioni SSO basate su password. Un browser protetto da criteri consente il trasferimento della password salvata per l'applicazione. Microsoft Edge o il browser gestito offre un set di funzionalità di protezione dei dati Web. Puoi anche usare Microsoft Edge per scenari aziendali su dispositivi iOS e Android.You can also use Microsoft Edge for enterprise scenarios on iOS and Android devices. Microsoft Edge supporta gli stessi scenari di gestione di Intune Managed Browser e migliora l'esperienza utente. Altre informazioni: [Gestire l'accesso Web usando un browser protetto da criteri](https://docs.microsoft.com/intune/app-configuration-managed-browser)di Microsoft Intune.

## <a name="plan-your-my-apps-deployment"></a>Pianificare la distribuzione di app personali

La base del pannello di accesso è il programma [https://myapps.microsoft.com](https://myapps.microsoft.com/)di avvio delle applicazioni My Apps, a cui gli utenti accedono a . Le pagine App personali offrono agli utenti un'unica posizione per iniziare il proprio lavoro e accedere alle applicazioni necessarie. Qui, gli utenti trovano un elenco di tutte le applicazioni a cui hanno accesso Single Sign-On. 

![Cattura di schermata del pannello delle app](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Le stesse applicazioni verranno visualizzate nell'icona di avvio delle app di Office 365 quando gli utenti utilizzano il portale di Office 365.

Pianificare l'ordine in cui si aggiungeranno applicazioni al programma di avvio Delle app personali e decidere se distribuirle gradualmente o tutte contemporaneamente. A tale scopo, creare un inventario dell'applicazione che elenca il tipo di autenticazione e tutte le integrazioni SSO esistenti per ogni applicazione.

#### <a name="add-applications-to-the-my-apps-panel"></a>Aggiungere applicazioni al pannello App personali

Qualsiasi applicazione abilitata per SSO di Azure AD può essere aggiunta all'utilità di avvio App personali. Altre applicazioni vengono aggiunte utilizzando l'opzione SSO collegato. È possibile configurare un riquadro dell'applicazione che si collega all'URL dell'applicazione Web esistente. SSO collegato consente di iniziare a indirizzare gli utenti al portale My Apps senza eseguire la migrazione di tutte le applicazioni ad Azure AD SSO. È possibile passare gradualmente alle applicazioni configurate con SSO di Azure AD senza interrompere l'esperienza degli utenti.

#### <a name="use-my-apps-collections"></a>Usare le raccolte di app personaliUse My Apps collections

Per impostazione predefinita, tutte le applicazioni sono elencate insieme in una singola pagina. È tuttavia possibile utilizzare le raccolte per raggruppare le applicazioni correlate e presentarle in una scheda separata, semplificandone l'individuazione. Ad esempio, è possibile utilizzare le raccolte per creare raggruppamenti logici di applicazioni per ruoli di lavoro, attività, progetti e così via specifici. Per informazioni, consultate [Come utilizzare le raccolte Di App personali per personalizzare i pannelli di accesso utente.](access-panel-collections.md) 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Pianificare se usare le app personali o un portale esistente

È possibile che gli utenti dispongano già di un'applicazione o di un portale diverso da App personali. In tal caso, decidere se supportare entrambi i portali o utilizzarne solo uno.

Se un portale esistente è già utilizzato come punto di partenza per gli utenti, è possibile integrare la funzionalità App personali utilizzando gli URL di accesso utente. Gli URL di accesso utente funzionano come collegamenti diretti alle applicazioni disponibili nel portale App personali. Questi URL possono essere incorporati in qualsiasi sito web esistente. Quando un utente seleziona il collegamento, apre l'applicazione dal portale App personali.

È possibile trovare la proprietà URL di accesso utente nell'area Proprietà dell'applicazione nel portale di Azure.You can find the user access URL property in the **Properties** area of the application in the Azure portal.

![Cattura di schermata del pannello delle app](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Pianificare l'individuazione e l'accesso alle applicazioni self-service

Dopo aver distribuito un set di applicazioni di base nella pagina App personali di un utente, è necessario abilitare le funzionalità di gestione delle app self-service. L'individuazione self-service delle app consente agli utenti di:

* Trova nuove app da aggiungere alle mie app. 
* Aggiungi app facoltative che potrebbero non sapere di cui hanno bisogno durante la configurazione.

I flussi di lavoro di approvazione sono disponibili per l'approvazione esplicita per l'accesso alle applicazioni. Gli utenti che sono responsabili dell'approvazione riceveranno notifiche all'interno del portale My Apps quando sono presenti richieste di accesso all'applicazione in sospeso.

## <a name="plan-self-service-group-membership"></a>Pianificare l'appartenenza ai gruppi self-service 

È possibile consentire agli utenti di creare e gestire i propri gruppi di sicurezza o gruppi di Office 365 in Azure AD. Il proprietario del gruppo può approvare o rifiutare le richieste di appartenenza e delegare il controllo dell'appartenenza al gruppo. Le funzionalità di gestione dei gruppi in modalità self-service non sono disponibili per i gruppi di sicurezza abilitati alla posta elettronica o le liste di distribuzione.

Per pianificare l'appartenenza al gruppo self-service, determinare se si consentirà a tutti gli utenti dell'organizzazione di creare e gestire gruppi o solo un sottoinsieme di utenti. Se consenti un sottoinsieme di utenti, dovrai configurare un gruppo a cui vengono aggiunte tali persone. Per informazioni dettagliate sull'abilitazione di questi scenari, vedere Configurare la gestione dei [gruppi self-service in Azure Active Directory.See Set up self-service group management in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) for details on enabling these scenarios.

## <a name="plan-reporting-and-auditing"></a>Pianificare la creazione di report e il controllo

Azure AD fornisce [report che offrono informazioni tecniche e aziendali.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) Collaborare con i proprietari aziendali e tecnici delle applicazioni per assumere la proprietà di questi report e utilizzarli regolarmente. Nella tabella seguente vengono forniti alcuni esempi di scenari di report tipici.

|   | per gestire i rischi.| Aumentare la produttività| Governance e conformità |
|  - |- | - | - |
| Tipi di report|  Autorizzazioni e utilizzo dell'applicazione| Attività di provisioning dell'account| Verificare chi accede alle applicazioniReview who is accessing the applications |
| Azioni potenziali| Controllare l'accesso; revoca delle autorizzazioni| Correggere eventuali errori di provisioningRemediate any provisioning errors| revoca dell'accesso |

Azure AD mantiene la maggior parte dei dati di controllo per 30 giorni. I dati sono disponibili tramite il portale di amministrazione di Azure o l'API da scaricare nei sistemi di analisi.

#### <a name="auditing"></a>Controllo

I registri di controllo per l'accesso alle applicazioni sono disponibili per 30 giorni. Se il controllo della sicurezza all'interno dell'azienda richiede una conservazione più lunga, i registri devono essere esportati in uno strumento SIEM (Security Information Event and Management), ad esempio Splunk o ArcSight.

Per i backup di controllo, creazione di report e ripristino di emergenza, documentare la frequenza richiesta di download, il sistema di destinazione e chi è responsabile della gestione di ogni backup. Potrebbero non essere necessari backup di controllo e di report separati. Il backup del ripristino di emergenza deve essere un'entità separata.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Distribuire le applicazioni nel pannello App personali degli utenti

Dopo aver configurato un'applicazione per SSO, ai gruppi viene assegnato l'accesso. Gli utenti dei gruppi assegnati avranno accesso e vedranno l'applicazione nelle app personali e nell'icona di avvio delle app di Office 365.

Vedere [Assegnare utenti e gruppi a un'applicazione in Active Directory.](methods-for-assigning-users-and-groups.md)

Se durante il test o la distribuzione si desidera aggiungere i gruppi ma non si consentono ancora la visualizzazione delle applicazioni in App personali, vedere [Nascondere un'applicazione dall'esperienza dell'utente in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Distribuire le applicazioni di Microsoft Office 365 nelle app personali

Per le applicazioni di Office 365, gli utenti ricevono una copia di Office in base alle licenze assegnate. Un prerequisito per l'accesso alle applicazioni di Office è per gli utenti da assegnare le licenze corrette legate alle applicazioni di Office. Quando si assegna a un utente una licenza, queste verranno visualizzate automaticamente le applicazioni associate alla licenza nella pagina App personali e nell'icona di avvio delle app di Office 365.

Se si vuole nascondere un set di applicazioni di Office agli utenti, è disponibile un'opzione per nascondere le app dal portale App personali, consentendo comunque l'accesso dal portale di Office 365. Trovare queste impostazioni nella parte Impostazioni utente dell'applicazione. Altre informazioni: [Nascondere un'applicazione dall'esperienza dell'utente in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)

![Uno screenshot della configurazione di come nascondere le applicazioni](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Distribuire funzionalità self-service per le applicazioniDeploy application self-service capabilities

L'accesso alle applicazioni self-service consente agli utenti di individuare autonomamente e richiedere l'accesso alle applicazioni. Gli utenti hanno la libertà di accedere alle app di cui hanno bisogno senza passare attraverso un gruppo IT ogni volta per richiedere l'accesso. Quando un utente richiede l'accesso e viene approvato, automaticamente o manualmente dal proprietario dell'app, viene aggiunto a un gruppo nel back-end. La creazione di report è abilitata su chi ha richiesto, approvato o rimosso l'accesso e consente di controllare la gestione dei ruoli assegnati.

È possibile delegare l'approvazione delle richieste di accesso alle applicazioni agli approvatori aziendali. L'approvatore aziendale può impostare le password di accesso all'app dalla pagina App personali dell'approvatore aziendale.

Ulteriori informazioni: [Come utilizzare l'accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![Schermata della configurazione della gestione delle applicazioni self-service](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Convalidare la distribuzione

Assicurarsi che la distribuzione del Pannello di accesso sia accuratamente testata e che sia stato pianificato un piano di rollback.

I seguenti test devono essere condotti sia con dispositivi di proprietà dell'azienda che con dispositivi personali. Questi test case devono inoltre riflettere i casi d'uso aziendali. Di seguito sono riportati alcuni casi in base ai requisiti aziendali di esempio in questo documento e a scenari tecnici tipici. Aggiungi altre specifiche alle tue esigenze.

#### <a name="application-sso-access-test-case-examples"></a>Esempi di test case di accesso SSO dell'applicazione:Application SSO access test case examples:


| Business case| Risultato previsto |
| - | -|
| L'utente accede al portale Delle app personali| L'utente può accedere e visualizzare le applicazioni |
| L'utente avvia un'applicazione SSO federata| L'utente ha eseguito automaticamente l'accesso all'applicazione |
| L'utente avvia un'applicazione SSO con password per la prima volta| L'utente deve installare l'estensione App personali |
| L'utente avvia un'applicazione SSO password un'ora successiva| L'utente ha eseguito automaticamente l'accesso all'applicazione |
| L'utente avvia un'app dal portale di Office 365| L'utente ha eseguito automaticamente l'accesso all'applicazione |
| L'utente avvia un'app dal Browser gestito| L'utente ha eseguito automaticamente l'accesso all'applicazione |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Esempi di test case sulle funzionalità self-service delle applicazioniApplication self-service test case examples


| Business case| Risultato previsto |
| - | - |
| L'utente può gestire l'appartenenza all'applicazione| L'utente può aggiungere/rimuovere membri che hanno accesso all'app |
| L'utente può modificare l'applicazione| L'utente può modificare la descrizione dell'applicazione e le credenziali per le applicazioni SSO password |

### <a name="rollback-steps"></a>Procedura di ripristino dello stato precedente

È importante pianificare le operazioni da eseguire se la distribuzione non viene completata come previsto. Se la configurazione SSO non riesce durante la distribuzione, è necessario comprendere come risolvere i problemi relativi a [SSO](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) e ridurre l'impatto sugli utenti. In circostanze estreme, potrebbe essere necessario eseguire il [rollback SSO](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Gestisci la tua implementazione

È consigliabile usare il ruolo con privilegi minimi per eseguire un'attività richiesta in Azure Active Directory.You should use the least privileged role to accomplish a required task within Azure Active Directory. [Esaminare i diversi ruoli disponibili](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e scegliere quello giusto per risolvere le proprie esigenze per ogni persona per questa applicazione. Potrebbe essere necessario applicare temporaneamente alcuni ruoli e rimuoverli dopo il completamento della distribuzione.

| Utenti tipo| Ruoli| Ruolo di Azure AD  |
| - | -| -|
| Amministratore dell'helpdesk| Supporto per il livello 1| nessuno |
| Amministratore identità| Configurare ed eseguire il debug quando i problemi influiscono su Azure ADConfigure and debug when issues impact Azure AD| Amministratore globale |
| Amministratore dell'applicazione| Attestazione dell'utente nell'applicazione, configurazione per gli utenti con autorizzazioni| nessuno |
| Amministratori dell'infrastruttura| Proprietario rollover del certificatoCert rollover owner| Amministratore globale |
| Imprenditore/stakeholder| Attestazione dell'utente nell'applicazione, configurazione per gli utenti con autorizzazioni| nessuno |

È possibile utilizzare [Gestione identità con privilegi](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) per gestire i ruoli per fornire controllo aggiuntivo, controllo e verifica dell'accesso per gli utenti con autorizzazioni di directory.

### <a name="troubleshoot-access-panel-issues"></a>Risolvere i problemi relativi al Pannello di accesso

Creare guide alla risoluzione dei problemi per l'organizzazione di supporto con scenari comuni, che puntano alla documentazione Microsoft nelle relative risoluzioni. È possibile creare guide che interrompano il supporto nei livelli utilizzati dall'organizzazione.

Per informazioni di riferimento, consulta queste guide alla risoluzione dei problemi:

[Applicazioni non visualizzate](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Applicazioni impreviste che appaiono](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[L'utente non può accedere al pannello di accesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemi relativi all'utilizzo dell'accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemi con l'estensione del browser](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Passaggi successivi

[Pianificare una distribuzione di Azure Multi-Factor AuthenticationPlan a deployment of Azure Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)
