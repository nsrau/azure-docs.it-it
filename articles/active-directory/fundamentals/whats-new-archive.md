---
title: Per informazioni sulle novità di Azure Active Directory, | Microsoft Docs
description: Le novità delle note sulla versione nella sezione Panoramica di questo insieme di contenuti includono 6 mesi di attività. Dopo 6 mesi, gli elementi vengono rimossi dall'articolo principale e inseriti in questo articolo di archivio.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: b117309a3ccc9893890d82c2d0b0d2cb0c6e7bca
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197881"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Per informazioni sulle novità di Azure Active Directory,

Il primo articolo sulle [novità di Azure Active Directory? note sulla versione](whats-new.md) contiene aggiornamenti per gli ultimi sei mesi, mentre questo articolo contiene tutte le informazioni meno recenti.

Quali sono le novità di Azure Active Directory? le note sulla versione forniscono informazioni su:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

---

## <a name="october-2019"></a>Ottobre 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Deprecazione dell'API identityRiskEvent per i rilevamenti dei rischi Azure AD Identity Protection

**Tipo:** Pianificare la **categoria del servizio** di modifica: **funzionalità del prodotto** Identity Protection: sicurezza delle identità & protezione

In risposta al feedback degli sviluppatori, gli abbonati Azure AD Premium P2 possono ora eseguire query complesse sui dati di rilevamento dei rischi Azure AD Identity Protection usando la nuova API riskDetection per Microsoft Graph. La versione beta dell'API [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) esistente smetterà di restituire i dati circa il **10 gennaio 2020**. Se l'organizzazione usa l'API identityRiskEvent, è necessario passare alla nuova API di riskDetection.

