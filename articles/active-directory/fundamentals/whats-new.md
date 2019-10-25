---
title: Novità Note sulla versione - Azure Active Directory | Microsoft Docs
description: Informazioni sulle novità di Azure Active Directory, tra cui le note sulla versione più aggiornate, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e732e62afcc7af0a2b90d7c525a6de5e65195aa
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809242"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Consente di ricevere notifiche su quando rivisitare la pagina per gli aggiornamenti copiando e incollando questo URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` nell'icona del lettore del feed RSS ![](./media/whats-new/feed-icon-16x16.png) lettore di feed.

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarli in [Archivio per le novità in Azure Active Directory](whats-new-archive.md).

---

## <a name="october-2019"></a>2019 ottobre

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Deprecazione dell'API identityRiskEvent per i rilevamenti dei rischi Azure AD Identity Protection  

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

In risposta al feedback degli sviluppatori, gli abbonati Azure AD Premium P2 possono ora eseguire query complesse sui dati di rilevamento dei rischi Azure AD Identity Protection usando la nuova API riskDetection per Microsoft Graph. La versione beta dell'API [identityRiskEvent](https://docs.microsoft.com/en-us/graph/api/resources/identityriskevent?view=graph-rest-beta) esistente smetterà di restituire i dati circa il **10 gennaio 2020**. Se l'organizzazione usa l'API identityRiskEvent, è necessario passare alla nuova API di riskDetection.

