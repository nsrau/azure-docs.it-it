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
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6a66aefec69a0551f85b11a380c90d1915bd776
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474195"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Ricevere notifiche sulle situazioni in cui visualizzare nuovamente questa pagina per gli aggiornamenti da copiare e incollare questo URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` nella ![icona lettore del feed RSS](./media/whats-new/feed-icon-16x16.png) lettore di feed.

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarli in [Archivio per le novità in Azure Active Directory](whats-new-archive.md).

---

## <a name="may-2019"></a>Maggio 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Modifica del servizio: Supporto futuro per solo i protocolli TLS 1.2 nel servizio Proxy di applicazione

**Tipo:** Modifica prevista  
**Categoria di servizio:** Proxy dell'app  
**Funzionalità del prodotto:** Controllo dell’accesso

Per offrire la crittografia migliori per i nostri clienti, ci limitiamo accesso al solo i protocolli TLS 1.2 nel servizio Proxy di applicazione. Questa modifica è stata gradualmente implementata per i clienti che usano già solo i protocolli TLS 1.2, pertanto non dovrebbe verificarsi tutte le modifiche.

Deprecazione di TLS 1.1 e TLS 1.0 viene effettuato il 31 agosto 2019, ma, verrà inviata una notifica avanzata aggiuntivo, ed è pertanto necessario tempo di prepararsi per la modifica. Per preparare questa modifica, assicurarsi che le combinazioni di client-server e browser e server, inclusi tutti i client utilizzano gli utenti per accedere alle App pubblicate tramite Proxy dell'applicazione, sono stati aggiornati per usare il protocollo TLS 1.2 per mantenere la connessione all'applicazione Servizio proxy. Per altre informazioni, vedere [aggiungere un'applicazione in locale per l'accesso remoto tramite il Proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Usare il report sull'utilizzo e informazioni dettagliate per visualizzare i dati correlati all'app accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile usare il report sull'utilizzo e informazioni dettagliate, che si trova nel **applicazioni aziendali** area del portale di Azure, per ottenere una visualizzazione incentrata sull'applicazione dei dati di accesso, incluse informazioni su:

- Le app di utilizzare Top per l'organizzazione

- App con gli accessi più non riusciti

- Errori di accesso principali per ogni app

Per altre informazioni su questa funzionalità, vedere [rapporto sull'utilizzo e insights nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizzare il provisioning dell'utente per applicazioni cloud usando Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Seguire queste esercitazioni per utilizzare il servizio di Provisioning di Azure AD per automatizzare la creazione, eliminazione e nuova e l'aggiornamento degli account utente per le app basate su cloud seguenti:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

È anche possibile seguire questo nuove [Dropbox esercitazione](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), che fornisce informazioni su come effettuare il provisioning di oggetti di gruppo.

Per altre informazioni su come proteggere l'organizzazione tramite il provisioning degli account utente automatico, vedere [automatizza il provisioning utenti in applicazioni SaaS con Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Punteggio sicuro di identità è ora disponibile in Azure AD (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

È ora possibile monitorare e migliorare la tua identità condizioni di sicurezza usando l'identità secure score funzionalità di Azure AD. L'identità secure score funzionalità Usa un unico dashboard che consentono di:

- Obiettivamente misurano il comportamento di sicurezza di identità basato su un punteggio compreso tra 1 e 223.

- Piano per i miglioramenti di protezione delle identità

- Esaminare l'esito positivo dei miglioramenti della protezione

Per altre informazioni sulla funzionalità di identità sicurezza punteggio, vedere [che cos'è il punteggio sicuro di identità in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nuova esperienza di registrazioni per l'App è ora disponibile (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** esperienza di sviluppo

Il nuovo [registrazioni per l'App](https://aka.ms/appregistrations) esperienza è ora in disponibilità generale. Questa nuova esperienza include tutte le principali funzionalità che si ha familiarità con il portale di Azure e il portale di registrazione dell'applicazione e un miglioramento delle loro tramite:

- **Migliore gestione delle app.** Invece di visualizzare le tue App in diversi portali, è ora possibile visualizzare tutte le App in un'unica posizione.

- **Registrazione dell'app semplificata.** Rispetto all'esperienza di esplorazione migliorata l'esperienza di selezione dell'autorizzazione rinnovata, è ora più semplice registrare e gestire le app.

- **Informazioni più dettagliate.** È possibile trovare altre informazioni sull'app, incluse guide introduttive e altro ancora.

Per altre informazioni, vedere [piattaforma delle identità di Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) e il [esperienza delle registrazioni per l'App è ora disponibile a livello generale.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) annuncio del blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nuove funzionalità disponibili nell'API di utenti a rischio di Identity Protection

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Siamo lieti di annunciare che è ora possibile usare l'API di utenti a rischio per recuperare la cronologia di rischio degli utenti, ignorare gli utenti a rischio e per verificare gli utenti come compromesso. Questa modifica consente di aggiornare lo stato di rischio degli utenti in modo più efficiente e comprendere la cronologia di rischio.

Per altre informazioni, vedere la [documentazione di riferimento API di utenti a rischio](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - maggio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

In maggio 2019, sono state aggiunte che le 21 nuove app con federazione supportano alla raccolta di app:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [reale collega](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Sign semplice](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Sales Marketo coinvolgere](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 globale delle risorse Umane](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [All'area di lavoro di quantum](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [cobalto](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [WebMethod API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Controllo](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Esperienze di gestione e la creazione dei gruppi migliorata nel portale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione di gruppi  
**Funzionalità del prodotto:** Collaborazione

Sono stati apportati miglioramenti per le esperienze basate sui gruppi nel portale di Azure AD. Questi miglioramenti consentono agli amministratori di gestire meglio gli elenchi di gruppi, elenchi di membri e per fornire opzioni aggiuntive per la creazione.

I miglioramenti includono:

- Un filtro di base dal tipo di appartenenza e il tipo di gruppo.

- Aggiunta di nuove colonne, ad esempio indirizzo di posta elettronica e di origine.

- Possibilità di selezioni più gruppi, membri e proprietario sono elencati per semplificarne l'eliminazione.

- Possibilità di scegliere un indirizzo di posta elettronica e i proprietari aggiunti durante la creazione del gruppo.

Per altre informazioni, vedere [creare un gruppo di base e aggiungere i membri che usano Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurare un criterio di denominazione per i gruppi di Office 365 nel portale di Azure AD (disponibilità generale)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione di gruppi  
**Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare criteri di denominazione per i gruppi di Office 365, usando il portale di Azure AD. Questa modifica consente di applicare le convenzioni di denominazione coerente per i gruppi di Office 365 creati o modificati dagli utenti nell'organizzazione.

È possibile configurare criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentiti nei nomi di gruppo (ad esempio "CEO, salari, HR").

Per altre informazioni, vedere [applicare un criterio di denominazione per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Gli endpoint dell'API Graph di Microsoft sono ora disponibili per i log attività di Azure AD (disponibilità generale)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare la disponibilità generale del supporto per gli endpoint API Microsoft Graph per attività di Azure AD i log. Con questa versione, è ora possibile usare versione 1.0 di Azure AD di controllo Registra, nonché i log di accesso API.

Per altre informazioni, vedere [Panoramica di Azure AD audit log API](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Gli amministratori possono ora usare l'accesso condizionale per il processo di registrazione combinata (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità  

Gli amministratori possono ora creare criteri di accesso condizionale per l'utilizzo dalla pagina di registrazione combinato. Ciò include l'applicazione dei criteri per consentire la registrazione se:

- Gli utenti sono in una rete attendibile.

- Gli utenti sono un basso rischio di accesso.

- Gli utenti sono in un dispositivo gestito.

- Gli utenti accettano i termini dell'organizzazione di utilizzo (condizioni).

Per altre informazioni sull'accesso condizionale e la reimpostazione della password, è possibile vedere le [combinato di accesso condizionale per Azure AD MFA e post di blog di esperienza di registrazione di reimpostazione della password](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Per altre informazioni sui criteri di accesso condizionale per il processo di registrazione combinato, vedere [criteri di accesso condizionale per la registrazione combinata](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Per altre informazioni sui termini AD Azure di usare funzionalità, vedere [condizioni per Azure Active Directory utilizza funzionalità](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Aprile 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>Nuovo rilevamento intelligence delle minacce per Azure AD è ora disponibile in Azure AD Identity Protection aggiornato

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Rilevamento di intelligence delle minacce AD Azure è ora disponibile in aggiornato Azure AD Identity Protection. Questa nuova funzionalità consente di indicare attività dell'utente che è insolito per un utente specifico o che sia coerente con i modelli di attacco noti basati su intelligence per le minacce interne ed esterne di Microsoft.

Per altre informazioni sulla versione aggiornata di Azure AD Identity Protection, vedere la [quattro principali miglioramenti di Azure AD Identity Protection sono ora disponibili in anteprima pubblica](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) blog e [che cos'è Azure Active Directory Protezione dell'identità (aggiornata)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) articolo. Per altre informazioni sul rilevamento intelligence delle minacce per Azure AD, vedere la [eventi di rischio di Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) articolo.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Gestione dei diritti di Azure AD è ora disponibile (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Governance  
**Funzionalità del prodotto:** Identity Governance

Gestione dei diritti di Azure AD, ora in anteprima pubblica, aiuta i clienti per delegare la gestione dei pacchetti di accesso, che definisce la modalità dipendenti e partner commerciali possono richiedere l'accesso, chi deve approvare e quanto tempo hanno accesso. I pacchetti di accesso è possono gestire l'appartenenza in Azure AD e Office 365 gruppi, le assegnazioni di ruolo nelle applicazioni aziendali e le assegnazioni di ruolo per i siti di SharePoint Online. Altre informazioni sulla gestione dei diritti di [Panoramica di gestione dei diritti di Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Per altre informazioni sulla gamma di funzionalità di governance delle identità di Azure AD, tra cui Privileged Identity Management, le verifiche di accesso e le condizioni d'uso, vedere [che cos'è Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurare un criterio di denominazione per i gruppi di Office 365 nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione di gruppi  
**Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare criteri di denominazione per i gruppi di Office 365, usando il portale di Azure AD. Questa modifica consente di applicare le convenzioni di denominazione coerente per i gruppi di Office 365 creati o modificati dagli utenti nell'organizzazione.

È possibile configurare criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentiti nei nomi di gruppo (ad esempio "CEO, salari, HR").

Per altre informazioni, vedere [applicare un criterio di denominazione per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>I log attività AD Azure sono ora disponibili in Monitoraggio di Azure (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Per contribuire a gestire i tuoi commenti sulle visualizzazioni con i log attività di Azure AD, verrà introdotta una nuova funzionalità informazioni dettagliate nel Log Analitica. Questa funzionalità consente di ottenere informazioni dettagliate sulle risorse di Azure AD usando i modelli interattivi, denominati cartelle di lavoro. I pre-compilato le cartelle di lavoro possono riportano informazioni dettagliate per le app o gli utenti e includono:

- **Accessi.** Fornisce informazioni dettagliate per le App e gli utenti, compresi posizione di accesso, il sistema operativo in uso o client del browser e versione e il numero di esito positivo o negativo accessi.

- **Accesso condizionale e autenticazione legacy.** Fornisce informazioni dettagliate per le App e gli utenti che usano l'autenticazione legacy, incluso l'utilizzo di multi-Factor Authentication attivata da criteri di accesso condizionale, le app usando criteri di accesso condizionale e così via.

- **Analisi di errore di accesso.** Consente di determinare se gli errori di accesso si verificano a causa di un'azione dell'utente, problemi relativi ai criteri o dell'infrastruttura.

- **Report personalizzati.** È possibile creare un nuovo o modifica di cartelle di lavoro esistenti per personalizzare la funzionalità informazioni dettagliate per l'organizzazione.

Per altre informazioni, vedere [come usare le cartelle di lavoro di monitoraggio di Azure per i report di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - aprile 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

In aprile 2019, sono state aggiunte che le 21 nuove app con federazione supportano alla raccolta di app:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connettere](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel connettersi](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (basata su ruoli SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [netto Certent gestione](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Gestore di certificati Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ Enterprise SurveyMonkey](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Verifiche di accesso nuova opzione di frequenza e la selezione multipla di ruolo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Identity Governance

Le verifiche di accesso di nuovi aggiornamenti in Azure AD consentono di:

- Modifica la frequenza del tuo accesso verifiche al **ogni sei mesi**, oltre alle opzioni esistenti di settimanale, mensile, trimestrale e annuale.

- Selezionare più Azure AD ed esaminare i ruoli delle risorse di Azure durante la creazione di un singolo accesso. In questo caso, tutti i ruoli vengono impostati con le stesse impostazioni e tutti i revisori ricevono una notifica allo stesso tempo.

Per altre informazioni su come creare una verifica di accesso, vedere [creare una verifica di accesso dei gruppi o applicazioni in Azure AD le verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Sistemi di avvisi di Azure AD Connect tramite posta elettronica in corso la transizione, l'invio di nuove informazioni sul mittente di posta elettronica per alcuni clienti

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma

Azure AD Connect è in corso la transizione nostri sistemi di avviso tramite posta elettronica, potenzialmente che mostra alcuni clienti un nuovo mittente di posta elettronica. Per risolvere questo problema, è necessario aggiungere `azure-noreply@microsoft.com` per l'organizzazione elenco Consenti o non sarà in grado di continuare a ricevere avvisi importanti da di Office 365, Azure o i servizi di sincronizzazione.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Modifiche di suffisso UPN ora hanno esito positivo tra domini federati in Azure AD Connect

**Tipo:** Correzione  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma

È possibile ora modificare suffisso UPN dell'utente da un dominio federato a un altro dominio federato in Azure AD Connect. Questa correzione significa che deve essere visualizzato il messaggio di errore FederatedDomainChangeError durante il ciclo di sincronizzazione o ricevere una notifica tramite posta elettronica che indica che non è più "non è possibile aggiornare l'oggetto in Azure Active Directory, perché l'attributo [ FederatedUser.UserPrincipalName], non è valido. Aggiornare il valore nei servizi di directory locale".

Per altre informazioni, vedere [risoluzione dei problemi durante la sincronizzazione](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Una maggiore sicurezza usando i criteri di accesso condizionale basato su protezione app di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Accesso condizionale basato su protezione App è ora disponibile tramite il **richiedono la protezione delle app** criteri. Il nuovo criterio consente di aumentare la sicurezza dell'organizzazione grazie alla possibilità di impedire che:

- Utenti che accedono alle App senza una licenza di Microsoft Intune.

- Utenti sia in grado di ottenere un criterio di protezione app di Microsoft Intune.

- Utenti che accedono alle App senza un criterio di protezione app di Microsoft Intune configurato.

Per altre informazioni, vedere [come richiedere i criteri di protezione delle app per accedere all'app cloud con l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nuovo supporto per Azure AD l'accesso single sign-on e l'accesso condizionale in Microsoft Edge (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

È stato migliorato il supporto di Azure AD per Microsoft Edge, fornendo un nuovo supporto per Azure AD single sign-on e l'accesso condizionale. Se hai già usato in precedenza Microsoft Intune Managed Browser, è possibile ora usare invece Microsoft Edge.

Per altre informazioni sull'impostazione e gestione di dispositivi e App con accesso condizionale, vedere [richiedono gestiti i dispositivi per accedere all'app cloud con l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e [Richiedi App client per il cloud approvate accesso alle App con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Per altre informazioni su come gestire l'accesso usando Microsoft Edge con i criteri di Microsoft Intune, vedere [gestire un accesso Internet tramite un browser protetto da criteri di Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marzo 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Framework dell'esperienza di identità e criteri personalizzati supporto in Azure Active Directory B2C è ora disponibile (GA)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile creare i criteri personalizzati in Azure AD B2C, incluse le attività seguenti, che sono supportate su larga scala e nel nostro contratto di servizio di Azure:

- Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati.

- Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.

- Definire la diramazione condizionale nei percorsi utente.

- Trasformare ed eseguire il mapping delle attestazioni per prendere decisioni in tempo reale e le comunicazioni.

- Utilizzare i servizi abilitati per API REST nei percorsi utente di autenticazione personalizzato. Ad esempio, con i provider di posta elettronica, durevolezza e sistemi di autorizzazione proprietario.

- Attuare la federazione con provider di identità che sono conformi con il protocollo openid Connect. Ad esempio, con multi-tenant di Azure AD, provider di account basati su social network o provider di verifica a due fattori.

Per altre informazioni sulla creazione di criteri personalizzati, vedere [note degli sviluppatori per i criteri personalizzati in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leggere [post di blog di Alex Simon, inclusi i case study](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - marzo 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

In marzo 2019, sono state aggiunte che queste 14 nuove app con la federazione di supporto per la raccolta di app:

[Delegato di Exchange per dispositivi mobili ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Spiegazione in base al sistema di controllo](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool prestazioni questioni](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit orizzonti](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [VITÀ](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nuovo Zscaler e Atlassian nella raccolta di Azure AD - marzo 2019 i connettori di provisioning

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Automatizzare la creazione, aggiornamento ed eliminazione di account utente per le app seguenti:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler tre](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Per altre informazioni su come proteggere l'organizzazione tramite il provisioning degli account utente automatico, vedere [automatizza il provisioning utenti in applicazioni SaaS con Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Ripristinare e gestire i gruppi di Office 365 eliminati nel portale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione di gruppi  
**Funzionalità del prodotto:** Collaborazione

È ora possibile visualizzare e gestire i gruppi di Office 365 eliminati dal portale di Azure AD. Questa modifica consente di visualizzare i gruppi a cui sono disponibili per il ripristino, insieme a cui è possibile in modo permanente, eliminare tutti i gruppi necessari dall'organizzazione.

Per altre informazioni, vedere [ripristino scaduto o i gruppi eliminati](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Accesso Single sign-on è ora disponibile per le app protette con Azure AD SAML in locale tramite il Proxy di applicazione (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy dell'app  
**Funzionalità del prodotto:** Controllo dell’accesso

È ora possibile fornire un'esperienza single sign-on (SSO) in locale, le app con autenticazione SAML, oltre all'accesso remoto a queste App tramite il Proxy di applicazione. Per altre informazioni sull'impostazione di SAML SSO con le App in locale, vedere [SAML single sign-on per applicazioni locali con il Proxy di applicazione (anteprima)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Le app client in cicli di richiesta verranno interrotto per migliorare l'affidabilità e l'esperienza utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione dell'utente

Le app client in modo non corretto possono rilasciare centinaia di richieste di accesso stesso in un breve periodo di tempo. Queste richieste, sia che si ha esito positivo o non, contribuiscono a un'esperienza utente misera e cogliere nuove opportunità di carichi di lavoro per il provider di identità, aumentare la latenza per tutti gli utenti e ridurre la disponibilità del provider di identità.

Questo aggiornamento invia un' `invalid_grant` errori: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` alle App client che inviano richieste di duplicazione più volte in un breve periodo di tempo, esula dall'ambito del normale funzionamento. Le app client che si verificano questo problema dovrebbe essere visualizzato un prompt interattivo, richiedendo all'utente di accedere di nuovo. Per altre informazioni su questa modifica e su come correggere l'app se si verifica questo errore, vedere [nuove funzionalità per l'autenticazione?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nuova esperienza utente i log di controllo è ora disponibile

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Abbiamo creato un nuovo Azure AD **log di controllo** pagina per contribuire a migliorare la leggibilità e come cercare le informazioni. Per vedere le nuove **log di controllo** pagina, selezionare **log di controllo** nel **attività** sezione di Azure Active Directory.

![Nuova pagina di log di controllo, con informazioni di esempio](media/whats-new/audit-logs-page.png)

Per altre informazioni sulla nuova **log di controllo** pagina, vedere [report di controllo attività nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nuovi avvisi e indicazioni per consentire di evitare il blocco amministratore accidentale da criteri di accesso condizionale configurati in modo errati

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per evitare che gli amministratori che bloccano accidentalmente i propri tenant mediante criteri di accesso condizionale configurati in modo errati, abbiamo creato indicazioni aggiornate e nuovi avvisi nel portale di Azure. Per altre informazioni sulle nuove informazioni, vedere [quali sono le dipendenze dei servizi nell'accesso condizionale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Condizioni per l'utente finale migliorata delle esperienze di utilizzo nei dispositivi mobili

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance

Sono state aggiornate le condizioni esistenti delle esperienze di utilizzo per contribuire a migliorare come rivedere e fornire il consenso alle condizioni d'uso in un dispositivo mobile. È ora possibile eseguire lo zoom avanti e indietro, tornare indietro, scaricare le informazioni e selezionare i collegamenti ipertestuali. Per altre informazioni sui termini di utilizzo aggiornati, vedere [condizioni per Azure Active Directory utilizza funzionalità](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nuovi log attività di Azure AD di download disponibili

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile scaricare grandi quantità di log attività direttamente dal portale di Azure. Questo aggiornamento consente di:

- Scaricare fino a 250.000 righe.

- Ricevi una notifica al completamento del download.

- Personalizzare il nome del file.

- Determinare il formato di output, JSON o CSV.

Per altre informazioni su questa funzionalità, vedere [Guida introduttiva: Scaricare un report di controllo nel portale di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Modifica che causa un'interruzione: Aggiornamenti alla valutazione della condizione da Exchange ActiveSync (EAS)

**Tipo:** Modifica prevista  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Controllo dell’accesso

È in corso l'aggiornamento come Exchange ActiveSync (EAS) valuta le condizioni seguenti:

- Posizione dell'utente, basato sul paese, area o indirizzo IP

- Rischio di accesso

- Piattaforma del dispositivo.

Se è stata usata in precedenza queste condizioni nei criteri di accesso condizionale, tenere presente che il comportamento di condizione potrebbero subire modifiche. Ad esempio, se la condizione di percorso utente è stato usato in precedenza in un criterio, si noterà il criterio ora verrà ignorato in base alla posizione dell'utente.

---

## <a name="february-2019"></a>Febbraio 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Crittografia token configurabile SAML in Azure AD (anteprima pubblica) 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

È ora possibile configurare qualsiasi app SAML supportati per ricevere i token SAML crittografati. Quando configurato e usato con un'app, Azure AD consente di crittografare le asserzioni SAML generate usando una chiave pubblica ottenuta da un certificato archiviato in Azure AD.

Per altre informazioni sulla configurazione di crittografia di token SAML, vedere [crittografia di token SAML in Azure AD configurare](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Creare una verifica di accesso per gruppi o le app usando le verifiche di accesso di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance

È ora possibile includere più gruppi o le App in un singolo di Azure AD di accedere alle revisione per l'appartenenza al gruppo o l'assegnazione di app. Verifiche di accesso con più gruppi o le app vengono configurate usando le stesse impostazioni e tutti i revisori inclusi vengono informati allo stesso tempo.

Per altre informazioni su come creare una verifica di accesso usando le verifiche di accesso di Azure AD, vedere [creare una verifica di accesso di gruppi o applicazioni nelle verifiche di accesso di Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - febbraio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel febbraio 2019, sono state aggiunte che supportano questi 27 nuove app con la federazione alla raccolta di app:

[Passport Euromonitor](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [accidentali](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Qmlativ Skyward](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [SSO Soloinsight CloudGate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Autorizzazione clic [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [radicali ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Condivisione un sogno](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [WebMethod Cloud Integration](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge ovunque LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [dati Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [portale Netop](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud da Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp piattaforma di produttività](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Migliorata la registrazione MFA/reimpostazione combinata

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione della password self-service  
**Funzionalità del prodotto:** Autenticazione dell'utente

In risposta ai commenti dei clienti, è stato migliorato l'esperienza di anteprima di registrazione MFA/reimpostazione combinato, consentendo agli utenti di registrare più rapidamente le tue info di sicurezza per MFA e per SSPR. 

**Per attivare l'esperienza avanzata per gli utenti oggi, seguire questa procedura:**

1. Come amministratore globale o amministratore dell'utente, accedere al portale di Azure e passare a **Azure Active Directory > impostazioni utente > gestire le impostazioni per le funzionalità di anteprima di Pannello di accesso**. 

2. Nel **gli utenti che possono usare l'anteprima funzionalità per la registrazione e la gestione delle tue info di sicurezza: aggiornare** opzione, scegliere di attivare le funzionalità per un **gruppo selezionato di utenti** o per **tutti gli utenti** .

Nelle poche settimane, verrà rimosso la possibilità di attivare il vecchio combinato MFA/reimpostazione anteprima esperienza di registrazione per i tenant che ha già attivato.

**Per vedere se il controllo verrà rimossa per il tenant, seguire questa procedura:**

1. Come amministratore globale o amministratore dell'utente, accedere al portale di Azure e passare a **Azure Active Directory > impostazioni utente > gestire le impostazioni per le funzionalità di anteprima di Pannello di accesso**.  

2. Se il **utenti che possono usare le funzionalità di anteprima per la registrazione e la gestione delle tue info di sicurezza** opzione è impostata su **None**, l'opzione verrà rimosso dal tenant.

Indipendentemente dal fatto se ha attivato in precedenza la precedente registrazione MFA/reimpostazione combinata funzionalità di anteprima per gli utenti o non, verrà disattivata la vecchia esperienza in una data futura. Per questo motivo, è consigliabile che passare alla nuova e migliorata esperienza appena possibile.

Per altre informazioni sull'esperienza di registrazione avanzata, vedere la [miglioramenti ad accesso sporadico ad Azure AD combinati autenticazione a più fattori e l'esperienza di registrazione di reimpostazione della password](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Esperienza di gestione dei criteri aggiornati per i flussi utente

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

Abbiamo aggiornato il processo di creazione e la gestione dei criteri per i flussi degli utenti (in precedenza incorporati, noti come criteri) più semplice. Questa nuova esperienza è ora il valore predefinito per tutti i tenant di Azure AD.

È possibile fornire altri commenti e suggerimenti usando smile o Invia faccia imbronciata icone nel **inviare commenti e suggerimenti** area nella parte superiore della schermata del portale.

Per altre informazioni sulla nuova esperienza di gestione dei criteri, vedere la [Azure AD B2C dispone ora di personalizzazione di JavaScript e molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Scegliere le versioni di elemento pagina specifica fornite da Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile scegliere una versione specifica di elementi della pagina fornite da Azure AD B2C. Se si seleziona una versione specifica, è possibile testare gli aggiornamenti prima vengono visualizzati in una pagina ed è possibile ottenere un comportamento prevedibile. Inoltre, è possibile ora acconsentire per applicare versioni pagina specifica per consentire le personalizzazioni di JavaScript. Per attivare questa funzionalità, vedere la **proprietà** pagina nei flussi utente.

Per altre informazioni sulla scelta di versioni specifiche di elementi della pagina, vedere la [Azure AD B2C dispone ora di personalizzazione di JavaScript e molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisiti di password dell'utente finale può essere configurato per B2C (GA)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile impostare la complessità delle password nell'organizzazione per gli utenti finali, anziché utilizzare il nativo dei criteri password di Azure AD. Dal **delle proprietà** pannello dell'utente di flussi (precedentemente noti come i criteri predefiniti), è possibile scegliere la complessità delle password di **semplice** o **sicuro**, oppure è possibile creare un **Custom** set di requisiti.

Per altre informazioni sulla configurazione del requisito complessità password, vedere [configurare i requisiti di complessità delle password in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nuovi modelli predefiniti per le esperienze di autenticazione personalizzato personalizzato

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È possibile usare i nuovi modelli predefiniti, che si trova nel **il layout di pagina** pannello dei flussi utente (precedentemente noto come criteri predefiniti), per creare un oggetto personalizzato con marchio di esperienza di autenticazione per gli utenti.

Per altre informazioni sull'uso dei modelli, vedere [Azure AD B2C dispone ora di personalizzazione di JavaScript e molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Gennaio 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Collaborazione B2B di Active Directory tramite l'autenticazione con passcode monouso (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

È stata introdotta l'autenticazione con passcode monouso (OTP) per autenticare gli utenti guest B2B che non possono essere autenticati tramite altri mezzi quali Azure AD, un account Microsoft (account del servizio gestito) o la federazione di Google. Con questo nuovo metodo di autenticazione, gli utenti guest non devono creare un nuovo account Microsoft. Durante il riscatto di un invito o l'accesso a una risorsa condivisa, un utente guest può invece richiedere l'invio di un codice temporaneo a un indirizzo di posta elettronica. Usando questo codice temporaneo, l'utente guest può continuare ad accedere.

Per altre informazioni, vedere [Autenticazione con passcode monouso tramite indirizzo di posta elettronica (anteprima)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e il blog [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (Azure AD semplifica le condivisioni e le collaborazioni per qualsiasi utente con qualsiasi account).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nuove impostazioni dei cookie di Azure AD Application Proxy

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy dell'app  
**Funzionalità del prodotto:** Controllo dell’accesso

Sono state introdotte tre nuove impostazioni dei cookie, disponibili per le app pubblicate tramite il proxy di applicazione:

- **Usa un cookie di tipo solo HTTP.** Consente di impostare il flag **HTTPOnly** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, tra cui la possibilità di evitare la copia o la modifica dei cookie tramite script lato client. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Usa un cookie sicuro.** Consente di impostare il flag **Secure** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, assicurando che i cookie vengano trasmessi solo su canali TLS sicuri, ad esempio HTTPS. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Usa un cookie permanente.** Consente di impedire la scadenza dei cookie di accesso alla chiusura del Web browser. Questi cookie rimangono validi per l'intera durata del token di accesso. I cookie vengono tuttavia reimpostati se viene raggiunta la scadenza o se l'utente elimina manualmente il cookie. È consigliabile mantenere l'impostazione predefinita **No**, attivando l'impostazione solo per app meno recenti che non condividono cookie tra i processi.

Per altre informazioni sui nuovi cookie, vedere [Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Gennaio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di gennaio 2019, alla raccolta di app sono state aggiunte queste 35 nuove app con il supporto della federazione:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent tavolozza](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco ombrello](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Amministratore di accesso Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [promemoria di scadenza](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Visualizzatore](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [verbo](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital chiudere](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Sistema SSO GTNexus](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Are for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 per Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn portare ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas piattaforma](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nuovi miglioramenti ad Azure AD Identity Protection (anteprima pubblica)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

All'offerta di anteprima pubblica di Azure AD Identity Protection sono stati aggiunti i miglioramenti seguenti:

- Interfaccia utente aggiornata e più integrata

- API aggiuntive

- Valutazione migliorata dei rischi tramite Machine Learning

- Allineamento a livello di prodotto per utenti e accessi rischiosi

Per altre informazioni sui miglioramenti, vedere [Informazioni su Azure Active Directory Identity Protection (versione aggiornata)](https://aka.ms/IdentityProtectionDocs). È possibile ottenere informazioni aggiuntive e condividere le proprie opinioni tramite i prompt nel prodotto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nuova funzionalità di blocco dell'app per l'app Microsoft Authenticator in dispositivi iOS e Android

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App Microsoft Authenticator  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per rendere più sicuri i passcode monouso, le informazioni sull'app e le impostazioni dell'app, è possibile attivare la funzionalità di blocco dell'app nell'app Microsoft Authenticator. Attivando il blocco dell'app verrà chiesto di eseguire l'autenticazione usando il PIN o la biometria ogni volta che si apre l'app Microsoft Authenticator.

Per altre informazioni, vedere [Domande frequenti sull'app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Miglioramenti alle funzionalità di esportazione di Azure AD Privileged Identity Management (PIM)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management

Gli amministratori di Privileged Identity Management (PIM) possono ora esportare tutte le assegnazioni di ruolo attive e idonee per una risorsa specifica, incluse le assegnazioni di ruolo per tutte le risorse figlio. In precedenza risultava difficile per gli amministratori ottenere un elenco completo di assegnazioni di ruolo per una sottoscrizione ed era necessario esportare le assegnazioni di ruolo per ogni risorsa specifica.

Per altre informazioni, vedere [Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Novembre/Dicembre 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Gli utenti non rimossi dall'ambito di sincronizzazione non passano più ad account di tipo solo cloud

**Tipo:** Correzione  
**Categoria di servizio:** User Management  
**Funzionalità del prodotto:** Directory

>[!Important]
>Microsoft ha ascoltato e compreso i commenti di insoddisfazione dei clienti in merito a questa correzione. Ha quindi ripristinato lo stato antecedente alla modifica finché non sarà più facile per i clienti implementare questa correzione nella loro organizzazione.

È stato risolto un bug a causa del quale il flag DirSyncEnabled di un utente viene erroneamente impostato su **False** quando l'oggetto Active Directory Domain Services viene escluso dall'ambito di sincronizzazione e quindi spostato nel Cestino in Azure AD durante il ciclo di sincronizzazione successivo. In seguito a questa correzione, se l'utente viene escluso dall'ambito di sincronizzazione e successivamente ripristinato dal Cestino di Azure AD, l'account utente resta sincronizzato da AD locale come previsto e non può essere gestito nel cloud poiché la relativa origine di autorità rimane AD locale.

Prima di questa correzione si verificava un problema quando il flag DirSyncEnabled veniva impostato su False. Veniva data l'erronea l'impressione che questi account fossero stati convertiti in oggetti solo cloud e potessero essere gestiti solo nel cloud. Tuttavia, gli account mantenevano l'origine di autorità come locale e tutte le proprietà sincronizzate (attributi shadow) provenienti da AD locale. Questa condizione causava vari problemi in Azure AD e in altri carichi di lavoro cloud, ad esempio Exchange Online, che prevedevano di gestire questi account come sincronizzati da AD, ma che si comportavano ora come account solo cloud.

Attualmente, l'unico modo per convertire realmente un account sincronizzato da AD in un account solo cloud è disabilitare DirSync a livello di tenant, attivando così un'operazione di back-end per trasferire l'origine di autorità. Questo tipo di modifica dell'origine di autorità richiede, tra l'altro, la pulizia di tutti gli attributi locali correlati (ad esempio LastDirSyncTime e gli attributi shadow) e l'invio di un segnale ad altri carichi di lavoro cloud perché convertano anch'essi il rispettivo oggetto in un account solo cloud.

Questa correzione, di conseguenza, consente di evitare gli aggiornamenti diretti dell'attributo ImmutableID di un utente sincronizzato da AD, che in precedenza erano necessari in alcuni scenari. Per impostazione predefinita, l'attributo ImmutableID di un oggetto in Azure AD, come suggerisce il nome, deve essere immutabile. Per questi scenari sono disponibili nuove funzionalità implementate in Azure AD Connect Health e nel client di sincronizzazione di Azure AD Connect:

- **Aggiornamento di ImmutableID su larga scala per molti utenti in un approccio a fasi**
  
  Ad esempio, è necessario eseguire una lunga migrazione tra foreste di Active Directory Domain Services. Soluzione: Usare Azure AD Connect per **Configurare l'ancoraggio di origine** e, quando viene completata la migrazione dell'utente, copiare i valori di ImmutableID esistenti da Azure AD nell'attributo ms-DS-Consistency-Guid dell'utente di Active Directory Domain Services locale della nuova foresta. Per altre informazioni, vedere [Uso di ms-DS-ConsistencyGuid come sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Aggiornamenti di ImmutableID su larga scala per molti utenti in un unico passaggio**

  Ad esempio, se durante l'implementazione di Azure AD Connect si commette un errore ed è necessario modificare l'attributo SourceAnchor. Soluzione: Disabilitare DirSync a livello di tenant e cancellare tutti i valori di ImmutableID non validi. Per altre informazioni, vedere [Disabilitare la sincronizzazione della directory per Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Abbinare di nuovo un utente locale e un utente esistente in Azure AD** Ad esempio, un utente che è stato ricreato in Active Directory Domain Services genera un duplicato nell'account Azure AD anziché essere abbinato di nuovo a un account Azure AD esistente (oggetto orfano). Soluzione: Usare Azure AD Connect Health nel portale di Azure per eseguire di nuovo il mapping di ancoraggio di origine o ImmutableID. Per altre informazioni, vedere [Scenario con oggetto orfano](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Modifica di rilievo: aggiornamenti apportati allo schema dei log di controllo e di accesso tramite Monitoraggio di Azure

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione di report

Sono attualmente in fase di pubblicazione flussi di log di controllo e accesso tramite Monitoraggio di Azure, per poter integrare uniformemente i file di log con gli strumenti di informazioni di sicurezza e gestione degli eventi o con Log Analytics. In base ai commenti e suggerimenti degli utenti e in preparazione all'annuncio della disponibilità generale di questa funzionalità, Microsoft sta apportando le modifiche seguenti allo schema. Queste modifiche dello schema e i relativi aggiornamenti alla documentazione verranno completati entro la prima settimana di gennaio.

#### <a name="new-fields-in-the-audit-schema"></a>Nuovi campi nello schema di controllo
Sta per essere aggiunto il nuovo campo **Tipo di operazione**, che permetterà di specificare il tipo di operazione eseguita sulla risorsa. Ad esempio, **aggiunta**, **aggiornamento** o **eliminazione**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campi modificati nello schema di controllo
È in corso la modifica dei campi seguenti nello schema di controllo:

|Nome campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|Category|Campo **Nome servizio** nelle versioni precedenti. Si tratta ora del campo **Audit Categories** (Categorie di controllo). Il campo **Nome del servizio** è stato rinominato in **loggedByService**.|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione password self-service</li></ul>|<ul><li>User Management</li><li>Gestione di gruppi</li><li>Gestione app</li></ul>|
|targetResources|Include **TargetResourceType** al livello superiore.|&nbsp;|<ul><li>Policy</li><li>App</li><li>Utente</li><li>Group</li></ul>|
|loggedByService|Specifica il nome del servizio che ha generato il log di controllo.|Null|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione della password self-service</li></ul>|
|Risultato|Indica il risultato dei log di controllo. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li></ul>|<ul><li>Riuscito</li><li>Esito negativo</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campi modificati nello schema di accesso
È in corso la modifica dei campi seguenti nello schema di accesso:

|Nome campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Campo **conditionalaccessPolicies** nelle versioni precedenti. Si tratta ora del campo **appliedConditionalAccessPolicies**.|Nessuna modifica|Nessuna modifica|
|conditionalAccessStatus|Indica il risultato dello stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Riuscito</li><li>Esito negativo</li><li>Non applicato</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: risultato|Indica il risultato del singolo stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Riuscito</li><li>Esito negativo</li><li>Non applicato</li><li>Disabled</li></ul>|

Per altre informazioni sullo schema, vedere [Interpretare lo schema dei log di controllo di Azure AD in Monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Miglioramenti relativi a Identity Protection apportati al modello di apprendimento automatico supervisionato e al motore dei punteggi di rischio

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Punteggi di rischio

I miglioramenti apportati al motore di valutazione dei punteggi di rischio utente e di accesso in relazione a Identity Protection ottimizzano la copertura e la precisione del rischio utente. Gli amministratori potrebbero notare che il livello di rischio utente non è più direttamente collegato al livello di rischio di rilevamenti specifici e che vi è stato un aumento nel numero e nel livello di eventi di accesso rischiosi.

I rilevamenti del rischio vengono ora valutati dal modello di apprendimento automatico supervisionato, che calcola il rischio utente tramite funzionalità aggiuntive degli accessi dell'utente e un modello dei rilevamenti. Attraverso questo modello l'amministratore può individuare gli utenti con punteggi di rischio elevato, anche se i rilevamenti associati all'utente indicano un rischio basso o medio. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Possibilità per gli amministratori di reimpostare la propria password tramite l'app Microsoft Authenticator (anteprima pubblica)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione della password self-service  
**Funzionalità del prodotto:** Autenticazione dell'utente

Gli amministratori di Azure AD possono ora reimpostare la propria password seguendo le notifiche dell'app Microsoft Authenticator o tramite un codice da qualsiasi app di autenticazione per dispositivi mobili o token hardware. Per reimpostare la propria password, gli amministratori possono ora usare due metodi diversi:

- Notifica dell'app Microsoft Authenticator

- Altro codice di token hardware/app di autenticazione per dispositivi mobili

- Email

- Chiamata telefonica

- SMS

Per altre informazioni sull'uso dell'app Microsoft Authenticator per reimpostare le password, vedere [Reimpostazione della password self-service di Azure AD - App per dispositivi mobili e SSPR (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nuovo ruolo Amministratore dispositivo cloud di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Funzionalità del prodotto:** Controllo di accesso

Gli amministratori possono assegnare agli utenti il nuovo ruolo Amministratore dispositivo cloud per l'esecuzione di attività di amministrazione dei dispositivi cloud. Gli utenti assegnati al ruolo Amministratore dispositivo cloud possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure.

Per altre informazioni sui ruoli e sulle autorizzazioni, vedere [Assegnazione di ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gestione dei dispositivi tramite il nuovo timestamp di attività in Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi

Ci rendiamo conto che nel corso del tempo è necessario aggiornare e ritirare i dispositivi di organizzazioni in Azure AD, per evitare che i dispositivi non aggiornati nell'ambiente in uso. Per semplificare questo processo, Azure AD aggiorna ora i dispositivi con un nuovo timestamp di attività, che permette di gestirne il ciclo di vita.

Per altre informazioni su come ottenere e usare questo timestamp, vedere [Procedura: Gestire i dispositivi non aggiornati in Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Gli amministratori possono richiedere agli utenti di accettare le condizioni per l'uso in ogni dispositivo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance
 
Gli amministratori possono attivare i **richiedere agli utenti di fornire il consenso in ogni dispositivo** opzione per richiedere agli utenti di accettare le condizioni d'uso in ogni dispositivo in uso nel tenant.

Per altre informazioni, vedere la [condizioni per ogni dispositivo della sezione di utilizzo delle condizioni di Azure Active Directory della funzionalità di uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Gli amministratori possono configurare le condizioni d'uso scada in base a una pianificazione ricorrente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance
 

Gli amministratori possono attivare i **scadono consensi** opzione per rendere le condizioni d'uso scadono per tutti gli utenti in base alla pianificazione di ricorrenza specificata. La pianificazione può essere annuale, biennale, trimestrale o mensile. Quando le condizioni d'uso scadono, gli utenti devono accettare.

Per altre informazioni, vedere la [aggiungere condizioni per l'uso sezione delle condizioni di Azure Active Directory della funzionalità di uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Gli amministratori possono configurare le condizioni d'uso scada in base a pianificazione ciascun utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance

Gli amministratori possono ora specificare un periodo di tempo che l'utente deve accettare le condizioni per l'uso. Ad esempio, gli amministratori possono specificare che gli utenti devono riaccettino condizioni d'uso ogni 90 giorni.

Per altre informazioni, vedere la [aggiungere condizioni per l'uso sezione delle condizioni di Azure Active Directory della funzionalità di uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nuovi messaggi di posta elettronica di Azure AD Privileged Identity Management (PIM) per ruoli di Azure Active Directory

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
I clienti che usano Azure AD Privileged Identity Management (PIM) possono ora ricevere un messaggio di posta elettronica di riepilogo settimanale, che include le informazioni seguenti per gli ultimi sette giorni:

- Panoramica delle assegnazioni di ruoli permanenti e idonei principali

- Numero di utenti che attivano ruoli

- Numero di utenti assegnati a ruoli in PIM

- Numero di utenti assegnati a ruoli al di fuori di PIM

- Numero di utenti "resi permanenti" in PIM

Per altre informazioni su PIM e sulle notifiche di posta elettronica disponibili, vedere [Notifiche tramite posta elettronica in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licenze basate sui gruppi ora disponibili a livello generale

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altri  
**Funzionalità del prodotto:** Directory

Le licenze basate sui gruppi sono ora in anteprima pubblica e disponibili a livello generale. Nell'ambito della disponibilità generale questa funzionalità è stata resa più scalabile e sono state aggiunte la possibilità di rielaborare assegnazioni di licenze basate sui gruppi per un singolo utente e quella di usare licenze basate sui gruppi con licenze di Office 365 E3/A3.

Per altre informazioni sulle licenze basate sui gruppi, vedere [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Novembre 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti
 
Nel mese di novembre 2018 sono state aggiunte alla raccolta di app queste 26 nuove app con supporto per la federazione:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps - Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps - UX](https://cloud.plex.com/sso), [Plex Apps - IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---