Per ulteriori informazioni sulla nuova API riskDetection, vedere la [documentazione di riferimento dell'API di rilevamento dei rischi](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Supporto del proxy di applicazione per l'attributo navigava sullostesso sito e Chrome 80

**Tipo:** Pianificare la **categoria del servizio** di modifica: **funzionalità del prodotto** proxy app: controllo di accesso

Due settimane prima della versione del browser Chrome 80, si prevede di aggiornare il modo in cui i cookie del proxy applicazione gestiscono l'attributo **navigava sullostesso sito** . Con la versione di Chrome 80, qualsiasi cookie che non specifica l'attributo **navigava sullostesso sito** verrà considerato come se fosse stato impostato su `SameSite=Lax` .

Per evitare potenziali conseguenze negative a causa di questa modifica, stiamo aggiornando l'accesso al proxy di applicazione e i cookie di sessione per:

- Impostando il valore predefinito per l'impostazione **usa cookie sicuro** su **Sì**.

- Impostazione del valore predefinito per l'attributo **navigava sullostesso sito** su **None**.

    >[!NOTE]
    > I cookie di accesso del proxy di applicazione sono sempre stati trasmessi esclusivamente su canali protetti. Queste modifiche si applicano solo ai cookie di sessione.

Per altre informazioni sulle impostazioni dei cookie del proxy di applicazione, vedere [impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Registrazioni app (legacy) e la gestione delle app nel portale di registrazione delle applicazioni (apps.dev.microsoft.com) non è più disponibile

**Tipo:** Pianificare la **categoria del servizio** di modifica: N/una funzionalità del **prodotto:** esperienza dello sviluppatore

Gli utenti con account Azure AD non possono più registrare o gestire le applicazioni tramite il portale di registrazione delle applicazioni (apps.dev.microsoft.com) oppure registrare e gestire le applicazioni nell'esperienza di Registrazioni app (legacy) nel portale di Azure.

Per ulteriori informazioni sulla nuova esperienza di Registrazioni app, vedere l' [registrazioni app nella Guida alla formazione portale di Azure](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Non è più necessario ripetere la registrazione degli utenti durante la migrazione dall'autenticazione a più fattori per utente all'autenticazione a più fattori basata sull'accesso condizionale

**Tipo:** **Categoria di servizio** fisso: **funzionalità del prodotto multi-** Factor Authentication: sicurezza delle identità & protezione

È stato risolto un problema noto per cui gli utenti dovevano ripetere la registrazione se sono stati disabilitati per l'autenticazione a più fattori Multi-Factor Authentication (multi-factor authentication) e quindi abilitati per l'autenticazione a più fattori tramite criteri di accesso condizionale.

Per richiedere agli utenti di ripetere la registrazione, è possibile selezionare l'opzione **necessaria per ripetere la registrazione** dell'autenticazione a più fattori nei metodi di autenticazione dell'utente nel portale di Azure ad. Per altre informazioni sulla migrazione degli utenti dall'autenticazione a più fattori per utente all'autenticazione a più fattori basata sull'accesso condizionale, vedere [convertire gli utenti dall'autenticazione a più fattori per ogni utente in](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)multi-factor authentication based.

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nuove funzionalità per trasformare e inviare attestazioni nel token SAML

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: SSO

Sono state aggiunte funzionalità aggiuntive che consentono di personalizzare e inviare attestazioni nel token SAML. Queste nuove funzionalità includono:

- Funzioni aggiuntive per la trasformazione delle attestazioni che consentono di modificare il valore inviato nell'attestazione.

- Possibilità di applicare più trasformazioni a una singola attestazione.

- Possibilità di specificare l'origine dell'attestazione, in base al tipo di utente e al gruppo a cui appartiene l'utente.

Per informazioni dettagliate su queste nuove funzionalità, ad esempio su come usarle, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nuova pagina di accesso per gli utenti finali in Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (account di accesso) **funzionalità del prodotto:** monitoraggio & Reporting

È stata aggiunta una nuova pagina degli **accessi** ( https://mysignins.microsoft.com) per consentire agli utenti dell'organizzazione di visualizzare la cronologia di accesso recente per verificare eventuali attività insolite. Questa nuova pagina consente agli utenti di vedere:

- Se qualcuno sta provando a indovinare la password.

- Se un utente malintenzionato ha eseguito l'accesso al proprio account e dalla posizione.

- App a cui l'utente malintenzionato ha provato ad accedere.

Per ulteriori informazioni, vedere gli [utenti possono ora verificare la cronologia di accesso per il Blog di attività insolite](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrazione di Azure AD Domain Services (Azure AD DS) dal modello classico a quello Azure Resource Manager reti virtuali

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad Domain Services **funzionalità del prodotto:** Azure ad Domain Services

Per i clienti che si sono bloccati nelle reti virtuali classiche, abbiamo ottime notizie. È ora possibile eseguire una migrazione una sola volta da una rete virtuale classica a una rete virtuale Gestione risorse esistente. Dopo il passaggio alla rete virtuale Gestione risorse, sarà possibile sfruttare le funzionalità aggiuntive e aggiornate, ad esempio i criteri granulari per le password, le notifiche tramite posta elettronica e i log di controllo.

Per altre informazioni, vedere [Preview-migrate Azure ad Domain Services dal modello di rete virtuale classica a gestione risorse](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aggiornamenti al layout del contratto di pagina Azure AD B2C

**Tipo:** Nuova **Categoria servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

Sono state introdotte alcune nuove modifiche alla versione 1.2.0 del contratto di pagina per Azure AD B2C. In questa versione aggiornata è ora possibile controllare l'ordine di caricamento per gli elementi, che può anche essere utile per arrestare lo sfarfallio che si verifica quando viene caricato il foglio di stile (CSS).

Per un elenco completo delle modifiche apportate al contratto di pagina, vedere il log delle modifiche della [versione](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Eseguire l'aggiornamento alla pagina App personali insieme alle nuove aree di lavoro (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app personali: controllo di accesso

È ora possibile personalizzare il modo in cui gli utenti dell'organizzazione visualizzano e accedono alla nuova esperienza di app personali, incluso l'uso della nuova funzionalità aree di lavoro per semplificare la ricerca delle app. La funzionalità nuove aree di lavoro funge da filtro per le app a cui gli utenti dell'organizzazione hanno già accesso.

Per altre informazioni su come implementare la nuova esperienza app personali e creare aree di lavoro, vedere [creare aree di lavoro nel portale app personali (anteprima)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Supporto per il modello di fatturazione mensile attivo basato sull'utente (disponibilità generale)

**Tipo:** Nuova **Categoria servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

Azure AD B2C supporta ora la fatturazione mensile di utenti attivi (MAU). La fatturazione di MAU si basa sul numero di utenti univoci con attività di autenticazione durante un mese di calendario. I clienti esistenti possono passare a questo nuovo metodo di fatturazione in qualsiasi momento.

A partire dal 1 ° novembre 2019, tutti i nuovi clienti verranno addebitati automaticamente utilizzando questo metodo. Questo metodo di fatturazione consente ai clienti di sfruttare i vantaggi in termini di costi e la possibilità di pianificare in anticipo.

Per altre informazioni, vedere [aggiornare il modello di fatturazione di utenti attivi mensili](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-ottobre 2019

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel 2019 ottobre sono state aggiunte le nuove app 35 con supporto federativo per la raccolta di app:

[In caso di crisi-mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [tatto](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [ECornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate-Europe](https://www.hirevue.com/), [coordinata HireVue-USOnly](https://www.hirevue.com/), [HireVue coordinata-US](https://www.hirevue.com/), [WittyParrot Knowledge box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [mail Fortune!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [teamie](https://theteamie.com/), [Velocity per Teams](https://velocity.peakup.org/teams/login) [, SIGNL4, EAB](https://account.signl4.com/manage) [Navigate impl](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/) [,](https://qubie.azurewebsites.net/static/adminTab/authorize.html) indirizzo di [posta elettronica per Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [indirizzo di posta elettronica per Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct) [, ExactCare](https://ihealthnav.com/account/signin) [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Voce di menu sicurezza consolidata nel portale Azure AD

**Tipo:** Categoria del **servizio** funzionalità modificata: funzionalità di protezione delle identità del **prodotto:** sicurezza delle identità & protezione

È ora possibile accedere a tutte le funzionalità di sicurezza Azure AD disponibili dalla nuova voce di menu **sicurezza** e dalla barra di **ricerca** nella portale di Azure. Inoltre, la nuova pagina di destinazione della **sicurezza** , denominata **Security-Getting Started**, fornirà collegamenti alla documentazione pubblica, alle linee guida per la sicurezza e alla guida alla distribuzione.

Il nuovo menu **sicurezza** include:

- Accesso condizionale
- Identity Protection
- Centro sicurezza
- Identity Secure Score
- Metodi di autenticazione
- AMF
- Report di rischio-utenti a rischio, accessi a rischio, rilevamento dei rischi
- E molto altro ancora.

Per ulteriori informazioni, vedere [Security-Getting Started](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Criteri di scadenza dei gruppi di Office 365 migliorati con il rinnovo automatico

**Tipo:** **Categoria servizio** funzionalità modificata: Gestione gruppo **funzionalità:** gestione del ciclo di vita delle identità

I criteri di scadenza dei gruppi di Office 365 sono stati migliorati per rinnovare automaticamente i gruppi che sono attivamente usati dai relativi membri. I gruppi verranno rinnovati in base all'attività dell'utente in tutte le app di Office 365, inclusi Outlook, SharePoint e teams.

Questa funzionalità avanzata consente di ridurre le notifiche di scadenza del gruppo e di garantire che i gruppi attivi continuino a essere disponibili. Se si dispone già di un criterio di scadenza attivo per i gruppi di Office 365, non è necessario eseguire alcuna operazione per attivare la nuova funzionalità.

Per altre informazioni, vedere [configurare i criteri di scadenza per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Esperienza di creazione Azure AD Domain Services (Azure AD DS) aggiornata

**Tipo:** **Categoria servizio** funzionalità modificata: Azure ad Domain Services **funzionalità del prodotto:** Azure ad Domain Services

È stato aggiornato Azure AD Domain Services (Azure AD DS) per includere un'esperienza di creazione nuova e migliorata, che consente di creare un dominio gestito con pochi clic. Inoltre, è ora possibile caricare e distribuire Azure AD DS da un modello.

Per altre informazioni, vedere [esercitazione: creare e configurare un'istanza di Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Settembre 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Piano per la modifica: deprecazione dei pacchetti di contenuto Power BI

**Tipo:** Pianificare la **categoria del servizio** di modifica: Reporting **Product capability:** monitoraggio & Reporting

A partire dal 1 ° ottobre 2019, Power BI inizierà a deprecare tutti i pacchetti di contenuto, incluso il pacchetto di contenuto Azure AD Power BI. In alternativa a questo pacchetto di contenuto, è possibile usare Azure AD cartelle di lavoro per ottenere informazioni approfondite sui servizi correlati a Azure AD. Sono in arrivo cartelle di lavoro aggiuntive, incluse le cartelle di lavoro sui criteri di accesso condizionale in modalità solo report, informazioni dettagliate basate sul consenso delle app e altro ancora.

Per ulteriori informazioni sulle cartelle di lavoro di, vedere [come utilizzare le cartelle di lavoro di monitoraggio di Azure per Azure Active Directory report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Per altre informazioni sulla deprecazione dei pacchetti di contenuto, vedere il post di Blog relativo all' [annuncio della disponibilità generale delle app](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) per i modelli di Power bi.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Il profilo è stato rinominato e integrato nella pagina dell'account Microsoft Office

**Tipo:** Pianificare la **categoria del servizio** di modifica: profilo/ **funzionalità del prodotto account:** collaborazione

A partire da ottobre, l'esperienza di profilo personale diventerà il mio account. Come parte di questa modifica, ovunque si trovi attualmente, il **mio profilo** cambierà nel **mio account**. Oltre alla modifica dei nomi e ad alcuni miglioramenti della progettazione, l'esperienza aggiornata offrirà integrazione aggiuntiva con la pagina account Microsoft Office. In particolare, sarà possibile accedere alle installazioni e alle sottoscrizioni di Office dalla pagina **Panoramica dell'account** , insieme alle preferenze di contatto relative a Office dalla pagina **privacy** .

Per altre informazioni sull'esperienza my profile (anteprima), vedere [Cenni preliminari sul portale My Profile (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gestire in blocco i gruppi e i membri usando i file CSV nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** gestione gruppi **funzionalità del prodotto:** collaborazione

Siamo lieti di annunciare la disponibilità dell'anteprima pubblica delle esperienze di gestione dei gruppi in blocco nel portale di Azure AD. È ora possibile usare un file CSV e il portale di Azure AD per gestire i gruppi e gli elenchi di membri, tra cui:

- Aggiunta o rimozione di membri da un gruppo.

- Download dell'elenco di gruppi dalla directory.

- Download dell'elenco dei membri del gruppo per un gruppo specifico.

Per ulteriori informazioni, vedere l'elenco [bulk Add members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [bulk Remove members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [bulk download members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)e bulk [download groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Il consenso dinamico è ora supportato tramite un nuovo endpoint di consenso dell'amministratore

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

È stato creato un nuovo endpoint di consenso dell'amministratore per supportare il consenso dinamico, utile per le app che vogliono usare il modello di consenso dinamico sulla piattaforma di identità Microsoft.

Per altre informazioni su come usare questo nuovo endpoint, vedere [uso dell'endpoint di consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2019 settembre

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel settembre 2019 sono state aggiunte le 29 nuove app con supporto federativo per la raccolta di app:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [accesso SSO di Microsoft Azure per Ethidex Compliance Office &trade; -Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [portale di IServer](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), coordinate [Travel and Expense](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), `https://apps.yeeflow.com/` , [Arc](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus team](https://stratus.emea.luware.cloud/login), [Wide Ideas](https://wideideas.online/wideideas/), [Prisme cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT client Hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prism cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), `https://app.penneo.com/` , `https://app.testhtm.com/settings/email-integration` , [Cintoo cloud](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [Hosted Heritage online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [bis](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [Discovery benefits SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/)`https://itask.yipinapp.com/`

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nuovo ruolo lettore globale Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** **funzionalità del prodotto RBAC:** controllo di accesso

A partire dal 24 settembre 2019, verrà avviato il rollup di un nuovo ruolo di Azure Active Directory (AD) denominato Global Reader. Questa implementazione inizierà con la produzione e i clienti cloud globali (GCC), completando il mondo nel corso di ottobre.

Il ruolo di lettore globale è la controparte di sola lettura dell'amministratore globale. Gli utenti con questo ruolo possono leggere le impostazioni e le informazioni amministrative tra Microsoft 365 servizi, ma non possono eseguire azioni di gestione. Il ruolo di lettore globale è stato creato per ridurre il numero di amministratori globali dell'organizzazione. Poiché gli account di amministratore globale sono potenti e vulnerabili ad attacchi, è consigliabile disporre di meno di cinque amministratori globali. È consigliabile usare il ruolo di lettore globale per la pianificazione, i controlli o le indagini. Si consiglia inoltre di utilizzare il ruolo di lettore globale in combinazione con altri ruoli di amministratore limitati, ad esempio amministratore di Exchange, per facilitare il lavoro senza richiedere il ruolo di amministratore globale.

Il ruolo di lettore globale funziona con il nuovo centro di amministrazione di Microsoft 365, l'interfaccia di amministrazione di Exchange, l'interfaccia di amministrazione dei team, il Centro sicurezza, il centro di conformità, l'interfaccia di amministrazione di Azure AD e l'interfaccia di amministrazione Gestione dispositivi.

>[!NOTE]
> Al primo avvio della versione di anteprima pubblica, il ruolo di lettore globale non funzionerà con: SharePoint, Privileged Access Management, Customer Lockbox, etichette di riservatezza, ciclo di vita dei team, creazione di report dei team & analisi delle chiamate, gestione dei dispositivi telefonici IP Team e catalogo delle app teams.

Per ulteriori informazioni, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Accedere a un server di report locale dall'app Power BI mobile usando Azure Active Directory Application Proxy

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** proxy app: controllo di accesso

Una nuova integrazione tra il Power BI app per dispositivi mobili e Azure AD proxy di applicazione consente di accedere in modo sicuro all'app per dispositivi mobili Power BI e di visualizzare i report dell'organizzazione ospitati nell'Server di report di Power BI locale.

Per informazioni sull'app per dispositivi mobili Power BI, inclusa la posizione in cui scaricare l'app, vedere il [sito Power bi](https://powerbi.microsoft.com/mobile/). Per altre informazioni su come configurare l'app per dispositivi mobili Power BI con Azure AD proxy di applicazione, vedere [abilitare l'accesso remoto a Power bi dispositivi mobili con Azure ad proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>È disponibile una nuova versione del modulo AzureADPreview di PowerShell

**Tipo:** **Categoria servizio** funzionalità modificata: altra **funzionalità del prodotto:** directory

Sono stati aggiunti nuovi cmdlet al modulo AzureADPreview per semplificare la definizione e l'assegnazione di ruoli personalizzati in Azure AD, tra cui:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nuova versione di Azure AD Connect

**Tipo:** **Categoria servizio** funzionalità modificata: altra **funzionalità del prodotto:** directory

È stata rilasciata una versione aggiornata di Azure AD Connect per i clienti con aggiornamento automatico. Questa nuova versione include diverse nuove funzionalità, miglioramenti e correzioni di bug. Per ulteriori informazioni su questa nuova versione, vedere [Azure ad Connect: cronologia delle versioni](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication Server (multi-factor authentication), versione 8.0.2 è ora disponibile

**Tipo:** **Categoria di servizio** fisso: **funzionalità del prodotto multi-** Factor Authentication: sicurezza delle identità & protezione

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

**Tipo:** Nuova **Categoria servizio funzionalità:** gestione gruppi **funzionalità del prodotto:** collaborazione

Siamo lieti di annunciare la disponibilità dell'anteprima pubblica delle esperienze correlate ai gruppi avanzati nel portale di Azure AD. Questi miglioramenti consentono una migliore gestione dei gruppi e degli elenchi di membri, fornendo:

- Funzionalità di ricerca avanzate, ad esempio la ricerca di sottostringhe negli elenchi di gruppi.
- Opzioni di filtro e ordinamento avanzate negli elenchi di membri e proprietari.
- Nuove funzionalità di ricerca per gli elenchi di membri e proprietari.
- Conteggi di gruppo più accurati per gruppi di grandi dimensioni.

Per ulteriori informazioni, vedere [Manage groups in the portale di Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Sono disponibili nuovi ruoli personalizzati per la gestione della registrazione delle app (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** **funzionalità del prodotto RBAC:** controllo di accesso

I ruoli personalizzati, disponibili con una sottoscrizione Azure AD P1 o P2, possono ora offrire un accesso con granularità fine, consentendo di creare definizioni di ruolo con autorizzazioni specifiche e quindi di assegnare tali ruoli a risorse specifiche. Attualmente, è possibile creare ruoli personalizzati usando le autorizzazioni per la gestione delle registrazioni delle app e quindi l'assegnazione del ruolo a un'app specifica. Per ulteriori informazioni sui ruoli personalizzati, vedere [ruoli di amministratore personalizzato in Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Se sono necessarie altre autorizzazioni o risorse supportate, che attualmente non vengono visualizzate, è possibile inviare commenti e suggerimenti al [sito di commenti e suggerimenti su Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) .

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>I nuovi log di provisioning consentono di monitorare e risolvere i problemi della distribuzione del provisioning delle app (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio funzionalità:** provisioning app funzionalità del **prodotto:** gestione del ciclo di vita delle identità

Sono disponibili nuovi log di provisioning che consentono di monitorare e risolvere i problemi relativi alla distribuzione del provisioning di utenti e gruppi. I nuovi file di log includono informazioni su:

- Quali gruppi sono stati creati correttamente in [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Quali ruoli sono stati importati da [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Quali dipendenti non sono stati importati dalla [giornata lavorativa](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Per altre informazioni, vedere [provisioning dei report nel portale di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nuovi report sulla sicurezza per tutti gli amministratori di Azure AD (disponibilità generale)

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità di protezione delle identità del **prodotto:** sicurezza delle identità & protezione

Per impostazione predefinita, tutti gli amministratori di Azure AD saranno presto in grado di accedere ai report di sicurezza moderni entro Azure AD. Fino alla fine di settembre, sarà possibile usare il banner nella parte superiore dei report di sicurezza moderni per tornare ai report precedenti.

I report di sicurezza moderni forniranno funzionalità aggiuntive dalle versioni precedenti, tra cui:

- Filtro e ordinamento avanzati
- Azioni bulk, ad esempio la dismissing del rischio utente
- Conferma di entità compromesse o sicure
- Stato di rischio, copertura: a rischio, non rilasciata, risolta e confermata compromessa
- Nuovi rilevamenti correlati al rischio (disponibili per Azure AD Premium sottoscrittori)

Per altre informazioni, vedere [utenti a rischio](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [accessi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)a rischio e rilevamento dei [rischi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>L'identità gestita assegnata dall'utente è disponibile per le macchine virtuali e i set di scalabilità di macchine virtuali (disponibilità generale)

**Tipo:** Nuova **Categoria servizio funzionalità:** identità gestite per le risorse di Azure **funzionalità del prodotto:** esperienza dello sviluppatore

Le identità gestite assegnate dall'utente sono ora disponibili a livello generale per le macchine virtuali e i set di scalabilità di macchine virtuali. Come parte di questo, Azure può creare un'identità nel tenant di Azure AD considerata attendibile dalla sottoscrizione in uso e può essere assegnata a una o più istanze del servizio di Azure. Per altre informazioni sulle identità gestite assegnate dall'utente, vedere informazioni sulle [identità gestite per le risorse di Azure](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Gli utenti possono reimpostare le password usando un'app per dispositivi mobili o un token hardware (disponibilità generale)

**Tipo:** Modifica della **categoria del servizio funzionalità:** reimpostazione della password self **-Service funzionalità del prodotto:** autenticazione utente

Gli utenti che hanno registrato un'app per dispositivi mobili con l'organizzazione possono ora reimpostare la propria password approvando una notifica dall'app Microsoft Authenticator o immettendo un codice dall'app per dispositivi mobili o dal token hardware.

Per altre informazioni, vedere [come funziona: Azure ad la reimpostazione della password self-service](https://aka.ms/authappsspr). Per altre informazioni sull'esperienza utente, vedere la [panoramica sulla reimpostazione della password aziendale o dell'Istituto di istruzione](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora la cache condivisa MSAL.NET per gli scenari per conto di

**Tipo:** **Categoria di servizio fisso:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

A partire da Azure AD Authentication Library (ADAL.NET) versione 5.0.0-Preview, gli sviluppatori [di app devono serializzare una cache per ogni account per le app Web e le API Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). In caso contrario, alcuni scenari che usano il [flusso per conto di](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), insieme ad alcuni casi d'uso specifici di `UserAssertion` , possono determinare un'elevazione dei privilegi. Per evitare questa vulnerabilità, ADAL.NET ignora ora la cache condivisa di Microsoft Authentication Library per DotNet (MSAL.NET) per gli scenari per conto di.

Per altre informazioni su questo problema, vedere [Azure Active Directory vulnerabilità dell'elevazione dei privilegi della libreria di autenticazione](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2019 agosto

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel 2019 agosto sono state aggiunte le 26 nuove app con supporto federativo per la raccolta di app:

[Piattaforma civica](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [pronovos Ops manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [portale inativ di Viareport (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [partecipazione Academy](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [matrice di priorità](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), backup degli [endpoint di Carbonite](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by cincot](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [Deliver. Media &trade; Portal](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat ad Connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [pronovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Esplora la cura strategica](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Sono disponibili nuove versioni dei moduli AzureAD PowerShell e AzureADPreview PowerShell

**Tipo:** **Categoria servizio** funzionalità modificata: altra **funzionalità del prodotto:** directory

Sono disponibili nuovi aggiornamenti ai moduli di PowerShell per AzureAD e AzureAD Preview:

- Un nuovo `-Filter` parametro è stato aggiunto al `Get-AzureADDirectoryRole` parametro nel modulo AzureAD. Questo parametro consente di filtrare in base ai ruoli della directory restituiti dal cmdlet.
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

**Tipo:** **Categoria servizio** funzionalità modificata: funzionalità del **prodotto** gestione gruppi: collaborazione

Sono stati apportati alcuni miglioramenti dell'interfaccia utente al generatore di regole di gruppo dinamico, disponibile nel portale di Azure, per semplificare la configurazione di una nuova regola o per modificare le regole esistenti. Questo miglioramento della progettazione consente di creare regole con fino a cinque espressioni, anziché una sola. È stato aggiornato anche l'elenco delle proprietà del dispositivo per rimuovere le proprietà del dispositivo deprecate.

Per altre informazioni, vedere [gestire le regole di appartenenza dinamica](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nuova autorizzazione Microsoft Graph App disponibile per l'uso con le verifiche di accesso

**Tipo:** Modifica della **categoria del servizio funzionalità:** verifiche di accesso **funzionalità del prodotto:** governance delle identità

È stata introdotta una nuova autorizzazione Microsoft Graph App, `AccessReview.ReadWrite.Membership` , che consente alle app di creare e recuperare automaticamente le verifiche di accesso per l'appartenenza ai gruppi e le assegnazioni delle app. Questa autorizzazione può essere usata dai processi pianificati o come parte dell'automazione, senza richiedere un contesto utente connesso.

Per altre informazioni, vedere l' [esempio come creare Azure ad verifiche di accesso usando Microsoft Graph le autorizzazioni dell'app con il Blog di PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD log attività sono ora disponibili per le istanze del cloud per enti pubblici in monitoraggio di Azure

**Tipo:** Modifica della **categoria del servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

Siamo lieti di annunciare che Azure AD log attività sono ora disponibili per le istanze del cloud per enti pubblici in monitoraggio di Azure. È ora possibile inviare i log di Azure AD all'account di archiviazione o a un hub eventi per l'integrazione con gli strumenti SIEM, ad esempio [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)e [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight).

Per altre informazioni sulla configurazione di monitoraggio di Azure, vedere [Azure ad log attività in monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aggiornare gli utenti alla nuova esperienza avanzata per le informazioni di sicurezza

**Tipo:** Modifica della **categoria del servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

A partire dal 25 settembre 2019, verrà disattivata l'esperienza di sicurezza precedente, non avanzata, per la registrazione e la gestione delle informazioni di sicurezza degli utenti e per attivare solo la nuova [versione migliorata](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Ciò significa che gli utenti non saranno più in grado di usare l'esperienza precedente.

Per ulteriori informazioni sull'esperienza avanzata delle informazioni di sicurezza, vedere la [documentazione di amministrazione](https://aka.ms/securityinfodocs) e la [documentazione dell'utente](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Per attivare questa nuova esperienza, è necessario:

1. Accedere al portale di Azure come amministratore globale o amministratore utente.

2. Passare a **Azure Active Directory > impostazioni utente > Gestisci impostazioni per le funzionalità di anteprima del pannello di accesso**.

3. In **utenti possono usare le funzionalità di anteprima per la registrazione e la gestione delle informazioni di sicurezza-area avanzata** , selezionare **selezionata**, quindi scegliere un gruppo di utenti o scegliere **tutti** per attivare questa funzionalità per tutti gli utenti nel tenant.

4. In * * gli utenti possono usare le funzionalità di anteprima per la registrazione e la gestione della sicurezza * * info * * * * area, selezionare **nessuno**.

5. Salvare le impostazioni.

    Dopo aver salvato le impostazioni, non sarà più possibile accedere all'esperienza di informazioni di sicurezza precedente.

>[!Important]
>Se non si completano questi passaggi prima del 25 settembre 2019, il tenant di Azure Active Directory verrà abilitato automaticamente per migliorare l'esperienza. Per eventuali domande, contattaci all'indirizzo registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Le richieste di autenticazione che usano gli account di accesso POST verranno convalidate più rigorosamente

**Tipo:** Modifica della **categoria del servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** standard

A partire dal 2 settembre 2019, le richieste di autenticazione che usano il metodo POST verranno convalidate in modo più rigoroso rispetto agli standard HTTP. In particolare, gli spazi e le virgolette doppie (") non verranno più rimossi dai valori del modulo della richiesta. Queste modifiche non sono previste per interrompere i client esistenti e consentiranno di verificare che le richieste inviate a Azure AD siano gestite in modo affidabile ogni volta.

Per ulteriori informazioni, vedere le [notifiche relative alle modifiche Azure ad di rilievo](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Luglio 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Pianificare la modifica: aggiornamento del servizio proxy di applicazione per supportare solo TLS 1,2

**Tipo:** Pianificare la **categoria del servizio** di modifica: **funzionalità del prodotto** proxy app: controllo di accesso

Per fornire la crittografia più avanzata, l'accesso al servizio proxy di applicazione verrà limitato solo ai protocolli TLS 1,2. Questa limitazione verrà inizialmente implementata per i clienti che usano già i protocolli TLS 1,2, quindi non sarà possibile vedere l'effetto. La deprecazione completa dei protocolli TLS 1,0 e TLS 1,1 verrà completata il 31 agosto 2019. I clienti che usano ancora TLS 1,0 e TLS 1,1 riceveranno una notifica avanzata per prepararsi a questa modifica.

Per mantenere la connessione al servizio proxy di applicazione in tutte le modifiche, è consigliabile assicurarsi che le combinazioni client-server e browser-server siano aggiornate per l'uso di TLS 1,2. È inoltre consigliabile assicurarsi di includere tutti i sistemi client usati dai dipendenti per accedere alle app pubblicate tramite il servizio proxy di applicazione.

Per altre informazioni, vedere [aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Pianificare le modifiche: gli aggiornamenti della progettazione sono disponibili per la raccolta di applicazioni

**Tipo:** Pianificare la **categoria del servizio** di modifica: **funzionalità del prodotto** app aziendali: SSO

Nuove modifiche all'interfaccia utente verranno apportate alla progettazione dell' **aggiunta dall'area raccolta** del pannello **Aggiungi un'applicazione** . Queste modifiche consentiranno di trovare più facilmente le app che supportano il provisioning automatico, OpenID Connect, Security Assertion Markup Language (SAML) e Single Sign-On di password (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Pianificare la modifica: rimozione dell'indirizzo IP del server multi-factor authentication dall'indirizzo IP di Office 365

**Tipo:** Pianificare la **categoria del servizio** di modifica: **funzionalità del prodotto** multi-factor authentication: sicurezza delle identità & protezione

È in corso la rimozione dell'indirizzo IP del server multi-factor authentication dall' [indirizzo IP di Office 365 e dal servizio Web URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Se attualmente si fa affidamento su queste pagine per aggiornare le impostazioni del firewall, è necessario assicurarsi di includere anche l'elenco degli indirizzi IP documentati nella sezione **requisiti del firewall di azure server multi-factor authentication** dell'articolo [introduzione ad Azure server multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>I token solo app richiedono ora che l'app client esista nel tenant delle risorse

**Tipo:** **Categoria di servizio fisso:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Il 26 luglio 2019 è stato modificato il modo in cui vengono forniti i token solo app tramite la [concessione delle credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). In precedenza, le app potevano ottenere i token per chiamare altre app, indipendentemente dal fatto che l'app client si trovasse nel tenant. Questo comportamento è stato aggiornato in modo che le risorse a tenant singolo, talvolta chiamate API Web, possano essere chiamate solo da app client presenti nel tenant delle risorse.

Se l'app non si trova nel tenant delle risorse, viene visualizzato un messaggio di errore che indica che `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` per risolvere il problema è necessario creare l'entità servizio dell'app client nel tenant, usando l' [endpoint di consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) o [tramite PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), che garantisce al tenant l'autorizzazione dell'app per operare nel tenant.

Per ulteriori informazioni, vedere [What ' s New for Authentication?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Il consenso esistente tra il client e l'API continua a non essere necessario. Le app devono ancora eseguire controlli di autorizzazione personalizzati.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nuovo accesso senza password per Azure AD usando le chiavi di sicurezza FIDO2

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Azure AD i clienti possono ora impostare i criteri per gestire le chiavi di sicurezza FIDO2 per gli utenti e i gruppi dell'organizzazione. Gli utenti finali possono anche registrare autonomamente le proprie chiavi di sicurezza, usare le chiavi per accedere ai propri account Microsoft nei siti Web, mentre nei dispositivi che supportano FIDO, nonché accedere ai dispositivi Windows 10 aggiunti a Azure AD.

Per altre informazioni, vedere [abilitare l'accesso senza password per Azure ad (anteprima)](/azure/active-directory/authentication/concept-authentication-passwordless) per le informazioni relative all'amministratore e [configurare le informazioni di sicurezza per l'uso di una chiave di sicurezza (anteprima)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) per le informazioni relative all'utente finale.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2019 luglio

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel 2019 luglio sono state aggiunte le 18 nuove app con supporto federativo per la raccolta di app:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright pattern omnicanale Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access &trade; per Ethidex Compliance Office](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [iperanna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), PharmID [WasteWitness](https://pharmid.com/), i2B [Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), JFrog [Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova **Categoria servizio funzionalità:** app aziendali **funzionalità del prodotto:** monitoraggio & Reporting

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nuovo tag del servizio Azure AD Domain Services per il gruppo di sicurezza di rete

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad Domain Services **funzionalità del prodotto:** Azure ad Domain Services

Se si è stanchi di gestire lunghi elenchi di indirizzi IP e intervalli, è possibile usare il nuovo tag del servizio di rete **AzureActiveDirectoryDomainServices** nel gruppo di sicurezza di rete di Azure per proteggere il traffico in ingresso verso la subnet della rete virtuale Azure ad Domain Services.

Per ulteriori informazioni su questo nuovo tag di servizio, vedere [gruppi di sicurezza di rete per Azure ad Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Azure AD Domain Services (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad Domain Services **funzionalità del prodotto:** Azure ad Domain Services

Siamo lieti di annunciare il rilascio di Azure AD controllo della sicurezza del servizio del dominio per l'anteprima pubblica. Il controllo della sicurezza consente di fornire informazioni importanti sui servizi di autenticazione tramite lo streaming di eventi di controllo di sicurezza a risorse mirate, tra cui archiviazione di Azure, aree di lavoro di Azure Log Analytics e hub eventi di Azure, tramite il portale del servizio di Azure AD dominio.

Per ulteriori informazioni, vedere [Enable Security Audits for Azure ad Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nuovi metodi di autenticazione utilizzo & Insights (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** reimpostazione password self **-Service funzionalità del prodotto:** monitoraggio & Reporting

I nuovi metodi di autenticazione & i report di Insights consentono di comprendere in che modo le funzionalità come Azure Multi-Factor Authentication e la reimpostazione della password self-service vengono registrate e usate nell'organizzazione, tra cui il numero di utenti registrati per ogni funzionalità, la frequenza con cui viene usata la reimpostazione della password self-service per reimpostare le password e il metodo in cui viene eseguita la reimpostazione.

Per altre informazioni, vedere [metodi di autenticazione utilizzo & Insights (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Sono disponibili nuovi report di sicurezza per tutti gli amministratori di Azure AD (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità di protezione delle identità del **prodotto:** sicurezza delle identità & protezione

Tutti gli amministratori di Azure AD ora possono selezionare il banner nella parte superiore dei report di sicurezza esistenti, ad esempio il report **utenti contrassegnati per il rischio** , per iniziare a usare la nuova esperienza di sicurezza, come illustrato in **utenti a rischio** e report sugli **accessi a rischio** . Nel corso del tempo, tutti i report sulla sicurezza passano dalle versioni precedenti alle nuove versioni, con i nuovi report che forniscono le funzionalità aggiuntive seguenti:

- Filtro e ordinamento avanzati

- Azioni bulk, ad esempio la dismissing del rischio utente

- Conferma di entità compromesse o sicure

- Stato di rischio, copertura: a rischio, non rilasciata, risolta e confermata compromessa

Per altre informazioni, vedere [report utenti a rischio](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) e [report sugli accessi a rischio](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Azure AD Domain Services (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad Domain Services **funzionalità del prodotto:** Azure ad Domain Services

Siamo lieti di annunciare il rilascio di Azure AD controllo della sicurezza del servizio del dominio per l'anteprima pubblica. Il controllo della sicurezza consente di fornire informazioni importanti sui servizi di autenticazione tramite lo streaming di eventi di controllo di sicurezza a risorse mirate, tra cui archiviazione di Azure, aree di lavoro di Azure Log Analytics e hub eventi di Azure, tramite il portale del servizio di Azure AD dominio.

Per ulteriori informazioni, vedere [Enable Security Audits for Azure ad Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nuova federazione diretta B2B con SAML/WS-Fed (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio** funzionalità: **funzionalità del prodotto B2B:** B2B/B2C

La Federazione diretta aiuta a semplificare l'utilizzo dei partner la cui soluzione di gestione delle identità gestita da IT non è Azure AD, usando sistemi di identità che supportano gli standard SAML o WS-Fed. Dopo aver configurato una relazione di federazione diretta con un partner, qualsiasi nuovo utente Guest invitato da tale dominio potrà collaborare con l'utente usando il proprio account aziendale esistente, rendendo l'esperienza utente per i guest più trasparente.

Per ulteriori informazioni, vedere [Federazione diretta con ad FS e provider di terze parti per utenti Guest (anteprima)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova **Categoria servizio funzionalità:** app aziendali **funzionalità del prodotto:** monitoraggio & Reporting

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nuovo controllo per i nomi di gruppi duplicati nel portale di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** gestione gruppi **funzionalità del prodotto:** collaborazione

A questo punto, quando si crea o si aggiorna un nome di gruppo dal portale di Azure AD, verrà eseguito un controllo per verificare se si sta duplicando un nome di gruppo esistente nella risorsa. Se si determina che il nome è già in uso da parte di un altro gruppo, verrà chiesto di modificarne il nome.

Per altre informazioni, vedere [gestire i gruppi nel portale di Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD ora supporta i parametri di query statici negli URI di risposta (Reindirizzamento)

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Azure AD app possono ora registrare e usare URI di risposta (Reindirizzamento) con parametri di query statici (ad esempio, `https://contoso.com/oauth2?idp=microsoft` ) per le richieste OAuth 2,0. Il parametro della query statica è soggetto alle stringhe corrispondenti per gli URI di risposta, esattamente come qualsiasi altra parte dell'URI di risposta. Se non è presente alcuna stringa registrata corrispondente all'URI di reindirizzamento con decodifica URL, la richiesta viene rifiutata. Se viene trovato l'URI di risposta, l'intera stringa viene utilizzata per reindirizzare l'utente, incluso il parametro della query statica.

Gli URI di risposta dinamici sono ancora proibiti perché rappresentano un rischio per la sicurezza e non possono essere usati per mantenere le informazioni sullo stato attraverso una richiesta di autenticazione. A questo scopo, usare il `state` parametro.

Attualmente, le schermate di registrazione delle app del portale di Azure continuano a bloccare i parametri della query. Tuttavia, è possibile modificare manualmente il manifesto dell'applicazione per aggiungere e testare i parametri di query nell'app. Per ulteriori informazioni, vedere [What ' s New for Authentication?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>I log attività (API Graph) per Azure AD sono ora disponibili tramite i cmdlet di PowerShell

**Tipo:** Nuova **Categoria servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

Siamo lieti di annunciare che Azure AD log attività (report di controllo e accesso) sono ora disponibili tramite il modulo di Azure AD PowerShell. In precedenza, era possibile creare script personalizzati usando gli endpoint MS API Graph e ora abbiamo esteso questa funzionalità ai cmdlet di PowerShell.

Per ulteriori informazioni sull'utilizzo di questi cmdlet, vedere [Azure ad cmdlet di PowerShell per la creazione di report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controlli filtro aggiornati per i log di controllo e accesso in Azure AD

**Tipo:** Modifica della **categoria del servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

I report di controllo e accesso sono stati aggiornati per poter applicare diversi filtri senza doverli aggiungere come colonne nelle schermate dei report. Inoltre, è ora possibile decidere il numero di filtri che si desidera visualizzare sullo schermo. Questi aggiornamenti interagiscono tra loro per semplificare la lettura dei report e l'ambito delle proprie esigenze.

Per altre informazioni su questi aggiornamenti, vedere [filtrare i log di controllo](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) e le [attività di accesso al filtro](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Giugno 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nuova API riskDetections per Microsoft Graph (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità di protezione delle identità del **prodotto:** sicurezza delle identità & protezione

Siamo lieti di annunciare che la nuova API riskDetections per Microsoft Graph è ora disponibile in anteprima pubblica. È possibile usare questa nuova API per visualizzare un elenco dei rilevamenti dei rischi di accesso e dell'utente correlati alla protezione delle identità dell'organizzazione. È anche possibile usare questa API per eseguire query in modo più efficiente sui rilevamenti dei rischi, inclusi i dettagli sul tipo di rilevamento, lo stato, il livello e altro ancora.

Per ulteriori informazioni, vedere la [documentazione di riferimento dell'API di rilevamento dei rischi](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-2019 giugno

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel giugno 2019 sono state aggiunte le 22 nuove app con supporto federativo per la raccolta di app:

[Azure ad SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [Anaqua](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [client VPN di Azure](https://portal.azure.com/), [expensein](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)helper, [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz in-Car Office](https://me.secure.mercedes-benz.com/), [SKORE](https://app.justskore.it/), [Oracle Cloud Infrastructure console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), PROPTIMISE [OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager per Oracle Retail Merchandising, Oracle Access Manager per Oracle E-Business Suite, Oracle IDC per E-Business Suite, Oracle IDC per PeopleSoft, Oracle IDC per JD Edwards

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova **Categoria servizio funzionalità:** app aziendali **funzionalità del prodotto:** monitoraggio & Reporting

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visualizzare lo stato di avanzamento in tempo reale del servizio di provisioning Azure AD

**Tipo:** Modifica della **categoria del servizio funzionalità:** provisioning delle app **funzionalità del prodotto:** gestione del ciclo di vita delle identità

L'esperienza di provisioning Azure AD è stata aggiornata in modo da includere un nuovo indicatore di stato che mostra la distanza del processo di provisioning degli utenti. Questa esperienza aggiornata fornisce anche informazioni sul numero di utenti di cui è stato effettuato il provisioning durante il ciclo corrente, nonché sul numero di utenti di cui è stato effettuato il provisioning fino a oggi.

Per altre informazioni, vedere [controllare lo stato del provisioning dell'utente](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Le informazioni personalizzate distintive dell'azienda vengono visualizzate nelle schermate di disconnessione e di errore

**Tipo:** Modifica della **categoria del servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Il Azure AD è stato aggiornato in modo che la personalizzazione dell'azienda venga ora visualizzata nelle schermate di disconnessione e di errore, nonché nella pagina di accesso. Non è necessario eseguire alcuna operazione per attivare questa funzionalità, Azure AD usa semplicemente gli asset già configurati nell'area di **personalizzazione della società** del portale di Azure.

Per ulteriori informazioni sulla configurazione della personalizzazione dell'azienda, vedere [la pagina relativa all'aggiunta della personalizzazione alle pagine di Azure Active Directory dell'organizzazione](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Il server Azure Multi-Factor Authentication (multi-factor authentication) non è più disponibile per le nuove distribuzioni

**Tipo:** Categoria di **servizio** deprecata: **funzionalità del prodotto multi-** Factor Authentication: sicurezza delle identità & protezione

A partire dal 1 ° luglio 2019, Microsoft non offrirà più il server multi-factor authentication per le nuove distribuzioni. I nuovi clienti che vogliono richiedere l'autenticazione a più fattori nella propria organizzazione devono ora usare Azure Multi-Factor Authentication basato sul cloud. I clienti che hanno attivato il server di autenticazione a più fattori prima del 1 ° luglio non vedranno una modifica. Sarà comunque possibile scaricare la versione più recente, ottenere gli aggiornamenti futuri e generare le credenziali di attivazione.

Per altre informazioni, vedere [Introduzione all'server multi-factor authentication di Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Per altre informazioni sui Multi-Factor Authentication di Azure basati sul cloud, vedere [pianificazione di una distribuzione di azure multi-factor authentication basata sul cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maggio 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Modifica del servizio: supporto futuro solo per i protocolli TLS 1,2 nel servizio proxy di applicazione

**Tipo:** Pianificare la **categoria del servizio** di modifica: **funzionalità del prodotto** proxy app: controllo di accesso

Per fornire la crittografia migliore per i clienti, è possibile limitare l'accesso solo ai protocolli TLS 1,2 nel servizio proxy di applicazione. Questa modifica viene implementata gradualmente per i clienti che usano già i protocolli TLS 1,2, pertanto non è possibile visualizzare alcuna modifica.

La deprecazione di TLS 1,0 e TLS 1,1 si verifica il 31 agosto 2019, ma verrà fornita una notifica avanzata aggiuntiva, per cui si avrà il tempo necessario per prepararsi a questa modifica. Per prepararsi a questa modifica, verificare che le combinazioni client-server e server browser, inclusi i client usati dagli utenti per accedere alle app pubblicate tramite il proxy di applicazione, siano aggiornate per usare il protocollo TLS 1,2 per mantenere la connessione al servizio proxy di applicazione. Per altre informazioni, vedere [aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Usare il report utilizzo e informazioni dettagliate per visualizzare i dati di accesso correlati all'app

**Tipo:** Nuova **Categoria servizio funzionalità:** app aziendali **funzionalità del prodotto:** monitoraggio & Reporting

È ora possibile usare il report Usage and Insights, disponibile nell'area **applicazioni aziendali** del portale di Azure, per ottenere una visualizzazione incentrata sull'applicazione dei dati di accesso, incluse informazioni su:

- App principali usate per la tua organizzazione

- App con gli accessi più non riusciti

- Errori di accesso principali per ogni app

Per altre informazioni su questa funzionalità, vedere [report sull'utilizzo e informazioni dettagliate nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizzare il provisioning degli utenti nelle app cloud usando Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** app aziendali **funzionalità del prodotto:** monitoraggio & Reporting

Seguire queste nuove esercitazioni per usare il servizio di provisioning Azure AD per automatizzare la creazione, l'eliminazione e l'aggiornamento degli account utente per le app basate sul cloud seguenti:

- [Del](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

È anche possibile seguire questa nuova [esercitazione su Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), che fornisce informazioni su come eseguire il provisioning di oggetti gruppo.

Per altre informazioni su come proteggere meglio l'organizzazione tramite il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Il Punteggio di sicurezza identità è ora disponibile in Azure AD (disponibilità generale)

**Tipo:** Nuova **categoria di servizio funzionalità:** N/A funzionalità del **prodotto:** sicurezza delle identità & protezione

È ora possibile monitorare e migliorare il comportamento di sicurezza delle identità usando la funzionalità di assegnazione del Punteggio Identity Secure in Azure AD. La funzionalità di assegnazione del Punteggio Identity Secure usa un unico dashboard che consente di:

- Misurare oggettivamente il comportamento di sicurezza delle identità, in base a un punteggio compreso tra 1 e 223.

- Pianificare i miglioramenti della sicurezza delle identità

- Verifica il successo dei miglioramenti della sicurezza

Per ulteriori informazioni sulla funzionalità di assegnazione dei punteggi di sicurezza Identity, vedere la pagina relativa [al Punteggio di identità sicuro in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>È ora disponibile una nuova esperienza Registrazioni app (disponibilità generale)

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** esperienza dello sviluppatore

La nuova esperienza [registrazioni app](https://aka.ms/appregistrations) è ora disponibile a livello generale. Questa nuova esperienza include tutte le funzionalità chiave con cui si ha familiarità dall'portale di Azure e dal portale di registrazione delle applicazioni e migliora le operazioni tramite:

- **Migliore gestione delle app.** Invece di visualizzare le app in portali diversi, è ora possibile visualizzare tutte le app in un'unica posizione.

- **Registrazione semplificata dell'app.** Dall'esperienza di navigazione migliorata all'esperienza di selezione delle autorizzazioni rinnovata, ora è più semplice registrare e gestire le app.

- **Informazioni più dettagliate.** È possibile trovare altri dettagli sull'app, incluse le guide introduttive e altro ancora.

Per ulteriori informazioni, vedere la pagina relativa alla [piattaforma Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/) e l' [esperienza registrazioni app è ora disponibile a livello generale.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) annuncio del Blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nuove funzionalità disponibili nell'API utenti a rischio per la protezione delle identità

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità di protezione delle identità del **prodotto:** sicurezza delle identità & protezione

Siamo lieti di annunciare che è ora possibile usare l'API Users rischiosa per recuperare la cronologia dei rischi degli utenti, eliminare gli utenti a rischio e verificare che gli utenti siano compromessi. Questa modifica consente di aggiornare in modo più efficiente lo stato dei rischi degli utenti e di comprendere la cronologia dei rischi.

Per ulteriori informazioni, vedere la [documentazione di riferimento dell'API utenti rischiosi](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-2019 maggio

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel 2019 maggio sono state aggiunte le 21 nuove app con supporto federativo per la raccolta di app:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [collegamenti reali](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [brasate](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayer](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [Outsystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum area di lavoro](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [WebMethods API cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Miglioramento della creazione e della gestione dei gruppi nel portale Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** gestione gruppi **funzionalità del prodotto:** collaborazione

Sono stati apportati miglioramenti alle esperienze correlate ai gruppi nel portale di Azure AD. Questi miglioramenti consentono agli amministratori di gestire più facilmente elenchi di gruppi, elenchi di membri e fornire opzioni di creazione aggiuntive.

Sono inclusi i miglioramenti seguenti:

- Filtro di base per tipo di appartenenza e tipo di gruppo.

- Aggiunta di nuove colonne, ad esempio origine e indirizzo di posta elettronica.

- Possibilità di selezionare più gruppi, membri e elenchi di proprietari per un'eliminazione semplificata.

- Possibilità di scegliere un indirizzo di posta elettronica e di aggiungere proprietari durante la creazione del gruppo.

Per altre informazioni, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurare i criteri di denominazione per i gruppi di Office 365 nel portale di Azure AD (disponibilità generale)

**Tipo:** **Categoria servizio** funzionalità modificata: funzionalità del **prodotto** gestione gruppi: collaborazione

Gli amministratori possono ora configurare un criterio di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica consente di applicare convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare i criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi dei gruppi (ad esempio, "CEO, Payroll, HR").

Per altre informazioni, vedere [applicare criteri di denominazione per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Sono ora disponibili endpoint API Microsoft Graph per Azure AD log attività (disponibilità generale)

**Tipo:** Modifica della **categoria del servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

Siamo lieti di annunciare la disponibilità generale del supporto per gli endpoint dell'API Microsoft Graph per Azure AD log attività. Con questa versione è ora possibile usare la versione 1,0 dei log di controllo Azure AD, oltre alle API dei log di accesso.

Per altre informazioni, vedere [Azure ad Panoramica dell'API del registro di controllo](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Gli amministratori possono ora usare l'accesso condizionale per il processo di registrazione combinato (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio funzionalità:** accesso condizionale funzionalità del **prodotto:** sicurezza delle identità & protezione

Gli amministratori possono ora creare criteri di accesso condizionale per l'uso nella pagina di registrazione combinata. Ciò include l'applicazione di criteri per consentire la registrazione se:

- Gli utenti si trovano in una rete attendibile.

- Il rischio di accesso degli utenti è basso.

- Gli utenti si trovano su un dispositivo gestito.

- Gli utenti accettano le condizioni per l'utilizzo (TOU) dell'organizzazione.

Per altre informazioni sull'accesso condizionale e sulla reimpostazione della password, vedere il [post di Blog relativo all'accesso condizionale per l'esperienza di registrazione con autenticazione a più fattori e reimpostazione della password di Azure ad](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348) Per ulteriori informazioni sui criteri di accesso condizionale per il processo di registrazione combinato, vedere [criteri di accesso condizionale per la registrazione combinata](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Per ulteriori informazioni sulla funzionalità Azure AD condizioni per l'utilizzo, vedere [Azure Active Directory le condizioni](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)per l'utilizzo.

---

## <a name="april-2019"></a>Aprile 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nuovo Azure AD rilevamento Intelligence per le minacce è ora disponibile come parte di Azure AD Identity Protection

**Tipo:** Nuova **categoria di servizio funzionalità:** Azure ad Identity Protection funzionalità del **prodotto:** protezione delle identità & protezione

Azure AD rilevamento Intelligence per le minacce è ora disponibile come parte della funzionalità Azure AD Identity Protection aggiornata. Questa nuova funzionalità consente di indicare un'attività insolita dell'utente per un utente o un'attività specifica, coerente con i modelli di attacco noti basati sulle fonti di intelligence per le minacce interne ed esterne di Microsoft.

Per ulteriori informazioni sulla versione aggiornata di Azure AD Identity Protection, vedere i [quattro miglioramenti principali Azure ad Identity Protection sono ora disponibili in anteprima pubblica](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) Blog e gli [elementi Azure Active Directory Identity Protection (aggiornati)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) . Per ulteriori informazioni sul rilevamento Azure AD Intelligence per le minacce, vedere l'articolo relativo ai [rilevamenti dei rischi Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>È ora disponibile la gestione dei diritti Azure AD (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** governance Identity **funzionalità del prodotto:** governance delle identità

Azure AD la gestione dei diritti, ora disponibile in anteprima pubblica, consente ai clienti di delegare la gestione dei pacchetti di accesso, che definisce il modo in cui i dipendenti e i partner commerciali possono richiedere l'accesso, che devono approvare e il tempo di accesso. I pacchetti di Access possono gestire l'appartenenza a gruppi di Azure AD e Office 365, assegnazioni di ruolo in applicazioni aziendali e assegnazioni di ruolo per i siti di SharePoint Online. Per altre informazioni sulla gestione dei diritti, vedere [Panoramica della gestione dei diritti Azure ad](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Per ulteriori informazioni sull'ampia gamma di funzionalità Azure AD Identity Governance, tra cui Privileged Identity Management, verifiche di accesso e condizioni per l'utilizzo, vedere [che cos'è Azure ad Identity governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurare i criteri di denominazione per i gruppi di Office 365 nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** gestione gruppi **funzionalità del prodotto:** collaborazione

Gli amministratori possono ora configurare un criterio di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica consente di applicare convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare i criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi dei gruppi (ad esempio, "CEO, Payroll, HR").

Per altre informazioni, vedere [applicare criteri di denominazione per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>I log attività di Azure AD sono ora disponibili in monitoraggio di Azure (disponibilità generale)

**Tipo:** Nuova **Categoria servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

Per risolvere i commenti e i suggerimenti sulle visualizzazioni con i log attività Azure AD, viene introdotta una nuova funzionalità Insights in Log Analytics. Questa funzionalità consente di ottenere informazioni approfondite sulle risorse di Azure AD usando i modelli interattivi, denominati cartelle di lavoro. Queste cartelle di lavoro predefinite possono fornire dettagli per app o utenti e includono:

- **Accessi.** Fornisce informazioni dettagliate per app e utenti, tra cui la posizione di accesso, il sistema operativo in uso o il client e la versione del browser e il numero di accessi riusciti o non riusciti.

- **Autenticazione legacy e accesso condizionale.** Fornisce informazioni dettagliate per le app e gli utenti che usano l'autenticazione legacy, incluso Multi-Factor Authentication l'uso attivato dai criteri di accesso condizionale, le app che usano i criteri di accesso condizionale e così via.

- **Analisi degli errori di accesso.** Consente di determinare se gli errori di accesso si verificano a causa di un'azione dell'utente, dei problemi relativi ai criteri o dell'infrastruttura.

- **Report personalizzati.** È possibile creare nuove cartelle di lavoro o modificare quelle esistenti per personalizzare la funzionalità Insights per l'organizzazione.

Per ulteriori informazioni, vedere [come utilizzare le cartelle di lavoro di monitoraggio di Azure per Azure Active Directory report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-aprile 2019

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel 2019 aprile sono state aggiunte le 21 nuove app con supporto federativo per la raccolta di app:

[SAP fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [PERCOLATE](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [benching](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PAGEDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [rstudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [alibaba cloud (SSO basato su ruoli)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [indiggo](https://indiggolead.com/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nuova opzione Frequency revisioni di accesso e selezione di più ruoli

**Tipo:** Nuova **Categoria servizio funzionalità:** verifiche di accesso **funzionalità del prodotto:** governance delle identità

I nuovi aggiornamenti in Azure AD le verifiche di accesso consentono di:

- Modificare la frequenza delle verifiche di accesso in modo **semestrale**, oltre alle opzioni precedentemente esistenti di ogni settimana, ogni mese, ogni trimestre e ogni anno.

- Quando si crea una singola verifica di accesso, selezionare più Azure AD e ruoli delle risorse di Azure. In questa situazione, tutti i ruoli vengono configurati con le stesse impostazioni e tutti i revisori ricevono una notifica allo stesso tempo.

Per altre informazioni su come creare una verifica di accesso, vedere [creare una verifica di accesso di gruppi o applicazioni in Azure ad verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect i sistemi di avvisi tramite posta elettronica sono in fase di transizione e inviano nuove informazioni sul mittente di posta elettronica per alcuni clienti

**Tipo:** **Categoria servizio** funzionalità modificata: ad Sync **funzionalità del prodotto:** piattaforma

Azure AD Connect è in corso la transizione dei sistemi di avviso di posta elettronica, potenzialmente mostrando ad alcuni clienti un nuovo mittente di posta elettronica. Per risolvere questo problema, è necessario aggiungere `azure-noreply@microsoft.com` all'elenco Consenti dell'organizzazione oppure non sarà possibile continuare a ricevere avvisi importanti dai servizi di Office 365, Azure o Sync.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Le modifiche del suffisso UPN hanno ora esito positivo tra domini federati in Azure AD Connect

**Tipo:** **Categoria servizio fisso:** ad Sync **funzionalità del prodotto:** piattaforma

È ora possibile modificare correttamente il suffisso UPN di un utente da un dominio federato a un altro dominio federato in Azure AD Connect. Questa correzione significa che non si dovrebbe più riscontrare il messaggio di errore FederatedDomainChangeError durante il ciclo di sincronizzazione o ricevere un messaggio di posta elettronica di notifica, "Impossibile aggiornare l'oggetto in Azure Active Directory perché l'attributo [FederatedUser. UserPrincipalName] non è valido. Aggiornare il valore nei servizi directory locale ".

Per ulteriori informazioni, vedere [risoluzione degli errori durante la sincronizzazione](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maggiore sicurezza usando i criteri di accesso condizionale basato sulla protezione delle app in Azure AD (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio funzionalità:** accesso condizionale funzionalità del **prodotto:** sicurezza delle identità & protezione

L'accesso condizionale basato sulla protezione delle app è ora disponibile usando il Richiedi criterio di **protezione delle app** . Questo nuovo criterio consente di aumentare la sicurezza dell'organizzazione evitando:

- Gli utenti ottengono l'accesso alle app senza una licenza Microsoft Intune.

- Gli utenti non sono in grado di ottenere i criteri di protezione delle app Microsoft Intune.

- Utenti che accedono alle app senza un criterio di protezione delle app configurato Microsoft Intune.

Per altre informazioni, vedere [come richiedere criteri di protezione delle app per l'accesso alle app cloud con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nuovo supporto per Azure AD Single Sign-On e l'accesso condizionale in Microsoft Edge (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio funzionalità:** accesso condizionale funzionalità del **prodotto:** sicurezza delle identità & protezione

Il supporto Azure AD per Microsoft Edge è stato migliorato, inclusa la fornitura di un nuovo supporto per Azure AD Single Sign-On e l'accesso condizionale. Se in precedenza è stato usato Microsoft Intune Managed Browser, ora è possibile usare Microsoft Edge.

Per altre informazioni sulla configurazione e la gestione di dispositivi e app usando l'accesso condizionale, vedere [richiedere i dispositivi gestiti per l'accesso alle app cloud con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e [richiedere app client approvate per l'accesso alle app cloud con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Per altre informazioni su come gestire l'accesso tramite Microsoft Edge con i criteri di Microsoft Intune, vedere [gestire l'accesso a Internet tramite un browser protetto da criteri Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marzo 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Framework dell'esperienza di identità e supporto dei criteri personalizzati in Azure Active Directory B2C è ora disponibile (GA)

**Tipo:** Nuova **Categoria servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

È ora possibile creare criteri personalizzati in Azure AD B2C, incluse le attività seguenti, che sono supportate su vasta scala e con il contratto di licenza di Azure:

- Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati.

- Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.

- Definire la diramazione condizionale nei percorsi utente.

- Trasformare ed eseguire il mapping delle attestazioni per l'uso in decisioni e comunicazioni in tempo reale.

- Usare i servizi abilitati per l'API REST nei percorsi utente di autenticazione personalizzati. Ad esempio, con provider di posta elettronica, dispongono e sistemi di autorizzazione proprietari.

- Federazione con provider di identità che sono conformi al protocollo OpenIDConnect. Ad esempio, con Azure AD multi-tenant, provider di account di social networking o provider di verifica a due fattori.

Per altre informazioni sulla creazione di criteri personalizzati, vedere [Note per gli sviluppatori per i criteri personalizzati in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leggere [il post di Blog di Alex Simon, inclusi i case study](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-2019 marzo

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel 2019 marzo sono state aggiunte le 14 nuove app con supporto federativo per la raccolta di app:

[ISEC7 mobile Exchange delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [sistema di controllo basato su spiegazione](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nuovi connettori di provisioning di zScaler e Atlassian nella raccolta di Azure AD-2019 marzo

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità del prodotto per il provisioning delle app **:** integrazione di terze parti

Automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per le app seguenti:

[ZScaler](https://aka.ms/ZscalerProvisioning), [zScaler beta](https://aka.ms/ZscalerBetaProvisioning), [zScaler One](https://aka.ms/ZscalerOneProvisioning), [zScaler Two](https://aka.ms/ZscalerTwoProvisioning), [zScaler Three](https://aka.ms/ZscalerThreeProvisioning), [zScaler](https://aka.ms/ZscalerZSCloudProvisioning)ZSCloud, [Atlassian cloud](https://aka.ms/atlassianCloudProvisioning)

Per altre informazioni su come proteggere meglio l'organizzazione tramite il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Ripristinare e gestire i gruppi di Office 365 eliminati nel portale di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** gestione gruppi **funzionalità del prodotto:** collaborazione

È ora possibile visualizzare e gestire i gruppi di Office 365 eliminati dal portale di Azure AD. Questa modifica consente di visualizzare i gruppi disponibili per il ripristino, oltre a consentire l'eliminazione definitiva dei gruppi che non sono necessari per l'organizzazione.

Per ulteriori informazioni, vedere la pagina relativa al [ripristino di gruppi scaduti o eliminati](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Single Sign-on è ora disponibile per Azure AD app locali protette con SAML tramite il proxy di applicazione (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** proxy app: controllo di accesso

È ora possibile fornire un'esperienza di Single Sign-On (SSO) per le app locali con autenticazione SAML, oltre all'accesso remoto a queste app tramite il proxy di applicazione. Per altre informazioni su come configurare l'accesso Single Sign-On SAML con le app locali, vedere [saml Single Sign-on per applicazioni locali con proxy di applicazione (anteprima)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Le app client nei cicli di richiesta verranno interrotte per migliorare l'affidabilità e l'esperienza utente

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Le app client possono emettere in modo errato centinaia delle stesse richieste di accesso in un breve periodo di tempo. Queste richieste, a prescindere dal fatto che abbiano esito positivo o negativo, contribuiscono a una scarsa esperienza utente e a carichi di lavoro accresciuti per l'IDP, aumentando la latenza per tutti gli utenti e riducendo la disponibilità dell'IDP.

Questo aggiornamento Invia un `invalid_grant` errore: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` alle app client che rilasciano richieste duplicate più volte in un breve periodo di tempo, oltre l'ambito del normale funzionamento. Le app client che rilevano questo problema dovrebbero visualizzare un prompt interattivo, richiedendo all'utente di eseguire di nuovo l'accesso. Per ulteriori informazioni su questa modifica e su come correggere l'applicazione se si verifica questo errore, vedere [What ' s New for Authentication?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>È ora disponibile una nuova esperienza utente per i log di controllo

**Tipo:** Modifica della **categoria del servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

È stata creata una nuova pagina di **log di controllo** Azure ad per contribuire a migliorare la leggibilità e la modalità di ricerca delle informazioni. Per visualizzare la pagina nuovi **log di controllo** , selezionare log di **controllo** nella sezione **attività** di Azure ad.

![Pagina nuovi log di controllo con informazioni di esempio](media/whats-new/audit-logs-page.png)

Per ulteriori informazioni sulla nuova pagina **log di controllo** , vedere [report attività di controllo nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nuovi avvisi e indicazioni per impedire il blocco accidentale degli amministratori da criteri di accesso condizionale non configurati correttamente

**Tipo:** Categoria del **servizio** funzionalità modificata: accesso condizionale **-funzionalità del prodotto:** sicurezza delle identità & protezione

Per impedire agli amministratori di bloccarsi accidentalmente dai propri tenant tramite criteri di accesso condizionale configurati in modo non conforme, sono stati creati nuovi avvisi e indicazioni aggiornate nella portale di Azure. Per ulteriori informazioni sulle nuove linee guida, vedere [che cosa sono le dipendenze del servizio in Azure Active Directory l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Miglioramento delle condizioni per l'utilizzo degli utenti finali nei dispositivi mobili

**Tipo:** **Categoria servizio** funzionalità modificata: Condizioni per l'utilizzo **funzionalità del prodotto:** governance

Sono state aggiornate le condizioni per l'utilizzo esistenti per contribuire a migliorare la modalità di revisione e consenso alle condizioni per l'utilizzo in un dispositivo mobile. È ora possibile eseguire lo zoom avanti e indietro, tornare indietro, scaricare le informazioni e selezionare collegamenti ipertestuali. Per ulteriori informazioni sulle condizioni per l'utilizzo aggiornate, vedere [Azure Active Directory la funzionalità condizioni](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)per l'utilizzo.

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nuova esperienza di download dei log attività Azure AD disponibile

**Tipo:** Modifica della **categoria del servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

È ora possibile scaricare grandi quantità di log attività direttamente dalla portale di Azure. Questo aggiornamento consente di:

- Scaricare fino a 250.000 righe.

- Ricevere una notifica al termine del download.

- Personalizzare il nome del file.

- Determinare il formato di output, ovvero JSON o CSV.

Per ulteriori informazioni su questa funzionalità, vedere [Guida introduttiva: scaricare un report di controllo utilizzando il portale di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Modifica di rilievo: aggiornamenti alla valutazione della condizione di Exchange ActiveSync (EAS)

**Tipo:** Pianificare la **categoria del servizio** di modifica: accesso condizionale funzionalità del **prodotto:** controllo di accesso

È in corso l'aggiornamento del modo in cui Exchange ActiveSync (EAS) valuta le condizioni seguenti:

- Località utente, in base a paese, area geografica o indirizzo IP

- Rischio di accesso

- Piattaforma per i dispositivi

Se queste condizioni sono state usate in precedenza nei criteri di accesso condizionale, tenere presente che il comportamento della condizione potrebbe cambiare. Ad esempio, se in precedenza è stata usata la condizione posizione utente in un criterio, è possibile che i criteri vengano ignorati in base alla posizione dell'utente.

---

## <a name="february-2019"></a>Febbraio 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Configurabile Azure AD la crittografia del token SAML (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: SSO

È ora possibile configurare qualsiasi app SAML supportata per ricevere i token SAML crittografati. Se configurata e usata con un'app, Azure AD crittografa le asserzioni SAML emesse usando una chiave pubblica ottenuta da un certificato archiviato nel Azure AD.

Per altre informazioni sulla configurazione della crittografia del token SAML, vedere [configurare Azure ad crittografia dei token SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Creare una verifica di accesso per i gruppi o le app usando le verifiche di accesso Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** verifiche di accesso **funzionalità del prodotto:** governance

È ora possibile includere più gruppi o app in una singola Azure AD verifica di accesso per l'appartenenza al gruppo o l'assegnazione di app. Le verifiche di accesso con più gruppi o app sono configurate con le stesse impostazioni e tutti i revisori inclusi ricevono una notifica allo stesso tempo.

Per altre informazioni su come creare una verifica di accesso usando le verifiche di accesso Azure AD, vedere [creare una verifica di accesso di gruppi o applicazioni in verifiche di accesso Azure ad](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-2019 febbraio

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel febbraio 2019 sono state aggiunte le 27 nuove app con supporto federativo per la raccolta di app:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [Fat Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)Assignment [, Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skywards Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [BrightIdea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), click delle autorizzazioni, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial) [, StoregateSmartFile, Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial) [, Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial) [sismica](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), condivisione di [un sogno](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [WebMethods Integration cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), Knowledge base [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [ou Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [i dati del periscopio](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial) [, smartvid.io, PureCloud](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial) [per](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) [Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registrazione multi-factor authentication combinata avanzata

**Tipo:** Modifica della **categoria del servizio funzionalità:** reimpostazione della password self **-Service funzionalità del prodotto:** autenticazione utente

In risposta ai commenti dei clienti, è stata migliorata l'esperienza combinata di anteprima della registrazione di multi-factor authentication/SSPR, che consente agli utenti di registrare più rapidamente le info di sicurezza per l'autenticazione a più fattori e SSPR.

**Per abilitare l'esperienza avanzata per gli utenti, seguire questa procedura:**

1. In qualità di amministratore globale o Amministratore utenti, accedere al portale di Azure e passare a **Azure Active Directory > impostazioni utente > Gestisci impostazioni per le funzionalità di anteprima del pannello di accesso**.

2. Nell'opzione **utenti che possono usare le funzionalità di anteprima per la registrazione e la gestione delle informazioni di sicurezza-Aggiorna** scegliere di attivare le funzionalità per un **gruppo selezionato di utenti** o per **tutti gli utenti**.

Nelle prossime settimane verrà rimossa la possibilità di abilitare l'esperienza di anteprima della registrazione di autenticazione a più fattori per i tenant che non hanno già attivato.

**Per verificare se il controllo verrà rimosso per il tenant, attenersi alla procedura seguente:**

1. In qualità di amministratore globale o Amministratore utenti, accedere al portale di Azure e passare a **Azure Active Directory > impostazioni utente > Gestisci impostazioni per le funzionalità di anteprima del pannello di accesso**.

2. Se gli **utenti che possono usare l'opzione Anteprima funzionalità per la registrazione e la gestione delle informazioni di sicurezza** sono impostati su **nessuno**, l'opzione verrà rimossa dal tenant.

A prescindere dal fatto che in precedenza sia stata attivata l'esperienza di anteprima della registrazione di multi-factor authentication/SSPR precedente per gli utenti, l'esperienza precedente verrà disattivata in una data futura. Per questo motivo, è consigliabile passare alla nuova esperienza migliorata il prima possibile.

Per ulteriori informazioni sull'esperienza di registrazione avanzata, vedere i miglioramenti apportati [alla Azure ad esperienza combinata di registrazione a più fattori e reimpostazione della password](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Esperienza di gestione dei criteri aggiornata per i flussi utente

**Tipo:** Modifica della **categoria del servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

È stato aggiornato il processo di creazione e gestione dei criteri per i flussi utente (noti in precedenza come criteri predefiniti). Questa nuova esperienza è ora l'impostazione predefinita per tutti i tenant di Azure AD.

È possibile fornire commenti e suggerimenti aggiuntivi usando le Icone Smile o cipiglio nell'area **inviare commenti** e suggerimenti nella parte superiore della schermata del portale.

Per ulteriori informazioni sulla nuova esperienza di gestione dei criteri, vedere il [Azure ad B2C ora dispone di personalizzazione JavaScript e di molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) Blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Scegliere le versioni specifiche degli elementi della pagina fornite da Azure AD B2C

**Tipo:** Nuova **Categoria servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

È ora possibile scegliere una versione specifica degli elementi della pagina forniti da Azure AD B2C. Selezionando una versione specifica, è possibile testare gli aggiornamenti prima che vengano visualizzati in una pagina ed è possibile ottenere un comportamento prevedibile. Inoltre, è ora possibile acconsentire esplicitamente a applicare versioni specifiche della pagina per consentire le personalizzazioni di JavaScript. Per attivare questa funzionalità, passare alla pagina delle **Proprietà** nei flussi utente.

Per ulteriori informazioni sulla scelta di versioni specifiche degli elementi della pagina, vedere il [Azure ad B2C ora dispone di personalizzazione JavaScript e di molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) Blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisiti configurabili per le password dell'utente finale per B2C (GA)

**Tipo:** Nuova **Categoria servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

È ora possibile configurare la complessità delle password dell'organizzazione per gli utenti finali, anziché usare i criteri di Azure AD password nativi. Dal pannello delle **Proprietà** dei flussi utente, noti in precedenza come criteri predefiniti, è possibile scegliere una complessità della password **semplice** o **affidabile**oppure creare un set **personalizzato** di requisiti.

Per ulteriori informazioni sulla configurazione dei requisiti di complessità delle password, vedere [configurare i requisiti di complessità per le password in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nuovi modelli predefiniti per le esperienze di autenticazione personalizzate

**Tipo:** Nuova **Categoria servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

È possibile usare i nuovi modelli predefiniti, situati nel pannello **layout pagina** dei flussi utente (noti in precedenza come criteri predefiniti), per creare un'esperienza di autenticazione personalizzata per gli utenti.

Per ulteriori informazioni sull'utilizzo dei modelli, vedere [Azure ad B2C ora dispone di personalizzazione JavaScript e di molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Gennaio 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Collaborazione B2B di Active Directory tramite l'autenticazione con passcode monouso (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio** funzionalità: **funzionalità del prodotto B2B:** B2B/B2C

È stata introdotta l'autenticazione con passcode monouso (OTP) per autenticare gli utenti guest B2B che non possono essere autenticati tramite altri mezzi quali Azure AD, un account Microsoft (account del servizio gestito) o la federazione di Google. Con questo nuovo metodo di autenticazione, gli utenti guest non devono creare un nuovo account Microsoft. Durante il riscatto di un invito o l'accesso a una risorsa condivisa, un utente guest può invece richiedere l'invio di un codice temporaneo a un indirizzo di posta elettronica. Usando questo codice temporaneo, l'utente guest può continuare ad accedere.

Per altre informazioni, vedere [Autenticazione con passcode monouso tramite indirizzo di posta elettronica (anteprima)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e il blog [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (Azure AD semplifica le condivisioni e le collaborazioni per qualsiasi utente con qualsiasi account).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nuove impostazioni dei cookie di Azure AD Application Proxy

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** proxy app: controllo di accesso

Sono state introdotte tre nuove impostazioni dei cookie, disponibili per le app pubblicate tramite il proxy di applicazione:

- **Usare un cookie solo HTTP.** Consente di impostare il flag **HTTPOnly** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, tra cui la possibilità di evitare la copia o la modifica dei cookie tramite script lato client. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Usa cookie protetto.** Consente di impostare il flag **Secure** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, assicurando che i cookie vengano trasmessi solo su canali TLS sicuri, ad esempio HTTPS. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Usa cookie permanente.** Consente di impedire la scadenza dei cookie di accesso alla chiusura del Web browser. Questi cookie rimangono validi per l'intera durata del token di accesso. I cookie vengono tuttavia reimpostati se viene raggiunta la scadenza o se l'utente elimina manualmente il cookie. È consigliabile mantenere l'impostazione predefinita **No**, attivando l'impostazione solo per app meno recenti che non condividono cookie tra i processi.

Per altre informazioni sui nuovi cookie, vedere [Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Gennaio 2019

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di gennaio 2019, alla raccolta di app sono state aggiunte queste 35 nuove app con il supporto della federazione:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [Expiration Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nuovi miglioramenti ad Azure AD Identity Protection (anteprima pubblica)

**Tipo:** Categoria del **servizio** funzionalità modificata: funzionalità di protezione delle identità del **prodotto:** sicurezza delle identità & protezione

All'offerta di anteprima pubblica di Azure AD Identity Protection sono stati aggiunti i miglioramenti seguenti:

- Interfaccia utente aggiornata e più integrata

- API aggiuntive

- Valutazione migliorata dei rischi tramite Machine Learning

- Allineamento a livello di prodotto per utenti e accessi rischiosi

Per altre informazioni sui miglioramenti, vedere [Informazioni su Azure Active Directory Identity Protection (versione aggiornata)](https://aka.ms/IdentityProtectionDocs). È possibile ottenere informazioni aggiuntive e condividere le proprie opinioni tramite i prompt nel prodotto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nuova funzionalità di blocco dell'app per l'app Microsoft Authenticator in dispositivi iOS e Android

**Tipo:** Nuova **Categoria servizio funzionalità:** Microsoft Authenticator **funzionalità del prodotto App:** sicurezza & di identità protezione

Per rendere più sicuri i passcode monouso, le informazioni sull'app e le impostazioni dell'app, è possibile attivare la funzionalità di blocco dell'app nell'app Microsoft Authenticator. Se si attiva blocco app, verrà richiesto di eseguire l'autenticazione usando il PIN o la biometria ogni volta che si apre l'app Microsoft Authenticator.

Per altre informazioni, vedere [Domande frequenti sull'app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Miglioramenti alle funzionalità di esportazione di Azure AD Privileged Identity Management (PIM)

**Tipo:** Nuova **Categoria servizio funzionalità:** Privileged Identity Management **funzionalità del prodotto:** Privileged Identity Management

Gli amministratori di Privileged Identity Management (PIM) possono ora esportare tutte le assegnazioni di ruolo attive e idonee per una risorsa specifica, incluse le assegnazioni di ruolo per tutte le risorse figlio. In precedenza risultava difficile per gli amministratori ottenere un elenco completo di assegnazioni di ruolo per una sottoscrizione ed era necessario esportare le assegnazioni di ruolo per ogni risorsa specifica.

Per altre informazioni, vedere [Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Novembre/Dicembre 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Gli utenti non rimossi dall'ambito di sincronizzazione non passano più ad account di tipo solo cloud

**Tipo:** **Categoria servizio fisso:** funzionalità del **prodotto** gestione utenti: directory

>[!Important]
>Microsoft ha ascoltato e compreso i commenti di insoddisfazione dei clienti in merito a questa correzione. Ha quindi ripristinato lo stato antecedente alla modifica finché non sarà più facile per i clienti implementare questa correzione nella loro organizzazione.

È stato corretto un bug in cui il flag DirSyncEnabled di un utente viene erroneamente impostato su **false** quando l'oggetto Active Directory Domain Services (ad DS) è stato escluso dall'ambito di sincronizzazione e quindi spostato nel cestino Azure ad nel seguente ciclo di sincronizzazione. In seguito a questa correzione, se l'utente viene escluso dall'ambito di sincronizzazione e successivamente ripristinato dal Cestino di Azure AD, l'account utente resta sincronizzato da AD locale come previsto e non può essere gestito nel cloud poiché la relativa origine di autorità rimane AD locale.

Prima di questa correzione si verificava un problema quando il flag DirSyncEnabled veniva impostato su False. Veniva data l'erronea l'impressione che questi account fossero stati convertiti in oggetti solo cloud e potessero essere gestiti solo nel cloud. Tuttavia, gli account mantenevano l'origine di autorità come locale e tutte le proprietà sincronizzate (attributi shadow) provenienti da AD locale. Questa condizione causava vari problemi in Azure AD e in altri carichi di lavoro cloud, ad esempio Exchange Online, che prevedevano di gestire questi account come sincronizzati da AD, ma che si comportavano ora come account solo cloud.

Attualmente, l'unico modo per convertire realmente un account sincronizzato da AD in un account solo cloud è disabilitare DirSync a livello di tenant, attivando così un'operazione di back-end per trasferire l'origine di autorità. Questo tipo di modifica dell'origine di autorità richiede, tra l'altro, la pulizia di tutti gli attributi locali correlati (ad esempio LastDirSyncTime e gli attributi shadow) e l'invio di un segnale ad altri carichi di lavoro cloud perché convertano anch'essi il rispettivo oggetto in un account solo cloud.

Questa correzione, di conseguenza, consente di evitare gli aggiornamenti diretti dell'attributo ImmutableID di un utente sincronizzato da AD, che in precedenza erano necessari in alcuni scenari. Per impostazione predefinita, l'attributo ImmutableID di un oggetto in Azure AD, come suggerisce il nome, deve essere immutabile. Per questi scenari sono disponibili nuove funzionalità implementate in Azure AD Connect Health e nel client di sincronizzazione di Azure AD Connect:

- **Aggiornamento di ImmutableID su larga scala per molti utenti in un approccio a fasi**

  Ad esempio, è necessario eseguire una lunga migrazione tra foreste di Active Directory Domain Services. Soluzione: usare Azure AD Connect per **configurare l'ancoraggio di origine** e, quando l'utente esegue la migrazione, copiare i valori ImmutableId esistenti da Azure ad nell'attributo ms-DS-coerenza-GUID dell'utente di Active Directory Domain Services locale della nuova foresta. Per altre informazioni, vedere [Uso di ms-DS-ConsistencyGuid come sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Aggiornamenti di ImmutableID su larga scala per molti utenti in un unico passaggio**

  Ad esempio, se durante l'implementazione di Azure AD Connect si commette un errore ed è necessario modificare l'attributo SourceAnchor. Soluzione: disabilitare DirSync a livello di tenant e deselezionare tutti i valori ImmutableID non validi. Per altre informazioni, vedere [Disabilitare la sincronizzazione della directory per Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Abbinare di nuovo un utente locale e un utente esistente in Azure AD** Ad esempio, un utente che è stato ricreato in Active Directory Domain Services genera un duplicato nell'account Azure AD anziché essere abbinato di nuovo a un account Azure AD esistente (oggetto orfano). Soluzione: usare Azure AD Connect Health nel portale di Azure per modificare il mapping dell'ancoraggio di origine/ImmutableID. Per altre informazioni, vedere [Scenario con oggetto orfano](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Modifica di rilievo: aggiornamenti allo schema dei log di controllo e accesso tramite monitoraggio di Azure

**Tipo:** Modifica della **categoria del servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

Sono attualmente in fase di pubblicazione flussi di log di controllo e accesso tramite Monitoraggio di Azure, per poter integrare uniformemente i file di log con gli strumenti di informazioni di sicurezza e gestione degli eventi o con Log Analytics. In base ai commenti e suggerimenti degli utenti e in preparazione all'annuncio della disponibilità generale di questa funzionalità, Microsoft sta apportando le modifiche seguenti allo schema. Queste modifiche dello schema e i relativi aggiornamenti alla documentazione verranno completati entro la prima settimana di gennaio.

#### <a name="new-fields-in-the-audit-schema"></a>Nuovi campi nello schema di controllo
Sta per essere aggiunto il nuovo campo **Tipo di operazione**, che permetterà di specificare il tipo di operazione eseguita sulla risorsa. Ad esempio, **aggiunta**, **aggiornamento** o **eliminazione**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campi modificati nello schema di controllo
È in corso la modifica dei campi seguenti nello schema di controllo:

|Nome del campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|Categoria|Campo **Nome servizio** nelle versioni precedenti. Si tratta ora del campo **Audit Categories** (Categorie di controllo). Il campo **Nome del servizio** è stato rinominato in **loggedByService**.|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione password self-service</li></ul>|<ul><li>User Management</li><li>Gestione di gruppi</li><li>Gestione app</li></ul>|
|targetResources|Include **TargetResourceType** al livello superiore.|&nbsp;|<ul><li>Policy</li><li>App</li><li>Utente</li><li>Gruppo</li></ul>|
|loggedByService|Specifica il nome del servizio che ha generato il log di controllo.|Null|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione della password self-service</li></ul>|
|Risultato|Indica il risultato dei log di controllo. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li></ul>|<ul><li>Operazione completata</li><li>Errore</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campi modificati nello schema di accesso
È in corso la modifica dei campi seguenti nello schema di accesso:

|Nome del campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Campo **conditionalaccessPolicies** nelle versioni precedenti. Si tratta ora del campo **appliedConditionalAccessPolicies**.|Nessuna modifica|Nessuna modifica|
|conditionalAccessStatus|Indica il risultato dello stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Operazione completata</li><li>Errore</li><li>Non applicato</li><li>Disabilitata</li></ul>|
|appliedConditionalAccessPolicies: risultato|Indica il risultato del singolo stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Operazione completata</li><li>Errore</li><li>Non applicato</li><li>Disabilitata</li></ul>|

Per altre informazioni sullo schema, vedere [Interpretare lo schema dei log di controllo di Azure AD in Monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Miglioramenti relativi a Identity Protection apportati al modello di apprendimento automatico supervisionato e al motore dei punteggi di rischio

**Tipo:** **Categoria servizio** funzionalità modificata: funzionalità del **prodotto** Identity Protection: punteggi di rischio

I miglioramenti apportati al motore di valutazione dei punteggi di rischio utente e di accesso in relazione a Identity Protection ottimizzano la copertura e la precisione del rischio utente. Gli amministratori potrebbero notare che il livello di rischio utente non è più direttamente collegato al livello di rischio di rilevamenti specifici e che vi è stato un aumento nel numero e nel livello di eventi di accesso rischiosi.

I rilevamenti dei rischi sono ora valutati dal modello di Machine Learning supervisionato, che calcola il rischio utente usando funzionalità aggiuntive degli accessi dell'utente e un modello di rilevamenti. Attraverso questo modello l'amministratore può individuare gli utenti con punteggi di rischio elevato, anche se i rilevamenti associati all'utente indicano un rischio basso o medio.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Possibilità per gli amministratori di reimpostare la propria password tramite l'app Microsoft Authenticator (anteprima pubblica)

**Tipo:** Modifica della **categoria del servizio funzionalità:** reimpostazione della password self **-Service funzionalità del prodotto:** autenticazione utente

Gli amministratori di Azure AD possono ora reimpostare la propria password seguendo le notifiche dell'app Microsoft Authenticator o tramite un codice da qualsiasi app di autenticazione per dispositivi mobili o token hardware. Per reimpostare la propria password, gli amministratori possono ora usare due metodi diversi:

- Notifica dell'app Microsoft Authenticator

- Altro codice di token hardware/app di autenticazione per dispositivi mobili

- Email

- Chiamata telefonica

- SMS

Per altre informazioni sull'uso dell'app Microsoft Authenticator per reimpostare le password, vedere [Reimpostazione della password self-service di Azure AD - App per dispositivi mobili e SSPR (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nuovo ruolo Amministratore dispositivo cloud di Azure AD (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio funzionalità:** registrazione del dispositivo e gestione funzionalità del **prodotto:** controllo di accesso

Gli amministratori possono assegnare agli utenti il nuovo ruolo Amministratore dispositivo cloud per l'esecuzione di attività di amministrazione dei dispositivi cloud. Gli utenti assegnati al ruolo Amministratore dispositivo cloud possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure.

Per altre informazioni sui ruoli e sulle autorizzazioni, vedere [Assegnazione di ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gestione dei dispositivi tramite il nuovo timestamp di attività in Azure AD (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio funzionalità:** registrazione e gestione del dispositivo funzionalità del **prodotto:** gestione del ciclo di vita del dispositivo

Ci rendiamo conto che nel corso del tempo è necessario aggiornare e ritirare i dispositivi delle organizzazioni in Azure AD, per evitare di avere dispositivi non aggiornati nell'ambiente in uso. Per semplificare questo processo, Azure AD aggiorna ora i dispositivi con un nuovo timestamp di attività, che permette di gestirne il ciclo di vita.

Per altre informazioni su come ottenere e usare questo timestamp, vedere [procedura: gestire i dispositivi non aggiornati in Azure ad](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Gli amministratori possono richiedere agli utenti di accettare le condizioni per l'utilizzo in ogni dispositivo

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** governance

Gli amministratori possono ora attivare l'opzione **Richiedi agli utenti di acconsentire su ogni dispositivo** per richiedere agli utenti di accettare le condizioni per l'utilizzo in ogni dispositivo usato nel tenant.

Per ulteriori informazioni, vedere la [sezione Condizioni per l'utilizzo per ogni dispositivo della funzionalità Azure Active Directory condizioni per l'utilizzo](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Gli amministratori possono configurare le condizioni per l'utilizzo per scadere in base a una pianificazione ricorrente

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** governance


Gli amministratori possono ora attivare l'opzione di **scadenza dei consensi** per fare in modo che le condizioni per l'utilizzo scadano per tutti gli utenti in base alla pianificazione ricorrente specificata. La pianificazione può essere annuale, biennale, trimestrale o mensile. Una volta terminate le condizioni per l'utilizzo, gli utenti devono riaccettarle.

Per ulteriori informazioni, vedere la [sezione aggiungere le condizioni per l'utilizzo della funzionalità Azure Active Directory condizioni](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)per l'utilizzo.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Gli amministratori possono configurare le condizioni per l'utilizzo per scadere in base alla pianificazione di ogni utente

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** governance

Gli amministratori possono ora specificare una durata per la quale l'utente deve accettare nuovamente le condizioni per l'utilizzo. Ad esempio, gli amministratori possono specificare che gli utenti devono accettare nuovamente le condizioni per l'utilizzo ogni 90 giorni.

Per ulteriori informazioni, vedere la [sezione aggiungere le condizioni per l'utilizzo della funzionalità Azure Active Directory condizioni](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)per l'utilizzo.

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nuovi messaggi di posta elettronica di Azure AD Privileged Identity Management (PIM) per ruoli di Azure Active Directory

**Tipo:** Nuova **Categoria servizio funzionalità:** Privileged Identity Management **funzionalità del prodotto:** Privileged Identity Management

I clienti che usano Azure AD Privileged Identity Management (PIM) possono ora ricevere un messaggio di posta elettronica di riepilogo settimanale, che include le informazioni seguenti per gli ultimi sette giorni:

- Panoramica delle assegnazioni di ruoli permanenti e idonei principali

- Numero di utenti che attivano ruoli

- Numero di utenti assegnati a ruoli in PIM

- Numero di utenti assegnati a ruoli al di fuori di PIM

- Numero di utenti "resi permanenti" in PIM

Per altre informazioni su PIM e sulle notifiche di posta elettronica disponibili, vedere [Notifiche tramite posta elettronica in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licenze basate sui gruppi ora disponibili a livello generale

**Tipo:** **Categoria servizio** funzionalità modificata: altra **funzionalità del prodotto:** directory

Le licenze basate sui gruppi sono ora in anteprima pubblica e disponibili a livello generale. Nell'ambito della disponibilità generale questa funzionalità è stata resa più scalabile e sono state aggiunte la possibilità di rielaborare assegnazioni di licenze basate sui gruppi per un singolo utente e quella di usare licenze basate sui gruppi con licenze di Office 365 E3/A3.

Per altre informazioni sulle licenze basate sui gruppi, vedere [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Novembre 2018

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di novembre 2018 sono state aggiunte alla raccolta di app queste 26 nuove app con supporto per la federazione:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps - Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps - UX](https://cloud.plex.com/sso), [Plex Apps - IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Ottobre 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Log di Azure AD ora funziona con Azure Log Analytics (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

Siamo lieti di annunciare che è ora possibile inoltrare i log di Azure AD ad Azure Log Analytics. Questa funzionalità molto richiesta consente di fornire un accesso ancora migliore ad analisi per l'azienda, operazioni e sicurezza, nonché un modo per monitorare l'infrastruttura. Per altre informazioni, consultare il blog [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) (Log attività di Azure Active Directory in Azure Log Analytics ora disponibile).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Ottobre 2018

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di ottobre 2018 sono state aggiunte 14 nuove app con il supporto della federazione alla raccolta di app:

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notifiche tramite posta elettronica di Azure AD Domain Services

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad Domain Services **funzionalità del prodotto:** Azure ad Domain Services

Azure AD Domain Services offre la funzionalità di avvisi relativi ad errori o problemi di configurazione sul portale di Azure. Questi avvisi includono le procedure dettagliate per tentare di risolvere i problemi senza dover contattare l'assistenza.

A partire da ottobre, sarà possibile personalizzare le impostazioni di notifica per il dominio gestito, in modo che quando vengono generati nuovi avvisi, viene inviato un messaggio di posta elettronica a un gruppo di utenti specificato, eliminando la necessità di controllare continuamente il portale per verificare la presenza di aggiornamenti.

Per altre informazioni, vedere [Impostazioni di notifica in Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Il portale di Azure AD supporta l'uso dell'API di dominio ForceDelete per eliminare i domini personalizzati

**Tipo:** Modifica della **categoria del servizio** funzionalità: **directory**

Siamo lieti di annunciare che è ora possibile usare l'API di dominio ForceDelete per eliminare i nomi di dominio personalizzati tramite la ridenominazione asincrona dei riferimenti, ad esempio utenti, gruppi e app dal nome di dominio personalizzato (contoso.com) al nome di dominio predefinito iniziale (contoso.onmicrosoft.com).

Questa modifica consente di eliminare in modo più rapido i nomi di dominio personalizzati se l'organizzazione non usa più il nome o se deve usare il nome di dominio in un'altra directory di Azure AD.

Per altre informazioni, vedere [Delete a custom domain name](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name) (Eliminare un nome di dominio personalizzato).

---

## <a name="september-2018"></a>Settembre 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Autorizzazioni del ruolo di amministratore aggiornate per i gruppi dinamici

**Tipo:** **Categoria di servizio fisso:** gestione dei gruppi funzionalità del **prodotto:** collaborazione

È stato risolto un problema e ora i ruoli di amministratore specifici possono creare e aggiornare le regole di appartenenza dinamica, senza la necessità di essere il proprietario del gruppo.

I ruoli sono:

- Amministratore globale

- Amministratore di Intune

- Amministratore utenti

Per ulteriori informazioni, vedere [creare un gruppo dinamico e verificare lo stato](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Impostazioni di configurazione di Single Sign-On (SSO) semplificate per alcune app di terze parti

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: SSO

È risaputo che la configurazione di Single Sign-On (SSO) per le app software come un servizio (SaaS, Software as a Service) può risultare complessa a causa della natura unica della configurazione di ogni app. È stata creata un'esperienza di configurazione semplificata per popolare automaticamente le impostazioni di configurazione di SSO per le app SaaS di terze parti seguenti:

- Zendesk

- ArcGis Online

- Jamf Pro

Per iniziare a usare questa esperienza con un clic, passare alla pagina di configurazione di **portale di Azure**  >  **SSO** per l'app. Per altre informazioni, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory - Pagina Dove vengono archiviati i tuoi dati?

**Tipo:** Nuova **Categoria servizio funzionalità:** altra **funzionalità del prodotto:** GoLocal

Selezionare l'area della società nella pagina **Azure Active Directory - Dove vengono archiviati i tuoi dati** per visualizzare il data center di Azure che ospita i dati inattivi di Azure AD per tutti i servizi di Azure AD. È possibile filtrare le informazioni in base ai servizi di Azure AD specifici per l'area della società.

Per accedere a questa funzionalità e per altre informazioni, vedere [Azure Active Directory - Dove vengono archiviati i tuoi dati](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nuovo piano di distribuzione disponibile per il pannello di accesso App personali

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app personali: SSO

Vedere il nuovo piano di distribuzione disponibile per il pannello di accesso App personali (https://aka.ms/deploymentplans).
Il pannello di accesso App personali fornisce agli utenti un'unica posizione per trovare le app e accedervi. Questo portale offre agli utenti anche l'opportunità di eseguire attività self-service, ad esempio richiedere l'accesso ad app e gruppi o gestire l'accesso a queste risorse per conto di altri.

Per altre informazioni, vedere [Informazioni sul portale delle app personali](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction).

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nuova scheda Risoluzione dei problemi e supporto nella pagina Accessi del portale di Azure

**Tipo:** Nuova **Categoria servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

La nuova scheda **risoluzione dei problemi e supporto** nella pagina **accessi** del portale di Azure è destinata agli amministratori e ai tecnici del supporto per la risoluzione dei problemi relativi agli accessi Azure ad. Questa nuova scheda fornisce il codice di errore, il messaggio di errore e le raccomandazioni per la correzione (se presenti) per facilitare la risoluzione del problema. Se il problema non è risolvibile, è anche possibile creare un ticket di supporto usando l'esperienza **Copia negli Appunti**, che popola i campi **ID richiesta** e **Data (UTC)** per il file di log nel ticket di supporto.

![Log di accesso che mostrano la nuova scheda](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Supporto migliorato per le proprietà di estensione personalizzate usate per creare regole di appartenenza dinamica

**Tipo:** **Categoria servizio** funzionalità modificata: funzionalità del **prodotto** gestione gruppi: collaborazione

Con questo aggiornamento, è ora possibile fare clic sul collegamento **Ottieni proprietà di estensione personalizzate** nel generatore di regole di assegnazione dei gruppi utenti dinamica, immettere l'ID app univoco e ricevere l'elenco completo di proprietà di estensione personalizzate da usare quando si crea una regola di appartenenza dinamica per gli utenti. È anche possibile aggiornare questo elenco per ottenere tutte le nuove proprietà di estensione personalizzate per l'app.

Per altre informazioni sull'uso delle proprietà di estensione personalizzate per le regole di appartenenza dinamica, vedere [Proprietà di estensione e proprietà di estensione personalizzate](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app Azure AD

**Tipo:** Pianificare la **categoria del servizio** di modifica: accesso condizionale funzionalità del **prodotto:** sicurezza e protezione delle identità

Le app seguenti sono nell'elenco delle [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft To-Do

- Microsoft Stream

Per altre informazioni, vedere:

- [Accesso condizionale basato su app Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nuovo supporto per la reimpostazione della password self-service dalla schermata di blocco di Windows 7/8/8.1

**Tipo:** Nuova **Categoria servizio funzionalità:** **funzionalità del prodotto SSPR:** autenticazione utente

Dopo aver configurato questa nuova funzionalità, gli utenti visualizzeranno un collegamento per reimpostare la password dalla schermata **Blocco** di un dispositivo che esegue Windows 7, Windows 8 o Windows 8.1. Facendo clic su tale collegamento, viene avviato lo stesso flusso di reimpostazione della password del Web browser.

Per altre informazioni, vedere [Come abilitare la reimpostazione della password da Windows 7, 8 e 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Avviso di modifica: i codici di autorizzazione non sono più disponibili per essere usati nuovamente

**Tipo:** Pianificare la **categoria del servizio** di modifica: autenticazione (accessi) funzionalità del **prodotto:** autenticazione utente

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Un'app che provi a usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per questa e altre modifiche relative ai protocolli, vedere [l'elenco completo delle novità nell'ambito dell'autenticazione](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Settembre 2018

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di settembre 2018 sono state aggiunte 16 nuove app con il supporto della federazione alla raccolta di app:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Supporto per i metodi aggiuntivi di trasformazione delle attestazioni

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: SSO

Sono stati introdotti nuovi metodi di trasformazione delle attestazioni, ToLower() e ToUpper(), che possono essere applicati ai token SAML dalla pagina **Configurazione Single Sign-on** basata su SAML.

Per altre informazioni, vedere [Come personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interfaccia utente di configurazione delle app basate su SAML aggiornata (anteprima)

**Tipo:** Modifica della **categoria del servizio** funzionalità: **funzionalità del prodotto** app aziendali: SSO

Caratteristiche dell'interfaccia utente di configurazione delle app basate su SAML aggiornata:

- Esperienza aggiornata per la configurazione delle app basate su SAML.

- Maggiore visibilità sugli elementi mancanti o non corretti nella configurazione.

- Possibilità di aggiungere più indirizzi di posta elettronica per la notifica della scadenza dei certificati.

- Nuovi metodi di trasformazione delle attestazioni, ToLower () e ToUpper (), e altro ancora.

- Possibilità di caricare il proprio certificato per la firma di token per le app aziendali.

- Possibilità di impostare il formato di NameID per le app SAML e di impostare il valore di NameID come estensioni della directory.

Per attivare questa visualizzazione aggiornata, fare clic sul collegamento **Prova la nuova esperienza** nella parte superiore della pagina **Single Sign-On**. Per altre informazioni, vedere [Esercitazione: Configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Agosto 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Modifiche agli intervalli di indirizzi IP di Azure Active Directory

**Tipo:** Pianificare la **categoria del servizio** di modifica: altra funzionalità del **prodotto:** piattaforma

È in corso l'introduzione di intervalli IP più ampi in Azure AD. Ciò indica che se gli intervalli di indirizzi IP di Azure AD per i firewall, i router o i gruppi di sicurezza di rete sono già stati configurati, è necessario aggiornarli. Questo aggiornamento ha lo scopo di evitare altre modifiche alla configurazione degli intervalli IP dei firewall, dei router o dei gruppi di sicurezza di rete quando Azure AD aggiunge nuovi endpoint.

Il traffico di rete passerà a questi nuovi intervalli nel corso dei prossimi due mesi. Per garantirsi la continuità del servizio, è necessario aggiungere questi valori aggiornati agli indirizzi IP prima del 10 settembre 2018:

- 20.190.128.0/18

- 40.126.0.0/18

È consigliabile rimuovere gli intervalli di indirizzi IP precedenti solo dopo che tutto il traffico di rete è passato ai nuovi intervalli. Per aggiornamenti sul passaggio e per informazioni su quando rimuovere gli intervalli precedenti, vedere [Office 365 URLs and IP address ranges](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) (URL e intervalli di indirizzi IP di Office 365).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Avviso di modifica: i codici di autorizzazione non sono più disponibili per essere usati nuovamente

**Tipo:** Pianificare la **categoria del servizio** di modifica: autenticazione (accessi) funzionalità del **prodotto:** autenticazione utente

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Un'app che provi a usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per questa e altre modifiche relative ai protocolli, vedere [l'elenco completo delle novità nell'ambito dell'autenticazione](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gestione convergente delle informazioni di sicurezza per la reimpostazione self-service della password e l'autenticazione MFA (Multi-Factor Authentication)

**Tipo:** Nuova **Categoria servizio funzionalità:** **funzionalità del prodotto SSPR:** autenticazione utente

Questa nuova funzionalità consente agli utenti di gestire le informazioni di sicurezza (ad esempio numero di telefono, app per dispositivi mobili e così via) per la reimpostazione password self-service e Multi-Factor Authentication in un'unica posizione e con un'unica esperienza, a differenza della situazione precedente in cui l'operazione veniva eseguita in due posizioni diverse.

Questa esperienza convergente si applica anche agli utenti che usano la reimpostazione password self-service o Multi-Factor Authentication. Se l'organizzazione non prevede la registrazione per Multi-Factor Authentication o la reimpostazione password self-service, gli utenti possono comunque registrare qualsiasi metodo correlato alle informazioni di sicurezza consentito dall'organizzazione nel portale App personali.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Gli amministratori possono attivare la nuova esperienza (se necessario) per tutti gli utenti di un tenant o solo per un gruppo selezionato. Per altre informazioni sull'esperienza convergente, vedere il blog sull'[esperienza convergente](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nuova impostazione HTTP-Only cookies (Cookie solo HTTP) nelle app del proxy di applicazione di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** proxy app: controllo di accesso

Nelle app del proxy di applicazione è presente una nuova impostazione denominata **HTTP-Only Cookies** (Cookie solo HTTP). Questa impostazione consente di fornire sicurezza aggiuntiva includendo il flag HTTPOnly nell'intestazione della risposta HTTP per entrambi i cookie di sessione e di accesso al proxy di applicazione, interrompendo l'accesso al cookie da uno script lato client ed evitando ulteriormente azioni come la copia o la modifica del cookie. Anche se questo flag non è stato usato in precedenza, i cookie sono sempre stati crittografati e trasmessi usando una connessione TLS per facilitare la protezione da modifiche non corrette.

Questa impostazione non è compatibile con le app che usano i controlli ActiveX, ad esempio Desktop remoto. In questo caso è consigliabile disattivare l'impostazione.

Per altre informazioni sull'impostazione HTTP-Only Cookies (Cookie solo HTTP), vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) per le risorse di Azure supporta i tipi di risorsa dei gruppi di gestione

**Tipo:** Nuova **Categoria servizio funzionalità:** Privileged Identity Management **funzionalità del prodotto:** Privileged Identity Management

Le impostazioni JIT e di assegnazione possono ora essere applicate ai tipi di risorsa dei gruppi di gestione, in modo analogo a come accade per sottoscrizioni, gruppi di risorse e risorse (ad esempio macchine virtuali, Servizi app e altro ancora). Tutti gli utenti con un ruolo che consente a un amministratore di accedere a un gruppo di gestione possono individuare e gestire tale risorsa in PIM.

Per altre informazioni su PIM e sulle risorse di Azure, vedere [Individuare e gestire le risorse di Azure usando Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>L'accesso alle applicazioni (in anteprima) consente di accedere più rapidamente al portale di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** Privileged Identity Management **funzionalità del prodotto:** Privileged Identity Management

L'attivazione di un ruolo tramite PIM può richiedere ora oltre 10 minuti prima che le autorizzazioni diventino effettive. Se si sceglie di usare l'accesso alle applicazioni, ora in versione di anteprima pubblica, gli amministratori possono accedere al portale di Azure AD non appena viene completata la richiesta di attivazione.

L'accesso alle applicazioni supporta attualmente solo l'esperienza del portale di Azure AD e le risorse di Azure. Per altre informazioni su PIM e l'accesso alle applicazioni, vedere [Che cos'è Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Agosto 2018

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di agosto 2018 sono state aggiunte 16 nuove app con il supporto della federazione alla raccolta di app:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Il supporto nativo di Tableau è ora disponibile nel proxy di applicazione di Azure AD

**Tipo:** **Categoria servizio** funzionalità modificata: funzionalità del **prodotto** proxy app: controllo di accesso

Con l'aggiornamento dal protocollo OpenID Connect al protocollo OAuth 2.0 Code Grant per il nostro protocollo di autenticazione, non è più necessario eseguire alcuna configurazione aggiuntiva per usare Tableau con il proxy di applicazione. Questa modifica al protocollo consente anche al proxy di applicazione di migliorare il supporto di app più moderne usando solo reindirizzamenti HTTP, supportati in genere in tag JavaScript e HTML.

Per altre informazioni sul supporto nativo per Tableau, vedere [Azure AD Application Proxy now with native Tableau support](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support) (Proxy di applicazione di Azure AD con il supporto nativo di Tableau).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nuovo supporto per aggiungere Google come provider di identità per utenti guest B2B in Azure Active Directory (anteprima)

**Tipo:** Nuova **categoria di servizio** funzionalità: **funzionalità del prodotto B2B:** B2B/B2C

Se si configura la federazione con Google nell'organizzazione, è possibile consentire agli utenti Gmail invitati di accedere alle app e alle risorse condivise tramite i propri account Google esistenti senza la necessità di creare un account Microsoft personale o un account Azure AD.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Per altre informazioni sulla federazione di Google, vedere [Add Google as an identity provider for B2B guest users](https://docs.microsoft.com/azure/active-directory/b2b/google-federation) (Aggiungere Google come provider di identità per utenti guest B2B).

---

## <a name="july-2018"></a>Luglio 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Miglioramenti alle notifiche di posta elettronica di Azure Active Directory

**Tipo:** **Categoria servizio** funzionalità modificata: altra **funzionalità del prodotto:** gestione del ciclo di vita delle identità

I messaggi di posta elettronica di Azure Active Directory (Azure AD) ora presentano una struttura grafica aggiornata, nonché modifiche all'indirizzo di posta elettronica e al nome visualizzato del mittente, quando vengono inviati dai servizi seguenti:

- Verifiche di accesso di Azure AD
- Azure AD Connect Health
- Azure AD Identity Protection
- Gestione identità con privilegi di Azure AD
- Notifiche di certificati in scadenza di app aziendali
- Notifiche del servizio di provisioning di app aziendali

Le notifiche di posta elettronica verranno inviate dall'indirizzo di posta elettronica e dal nome visualizzato seguenti:

- Indirizzo di posta elettronica: azure-noreply@microsoft.com
- Nome visualizzato: Microsoft Azure

Per esempi della nuova struttura grafica dei messaggi di posta elettronica e per altre informazioni, vedere [Email notifications in Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832) (Notifiche di posta elettronica in Azure AD PIM).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>I log attività di Azure AD ora sono disponibili tramite Monitoraggio di Azure

**Tipo:** Nuova **Categoria servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

I log attività di Azure AD ora sono disponibili in anteprima pubblica per Monitoraggio di Azure (il servizio monitoraggio di Azure a livello di piattaforma). Monitoraggio di Azure offre funzionalità di conservazione a lungo termine e un'integrazione senza problemi, oltre ai miglioramenti seguenti:

- Conservazione a lungo termine tramite il routing dei file di log all'account di Archiviazione di Azure.

- Completa integrazione con i sistemi SIEM, senza che sia necessario scrivere o gestire script personalizzati.

- Completa integrazione con soluzioni personalizzate, strumenti di analisi o soluzioni di gestione degli eventi imprevisti.

Per altre informazioni sulle nuove funzionalità, vedere il blog relativo alla [disponibilità in anteprima pubblica dei log attività di Azure AD nella diagnostica di Monitoraggio di Azure](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e la documentazione [Log attività di Azure Active Directory in Monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informazioni sull'accesso condizionale aggiunte al report degli accessi Azure AD

**Tipo:** Nuova **categoria di servizi funzionalità:** report funzionalità del **prodotto:** sicurezza delle identità & protezione

Questo aggiornamento consente di visualizzare i criteri che vengono valutati quando un utente esegue l'accesso, insieme al risultato dei criteri. Inoltre, il report ora include il tipo di app client usato dall'utente, quindi è possibile identificare il traffico dei protocolli legacy. È inoltre possibile cercare tra le voci del report un ID di correlazione, che è disponibile nel messaggio di errore per l'utente finale e può essere usato per identificare e risolvere i problemi della richiesta di accesso corrispondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visualizzazione delle autenticazioni legacy tramite i log attività di accesso

**Tipo:** Nuova **Categoria servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

Con l'introduzione del campo **App client** nei log attività di accesso, i clienti possono ora vedere gli utenti che usano autenticazioni legacy. I clienti saranno in grado di accedere a queste informazioni tramite l'API Microsoft Graph di accesso o tramite i log delle attività di accesso nel portale di Azure AD, in cui è possibile usare il controllo **app client** per filtrare le autenticazioni legacy. Per altre informazioni, vedere la documentazione.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Luglio 2018

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di luglio 2018 sono state aggiunte queste 16 nuove app con il supporto della federazione alla raccolta di app:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nuove integrazioni di app SaaS per il provisioning utenti - Luglio 2018

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità del prodotto per il provisioning delle app **:** integrazione di terze parti

Azure AD permette di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni SaaS, come Dropbox, Salesforce, ServiceNow e altre. A luglio 2018 è stato aggiunto il supporto per il provisioning utenti per le applicazioni seguenti nella raccolta di app Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Per un elenco di tutte le applicazioni che supportano il provisioning utenti nella raccolta di Azure AD, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync: un modo più semplice per risolvere gli errori di sincronizzazione di attributi orfani e duplicati

**Tipo:** Nuova **Categoria servizio funzionalità:** ad Connect **funzionalità del prodotto:** monitoraggio & Reporting

Azure AD Connect Health introduce funzionalità di correzione in modalità self-service che consentono di evidenziare e correggere gli errori di sincronizzazione. Questa funzionalità consente di risolvere gli errori di sincronizzazione degli attributi duplicati e di correggere gli oggetti resi orfani da Azure AD. Questa diagnostica offre i vantaggi seguenti:

- Consente di limitare gli errori di sincronizzazione degli attributi duplicati, fornendo correzioni specifiche

- Applica una correzione per gli scenari di Azure AD dedicati, consentendo la risoluzione degli errori in un unico passaggio

- Per attivare e usare questa funzionalità, non sono richiesti aggiornamenti o configurazioni

Per altre informazioni, vedere [Diagnosticare e correggere gli errori di sincronizzazione di attributi duplicati](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Aggiornamenti dell'interfaccia utente per le esperienze di accesso ad Azure AD e con account del servizio gestito

**Tipo:** **Categoria servizio** funzionalità modificata: Azure ad **funzionalità del prodotto:** autenticazione utente

È stata aggiornata l'interfaccia utente per l'esperienza di accesso dei servizi online di Microsoft, ad esempio per Office 365 e Azure. Questa modifica rende le schermate più semplici e lineari. Per altre informazioni su questa modifica, vedere il blog [Upcoming improvements to the Azure AD sign-in experience](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) (Prossimi miglioramenti all'esperienza di accesso di AD Azure).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nuova versione di Azure AD Connect - luglio 2018

**Tipo:** Modifica della **categoria del servizio funzionalità:** provisioning delle app **funzionalità del prodotto:** gestione del ciclo di vita delle identità

La versione più recente di Azure AD Connect include:

- Correzioni di bug e aggiornamenti relativi al supporto

- Disponibilità generale dell'integrazione di PingFederate

- Aggiornamenti del client più recente di SQL 2012

Per altre informazioni su questo aggiornamento, vedere [Azure AD Connect: Cronologia delle versioni](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aggiornamenti alle condizioni per l'utilizzo dell'interfaccia utente dell'utente finale

**Tipo:** **Categoria servizio** funzionalità modificata: Condizioni per l'utilizzo **funzionalità del prodotto:** governance

Stiamo aggiornando la stringa di accettazione nell'interfaccia utente delle Condizioni per l'utilizzo.

**Testo corrente.** Per accedere alle risorse di [NomeTenant], è necessario accettare le condizioni per l'utilizzo.<br>**Nuovo testo.** Per accedere alla risorsa di [NomeTenant], è necessario leggere le condizioni per l'utilizzo.

**Testo corrente:** Scegliendo di accettare, l'utente accetta tutte le condizioni per l'utilizzo indicate.<br>**Nuovo testo:** Fare clic su Accetto per confermare di avere letto e compreso le condizioni per l'utilizzo.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>L'autenticazione pass-through supporta le applicazioni e i protocolli legacy

**Tipo:** Modifica della **categoria del servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

L'autenticazione pass-through ora supporta le app e i protocolli legacy. Le limitazioni seguenti ora sono completamente supportate:

- Accessi utente ad applicazioni client legacy di Office (Office 2010 e Office 2013) senza che sia necessaria l'autenticazione moderna.

- Accesso alla condivisione del calendario e alle informazioni sulla disponibilità negli ambienti ibridi di Exchange solo in Office 2010.

- Accessi utente per applicazioni client Skype for Business senza che sia necessaria l'autenticazione moderna.

- Accessi utente a PowerShell versione 1.0.

- Apple Device Enrollment Program (Apple DEP) con l'Assistente configurazione di iOS.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gestione convergente delle informazioni di sicurezza per la reimpostazione della password self-service e Multi-Factor Authentication

**Tipo:** Nuova **Categoria servizio funzionalità:** **funzionalità del prodotto SSPR:** autenticazione utente

Questa nuova funzionalità consente agli utenti di gestire le informazioni di sicurezza (ad esempio, numero di telefono, indirizzo di posta elettronica, app per dispositivi mobili e così via) per la reimpostazione della password self-service e Multi-Factor Authentication in una singola esperienza. Gli utenti non dovranno più registrare le stesse informazioni di sicurezza per la reimpostazione della password self-service e Multi-Factor Authentication in due diverse esperienze. Questa nuova esperienza si applica anche agli utenti che dispongono della reimpostazione della password self-service o di Multi-Factor Authentication.

Se un'organizzazione non impone la registrazione per Multi-Factor Authentication o la reimpostazione della password self-service, gli utenti possono registrare le proprie informazioni di sicurezza tramite il portale **App personali**. Da qui, gli utenti possono eseguire la registrazione per qualsiasi metodo abilitato per MFA o SSPR.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Gli amministratori possono attivare la nuova esperienza (se necessario) per un gruppo selezionato di utenti o tutti gli utenti in un tenant.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Usare l'app Microsoft Authenticator per verificare la propria identità durante la reimpostazione della password

**Tipo:** **Categoria servizio** funzionalità modificata: **funzionalità del prodotto SSPR:** autenticazione utente

Questa funzionalità consente agli utenti non amministratori di verificare la propria identità durante la reimpostazione di una password tramite una notifica o un codice di Microsoft Authenticator (o qualsiasi altra app di autenticazione). Dopo che gli amministratori attivano questo metodo di reimpostazione della password self-service, gli utenti che hanno eseguito la registrazione di un'app per dispositivi mobili tramite aka.ms/mfasetup o aka.ms/setupsecurityinfo possono usare l'app per dispositivi mobili come metodo di verifica durante la reimpostazione della password.

Le notifiche dell'app per dispositivi mobili possono essere attivate solo come parte di un criterio che richiede due metodi di reimpostazione della password.

---

## <a name="june-2018"></a>Giugno 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Notifica di modifica: correzione della sicurezza per il flusso di autorizzazioni delegate per le app che usano l'API Log attività di Azure AD

**Tipo:** Pianificare la **categoria del servizio** di modifica: Reporting **Product capability:** monitoraggio & Reporting

A causa dell'imposizione della sicurezza più avanzata, è stato necessario apportare una modifica alle autorizzazioni per le app che usano un flusso di autorizzazione delegata per accedere alle [API dei log attività di Azure ad](https://aka.ms/aadreportsapi). Questa modifica verrà applicata entro il **26 giugno 2018**.

Se una delle app usa Azure AD API del log attività, seguire questa procedura per assicurarsi che l'app non si interrompa dopo la modifica.

**Per aggiornare le autorizzazioni delle app**

1. Accedere al portale di Azure, selezionare **Azure Active Directory** e quindi selezionare **Registrazioni per l'app**.
2. Selezionare l'app che usa l'API Log attività di Azure AD, selezionare **Impostazioni**, quindi **Autorizzazioni necessarie** e infine selezionare l'API di **Windows Azure Active Directory**.
3. Nell'area **Autorizzazioni delegate** del pannello **Abilita accesso** selezionare la casella accanto a **Lettura dati directory** e quindi selezionare **Salva**.
4. Selezionare **Concedi autorizzazioni** e quindi selezionare **Sì**.

    >[!Note]
    >È necessario essere un amministratore globale per concedere le autorizzazioni all'app.

Per altre informazioni, vedere l'area [Concedere le autorizzazioni](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) dell'articolo sui prerequisiti per l'accesso all'API di creazione report di Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Configurare le impostazioni TLS per connettersi ai servizi di Azure AD per la conformità PCI DSS

**Tipo:** Nuova **categoria di servizio funzionalità:** N/A funzionalità del **prodotto:** piattaforma

Transport Layer Security (TLS) è un protocollo che fornisce riservatezza e integrità dei dati tra due applicazioni in comunicazione ed è il protocollo di sicurezza più usato oggi.

Il [PCI Security Standards Council](https://www.pcisecuritystandards.org/) ha determinato che le versioni precedenti di TLS e Secure Sockets Layer (SSL) devono essere disabilitate a favore dell'abilitazione di protocolli di app nuovi e più sicuri, la cui conformità è prevista a partire dal **30 giugno 2018**. Questo cambiamento comporta che, se ci si connette ai servizi di Azure AD e si richiede la conformità PCI DSS, è necessario disabilitare TLS 1.0. Sono disponibili più versioni di TLS, ma TLS 1.2 è la versione più recente disponibile per i servizi di Azure Active Directory. Si consiglia di passare direttamente a TLS 1.2 per le combinazioni sia browser-server sia client-server.

I browser non aggiornati potrebbero non supportare le versioni più recenti di TLS, ad esempio TLS 1.2. Per vedere quali versioni di TLS sono supportate dal browser, accedere al sito [Qualys SSL Labs](https://www.ssllabs.com/) e fare clic su **Test your browser** (Verifica browser). È consigliabile eseguire l'aggiornamento alla versione più recente del Web browser e preferibilmente abilitare solo TLS 1.2.

**Per abilitare TLS 1.2 nei diversi browser**

- **Microsoft Edge e Internet Explorer (si configurano entrambi usando Internet Explorer)**

    1. Aprire Internet Explorer, selezionare **strumenti**  >  **Opzioni Internet**  >  **Avanzate**.
    2. Nell'area **Sicurezza** selezionare **Usa TLS 1.2** e quindi fare clic su **OK**.
    3. Chiudere tutte le finestre del browser e riavviare Internet Explorer.

- **Google Chrome**

    1. Aprire Google Chrome, digitare *chrome://settings/* nella barra degli indirizzi e premere **Invio**.
    2. Espandere le opzioni **Avanzate**, passare all'area **Sistema** e selezionare **Apri le impostazioni proxy**.
    3. Nella casella **Proprietà Internet** selezionare la scheda **Avanzate**, passare all'area **Sicurezza**, selezionare **Usa TLS 1.2** e fare clic su **OK**.
    4. Chiudere tutte le finestre del browser e riavviare Google Chrome.

- **Mozilla Firefox**

    1. Aprire Firefox, digitare *about:config* nella barra degli indirizzi e premere **Invio**.
    2. Cercare il termine *TLS* e quindi selezionare la voce **security.tls.version.max**.
    3. Impostare il valore su **3** per forzare il browser a usare TLS fino alla versione 1.2, quindi selezionare **OK**.

        >[!NOTE]
        >Firefox versione 60.0 supporta TLS 1.3, perciò è possibile impostare il valore security.tls.version.max anche su **4**.

    4. Chiudere tutte le finestre del browser e riavviare Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Giugno 2018

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di giugno 2018 sono state aggiunte queste 15 nuove app con il supporto della federazione alla raccolta di app:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token enabled LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>La protezione delle password di AD Azure è disponibile in anteprima pubblica

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** Identity Protection: autenticazione utente

Usare la protezione delle password di Azure Active Directory per eliminare le password facilmente individuabili dall'ambiente. Eliminando queste password è possibile diminuire il rischio di compromissione da attacco password spraying.

In particolare, la protezione delle password di Azure AD aiuta a:

- Proteggere gli account dell'organizzazione sia in Azure AD che in Windows Server Active Directory (AD).
- Impedisce agli utenti di usare le password di un elenco di più di 500 delle password utilizzate più di frequente e oltre 1 milione di varianti di tali password con sostituzione di caratteri.
- Amministrare la protezione delle password di Azure AD da un'unica posizione nel portale di Azure AD sia per Azure AD che per Windows Server Active Directory locale.

Per altre informazioni sulla protezione della password di Azure AD, vedere [Eliminare le password non appropriate nell'organizzazione](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nuovo modello di criteri di accesso condizionale "tutti i guest" creato durante la creazione delle condizioni per l'utilizzo

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** governance

Durante la creazione delle condizioni per l'utilizzo, viene creato anche un nuovo modello di criteri di accesso condizionale per "tutti i guest" e "tutte le app". Questo nuovo modello di criteri si applica alle Condizioni per l'utilizzo appena create, semplificando il processo di creazione e applicazione per i guest.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nuovo modello di criteri di accesso condizionale "personalizzato" creato durante la creazione delle condizioni per l'utilizzo

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** governance

Durante la creazione delle condizioni per l'utilizzo, viene creato anche un nuovo modello di criteri di accesso condizionale "personalizzato". Questo nuovo modello di criteri consente di creare le condizioni e quindi di passare immediatamente al pannello di creazione dei criteri di accesso condizionale, senza dover spostarsi manualmente nel portale.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nuova guida completa su come distribuire Azure Multi-Factor Authentication

**Tipo:** Nuova **categoria di servizi funzionalità:** altra funzionalità del **prodotto:** sicurezza delle identità & protezione

Sono state rilasciate nuove istruzioni dettagliate su come distribuire Azure Multi-Factor Authentication (MFA) nell'organizzazione.

Per visualizzare la guida alla distribuzione di Multi-Factor Authentication, visitare il repository [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (Guide alla distribuzione di identità) in GitHub. Per fornire commenti e suggerimenti sulle guide alla distribuzione, usare il [modulo di feedback per il piano di distribuzione](https://aka.ms/deploymentplanfeedback). In caso di domande sulle guide alla distribuzione, contattare Microsoft all'indirizzo [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>I ruoli di gestione delle app delegati di Azure Active Directory sono in anteprima pubblica

**Tipo:** Nuova **Categoria servizio funzionalità:** app aziendali **funzionalità del prodotto:** controllo di accesso

Gli amministratori ora possono delegare le attività di gestione delle app senza assegnare il ruolo di amministratore globale. I nuovi ruoli e funzionalità sono:

- **Nuovi ruoli amministrativi standard di Azure AD:**

    - **Amministratore dell'applicazione.** Concede la capacità di gestire tutti gli aspetti di tutte le app, fra cui registrazione, impostazioni SSO, assegnazioni di app e licenze, impostazioni proxy di applicazione e consenso (tranne che per le risorse di Azure AD).

    - **Amministratore applicazione cloud.** Concede tutte le capacità di Amministratore di applicazioni, eccetto Proxy di app perché non fornisce l'accesso locale.

    - **Sviluppatore di applicazioni.** Concede la capacità di creare registrazioni delle app, anche se l'opzione **consentire agli utenti di registrare le app** è disattivata.

- **La proprietà, configurata per registrazione di app e per app aziendale, analogamente al processo di proprietà del gruppo:**

    - **Proprietario della registrazione delle app.** Concede la capacità di gestire tutti gli aspetti della registrazione delle app di proprietà, inclusi il manifesto dell'app e l'aggiunta di altri proprietari.

    - **Proprietario di app aziendali.** Concede la capacità di gestire numerosi aspetti delle app aziendali di proprietà, fra cui impostazioni SSO, assegnazioni di app e consenso (tranne che per le risorse di Azure AD).

Per altre informazioni sull'anteprima pubblica, vedere il blog [Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) (I ruoli di gestione delle app delegati di Azure AD sono in anteprima pubblica) . Per ulteriori informazioni sui ruoli e sulle autorizzazioni, vedere [assegnazione di ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maggio 2018

### <a name="expressroute-support-changes"></a>Modifiche al supporto di ExpressRoute

**Tipo:** Pianificare la **categoria del servizio** di modifica: autenticazione (accessi) funzionalità del **prodotto:** piattaforma

Le offerte di software come un servizio, ad esempio Azure Active Directory (Azure AD), sono progettate per ottenere la massima efficienza accedendo direttamente a Internet, senza la necessità di ExpressRoute o di altri tunnel VPN privati. Per questo motivo, l'**1 agosto 2018**, verrà interrotto il supporto di ExpressRoute per i servizi di Azure AD che usano il peering pubblico di Azure e le community di Azure nel peering Microsoft. Tutti i servizi interessati da questa modifica potrebbero riscontrare il graduale passaggio del traffico di Azure AD da ExpressRoute a Internet.

Anche se la modifica al supporto è in corso, è risaputo che esistono ancora situazioni in cui potrebbe essere necessario usare un set di circuiti dedicato per il traffico di autenticazione. Per questo motivo, Azure AD continuerà a supportare le restrizioni per gli intervalli IP in base al tenant usando ExpressRoute e i servizi già inclusi nel peering Microsoft con la community "Altri servizi Online di Office 365". Se i servizi in uso sono interessati, ma è richiesto ExpressRoute, è necessario seguire questa procedura:

- **Se si è nel peering pubblico di Azure.** Passare al peering Microsoft e iscriversi alla community **Altri servizi online di Office 365 (12076:5100)**. Per altre informazioni su come passare dal peering pubblico di Azure al peering Microsoft, vedere l'articolo [Spostare un peering pubblico nel peering Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).

- **Se si è nel peering Microsoft.** Iscriversi alla community **Altri servizi online di Office 365 (12076:5100)**. Per altre informazioni sui requisiti per il routing, vedere la [sezione Supporto per le community BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) dell'articolo Requisiti per il routing di ExpressRoute.

Se è necessario continuare a usare circuiti dedicati, sarà necessario contattare il team degli account Microsoft per informazioni su come ottenere l'autorizzazione per usare la community **Altri servizi online di Office 365 (12076:5100)**. Il Review Board gestito da MS Office verificherà se tali circuiti sono necessari e illustrerà le implicazioni di tecniche che ne seguiranno mantenendoli. Le sottoscrizioni non autorizzate che proveranno a creare filtri della route per Office 365 riceveranno un messaggio di errore.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>API Microsoft Graph per scenari amministrativi relativi alle Condizioni per l'utilizzo

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** esperienza dello sviluppatore

Sono state aggiunte Microsoft Graph API per l'operazione di amministrazione di Azure AD condizioni per l'utilizzo. È possibile creare, aggiornare ed eliminare le condizioni per l'utilizzo dell'oggetto.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Aggiunta di un endpoint multi-tenant di Azure AD come provider di identità in Azure AD B2C

**Tipo:** Nuova **Categoria servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

Usando criteri personalizzati, è ora possibile aggiungere l'endpoint comune di Azure AD come provider di identità in Azure AD B2C. In questo modo, si otterrà un singolo punto di ingresso per tutti gli utenti di Azure AD che accedono alle applicazioni. Per altre informazioni, vedere [Azure Active Directory B2C: consentire agli utenti di accedere a un provider di identità di Azure AD multi-tenant mediante criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Uso di URL interni per accedere alle app da qualsiasi posizione con l'estensione My Apps Sign-in e Azure AD Application Proxy

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app personali: SSO

Gli utenti possono ora accedere alle applicazioni tramite URL interni quando si trovano all'esterno della rete aziendale usando l'estensione My Apps Secure Sign-in per Azure AD. Questa estensione funziona con tutte le applicazioni pubblicate tramite Azure AD Application Proxy su qualsiasi browser in cui è anche installata l'estensione del browser per il pannello di accesso. La funzionalità di reindirizzamento degli URL viene automaticamente abilitata quando un utente accede all'estensione. L'estensione è disponibile per il download in [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) e [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - Dati in Europa per i clienti europei

**Tipo:** Nuova **Categoria servizio funzionalità:** altra **funzionalità del prodotto:** GoLocal

I dati dei clienti in Europa devono restare in Europa e non possono essere replicati al di fuori di data center europei ai fini della conformità alle leggi europee e sulla privacy. Questo [articolo](https://go.microsoft.com/fwlink/?linkid=872328) include i dettagli specifici su quali informazioni sulle identità verranno archiviate in Europa e fornisce anche informazioni sui dati che verranno archiviati al di fuori dei data center europei.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nuove integrazioni di app SaaS per il provisioning utenti - Maggio 2018

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità del prodotto per il provisioning delle app **:** integrazione di terze parti

Azure AD permette di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni SaaS, come Dropbox, Salesforce, ServiceNow e altre. A maggio 2018 è stato aggiunto il supporto per il provisioning utenti per le applicazioni seguenti nella raccolta di app Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [cornerstone ondemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Per un elenco di tutte le applicazioni che supportano il provisioning degli utenti nella raccolta di Azure AD, vedere [https://aka.ms/appstutorial](https://aka.ms/appstutorial) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Le verifiche di accesso per l'accesso di app e gruppi in Azure AD includono ora controlli ricorrenti

**Tipo:** Nuova **Categoria servizio funzionalità:** verifiche di accesso **funzionalità del prodotto:** governance

La verifica di accesso di gruppi e app è in genere disponibile come parte di Azure AD Premium P2.  Gli amministratori potranno configurare le verifiche di accesso delle appartenenze ai gruppi e le assegnazioni delle applicazioni in modo che ricorrano a intervalli regolari, ad esempio ogni mese o ogni trimestre.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Log attività di Azure AD (accessi e controllo) ora disponibili tramite Microsoft Graph

**Tipo:** Nuova **Categoria servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

Azure AD log attività, che include accessi e log di controllo, sono ora disponibili tramite l'API Microsoft Graph. Per accedere a questi log sono stati esposti due punti finali attraverso l'API Microsoft Graph. Per iniziare, fare riferimento ai [documenti](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) per l'accesso a livello di codice alle API di creazione report di Azure AD.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Miglioramenti all'esperienza di riscatto B2B e di uscita da un'organizzazione

**Tipo:** Nuova **categoria di servizio** funzionalità: **funzionalità del prodotto B2B:** B2B/B2C

**Riscatto just-in-Time:** Quando si condivide una risorsa con un utente guest usando l'API B2B, non è necessario inviare un messaggio di posta elettronica di invito speciale. Nella maggior parte dei casi l'utente guest può accedere alla risorsa per poi essere guidato nell'esperienza di riscatto JIT. Di conseguenza, non vi è alcun impatto a causa di messaggi di posta elettronica non ricevuti. Non è più necessario chiedere agli utenti guest "è stato fatto clic sul collegamento di riscatto inviato dal sistema?". Questo significa che quando l'azienda del provider di servizi usa la gestione degli inviti, i file cloud allegati possono avere lo stesso URL canonico per tutti gli utenti, interni ed esterni, in qualsiasi stato di riscatto.

**Esperienza di riscatto moderna:** non è più necessario usare una pagina di destinazione per il riscatto con schermata divisa. Gli utenti vedranno un'esperienza di consenso moderna, che include l'informativa sulla privacy dell'organizzazione che ha emesso l'invito, proprio come avviene con le app di terze parti.

**Gli utenti guest possono uscire dall'organizzazione:** Una volta terminata la relazione di un utente con un'organizzazione, è possibile lasciare l'organizzazione in modo autonomo. Non è più possibile chiamare l'amministratore dell'organizzazione che invia l'invito a "essere rimosso", non è più necessario generare ticket di supporto.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Maggio 2018

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di maggio 2018 sono state aggiunte queste 18 nuove app con il supporto della federazione alla raccolta di app:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty governance, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [JAMF Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [invidia MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel [, Arc Publishing-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial) [, iWellnessNow, Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial) [, Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial) [,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial) [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nuove guide dettagliate alla distribuzione per Azure Active Directory

**Tipo:** Nuova **Categoria servizio funzionalità:** altra **funzionalità del prodotto:** directory

Nuove istruzioni dettagliate su come distribuire Azure Active Directory (Azure AD), tra cui la reimpostazione della password self-service (SSPR), la Single Sign-On (SSO), l'accesso condizionale (CA), il proxy applicazione, il provisioning utenti, Active Directory Federation Services (ADFS) per l'autenticazione pass-through (PTA) e la sincronizzazione dell'hash delle password (pH).

Per visualizzare le guide alla distribuzione, visitare il repository [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (Guide alla distribuzione di identità) in GitHub. Per fornire commenti e suggerimenti sulle guide alla distribuzione, usare il [modulo di feedback per il piano di distribuzione](https://aka.ms/deploymentplanfeedback). In caso di domande sulle guide alla distribuzione, contattare Microsoft all'indirizzo [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Ricerca di applicazioni aziendali - Caricamento di altre app

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: SSO

Per risolvere i problemi di individuazione di applicazioni/entità di servizio, è stata aggiunta la possibilità di caricare altre applicazioni nell'elenco di tutte le applicazioni aziendali. Per impostazione predefinita, vengono visualizzate 20 applicazioni. È ora possibile fare clic su **carica altro** per visualizzare altre applicazioni.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>La versione di maggio di AADConnect contiene un'anteprima pubblica dell'integrazione con PingFederate, importanti aggiornamenti della sicurezza, numerose correzioni di bug e nuovi eccezionali strumenti per la risoluzione dei problemi.

**Tipo:** Modifica della **categoria del servizio funzionalità:** ad Connect **funzionalità del prodotto:** gestione del ciclo di vita delle identità

La versione di maggio di AADConnect contiene un'anteprima pubblica dell'integrazione con PingFederate, importanti aggiornamenti della sicurezza, numerose correzioni di bug e nuovi eccezionali strumenti per la risoluzione dei problemi. Le note sulla versione sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Verifiche di accesso di Azure AD: applicazione automatica

**Tipo:** Modifica della **categoria del servizio funzionalità:** verifiche di accesso **funzionalità del prodotto:** governance

Le verifiche di accesso di gruppi e app sono in genere disponibili come parte di Azure AD Premium P2. Un amministratore può configurare l'ambiente per l'applicazione automatica delle modifiche da apportare al gruppo o all'app al termine delle verifiche di accesso. L'amministratore può anche specificare che cosa succederà all'accesso continuo dell'utente se i revisori non rispondono, rimuovere l'accesso, mantenere l'accesso o eseguire azioni consigliate relative al sistema.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>I token ID non possono più essere restituiti tramite response_mode query per le nuove app.

**Tipo:** Modifica della **categoria del servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Le app create a partire dal 25 aprile 2018 non potranno più richiedere **id_token** tramite il response_mode **query**.  Questa novità allinea Azure AD alle specifiche OIDC e contribuisce a ridurre la superficie di attacco delle app.  Alle app create prima del 25 aprile 2018 non viene impedito di usare il response_mode **query** con response_type **id_token**.  L'errore restituito durante la richiesta di un id_token da AAD è **AADSTS70007:' query ' non è un valore supportato di ' response_mode ' quando viene richiesto un token**.

I valori di response_mode **fragment**e **form_post** continuano a funzionare quando si creano nuovi oggetti applicazione, ad esempio per l'utilizzo del proxy di app. Assicurarsi di usare uno dei due prima di creare una nuova applicazione.

---

## <a name="april-2018"></a>Aprile 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>I token di accesso di Azure AD B2C sono disponibili a livello generale

**Tipo:** Nuova **Categoria servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

È ora possibile accedere all'API Web protetta da Azure Active Directory B2C tramite i token di accesso. La funzionalità passerà dall'anteprima pubblica alla disponibilità generale. L'esperienza dell'interfaccia utente per configurare applicazioni Azure Active Directory B2C e API Web è stata migliorata e sono stati apportati altri miglioramenti secondari.

Per ulteriori informazioni, vedere [Azure AD B2C: richiesta di token di accesso](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Test della configurazione Single Sign-On per le applicazioni basate su SAML

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: SSO

Quando si configurano applicazioni SSO basate su SAML, è possibile testare l'integrazione nella pagina di configurazione. Se si verifica un errore durante l'accesso, è possibile fornire l'errore nell'esperienza di test e Azure AD fornisce le procedure di risoluzione per risolvere il problema specifico.

Per altre informazioni, vedere:

- [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD le condizioni per l'utilizzo hanno ora report per utente

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** conformità

Gli amministratori possono ora selezionare una determinata ToU e vedere tutti gli utenti che hanno acconsentito a tale ToU e la data/ora in cui è stata effettuata.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: IP a rischio per la protezione tramite blocco della Extranet di AD FS

**Tipo:** Nuova **Categoria servizio funzionalità:** altra **funzionalità del prodotto:** monitoraggio & Reporting

Connect Health ora supporta la capacità di rilevare gli indirizzi IP che superano una soglia di accessi U/P non riusciti su base oraria o giornaliera. Le funzionalità fornite da questa funzione sono:

- Report completo che mostra l'indirizzo IP e il numero di accessi non riusciti generati su base oraria/giornaliera con soglia personalizzabile.
- Avvisi tramite e-mail che indicano quando un indirizzo IP specifico ha superato la soglia di accessi U/P non riusciti su base oraria/giornaliera.
- Un'opzione di download per eseguire un'analisi dettagliata dei dati

Per altre informazioni, vedere [Report sugli indirizzi IP rischiosi](https://aka.ms/aadchriskyip).

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Configurazione semplificata dell'app con file di metadati o URL

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: SSO

Nella pagina delle applicazioni Enterprise, gli amministratori possono caricare un file di metadati SAML per configurare l'accesso basato su SAML per  l'applicazione nella raccolta AAD e non nella raccolta.

Inoltre, è possibile utilizzare l'URL dei metadati della federazione di applicazioni Azure AD per configurare SSO con l'applicazione di destinazione.

Per altre informazioni, vedere [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Funzionalità Condizioni per l'utilizzo di Azure AD ora disponibile a livello generale

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** conformità


Azure AD le condizioni per l'utilizzo sono state spostate da anteprima pubblica a disponibile a livello generale.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche

**Tipo:** Nuova **categoria di servizio** funzionalità: **funzionalità del prodotto B2B:** B2B/B2C


È ora possibile specificare con quali organizzazioni partner si desidera condividere e collaborare in Azure AD B2B Collaboration. Per fare questo, è possibile scegliere di creare un elenco di domini specifici di consenso o negazione. Quando un dominio viene bloccato utilizzando queste funzionalità, i collaboratori non possono più inviare inviti a persone che si trovano in quel dominio.

Ciò consente di controllare l'accesso alle risorse, consentendo nel contempo un'esperienza fluida agli utenti approvati.

Questa funzionalità di collaborazione B2B è disponibile per tutti i clienti Azure Active Directory e può essere usata insieme a funzionalità di Azure AD Premium, ad esempio l'accesso condizionale e la protezione delle identità, per un controllo più granulare quando e come gli utenti aziendali esterni accedono e ottengono l'accesso.

Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di aprile 2018 sono state aggiunte queste 13 nuove app con il supporto della federazione alla raccolta di app:

Criterion HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (locale)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Possibilità di consentire agli utenti B2B di Azure AD l'accesso alle applicazioni locali (anteprima pubblica)

**Tipo:** Nuova **categoria di servizio** funzionalità: **funzionalità del prodotto B2B:** B2B/B2C

Un'organizzazione che usa Collaborazione B2B di Azure Active Directory (Azure AD) per invitare gli utenti guest di organizzazioni partner in Azure AD può ora fornire a questi utenti B2B l'accesso alle app locali. Queste app locali possono usare l'autenticazione basata su SAML o l'autenticazione integrata di Windows con la delega vincolata Kerberos.

Per altre informazioni, vedere [concedere agli utenti B2B in Azure ad l'accesso alle applicazioni locali](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Esercitazioni per l'integrazione dell'accesso Single Sign-On disponibili in Azure Marketplace

**Tipo:** Modifica della **categoria del servizio funzionalità:** altra **funzionalità del prodotto:** integrazione di terze parti

Se un'applicazione elencata in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) supporta la Single Sign-on basata su SAML, fare clic su **Ottieni ora** fornisce l'esercitazione sull'integrazione associata all'applicazione.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Prestazioni più rapide del provisioning automatico degli utenti di Azure AD nelle applicazioni SaaS

**Tipo:** Categoria del **servizio** funzionalità modificata: **funzionalità del prodotto** per il provisioning delle app: integrazione di terze parti

In precedenza, i clienti che usavano i connettori per il provisioning degli utenti di Azure Active Directory per applicazioni SaaS (ad esempio Salesforce, ServiceNow e Box) potevano sperimentare prestazioni lente se i loro tenant Azure AD contenevano oltre 100.000 utenti e gruppi combinati e usavano le assegnazioni degli utenti e dei gruppi per determinare per quali utenti doveva essere eseguito il provisioning.

Il 2 aprile 2018 il servizio di provisioning Azure AD è stato migliorato in modo significativo, riducendo notevolmente il tempo necessario per eseguire la sincronizzazione iniziale tra Azure Active Directory e le applicazioni SaaS di destinazione.

Di conseguenza, molti clienti con sincronizzazioni iniziali con le applicazioni che hanno richiesto molti giorni o che non sono mai state completate, ora possono completarle in pochi minuti o ore.

Per altre informazioni, vedere [Cosa accade durante il provisioning?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Reimpostazione della password self-service dalla schermata di blocco di Windows 10 per i computer aggiunti ad Azure AD ibrido

**Tipo:** Modifica della **categoria del servizio funzionalità:** reimpostazione della password self **-Service funzionalità del prodotto:** autenticazione utente

È stata aggiornata la funzionalità di Windows 10 SSPR per includere il supporto per i computer che sono parte di Azure AD ibrido. Questa funzionalità è disponibile in Windows 10 RS4 e consente agli utenti di reimpostare la password dalla schermata di blocco di un computer Windows 10. Gli utenti che sono abilitati e registrati per la reimpostazione self-service della password possono utilizzare questa funzionalità.

Per altre informazioni, consultare [Reimpostazione password self-service di Azure AD dalla schermata di accesso](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Marzo 2018

### <a name="certificate-expire-notification"></a>Notifica della scadenza dei certificati

**Tipo:** **Categoria servizio fisso:** funzionalità del **prodotto** app aziendali: SSO

Azure AD invia una notifica quando un certificato per un'applicazione inclusa o non inclusa nella raccolta sta per scadere.

Alcuni utenti non ricevevano notifiche per le applicazioni aziendali configurate per l'accesso Single Sign-On basato su SAML. Questo problema è stato risolto. Azure AD invia una notifica per i certificati in scadenza entro 7, 30 e 60 giorni. È possibile visualizzare questo evento nei log di controllo.

Per altre informazioni, vedere:

- [Gestione di certificati per accesso Single Sign-On federato in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Report delle attività di controllo nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Provider di identità Twitter e GitHub in Azure AD B2C

**Tipo:** Nuova **Categoria servizio funzionalità:** B2C-Consumer Identity Management **funzionalità del prodotto:** B2B/B2C

È ora possibile aggiungere Twitter o GitHub come provider di identità in Azure AD B2C. Twitter passerà dall'anteprima pubblica alla disponibilità generale. GitHub verrà rilasciato in anteprima pubblica.

Per altre informazioni, vedere [Informazioni su Collaborazione B2B di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Limitare l'accesso al browser usando Intune Managed Browser con Azure AD l'accesso condizionale basato sull'applicazione per iOS e Android

**Tipo:** Nuova **categoria di servizio funzionalità:** accesso condizionale funzionalità del **prodotto:** sicurezza delle identità & protezione

**Ora in anteprima pubblica.**

**SSO di Intune Managed Browser:** i dipendenti possono usare Single Sign-On nei client nativi (ad esempio, Microsoft Outlook) e in Intune Managed Browser per tutte le app connesse ad Azure AD.

**Intune Managed browser supporto per l'accesso condizionale:** È ora possibile richiedere ai dipendenti di usare Intune Managed browser usando i criteri di accesso condizionale basato sull'applicazione.

Per altre informazioni, vedere il [post di blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Per altre informazioni, vedere:

- [Configurare l'accesso condizionale basato sull'applicazione](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Configurare i criteri di Managed Browser](https://aka.ms/managedbrowser)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlet del proxy applicazione nel modulo GA di PowerShell

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** proxy app: controllo di accesso

Il supporto per i cmdlet del proxy di applicazione è ora disponibile nel modulo GA di PowerShell. Questa operazione richiede di rimanere aggiornati sui moduli di PowerShell. Se si diventa più di un anno dopo, alcuni cmdlet potrebbero smettere di funzionare.

Per altre informazioni, vedere [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>I client nativi di Office 365 includono il supporto per l'accesso SSO facile tramite un protocollo non interattivo

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Gli utenti dei client nativi di Office 365 (versione 16.0.8730.xxxx e successive) possono usufruire di un'esperienza di accesso non interattiva usando l'accesso SSO facile. Questo supporto viene fornito aggiungendo un protocollo non interattivo (WS-Trust) ad Azure AD.

Per ulteriori informazioni, vedere [come funziona l'accesso in un client nativo con accesso SSO facile?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>È disponibile un'esperienza di accesso non interattiva, con accesso SSO facile, se un'applicazione invia le richieste di accesso agli endpoint di tenant di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Gli utenti possono usufruire di un'esperienza di accesso non interattiva con Seamless SSO se un'applicazione, ad esempio `https://contoso.sharepoint.com`, invia le richieste di accesso agli endpoint tenant di Azure AD, ovvero `https://login.microsoftonline.com/contoso.com/<..>` o `https://login.microsoftonline.com/<tenant_ID>/<..>`, anziché all'endpoint comune di Azure AD (`https://login.microsoftonline.com/common/<...>`).

Per altre informazioni, vedere [Azure Active Directory accesso Single Sign-on](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)facile.

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Per implementare l'accesso SSO facile è necessario aggiungere un solo URL di Azure AD, anziché due URL come in precedenza, alle impostazioni dell'area Intranet degli utenti

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Per consentire agli utenti di usufruire dell'accesso SSO facile, è necessario aggiungere un solo URL di Azure AD alle impostazioni dell'area Intranet degli utenti usando Criteri di gruppo in Active Directory: `https://autologon.microsoftazuread-sso.com`. In precedenza era necessario aggiungere due URL.

Per altre informazioni, vedere [Azure Active Directory accesso Single Sign-on](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)facile.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di marzo 2018 sono state aggiunte queste 15 nuove app con il supporto della federazione alla raccolta di app:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM per Risorse di Azure è disponibile a livello generale

**Tipo:** Nuova **Categoria servizio funzionalità:** Privileged Identity Management **funzionalità del prodotto:** Privileged Identity Management

Se si usa Azure AD Privileged Identity Management (PIM) per i ruoli della directory, è ora possibile eseguire le relative funzionalità di assegnazione e accesso con vincoli di tempo per i ruoli di Risorse di Azure, ad esempio Sottoscrizioni, Gruppi di risorse, Macchine virtuali e qualsiasi altra risorsa supportata da Azure Resource Manager. Applicare Multi-Factor Authentication quando si attivano i ruoli in modalità Just-In-Time e si pianificano le attivazioni in base agli intervalli di modifica approvati. Inoltre, questa versione introduce miglioramenti non disponibili nell'anteprima pubblica, tra cui un'interfaccia utente aggiornata, i flussi di lavoro di approvazione e la possibilità di estendere i ruoli prossimi alla scadenza e rinnovare quelli scaduti.

Per altre informazioni, vedere [PIM per Risorse di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Aggiunta di attestazioni facoltative ai token delle app (anteprima pubblica)

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

Un'app di Azure AD può ora richiedere attestazioni personalizzate o facoltative nei token JWT e SAML.  Si tratta di attestazioni relative all'utente o al tenant che non sono incluse per impostazione predefinita nel token a causa di vincoli di dimensione o applicabilità.  Questa funzionalità è attualmente in anteprima pubblica per le app di Azure AD negli endpoint 1.0 e 2.0.  Vedere la documentazione per informazioni su quali attestazioni possono essere aggiunte e su come modificare il manifesto dell'applicazione per richiederle.

Per altre informazioni, vedere [Attestazioni facoltative in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD supporta PKCE per flussi OAuth più sicuri

**Tipo:** Nuova **Categoria servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

La documentazione di Azure AD è stata aggiornata in modo da mettere in evidenza il supporto per PKCE, che consente comunicazioni più sicure durante il flusso di concessione del codice di autorizzazione OAuth 2.0.  Negli endpoint 1.0 e 2.0 sono supportati entrambi i valori di code_challenge S256 e plaintext.

Per altre informazioni, vedere [richiedere un codice di autorizzazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code).

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Supporto per il provisioning di tutti i valori degli attributi utente disponibili nell'API Get_Workers di Workday

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità del prodotto per il provisioning delle app **:** integrazione di terze parti

L'anteprima pubblica del provisioning in ingresso da Workday ad Active Directory e Azure AD offre ora la possibilità di estrarre tutti i valori degli attributi disponibili nell'API Get_Workers di Workday ed effettuarne il provisioning. Ciò include il supporto per centinaia di attributi standard e personalizzati, oltre a quelli forniti con la versione iniziale del connettore di provisioning in ingresso di Workday.

Per altre informazioni, vedere [Personalizzazione dell'elenco di attributi utente di Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes).

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Modifica dell'appartenenza ai gruppi da dinamica a statica e viceversa

**Tipo:** Nuova **Categoria servizio funzionalità:** gestione gruppi **funzionalità del prodotto:** collaborazione

È possibile modificare il modo in cui viene gestita l'appartenenza in un gruppo. Ciò è utile per mantenere lo stesso ID e nome di gruppo nel sistema in modo che i riferimenti al gruppo esistenti restino validi. Creando un nuovo gruppo sarebbe infatti necessario aggiornare tali riferimenti.
L'interfaccia di amministrazione di Azure AD è stata aggiornata per il supporto di questa funzionalità. I clienti possono ora convertire i gruppi esistenti dall'appartenenza dinamica a quella assegnata e viceversa. Sono ancora disponibili anche i cmdlet di PowerShell esistenti.

Per ulteriori informazioni, vedere [regole di appartenenza dinamiche per i gruppi in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Miglioramento del comportamento di disconnessione con accesso SSO facile

**Tipo:** Modifica della **categoria del servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

In precedenza, anche se disconnessi in modo esplicito da un'applicazione protetta da Azure AD, gli utenti sarebbero stati riconnessi automaticamente tramite l'accesso SSO facile se avessero provato ad accedere nuovamente a un'applicazione di AD Azure all'interno della rete aziendale dai propri dispositivi aggiunti a un dominio. Con questa modifica, la disconnessione è supportata.  Gli utenti possono pertanto scegliere lo stesso o un altro account di Azure AD per eseguire nuovamente l'accesso anziché essere riconnessi automaticamente tramite l'accesso SSO facile.

Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso).

---

### <a name="application-proxy-connector-version-154020-released"></a>Rilascio della versione 1.5.402.0 del connettore di Application Proxy

**Tipo:** Categoria del **servizio** di funzionalità modificata: **funzionalità del prodotto** proxy di app: sicurezza delle identità & protezione

Questa versione del connettore è stata gradualmente implementata nel mese di novembre. La nuova versione include le modifiche seguenti:

- Il connettore imposta ora i cookie a livello di dominio anziché a livello di sottodominio. Ciò garantisce un'esperienza di accesso SSO più uniforme, evitando richieste di autenticazione ridondanti.
- È incluso il supporto per le richieste di codifica a blocchi.
- È stato migliorato il monitoraggio dell'integrità del connettore.
- Sono stati introdotti miglioramenti alla stabilità e correzioni di bug.

Per altre informazioni, vedere [Informazioni sui connettori di Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).

---

## <a name="february-2018"></a>Febbraio 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Esplorazione migliorata per la gestione di utenti e gruppi

**Tipo:** Pianificare la **categoria di servizi** di modifica: directory gestione directory **funzionalità:** directory

L'esperienza di esplorazione per la gestione di utenti e gruppi è stata semplificata. Ora è possibile passare dalla panoramica della directory direttamente all'elenco di tutti gli utenti, con un più facile accesso all'elenco degli utenti eliminati. Si può anche passare dalla panoramica della directory direttamente all'elenco di tutti i gruppi, con un più facile accesso alle impostazioni di gestione dei gruppi. Inoltre, dalla pagina di panoramica della directory è possibile cercare un utente, un gruppo, un'applicazione aziendale o una registrazione di app.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilità dei report di controllo e delle attività di accesso in Microsoft Azure gestito da 21Vianet (21Vianet per Azure Cina)

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure Stack **funzionalità del prodotto:** monitoraggio & Reporting

I report dei log attività di Azure AD sono ora disponibili nelle istanze di Microsoft Azure gestito da 21Vianet (21Vianet per Azure Cina). Sono inclusi i log seguenti:

- **Log di attività di accesso**: includono tutti i log sugli accessi associati al tenant.

- **Log di controllo delle password self-service**: includono tutti i log di controllo relativi alla reimpostazione password self-service.

- **Log di controllo della gestione directory**: includono tutti i log di controllo correlati alla gestione delle directory, come Gestione utenti, Gestione app e altri.

Questi log permettono di ottenere informazioni approfondite sullo stato dell'ambiente. I dati forniti consentono di:

- Determinare le modalità di utilizzo di app e servizi da parte degli utenti.

- Risolvere problemi che influiscono negativamente sulla produttività degli utenti.

Per altre informazioni sull'uso di questi report, vedere [Creazione di report in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Usare il ruolo "Lettore report" (ruolo non amministrativo) per visualizzare i report attività di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** report **funzionalità del prodotto:** monitoraggio & Reporting

Come parte del feedback dei clienti per consentire ai ruoli non amministrativi di accedere ai log attività di Azure AD, è stata abilitata la possibilità per gli utenti che si trovano nel ruolo "lettore report" di accedere agli accessi e alle attività di controllo all'interno del portale di Azure, nonché di usare l'API Microsoft Graph.

Per altre informazioni sull'uso di questi report, vedere [Creazione di report in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Attestazione EmployeeID disponibile come attributo utente e identificatore utente

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: SSO

È possibile configurare **EmployeeID** come ID utente e attributo utente per gli utenti membri e i guest B2B nelle applicazioni con accesso basato su SAML dall'interfaccia utente delle applicazioni aziendali.

Per altre informazioni, vedere [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gestione semplificata delle applicazioni mediante caratteri jolly in Azure AD Application Proxy

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** proxy app: autenticazione utente

Per facilitare la distribuzione delle applicazioni e ridurre il sovraccarico amministrativo, ora è possibile pubblicare le applicazioni usando caratteri jolly. Per pubblicare un'applicazione con caratteri jolly, è possibile seguire il flusso di pubblicazione standard delle applicazioni ma usare un carattere jolly negli URL interni ed esterni.

Per altre informazioni, vedere [Applicazioni con carattere jolly in Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard).

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nuovi cmdlet a supporto della configurazione del proxy di applicazione

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** proxy app: piattaforma

L'ultima versione del modulo di anteprima Azure AD PowerShell contiene nuovi cmdlet che consentono agli utenti di configurare applicazioni proxy di applicazione mediante PowerShell.

I nuovi cmdlet sono:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nuovi cmdlet a supporto della configurazione di gruppi

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** proxy app: piattaforma

L'ultima versione del modulo Azure AD PowerShell contiene cmdlet per la gestione dei gruppi in Azure AD. Questi cmdlet erano in precedenza disponibili nel modulo AzureADPreview e ora sono stati aggiunti al modulo AzureAD.

I cmdlet di gestione dei gruppi con disponibilità generale sono:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Disponibilità di una nuova versione di Azure AD Connect

**Tipo:** Nuova **Categoria servizio funzionalità:** ad Sync **funzionalità del prodotto:** piattaforma

Azure AD Connect è lo strumento preferito per sincronizzare i dati tra Azure AD e le origini dati locali, inclusi Windows Server Active Directory e LDAP.

>[!Important]
>Questa build introduce modifiche alle regole dello schema e di sincronizzazione. Il servizio di sincronizzazione Azure AD Connect attiva i passaggi di importazione e sincronizzazione complete dopo un aggiornamento. Per informazioni su come modificare questo comportamento, vedere [Come rinviare la sincronizzazione completa dopo l'aggiornamento](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Questa versione contiene gli aggiornamenti e le modifiche seguenti:

**Problemi risolti**

- Correzione della finestra temporale nelle attività in background per la pagina Filtro partizioni nel passaggio alla pagina successiva.

- Correzione di un bug che provocava la violazione di accesso durante l'azione personalizzata ConfigDB.

- Correzione di un bug per il ripristino dal timeout della connessione SQL.

- Correzione di un bug in cui i certificati con caratteri jolly SAN non superavano la verifica preliminare.

- Correzione di un bug che provocava l'arresto anomalo di miiserver.exe durante l'esportazione del connettore AAD.

- Correzione di un bug a causa del quale i tentativi di accesso con password non valide venivano registrati nel controller di dominio durante l'esecuzione della procedura guidata di Azure AD Connect per la modifica della configurazione

**Miglioramenti e nuove funzionalità**

- Dati di telemetria dell'applicazione: gli amministratori possono attivare o disattivare questa classe di dati.

- Dati di integrità di Azure AD: gli amministratori devono visitare il portale dei dati di integrità per gestire le impostazioni di integrità. Dopo aver modificato i criteri del servizio, gli agenti li leggeranno e applicheranno.

- Aggiunta di azioni di configurazione per il writeback dei dispositivi e indicatore di stato per l'inizializzazione della pagina.

- Miglioramento della diagnostica generale con report HTML e raccolta completa di dati in un report in formato ZIP di testo/HTML.

- Migliore affidabilità dell'aggiornamento automatico e aggiunta di ulteriori dati di telemetria per assicurare che sia possibile determinare l'integrità del server.

- Limitazione delle autorizzazioni disponibili per gli account con privilegi sull'account AD Connect. Per le nuove installazioni, la procedura guidata limita le autorizzazioni degli account con privilegi sull'account MSOL dopo la creazione dell'account MSOL stesso. Le modifiche interessano le installazioni rapide e le installazioni personalizzate con account a creazione automatica.

- Modifica del programma di installazione in modo da non richiedere il privilegio SA per l'installazione pulita di Azure AD Connect.

- Nuova utilità per risolvere i problemi di sincronizzazione per un oggetto specifico. L'utilità esegue attualmente le verifiche seguenti:

    - Mancata corrispondenza di UserPrincipalName tra l'oggetto utente sincronizzato e l'account utente nel tenant Azure AD.

    - Se l'oggetto viene escluso dalla sincronizzazione a causa dei filtri di dominio

    - Se l'oggetto viene escluso dalla sincronizzazione a causa dei filtri dell'unità organizzativa

- Nuova utilità per la sincronizzazione dell'hash delle password corrente archiviato nell'istanza locale di Active Directory per un account utente specifico. L'utilità non richiede la modifica delle password.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Applicazioni che supportano i criteri di Protezione app di Intune aggiunti per l'uso con l'accesso condizionale basato sull'applicazione Azure AD

**Tipo:** Categoria del **servizio** funzionalità modificata: accesso condizionale **-funzionalità del prodotto:** sicurezza delle identità & protezione

Sono state aggiunte altre applicazioni che supportano l'accesso condizionale basato sull'applicazione. Ora è possibile accedere a Office 365 e ad altre app cloud connesse ad Azure AD usando queste app client approvate.

Le applicazioni seguenti verranno aggiunte entro le fine di febbraio:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Per altre informazioni, vedere:

- [Requisito per le app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Accesso condizionale basato su app Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Condizioni per l'utilizzo l'aggiornamento all'esperienza mobile

**Tipo:** **Categoria servizio** funzionalità modificata: Condizioni per l'utilizzo **funzionalità del prodotto:** conformità

Quando vengono visualizzate le condizioni per l'utilizzo, è ora possibile fare clic su **problemi di visualizzazione? Fare clic qui**. Questo collegamento consente di aprire le Condizioni per l'utilizzo in modo nativo sul dispositivo. Indipendentemente dalle dimensioni del carattere del documento o dalle dimensioni dello schermo del dispositivo, è possibile ingrandire la visualizzazione finché non si riesce a leggere il documento.

---

## <a name="january-2018"></a>Gennaio 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel mese di gennaio 2018 sono state aggiunte le nuove app seguenti con il supporto della federazione alla raccolta di app:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), directory federata IriusRisk e [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---

### <a name="sign-in-with-additional-risk-detected"></a>Rilevato accesso con rischi aggiuntivi

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità di protezione delle identità del **prodotto:** sicurezza delle identità & protezione

Le informazioni che si ottengono per un rilevamento dei rischi rilevato sono legate alla sottoscrizione del Azure AD. Con l'edizione Azure AD Premium P2, si ottengono le informazioni più dettagliate su tutti i rilevamenti sottostanti.

Con l'edizione Azure AD Premium P1, i rilevamenti che non sono coperti dalla licenza vengono visualizzati come l'accesso al rilevamento dei rischi con rischi aggiuntivi rilevati.

Per altre informazioni, vedere [Rilevamenti dei rischi in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Possibilità di nascondere le applicazioni di Office 365 dai pannelli di accesso dell'utente finale

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app personali: SSO

Ora è possibile gestire in maniera più efficiente la modalità di visualizzazione delle applicazioni di Office 365 nei pannelli di accesso dell'utente tramite una nuova impostazione utente. Questa opzione è utile per ridurre il numero di app nei pannelli di accesso di un utente se si preferisce visualizzare solo le app di Office nel portale di Office. L'impostazione è disponibile in **Impostazioni utente** ed è denominata **Gli utenti possono visualizzare solo le app di Office 365 nel portale di Office 365**.

Per altre informazioni, vedere [Nascondere un'applicazione dall'esperienza utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Accesso trasparente alle app abilitato per l'accesso SSO basato su password direttamente dall'URL dell'app

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app personali: SSO

L'estensione del browser App personali è ora disponibile tramite un utile strumento che mette a disposizione la funzionalità Single-Sign On come collegamento nel browser. Dopo l'installazione, nel browser comparirà un'icona di avvio delle app che assicurerà un accesso rapido alle app. Gli utenti possono ora sfruttare i vantaggi seguenti:

- La possibilità di accedere direttamente alle app basate su password-SSO dalla pagina di accesso dell'app
- Avvio delle app tramite la funzionalità di ricerca rapida
- Collegamenti alle app usate di recente dall'estensione
- L'estensione è disponibile per il download in Microsoft Edge, Chrome e Firefox.

Per altre informazioni, vedere [Estensione per l'accesso sicuro alle app personali](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Esperienza di amministrazione di Azure AD nel portale di Azure classico ritirata

**Tipo:** Categoria di **servizio** deprecata: Azure ad **funzionalità del prodotto:** directory

A partire dall'8 gennaio 2018, l'esperienza di amministrazione di Azure AD nel portale di Azure classico è stata ritirata. Questo ritiro ha avuto luogo in concomitanza con quello del portale di Azure classico. In futuro è consigliabile usare il [centro di amministrazione di Azure AD](https://aad.portal.azure.com) per tutte le operazioni di amministrazione basate sul portale di Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Il portale Web di PhoneFactor è stato ritirato

**Tipo:** Categoria di **servizio** deprecata: Azure ad **funzionalità del prodotto:** directory

A partire dall'8 gennaio 2018, il portale Web di PhoneFactor è stato ritirato. Questo portale è stato usato per l'amministrazione del server MFA, ma tali funzionalità sono state spostate nel portale di Azure all'indirizzo portal.azure.com.

La configurazione di MFA è accessibile da **Azure Active Directory \> MFA Server**.

---

### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati

**Tipo:** Categoria di **servizio** deprecata: **funzionalità del prodotto** per Reporting: gestione del ciclo di vita delle identità


Considerando la disponibilità generale della nuova console di amministrazione di Azure Active Directory e delle nuove API ora disponibili per i report sulle attività e sulla sicurezza, le API di report nell'endpoint "/reports" sono state ritirate a partire dal 31 dicembre 2017.

**Elementi disponibili**

Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API offre funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. È ora possibile accedere ai dati disponibili in precedenza tramite i report di sicurezza tramite l'API di rilevamento dei rischi di Identity Protection in Microsoft Graph.

Per altre informazioni, vedere:

- [Introduzione all'API di creazione report di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Dicembre 2017

### <a name="terms-of-use-in-the-access-panel"></a>Condizioni per l'utilizzo nel riquadro di accesso

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** conformità

È ora possibile passare al riquadro di accesso e visualizzare le condizioni per l'utilizzo che sono state precedentemente accettate.

A tale scopo, seguire questa procedura:

1. Aprire il [portale App personali](https://myapps.microsoft.com) ed eseguire l'accesso.

2. Nell'angolo in alto a destra selezionare il nome e quindi scegliere **Profilo** dall'elenco.

3. In **Profilo** selezionare **Verificare le condizioni d'uso**.

4. È ora possibile verificare le condizioni per l'utilizzo accettate.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-azure-ad-sign-in-experience"></a>Nuova esperienza di accesso di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad **funzionalità del prodotto:** autenticazione utente

Azure AD e le interfacce utente del sistema di identità dell'account Microsoft sono stati riprogettati in modo da avere un aspetto uniforme e coerente. Inoltre, nella pagina di accesso di Azure AD viene visualizzato prima di tutto il nome utente, seguito dalle credenziali in una seconda schermata.

Per altre informazioni, vedere [The new Azure AD sign-in experience is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (La nuova esperienza di accesso di AD Azure è ora in anteprima pubblica).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Meno richieste di accesso: nuova esperienza di tipo "Mantieni l'accesso" per l'accesso ad Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad **funzionalità del prodotto:** autenticazione utente

La casella di controllo **Mantieni l'accesso** nella pagina di accesso di Azure AD è stata sostituita con un nuovo prompt che viene visualizzato dopo la corretta esecuzione dell'autenticazione.

Se risponde **Sì** a questo prompt, il servizio offre un token di aggiornamento permanente. Questo comportamento è analogo a quello che succedeva selezionando la casella di controllo **Mantieni l'accesso** nell'esperienza precedente. Per i tenant federati, questo prompt viene visualizzato dopo la corretta autenticazione con il servizio federato.

Per altre informazioni, vedere [Fewer sign-in prompts: The new "keep me signed in" experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Meno prompt di accesso: la nuova esperienza di tipo "Mantieni l'accesso" per Azure AD è in anteprima).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Aggiunta configurazione per richiedere l'espansione delle condizioni per l'utilizzo prima dell'accettazione

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** conformità

Un'opzione per gli amministratori richiede che gli utenti espandano le condizioni per l'utilizzo prima di accettarle.

Selezionare **Sì** o **No** per richiedere che gli utenti espandano le condizioni per l'utilizzo. L'impostazione **Sì** richiede che gli utenti visualizzino le condizioni per l'utilizzo prima di accettarle.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Attivazione con ambito per le assegnazioni di ruolo idonee

**Tipo:** Nuova **Categoria servizio funzionalità:** Privileged Identity Management **funzionalità del prodotto:** Privileged Identity Management

È possibile usare l'attivazione con ambito per attivare le assegnazioni di ruolo delle risorse di Azure idonee con meno autonomia rispetto alle impostazioni predefinite dell'assegnazione originale. Un esempio è se all'utente viene assegnato il ruolo di proprietario di una sottoscrizione nel tenant. Con l'attivazione con ambito, è possibile attivare il ruolo di proprietario per un massimo di cinque risorse contenute all'interno della sottoscrizione (ad esempio gruppi di risorse e macchine virtuali). La definizione dell'ambito per l'attivazione potrebbe ridurre la possibilità di apportare modifiche indesiderate alle risorse di Azure critiche.

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (Che cos'è Azure AD Privileged Identity Management?).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nuove app federate nella raccolta di app di Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app aziendali: integrazione di terze parti

Nel dicembre 2017 sono state aggiunte queste nuove app con il supporto della federazione alla raccolta di app:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [integrazione di Azure AD con MobileIron](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo di resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket di resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, integrazione di Azure AD con Zenegy.

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Flussi di lavoro di approvazione per i ruoli della directory di Azure AD

**Tipo:** **Categoria servizio** funzionalità modificata: Privileged Identity Management **funzionalità del prodotto:** Privileged Identity Management

Il flusso di lavoro di approvazione per i ruoli della directory di Azure AD è disponibile a livello generale.

Con il flusso di lavoro di approvazione, gli amministratori dei ruoli con privilegi possono fare in modo che i membri con ruolo idoneo richiedano l'attivazione del ruolo prima di poter usare il ruolo con privilegi. A più utenti e gruppi possono essere delegate responsabilità di approvazione. I membri con ruolo idoneo ricevono una notifica quando l'approvazione è completata e il ruolo è attivo.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticazione pass-through: supporto per Skype for Business

**Tipo:** Modifica della **categoria del servizio funzionalità:** autenticazione (accessi) **funzionalità del prodotto:** autenticazione utente

L'autenticazione pass-through supporta ora l'accesso degli utenti alle applicazioni client Skype for Business che supportano l'autenticazione moderna, che include topologie online e ibride.

Per altre informazioni, vedere [Skype for Business topologies supported with modern authentication](https://technet.microsoft.com/library/mt803262.aspx) (Topologie di Skype for Business supportate con l'autenticazione moderna).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aggiornamenti di Azure AD Privileged Identity Management per Controllo degli accessi in base al ruolo di Azure (anteprima)

**Tipo:** **Categoria servizio** funzionalità modificata: Privileged Identity Management **funzionalità del prodotto:** Privileged Identity Management

Con l'aggiornamento dell'anteprima pubblica di Azure AD Privileged Identity Management (PIM) per Controllo degli accessi in base al ruolo di Azure, è ora possibile:

* Usare Just Enough Administration.
* Richiedere l'approvazione per l'attivazione dei ruoli delle risorse.
* Pianificare l'attivazione futura di un ruolo che richiede l'approvazione per entrambi i ruoli di Azure AD e Controllo degli accessi in base al ruolo di Azure.

Per altre informazioni, vedere [Privileged Identity Management for Azure resources (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) (Privileged Identity Management per risorse di Azure (anteprima)).

---

## <a name="november-2017"></a>Novembre 2017

### <a name="access-control-service-retirement"></a>Ritiro del Servizio di controllo di accesso

**Tipo:** Pianificare la **categoria di servizi** di modifica: servizio di controllo di accesso funzionalità del **prodotto:** servizio di controllo di accesso

Controllo di accesso di Azure Active Directory, noto anche come Servizio di controllo di accesso, verrà deprecato alla fine del 2018. Altre informazioni, tra cui una pianificazione dettagliata e istruzioni di carattere generale sulla migrazione, saranno disponibili nelle prossime settimane. È possibile lasciare un commento in questa pagina con eventuali domande sul Servizio di controllo di accesso. Un membro del team sarà a disposizione per rispondere.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Limitazione dell'accesso del browser a Intune Managed Browser

**Tipo:** Pianificare la **categoria del servizio** di modifica: accesso condizionale funzionalità del **prodotto:** sicurezza e protezione delle identità

È possibile limitare l'accesso del browser a Office 365 e ad altre app cloud connesse ad Azure AD tramite Intune Managed Browser come app approvata.

È ora possibile configurare la condizione seguente per l'accesso condizionale basato sull'applicazione:

**App client:** Browser

**Qual è l'effetto della modifica?**

Attualmente, l'accesso è bloccato quando si usa questa condizione. Quando l'anteprima sarà disponibile, tutti gli accessi richiederanno l'uso dell'applicazione del browser gestita.

Cercare questa funzionalità e altre informazioni nelle note sulla versione e nei blog che verranno pubblicati a breve.

Per altre informazioni, vedere [accesso condizionale in Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app Azure AD

**Tipo:** Pianificare la **categoria del servizio** di modifica: accesso condizionale funzionalità del **prodotto:** sicurezza e protezione delle identità

Le app seguenti sono nell'elenco delle [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Per altre informazioni, vedere:

- [Requisito per le app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Accesso condizionale basato su app Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Supporto di più lingue per le condizioni per l'utilizzo

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** conformità

Gli amministratori possono ora creare nuove condizioni per l'utilizzo contenenti più documenti PDF. È possibile contrassegnare i documenti PDF con una lingua corrispondente. Gli utenti visualizzano il PDF con la lingua corrispondente in base alle proprie preferenze. Se non c'è corrispondenza, viene visualizzata la lingua predefinita.

---

### <a name="real-time-password-writeback-client-status"></a>Stato del client relativamente al writeback delle password in tempo reale

**Tipo:** Nuova **Categoria servizio funzionalità:** reimpostazione password self-service **funzionalità del prodotto:** autenticazione utente

È ora possibile esaminare lo stato del client relativamente al writeback delle password in locale. Questa opzione è disponibile nella sezione **Integrazione locale** della pagina [Reimpostazione della password](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset).

Se ci sono problemi con la connessione al client di writeback locale, viene visualizzato un messaggio di errore che specifica:

- Informazioni sui motivi per cui non è possibile connettersi al client di writeback locale.
- un collegamento alla documentazione di supporto alla risoluzione del problema.

Per altre informazioni, vedere [integrazione locale](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Accesso condizionale basato su app Azure AD

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad **funzionalità del prodotto:** protezione e sicurezza delle identità

È ora possibile limitare l'accesso a Office 365 e ad altre app Cloud connesse Azure AD alle [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) che supportano i criteri di protezione delle app di Intune usando [Azure ad l'accesso condizionale basato su app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). I criteri di protezione app di Intune vengono usati per configurare e proteggere i dati aziendali in queste applicazioni client.

Combinando i criteri di accesso condizionale basati su [app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) e basati su [dispositivi](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) , è possibile proteggere i dati per i dispositivi personali e aziendali.

Le condizioni e i controlli seguenti sono ora disponibili per l'uso con l'accesso condizionale basato su App:

**Condizione per le piattaforme supportate**

- iOS
- Android

**Condizione per le app client**

- App per dispositivi mobili e client desktop

**Controllo di accesso**

- Richiedi app client approvata

Per altre informazioni, vedere [Azure ad l'accesso condizionale basato su app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gestione di dispositivi di Azure AD nel portale di Azure

**Tipo:** Nuova **categoria di servizio funzionalità:** registrazione e gestione dei dispositivi funzionalità del **prodotto:** sicurezza e protezione delle identità

È ora possibile individuare tutti i dispositivi connessi ad Azure AD e le attività correlate al dispositivo in un'unica posizione. È disponibile una nuova esperienza di amministrazione per gestire tutte le impostazioni e le identità dei dispositivi nel portale di Azure. In questa versione è possibile:

- Visualizzare tutti i dispositivi disponibili per l'accesso condizionale in Azure AD.
- Visualizzare le proprietà, inclusi i dispositivi aggiunti ad Azure AD ibridi.
- Trovare le chiavi di BitLocker per i dispositivi aggiunti ad Azure AD, gestire il dispositivo con Intune e altro ancora.
- Gestire le impostazioni relative al dispositivo di Azure AD.

Per altre informazioni, vedere [Manage devices by using the Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal) (Gestire dispositivi tramite il portale di Azure).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Supporto per macOS come piattaforma per dispositivi per Azure AD l'accesso condizionale

**Tipo:** Nuova **categoria di servizio funzionalità:** accesso condizionale **-funzionalità del prodotto:** protezione e sicurezza delle identità

È ora possibile includere o escludere macOS come condizione della piattaforma del dispositivo nei criteri di accesso condizionale Azure AD. Con l'aggiunta di macOS alle piattaforme di dispositivi supportati, è possibile:

- **Registrare e gestire i dispositivi macOS con Intune.** Analogamente ad altre piattaforme quali iOS e Android, un'applicazione del portale aziendale è disponibile per macOS per eseguire le registrazioni unificate. È possibile usare la nuova app del portale aziendale per macOS per registrare un dispositivo con Intune e registrarlo con Azure AD.
- **Assicurarsi che i dispositivi macOS siano conformi ai criteri di conformità dell'organizzazione definiti in Intune.** Con Intune nel portale di Azure è ora possibile impostare criteri di conformità per i dispositivi macOS.
- **Limitare l'accesso alle applicazioni in Azure AD solo ai dispositivi macOS conformi.** La creazione di criteri di accesso condizionale ha macOS come opzione di piattaforma del dispositivo separata. È ora possibile creare criteri di accesso condizionale specifici di macOS per il set di applicazioni di destinazione in Azure.

Per altre informazioni, vedere:

- [Creare criteri di conformità per i dispositivi macOS con Intune](https://aka.ms/macoscompliancepolicy)
- [Accesso condizionale in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Estensione di Server dei criteri di rete per Azure Multi-Factor Authentication

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità di autenticazione a più fattori **:** autenticazione utente

L'estensione di Server dei criteri di rete per Azure Multi-Factor Authentication aggiunge funzionalità di autenticazione a più fattori basata sul cloud nell'infrastruttura di autenticazione corrente usando i server esistenti. Con l'estensione di Server dei criteri di rete, è possibile aggiungere la verifica con telefonata, messaggio di testo o app telefonica al flusso di autenticazione esistente. Pertanto, non è necessario installare, configurare e gestire nuovi server.

Questa estensione è stata creata per le organizzazioni che vogliono proteggere le connessioni della rete privata virtuale senza distribuire il server Azure Multi-Factor Authentication. L'estensione di Server dei criteri di rete funge da adattatore tra RADIUS e Azure Multi-Factor Authentication basato su cloud per fornire un secondo fattore di autenticazione per utenti federati o sincronizzati.

Per altre informazioni, vedere [Integrate your existing Network Policy Server infrastructure with Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension) (Integrare l'infrastruttura di Server dei criteri di rete esistente con Azure Multi-Factor Authentication).

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Ripristinare o rimuovere definitivamente gli utenti eliminati

**Tipo:** Nuova **Categoria servizio funzionalità:** gestione utenti **funzionalità del prodotto:** directory

Nel centro di amministrazione di Azure Active Directory è possibile:

- Ripristinare un utente eliminato.
- Eliminare definitivamente un utente.

**Per provare il servizio:**

1. Nel centro di amministrazione di Azure Active Directory, nella sezione **Gestisci** selezionare [Tutti gli utenti](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All).

2. Dall'elenco **Mostra** selezionare **Utenti eliminati di recente**.

3. Selezionare uno o più utenti eliminati di recente e quindi ripristinarli oppure eliminarli definitivamente.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app Azure AD

**Tipo:** Categoria del **servizio** funzionalità modificata: funzionalità di accesso condizionale del **prodotto:** sicurezza e protezione delle identità

Le app seguenti sono state aggiunte all'elenco di [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft Planner
- Azure Information Protection

Per altre informazioni, vedere:

- [Requisito per le app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Accesso condizionale basato su app Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Usare "OR" tra i controlli in un criterio di accesso condizionale

**Tipo:** Categoria del **servizio** funzionalità modificata: funzionalità di accesso condizionale del **prodotto:** sicurezza e protezione delle identità

È ora possibile usare "OR" (Richiedi uno dei controlli selezionati) per i controlli di accesso condizionale. È possibile usare questa funzionalità per creare criteri usando l'operatore "OR" tra i controlli di accesso. È ad esempio possibile usare questa funzionalità per creare un criterio che richiede all'utente di accedere usando Multi-Factor Authentication o in alternativa di usare un dispositivo conforme.

Per altre informazioni, vedere [controlli in Azure ad accesso condizionale](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

---

### <a name="aggregation-of-real-time-risk-detections"></a>Aggregazione di rilevamenti di rischi in tempo reale

**Tipo:** Categoria del **servizio** di funzionalità modificata: **funzionalità del prodotto** Identity Protection: sicurezza e protezione delle identità

In Azure AD Identity Protection, tutti i rilevamenti di rischio in tempo reale originati dallo stesso indirizzo IP in un determinato giorno vengono ora aggregati per ogni tipo di rilevamento dei rischi. Questa modifica limita il volume dei rilevamenti dei rischi visualizzati senza alcuna modifica nella sicurezza dell'utente.

Il rilevamento in tempo reale sottostante funziona ogni volta che l'utente effettua l'accesso. Se si impostano criteri di sicurezza relativi al rischio di accesso per Multi-Factor Authentication o il blocco dell'accesso, verranno attivati per ogni accesso rischioso.

---

## <a name="october-2017"></a>Ottobre 2017

### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati

**Tipo:** Pianificare la **categoria del servizio** di modifica: Reporting **Product Capability:** gestione del ciclo di vita delle identità

Il portale di Azure offre:

- Una nuova console di amministrazione di Azure AD.
- Nuove API per i report sulle attività e sulla sicurezza.

Considerando la disponibilità di queste nuove funzionalità, le API di report nell'endpoint /reports verranno ritirate il 10 dicembre 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Rilevamento automatico dei campi di accesso

**Tipo:** **Categoria servizio fisso:** funzionalità del **prodotto** app personali: Single Sign-on

Azure AD supporta il rilevamento automatico del campo di accesso per le applicazioni che eseguono il rendering di un campo HTML per nome utente e password. Questi passaggi sono illustrati in [Come acquisire manualmente i campi di accesso per un'applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). È possibile trovare questa funzionalità aggiungendo un'applicazione *non nella raccolta* nella pagina **Applicazioni aziendali** del [portale di Azure](https://aad.portal.azure.com). È anche possibile configurare la modalità **Single Sign-On** in questa nuova applicazione impostandola su **Accesso Single Sign-On basato su password**, immettere un URL Web e quindi salvare la pagina.

A causa di un problema del servizio, questa funzionalità è stata temporaneamente disabilitata. Il problema è stato risolto e il rilevamento automatico del campo di accesso è nuovamente disponibile.

---

### <a name="new-multi-factor-authentication-features"></a>Nuove funzionalità di Multi-Factor Authentication

**Tipo:** Nuova **categoria di servizio funzionalità:** funzionalità di autenticazione a più fattori **:** sicurezza e protezione delle identità

Multi-Factor Authentication (MFA) è fondamentale per proteggere l'organizzazione. Per rendere più adattive le credenziali e semplificare l'esperienza, sono state aggiunte le funzionalità seguenti:

- I risultati della richiesta a più fattori sono integrati direttamente nel report di accesso di Azure AD, che include l'accesso a livello di codice ai risultati dell'autenticazione a più fattori.
- La configurazione di MFA è integrata in modo più approfondito nell'esperienza di configurazione di Azure AD nel portale di Azure.

Con questa versione di anteprima pubblica, la gestione e la creazione di report di MFA sono alla base dell'esperienza di configurazione di Azure Active Directory. È ora possibile gestire la funzionalità del portale di gestione di MFA nell'esperienza di Azure AD.

Per altri dettagli, vedere [Informazioni di riferimento sui report dell'autenticazione a più fattori nel portale di Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa).

---

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

**Tipo:** Nuova **Categoria servizio funzionalità:** condizioni per l'utilizzo **funzionalità del prodotto:** conformità

È possibile usare le condizioni per l'utilizzo di Azure AD per presentare agli utenti informazioni quali dichiarazioni di non responsabilità pertinenti per requisiti legali o di conformità.

È possibile usare le condizioni per l'utilizzo di Azure AD negli scenari seguenti:

- Condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione
- Condizioni per l'utilizzo specifiche basate sugli attributi di un utente (ad esempio, medici o infermieri oppure dipendenti nazionali o internazionali, creazione tramite gruppi dinamici)
- Condizioni per l'utilizzo specifiche per l'accesso ad app aziendali a impatto elevato, come Salesforce

Per altre informazioni, vedere [Condizioni per l'utilizzo di Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Miglioramenti di Privileged Identity Management

**Tipo:** Nuova **Categoria servizio funzionalità:** Privileged Identity Management **funzionalità del prodotto:** Privileged Identity Management

Con Azure AD Privileged Identity Management, è possibile gestire, controllare e monitorare l'accesso alle risorse di Azure (anteprima) all'interno dell'organizzazione per:

- Sottoscrizioni
- Gruppi di risorse
- Macchine virtuali

Tutte le risorse nel portale di Azure che usano la funzionalità Controllo degli accessi in base al ruolo di Azure possono trarre vantaggio da tutte funzionalità di sicurezza e gestione del ciclo di vita offerte da Azure AD Privileged Identity Management.

Per altre informazioni, vedere [Privileged Identity Management for Azure resources](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) (Privileged Identity Management per risorse di Azure).

---

### <a name="access-reviews"></a>Verifiche di accesso

**Tipo:** Nuova **Categoria servizio funzionalità:** verifiche di accesso **funzionalità del prodotto:** conformità

Le organizzazioni possono usare le verifiche di accesso (anteprima) per gestire in modo efficiente l'appartenenza ai gruppi e l'accesso alle applicazioni aziendali:

- È possibile ripetere la certificazione dell'accesso degli utenti guest usando le verifiche di accesso per i rispettivi accessi alle applicazioni e le appartenenze ai gruppi. I revisori possono decidere in modo efficiente se consentire ai guest l'accesso continuo sulla base delle informazioni dettagliate fornite dalle verifiche di accesso.
- È possibile ripetere la certificazione dell'accesso dei dipendenti alle applicazioni e delle appartenenze ai gruppi con le verifiche di accesso.

È possibile raccogliere i controlli delle verifiche di accesso in programmi rilevanti per l'organizzazione, in modo da controllare le verifiche per ottenere informazioni sulla conformità o sulle applicazioni più a rischio.

Per altre informazioni, vedere [Verifiche di accesso di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Possibilità di nascondere le applicazioni di terze parti dall'utilità di avvio delle app di Office 365 e di App personali

**Tipo:** Nuova **Categoria servizio funzionalità:** funzionalità del **prodotto** app personali: Single Sign-on

È ora possibile gestire meglio le app visualizzate nei portali degli utenti tramite una nuova proprietà che consente di **nascondere le app**. È possibile nascondere le app nei casi in cui vengano visualizzati riquadri delle app per i servizi back-end o riquadri duplicati che creano disordine nelle icone di avvio delle app degli utenti. È possibile abilitare o disabilitare questa proprietà nella sezione **Proprietà** dell'app di terze parti tramite l'opzione **Visibile agli utenti?**. È anche possibile nascondere un'app a livello di codice tramite PowerShell.

Per altre informazioni, vedere [Nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).


**Elementi disponibili**

 Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API offre funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. È ora possibile accedere ai dati disponibili in precedenza tramite i report di sicurezza tramite l'API rilevamento dei rischi di Identity Protection in Microsoft Graph.


## <a name="september-2017"></a>Settembre 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix per Identity Manager

**Tipo:** Modifica della **categoria del servizio funzionalità:** Identity Manager **funzionalità del prodotto:** gestione del ciclo di vita delle identità

Dal 25 settembre 2017 è disponibile un pacchetto hotfix rollup (build 4.4.1642.0) per Identity Manager 2016 Service Pack 1. Questo pacchetto rollup:

- Risolve alcuni problemi e aggiunge miglioramenti.
- È un aggiornamento cumulativo che sostituisce tutti gli aggiornamenti di Identity Manager 2016 Service Pack 1 fino alla build 4.4.1459.0 per Identity Manager 2016.
- Richiede Identity Manager 2016 build 4.4.1302.0.

Per altre informazioni, vedere [Pacchetto hotfix rollup (build 4.4.1642.0) disponibile per Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562).

---