Per ulteriori informazioni sulla nuova API riskDetection, vedere la [documentazione di riferimento dell'API di rilevamento dei rischi](https://aka.ms/RiskDetectionsAPI).

---

## <a name="september-2019"></a>Settembre 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Piano per la modifica: deprecazione dei pacchetti di contenuto Power BI

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

A partire dal 1 ° ottobre 2019, Power BI inizierà a deprecare tutti i pacchetti di contenuto, incluso il pacchetto di contenuto Azure AD Power BI. In alternativa a questo pacchetto di contenuto, è possibile usare Azure AD cartelle di lavoro per ottenere informazioni approfondite sui servizi correlati a Azure AD. Sono in arrivo cartelle di lavoro aggiuntive, incluse le cartelle di lavoro sui criteri di accesso condizionale in modalità solo report, informazioni dettagliate basate sul consenso delle app e altro ancora.

Per ulteriori informazioni sulle cartelle di lavoro di, vedere [come utilizzare le cartelle di lavoro di monitoraggio di Azure per Azure Active Directory report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Per altre informazioni sulla deprecazione dei pacchetti di contenuto, vedere il post di Blog relativo all' [annuncio della disponibilità generale delle app](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) per i modelli di Power bi.

---

### <a name="my-profile-is-re-naming-and-integrating-with-the-microsoft-office-account-page"></a>Il profilo è stato rinominato e integrato nella pagina dell'account Microsoft Office

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Profilo/account  
**Funzionalità del prodotto:** Collaborazione

A partire da ottobre, l'esperienza di profilo personale diventerà il mio account. Come parte di questa modifica, ovunque si trovi attualmente, il **mio profilo** cambierà nel **mio account**. Oltre alla modifica dei nomi e ad alcuni miglioramenti della progettazione, l'esperienza aggiornata offrirà integrazione aggiuntiva con la pagina account Microsoft Office. In particolare, sarà possibile accedere alle installazioni e alle sottoscrizioni di Office dalla pagina **Panoramica dell'account** , insieme alle preferenze di contatto relative a Office dalla pagina **privacy** .

Per altre informazioni sull'esperienza my profile (anteprima), vedere [Cenni preliminari sul portale My Profile (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gestire in blocco i gruppi e i membri usando i file CSV nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Siamo lieti di annunciare la disponibilità dell'anteprima pubblica delle esperienze di gestione dei gruppi in blocco nel portale di Azure AD. È ora possibile usare un file CSV e il portale di Azure AD per gestire i gruppi e gli elenchi di membri, tra cui:

- Aggiunta o rimozione di membri da un gruppo.

- Download dell'elenco di gruppi dalla directory.

- Download dell'elenco dei membri del gruppo per un gruppo specifico.

Per ulteriori informazioni, vedere l'elenco [bulk Add members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [bulk Remove members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [bulk download members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)e bulk [download groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Il consenso dinamico è ora supportato tramite un nuovo endpoint di consenso dell'amministratore

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

È stato creato un nuovo endpoint di consenso dell'amministratore per supportare il consenso dinamico, utile per le app che vogliono usare il modello di consenso dinamico sulla piattaforma di identità Microsoft.

Per altre informazioni su come usare questo nuovo endpoint, vedere [uso dell'endpoint di consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-azure-ad-global-reader-role"></a>Nuovo ruolo lettore globale Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso

A partire dal 24 settembre 2019, verrà avviato il rollup di un nuovo ruolo di Azure Active Directory (AD) denominato Global Reader. Questa implementazione inizierà con la produzione e i clienti cloud globali (GCC), completando il mondo nel corso di ottobre.

Il ruolo di lettore globale è la controparte di sola lettura dell'amministratore globale. Gli utenti con questo ruolo possono leggere le impostazioni e le informazioni amministrative tra Microsoft 365 servizi, ma non possono eseguire azioni di gestione. Il ruolo di lettore globale è stato creato per ridurre il numero di amministratori globali dell'organizzazione. Poiché gli account di amministratore globale sono potenti e vulnerabili ad attacchi, è consigliabile disporre di meno di cinque amministratori globali. È consigliabile usare il ruolo di lettore globale per la pianificazione, i controlli o le indagini. Si consiglia inoltre di utilizzare il ruolo di lettore globale in combinazione con altri ruoli di amministratore limitati, ad esempio amministratore di Exchange, per facilitare il lavoro senza richiedere il ruolo di amministratore globale.

Il ruolo di lettore globale funziona con il nuovo centro di amministrazione di Microsoft 365, l'interfaccia di amministrazione di Exchange, l'interfaccia di amministrazione dei team, il Centro sicurezza, il centro di conformità, l'interfaccia di amministrazione di Azure AD e l'interfaccia di amministrazione Gestione dispositivi.

>[!NOTE]
> All'inizio della versione di anteprima pubblica, il ruolo di lettore globale non funzionerà con: SharePoint, Privileged Access Management, Customer Lockbox, etichette di riservatezza, ciclo di vita dei team, creazione di report per i team & analisi delle chiamate, gestione dei dispositivi telefonici IP e app Teams Catalogo. Tutti questi servizi sono destinati a funzionare con il ruolo in futuro.

Per ulteriori informazioni, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Accedere a un server di report locale dall'app Power BI mobile usando Azure Active Directory Application Proxy

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Una nuova integrazione tra il Power BI app per dispositivi mobili e Azure AD proxy di applicazione consente di accedere in modo sicuro all'app per dispositivi mobili Power BI e di visualizzare i report dell'organizzazione ospitati nell'Server di report di Power BI locale.

Per informazioni sull'app per dispositivi mobili Power BI, inclusa la posizione in cui scaricare l'app, vedere il [sito Power bi](https://powerbi.microsoft.com/mobile/). Per altre informazioni su come configurare l'app per dispositivi mobili Power BI con Azure AD proxy di applicazione, vedere [abilitare l'accesso remoto a Power bi dispositivi mobili con Azure ad proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>È disponibile una nuova versione del modulo AzureADPreview di PowerShell

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Directory

Sono stati aggiunti nuovi cmdlet al modulo AzureADPreview per semplificare la definizione e l'assegnazione di ruoli personalizzati in Azure AD, tra cui:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nuova versione di Azure AD Connect

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Directory

È stata rilasciata una versione aggiornata di Azure AD Connect per i clienti con aggiornamento automatico. Questa nuova versione include diverse nuove funzionalità, miglioramenti e correzioni di bug. Per ulteriori informazioni su questa nuova versione, vedere [Azure ad Connect: cronologia delle versioni](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication Server (multi-factor authentication), versione 8.0.2 è ora disponibile

**Tipo:** Correzione  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Se si è un cliente esistente, che ha attivato il server di autenticazione a più fattori prima del 1 ° luglio 2019, è ora possibile scaricare la versione più recente di multi-factor authentication server (versione 8.0.2). In questa nuova versione viene:

- Correzione di un problema. quando Azure AD Sync modifica un utente da disabilitato ad abilitato, viene inviato un messaggio di posta elettronica all'utente.

- È stato risolto un problema per consentire ai clienti di eseguire l'aggiornamento, continuando a usare la funzionalità tag.

- È stato aggiunto il codice paese Kosovo (+ 383).

- Aggiunta della registrazione di controllo bypass monouso a MultiFactorAuthSvc. log.

- Miglioramento delle prestazioni per l'SDK del servizio Web.

- Correzione di altri bug secondari.

A partire dal 1 ° luglio 2019, Microsoft ha smesso di offrire server di autenticazione a più fattori per nuove distribuzioni. I nuovi clienti che richiedono l'autenticazione a più fattori devono usare Azure Multi-Factor Authentication basato sul cloud. Per altre informazioni, vedere [pianificazione di una distribuzione di Azure multi-factor authentication basata sul cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Agosto 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>La funzionalità di ricerca, filtro e ordinamento migliorato per i gruppi è disponibile nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Siamo lieti di annunciare la disponibilità dell'anteprima pubblica delle esperienze correlate ai gruppi avanzati nel portale di Azure AD. Questi miglioramenti consentono una migliore gestione dei gruppi e degli elenchi di membri, fornendo:

- Funzionalità di ricerca avanzate, ad esempio la ricerca di sottostringhe negli elenchi di gruppi.
- Opzioni di filtro e ordinamento avanzate negli elenchi di membri e proprietari.
- Nuove funzionalità di ricerca per gli elenchi di membri e proprietari.
- Conteggi di gruppo più accurati per gruppi di grandi dimensioni.

Per ulteriori informazioni, vedere [Manage groups in the portale di Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Sono disponibili nuovi ruoli personalizzati per la gestione della registrazione delle app (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso

I ruoli personalizzati, disponibili con una sottoscrizione Azure AD P1 o P2, possono ora offrire un accesso con granularità fine, consentendo di creare definizioni di ruolo con autorizzazioni specifiche e quindi di assegnare tali ruoli a risorse specifiche. Attualmente, è possibile creare ruoli personalizzati usando le autorizzazioni per la gestione delle registrazioni delle app e quindi l'assegnazione del ruolo a un'app specifica. Per ulteriori informazioni sui ruoli personalizzati, vedere [ruoli di amministratore personalizzato in Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Se sono necessarie altre autorizzazioni o risorse supportate, che attualmente non vengono visualizzate, è possibile inviare commenti e suggerimenti al [sito di commenti e suggerimenti su Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) .

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>I nuovi log di provisioning consentono di monitorare e risolvere i problemi della distribuzione del provisioning delle app (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

Sono disponibili nuovi log di provisioning che consentono di monitorare e risolvere i problemi relativi alla distribuzione del provisioning di utenti e gruppi. I nuovi file di log includono informazioni su:

- Quali gruppi sono stati creati correttamente in [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Quali ruoli sono stati importati da [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Quali dipendenti non sono stati importati dalla [giornata lavorativa](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Per altre informazioni, vedere [provisioning dei report nel portale di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nuovi report sulla sicurezza per tutti gli amministratori di Azure AD (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per impostazione predefinita, tutti gli amministratori di Azure AD saranno presto in grado di accedere ai report di sicurezza moderni entro Azure AD. Fino alla fine di settembre, sarà possibile usare il banner nella parte superiore dei report di sicurezza moderni per tornare ai report precedenti.

I report di sicurezza moderni forniranno funzionalità aggiuntive dalle versioni precedenti, tra cui:

- Filtro e ordinamento avanzati
- Azioni bulk, ad esempio la dismissing del rischio utente
- Conferma di entità compromesse o sicure
- Stato di rischio, copertura: a rischio, non rilasciata, risolta e confermata compromessa
- Nuovi rilevamenti correlati al rischio (disponibili per Azure AD Premium sottoscrittori)

Per altre informazioni, vedere [utenti a rischio](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report), [accessi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)a rischio e rilevamento dei [rischi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>L'identità gestita assegnata dall'utente è disponibile per le macchine virtuali e i set di scalabilità di macchine virtuali (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identità gestite per le risorse di Azure  
**Funzionalità del prodotto:** esperienza di sviluppo

Le identità gestite assegnate dall'utente sono ora disponibili a livello generale per le macchine virtuali e i set di scalabilità di macchine virtuali. Come parte di questo, Azure può creare un'identità nel tenant di Azure AD considerata attendibile dalla sottoscrizione in uso e può essere assegnata a una o più istanze del servizio di Azure. Per altre informazioni sulle identità gestite assegnate dall'utente, vedere informazioni sulle [identità gestite per le risorse di Azure](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Gli utenti possono reimpostare le password usando un'app per dispositivi mobili o un token hardware (disponibilità generale)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Funzionalità del prodotto:** Autenticazione utente

Gli utenti che hanno registrato un'app per dispositivi mobili con l'organizzazione possono ora reimpostare la propria password approvando una notifica dall'app Microsoft Authenticator o immettendo un codice dall'app per dispositivi mobili o dal token hardware.

Per altre informazioni, vedere [come funziona: Azure ad la reimpostazione della password self-service](https://aka.ms/authappsspr). Per altre informazioni sull'esperienza utente, vedere la [panoramica sulla reimpostazione della password aziendale o dell'Istituto di istruzione](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora la cache condivisa MSAL.NET per gli scenari per conto di

**Tipo:** Correzione  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

A partire da Azure AD Authentication Library (ADAL.NET) versione 5.0.0-Preview, gli sviluppatori [di app devono serializzare una cache per ogni account per le app Web e le API Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). In caso contrario, alcuni scenari che usano il [flusso per conto di](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), insieme ad alcuni casi d'uso specifici di `UserAssertion`, possono determinare un'elevazione dei privilegi. Per evitare questa vulnerabilità, ADAL.NET ignora ora la cache condivisa di Microsoft Authentication Library per DotNet (MSAL.NET) per gli scenari per conto di.

Per altre informazioni su questo problema, vedere [Azure Active Directory vulnerabilità dell'elevazione dei privilegi della libreria di autenticazione](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2019 agosto

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2019 agosto sono state aggiunte le 26 nuove app con supporto federativo per la raccolta di app:

[Piattaforma civica](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [pronovos Ops manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport ' s inativ Portal (Europe)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy Presence](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite endpoint backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by cincot](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [Deliver. Media™ Portal](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat ad Connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [pronovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigate Strategic](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Sono disponibili nuove versioni dei moduli AzureAD PowerShell e AzureADPreview PowerShell

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Directory

Sono disponibili nuovi aggiornamenti ai moduli di PowerShell per AzureAD e AzureAD Preview:

- Al parametro `Get-AzureADDirectoryRole` del modulo AzureAD è stato aggiunto un nuovo parametro `-Filter`. Questo parametro consente di filtrare in base ai ruoli della directory restituiti dal cmdlet.
- Sono stati aggiunti nuovi cmdlet al modulo AzureADPreview per semplificare la definizione e l'assegnazione di ruoli personalizzati in Azure AD, tra cui:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Miglioramenti dell'interfaccia utente del generatore di regole di gruppo dinamico nel portale di Azure

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Sono stati apportati alcuni miglioramenti dell'interfaccia utente al generatore di regole di gruppo dinamico, disponibile nel portale di Azure, per semplificare la configurazione di una nuova regola o per modificare le regole esistenti. Questo miglioramento della progettazione consente di creare regole con fino a cinque espressioni, anziché una sola. È stato aggiornato anche l'elenco delle proprietà del dispositivo per rimuovere le proprietà del dispositivo deprecate.

Per altre informazioni, vedere [gestire le regole di appartenenza dinamica](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nuova autorizzazione Microsoft Graph App disponibile per l'uso con le verifiche di accesso

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** verifiche di accesso  
**Funzionalità del prodotto:** Governance delle identità

È stata introdotta una nuova autorizzazione Microsoft Graph App, `AccessReview.ReadWrite.Membership`, che consente alle app di creare e recuperare automaticamente le verifiche di accesso per l'appartenenza a gruppi e le assegnazioni di app. Questa autorizzazione può essere usata dai processi pianificati o come parte dell'automazione, senza richiedere un contesto utente connesso.

Per altre informazioni, vedere l' [esempio come creare Azure ad verifiche di accesso usando Microsoft Graph le autorizzazioni dell'app con il Blog di PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD log attività sono ora disponibili per le istanze del cloud per enti pubblici in monitoraggio di Azure

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare che Azure AD log attività sono ora disponibili per le istanze del cloud per enti pubblici in monitoraggio di Azure. È ora possibile inviare i log di Azure AD all'account di archiviazione o a un hub eventi per l'integrazione con gli strumenti SIEM, ad esempio [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)e [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Per altre informazioni sulla configurazione di monitoraggio di Azure, vedere [Azure ad log attività in monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aggiornare gli utenti alla nuova esperienza avanzata per le informazioni di sicurezza

**Tipo:** Funzionalità modificata  
**Categoria di servizio:**  Autenticazioni (account di accesso)   
**Funzionalità del prodotto:** Autenticazione utente

A partire dal 25 settembre 2019, verrà disattivata l'esperienza di sicurezza precedente, non avanzata, per la registrazione e la gestione delle informazioni di sicurezza degli utenti e per attivare solo la nuova [versione migliorata](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Ciò significa che gli utenti non saranno più in grado di usare l'esperienza precedente.

Per ulteriori informazioni sull'esperienza avanzata delle informazioni di sicurezza, vedere la [documentazione di amministrazione](https://aka.ms/securityinfodocs)  e la [documentazione dell'utente](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Per attivare questa nuova esperienza, è necessario:

1. Accedere al portale di Azure come amministratore globale o amministratore utente.

2. Passare a **Azure Active Directory > impostazioni utente > Gestisci impostazioni per le funzionalità di anteprima del pannello di accesso**.

3. In **utenti possono usare le funzionalità di anteprima per la registrazione e la gestione delle informazioni di sicurezza-area avanzata** , selezionare **selezionata**, quindi scegliere un gruppo di utenti o scegliere **tutti** per attivare questa funzionalità per tutti gli utenti nel tenant.

4. In * * gli utenti possono usare le funzionalità di anteprima per la registrazione e la gestione della sicurezza * * info * * * * area, selezionare **nessuno**.

5. Salvare le impostazioni.

    Dopo aver salvato le impostazioni, non sarà più possibile accedere all'esperienza di informazioni di sicurezza precedente.

>[!Important]
>Se non si completano questi passaggi prima del 25 settembre 2019, il tenant di Azure Active Directory verrà abilitato automaticamente per migliorare l'esperienza. Per eventuali domande, contattaci all'registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Le richieste di autenticazione che usano gli account di accesso POST verranno convalidate più rigorosamente

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Standard

A partire dal 2 settembre 2019, le richieste di autenticazione che usano il metodo POST verranno convalidate in modo più rigoroso rispetto agli standard HTTP. In particolare, gli spazi e le virgolette doppie (") non verranno più rimossi dai valori del modulo della richiesta. Queste modifiche non sono previste per interrompere i client esistenti e consentiranno di verificare che le richieste inviate a Azure AD siano gestite in modo affidabile ogni volta.

Per ulteriori informazioni, vedere le [notifiche relative alle modifiche Azure ad di rilievo](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Luglio 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Pianificare la modifica: aggiornamento del servizio proxy di applicazione per supportare solo TLS 1,2

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Per fornire la crittografia più avanzata, l'accesso al servizio proxy di applicazione verrà limitato solo ai protocolli TLS 1,2. Questa limitazione verrà inizialmente implementata per i clienti che usano già i protocolli TLS 1,2, quindi non sarà possibile vedere l'effetto. La deprecazione completa dei protocolli TLS 1,0 e TLS 1,1 verrà completata il 31 agosto 2019. I clienti che usano ancora TLS 1,0 e TLS 1,1 riceveranno una notifica avanzata per prepararsi a questa modifica.

Per mantenere la connessione al servizio proxy di applicazione in tutte le modifiche, è consigliabile assicurarsi che le combinazioni client-server e browser-server siano aggiornate per l'uso di TLS 1,2. È inoltre consigliabile assicurarsi di includere tutti i sistemi client usati dai dipendenti per accedere alle app pubblicate tramite il servizio proxy di applicazione.

Per altre informazioni, vedere [aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Pianificare le modifiche: gli aggiornamenti della progettazione sono disponibili per la raccolta di applicazioni

**Tipo:** Modifica pianificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Nuove modifiche all'interfaccia utente verranno apportate alla progettazione dell' **aggiunta dall'area raccolta** del pannello **Aggiungi un'applicazione** . Queste modifiche consentiranno di trovare più facilmente le app che supportano il provisioning automatico, OpenID Connect, Security Assertion Markup Language (SAML) e Single Sign-On di password (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Pianificare la modifica: rimozione dell'indirizzo IP del server multi-factor authentication dall'indirizzo IP di Office 365

**Tipo:** Modifica pianificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

È in corso la rimozione dell'indirizzo IP del server multi-factor authentication dall' [indirizzo IP di Office 365 e dal servizio Web URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Se attualmente si fa affidamento su queste pagine per aggiornare le impostazioni del firewall, è necessario assicurarsi di includere anche l'elenco degli indirizzi IP documentati nella sezione **requisiti del firewall di Azure server multi-factor authentication** della [Guida introduttiva con l'articolo server Multi-Factor Authentication di Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>I token solo app richiedono ora che l'app client esista nel tenant delle risorse

**Tipo:** Correzione  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Il 26 luglio 2019 è stato modificato il modo in cui vengono forniti i token solo app tramite la [concessione delle credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). In precedenza, le app potevano ottenere i token per chiamare altre app, indipendentemente dal fatto che l'app client si trovasse nel tenant. Questo comportamento è stato aggiornato in modo che le risorse a tenant singolo, talvolta chiamate API Web, possano essere chiamate solo da app client presenti nel tenant delle risorse.

Se l'app non si trova nel tenant delle risorse, viene visualizzato un messaggio di errore che indica `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` per risolvere il problema, è necessario creare l'entità servizio dell'app client nel tenant, usando l' [endpoint di consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) o [tramite PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell). il che garantisce al tenant l'autorizzazione dell'app per operare nel tenant.

Per ulteriori informazioni, vedere [What ' s New for Authentication?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Il consenso esistente tra il client e l'API continua a non essere necessario. Le app devono ancora eseguire controlli di autorizzazione personalizzati.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nuovo accesso senza password per Azure AD usando le chiavi di sicurezza FIDO2

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Azure AD i clienti possono ora impostare i criteri per gestire le chiavi di sicurezza FIDO2 per gli utenti e i gruppi dell'organizzazione. Gli utenti finali possono anche registrare autonomamente le proprie chiavi di sicurezza, usare le chiavi per accedere ai propri account Microsoft nei siti Web, mentre nei dispositivi che supportano FIDO, e accedere ai dispositivi Windows 10 aggiunti a Azure AD.

Per altre informazioni, vedere [abilitare l'accesso senza password per Azure ad (anteprima)](/azure/active-directory/authentication/concept-authentication-passwordless) per le informazioni relative all'amministratore e [configurare le informazioni di sicurezza per l'uso di una chiave di sicurezza (anteprima)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) per le informazioni relative all'utente finale.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2019 luglio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2019 luglio sono state aggiunte le 18 nuove app con supporto federativo per la raccolta di app:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright pattern omnicanale Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access per Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [iperanna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Tastierino](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Directory federata](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nuovo tag del servizio Azure AD Domain Services per il gruppo di sicurezza di rete

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Se si è stanchi di gestire lunghi elenchi di indirizzi IP e intervalli, è possibile usare il nuovo tag del servizio di rete **AzureActiveDirectoryDomainServices** nel gruppo di sicurezza di rete di Azure per proteggere il traffico in ingresso verso la Azure ad Domain Services virtuale subnet di rete.

Per ulteriori informazioni su questo nuovo tag di servizio, vedere [gruppi di sicurezza di rete per Azure ad Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Azure AD Domain Services (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Siamo lieti di annunciare il rilascio di Azure AD controllo della sicurezza del servizio del dominio per l'anteprima pubblica. Il controllo della sicurezza consente di fornire informazioni importanti sui servizi di autenticazione tramite lo streaming di eventi di controllo di sicurezza a risorse mirate, tra cui archiviazione di Azure, aree di lavoro di Azure Log Analytics e hub eventi di Azure, usando il servizio del dominio Azure AD Portale.

Per ulteriori informazioni, vedere [Enable Security Audits for Azure ad Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nuovi metodi di autenticazione utilizzo & Insights (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Funzionalità del prodotto:** Monitoraggio e creazione report

I nuovi metodi di autenticazione & i report di Insights consentono di comprendere in che modo le funzionalità come Azure Multi-Factor Authentication e la reimpostazione della password self-service vengono registrate e usate nell'organizzazione, incluso il numero di registrato utenti per ogni funzionalità, frequenza con cui viene usata la reimpostazione della password self-service per reimpostare le password e con quale metodo viene eseguita la reimpostazione.

Per altre informazioni, vedere [metodi di autenticazione utilizzo & Insights (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Sono disponibili nuovi report di sicurezza per tutti gli amministratori di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Tutti gli amministratori Azure AD ora possono selezionare il banner nella parte superiore dei report di sicurezza esistenti, ad esempio il report **utenti contrassegnati per il rischio** , per iniziare a usare la nuova esperienza di sicurezza, come illustrato in **utenti a rischio** e report sugli **accessi a rischio** . Nel corso del tempo, tutti i report sulla sicurezza passano dalle versioni precedenti alle nuove versioni, con i nuovi report che forniscono le funzionalità aggiuntive seguenti:

- Filtro e ordinamento avanzati

- Azioni bulk, ad esempio la dismissing del rischio utente

- Conferma di entità compromesse o sicure

- Stato di rischio, copertura: a rischio, non rilasciata, risolta e confermata compromessa

Per altre informazioni, vedere [report utenti a rischio](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report) e [report sugli accessi a rischio](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Azure AD Domain Services (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Siamo lieti di annunciare il rilascio di Azure AD controllo della sicurezza del servizio del dominio per l'anteprima pubblica. Il controllo della sicurezza consente di fornire informazioni importanti sui servizi di autenticazione tramite lo streaming di eventi di controllo di sicurezza a risorse mirate, tra cui archiviazione di Azure, aree di lavoro di Azure Log Analytics e hub eventi di Azure, usando il servizio del dominio Azure AD Portale.

Per ulteriori informazioni, vedere [Enable Security Audits for Azure ad Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nuova federazione diretta B2B con SAML/WS-Fed (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

La Federazione diretta aiuta a semplificare l'utilizzo dei partner la cui soluzione di gestione delle identità gestita da IT non è Azure AD, usando sistemi di identità che supportano gli standard SAML o WS-Fed. Dopo aver configurato una relazione di federazione diretta con un partner, qualsiasi nuovo utente Guest invitato da tale dominio potrà collaborare con l'utente usando il proprio account aziendale esistente, rendendo l'esperienza utente per i guest più trasparente.

Per ulteriori informazioni, vedere [Federazione diretta con ad FS e provider di terze parti per utenti Guest (anteprima)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Tastierino](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Directory federata](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nuovo controllo per i nomi di gruppi duplicati nel portale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

A questo punto, quando si crea o si aggiorna un nome di gruppo dal portale di Azure AD, verrà eseguito un controllo per verificare se si sta duplicando un nome di gruppo esistente nella risorsa. Se si determina che il nome è già in uso da parte di un altro gruppo, verrà chiesto di modificarne il nome.

Per altre informazioni, vedere [gestire i gruppi nel portale di Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD ora supporta i parametri di query statici negli URI di risposta (Reindirizzamento)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Azure AD app possono ora registrare e usare URI di risposta (Reindirizzamento) con parametri di query statici, ad esempio `https://contoso.com/oauth2?idp=microsoft`, per richieste OAuth 2,0. Il parametro della query statica è soggetto alle stringhe corrispondenti per gli URI di risposta, esattamente come qualsiasi altra parte dell'URI di risposta. Se non è presente alcuna stringa registrata corrispondente all'URI di reindirizzamento con decodifica URL, la richiesta viene rifiutata. Se viene trovato l'URI di risposta, l'intera stringa viene utilizzata per reindirizzare l'utente, incluso il parametro della query statica.

Gli URI di risposta dinamici sono ancora proibiti perché rappresentano un rischio per la sicurezza e non possono essere usati per mantenere le informazioni sullo stato attraverso una richiesta di autenticazione. A questo scopo, usare il parametro `state`.

Attualmente, le schermate di registrazione delle app del portale di Azure continuano a bloccare i parametri della query. Tuttavia, è possibile modificare manualmente il manifesto dell'applicazione per aggiungere e testare i parametri di query nell'app. Per ulteriori informazioni, vedere [What ' s New for Authentication?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>I log attività (API Graph) per Azure AD sono ora disponibili tramite i cmdlet di PowerShell

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare che Azure AD log attività (report di controllo e accesso) sono ora disponibili tramite il modulo di Azure AD PowerShell. In precedenza, era possibile creare script personalizzati usando gli endpoint MS API Graph e ora abbiamo esteso questa funzionalità ai cmdlet di PowerShell.

Per ulteriori informazioni sull'utilizzo di questi cmdlet, vedere [Azure ad cmdlet di PowerShell per la creazione di report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controlli filtro aggiornati per i log di controllo e accesso in Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

I report di controllo e accesso sono stati aggiornati per poter applicare diversi filtri senza doverli aggiungere come colonne nelle schermate dei report. Inoltre, è ora possibile decidere il numero di filtri che si desidera visualizzare sullo schermo. Questi aggiornamenti interagiscono tra loro per semplificare la lettura dei report e l'ambito delle proprie esigenze.

Per altre informazioni su questi aggiornamenti, vedere [filtrare i log di controllo](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) e le [attività di accesso al filtro](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Giugno 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nuova API riskDetections per Microsoft Graph (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Siamo lieti di annunciare che la nuova API riskDetections per Microsoft Graph è ora disponibile in anteprima pubblica. È possibile usare questa nuova API per visualizzare un elenco dei rilevamenti dei rischi di accesso e dell'utente correlati alla protezione delle identità dell'organizzazione. È anche possibile usare questa API per eseguire query in modo più efficiente sui rilevamenti dei rischi, inclusi i dettagli sul tipo di rilevamento, lo stato, il livello e altro ancora.

Per ulteriori informazioni, vedere la [documentazione di riferimento dell'API di rilevamento dei rischi](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-2019 giugno

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel giugno 2019 sono state aggiunte le 22 nuove app con supporto federativo per la raccolta di app:

[Azure ad SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [Anaqua](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [client VPN di Azure](https://portal.azure.com/), [expensein](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)helper, [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz in-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager per Oracle Merchandising al dettaglio, Oracle Access Manager per Oracle E-Business Suite, Oracle IDC per E-Business Suite, Oracle IDC per PeopleSoft, Oracle IDC per JD Edwards

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visualizzare lo stato di avanzamento in tempo reale del servizio di provisioning Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

L'esperienza di provisioning Azure AD è stata aggiornata in modo da includere un nuovo indicatore di stato che mostra la distanza del processo di provisioning degli utenti. Questa esperienza aggiornata fornisce anche informazioni sul numero di utenti di cui è stato effettuato il provisioning durante il ciclo corrente, nonché sul numero di utenti di cui è stato effettuato il provisioning fino a oggi.

Per altre informazioni, vedere [controllare lo stato del provisioning dell'utente](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Le informazioni personalizzate distintive dell'azienda vengono visualizzate nelle schermate di disconnessione e di errore

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Il Azure AD è stato aggiornato in modo che la personalizzazione dell'azienda venga ora visualizzata nelle schermate di disconnessione e di errore, nonché nella pagina di accesso. Non è necessario eseguire alcuna operazione per attivare questa funzionalità, Azure AD usa semplicemente gli asset già configurati nell'area di **personalizzazione della società** del portale di Azure.

Per ulteriori informazioni sulla configurazione della personalizzazione dell'azienda, vedere [la pagina relativa all'aggiunta della personalizzazione alle pagine di Azure Active Directory dell'organizzazione](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Il server Azure Multi-Factor Authentication (multi-factor authentication) non è più disponibile per le nuove distribuzioni

**Tipo:** Deprecato  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

A partire dal 1 ° luglio 2019, Microsoft non offrirà più il server multi-factor authentication per le nuove distribuzioni. I nuovi clienti che vogliono richiedere l'autenticazione a più fattori nella propria organizzazione devono ora usare Azure Multi-Factor Authentication basato sul cloud. I clienti che hanno attivato il server di autenticazione a più fattori prima del 1 ° luglio non vedranno una modifica. Sarà comunque possibile scaricare la versione più recente, ottenere gli aggiornamenti futuri e generare le credenziali di attivazione.

Per altre informazioni, vedere [Introduzione all'server multi-factor authentication di Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Per altre informazioni sui Multi-Factor Authentication di Azure basati sul cloud, vedere [pianificazione di una distribuzione di azure multi-factor authentication basata sul cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maggio 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Modifica del servizio: supporto futuro solo per i protocolli TLS 1,2 nel servizio proxy di applicazione

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Per fornire la crittografia migliore per i clienti, è possibile limitare l'accesso solo ai protocolli TLS 1,2 nel servizio proxy di applicazione. Questa modifica viene implementata gradualmente per i clienti che usano già i protocolli TLS 1,2, pertanto non è possibile visualizzare alcuna modifica.

La deprecazione di TLS 1,0 e TLS 1,1 si verifica il 31 agosto 2019, ma verrà fornita una notifica avanzata aggiuntiva, per cui si avrà il tempo necessario per prepararsi a questa modifica. Per prepararsi a questa modifica, verificare che le combinazioni client-server e server browser, inclusi i client usati dagli utenti per accedere alle app pubblicate tramite il proxy di applicazione, siano aggiornate in modo da usare il protocollo TLS 1,2 per mantenere la connessione all'applicazione Servizio proxy. Per altre informazioni, vedere [aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Usare il report utilizzo e informazioni dettagliate per visualizzare i dati di accesso correlati all'app

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile usare il report Usage and Insights, disponibile nell'area **applicazioni aziendali** del portale di Azure, per ottenere una visualizzazione incentrata sull'applicazione dei dati di accesso, incluse informazioni su:

- App principali usate per la tua organizzazione

- App con gli accessi più non riusciti

- Errori di accesso principali per ogni app

Per altre informazioni su questa funzionalità, vedere [report sull'utilizzo e informazioni dettagliate nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizzare il provisioning degli utenti nelle app cloud usando Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Seguire queste nuove esercitazioni per usare il servizio di provisioning Azure AD per automatizzare la creazione, l'eliminazione e l'aggiornamento degli account utente per le app basate sul cloud seguenti:

- [Del](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

È anche possibile seguire questa nuova [esercitazione su Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), che fornisce informazioni su come eseguire il provisioning di oggetti gruppo.

Per altre informazioni su come proteggere meglio l'organizzazione tramite il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Il Punteggio di sicurezza identità è ora disponibile in Azure AD (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

È ora possibile monitorare e migliorare il comportamento di sicurezza delle identità usando la funzionalità di assegnazione del Punteggio Identity Secure in Azure AD. La funzionalità di assegnazione del Punteggio Identity Secure usa un unico dashboard che consente di:

- Misurare oggettivamente il comportamento di sicurezza delle identità, in base a un punteggio compreso tra 1 e 223.

- Pianificare i miglioramenti della sicurezza delle identità

- Verifica il successo dei miglioramenti della sicurezza

Per ulteriori informazioni sulla funzionalità di assegnazione dei punteggi di sicurezza Identity, vedere la pagina relativa [al Punteggio di identità sicuro in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>È ora disponibile una nuova esperienza Registrazioni app (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** esperienza di sviluppo

La nuova esperienza [registrazioni app](https://aka.ms/appregistrations) è ora disponibile a livello generale. Questa nuova esperienza include tutte le funzionalità chiave con cui si ha familiarità dall'portale di Azure e dal portale di registrazione delle applicazioni e migliora le operazioni tramite:

- **Migliore gestione delle app.** Invece di visualizzare le app in portali diversi, è ora possibile visualizzare tutte le app in un'unica posizione.

- **Registrazione semplificata dell'app.** Dall'esperienza di navigazione migliorata all'esperienza di selezione delle autorizzazioni rinnovata, ora è più semplice registrare e gestire le app.

- **Informazioni più dettagliate.** È possibile trovare altri dettagli sull'app, incluse le guide introduttive e altro ancora.

Per ulteriori informazioni, vedere la pagina relativa alla [piattaforma Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/) e l' [esperienza registrazioni app è ora disponibile a livello generale.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) annuncio del Blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nuove funzionalità disponibili nell'API utenti a rischio per la protezione delle identità

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Siamo lieti di annunciare che è ora possibile usare l'API Users rischiosa per recuperare la cronologia dei rischi degli utenti, eliminare gli utenti a rischio e verificare che gli utenti siano compromessi. Questa modifica consente di aggiornare in modo più efficiente lo stato dei rischi degli utenti e di comprendere la cronologia dei rischi.

Per ulteriori informazioni, vedere la [documentazione di riferimento dell'API utenti rischiosi](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-2019 maggio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2019 maggio sono state aggiunte le 21 nuove app con supporto federativo per la raccolta di app:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [collegamenti reali](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [brasate](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayer](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [Outsystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum ](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial)Area di lavoro, [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [WebMethods API cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Miglioramento della creazione e della gestione dei gruppi nel portale Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Sono stati apportati miglioramenti alle esperienze correlate ai gruppi nel portale di Azure AD. Questi miglioramenti consentono agli amministratori di gestire più facilmente elenchi di gruppi, elenchi di membri e fornire opzioni di creazione aggiuntive.

I miglioramenti includono:

- Filtro di base per tipo di appartenenza e tipo di gruppo.

- Aggiunta di nuove colonne, ad esempio origine e indirizzo di posta elettronica.

- Possibilità di selezionare più gruppi, membri e elenchi di proprietari per un'eliminazione semplificata.

- Possibilità di scegliere un indirizzo di posta elettronica e di aggiungere proprietari durante la creazione del gruppo.

Per altre informazioni, vedere [creare un gruppo di base e aggiungere membri con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurare i criteri di denominazione per i gruppi di Office 365 nel portale di Azure AD (disponibilità generale)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare un criterio di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica consente di applicare convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare i criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi dei gruppi (ad esempio, "CEO, Payroll, HR").

Per altre informazioni, vedere [applicare criteri di denominazione per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Sono ora disponibili endpoint API Microsoft Graph per Azure AD log attività (disponibilità generale)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare la disponibilità generale del supporto per gli endpoint dell'API Microsoft Graph per Azure AD log attività. Con questa versione è ora possibile usare la versione 1,0 dei log di controllo Azure AD, oltre alle API dei log di accesso.

Per altre informazioni, vedere [Azure ad Panoramica dell'API del registro di controllo](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Gli amministratori possono ora usare l'accesso condizionale per il processo di registrazione combinato (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità  

Gli amministratori possono ora creare criteri di accesso condizionale per l'uso nella pagina di registrazione combinata. Ciò include l'applicazione di criteri per consentire la registrazione se:

- Gli utenti si trovano in una rete attendibile.

- Il rischio di accesso degli utenti è basso.

- Gli utenti si trovano su un dispositivo gestito.

- Gli utenti accettano le condizioni per l'utilizzo (TOU) dell'organizzazione.

Per altre informazioni sull'accesso condizionale e sulla reimpostazione della password, vedere il [post di Blog relativo all'accesso condizionale per l'esperienza di registrazione con autenticazione a più fattori e reimpostazione della password di Azure ad](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348) Per ulteriori informazioni sui criteri di accesso condizionale per il processo di registrazione combinato, vedere [criteri di accesso condizionale per la registrazione combinata](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Per ulteriori informazioni sulla funzionalità Azure AD condizioni per l'utilizzo, vedere [Azure Active Directory le condizioni](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)per l'utilizzo.

---

## <a name="april-2019"></a>Aprile 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nuovo Azure AD rilevamento Intelligence per le minacce è ora disponibile come parte di Azure AD Identity Protection

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Azure AD rilevamento Intelligence per le minacce è ora disponibile come parte della funzionalità Azure AD Identity Protection aggiornata. Questa nuova funzionalità consente di indicare un'attività insolita dell'utente per un utente o un'attività specifica, coerente con i modelli di attacco noti basati sulle fonti di intelligence per le minacce interne ed esterne di Microsoft.

Per ulteriori informazioni sulla versione aggiornata di Azure AD Identity Protection, vedere i [quattro miglioramenti principali Azure ad Identity Protection sono ora disponibili in anteprima pubblica](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) Blog e gli [elementi Azure Active Directory Identity Protection (aggiornati)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) articolo. Per ulteriori informazioni sul rilevamento Azure AD Intelligence per le minacce, vedere l'articolo relativo ai [rilevamenti dei rischi Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>È ora disponibile la gestione dei diritti Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Governance delle identità  
**Funzionalità del prodotto:** Governance delle identità

Azure AD la gestione dei diritti, ora disponibile in anteprima pubblica, consente ai clienti di delegare la gestione dei pacchetti di accesso, che definisce il modo in cui i dipendenti e i partner commerciali possono richiedere l'accesso, che devono approvare e il tempo di accesso. I pacchetti di Access possono gestire l'appartenenza a gruppi di Azure AD e Office 365, assegnazioni di ruolo in applicazioni aziendali e assegnazioni di ruolo per i siti di SharePoint Online. Per altre informazioni sulla gestione dei diritti, vedere [Panoramica della gestione dei diritti Azure ad](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Per ulteriori informazioni sull'ampia gamma di funzionalità Azure AD Identity Governance, tra cui Privileged Identity Management, verifiche di accesso e condizioni per l'utilizzo, vedere [che cos'è Azure ad Identity governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurare i criteri di denominazione per i gruppi di Office 365 nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare un criterio di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica consente di applicare convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare i criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi dei gruppi (ad esempio, "CEO, Payroll, HR").

Per altre informazioni, vedere [applicare criteri di denominazione per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>I log attività di Azure AD sono ora disponibili in monitoraggio di Azure (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Per risolvere i commenti e i suggerimenti sulle visualizzazioni con i log attività Azure AD, viene introdotta una nuova funzionalità Insights in Log Analytics. Questa funzionalità consente di ottenere informazioni approfondite sulle risorse di Azure AD usando i modelli interattivi, denominati cartelle di lavoro. Queste cartelle di lavoro predefinite possono fornire dettagli per app o utenti e includono:

- **Accessi.** Fornisce informazioni dettagliate per app e utenti, tra cui la posizione di accesso, il sistema operativo in uso o il client e la versione del browser e il numero di accessi riusciti o non riusciti.

- **Autenticazione legacy e accesso condizionale.** Fornisce informazioni dettagliate per le app e gli utenti che usano l'autenticazione legacy, incluso Multi-Factor Authentication l'uso attivato dai criteri di accesso condizionale, le app che usano i criteri di accesso condizionale e così via.

- **Analisi degli errori di accesso.** Consente di determinare se gli errori di accesso si verificano a causa di un'azione dell'utente, dei problemi relativi ai criteri o dell'infrastruttura.

- **Report personalizzati.** È possibile creare nuove cartelle di lavoro o modificare quelle esistenti per personalizzare la funzionalità Insights per l'organizzazione.

Per ulteriori informazioni, vedere [come utilizzare le cartelle di lavoro di monitoraggio di Azure per Azure Active Directory report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-aprile 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2019 aprile sono state aggiunte le 21 nuove app con supporto federativo per la raccolta di app:

[SAP fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [percolace](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [benching](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), EduBrite [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [rstudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [alibaba cloud (basato su ruoli SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [indiggo](https://indiggolead.com/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nuova opzione Frequency revisioni di accesso e selezione di più ruoli

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** verifiche di accesso  
**Funzionalità del prodotto:** Governance delle identità

I nuovi aggiornamenti in Azure AD le verifiche di accesso consentono di:

- Modificare la frequenza delle verifiche di accesso in modo **semestrale**, oltre alle opzioni precedentemente esistenti di ogni settimana, ogni mese, ogni trimestre e ogni anno.

- Quando si crea una singola verifica di accesso, selezionare più Azure AD e ruoli delle risorse di Azure. In questa situazione, tutti i ruoli vengono configurati con le stesse impostazioni e tutti i revisori ricevono una notifica allo stesso tempo.

Per altre informazioni su come creare una verifica di accesso, vedere [creare una verifica di accesso di gruppi o applicazioni in Azure ad verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect i sistemi di avvisi tramite posta elettronica sono in fase di transizione e inviano nuove informazioni sul mittente di posta elettronica per alcuni clienti

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma

Azure AD Connect è in corso la transizione dei sistemi di avviso di posta elettronica, potenzialmente mostrando ad alcuni clienti un nuovo mittente di posta elettronica. Per risolvere questo problema, è necessario aggiungere `azure-noreply@microsoft.com` all'elenco Consenti dell'organizzazione oppure non sarà possibile continuare a ricevere avvisi importanti dai servizi di Office 365, Azure o Sync.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Le modifiche del suffisso UPN hanno ora esito positivo tra domini federati in Azure AD Connect

**Tipo:** Correzione  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma

È ora possibile modificare correttamente il suffisso UPN di un utente da un dominio federato a un altro dominio federato in Azure AD Connect. Questa correzione significa che non si dovrebbe più riscontrare il messaggio di errore FederatedDomainChangeError durante il ciclo di sincronizzazione o ricevere un messaggio di posta elettronica di notifica, "Impossibile aggiornare questo oggetto in Azure Active Directory, perché l'attributo [ FederatedUser. UserPrincipalName], non è valido. Aggiornare il valore nei servizi directory locale ".

Per ulteriori informazioni, vedere [risoluzione degli errori durante la sincronizzazione](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maggiore sicurezza usando i criteri di accesso condizionale basato sulla protezione delle app in Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

L'accesso condizionale basato sulla protezione delle app è ora disponibile usando il Richiedi criterio di **protezione delle app** . Questo nuovo criterio consente di aumentare la sicurezza dell'organizzazione evitando:

- Gli utenti ottengono l'accesso alle app senza una licenza Microsoft Intune.

- Gli utenti non sono in grado di ottenere i criteri di protezione delle app Microsoft Intune.

- Utenti che accedono alle app senza un criterio di protezione delle app configurato Microsoft Intune.

Per altre informazioni, vedere [come richiedere criteri di protezione delle app per l'accesso alle app cloud con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nuovo supporto per Azure AD Single Sign-On e l'accesso condizionale in Microsoft Edge (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Il supporto Azure AD per Microsoft Edge è stato migliorato, inclusa la fornitura di un nuovo supporto per Azure AD Single Sign-On e l'accesso condizionale. Se in precedenza è stato usato Microsoft Intune Managed Browser, ora è possibile usare Microsoft Edge.

Per altre informazioni sulla configurazione e la gestione di dispositivi e app usando l'accesso condizionale, vedere [richiedere i dispositivi gestiti per l'accesso alle app cloud con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e [richiedere app client approvate per l'accesso alle app cloud con accesso condizionale ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Per altre informazioni su come gestire l'accesso tramite Microsoft Edge con i criteri di Microsoft Intune, vedere [gestire l'accesso a Internet tramite un browser protetto da criteri Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---
