---
title: Archivio per le novità di Azure Active Directory | Microsoft Docs
description: Le novità delle note sulla versione nella sezione Panoramica di questo insieme di contenuti includono 6 mesi di attività. Dopo 6 mesi, gli elementi vengono rimossi dall'articolo principale e inseriti in questo articolo di archivio.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15f5563c11e6abc5452ace01822e5559d04808df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369651"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archivio per le novità di Azure Active Directory

L'articolo principale Note sulla [versione di What's new in Azure Active Directory?](whats-new.md) contiene gli aggiornamenti per gli ultimi sei mesi, mentre questo articolo contiene tutte le informazioni meno recenti.

Novità di Azure Active Directory Le note sulla versione forniscono informazioni su:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

---

## <a name="september-2019"></a>Settembre 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Pianificare le modifiche: Deprecazione dei pacchetti di contenuto di Power BI

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

A partire dal 1 ottobre 2019 Power BI inizierà a deprecare tutti i pacchetti di contenuto, incluso il pacchetto di contenuto Di Power BI di Azure AD. In alternativa a questo pacchetto di contenuto, è possibile usare le cartelle di lavoro di Azure AD per ottenere informazioni dettagliate sui servizi correlati ad Azure AD. Sono in arrivo cartelle di lavoro aggiuntive, incluse le cartelle di lavoro sui criteri di accesso condizionale in modalità solo report, informazioni dettagliate basate sul consenso dell'app e altro ancora.

Per altre informazioni sulle cartelle di lavoro, vedere Come usare le cartelle di lavoro di Monitoraggio di Azure per i report di Azure Active Directory.For more information about the workbooks, see [How to use Azure Monitor workbooks for Azure Active Directory reports](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Per altre informazioni sulla deprecazione dei pacchetti di contenuto, vedere il post di blog Annuncio della disponibilità generale delle app modello di Power BI.For more information about the deprecation of the content packs, see the [Announcing Power BI template apps general availability](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) blog in blog.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Il mio profilo è la ridenominazione e l'integrazione con la pagina dell'account di Microsoft Office

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Il mio profilo/account  
**Funzionalità del prodotto:** Collaborazione

A partire da ottobre, l'esperienza Del mio profilo diventerà Il mio account. Come parte di questo cambiamento, ovunque attualmente dice, **Il mio profilo** cambierà in Il mio **account**. Oltre alla modifica della denominazione e ad alcuni miglioramenti alla progettazione, l'esperienza aggiornata offrirà un'ulteriore integrazione con la pagina dell'account di Microsoft Office. In particolare, potrai accedere alle installazioni e agli abbonamenti di Office dalla pagina **Panoramica dell'account,** insieme alle preferenze di contatto correlate a Office dalla pagina **Privacy.**

Per ulteriori informazioni sull'esperienza My Profile (anteprima), vedere Panoramica del [portale My Profile (anteprima).](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gestire in blocco gruppi e membri usando file CSV nel portale di Azure AD (anteprima pubblica)Bulk manage groups and members using CSV files in the Azure AD portal (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Siamo lieti di annunciare la disponibilità dell'anteprima pubblica delle esperienze di gestione di gruppi in blocco nel portale di Azure AD. È ora possibile usare un file CSV e il portale di Azure AD per gestire gruppi ed elenchi di membri, tra cui:You can now use a CSV file and the Azure AD portal to manage groups and member lists, including:

- Aggiunta o rimozione di membri da un gruppo.

- Download dell'elenco dei gruppi dalla directory.

- Download dell'elenco dei membri del gruppo per un gruppo specifico.

Per ulteriori informazioni, vedere [Aggiunta in blocco di membri,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)Rimozione in blocco dei [membri](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), Elenco membri download [in blocco](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)ed Elenco gruppi di download [in blocco](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Il consenso dinamico è ora supportato tramite un nuovo endpoint di consenso dell'amministratore

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Abbiamo creato un nuovo endpoint di consenso amministratore per supportare il consenso dinamico, che è utile per le app che vogliono usare il modello di consenso dinamico sulla piattaforma Microsoft Identity.We've created a new admin consent endpoint to support dynamic consent, which is helpful for apps that want to use the dynamic consent model on the Microsoft Identity platform.

Per ulteriori informazioni su come utilizzare questo nuovo endpoint, vedere [Utilizzo dell'endpoint di consenso dell'amministratore.](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Settembre 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

A settembre 2019, abbiamo aggiunto queste 29 nuove app con il supporto della federazione alla galleria di app:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [accesso MS Azure SSO per&trade; l'Ufficio di conformità Ethidex - Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [Portale iServer](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur Viaggi e spese](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), `https://apps.yeeflow.com/`ARC [Facilities](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), Wide [Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), `https://app.penneo.com/`, `https://app.testhtm.com/settings/email-integration`, [Cintoo Cloud](https://aec.cintoo.com/login), [ Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [Hosted Heritage Online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), Discovery Benefits [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/),`https://itask.yipinapp.com/`

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nuovo ruolo Lettore globale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso

A partire dal 24 settembre 2019, inizieremo a implementare un nuovo ruolo di Azure Active Directory (AD) denominato Lettore globale. Questa implementazione inizierà con la produzione e i clienti cloud globali (GCC), che finiranno in tutto il mondo nel mese di ottobre.

Il ruolo Lettore globale è la controparte di sola lettura di Global Administrator. Gli utenti in questo ruolo possono leggere le impostazioni e le informazioni amministrative nei servizi di Microsoft 365, ma non possono eseguire azioni di gestione. È stato creato il ruolo Lettore globale per ridurre il numero di amministratori globali nell'organizzazione. Poiché gli account amministratore globale sono potenti e vulnerabili agli attacchi, è consigliabile disporre di meno di cinque amministratori globali. È consigliabile utilizzare il ruolo Lettore globale per la pianificazione, gli audit o le indagini. Si consiglia inoltre di utilizzare il ruolo Lettore globale in combinazione con altri ruoli di amministratore limitati, ad esempio Amministratore di Exchange, per eseguire il lavoro senza richiedere il ruolo di amministratore globale.

Il ruolo Lettore globale funziona con la nuova interfaccia di amministrazione di Microsoft 365, l'interfaccia di amministrazione di Exchange, l'interfaccia di amministrazione di Teams, l'interfaccia di amministrazione, l'interfaccia di amministrazione, il Centro conformità, l'interfaccia di amministrazione di Azure AD e l'interfaccia di amministrazione della gestione dei dispositivi.

>[!NOTE]
> All'inizio dell'anteprima pubblica, il ruolo Lettore globale non funzionerà: SharePoint, Gestione degli accessi con privilegi, Customer Lockbox, etichette di sensibilità, Ciclo di vita di Teams, rapporti di team & Call Analytics, Gestione dei dispositivi telefonici IP team e Team App Catalog. 

Per altre informazioni, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory.For](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)more information, see Administrator role permissions in Azure Active Directory .

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Accedere a un server di report locale dall'app Power BI Mobile usando il proxy di applicazione di Azure Active DirectoryAccess an on-premises Report Server from your Power BI Mobile app using Azure Active Directory Application Proxy

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

La nuova integrazione tra l'app Power BI per dispositivi mobili e il proxy di applicazione di Azure AD consente di accedere in modo sicuro all'app Power BI per dispositivi mobili e di visualizzare i report dell'organizzazione ospitati nel server di report di Power BI locale.

Per informazioni sull'app Power BI Mobile, inclusa la posizione in cui scaricare l'app, vedere il [sito di Power BI.](https://powerbi.microsoft.com/mobile/) Per altre informazioni su come configurare l'app Power BI per dispositivi mobili con il proxy di applicazione di Azure AD, vedere [Abilitare l'accesso remoto a Power BI Mobile con](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)il proxy di applicazione di Azure AD.

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>È disponibile una nuova versione del modulo PowerShell di AzureADPreview

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Capacità del prodotto:** Directory

Sono stati aggiunti nuovi cmdlet al modulo AzureADPreview per definire e assegnare ruoli personalizzati in Azure AD, tra cui:New cmdlets were added to the AzureADPreview module, to help define and assign custom roles in Azure AD, including:

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
**Capacità del prodotto:** Directory

È stata rilasciata una versione aggiornata di Azure AD Connect per i clienti con aggiornamento automatico. Questa nuova versione include diverse nuove funzionalità, miglioramenti e correzioni di bug. Per altre informazioni su questa nuova versione, vedere [Azure AD Connect: cronologia delle versioni.](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA) Server, versione 8.0.2 è ora disponibileAzure Multi-Factor Authentication (MFA) Server, version 8.0.2 is now available

**Tipo:** Correzione  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Se si è un cliente esistente, che ha attivato MFA Server prima del 1 luglio 2019, è ora possibile scaricare la versione più recente di MFA Server (versione 8.0.2). In questa nuova versione:

- Risolto un problema in modo che quando la sincronizzazione di Azure AD modifica un utente da Disabilitato ad Abilitato, viene inviato un messaggio di posta elettronica all'utente.

- Risolto un problema in modo che i clienti possano eseguire correttamente l'aggiornamento, continuando a utilizzare la funzionalità Tag.

- Aggiunto il codice del paese Kosovo (n. 383).

- Aggiunta la registrazione di controllo di bypass una tantando al file MultiFactorAuthSvc.log.Added one-time bypass audit logging to the MultiFactorAuthSvc.log.

- Miglioramento delle prestazioni per l'SDK del servizio Web.

- Correzione di altri bug secondari.

A partire dal 1 luglio 2019, Microsoft ha smesso di offrire MFA Server per le nuove distribuzioni. I nuovi clienti che richiedono l'autenticazione a più fattori devono usare l'autenticazione a più fattori basata su cloud. Per altre informazioni, vedere Pianificazione di una distribuzione di [Azure Multi-Factor Authentication basata su cloud.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

---

## <a name="august-2019"></a>Agosto 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>La ricerca, il filtro e l'ordinamento migliorato per i gruppi sono disponibili nel portale di Azure AD (anteprima pubblica)Enhanced search, filtering, and sorting for groups is available in the Azure AD portal (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Siamo lieti di annunciare la disponibilità dell'anteprima pubblica delle esperienze avanzate correlate ai gruppi nel portale di Azure AD. Questi miglioramenti consentono di gestire meglio i gruppi e gli elenchi di membri, fornendo:

- Funzionalità di ricerca avanzate, ad esempio la ricerca di sottostringhe negli elenchi di gruppi.
- Opzioni avanzate di filtro e ordinamento negli elenchi dei membri e dei proprietari.
- Nuove funzionalità di ricerca per gli elenchi di membri e proprietari.
- Conteggi di gruppi più accurati per gruppi di grandi dimensioni.

Per altre informazioni, vedere Gestire gruppi nel portale di Azure.For more information, see [Manage groups in the Azure portal.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Sono disponibili nuovi ruoli personalizzati per la gestione della registrazione delle app (anteprima pubblica)New custom roles are available for app registration management (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso

I ruoli personalizzati (disponibili con una sottoscrizione ad Azure AD P1 o P2) possono ora fornire un accesso granulare, consentendo di creare definizioni di ruolo con autorizzazioni specifiche e quindi di assegnare tali ruoli a risorse specifiche. Attualmente, puoi creare ruoli personalizzati usando le autorizzazioni per la gestione delle registrazioni delle app e quindi assegnando il ruolo a un'app specifica. Per altre informazioni sui ruoli personalizzati, vedere [Ruoli di amministratore personalizzati in Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Se sono necessarie autorizzazioni o risorse aggiuntive supportate, che attualmente non vengono visualizzate, è possibile inviare commenti e suggerimenti al sito di commenti e suggerimenti di [Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) e aggiungeremo la richiesta alla road map di aggiornamento.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>I nuovi log di provisioning consentono di monitorare e risolvere i problemi relativi alla distribuzione del provisioning dell'app (anteprima pubblica)New provisioning logs can help you monitor and troubleshoot your app provisioning deployment (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management

Sono disponibili nuovi log di provisioning che consentono di monitorare e risolvere i problemi di distribuzione del provisioning di utenti e gruppi. Questi nuovi file di registro includono informazioni su:

- Gruppi creati correttamente in [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Quali ruoli sono stati importati da [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Quali dipendenti non sono stati importati da [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Per altre informazioni, vedere Provisioning dei report nel portale di [Azure Active Directory (anteprima).](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nuovi report sulla sicurezza per tutti gli amministratori di Azure AD (disponibilità generale)New security reports for all Azure AD administrators (General Availability)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per impostazione predefinita, tutti gli amministratori di Azure AD saranno presto in grado di accedere ai report di sicurezza moderni all'interno di Azure AD. Fino alla fine di settembre, potrai utilizzare il banner nella parte superiore dei moderni rapporti di sicurezza per tornare ai vecchi rapporti.

I report di sicurezza moderni forniranno funzionalità aggiuntive dalle versioni precedenti, tra cui:

- Filtro e ordinamento avanzati
- Azioni collettive, ad esempio la chiusura del rischio per gli utenti
- Conferma di entità compromesse o sicure
- Stato del rischio, che copre: a rischio, dispagato, verificato e confermato compromesso
- Nuovi rilevamenti correlati al rischio (disponibili per i sottoscrittori di Azure AD Premium)New risk-related detections (available to Azure AD Premium subscribers)

Per ulteriori informazioni, vedere [Utenti rischiosi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [Accesso rischiosi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)e [Rilevamenti dei rischi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>L'identità gestita assegnata dall'utente è disponibile per le macchine virtuali e i set di scalabilità delle macchine virtuali (disponibilità generale)User-assigned managed identity is available for Virtual Machines and Virtual Machine Scale Sets (General Availability)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identità gestite per le risorse di AzureManaged identities for Azure resources  
**Funzionalità del prodotto:** esperienza di sviluppo

Le identità gestite assegnate dall'utente sono ora disponibili in genere per le macchine virtuali e i set di scalabilità di macchine virtuali. In questo modo, Azure può creare un'identità nel tenant di Azure AD considerata attendibile dalla sottoscrizione in uso e che può essere assegnata a una o più istanze del servizio di Azure.As part of this, Azure can create an identity in the Azure AD tenant that's trusted by the subscription in use, and can be assigned to one or more Azure service instances. Per altre informazioni sulle identità gestite assegnate dall'utente, vedere [Che cos'è l'identità gestita per le risorse di Azure?.](https://aka.ms/azuremanagedidentity)

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Gli utenti possono reimpostare le password usando un'app per dispositivi mobili o un token hardware (disponibilità generale)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Funzionalità del prodotto:** Autenticazione utente

Gli utenti che hanno registrato un'app per dispositivi mobili con l'organizzazione possono ora reimpostare la propria password approvando una notifica dall'app Microsoft Authenticator o immettendo un codice dall'app per dispositivi mobili o dal token hardware.

Per altre informazioni, vedere [Funzionamento: reimpostazione della password self-service](https://aka.ms/authappsspr)di Azure AD . Per ulteriori informazioni sull'esperienza utente, vedere [Reimpostare la propria panoramica della password aziendale o dell'istituto](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)di istruzione .

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora la MSAL.NET cache condivisa per gli scenari per conto di

**Tipo:** Correzione  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

A partire dalla libreria di autenticazione di Azure AD (ADAL.NET) versione 5.0.0-preview, gli sviluppatori di app devono [serializzare una cache per ogni account per le app Web e le API Web.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api) In caso contrario, alcuni scenari che utilizzano il [flusso per conto di](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), insieme ad alcuni casi di utilizzo specifici di `UserAssertion`, possono comportare un'elevazione dei privilegi. Per evitare questa vulnerabilità, ADAL.NET ora ignora la libreria di autenticazione Microsoft per dotnet (MSAL.NET) cache condivisa per gli scenari per conto di.

Per ulteriori informazioni su questo problema, vedere [Azure Active Directory Authentication Library Elevation of Privilege Vulnerability](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Agosto 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nell'agosto 2019 sono state aggiunte queste 26 nuove app con il supporto della federazione alla raccolta di app:

[Piattaforma Civica](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport's Inativ Portal (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy Attendance](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial) [CPQSync di Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [&trade; Deliver.media Portal](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [ stashcat AD connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Orologio con colori](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigare Strategic Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Sono disponibili nuove versioni dei moduli di AzureAD PowerShell e AzureADPreview PowerShell

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Capacità del prodotto:** Directory

Sono disponibili nuovi aggiornamenti per i moduli PowerShell di AzureAD e AzureAD Preview:New updates to the AzureAD and AzureAD Preview PowerShell modules are available:

- È `-Filter` stato aggiunto un `Get-AzureADDirectoryRole` nuovo parametro al parametro nel modulo AzureAD. Questo parametro consente di filtrare i ruoli di directory restituiti dal cmdlet.
- Sono stati aggiunti nuovi cmdlet al modulo AzureADPreview per definire e assegnare ruoli personalizzati in Azure AD, tra cui:New cmdlets were added to the AzureADPreview module, to help define and assign custom roles in Azure AD, including:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Miglioramenti all'interfaccia utente del generatore di regole di gruppo dinamico nel portale di AzureImprovements to the UI of the dynamic group rule builder in the Azure portal

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Sono stati apportati alcuni miglioramenti dell'interfaccia utente al generatore di regole del gruppo dinamico, disponibile nel portale di Azure, per semplificare più facilmente la configurazione di una nuova regola o la modifica di regole esistenti. Questo miglioramento della progettazione consente di creare regole con un massimo di cinque espressioni, anziché una sola. Abbiamo anche aggiornato l'elenco delle proprietà del dispositivo per rimuovere le proprietà del dispositivo deprecate.

Per ulteriori informazioni, vedere [Gestire le regole di appartenenza dinamiche](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nuova autorizzazione dell'app Microsoft Graph disponibile per l'uso con le verifiche di accesso

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Recensioni di accesso  
**Capacità del prodotto:** Governance dell'identità

Abbiamo introdotto una nuova autorizzazione `AccessReview.ReadWrite.Membership`dell'app Microsoft Graph, che consente alle app di creare e recuperare automaticamente le verifiche di accesso per le appartenenze ai gruppi e le assegnazioni delle app. Questa autorizzazione può essere utilizzata dai processi pianificati o come parte dell'automazione, senza richiedere un contesto utente connesso.

Per altre informazioni, vedere il blog Di seguito, vedere Esempio di come creare verifiche di accesso di [Azure AD usando le autorizzazioni dell'app Microsoft Graph con PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD activity logs are now available for government cloud instances in Azure Monitor

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare che i log attività di Azure AD sono ora disponibili per le istanze cloud per enti pubblici in Monitoraggio di Azure.We're excited to announce that Azure AD activity logs are now available for government cloud instances in Azure Monitor. È ora possibile inviare i log di Azure AD all'account di archiviazione o a un hub eventi da integrare con gli strumenti SIEM, ad esempio [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)e [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Per altre informazioni sull'impostazione di Monitoraggio di Azure, vedere Log attività di Azure AD in Monitoraggio di Azure.For more information about setting up Azure Monitor, see [Azure AD activity logs in Azure Monitor.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aggiornare gli utenti alla nuova esperienza avanzata delle informazioni di sicurezza

**Tipo:** Funzionalità modificata  
**Categoria di servizio:**  Autenticazioni (account di accesso)   
**Funzionalità del prodotto:** Autenticazione utente

Il 25 settembre 2019, distogliamo la vecchia esperienza delle informazioni di sicurezza non migliorate per la registrazione e la gestione delle informazioni di sicurezza degli utenti e solo l'attivazione della nuova [versione avanzata.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271) Ciò significa che gli utenti non saranno più in grado di utilizzare l'esperienza precedente.

Per ulteriori informazioni sull'esperienza avanzata delle informazioni di sicurezza, vedere la [documentazione dell'amministratore](https://aka.ms/securityinfodocs) e la [documentazione per l'utente.](https://aka.ms/securityinfoguide)

#### <a name="to-turn-on-this-new-experience-you-must"></a>Per attivare questa nuova esperienza, devi:

1. Accedere al portale di Azure come amministratore globale o amministratore utente.

2. Passare a **Impostazioni utente > azure Active Directory > Gestisci impostazioni per le funzionalità**di anteprima del pannello di accesso.

3. Nell'area Avanzata degli utenti possono utilizzare le funzionalità di anteprima per la registrazione e la gestione delle **informazioni di sicurezza,** selezionare **Selezionato**e quindi scegliere un gruppo di utenti o **Tutti** per attivare questa funzionalità per tutti gli utenti nel tenant.

4. Nell'area : gli utenti possono utilizzare le funzionalità di anteprima per la registrazione e la gestione della sicurezza, **selezionare Nessuno.**

5. Salvare le impostazioni.

    Dopo aver salvato le impostazioni, non avrai più accesso alla vecchia esperienza delle info di sicurezza.

>[!Important]
>Se questi passaggi non vengono completati prima del 25 settembre 2019, il tenant di Azure Active Directory verrà abilitato automaticamente per l'esperienza avanzata. Se avete domande, vi registrationpreview@microsoft.compreghiamo di contattarci al .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Le richieste di autenticazione tramite account di accesso POST verranno convalidati in modo più rigoroso

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Capacità del prodotto:** Standard

A partire dal 2 settembre 2019, le richieste di autenticazione utilizzando il metodo POST verranno convalidate in modo più rigoroso rispetto agli standard HTTP. In particolare, gli spazi e le virgolette doppie (") non verranno più rimossi dai valori del modulo di richiesta. Queste modifiche non dovrebbero interrompere i client esistenti e consentono di assicurarsi che le richieste inviate ad Azure AD vengano gestite in modo affidabile ogni volta.

Per altre informazioni, vedere le notifiche di modifiche di rilievo di Azure AD .For more information, see the [Azure AD breaking changes notices](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Luglio 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Pianificare la modifica: aggiornamento del servizio proxy di applicazione per supportare solo TLS 1.2Plan for change: Application Proxy service update to support only TLS 1.2

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Per aiutarti a fornirti la nostra crittografia più forte, inizieremo a limitare l'accesso al servizio proxy di applicazione solo ai protocolli TLS 1.2. Questa limitazione verrà inizialmente implementata per i clienti che utilizzano già i protocolli TLS 1.2, in modo da non vedere l'impatto. La deprecazione completa dei protocolli TLS 1.0 e TLS 1.1 verrà completata il 31 agosto 2019. I clienti che utilizzano ancora TLS 1.0 e TLS 1.1 riceveranno un avviso avanzato per prepararsi a questa modifica.

Per mantenere la connessione al servizio proxy di applicazione durante questa modifica, è consigliabile assicurarsi che le combinazioni client-server e browser-server siano aggiornate per utilizzare TLS 1.2. È inoltre consigliabile assicurarsi di includere tutti i sistemi client utilizzati dai dipendenti per accedere alle app pubblicate tramite il servizio proxy di applicazione.

Per altre informazioni, vedere [Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory.For](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)more information, see Add an on-premises application for remote access through Application Proxy in Azure Active Directory.

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Pianificare le modifiche: gli aggiornamenti della progettazione sono in arrivo per la raccolta applicazioni

**Tipo:** Modifica pianificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Nuove modifiche all'interfaccia utente vengono apportate alla progettazione dell'area **Aggiungi dalla raccolta** del pannello Aggiungi **un'applicazione.** Queste modifiche ti consentiranno di trovare più facilmente le tue app che supportano il provisioning automatico, OpenID Connect, SAML (Security Assertion Markup Language) e L'accesso Single Sign-On (SSO) con password.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Pianificare la modifica: rimozione dell'indirizzo IP del server MFA dall'indirizzo IP di Office 365

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

L'indirizzo IP del server MFA viene rimosso dall'indirizzo IP e dal [servizio Web URL di Office 365.](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service) Se attualmente ci si basa su queste pagine per aggiornare le impostazioni del firewall, è necessario assicurarsi di includere anche l'elenco di indirizzi IP documentati nella sezione Requisiti del firewall del **server Azure Multi-Factor Authentication** dell'articolo Introduzione al server Azure [Multi-Factor Authentication.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>I token solo per le app ora richiedono l'esistenza dell'app client nel tenant delle risorse

**Tipo:** Correzione  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Il 26 luglio 2019 abbiamo modificato il modo in cui forniamo token solo app tramite la concessione delle [credenziali client.](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) In precedenza, le app potevano ottenere token per chiamare altre app, indipendentemente dal fatto che l'app client fosse nel tenant. È stato aggiornato questo comportamento in modo che le risorse single-tenant, talvolta denominate API Web, possano essere chiamate solo dalle app client presenti nel tenant delle risorse.

Se l'app non si trova nel tenant delle risorse, verrà `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` visualizzato un messaggio di errore con il messaggio di errore, per risolvere il problema, è necessario creare l'entità servizio app client nel tenant, usando l'endpoint di [consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) o [Tramite PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), che garantisce che il tenant abbia concesso all'app l'autorizzazione per operare all'interno del tenant.

Per ulteriori informazioni, vedere [Novità per l'autenticazione?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Il consenso esistente tra il client e l'API continua a non essere richiesto. Le app dovrebbero comunque eseguire i propri controlli di autorizzazione.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nuovo accesso senza password ad Azure AD usando le chiavi di sicurezza FIDO2New passwordless sign-in to Azure AD using FIDO2 security keys

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

I clienti di Azure AD possono ora impostare criteri per gestire le chiavi di sicurezza FIDO2 per gli utenti e i gruppi dell'organizzazione. Gli utenti finali possono anche registrare autonomamente le chiavi di sicurezza, usare le chiavi per accedere ai propri account Microsoft nei siti Web nei dispositivi Web nei dispositivi che supportano FIDO, nonché accedere ai dispositivi Windows 10 aggiunti ad Azure AD.

Per altre informazioni, vedere [Abilitare l'accesso senza password per Azure AD (anteprima)](/azure/active-directory/authentication/concept-authentication-passwordless) per le informazioni relative all'amministratore e Configurare le informazioni di [sicurezza per l'uso](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) di una chiave di sicurezza (anteprima) per le informazioni relative all'utente finale.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Luglio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

A luglio 2019, abbiamo aggiunto queste 18 nuove app con il supporto della federazione alla galleria di app:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), Clever [Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access per Ethidex Compliance Office&trade;](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), J [ArtFrogfactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste nuove app SaaS supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Ora puoi automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste nuove app integrate:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatico degli account utente, vedere Automatizzare il [provisioning degli utenti nelle applicazioni SaaS con Azure ADFor](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) more information about how to better secure your organization by using automated user account provisioning, see Automate user provisioning to SaaS applications with Azure AD

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nuovo tag di servizio servizi di dominio Azure AD per il gruppo di sicurezza di reteNew Azure AD Domain Services service tag for Network Security Group

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Se si è stanchi di gestire lunghi elenchi di indirizzi IP e intervalli, è possibile usare il nuovo tag del servizio di rete **AzureActiveDirectoryDomainServices** nel gruppo di sicurezza di rete di Azure per proteggere il traffico in ingresso nella subnet della rete virtuale di Servizi di dominio Azure AD.

Per altre informazioni su questo nuovo tag di servizio, vedere Gruppi di sicurezza di rete per Servizi di dominio Azure AD.For more information about this new service tag, see [Network Security Groups for Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Servizi di dominio Azure AD (anteprima pubblica)New Security Audits for Azure AD Domain Services (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Siamo lieti di annunciare il rilascio del controllo di sicurezza del servizio di dominio di Azure AD per l'anteprima pubblica. Il controllo della sicurezza consente di fornire informazioni critiche sui servizi di autenticazione trasmettendo gli eventi di controllo di sicurezza alle risorse di destinazione, tra cui Archiviazione di Azure, aree di lavoro di Azure Log Analytics e Hub eventi di Azure, usando il servizio di dominio di Azure AD Portale.

Per altre informazioni, vedere Abilitare i controlli di sicurezza per Servizi di [dominio Azure AD (anteprima)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nuovi metodi di autenticazione utilizzo & informazioni dettagliate (anteprima pubblica)New Authentication methods usage & insights (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Funzionalità del prodotto:** Monitoraggio e creazione report

I nuovi metodi di autenticazione utilizzano & report di informazioni dettagliate consentono di comprendere come funzionalità come Azure Multi-Factor Authentication e la reimpostazione della password self-service nell'organizzazione, incluso il numero di utenti registrati per ogni funzionalità, la frequenza di utilizzo della reimpostazione della password self-service per reimpostare le password e con quale metodo si verifica la reimpostazione.

Per ulteriori informazioni, vedere Metodi di [autenticazione & informazioni dettagliate (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nuovi report sulla sicurezza sono disponibili per tutti gli amministratori di Azure AD (anteprima pubblica)New security reports are available for all Azure AD administrators (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Tutti gli amministratori di Azure AD possono ora selezionare il banner nella parte superiore dei report di sicurezza esistenti, ad esempio il report **Utenti contrassegnati per il rischio,** per iniziare a usare la nuova esperienza di sicurezza, come illustrato nei report **Utenti Rischiosi** e **Accessi rischiosi.** Nel corso del tempo, tutti i report di sicurezza passeranno dalle versioni precedenti alle nuove versioni, con i nuovi report che forniscono le seguenti funzionalità aggiuntive:

- Filtro e ordinamento avanzati

- Azioni collettive, ad esempio la chiusura del rischio per gli utenti

- Conferma di entità compromesse o sicure

- Stato del rischio, che copre: a rischio, dispagato, verificato e confermato compromesso

Per ulteriori informazioni, vedere [Report Utenti rischiosi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) e [Report di accesso rischiosi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Servizi di dominio Azure AD (anteprima pubblica)New Security Audits for Azure AD Domain Services (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Siamo lieti di annunciare il rilascio del controllo di sicurezza del servizio di dominio di Azure AD per l'anteprima pubblica. Il controllo della sicurezza consente di fornire informazioni critiche sui servizi di autenticazione trasmettendo gli eventi di controllo di sicurezza alle risorse di destinazione, tra cui Archiviazione di Azure, aree di lavoro di Azure Log Analytics e Hub eventi di Azure, usando il servizio di dominio di Azure AD Portale.

Per altre informazioni, vedere Abilitare i controlli di sicurezza per Servizi di [dominio Azure AD (anteprima)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nuova federazione diretta B2B con SAML/WS-Fed (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

La federazione diretta consente di semplificare l'utilizzo di partner la cui soluzione di identità gestita dall'IT non è Azure AD, collaborando con sistemi di identità che supportano gli standard SAML o WS-Fed. Dopo aver impostato una relazione di federazione diretta con un partner, qualsiasi nuovo utente guest invitato da tale dominio può collaborare con l'utente utilizzando l'account aziendale esistente, rendendo l'esperienza utente per gli ospiti più semplice.

Per altre informazioni, vedere Federazione diretta con ADFS e provider di [terze parti per gli utenti guest (anteprima).](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste nuove app SaaS supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Ora puoi automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste nuove app integrate:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatico degli account utente, vedere Automatizzare il provisioning degli utenti nelle [applicazioni SaaS con Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nuovo controllo per i nomi di gruppo duplicati nel portale di Azure ADNew check for duplicate group names in the Azure AD portal

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

A questo punto, quando si crea o si aggiorna il nome di un gruppo dal portale di Azure AD, verrà eseguito un controllo per verificare se si sta duplicando un nome di gruppo esistente nella risorsa. Se determiniamo che il nome è già in uso da un altro gruppo, ti verrà chiesto di modificare il tuo nome.

Per altre informazioni, vedere Gestire gruppi nel portale di [Azure AD.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD now supports static query parameters in reply (redirect) URIs

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Le app di Azure AD possono ora registrare e usare GLI `https://contoso.com/oauth2?idp=microsoft`URI di risposta (reindirizzamento) con parametri di query statici (ad esempio, ) per le richieste OAuth 2.0. Il parametro di query statico è soggetto alla corrispondenza della stringa per gli URI di risposta, come qualsiasi altra parte dell'URI di risposta. Se non è presente alcuna stringa registrata che corrisponde all'URI di reindirizzamento decodificato dall'URL, la richiesta viene rifiutata. Se viene trovato l'URI di risposta, viene utilizzata l'intera stringa per reindirizzare l'utente, incluso il parametro di query statico.

Gli URI di risposta dinamica sono ancora proibiti perché rappresentano un rischio per la sicurezza e non possono essere utilizzati per conservare le informazioni sullo stato in una richiesta di autenticazione. A tale scopo, `state` utilizzare il parametro.

Attualmente, le schermate di registrazione dell'app del portale di Azure bloccano ancora i parametri di query. Tuttavia, puoi modificare manualmente il manifesto dell'app per aggiungere e testare i parametri di query nell'app. Per ulteriori informazioni, vedere [Novità per l'autenticazione?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>I log attività (API MS Graph) per Azure AD sono ora disponibili tramite i cmdlet di PowerShell

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare che i log attività di Azure AD (rapporti di controllo e accessi) sono ora disponibili tramite il modulo Azure AD PowerShell.We're we're excited to announce that Azure AD activity logs (Audit and Sign-ins reports) are now available through the Azure AD PowerShell module. In precedenza, era possibile creare script personalizzati utilizzando gli endpoint dell'API MS Graph e ora abbiamo esteso tale funzionalità ai cmdlet di PowerShell.Previously, you could create your own scripts using MS Graph API endpoints, and now we've extended that capability to PowerShell cmdlets.

Per ulteriori informazioni su come utilizzare questi cmdlet, vedere Cmdlet di [Azure AD PowerShell per la creazione di report.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controlli dei filtri aggiornati per i log di controllo e accesso in Azure ADUpdated filter controls for Audit and Sign-in logs in Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Abbiamo aggiornato i rapporti Di registro di controllo e accesso in modo da poter applicare vari filtri senza doverli aggiungere come colonne nelle schermate del report. Inoltre, è ora possibile decidere quanti filtri si desidera visualizzare sullo schermo. Questi aggiornamenti funzionano tutti insieme per semplificare la lettura dei report e definire l'ambito in base alle proprie esigenze.

Per ulteriori informazioni su questi aggiornamenti, vedere [Filtrare i registri](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) di controllo e [Filtrare](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)le attività di accesso .

---

## <a name="june-2019"></a>Giugno 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nuova API riskDetections per Microsoft Graph (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Siamo lieti di annunciare che la nuova API riskDetections per Microsoft Graph è ora in anteprima pubblica. È possibile utilizzare questa nuova API per visualizzare un elenco dei rilevamenti dei rischi relativi a Identity Protection dell'organizzazione. È inoltre possibile utilizzare questa API per eseguire query in modo più efficiente sui rilevamenti dei rischi, inclusi dettagli sul tipo di rilevamento, sullo stato, sul livello e altro ancora.

Per ulteriori informazioni, vedere la documentazione di [riferimento sull'API di rilevamento dei rischi](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Giugno 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

A giugno 2019, abbiamo aggiunto queste 22 nuove app con il supporto della federazione alla galleria di app:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai ,](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial) [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Client VPN](https://portal.azure.com/)di Azure , [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager per Oracle Retail Merchandising, Oracle Access Manager per Oracle E-Business Suite, Oracle IDCS per E-Business Suite, Oracle IDCS per PeopleSoft, Oracle IDCS per JD Edwards

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste nuove app SaaS supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Ora puoi automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste nuove app integrate:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatico degli account utente, vedere Automatizzare il [provisioning degli utenti nelle applicazioni SaaS con Azure ADFor](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) more information about how to better secure your organization by using automated user account provisioning, see Automate user provisioning to SaaS applications with Azure AD

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visualizzare lo stato di avanzamento in tempo reale del servizio di provisioning di Azure ADView the real-time progress of the Azure AD provisioning service

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management

L'esperienza di provisioning di Azure AD è stata aggiornata per includere una nuova barra di stato che mostra la distanza nel processo di provisioning degli utenti. Questa esperienza aggiornata fornisce anche informazioni sul numero di utenti di cui è stato eseguito il provisioning durante il ciclo corrente, nonché sul numero di utenti di cui è stato eseguito il provisioning fino ad oggi.

Per ulteriori informazioni, consultate [Controllare lo stato del provisioning degli utenti.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Il marchio dell'azienda ora viene visualizzato nella disconnessione e nelle schermate di errore

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Abbiamo aggiornato Azure AD in modo che la personalizzazione dell'azienda venga ora visualizzata nelle schermate di disconnessione e di errore, nonché nella pagina di accesso. Non è necessario eseguire alcuna operazione per attivare questa funzionalità, Azure AD usa semplicemente le risorse già configurate nell'area **Personalizzazione società** del portale di Azure.You don't have to do anything to turn on this feature, Azure AD simply uses the assets you've already set up in the Company branding area of the Azure portal.

Per altre informazioni sulla configurazione della personalizzazione dell'azienda, vedere Aggiungere personalizzazione alle pagine di [Azure Active Directory dell'organizzazione.](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Il server Azure Multi-Factor Authentication (MFA) non è più disponibile per le nuove distribuzioniAzure Multi-Factor Authentication (MFA) Server is no longer available for new deployments

**Tipo:** Deprecato  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

A partire dal 1 luglio 2019, Microsoft non offrirà più MFA Server per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori nell'organizzazione devono ora usare Azure Multi-Factor Authentication basato su cloud. I clienti che hanno attivato MFA Server prima del 1 luglio non vedranno una modifica. Potrai comunque scaricare la versione più recente, ottenere aggiornamenti futuri e generare credenziali di attivazione.

Per altre informazioni, vedere Introduzione al server [Azure Multi-Factor Authentication.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy) Per altre informazioni su Azure Multi-Factor Authentication basato su cloud, vedere Pianificazione di una distribuzione di [Azure Multi-Factor Authentication basata su cloud.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

---

## <a name="may-2019"></a>Maggio 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Modifica del servizio: supporto futuro solo per i protocolli TLS 1.2 nel servizio proxy di applicazioneService Change: Future support for only TLS 1.2 protocols on the Application Proxy service

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Per fornire la crittografia best-in-class per i nostri clienti, stiamo limitando l'accesso solo ai protocolli TLS 1.2 sul servizio proxy di applicazione. Questa modifica viene gradualmente implementata per i clienti che utilizzano già solo i protocolli TLS 1.2, pertanto non è consigliabile visualizzare alcuna modifica.

La deprecazione di TLS 1.0 e TLS 1.1 avviene il 31 agosto 2019, ma ti forniremo ulteriori avvisi avanzati, quindi avrai il tempo di prepararti per questa modifica. Per prepararsi a questa modifica, assicurarsi che le combinazioni client-server e browser-server, inclusi i client utilizzati dagli utenti per accedere alle app pubblicate tramite il proxy di applicazione, siano aggiornate per utilizzare il protocollo TLS 1.2 per mantenere la connessione all'applicazione Servizio proxy. Per altre informazioni, vedere [Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory.For](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)more information, see Add an on-premises application for remote access through Application Proxy in Azure Active Directory.

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Usare il report di utilizzo e informazioni dettagliate per visualizzare i dati di accesso correlati all'appUse the usage and insights report to view your app-related sign-in data

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile usare il report di utilizzo e informazioni dettagliate, disponibile nell'area Applicazioni aziendali del portale di Azure, per ottenere una visualizzazione incentrata sull'applicazione dei dati di accesso, incluse informazioni su:You can now use the usage and insights report, located in the **Enterprise applications** area of the Azure portal, to get an application-centric view of your sign-in data, including info about:

- Principali app usate per l'organizzazione

- App con gli accessi più riusciti

- Principali errori di accesso per ogni app

Per altre informazioni su questa funzionalità, vedere [Report sull'utilizzo e sulle informazioni dettagliate nel portale](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) di Azure Active DirectoryFor more information about this feature, see Usage and insights report in the Azure Active Directory portal

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizzare il provisioning degli utenti nelle app cloud usando Azure ADAutomate your user provisioning to cloud apps using Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Seguire queste nuove esercitazioni per usare il servizio di provisioning di Azure AD per automatizzare la creazione, l'eliminazione e l'aggiornamento degli account utente per le app basate su cloud seguenti:Follow these new tutorials to use the Azure AD Provisioning Service to automate the creation, deletion, and updating of user accounts for the following cloud-based apps:

- [Comete](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [Segnale dinamico](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Puoi anche seguire questa nuova esercitazione di [Dropbox,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)che fornisce informazioni su come eseguire il provisioning degli oggetti gruppo.

Per altre informazioni su come proteggere meglio l'organizzazione tramite il provisioning automatico degli account utente, vedere Automatizzare il provisioning degli utenti nelle [applicazioni SaaS con Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Il punteggio di sicurezza dell'identità è ora disponibile in Azure AD (disponibilità generale)Identity secure score is now available in Azure AD (General availability)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

È ora possibile monitorare e migliorare la posizione di sicurezza dell'identità usando la funzionalità di punteggio sicuro dell'identità in Azure AD. La funzionalità di punteggio sicuro dell'identità utilizza un singolo dashboard per aiutarti a:

- Misura oggettivamente la tua posizione di sicurezza dell'identità, in base a un punteggio compreso tra 1 e 223.

- Pianificare i miglioramenti della sicurezza dell'identità

- Rivedere il successo dei miglioramenti della sicurezza

Per altre informazioni sulla funzionalità di sicurezza dell'identità, vedere [Qual è il punteggio di sicurezza dell'identità in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>È ora disponibile una nuova esperienza di registrazione delle app (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** esperienza di sviluppo

La nuova esperienza di [registrazione app](https://aka.ms/appregistrations) è ora in disponibilità generale. Questa nuova esperienza include tutte le funzionalità principali con cui si ha familiarità dal portale di Azure e dal portale di registrazione delle applicazioni e le migliora tramite:This new experience includes all the key features you're familiar with from the Azure portal and the Application Registration portal and improves on them through:

- **Migliore gestione delle app.** Invece di visualizzare le app in portali diversi, ora puoi visualizzare tutte le app in un'unica posizione.

- **Registrazione semplificata dell'app.** Dall'esperienza di navigazione migliorata all'esperienza di selezione delle autorizzazioni rinnovata, ora è più facile registrare e gestire le tue app.

- **Informazioni più dettagliate.** Puoi trovare maggiori dettagli sulla tua app, tra cui guide introduttive e altro ancora.

Per altre informazioni, vedere Piattaforma di [identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) e l'esperienza di registrazione di [app è ora generalmente disponibile!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) annuncio del blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nuove funzionalità disponibili nell'API Risky Users per la protezione delle identità

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Siamo lieti di annunciare che ora è possibile utilizzare l'API Risky Users per recuperare la cronologia dei rischi degli utenti, ignorare gli utenti rischiosi e confermare gli utenti come compromessi. Questa modifica consente di aggiornare in modo più efficiente lo stato dei rischi degli utenti e di comprenderne la cronologia dei rischi.

Per ulteriori informazioni, vedere la documentazione di [riferimento dell'API Risky Users](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - maggio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

A maggio 2019, abbiamo aggiunto queste 21 nuove app con il supporto della federazione alla galleria di app:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), Quantum [Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Esperienze di creazione e gestione dei gruppi migliorate nel portale di Azure ADImproved groups creation and management experiences in the Azure AD portal

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Sono stati apportati miglioramenti alle esperienze correlate ai gruppi nel portale di Azure AD. Questi miglioramenti consentono agli amministratori di gestire meglio gli elenchi di gruppi, gli elenchi dei membri e di fornire ulteriori opzioni di creazione.

I miglioramenti includono:

- Filtro di base per tipo di appartenenza e tipo di gruppo.

- Aggiunta di nuove colonne, ad esempio Origine e Indirizzo di posta elettronica.

- Possibilità di selezionare più gruppi, membri ed elenchi di proprietari per semplificarne l'eliminazione.

- Possibilità di scegliere un indirizzo e-mail e aggiungere proprietari durante la creazione del gruppo.

Per altre informazioni, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurare criteri di denominazione per i gruppi di Office 365 nel portale di Azure AD (disponibilità generale)Configure a naming policy for Office 365 groups in Azure AD portal (General availability)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare criteri di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica consente di applicare convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare i criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi dei gruppi (ad esempio, "CEO, Payroll, HR").

Per ulteriori informazioni, vedere [Enforce a Naming Policy for Office 365 groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Gli endpoint API di Microsoft Graph sono ora disponibili per i log attività di Azure AD (disponibilità generale)Microsoft Graph API endpoints are now available for Azure AD activity logs (General availability)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare la disponibilità generale del supporto degli endpoint API Microsoft Graph per i log attività di Azure AD. Con questa versione, è ora possibile usare la versione 1.0 dei log di controllo di Azure AD e delle API dei log di accesso.

Per altre informazioni, vedere [Panoramica dell'API](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)del log di controllo di Azure AD.For more information, see Azure AD audit log API overview .

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Gli amministratori possono ora utilizzare l'accesso condizionale per il processo di registrazione combinata (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità  

Gli amministratori possono ora creare criteri di accesso condizionale per l'utilizzo da parte della pagina di registrazione combinata. Ciò include l'applicazione di criteri per consentire la registrazione se:This includes applying policies to allow registration if:

- Gli utenti si trovano in una rete attendibile.

- Gli utenti sono un rischio di accesso basso.

- Gli utenti si trovano su un dispositivo gestito.

- Gli utenti accettano le condizioni per l'utilizzo dell'organizzazione (TOU).

Per altre informazioni sull'accesso condizionale e la reimpostazione della password, vedere il post di [blog Accesso condizionale per l'autenticazione](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)a più fattori combinati di Azure AD e la reimpostazione della password. Per ulteriori informazioni sui criteri di accesso condizionale per il processo di registrazione combinato, vedere [Criteri di accesso condizionale per la registrazione combinata.](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration) Per altre informazioni sulla funzionalità delle condizioni per l'uso di Azure AD, vedere [Funzionalità delle condizioni per l'uso di Azure Active Directory.For](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)more information about the Azure AD terms of use feature, see Azure Active Directory terms of use feature .

---

## <a name="april-2019"></a>Aprile 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Il nuovo rilevamento di intelligence delle minacce di Azure AD è ora disponibile come parte di Azure AD Identity Protection

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Il rilevamento di intelligence delle minacce di Azure AD è ora disponibile come parte della funzionalità di protezione delle identità di Azure AD aggiornata. Questa nuova funzionalità consente di indicare attività utente insolite per un utente specifico o un'attività coerente con i modelli di attacco noti basati sulle origini di Intelligence sulle minacce interne ed esterne di Microsoft.This new functionality helps to indicate unusual user activity for a specific user or activity that's consistent with known attack patterns based on Microsoft's internal and external threat intelligence sources.

Per altre informazioni sulla versione aggiornata di Azure AD Identity Protection, vedere i quattro principali miglioramenti apportati a Azure AD Identity Protection nel blog [sull'anteprima pubblica](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) e [Che cos'è Azure Active Directory Identity Protection (aggiornato)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) . Per altre informazioni sul rilevamento di Threat Intelligence di Azure AD, vedere l'articolo Rilevamenti dei rischi di Azure Active Directory Identity Protection.For more information about Azure AD threat intelligence detection, see the [Azure Active Directory Identity Protection risk detections.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>La gestione dei diritti di Azure AD è ora disponibile (anteprima pubblica)Azure AD entitlement management is now available (Public preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Governance dell'identità  
**Capacità del prodotto:** Governance dell'identità

La gestione dei diritti di Azure AD, ora in anteprima pubblica, consente ai clienti di delegare la gestione dei pacchetti di accesso, che definisce il modo in cui dipendenti e partner commerciali possono richiedere l'accesso, chi deve approvare e per quanto tempo hanno accesso. I pacchetti di accesso possono gestire l'appartenenza ai gruppi di Azure AD e Office 365, alle assegnazioni di ruolo nelle applicazioni aziendali e alle assegnazioni di ruolo per i siti di SharePoint Online. Per altre informazioni sulla gestione dei [diritti, vedere Panoramica della gestione dei diritti di Azure AD.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) Per altre informazioni sull'ampiezza delle funzionalità di Governance delle identità di Azure AD, tra cui gestione delle identità con privilegi, verifiche di accesso e condizioni per l'utilizzo, vedere [Che cos'è Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurare criteri di denominazione per i gruppi di Office 365 nel portale di Azure AD (anteprima pubblica)Configure a naming policy for Office 365 groups in Azure AD portal (Public preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare criteri di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica consente di applicare convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare i criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi dei gruppi (ad esempio, "CEO, Payroll, HR").

Per ulteriori informazioni, vedere [Enforce a Naming Policy for Office 365 groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>I log attività di Azure AD sono ora disponibili in Monitoraggio di Azure (disponibilità generale)Azure AD Activitylogs are now available in Azure Monitor (General availability)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Per risolvere i commenti e suggerimenti sulle visualizzazioni con i log attività di Azure AD, è stata introdotta una nuova funzionalità Insights in Log Analytics.To help address your feedbacking about visualizations with the Azure AD Activity logs, we're introducing a new Insights feature in Log Analytics. Questa funzionalità consente di ottenere informazioni dettagliate sulle risorse di Azure AD usando i modelli interattivi, denominati cartelle di lavoro. Queste cartelle di lavoro predefinite possono fornire dettagli per app o utenti e includere:These pre-built Workbooks can provide details for apps or users, and include:

- **Gli invii.** Fornisce dettagli per le app e gli utenti, tra cui il percorso di accesso, il sistema operativo in uso o il client del browser e la versione e il numero di accessi riusciti o non riusciti.

- **Autenticazione legacy e accesso condizionale.** Fornisce informazioni dettagliate per le app e gli utenti che usano l'autenticazione legacy, incluso l'utilizzo di Multi-Factor Authentication attivato dai criteri di accesso condizionale, le app che usano i criteri di accesso condizionale e così via.

- **Analisi degli errori di accesso.** Consente di determinare se si verificano errori di accesso a causa di un'azione dell'utente, problemi relativi ai criteri o dell'infrastruttura.

- **Report personalizzati.** È possibile creare nuove cartelle di lavoro o modificare cartelle di lavoro esistenti per personalizzare la caratteristica Insights per l'organizzazione.

Per altre informazioni, vedere Come usare le cartelle di lavoro di Monitoraggio di Azure per i report di Azure Active Directory.For more information, see [How to use Azure Monitor workbooks for Azure Active Directory reports.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Aprile 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di aprile 2019, abbiamo aggiunto queste 21 nuove applicazioni con il supporto della federazione alla galleria di app:

SAP [Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (SSO basato sui ruoli)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial) [monday.com SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nuova opzione di frequenza delle verifiche di accesso e selezione di più ruoli

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Recensioni di accesso  
**Capacità del prodotto:** Governance dell'identità

I nuovi aggiornamenti nelle verifiche di accesso di Azure AD consentono di:New updates in Azure AD access reviews allow you to:

- Modificare la frequenza delle verifiche di accesso in **semestralmente,** oltre alle opzioni precedentemente esistenti di settimana, mensile, trimestrale e annuale.

- Selezionare più ruoli delle risorse di Azure AD e Azure durante la creazione di una singola verifica di accesso. In questo caso, tutti i ruoli vengono impostati con le stesse impostazioni e tutti i revisori vengono notificati contemporaneamente.

Per altre informazioni su come creare una verifica di accesso, vedere Creare una verifica di accesso di gruppi o applicazioni nelle verifiche di accesso di [Azure AD.](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>I sistemi di avviso di posta elettronica di Azure AD Connect stanno passando, inviando nuove informazioni sul mittente di posta elettronica per alcuni clientiAzure AD Connect email alert system(s) are transitioning, sending new email sender information for some customers

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma

Azure AD Connect è in fase di transizione dei nostri sistemi di avviso di posta elettronica, mostrando potenzialmente ad alcuni clienti un nuovo mittente di posta elettronica. Per risolvere questo problema, è necessario aggiungere `azure-noreply@microsoft.com` all'elenco Consenti dell'organizzazione o non sarà possibile continuare a ricevere avvisi importanti da Office 365, Azure o i servizi di sincronizzazione.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN suffix changes are now successful between Federated domains in Azure AD Connect

**Tipo:** Correzione  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma

È ora possibile modificare correttamente il suffisso UPN di un utente da un dominio federato a un altro dominio federato in Azure AD Connect.You can now successfully change a user's UPN suffix from one Federated domain to another Federated domain in Azure AD Connect. Questa correzione indica che non si dovrebbe più verificarsi il messaggio di errore FederatedDomainChangeError durante il ciclo di sincronizzazione o ricevere un messaggio di posta elettronica di notifica che indica, "Impossibile aggiornare l'oggetto in Azure Active Directory, perché l'attributo [ FederatedUser.UserPrincipalName], non è valido. Aggiornare il valore nei servizi directory locali".

Per ulteriori informazioni, vedere [Risoluzione degli errori durante la sincronizzazione](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maggiore sicurezza usando i criteri di accesso condizionale basati sulla protezione delle app in Azure AD (anteprima pubblica)Increased security using the app protection-based Conditional Access policy in Azure AD (Public preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

L'accesso condizionale basato sulla protezione delle app è ora disponibile tramite i criteri **Richiedi protezione app.** Questo nuovo criterio consente di aumentare la sicurezza dell'organizzazione,

- Utenti che ottengono l'accesso alle app senza una licenza di Microsoft Intune.

- Gli utenti non sono in grado di ottenere un criterio di protezione delle app di Microsoft Intune.Users are unable to get a Microsoft Intune app protection policy.

- Utenti che ottengono l'accesso alle app senza criteri di protezione delle app di Microsoft Intune configurati.

Per altre informazioni, vedere Come richiedere criteri di protezione delle [app per l'accesso alle app cloud con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nuovo supporto per l'accesso Single Sign-On di Azure AD e l'accesso condizionale in Microsoft Edge (anteprima pubblica)New support for Azure AD single sign-on and Conditional Access in Microsoft Edge (Public preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Abbiamo migliorato il supporto di Azure AD per Microsoft Edge, incluso il fornire nuovo supporto per l'accesso Single Sign-On di Azure AD e l'accesso condizionale. Se in precedenza è stato usato Microsoft Intune Managed Browser, è ora possibile usare Microsoft Edge.If you've previously used Microsoft Intune Managed Browser, you can now use Microsoft Edge instead.

Per altre informazioni sulla configurazione e la gestione di dispositivi e app tramite l'accesso condizionale, vedere [Richiedere i dispositivi gestiti per l'accesso alle app cloud con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e Richiedere app client [approvate per l'accesso](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)alle app cloud con accesso condizionale . Per altre informazioni su come gestire l'accesso usando Microsoft Edge con i criteri di Microsoft Intune, vedere [Gestire l'accesso a Internet tramite un browser protetto da criteri](https://docs.microsoft.com/intune/app-configuration-managed-browser)di Microsoft Intune.For more information about how to manage access using Microsoft Edge with Microsoft Intune policies, see Manage Internet access using a Microsoft Intune protected browser.

---

## <a name="march-2019"></a>Marzo 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identity Experience Framework e il supporto dei criteri personalizzati in Azure Active Directory B2C sono ora disponibili (GA)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile creare criteri personalizzati in Azure AD B2C, incluse le attività seguenti, supportate su larga scala e con il nostro servizio di archiviazione di Azure:You can now create custom policies in Azure AD B2C, including the following tasks, which are supported at-scale and under our Azure SLA:

- Creare e caricare percorsi utente di autenticazione personalizzati utilizzando criteri personalizzati.

- Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.

- Definire la diramazione condizionale nei percorsi utente.

- Trasformare e mappare le rivendicazioni per l'utilizzo in tempo reale di decisioni e comunicazioni.

- Usare i servizi abilitati per l'API REST nei percorsi utente dell'autenticazione personalizzata. Ad esempio, con provider di posta elettronica, CRM e sistemi di autorizzazione proprietari.

- Federare con provider di identità conformi al protocollo OpenIDConnect. Ad esempio, con Azure AD multi-tenant, provider di account social o provider di verifica a due fattori.

Per altre informazioni sulla creazione di criteri personalizzati, vedere Note per gli [sviluppatori per i criteri personalizzati in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leggere il post di blog di [Alex Simon, inclusi i case study.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Marzo 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

A marzo 2019, abbiamo aggiunto queste 14 nuove app con il supporto della federazione alla galleria di app:

[Delegato DiScambio Mobile ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), Sistema di [controllo basato sulla spiegazione](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nuovi connettori di provisioning di .scaler e Atlassian nella raccolta di Azure AD - Marzo 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Automatizza la creazione, l'aggiornamento e l'eliminazione di account utente per le app seguenti:

[Scalare](https://aka.ms/ZscalerProvisioning), [Beta ,](https://aka.ms/ZscalerBetaProvisioning) [, scalare Uno](https://aka.ms/ZscalerOneProvisioning), Due , [Scalare Due](https://aka.ms/ZscalerTwoProvisioning), [Tre](https://aka.ms/ZscalerThreeProvisioning), [Scalare , SCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Per altre informazioni su come proteggere meglio l'organizzazione tramite il provisioning automatico degli account utente, vedere Automatizzare il provisioning degli utenti nelle [applicazioni SaaS con Azure AD.](https://aka.ms/ProvisioningDocumentation)

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Ripristinare e gestire i gruppi di Office 365 eliminati nel portale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

È ora possibile visualizzare e gestire i gruppi di Office 365 eliminati dal portale di Azure AD. Questa modifica consente di visualizzare i gruppi disponibili per il ripristino, oltre a consentire l'eliminazione definitiva di tutti i gruppi non necessari per l'organizzazione.

Per ulteriori informazioni, consultate [Ripristinare gruppi scaduti o eliminati.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Single Sign-On è ora disponibile per le app locali con protezione SAML di Azure AD tramite il proxy di applicazione (anteprima pubblica)Single Sign-On is now available for Azure AD SAML-secured on-premises apps through Application Proxy (public preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

È ora possibile fornire un'esperienza Single Sign-On (SSO) per le app locali con autenticazione SAML, insieme all'accesso remoto a queste app tramite il proxy di applicazione. Per ulteriori informazioni su come configurare SAML SSO con le app locali, vedere [Single Sign-On SAML per le applicazioni locali con proxy di applicazione (anteprima)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Le app client nei cicli di richiesta verranno interrotte per migliorare l'affidabilità e l'esperienza utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Le app client possono inviare in modo non corretto centinaia delle stesse richieste di accesso in un breve periodo di tempo. Queste richieste, che abbiano esito positivo o meno, contribuiscono a una scarsa esperienza utente e a carichi di lavoro più accresciuti per l'IDP, aumentando la latenza per tutti gli utenti e riducendo la disponibilità dell'IDP.

Questo aggiornamento `invalid_grant` invia `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` un errore: alle applicazioni client che emettono richieste duplicate più volte in un breve periodo di tempo, oltre l'ambito del normale funzionamento. Le app client che riscontrano questo problema dovrebbero mostrare un prompt interattivo che richiede all'utente di accedere di nuovo. Per ulteriori informazioni su questa modifica e su come correggere l'app se si verifica questo errore, vedere [Novità per l'autenticazione?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nuova esperienza utente dei registri di controllo ora disponibile

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È stata creata una nuova pagina Dei log di **controllo** di Azure AD per migliorare sia la leggibilità che la modalità di ricerca delle informazioni. Per visualizzare la nuova pagina **Log di controllo,** selezionare Log di **controllo** nella sezione **Attività** di Azure AD.

![Nuova pagina Log di controllo, con informazioni di esempio](media/whats-new/audit-logs-page.png)

Per altre informazioni sulla nuova pagina Log di **controllo,** vedere Report attività di controllo nel portale di Azure Active Directory.For more information about the new Audit logs page, see [Audit activity reports in the Azure Active Directory portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nuovi avvisi e indicazioni per impedire il blocco accidentale dell'amministratore da criteri di accesso condizionale non configurati correttamente

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per impedire agli amministratori di bloccarsi accidentalmente dai propri tenant tramite criteri di accesso condizionale non configurati correttamente, sono stati creati nuovi avvisi e indicazioni aggiornate nel portale di Azure.To help prevent administrators from accidentally locking themselves out of their own tenants through misconfigured Conditional Access policies, we've created new warnings and updated guidance in the Azure portal. Per altre informazioni sulle nuove indicazioni, vedere [Che cosa sono le dipendenze del servizio in Accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)di Azure Active Directory .

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Condizioni per l'utilizzo migliorate per l'utente finale sui dispositivi mobili

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Governance

Abbiamo aggiornato le nostre esperienze esistenti sulle condizioni d'uso per contribuire a migliorare il modo in cui l'utente esamina e acconsente alle condizioni d'uso su un dispositivo mobile. È ora possibile eseguire lo zoom avanti e indietro, tornare indietro, scaricare le informazioni e selezionare i collegamenti ipertestuali. Per altre informazioni sulle condizioni per l'uso aggiornate, vedere [Funzionalità delle condizioni per l'uso di Azure Active Directory.For](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)more information about the updated terms of use, see Azure Active Directory terms of use feature .

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nuovi log attività di Azure AD disponibili

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile scaricare grandi quantità di log attività direttamente dal portale di Azure.You can now download large amounts of activity logs directly from the Azure portal. Questo aggiornamento consente di:

- Scarica fino a 250.000 righe.

- Ricevi una notifica al termine del download.

- Personalizzare il nome del file.

- Determinare il formato di output, JSON o CSV.

Per altre informazioni su questa funzionalità, vedere Guida introduttiva: Scaricare un report di controllo tramite il portale di AzureFor more information about this feature, see [Quickstart: Download an audit report using the Azure portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Modifica di rilievo: aggiornamenti alla valutazione delle condizioni da parte di Exchange ActiveSync (EAS)

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Controllo di accesso

È in corso l'aggiornamento del modo in cui Exchange ActiveSync (EAS) valuta le seguenti condizioni:

- Posizione dell'utente, in base al paese, all'area geografica o all'indirizzo IP

- Rischio di accesso

- Piattaforma del dispositivo.

Se in precedenza sono state usate queste condizioni nei criteri di accesso condizionale, tenere presente che il comportamento della condizione potrebbe cambiare. Ad esempio, se in precedenza è stata utilizzata la condizione di posizione utente in un criterio, è possibile che il criterio venga ignorato in base alla posizione dell'utente.

---

## <a name="february-2019"></a>Febbraio 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Crittografia del token SAML di Azure AD configurabile (anteprima pubblica)Configurable Azure AD SAML token encryption (Public preview) 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

È ora possibile configurare qualsiasi app SAML supportata per ricevere token SAML crittografati. Se configurato e usato con un'app, Azure AD crittografa le asserzioni SAML generate usando una chiave pubblica ottenuta da un certificato archiviato in Azure AD.

Per altre informazioni sulla configurazione della crittografia del token SAML, vedere Configurare la crittografia del token SAML di [Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Creare una verifica di accesso per gruppi o app usando le revisioni di accesso di Azure ADCreate an access review for groups or apps using Azure AD Access Reviews

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Recensioni di accesso  
**Funzionalità del prodotto:** Governance

È ora possibile includere più gruppi o app in una singola verifica di accesso di Azure AD per l'appartenenza al gruppo o l'assegnazione di app. Le verifiche di accesso con più gruppi o app vengono configurate utilizzando le stesse impostazioni e tutti i revisori inclusi ricevono notifiche contemporaneamente.

Per altre informazioni su come creare una revisione di accesso usando le verifiche di accesso di Azure AD, vedere Creare una [revisione di accesso di gruppi o applicazioni nelle revisioni](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) di accesso di Azure ADFor more information about how create an access review using Azure AD Access Reviews, see Create an access review of groups or applications in Azure AD Access Reviews

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Febbraio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
A febbraio 2019, abbiamo aggiunto queste 27 nuove app con il supporto della federazione alla galleria di app:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Permission Click, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [Seismic](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Condividi un sogno](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Conoscenza Ovunque LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope Data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), Portale [Netop](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud di Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Productivity Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registrazione combinata avanzata di MFA/SSPR

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Funzionalità del prodotto:** Autenticazione utente

In risposta al feedback dei clienti, abbiamo migliorato l'esperienza combinata di anteprima della registrazione MFA/SSPR, aiutando gli utenti a registrare più rapidamente le informazioni di sicurezza sia per l'autenticazione a più fattori che per SSPR. 

**Per attivare l'esperienza avanzata per i tuoi utenti oggi, segui questi passaggi:**

1. In qualità di amministratore globale o amministratore utente, accedere al portale di Azure e passare ad **Azure Active Directory > Impostazioni utente > Gestire le impostazioni per le funzionalità**di anteprima del pannello di accesso. 

2. Nell'opzione Utenti che possono utilizzare le funzionalità di anteprima per la registrazione e la gestione delle informazioni di **sicurezza - aggiornamento,** scegliere di attivare le funzionalità per un **gruppo selezionato di utenti** o per Tutti gli **utenti**.

Nelle prossime settimane, rimuoveremo la possibilità di attivare la vecchia esperienza combinata di anteprima di registrazione MFA/SSPR per i tenant che non lo hanno già attivato.

**Per verificare se il controllo verrà rimosso per il tenant, attenersi alla seguente procedura:**

1. In qualità di amministratore globale o amministratore utente, accedere al portale di Azure e passare ad **Azure Active Directory > Impostazioni utente > Gestire le impostazioni per le funzionalità**di anteprima del pannello di accesso.  

2. Se l'opzione **Utenti che possono utilizzare le funzionalità** di anteprima per la registrazione e la gestione delle informazioni di sicurezza è impostata su **Nessuno**, l'opzione verrà rimossa dal tenant.

Indipendentemente dal fatto che in precedenza è stata attivata la vecchia esperienza combinata di anteprima di registrazione MFA/SSPR per gli utenti o meno, l'esperienza precedente verrà disattivata in una data futura. Per questo, ti consigliamo vivamente di passare alla nuova esperienza migliorata il prima possibile.

Per altre informazioni sull'esperienza di registrazione avanzata, vedere miglioramenti dell'autenticazione a più fattori combinata di [Azure AD e dell'esperienza](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)di registrazione della reimpostazione della password.

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Esperienza di gestione dei criteri aggiornata per i flussi utente

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

Abbiamo aggiornato il processo di creazione e gestione dei criteri per i flussi utente (precedentemente noti come, criteri incorporati) più semplice. Questa nuova esperienza è ora l'impostazione predefinita per tutti i tenant di Azure AD.

È possibile fornire ulteriori commenti e suggerimenti utilizzando le icone smile o cipiglio nell'area **Invia commenti** e suggerimenti nella parte superiore della schermata del portale.

Per altre informazioni sulla nuova esperienza di gestione dei criteri, vedere il b2C di Azure AD ora include la [personalizzazione di JavaScript e](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) il blog di molte altre nuove funzionalità.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Scegliere versioni specifiche degli elementi di pagina fornite da Azure AD B2CChoose specific page element versions provided by Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile scegliere una versione specifica degli elementi di pagina forniti da Azure AD B2C. Selezionando una versione specifica, è possibile testare gli aggiornamenti prima che vengano visualizzati in una pagina ed è possibile ottenere un comportamento prevedibile. Inoltre, è ora possibile scegliere di applicare versioni di pagina specifiche per consentire le personalizzazioni JavaScript. Per attivare questa funzionalità, passare alla pagina **Proprietà** nei flussi utente.

Per altre informazioni sulla scelta di versioni specifiche degli elementi di pagina, vedere il [blog Azure AD B2C include la personalizzazione JavaScript e il](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog di molte altre nuove funzionalità.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisiti configurabili per la password dell'utente finale per B2C (GA)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile configurare la complessità delle password dell'organizzazione per gli utenti finali, anziché dover usare i criteri password nativi di Azure AD. Dal pannello **Proprietà** dei flussi utente (precedentemente noti come criteri predefiniti), è possibile scegliere una complessità della password **Semplice** o **Forte**oppure creare un set di requisiti **personalizzato.**

Per altre informazioni sulla configurazione dei requisiti di complessità delle password, vedere Configurare i requisiti di [complessità per le password in Azure Active Directory B2C.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nuovi modelli predefiniti per esperienze di autenticazione personalizzate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

Puoi usare i nostri nuovi modelli predefiniti, disponibili nel pannello Layout di pagina dei **flussi** utente (precedentemente noti come criteri predefiniti), per creare un'esperienza di autenticazione personalizzata per gli utenti.

Per altre informazioni sull'uso dei modelli, vedere [Azure AD B2C ora include la personalizzazione Di Script e molte altre nuove funzionalità.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

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
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Sono state introdotte tre nuove impostazioni dei cookie, disponibili per le app pubblicate tramite il proxy di applicazione:

- **Utilizzare cookie solo HTTP.** Consente di impostare il flag **HTTPOnly** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, tra cui la possibilità di evitare la copia o la modifica dei cookie tramite script lato client. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Utilizzare cookie sicuro.** Consente di impostare il flag **Secure** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, assicurando che i cookie vengano trasmessi solo su canali TLS sicuri, ad esempio HTTPS. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Utilizzare cookie persistente.** Consente di impedire la scadenza dei cookie di accesso alla chiusura del Web browser. Questi cookie rimangono validi per l'intera durata del token di accesso. I cookie vengono tuttavia reimpostati se viene raggiunta la scadenza o se l'utente elimina manualmente il cookie. È consigliabile mantenere l'impostazione predefinita **No**, attivando l'impostazione solo per app meno recenti che non condividono cookie tra i processi.

Per altre informazioni sui nuovi cookie, vedere [Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Gennaio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di gennaio 2019, alla raccolta di app sono state aggiunte queste 35 nuove app con il supporto della federazione:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [Expiration Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nuovi miglioramenti ad Azure AD Identity Protection (anteprima pubblica)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Protezione dell'identità  
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
**Categoria di servizio:** Microsoft Authenticator App  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per rendere più sicuri i passcode monouso, le informazioni sull'app e le impostazioni dell'app, è possibile attivare la funzionalità di blocco dell'app nell'app Microsoft Authenticator. L'attivazione di Blocco app indica che ti verrà chiesto di eseguire l'autenticazione utilizzando il PIN o il dati biometrico ogni volta che apri l'app Microsoft Authenticator.

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
**Categoria di servizio:** Gestione utenti  
**Capacità del prodotto:** Directory

>[!Important]
>Microsoft ha ascoltato e compreso i commenti di insoddisfazione dei clienti in merito a questa correzione. Ha quindi ripristinato lo stato antecedente alla modifica finché non sarà più facile per i clienti implementare questa correzione nella loro organizzazione.

È stato corretto un bug in cui il flag DirSyncEnabled di un utente sarebbe erroneamente passato a **False** quando l'oggetto servizi di dominio Active Directory (AD DS) è stato escluso dall'ambito di sincronizzazione e quindi spostato nel Cestino in Azure AD il ciclo di sincronizzazione seguente. In seguito a questa correzione, se l'utente viene escluso dall'ambito di sincronizzazione e successivamente ripristinato dal Cestino di Azure AD, l'account utente resta sincronizzato da AD locale come previsto e non può essere gestito nel cloud poiché la relativa origine di autorità rimane AD locale.

Prima di questa correzione si verificava un problema quando il flag DirSyncEnabled veniva impostato su False. Veniva data l'erronea l'impressione che questi account fossero stati convertiti in oggetti solo cloud e potessero essere gestiti solo nel cloud. Tuttavia, gli account mantenevano l'origine di autorità come locale e tutte le proprietà sincronizzate (attributi shadow) provenienti da AD locale. Questa condizione causava vari problemi in Azure AD e in altri carichi di lavoro cloud, ad esempio Exchange Online, che prevedevano di gestire questi account come sincronizzati da AD, ma che si comportavano ora come account solo cloud.

Attualmente, l'unico modo per convertire realmente un account sincronizzato da AD in un account solo cloud è disabilitare DirSync a livello di tenant, attivando così un'operazione di back-end per trasferire l'origine di autorità. Questo tipo di modifica dell'origine di autorità richiede, tra l'altro, la pulizia di tutti gli attributi locali correlati (ad esempio LastDirSyncTime e gli attributi shadow) e l'invio di un segnale ad altri carichi di lavoro cloud perché convertano anch'essi il rispettivo oggetto in un account solo cloud.

Questa correzione, di conseguenza, consente di evitare gli aggiornamenti diretti dell'attributo ImmutableID di un utente sincronizzato da AD, che in precedenza erano necessari in alcuni scenari. Per impostazione predefinita, l'attributo ImmutableID di un oggetto in Azure AD, come suggerisce il nome, deve essere immutabile. Per questi scenari sono disponibili nuove funzionalità implementate in Azure AD Connect Health e nel client di sincronizzazione di Azure AD Connect:

- **Aggiornamento di ImmutableID su larga scala per molti utenti in un approccio a fasi**
  
  Ad esempio, è necessario eseguire una lunga migrazione tra foreste di Active Directory Domain Services. Soluzione: usare Azure AD Connect per **configurare l'ancoraggio** di origine e, durante la migrazione dell'utente, copiare i valori ImmutableID esistenti da Azure AD nell'attributo ms-DS-Consistency-Guid dell'utente di Servizi di dominio Active Directory locale della nuova foresta. Per altre informazioni, vedere [Uso di ms-DS-ConsistencyGuid come sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Aggiornamenti di ImmutableID su larga scala per molti utenti in un unico passaggio**

  Ad esempio, se durante l'implementazione di Azure AD Connect si commette un errore ed è necessario modificare l'attributo SourceAnchor. Soluzione: disabilitare DirSync a livello di tenant e cancellare tutti i valori ImmutableID non validi. Per altre informazioni, vedere [Disabilitare la sincronizzazione della directory per Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Abbinare di nuovo un utente locale e un utente esistente in Azure AD** Ad esempio, un utente che è stato ricreato in Active Directory Domain Services genera un duplicato nell'account Azure AD anziché essere abbinato di nuovo a un account Azure AD esistente (oggetto orfano). Soluzione: usare Azure AD Connect Health nel portale di Azure per rimappare l'ancoraggio di origine/ImmutableID.Solution: Use Azure AD Connect Health in the Azure portal to remap the Source Anchor/ImmutableID. Per altre informazioni, vedere [Scenario con oggetto orfano](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Interruzione della modifica: aggiornamenti allo schema dei log di controllo e di accesso tramite Monitoraggio di AzureBreaking Change: Updates to the audit and sign-in logs schema through Azure Monitor

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Sono attualmente in fase di pubblicazione flussi di log di controllo e accesso tramite Monitoraggio di Azure, per poter integrare uniformemente i file di log con gli strumenti di informazioni di sicurezza e gestione degli eventi o con Log Analytics. In base ai commenti e suggerimenti degli utenti e in preparazione all'annuncio della disponibilità generale di questa funzionalità, Microsoft sta apportando le modifiche seguenti allo schema. Queste modifiche dello schema e i relativi aggiornamenti alla documentazione verranno completati entro la prima settimana di gennaio.

#### <a name="new-fields-in-the-audit-schema"></a>Nuovi campi nello schema di controllo
Sta per essere aggiunto il nuovo campo **Tipo di operazione**, che permetterà di specificare il tipo di operazione eseguita sulla risorsa. Ad esempio, **aggiunta**, **aggiornamento** o **eliminazione**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campi modificati nello schema di controllo
È in corso la modifica dei campi seguenti nello schema di controllo:

|Nome campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|Category|Campo **Nome servizio** nelle versioni precedenti. Si tratta ora del campo **Audit Categories** (Categorie di controllo). Il campo **Nome del servizio** è stato rinominato in **loggedByService**.|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione password self-service</li></ul>|<ul><li>User Management</li><li>Gestione di gruppi</li><li>Gestione app</li></ul>|
|targetResources|Include **TargetResourceType** al livello superiore.|&nbsp;|<ul><li>Policy</li><li>App</li><li>Utente</li><li>Gruppo</li></ul>|
|loggedByService|Specifica il nome del servizio che ha generato il log di controllo.|Null|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione della password self-service</li></ul>|
|Risultato|Indica il risultato dei log di controllo. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li></ul>|<ul><li>Operazione completata</li><li>Operazioni non riuscite</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campi modificati nello schema di accesso
È in corso la modifica dei campi seguenti nello schema di accesso:

|Nome campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Campo **conditionalaccessPolicies** nelle versioni precedenti. Si tratta ora del campo **appliedConditionalAccessPolicies**.|Nessuna modifica|Nessuna modifica|
|conditionalAccessStatus|Indica il risultato dello stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Operazione completata</li><li>Operazioni non riuscite</li><li>Non applicato</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: risultato|Indica il risultato del singolo stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Operazione completata</li><li>Operazioni non riuscite</li><li>Non applicato</li><li>Disabled</li></ul>|

Per altre informazioni sullo schema, vedere [Interpretare lo schema dei log di controllo di Azure AD in Monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Miglioramenti relativi a Identity Protection apportati al modello di apprendimento automatico supervisionato e al motore dei punteggi di rischio

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Protezione dell'identità  
**Capacità del prodotto:** Punteggi di rischio

I miglioramenti apportati al motore di valutazione dei punteggi di rischio utente e di accesso in relazione a Identity Protection ottimizzano la copertura e la precisione del rischio utente. Gli amministratori potrebbero notare che il livello di rischio utente non è più direttamente collegato al livello di rischio di rilevamenti specifici e che vi è stato un aumento nel numero e nel livello di eventi di accesso rischiosi.

I rilevamenti dei rischi vengono ora valutati dal modello di apprendimento automatico supervisionato, che calcola il rischio dell'utente utilizzando funzionalità aggiuntive degli invii dell'utente e un modello di rilevamenti. Attraverso questo modello l'amministratore può individuare gli utenti con punteggi di rischio elevato, anche se i rilevamenti associati all'utente indicano un rischio basso o medio. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Possibilità per gli amministratori di reimpostare la propria password tramite l'app Microsoft Authenticator (anteprima pubblica)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Funzionalità del prodotto:** Autenticazione utente

Gli amministratori di Azure AD possono ora reimpostare la propria password seguendo le notifiche dell'app Microsoft Authenticator o tramite un codice da qualsiasi app di autenticazione per dispositivi mobili o token hardware. Per reimpostare la propria password, gli amministratori possono ora usare due metodi diversi:

- Notifica dell'app Microsoft Authenticator

- Altro codice di token hardware/app di autenticazione per dispositivi mobili

- Email

- Chiamata telefonica

- SMS

Per altre informazioni sull'uso dell'app Microsoft Authenticator per reimpostare le password, vedere [Reimpostazione della password self-service di Azure AD - App per dispositivi mobili e SSPR (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nuovo ruolo Amministratore dispositivo cloud di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Capacità del prodotto:** Controllo degli accessi

Gli amministratori possono assegnare agli utenti il nuovo ruolo Amministratore dispositivo cloud per l'esecuzione di attività di amministrazione dei dispositivi cloud. Gli utenti assegnati al ruolo Amministratore dispositivo cloud possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure.

Per altre informazioni sui ruoli e sulle autorizzazioni, vedere [Assegnazione di ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gestione dei dispositivi tramite il nuovo timestamp di attività in Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Capacità del prodotto:** Gestione del ciclo di vita dei dispositivi

Ci rendiamo conto che nel tempo è necessario aggiornare e ritirare i dispositivi dell'organizzazione in Azure AD, per evitare di avere dispositivi non aggiornati nell'ambiente. Per semplificare questo processo, Azure AD aggiorna ora i dispositivi con un nuovo timestamp di attività, che permette di gestirne il ciclo di vita.

Per altre informazioni su come ottenere e usare questo timestamp, vedere [Procedura: Gestire i dispositivi non aggiornati in Azure ADFor](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices) more information about how to get and use this timestamp, see How To: Manage the stale devices in Azure AD

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Gli amministratori possono richiedere agli utenti di accettare le condizioni per l'utilizzo su ogni dispositivo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Governance
 
Gli amministratori possono ora attivare l'opzione **Richiedi agli utenti di acconsentire a ogni dispositivo** per richiedere agli utenti di accettare le condizioni per l'utilizzo in ogni dispositivo che usano nel tenant.

Per altre informazioni, vedere la [sezione Condizioni per l'utilizzo per dispositivo della funzionalità Condizioni per l'uso di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Gli amministratori possono configurare una condizioni per l'utilizzo per la scadenza in base a una pianificazione ricorrente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Governance
 

Gli amministratori possono ora attivare l'opzione di scadenza **dei consensi** per rendere scadono le condizioni per l'utilizzo di tutti gli utenti in base alla pianificazione ricorrente specificata. La pianificazione può essere annuale, biennale, trimestrale o mensile. Dopo la scadenza delle condizioni per l'utilizzo, gli utenti devono essere nuovamente accettati.

Per altre informazioni, vedere la [sezione Aggiungere condizioni per l'uso della funzionalità Condizioni per l'uso di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Gli amministratori possono configurare una condizioni per l'utilizzo per la scadenza in base alla pianificazione di ogni utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Governance

Gli amministratori possono ora specificare una durata che l'utente deve accettare nuovamente una condizioni per l'utilizzo. Ad esempio, gli amministratori possono specificare che gli utenti devono accettare nuovamente una condizioni per l'utilizzo ogni 90 giorni.

Per altre informazioni, vedere la [sezione Aggiungere condizioni per l'uso della funzionalità Condizioni per l'uso di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
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
**Categoria di servizio:** Altro  
**Capacità del prodotto:** Directory

Le licenze basate sui gruppi sono ora in anteprima pubblica e disponibili a livello generale. Nell'ambito della disponibilità generale questa funzionalità è stata resa più scalabile e sono state aggiunte la possibilità di rielaborare assegnazioni di licenze basate sui gruppi per un singolo utente e quella di usare licenze basate sui gruppi con licenze di Office 365 E3/A3.

Per altre informazioni sulle licenze basate sui gruppi, vedere [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Novembre 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di novembre 2018 sono state aggiunte alla raccolta di app queste 26 nuove app con supporto per la federazione:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps - Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps - UX](https://cloud.plex.com/sso), [Plex Apps - IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Ottobre 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Log di Azure AD ora funziona con Azure Log Analytics (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare che è ora possibile inoltrare i log di Azure AD ad Azure Log Analytics. Questa funzionalità molto richiesta consente di fornire un accesso ancora migliore ad analisi per l'azienda, operazioni e sicurezza, nonché un modo per monitorare l'infrastruttura. Per altre informazioni, consultare il blog [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) (Log attività di Azure Active Directory in Azure Log Analytics ora disponibile).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Ottobre 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di ottobre 2018 sono state aggiunte 14 nuove app con il supporto della federazione alla raccolta di app:

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notifiche tramite posta elettronica di Azure AD Domain Services

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Azure AD Domain Services offre la funzionalità di avvisi relativi ad errori o problemi di configurazione sul portale di Azure. Questi avvisi includono le procedure dettagliate per tentare di risolvere i problemi senza dover contattare l'assistenza.

A partire da ottobre, sarà possibile personalizzare le impostazioni di notifica per il dominio gestito, in modo che quando vengono generati nuovi avvisi, viene inviato un messaggio di posta elettronica a un gruppo di utenti specificato, eliminando la necessità di controllare continuamente il portale per verificare la presenza di aggiornamenti.

Per altre informazioni, vedere [Impostazioni di notifica in Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Il portale di Azure AD supporta l'uso dell'API di dominio ForceDelete per eliminare i domini personalizzati 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione directory  
**Capacità del prodotto:** Directory

Siamo lieti di annunciare che è ora possibile usare l'API di dominio ForceDelete per eliminare i nomi di dominio personalizzati tramite la ridenominazione asincrona dei riferimenti, ad esempio utenti, gruppi e app dal nome di dominio personalizzato (contoso.com) al nome di dominio predefinito iniziale (contoso.onmicrosoft.com).

Questa modifica consente di eliminare in modo più rapido i nomi di dominio personalizzati se l'organizzazione non usa più il nome o se deve usare il nome di dominio in un'altra directory di Azure AD.

Per altre informazioni, vedere [Delete a custom domain name](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name) (Eliminare un nome di dominio personalizzato).

---

## <a name="september-2018"></a>Settembre 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Autorizzazioni del ruolo di amministratore aggiornate per i gruppi dinamici

**Tipo:** Correzione  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

È stato risolto un problema e ora i ruoli di amministratore specifici possono creare e aggiornare le regole di appartenenza dinamica, senza la necessità di essere il proprietario del gruppo.

I ruoli sono:

- Amministratore globale

- Amministratore di Intune

- Amministratore utenti

Per ulteriori informazioni, vedere [Creare un gruppo dinamico e controllare lo stato](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Impostazioni di configurazione di Single Sign-On (SSO) semplificate per alcune app di terze parti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

È risaputo che la configurazione di Single Sign-On (SSO) per le app software come un servizio (SaaS, Software as a Service) può risultare complessa a causa della natura unica della configurazione di ogni app. È stata creata un'esperienza di configurazione semplificata per popolare automaticamente le impostazioni di configurazione di SSO per le app SaaS di terze parti seguenti:

- Zendesk

- ArcGis Online

- Jamf Pro

Per iniziare a usare questa esperienza con un clic, passare alla pagina di**configurazione SSO** del **portale** > di Azure per l'app. Per altre informazioni, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory - Pagina Dove vengono archiviati i tuoi dati?

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** GoLocal

Selezionare l'area della società nella pagina **Azure Active Directory - Dove vengono archiviati i tuoi dati** per visualizzare il data center di Azure che ospita i dati inattivi di Azure AD per tutti i servizi di Azure AD. È possibile filtrare le informazioni in base ai servizi di Azure AD specifici per l'area della società.

Per accedere a questa funzionalità e per altre informazioni, vedere [Azure Active Directory - Dove vengono archiviati i tuoi dati](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nuovo piano di distribuzione disponibile per il pannello di accesso App personali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO

Vedere il nuovo piano di distribuzione disponibile per il pannello di accesso App personali (https://aka.ms/deploymentplans).
Il pannello di accesso App personali fornisce agli utenti un'unica posizione per trovare le app e accedervi. Questo portale offre agli utenti anche l'opportunità di eseguire attività self-service, ad esempio richiedere l'accesso ad app e gruppi o gestire l'accesso a queste risorse per conto di altri.

Per altre informazioni, vedere [Informazioni sul portale delle app personali](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction).

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nuova scheda Risoluzione dei problemi e supporto nella pagina Accessi del portale di Azure

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

La nuova scheda **Risoluzione dei problemi e supporto** nella pagina Accessi del portale di Azure consente agli amministratori e **ai** tecnici del supporto di risolvere i problemi relativi agli accessi di Azure AD. Questa nuova scheda fornisce il codice di errore, il messaggio di errore e i consigli di correzione (se presenti) per risolvere il problema. Se il problema non è risolvibile, è anche possibile creare un ticket di supporto usando l'esperienza **Copia negli Appunti**, che popola i campi **ID richiesta** e **Data (UTC)** per il file di log nel ticket di supporto.  

![Registri di accesso che mostrano la nuova scheda](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Supporto migliorato per le proprietà di estensione personalizzate usate per creare regole di appartenenza dinamica

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Con questo aggiornamento, è ora possibile fare clic sul collegamento **Ottieni proprietà di estensione personalizzate** nel generatore di regole di assegnazione dei gruppi utenti dinamica, immettere l'ID app univoco e ricevere l'elenco completo di proprietà di estensione personalizzate da usare quando si crea una regola di appartenenza dinamica per gli utenti. È anche possibile aggiornare questo elenco per ottenere tutte le nuove proprietà di estensione personalizzate per l'app.

Per altre informazioni sull'uso delle proprietà di estensione personalizzate per le regole di appartenenza dinamica, vedere [Proprietà di estensione e proprietà di estensione personalizzate](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure ADNew approved client apps for Azure AD app-based Conditional Access

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Capacità del prodotto:** Sicurezza e protezione delle identità

Le app seguenti sono nell'elenco delle [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft To-Do

- Microsoft Stream

Per altre informazioni, vedere:

- [Azure AD app-based Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nuovo supporto per la reimpostazione della password self-service dalla schermata di blocco di Windows 7/8/8.1

**Tipo:** Nuova funzionalità  
**Categoria del servizio:** reimpostazione password self-service  
**Funzionalità del prodotto:** Autenticazione utente

Dopo aver configurato questa nuova funzionalità, gli utenti visualizzeranno un collegamento per reimpostare la password dalla schermata **Blocco** di un dispositivo che esegue Windows 7, Windows 8 o Windows 8.1. Facendo clic su tale collegamento, viene avviato lo stesso flusso di reimpostazione della password del Web browser.

Per altre informazioni, vedere [Come abilitare la reimpostazione della password da Windows 7, 8 e 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Avviso di modifica: i codici di autorizzazione non sono più disponibili per essere usati nuovamente 

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Un'app che provi a usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per questa e altre modifiche relative ai protocolli, vedere [l'elenco completo delle novità nell'ambito dell'autenticazione](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Settembre 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di settembre 2018 sono state aggiunte 16 nuove app con il supporto della federazione alla raccolta di app:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Supporto per i metodi aggiuntivi di trasformazione delle attestazioni

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Sono stati introdotti nuovi metodi di trasformazione delle attestazioni, ToLower() e ToUpper(), che possono essere applicati ai token SAML dalla pagina **Configurazione Single Sign-on** basata su SAML.

Per altre informazioni, vedere [Come personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interfaccia utente di configurazione delle app basate su SAML aggiornata (anteprima)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

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

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Piattaforma

È in corso l'introduzione di intervalli IP più ampi in Azure AD. Ciò indica che se gli intervalli di indirizzi IP di Azure AD per i firewall, i router o i gruppi di sicurezza di rete sono già stati configurati, è necessario aggiornarli. Questo aggiornamento ha lo scopo di evitare altre modifiche alla configurazione degli intervalli IP dei firewall, dei router o dei gruppi di sicurezza di rete quando Azure AD aggiunge nuovi endpoint. 

Il traffico di rete passerà a questi nuovi intervalli nel corso dei prossimi due mesi. Per garantirsi la continuità del servizio, è necessario aggiungere questi valori aggiornati agli indirizzi IP prima del 10 settembre 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

È consigliabile rimuovere gli intervalli di indirizzi IP precedenti solo dopo che tutto il traffico di rete è passato ai nuovi intervalli. Per aggiornamenti sul passaggio e per informazioni su quando rimuovere gli intervalli precedenti, vedere [Office 365 URLs and IP address ranges](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) (URL e intervalli di indirizzi IP di Office 365).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Avviso di modifica: i codici di autorizzazione non sono più disponibili per essere usati nuovamente 

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Un'app che provi a usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per questa e altre modifiche relative ai protocolli, vedere [l'elenco completo delle novità nell'ambito dell'autenticazione](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gestione convergente delle informazioni di sicurezza per la reimpostazione self-service della password e l'autenticazione MFA (Multi-Factor Authentication)

**Tipo:** Nuova funzionalità  
**Categoria del servizio:** reimpostazione password self-service  
**Funzionalità del prodotto:** Autenticazione utente
 
Questa nuova funzionalità consente agli utenti di gestire le informazioni di sicurezza (ad esempio numero di telefono, app per dispositivi mobili e così via) per la reimpostazione password self-service e Multi-Factor Authentication in un'unica posizione e con un'unica esperienza, a differenza della situazione precedente in cui l'operazione veniva eseguita in due posizioni diverse.

Questa esperienza convergente si applica anche agli utenti che usano la reimpostazione password self-service o Multi-Factor Authentication. Se l'organizzazione non prevede la registrazione per Multi-Factor Authentication o la reimpostazione password self-service, gli utenti possono comunque registrare qualsiasi metodo correlato alle informazioni di sicurezza consentito dall'organizzazione nel portale App personali.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Gli amministratori possono attivare la nuova esperienza (se necessario) per tutti gli utenti di un tenant o solo per un gruppo selezionato. Per altre informazioni sull'esperienza convergente, vedere il blog sull'[esperienza convergente](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nuova impostazione HTTP-Only cookies (Cookie solo HTTP) nelle app del proxy di applicazione di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Nelle app del proxy di applicazione è presente una nuova impostazione denominata **HTTP-Only Cookies** (Cookie solo HTTP). Questa impostazione consente di fornire sicurezza aggiuntiva includendo il flag HTTPOnly nell'intestazione della risposta HTTP per entrambi i cookie di sessione e di accesso al proxy di applicazione, interrompendo l'accesso al cookie da uno script lato client ed evitando ulteriormente azioni come la copia o la modifica del cookie. Anche se questo flag non è stato utilizzato in precedenza, i cookie sono sempre stati crittografati e trasmessi utilizzando una connessione TLS per proteggere da modifiche improprie.

Questa impostazione non è compatibile con le app che usano i controlli ActiveX, ad esempio Desktop remoto. In questo caso è consigliabile disattivare l'impostazione.

Per altre informazioni sull'impostazione HTTP-Only Cookies (Cookie solo HTTP), vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) per le risorse di Azure supporta i tipi di risorsa dei gruppi di gestione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Le impostazioni JIT e di assegnazione possono ora essere applicate ai tipi di risorsa dei gruppi di gestione, in modo analogo a come accade per sottoscrizioni, gruppi di risorse e risorse (ad esempio macchine virtuali, Servizi app e altro ancora). Tutti gli utenti con un ruolo che consente a un amministratore di accedere a un gruppo di gestione possono individuare e gestire tale risorsa in PIM.

Per altre informazioni su PIM e sulle risorse di Azure, vedere [Individuare e gestire le risorse di Azure usando Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>L'accesso alle applicazioni (in anteprima) consente di accedere più rapidamente al portale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
L'attivazione di un ruolo tramite PIM può richiedere ora oltre 10 minuti prima che le autorizzazioni diventino effettive. Se si sceglie di usare l'accesso alle applicazioni, ora in versione di anteprima pubblica, gli amministratori possono accedere al portale di Azure AD non appena viene completata la richiesta di attivazione.

L'accesso alle applicazioni supporta attualmente solo l'esperienza del portale di Azure AD e le risorse di Azure. Per altre informazioni su PIM e l'accesso alle applicazioni, vedere [Che cos'è Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Agosto 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di agosto 2018 sono state aggiunte 16 nuove app con il supporto della federazione alla raccolta di app:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Il supporto nativo di Tableau è ora disponibile nel proxy di applicazione di Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Con l'aggiornamento dal protocollo OpenID Connect al protocollo OAuth 2.0 Code Grant per il nostro protocollo di autenticazione, non è più necessario eseguire alcuna configurazione aggiuntiva per usare Tableau con il proxy di applicazione. Questa modifica al protocollo consente anche al proxy di applicazione di migliorare il supporto di app più moderne usando solo reindirizzamenti HTTP, supportati in genere in tag JavaScript e HTML.

Per altre informazioni sul supporto nativo per Tableau, vedere [Azure AD Application Proxy now with native Tableau support](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support) (Proxy di applicazione di Azure AD con il supporto nativo di Tableau).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nuovo supporto per aggiungere Google come provider di identità per utenti guest B2B in Azure Active Directory (anteprima)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

Se si configura la federazione con Google nell'organizzazione, è possibile consentire agli utenti Gmail invitati di accedere alle app e alle risorse condivise tramite i propri account Google esistenti senza la necessità di creare un account Microsoft personale o un account Azure AD.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Per altre informazioni sulla federazione di Google, vedere [Add Google as an identity provider for B2B guest users](https://docs.microsoft.com/azure/active-directory/b2b/google-federation) (Aggiungere Google come provider di identità per utenti guest B2B).

---

## <a name="july-2018"></a>Luglio 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Miglioramenti alle notifiche di posta elettronica di Azure Active Directory

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
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

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

I log attività di Azure AD ora sono disponibili in anteprima pubblica per Monitoraggio di Azure (il servizio monitoraggio di Azure a livello di piattaforma). Monitoraggio di Azure offre funzionalità di conservazione a lungo termine e un'integrazione senza problemi, oltre ai miglioramenti seguenti:

- Conservazione a lungo termine tramite il routing dei file di log all'account di Archiviazione di Azure.

- Completa integrazione con i sistemi SIEM, senza che sia necessario scrivere o gestire script personalizzati.

- Completa integrazione con soluzioni personalizzate, strumenti di analisi o soluzioni di gestione degli eventi imprevisti.

Per altre informazioni sulle nuove funzionalità, vedere il blog relativo alla [disponibilità in anteprima pubblica dei log attività di Azure AD nella diagnostica di Monitoraggio di Azure](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e la documentazione [Log attività di Azure Active Directory in Monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informazioni sull'accesso condizionale aggiunte al report di accesso ad Azure ADConditional Access information added to the Azure AD sign-ins report

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Questo aggiornamento consente di visualizzare i criteri che vengono valutati quando un utente esegue l'accesso, insieme al risultato dei criteri. Inoltre, il report ora include il tipo di app client usato dall'utente, quindi è possibile identificare il traffico dei protocolli legacy. È inoltre possibile cercare tra le voci del report un ID di correlazione, che è disponibile nel messaggio di errore per l'utente finale e può essere usato per identificare e risolvere i problemi della richiesta di accesso corrispondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visualizzazione delle autenticazioni legacy tramite i log attività di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Con l'introduzione del campo **App client** nei log attività di accesso, i clienti possono ora vedere gli utenti che usano autenticazioni legacy. I clienti potranno accedere a queste informazioni usando l'API Microsoft Graph per gli accessi o tramite i log attività di accesso nel portale di Azure AD in cui è possibile usare il controllo **App client** per filtrare le autenticazioni legacy. Per altre informazioni, vedere la documentazione.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Luglio 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di luglio 2018 sono state aggiunte queste 16 nuove app con il supporto della federazione alla raccolta di app:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nuove integrazioni di app SaaS per il provisioning utenti - Luglio 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Azure AD permette di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni SaaS, come Dropbox, Salesforce, ServiceNow e altre. A luglio 2018 è stato aggiunto il supporto per il provisioning utenti per le applicazioni seguenti nella raccolta di app Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Per un elenco di tutte le applicazioni che supportano il provisioning utenti nella raccolta di Azure AD, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync: un modo più semplice per risolvere gli errori di sincronizzazione di attributi orfani e duplicati

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** AD Connect  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Azure AD Connect Health introduce funzionalità di correzione in modalità self-service che consentono di evidenziare e correggere gli errori di sincronizzazione. Questa funzionalità consente di risolvere gli errori di sincronizzazione degli attributi duplicati e di correggere gli oggetti resi orfani da Azure AD. Questa diagnostica offre i vantaggi seguenti:

- Consente di limitare gli errori di sincronizzazione degli attributi duplicati, fornendo correzioni specifiche

- Applica una correzione per gli scenari di Azure AD dedicati, consentendo la risoluzione degli errori in un unico passaggio

- Per attivare e usare questa funzionalità, non sono richiesti aggiornamenti o configurazioni

Per altre informazioni, vedere [Diagnosticare e correggere gli errori di sincronizzazione di attributi duplicati](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Aggiornamenti dell'interfaccia utente per le esperienze di accesso ad Azure AD e con account del servizio gestito

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Autenticazione utente

È stata aggiornata l'interfaccia utente per l'esperienza di accesso dei servizi online di Microsoft, ad esempio per Office 365 e Azure. Questa modifica rende le schermate più semplici e lineari. Per altre informazioni su questa modifica, vedere il blog [Upcoming improvements to the Azure AD sign-in experience](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) (Prossimi miglioramenti all'esperienza di accesso di AD Azure).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nuova versione di Azure AD Connect - luglio 2018

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management

La versione più recente di Azure AD Connect include: 

- Correzioni di bug e aggiornamenti relativi al supporto 

- Disponibilità generale dell'integrazione di PingFederate

- Aggiornamenti del client più recente di SQL 2012 

Per altre informazioni su questo aggiornamento, vedere [Azure AD Connect: Cronologia delle versioni](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aggiornamenti alle condizioni per l'utilizzo dell'interfaccia utente finale

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Governance

Stiamo aggiornando la stringa di accettazione nell'interfaccia utente delle Condizioni per l'utilizzo.

**Testo corrente.** Per accedere alle risorse di [NomeTenant], è necessario accettare le condizioni per l'utilizzo.<br>**Nuovo testo.** Per accedere alla risorsa di [NomeTenant], è necessario leggere le condizioni per l'utilizzo.

**Testo corrente:** Scegliendo di accettare, l'utente accetta tutte le condizioni per l'utilizzo indicate.<br>**Nuovo testo:** Fare clic su Accetto per confermare di avere letto e compreso le condizioni per l'utilizzo.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>L'autenticazione pass-through supporta le applicazioni e i protocolli legacy

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
L'autenticazione pass-through ora supporta le app e i protocolli legacy. Le limitazioni seguenti ora sono completamente supportate:

- Accessi utente ad applicazioni client legacy di Office (Office 2010 e Office 2013) senza che sia necessaria l'autenticazione moderna.

- Accesso alla condivisione del calendario e alle informazioni sulla disponibilità negli ambienti ibridi di Exchange solo in Office 2010.

- Accessi utente per applicazioni client Skype for Business senza che sia necessaria l'autenticazione moderna.

- Accessi utente a PowerShell versione 1.0.

- Apple Device Enrollment Program (Apple DEP) con l'Assistente configurazione di iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gestione convergente delle informazioni di sicurezza per la reimpostazione della password self-service e Multi-Factor Authentication

**Tipo:** Nuova funzionalità  
**Categoria del servizio:** reimpostazione password self-service  
**Funzionalità del prodotto:** Autenticazione utente

Questa nuova funzionalità consente agli utenti di gestire le informazioni di sicurezza (ad esempio, numero di telefono, indirizzo di posta elettronica, app per dispositivi mobili e così via) per la reimpostazione della password self-service e Multi-Factor Authentication in una singola esperienza. Gli utenti non dovranno più registrare le stesse informazioni di sicurezza per la reimpostazione della password self-service e Multi-Factor Authentication in due diverse esperienze. Questa nuova esperienza si applica anche agli utenti che dispongono della reimpostazione della password self-service o di Multi-Factor Authentication.

Se un'organizzazione non impone la registrazione per Multi-Factor Authentication o la reimpostazione della password self-service, gli utenti possono registrare le proprie informazioni di sicurezza tramite il portale **App personali**. Da qui, gli utenti possono eseguire la registrazione per qualsiasi metodo abilitato per MFA o SSPR. 

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Gli amministratori possono attivare la nuova esperienza (se necessario) per un gruppo selezionato di utenti o tutti gli utenti in un tenant.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Usare l'app Microsoft Authenticator per verificare la propria identità durante la reimpostazione della password

**Tipo:** Funzionalità modificata  
**Categoria del servizio:** reimpostazione password self-service  
**Funzionalità del prodotto:** Autenticazione utente

Questa funzionalità consente agli utenti non amministratori di verificare la propria identità durante la reimpostazione di una password tramite una notifica o un codice di Microsoft Authenticator (o qualsiasi altra app di autenticazione). Dopo che gli amministratori attivano questo metodo di reimpostazione della password self-service, gli utenti che hanno eseguito la registrazione di un'app per dispositivi mobili tramite aka.ms/mfasetup o aka.ms/setupsecurityinfo possono usare l'app per dispositivi mobili come metodo di verifica durante la reimpostazione della password.

Le notifiche dell'app per dispositivi mobili possono essere attivate solo come parte di un criterio che richiede due metodi di reimpostazione della password.

---

## <a name="june-2018"></a>Giugno 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Notifica di modifica: correzione della sicurezza per il flusso di autorizzazioni delegate per le app che usano l'API Log attività di Azure AD

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

A causa dell'applicazione di sicurezza più forte, è stato necessario apportare una modifica alle autorizzazioni per le app che usano un flusso di autorizzazione delegato per accedere alle API dei log attività di [Azure AD.](https://aka.ms/aadreportsapi) Questa modifica verrà applicata entro il **26 giugno 2018**.

Se una delle tue app usa le API del log attività di Azure AD, segui questi passaggi per assicurarti che l'app non si interrompa dopo la modifica.

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

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Piattaforma

Transport Layer Security (TLS) è un protocollo che fornisce riservatezza e integrità dei dati tra due applicazioni in comunicazione ed è il protocollo di sicurezza più usato oggi.

Il [PCI Security Standards Council](https://www.pcisecuritystandards.org/) ha determinato che le versioni precedenti di TLS e Secure Sockets Layer (SSL) devono essere disabilitate a favore dell'abilitazione di protocolli di app nuovi e più sicuri, la cui conformità è prevista a partire dal **30 giugno 2018**. Questo cambiamento comporta che, se ci si connette ai servizi di Azure AD e si richiede la conformità PCI DSS, è necessario disabilitare TLS 1.0. Sono disponibili più versioni di TLS, ma TLS 1.2 è la versione più recente disponibile per i servizi di Azure Active Directory. Si consiglia di passare direttamente a TLS 1.2 per le combinazioni sia browser-server sia client-server.

I browser non aggiornati potrebbero non supportare le versioni più recenti di TLS, ad esempio TLS 1.2. Per vedere quali versioni di TLS sono supportate dal browser, accedere al sito [Qualys SSL Labs](https://www.ssllabs.com/) e fare clic su **Test your browser** (Verifica browser). È consigliabile eseguire l'aggiornamento alla versione più recente del Web browser e preferibilmente abilitare solo TLS 1.2.

**Per abilitare TLS 1.2 nei diversi browser**

- **Microsoft Edge e Internet Explorer (si configurano entrambi usando Internet Explorer)**

    1. Aprire Internet Explorer, selezionare **Strumenti** > **Opzioni** > Internet**Avanzate**.
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

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di giugno 2018 sono state aggiunte queste 15 nuove app con il supporto della federazione alla raccolta di app:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token enabled LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>La protezione delle password di AD Azure è disponibile in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Autenticazione utente

Usare la protezione delle password di Azure Active Directory per eliminare le password facilmente individuabili dall'ambiente. Eliminando queste password è possibile diminuire il rischio di compromissione da attacco password spraying.

In particolare, la protezione delle password di Azure AD aiuta a:

- Proteggere gli account dell'organizzazione sia in Azure AD che in Windows Server Active Directory (AD). 
- Impedisce agli utenti di usare le password di un elenco di più di 500 delle password utilizzate più di frequente e oltre 1 milione di varianti di tali password con sostituzione di caratteri.
- Amministrare la protezione delle password di Azure AD da un'unica posizione nel portale di Azure AD sia per Azure AD che per Windows Server Active Directory locale.

Per altre informazioni sulla protezione della password di Azure AD, vedere [Eliminare le password non appropriate nell'organizzazione](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nuovo modello di criteri di accesso condizionale "tutti gli ospiti" creato durante la creazione delle condizioni per l'utilizzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Governance

Durante la creazione delle condizioni per l'utilizzo, viene creato anche un nuovo modello di criteri di accesso condizionale per "tutti gli ospiti" e "tutte le app". Questo nuovo modello di criteri si applica alle Condizioni per l'utilizzo appena create, semplificando il processo di creazione e applicazione per i guest.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nuovo modello di criteri di accesso condizionale "personalizzato" creato durante la creazione delle condizioni per l'utilizzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Governance

Durante la creazione delle condizioni per l'utilizzo, viene creato anche un nuovo modello di criteri di accesso condizionale "personalizzato". Questo nuovo modello di criteri consente di creare la ToU e quindi passare immediatamente al pannello di creazione dei criteri di accesso condizionale, senza dover spostarsi manualmente nel portale.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nuova guida completa su come distribuire Azure Multi-Factor Authentication

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Sono state rilasciate nuove istruzioni dettagliate su come distribuire Azure Multi-Factor Authentication (MFA) nell'organizzazione.

Per visualizzare la guida alla distribuzione di Multi-Factor Authentication, visitare il repository [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (Guide alla distribuzione di identità) in GitHub. Per fornire commenti e suggerimenti sulle guide alla distribuzione, usare il [modulo di feedback per il piano di distribuzione](https://aka.ms/deploymentplanfeedback). In caso di domande sulle guide alla distribuzione, contattare Microsoft all'indirizzo [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>I ruoli di gestione delle app delegati di Azure Active Directory sono in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Controllo di accesso

Gli amministratori ora possono delegare le attività di gestione delle app senza assegnare il ruolo di amministratore globale. I nuovi ruoli e funzionalità sono:

- **Nuovi ruoli amministrativi standard di Azure AD:**

    - **Amministratore dell'applicazione.** Concede la capacità di gestire tutti gli aspetti di tutte le app, fra cui registrazione, impostazioni SSO, assegnazioni di app e licenze, impostazioni proxy di applicazione e consenso (tranne che per le risorse di Azure AD).

    - **Amministratore delle applicazioni cloud.** Concede tutte le capacità di Amministratore di applicazioni, eccetto Proxy di app perché non fornisce l'accesso locale.

    - **Sviluppatore di applicazioni.** Concede la capacità di creare registrazioni delle app, anche se l'opzione **consentire agli utenti di registrare le app** è disattivata.

- **La proprietà, configurata per registrazione di app e per app aziendale, analogamente al processo di proprietà del gruppo:**
 
    - **Proprietario della registrazione delle app.** Concede la capacità di gestire tutti gli aspetti della registrazione delle app di proprietà, inclusi il manifesto dell'app e l'aggiunta di altri proprietari.

    - **Proprietario di app aziendali.** Concede la capacità di gestire numerosi aspetti delle app aziendali di proprietà, fra cui impostazioni SSO, assegnazioni di app e consenso (tranne che per le risorse di Azure AD).

Per altre informazioni sull'anteprima pubblica, vedere il blog [Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) (I ruoli di gestione delle app delegati di Azure AD sono in anteprima pubblica) . Per altre informazioni sui ruoli e le autorizzazioni, vedere Assegnazione di ruoli di amministratore in Azure Active Directory.For more information about roles and permissions, see [Assigning administrator roles in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)

---

## <a name="may-2018"></a>Maggio 2018

### <a name="expressroute-support-changes"></a>Modifiche al supporto di ExpressRoute

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Piattaforma  

Le offerte di software come un servizio, ad esempio Azure Active Directory (Azure AD), sono progettate per ottenere la massima efficienza accedendo direttamente a Internet, senza la necessità di ExpressRoute o di altri tunnel VPN privati. Per questo motivo, l'**1 agosto 2018**, verrà interrotto il supporto di ExpressRoute per i servizi di Azure AD che usano il peering pubblico di Azure e le community di Azure nel peering Microsoft. Tutti i servizi interessati da questa modifica potrebbero riscontrare il graduale passaggio del traffico di Azure AD da ExpressRoute a Internet.

Anche se la modifica al supporto è in corso, è risaputo che esistono ancora situazioni in cui potrebbe essere necessario usare un set di circuiti dedicato per il traffico di autenticazione. Per questo motivo, Azure AD continuerà a supportare le restrizioni per gli intervalli IP in base al tenant usando ExpressRoute e i servizi già inclusi nel peering Microsoft con la community "Altri servizi Online di Office 365". Se i servizi in uso sono interessati, ma è richiesto ExpressRoute, è necessario seguire questa procedura:

- **Se si è nel peering pubblico di Azure.** Passare al peering Microsoft e iscriversi alla community **Altri servizi online di Office 365 (12076:5100)**. Per altre informazioni su come passare dal peering pubblico di Azure al peering Microsoft, vedere l'articolo [Spostare un peering pubblico nel peering Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).

- **Se si è nel peering Microsoft.** Iscriversi alla community **Altri servizi online di Office 365 (12076:5100)**. Per altre informazioni sui requisiti per il routing, vedere la [sezione Supporto per le community BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) dell'articolo Requisiti per il routing di ExpressRoute.

Se è necessario continuare a usare circuiti dedicati, sarà necessario contattare il team degli account Microsoft per informazioni su come ottenere l'autorizzazione per usare la community **Altri servizi online di Office 365 (12076:5100)**. Il Review Board gestito da MS Office verificherà se tali circuiti sono necessari e illustrerà le implicazioni di tecniche che ne seguiranno mantenendoli. Le sottoscrizioni non autorizzate che proveranno a creare filtri della route per Office 365 riceveranno un messaggio di errore. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>API Microsoft Graph per scenari amministrativi relativi alle Condizioni per l'utilizzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Sono state aggiunte le API di Microsoft Graph per l'amministrazione delle condizioni per l'utilizzo di Azure AD. È possibile creare, aggiornare, eliminare l'oggetto condizioni per l'utilizzo.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Aggiunta di un endpoint multi-tenant di Azure AD come provider di identità in Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
Usando criteri personalizzati, è ora possibile aggiungere l'endpoint comune di Azure AD come provider di identità in Azure AD B2C. In questo modo, si otterrà un singolo punto di ingresso per tutti gli utenti di Azure AD che accedono alle applicazioni. Per altre informazioni, vedere [Azure Active Directory B2C: consentire agli utenti di accedere a un provider di identità di Azure AD multi-tenant mediante criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Uso di URL interni per accedere alle app da qualsiasi posizione con l'estensione My Apps Sign-in e Azure AD Application Proxy

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO
 
Gli utenti possono ora accedere alle applicazioni tramite URL interni quando si trovano all'esterno della rete aziendale usando l'estensione My Apps Secure Sign-in per Azure AD. Questa estensione funziona con tutte le applicazioni pubblicate tramite Azure AD Application Proxy su qualsiasi browser in cui è anche installata l'estensione del browser per il pannello di accesso. La funzionalità di reindirizzamento degli URL viene automaticamente abilitata quando un utente accede all'estensione. L'estensione è disponibile per il download in [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) e [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - Dati in Europa per i clienti europei

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** GoLocal

I dati dei clienti in Europa devono restare in Europa e non possono essere replicati al di fuori di data center europei ai fini della conformità alle leggi europee e sulla privacy. Questo [articolo](https://go.microsoft.com/fwlink/?linkid=872328) include i dettagli specifici su quali informazioni sulle identità verranno archiviate in Europa e fornisce anche informazioni sui dati che verranno archiviati al di fuori dei data center europei. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nuove integrazioni di app SaaS per il provisioning utenti - Maggio 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Azure AD permette di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni SaaS, come Dropbox, Salesforce, ServiceNow e altre. A maggio 2018 è stato aggiunto il supporto per il provisioning utenti per le applicazioni seguenti nella raccolta di app Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [cornerstone ondemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Per un elenco di tutte le applicazioni che supportano il provisioning degli utenti nella raccolta di Azure AD, vedere [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Le verifiche di accesso per l'accesso di app e gruppi in Azure AD includono ora controlli ricorrenti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Recensioni di accesso  
**Funzionalità del prodotto:** Governance
 
La verifica di accesso di gruppi e app è in genere disponibile come parte di Azure AD Premium P2.  Gli amministratori potranno configurare le verifiche di accesso delle appartenenze ai gruppi e le assegnazioni delle applicazioni in modo che ricorrano a intervalli regolari, ad esempio ogni mese o ogni trimestre.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Log attività di Azure AD (accessi e controllo) ora disponibili tramite Microsoft Graph

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Azure AD Activity logs, which, includes Sign-ins and Audit logs, are now available through the Microsoft Graph API. Sono stati esposti due endpoint tramite l'API Microsoft Graph per accedere a questi log. Per iniziare, fare riferimento ai [documenti](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) per l'accesso a livello di codice alle API di creazione report di Azure AD. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Miglioramenti all'esperienza di riscatto B2B e di uscita da un'organizzazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

**Redenzione Just-in-Time:** Dopo aver condiviso una risorsa con un utente guest tramite l'API B2B, non è necessario inviare un messaggio di posta elettronica di invito speciale. Nella maggior parte dei casi l'utente guest può accedere alla risorsa per poi essere guidato nell'esperienza di riscatto JIT. Di conseguenza, non vi è alcun impatto a causa di messaggi di posta elettronica non ricevuti. Non più chiedere agli utenti ospiti "Hai cliccato su quel link di riscatto che il sistema ti ha inviato?". Questo significa che quando l'azienda del provider di servizi usa la gestione degli inviti, i file cloud allegati possono avere lo stesso URL canonico per tutti gli utenti, interni ed esterni, in qualsiasi stato di riscatto.

**Esperienza di riscatto moderna:** non è più necessario usare una pagina di destinazione per il riscatto con schermata divisa. Gli utenti vedranno un'esperienza di consenso moderna, che include l'informativa sulla privacy dell'organizzazione che ha emesso l'invito, proprio come avviene con le app di terze parti.

**Gli utenti guest possono lasciare l'organizzazione:** Una volta che il rapporto di un utente con un'organizzazione è finita, può self-service lasciando l'organizzazione. Non più chiamando l'amministratore dell'organizzazione invitante per "essere rimosso", non più sollevando ticket di supporto.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Maggio 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di maggio 2018 sono state aggiunte queste 18 nuove app con il supporto della federazione alla raccolta di app:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial)Sz , OpenReel, Arc Publishing [- SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nuove guide dettagliate alla distribuzione per Azure Active Directory

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Capacità del prodotto:** Directory
 
Nuove istruzioni dettagliate su come distribuire Azure Active Directory (Azure AD), tra cui la reimpostazione della password self-service (SSPR), Single Sign-On (SSO), l'accesso condizionale (CA), il proxy dell'app, il provisioning degli utenti, Active Directory Federation Services (ADFS) per Autenticazione pass-through (PTA) e ADFS a Sincronizzazione hash password (PHS).

Per visualizzare le guide alla distribuzione, visitare il repository [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (Guide alla distribuzione di identità) in GitHub. Per fornire commenti e suggerimenti sulle guide alla distribuzione, usare il [modulo di feedback per il piano di distribuzione](https://aka.ms/deploymentplanfeedback). In caso di domande sulle guide alla distribuzione, contattare Microsoft all'indirizzo [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Ricerca di applicazioni aziendali - Caricamento di altre app

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Per risolvere i problemi di individuazione di applicazioni/entità di servizio, è stata aggiunta la possibilità di caricare altre applicazioni nell'elenco di tutte le applicazioni aziendali. Per impostazione predefinita, vengono visualizzate 20 applicazioni. È ora possibile fare clic, **Carica altro** per visualizzare applicazioni aggiuntive. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>La versione di maggio di AADConnect contiene un'anteprima pubblica dell'integrazione con PingFederate, importanti aggiornamenti della sicurezza, numerose correzioni di bug e nuovi eccezionali strumenti per la risoluzione dei problemi. 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AD Connect  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management
 
La versione di maggio di AADConnect contiene un'anteprima pubblica dell'integrazione con PingFederate, importanti aggiornamenti della sicurezza, numerose correzioni di bug e nuovi eccezionali strumenti per la risoluzione dei problemi. Le note sulla versione sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Verifiche di accesso di Azure AD: applicazione automatica

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Recensioni di accesso  
**Funzionalità del prodotto:** Governance

Le verifiche di accesso di gruppi e app sono in genere disponibili come parte di Azure AD Premium P2. Un amministratore può configurare l'ambiente per l'applicazione automatica delle modifiche da apportare al gruppo o all'app al termine delle verifiche di accesso. L'amministratore può anche specificare che cosa succederà all'accesso continuo dell'utente se i revisori non rispondono, rimuovere l'accesso, mantenere l'accesso o eseguire azioni consigliate relative al sistema. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>I token ID non possono più essere restituiti tramite response_mode query per le nuove app. 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Le app create a partire dal 25 aprile 2018 non potranno più richiedere **id_token** tramite il response_mode **query**.  Questa novità allinea Azure AD alle specifiche OIDC e contribuisce a ridurre la superficie di attacco delle app.  Alle app create prima del 25 aprile 2018 non viene impedito di usare il response_mode **query** con response_type **id_token**.  L'errore restituito, quando si richiede un id_token da AAD, è **AADSTS70007: 'query' non è un valore supportato di 'response_mode' quando**si richiede un token.

I valori di response_mode **fragment**e **form_post** continuano a funzionare quando si creano nuovi oggetti applicazione, ad esempio per l'utilizzo del proxy di app. Assicurarsi di usare uno dei due prima di creare una nuova applicazione.  

---
 
## <a name="april-2018"></a>Aprile 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>I token di accesso di Azure AD B2C sono disponibili a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C 

È ora possibile accedere all'API Web protetta da Azure Active Directory B2C tramite i token di accesso. La funzionalità passerà dall'anteprima pubblica alla disponibilità generale. L'esperienza dell'interfaccia utente per configurare applicazioni Azure Active Directory B2C e API Web è stata migliorata e sono stati apportati altri miglioramenti secondari.
 
Per ulteriori informazioni, vedere [Azure AD B2C: richiesta di token di accesso](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Test della configurazione Single Sign-On per le applicazioni basate su SAML

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Quando si configurano applicazioni SSO basate su SAML, è possibile testare l'integrazione nella pagina di configurazione. Se si verifica un errore durante l'accesso, è possibile fornire l'errore nell'esperienza di test e Azure AD fornisce le procedure di risoluzione per risolvere il problema specifico.

Per altre informazioni, vedere:

- [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Le condizioni per l'uso di Azure AD ora hanno la creazione di report per utenteAzure AD terms of use now has per user reporting

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Conformità
 
Gli amministratori possono ora selezionare una determinata ToU e vedere tutti gli utenti che hanno acconsentito a tale ToU e la data/ora in cui è stata effettuata.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: IP a rischio per la protezione tramite blocco della Extranet di AD FS 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Connect Health ora supporta la capacità di rilevare gli indirizzi IP che superano una soglia di accessi U/P non riusciti su base oraria o giornaliera. Le funzionalità fornite da questa funzione sono:

- Report completo che mostra l'indirizzo IP e il numero di accessi non riusciti generati su base oraria/giornaliera con soglia personalizzabile.
- Avvisi tramite e-mail che indicano quando un indirizzo IP specifico ha superato la soglia di accessi U/P non riusciti su base oraria/giornaliera.
- Un'opzione di download per eseguire un'analisi dettagliata dei dati

Per altre informazioni, vedere [Report sugli indirizzi IP rischiosi](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Configurazione semplificata dell'app con file di metadati o URL

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Nella pagina delle applicazioni Enterprise, gli amministratori possono caricare un file di metadati SAML per configurare l'accesso basato su SAML per  l'applicazione nella raccolta AAD e non nella raccolta.

Inoltre, è possibile utilizzare l'URL dei metadati della federazione di applicazioni Azure AD per configurare SSO con l'applicazione di destinazione.

Per altre informazioni, vedere [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Funzionalità Condizioni per l'utilizzo di Azure AD ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Conformità
 

Le condizioni per l'uso di Azure AD sono state spostate dall'anteprima pubblica alla disponibilità generale.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

È ora possibile specificare con quali organizzazioni partner si desidera condividere e collaborare in Azure AD B2B Collaboration. Per fare questo, è possibile scegliere di creare un elenco di domini specifici di consenso o negazione. Quando un dominio viene bloccato utilizzando queste funzionalità, i collaboratori non possono più inviare inviti a persone che si trovano in quel dominio.

Ciò consente di controllare l'accesso alle risorse, consentendo nel contempo un'esperienza fluida agli utenti approvati.

Questa funzionalità di collaborazione B2B è disponibile per tutti i clienti di Azure Active Directory e può essere usata insieme alle funzionalità di Azure AD Premium come l'accesso condizionale e la protezione delle identità per un controllo più granulare di quando e come gli utenti aziendali esterni firmano accedere e ottenere l'accesso.

Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di aprile 2018 sono state aggiunte queste 13 nuove app con il supporto della federazione alla raccolta di app:

Criterion HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (locale)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Possibilità di consentire agli utenti B2B di Azure AD l'accesso alle applicazioni locali (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

Un'organizzazione che usa Collaborazione B2B di Azure Active Directory (Azure AD) per invitare gli utenti guest di organizzazioni partner in Azure AD può ora fornire a questi utenti B2B l'accesso alle app locali. Queste app locali possono usare l'autenticazione basata su SAML o l'autenticazione integrata di Windows con la delega vincolata Kerberos.

Per altre informazioni, vedere [Concedere agli utenti B2B in Azure AD l'accesso alle applicazioni locali.](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Esercitazioni per l'integrazione dell'accesso Single Sign-On disponibili in Azure Marketplace

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Se un'applicazione elencata in [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) supporta l'accesso singolo basato su SAML, facendo clic su **Scarica adesso** si ottiene il tutorial di integrazione associato a tale applicazione. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Prestazioni più rapide del provisioning automatico degli utenti di Azure AD nelle applicazioni SaaS

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
In precedenza, i clienti che usavano i connettori per il provisioning degli utenti di Azure Active Directory per applicazioni SaaS (ad esempio Salesforce, ServiceNow e Box) potevano sperimentare prestazioni lente se i loro tenant Azure AD contenevano oltre 100.000 utenti e gruppi combinati e usavano le assegnazioni degli utenti e dei gruppi per determinare per quali utenti doveva essere eseguito il provisioning.

Il 2 aprile 2018 il servizio di provisioning Azure AD è stato migliorato in modo significativo, riducendo notevolmente il tempo necessario per eseguire la sincronizzazione iniziale tra Azure Active Directory e le applicazioni SaaS di destinazione.

Di conseguenza, molti clienti con sincronizzazioni iniziali con le applicazioni che hanno richiesto molti giorni o che non sono mai state completate, ora possono completarle in pochi minuti o ore.

Per altre informazioni, vedere [Cosa accade durante il provisioning?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Reimpostazione della password self-service dalla schermata di blocco di Windows 10 per i computer aggiunti ad Azure AD ibrido

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Funzionalità del prodotto:** Autenticazione utente
 
È stata aggiornata la funzionalità di Windows 10 SSPR per includere il supporto per i computer che sono parte di Azure AD ibrido. Questa funzionalità è disponibile in Windows 10 RS4 e consente agli utenti di reimpostare la password dalla schermata di blocco di un computer Windows 10. Gli utenti che sono abilitati e registrati per la reimpostazione self-service della password possono utilizzare questa funzionalità.

Per altre informazioni, consultare [Reimpostazione password self-service di Azure AD dalla schermata di accesso](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Marzo 2018
 
### <a name="certificate-expire-notification"></a>Notifica della scadenza dei certificati

**Tipo:** Correzione  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Azure AD invia una notifica quando un certificato per un'applicazione inclusa o non inclusa nella raccolta sta per scadere. 

Alcuni utenti non ricevevano notifiche per le applicazioni aziendali configurate per l'accesso Single Sign-On basato su SAML. Questo problema è stato risolto. Azure AD invia una notifica per i certificati in scadenza entro 7, 30 e 60 giorni. È possibile visualizzare questo evento nei log di controllo. 

Per altre informazioni, vedere:

- [Gestione di certificati per accesso Single Sign-On federato in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Report delle attività di controllo nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Provider di identità Twitter e GitHub in Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
È ora possibile aggiungere Twitter o GitHub come provider di identità in Azure AD B2C. Twitter passerà dall'anteprima pubblica alla disponibilità generale. GitHub verrà rilasciato in anteprima pubblica.

Per altre informazioni, vedere [Informazioni su Collaborazione B2B di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Limitare l'accesso al browser usando Intune Managed Browser con l'accesso condizionale basato su applicazioni Azure AD per iOS e AndroidRestrict browser access using Intune Managed Browser with Azure AD application-based Conditional Access for iOS and Android

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
**Ora in anteprima pubblica.**

**SSO di Intune Managed Browser:** i dipendenti possono usare Single Sign-On nei client nativi (ad esempio, Microsoft Outlook) e in Intune Managed Browser per tutte le app connesse ad Azure AD.

**Supporto dell'accesso condizionale di Intune Managed Browser:Intune Managed Browser Conditional Access Support:** È ora possibile richiedere ai dipendenti di usare il browser gestito da Intune usando criteri di accesso condizionale basati sull'applicazione.

Per altre informazioni, vedere il [post di blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Per altre informazioni, vedere:

- [Configurare l'accesso condizionale basato sull'applicazione](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Configurare i criteri di Managed Browser](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlet per il proxy dell'app nel modulo GA di PowerShell

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Il supporto per i cmdlet del proxy di applicazione è ora disponibile nel modulo PowerShell GA! Ciò richiede di rimanere aggiornati sui moduli di PowerShell - se si diventa più di un anno indietro, alcuni cmdlet potrebbero smettere di funzionare. 

Per altre informazioni, vedere [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>I client nativi di Office 365 includono il supporto per l'accesso SSO facile tramite un protocollo non interattivo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Gli utenti dei client nativi di Office 365 (versione 16.0.8730.xxxx e successive) possono usufruire di un'esperienza di accesso non interattiva usando l'accesso SSO facile. Questo supporto viene fornito aggiungendo un protocollo non interattivo (WS-Trust) ad Azure AD.

Per altre informazioni, vedere [Come funziona l'accesso in un client nativo con SSO semplice?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>È disponibile un'esperienza di accesso non interattiva, con accesso SSO facile, se un'applicazione invia le richieste di accesso agli endpoint di tenant di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Gli utenti possono usufruire di un'esperienza di accesso non interattiva con Seamless SSO se un'applicazione, ad esempio `https://contoso.sharepoint.com`, invia le richieste di accesso agli endpoint tenant di Azure AD, ovvero `https://login.microsoftonline.com/contoso.com/<..>` o `https://login.microsoftonline.com/<tenant_ID>/<..>`, anziché all'endpoint comune di Azure AD (`https://login.microsoftonline.com/common/<...>`).

Per altre informazioni, vedere [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Per implementare l'accesso SSO facile è necessario aggiungere un solo URL di Azure AD, anziché due URL come in precedenza, alle impostazioni dell'area Intranet degli utenti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Per consentire agli utenti di usufruire dell'accesso SSO facile, è necessario aggiungere un solo URL di Azure AD alle impostazioni dell'area Intranet degli utenti usando Criteri di gruppo in Active Directory: `https://autologon.microsoftazuread-sso.com`. In precedenza era necessario aggiungere due URL.

Per altre informazioni, vedere [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di marzo 2018 sono state aggiunte queste 15 nuove app con il supporto della federazione alla raccolta di app:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM per Risorse di Azure è disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Se si usa Azure AD Privileged Identity Management (PIM) per i ruoli della directory, è ora possibile eseguire le relative funzionalità di assegnazione e accesso con vincoli di tempo per i ruoli di Risorse di Azure, ad esempio Sottoscrizioni, Gruppi di risorse, Macchine virtuali e qualsiasi altra risorsa supportata da Azure Resource Manager. Applicare Multi-Factor Authentication quando si attivano i ruoli in modalità Just-In-Time e si pianificano le attivazioni in base agli intervalli di modifica approvati. Inoltre, questa versione introduce miglioramenti non disponibili nell'anteprima pubblica, tra cui un'interfaccia utente aggiornata, i flussi di lavoro di approvazione e la possibilità di estendere i ruoli prossimi alla scadenza e rinnovare quelli scaduti.

Per altre informazioni, vedere [PIM per Risorse di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Aggiunta di attestazioni facoltative ai token delle app (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Un'app di Azure AD può ora richiedere attestazioni personalizzate o facoltative nei token JWT e SAML.  Si tratta di attestazioni relative all'utente o al tenant che non sono incluse per impostazione predefinita nel token a causa di vincoli di dimensione o applicabilità.  Questa funzionalità è attualmente in anteprima pubblica per le app di Azure AD negli endpoint 1.0 e 2.0.  Vedere la documentazione per informazioni su quali attestazioni possono essere aggiunte e su come modificare il manifesto dell'applicazione per richiederle.  

Per altre informazioni, vedere [Attestazioni facoltative in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD supporta PKCE per flussi OAuth più sicuri

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
La documentazione di Azure AD è stata aggiornata in modo da mettere in evidenza il supporto per PKCE, che consente comunicazioni più sicure durante il flusso di concessione del codice di autorizzazione OAuth 2.0.  Negli endpoint 1.0 e 2.0 sono supportati entrambi i valori di code_challenge S256 e plaintext. 

Per ulteriori informazioni, consultate [Richiedere un codice di autorizzazione.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code) 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Supporto per il provisioning di tutti i valori degli attributi utente disponibili nell'API Get_Workers di Workday

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
L'anteprima pubblica del provisioning in ingresso da Workday ad Active Directory e Azure AD offre ora la possibilità di estrarre tutti i valori degli attributi disponibili nell'API Get_Workers di Workday ed effettuarne il provisioning. Ciò include il supporto per centinaia di attributi standard e personalizzati, oltre a quelli forniti con la versione iniziale del connettore di provisioning in ingresso di Workday.

Per altre informazioni, vedere [Personalizzazione dell'elenco di attributi utente di Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes).

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Modifica dell'appartenenza ai gruppi da dinamica a statica e viceversa

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione
 
È possibile modificare il modo in cui viene gestita l'appartenenza in un gruppo. Ciò è utile per mantenere lo stesso ID e nome di gruppo nel sistema in modo che i riferimenti al gruppo esistenti restino validi. Creando un nuovo gruppo sarebbe infatti necessario aggiornare tali riferimenti.
L'interfaccia di amministrazione di Azure AD è stata aggiornata per il supporto di questa funzionalità. I clienti possono ora convertire i gruppi esistenti dall'appartenenza dinamica a quella assegnata e viceversa. Sono ancora disponibili anche i cmdlet di PowerShell esistenti.

Per altre informazioni, vedere Regole di appartenenza dinamica per i [gruppi in Azure Active DirectoryFor](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) more information, see Dynamic membership rules for groups in Azure Active Directory

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Miglioramento del comportamento di disconnessione con accesso SSO facile

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
In precedenza, anche se disconnessi in modo esplicito da un'applicazione protetta da Azure AD, gli utenti sarebbero stati riconnessi automaticamente tramite l'accesso SSO facile se avessero provato ad accedere nuovamente a un'applicazione di AD Azure all'interno della rete aziendale dai propri dispositivi aggiunti a un dominio. Con questa modifica, la disconnessione è supportata.  Gli utenti possono pertanto scegliere lo stesso o un altro account di Azure AD per eseguire nuovamente l'accesso anziché essere riconnessi automaticamente tramite l'accesso SSO facile.

Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso).
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Rilascio della versione 1.5.402.0 del connettore di Application Proxy

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Questa versione del connettore è stata gradualmente implementata nel mese di novembre. La nuova versione include le modifiche seguenti:

- Il connettore imposta ora i cookie a livello di dominio anziché a livello di sottodominio. Ciò garantisce un'esperienza di accesso SSO più uniforme, evitando richieste di autenticazione ridondanti.
- È incluso il supporto per le richieste di codifica a blocchi.
- È stato migliorato il monitoraggio dell'integrità del connettore. 
- Sono stati introdotti miglioramenti alla stabilità e correzioni di bug.

Per altre informazioni, vedere [Informazioni sui connettori di Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Febbraio 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Esplorazione migliorata per la gestione di utenti e gruppi

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Gestione directory  
**Capacità del prodotto:** Directory

L'esperienza di esplorazione per la gestione di utenti e gruppi è stata semplificata. Ora è possibile passare dalla panoramica della directory direttamente all'elenco di tutti gli utenti, con un più facile accesso all'elenco degli utenti eliminati. Si può anche passare dalla panoramica della directory direttamente all'elenco di tutti i gruppi, con un più facile accesso alle impostazioni di gestione dei gruppi. Inoltre, dalla pagina di panoramica della directory è possibile cercare un utente, un gruppo, un'applicazione aziendale o una registrazione di app. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilità dei report di controllo e delle attività di accesso in Microsoft Azure gestito da 21Vianet (21Vianet per Azure Cina)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure Stack  
**Funzionalità del prodotto:** Monitoraggio e creazione report

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

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Come parte del feedback dei clienti per consentire ai ruoli non amministratori di avere accesso ai log attività di Azure AD, è stata abilitata la possibilità per gli utenti che utilizzano il ruolo "Lettore report" di accedere alle attività di accesso e controllo all'interno del portale di Azure e di usare Microsoft Graph Api. 

Per altre informazioni sull'uso di questi report, vedere [Creazione di report in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Attestazione EmployeeID disponibile come attributo utente e identificatore utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
È possibile configurare **EmployeeID** come ID utente e attributo utente per gli utenti membri e i guest B2B nelle applicazioni con accesso basato su SAML dall'interfaccia utente delle applicazioni aziendali.

Per altre informazioni, vedere [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gestione semplificata delle applicazioni mediante caratteri jolly in Azure AD Application Proxy

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Autenticazione utente
 
Per facilitare la distribuzione delle applicazioni e ridurre il sovraccarico amministrativo, ora è possibile pubblicare le applicazioni usando caratteri jolly. Per pubblicare un'applicazione con caratteri jolly, è possibile seguire il flusso di pubblicazione standard delle applicazioni ma usare un carattere jolly negli URL interni ed esterni.

Per altre informazioni, vedere [Applicazioni con carattere jolly in Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard).

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nuovi cmdlet a supporto della configurazione del proxy di applicazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Piattaforma

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

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Piattaforma

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

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma
 
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
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Applicazioni che supportano i criteri di protezione delle app di Intune aggiunti per l'uso con l'accesso condizionale basato sulle applicazioni di Azure ADApplications supporting Intune App Protection policies added for use with Azure AD application-based Conditional Access

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Sono state aggiunte altre applicazioni che supportano l'accesso condizionale basato su applicazioni. Ora è possibile accedere a Office 365 e ad altre app cloud connesse ad Azure AD usando queste app client approvate.

Le applicazioni seguenti verranno aggiunte entro le fine di febbraio:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Per altre informazioni, vedere:

- [Requisito per le app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD app-based Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Aggiornamento delle condizioni per l'utilizzo dell'esperienza mobile 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Conformità

Quando vengono visualizzate le condizioni per l'utilizzo, è ora possibile fare clic su **Problemi di visualizzazione? Clicca qui**. Questo collegamento consente di aprire le Condizioni per l'utilizzo in modo nativo sul dispositivo. Indipendentemente dalle dimensioni del carattere del documento o dalle dimensioni dello schermo del dispositivo, è possibile ingrandire la visualizzazione finché non si riesce a leggere il documento. 

---
 
## <a name="january-2018"></a>Gennaio 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di gennaio 2018 sono state aggiunte le nuove app seguenti con il supporto della federazione alla raccolta di app:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), directory federata IriusRisk e [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Rilevato accesso con rischi aggiuntivi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Le informazioni dettagliate ottenute per un rilevamento dei rischi rilevato sono legate alla sottoscrizione di Azure AD. Con l'edizione Azure AD Premium P2, si ottengono le informazioni più dettagliate su tutti i rilevamenti sottostanti.

Con l'edizione di Azure AD Premium P1, i rilevamenti non coperti dalla licenza vengono visualizzati come accesso al rilevamento dei rischi con rischio aggiuntivo rilevato.

Per altre informazioni, vedere [Rilevamenti dei rischi in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Possibilità di nascondere le applicazioni di Office 365 dai pannelli di accesso dell'utente finale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO

Ora è possibile gestire in maniera più efficiente la modalità di visualizzazione delle applicazioni di Office 365 nei pannelli di accesso dell'utente tramite una nuova impostazione utente. Questa opzione è utile per ridurre il numero di app nei pannelli di accesso di un utente se si preferisce visualizzare solo le app di Office nel portale di Office. L'impostazione è disponibile in **Impostazioni utente** ed è denominata **Gli utenti possono visualizzare solo le app di Office 365 nel portale di Office 365**.

Per altre informazioni, vedere [Nascondere un'applicazione dall'esperienza utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Accesso trasparente alle app abilitato per l'accesso SSO basato su password direttamente dall'URL dell'app 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO

L'estensione del browser App personali è ora disponibile tramite un utile strumento che mette a disposizione la funzionalità Single-Sign On come collegamento nel browser. Dopo l'installazione, nel browser comparirà un'icona di avvio delle app che assicurerà un accesso rapido alle app. Gli utenti possono ora sfruttare i vantaggi seguenti:

- La possibilità di accedere direttamente alle app basate su SSO con password dalla pagina di accesso dell'app
- Avvio delle app tramite la funzionalità di ricerca rapida
- Collegamenti alle app usate di recente dall'estensione
- L'estensione è disponibile per il download in Microsoft Edge, Chrome e Firefox.
 
Per altre informazioni, vedere [Estensione per l'accesso sicuro alle app personali](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Esperienza di amministrazione di Azure AD nel portale di Azure classico ritirata

**Tipo:** Deprecato   
**Categoria di servizio:** Azure AD  
**Capacità del prodotto:** Directory

A partire dall'8 gennaio 2018, l'esperienza di amministrazione di Azure AD nel portale di Azure classico è stata ritirata. Questo ritiro ha avuto luogo in concomitanza con quello del portale di Azure classico. In futuro è consigliabile usare il [centro di amministrazione di Azure AD](https://aad.portal.azure.com) per tutte le operazioni di amministrazione basate sul portale di Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Il portale Web di PhoneFactor è stato ritirato

**Tipo:** Deprecato  
**Categoria di servizio:** Azure AD  
**Capacità del prodotto:** Directory
 
A partire dall'8 gennaio 2018, il portale Web di PhoneFactor è stato ritirato. Questo portale è stato usato per l'amministrazione del server MFA, ma tali funzionalità sono state spostate nel portale di Azure all'indirizzo portal.azure.com. 

La configurazione di MFA è accessibile da **Azure Active Directory \> MFA Server**.
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati

**Tipo:** Deprecato  
**Categoria di servizio:** Creazione di report  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management  


Considerando la disponibilità generale della nuova console di amministrazione di Azure Active Directory e delle nuove API ora disponibili per i report sulle attività e sulla sicurezza, le API di report nell'endpoint "/reports" sono state ritirate a partire dal 31 dicembre 2017.

**Elementi disponibili**

Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API offre funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. È ora possibile accedere ai dati precedentemente disponibili tramite i report di sicurezza tramite l'API di rilevamento dei rischi di Identity Protection in Microsoft Graph.

Per altre informazioni, vedere:

- [Introduzione all'API di creazione report di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Dicembre 2017

### <a name="terms-of-use-in-the-access-panel"></a>Condizioni per l'utilizzo nel riquadro di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Conformità
 
È ora possibile passare al riquadro di accesso e visualizzare le condizioni per l'utilizzo che sono state precedentemente accettate.

A tale scopo, seguire questa procedura:

1. Aprire il [portale App personali](https://myapps.microsoft.com) ed eseguire l'accesso.

2. Nell'angolo in alto a destra selezionare il nome e quindi scegliere **Profilo** dall'elenco. 

3. In **Profilo** selezionare **Verificare le condizioni d'uso**. 

4. È ora possibile verificare le condizioni per l'utilizzo accettate. 

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nuova esperienza di accesso di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Capacità del prodotto:** Autenticazione utente
 
Azure AD e le interfacce utente del sistema di identità dell'account Microsoft sono stati riprogettati in modo da avere un aspetto uniforme e coerente. Inoltre, nella pagina di accesso di Azure AD viene visualizzato prima di tutto il nome utente, seguito dalle credenziali in una seconda schermata.

Per altre informazioni, vedere [The new Azure AD sign-in experience is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (La nuova esperienza di accesso di AD Azure è ora in anteprima pubblica).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Meno richieste di accesso: nuova esperienza di tipo "Mantieni l'accesso" per l'accesso ad Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Capacità del prodotto:** Autenticazione utente
 
La casella di controllo **Mantieni l'accesso** nella pagina di accesso di Azure AD è stata sostituita con un nuovo prompt che viene visualizzato dopo la corretta esecuzione dell'autenticazione. 

Se risponde **Sì** a questo prompt, il servizio offre un token di aggiornamento permanente. Questo comportamento è analogo a quello che succedeva selezionando la casella di controllo **Mantieni l'accesso** nell'esperienza precedente. Per i tenant federati, questo prompt viene visualizzato dopo la corretta autenticazione con il servizio federato.

Per altre informazioni, vedere [Fewer sign-in prompts: The new "keep me signed in" experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Meno prompt di accesso: la nuova esperienza di tipo "Mantieni l'accesso" per Azure AD è in anteprima). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Aggiunta configurazione per richiedere l'espansione delle condizioni per l'utilizzo prima dell'accettazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Conformità
 
Un'opzione per gli amministratori richiede che gli utenti espandano le condizioni per l'utilizzo prima di accettarle.

Selezionare **Sì** o **No** per richiedere che gli utenti espandano le condizioni per l'utilizzo. L'impostazione **Sì** richiede che gli utenti visualizzino le condizioni per l'utilizzo prima di accettarle.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Attivazione con ambito per le assegnazioni di ruolo idonee

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
È possibile usare l'attivazione con ambito per attivare le assegnazioni di ruolo delle risorse di Azure idonee con meno autonomia rispetto alle impostazioni predefinite dell'assegnazione originale. Un esempio è se all'utente viene assegnato il ruolo di proprietario di una sottoscrizione nel tenant. Con l'attivazione con ambito, è possibile attivare il ruolo di proprietario per un massimo di cinque risorse contenute all'interno della sottoscrizione (ad esempio gruppi di risorse e macchine virtuali). La definizione dell'ambito per l'attivazione potrebbe ridurre la possibilità di apportare modifiche indesiderate alle risorse di Azure critiche.

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (Che cos'è Azure AD Privileged Identity Management?).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nuove app federate nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel dicembre 2017 sono state aggiunte queste nuove app con il supporto della federazione alla raccolta di app:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [integrazione di Azure AD con MobileIron](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo di resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket di resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, integrazione di Azure AD con Zenegy.

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Flussi di lavoro di approvazione per i ruoli della directory di Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Il flusso di lavoro di approvazione per i ruoli della directory di Azure AD è disponibile a livello generale.

Con il flusso di lavoro di approvazione, gli amministratori dei ruoli con privilegi possono fare in modo che i membri con ruolo idoneo richiedano l'attivazione del ruolo prima di poter usare il ruolo con privilegi. A più utenti e gruppi possono essere delegate responsabilità di approvazione. I membri con ruolo idoneo ricevono una notifica quando l'approvazione è completata e il ruolo è attivo.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticazione pass-through: supporto per Skype for Business

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Capacità del prodotto:** Autenticazione utente

L'autenticazione pass-through supporta ora l'accesso degli utenti alle applicazioni client Skype for Business che supportano l'autenticazione moderna, che include topologie online e ibride. 

Per altre informazioni, vedere [Skype for Business topologies supported with modern authentication](https://technet.microsoft.com/library/mt803262.aspx) (Topologie di Skype for Business supportate con l'autenticazione moderna).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aggiornamenti di Azure AD Privileged Identity Management per Controllo degli accessi in base al ruolo di Azure (anteprima)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Con l'aggiornamento dell'anteprima pubblica di Azure AD Privileged Identity Management (PIM) per Controllo degli accessi in base al ruolo di Azure, è ora possibile:

* Usare Just Enough Administration.
* Richiedere l'approvazione per l'attivazione dei ruoli delle risorse.
* Pianificare l'attivazione futura di un ruolo che richiede l'approvazione per entrambi i ruoli di Azure AD e Controllo degli accessi in base al ruolo di Azure.
 
Per altre informazioni, vedere [Privileged Identity Management for Azure resources (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) (Privileged Identity Management per risorse di Azure (anteprima)).

---
 
## <a name="november-2017"></a>Novembre 2017
 
### <a name="access-control-service-retirement"></a>Ritiro del Servizio di controllo di accesso

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Servizio di controllo di accesso  
**Capacità del prodotto:** Servizio di controllo di accesso 

Controllo di accesso di Azure Active Directory, noto anche come Servizio di controllo di accesso, verrà deprecato alla fine del 2018. Altre informazioni, tra cui una pianificazione dettagliata e istruzioni di carattere generale sulla migrazione, saranno disponibili nelle prossime settimane. È possibile lasciare un commento in questa pagina con eventuali domande sul Servizio di controllo di accesso. Un membro del team sarà a disposizione per rispondere.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Limitazione dell'accesso del browser a Intune Managed Browser 

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Capacità del prodotto:** Sicurezza e protezione delle identità

È possibile limitare l'accesso del browser a Office 365 e ad altre app cloud connesse ad Azure AD tramite Intune Managed Browser come app approvata. 

È ora possibile configurare la condizione seguente per l'accesso condizionale basato sull'applicazione:You can now configure the following condition for application-based Conditional Access:

**App client:** Browser

**Qual è l'effetto della modifica?**

Attualmente, l'accesso è bloccato quando si usa questa condizione. Quando l'anteprima sarà disponibile, tutti gli accessi richiederanno l'uso dell'applicazione del browser gestita. 

Cercare questa funzionalità e altre informazioni nelle note sulla versione e nei blog che verranno pubblicati a breve. 

Per altre informazioni, vedere [Accesso condizionale in Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure ADNew approved client apps for Azure AD app-based Conditional Access

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Capacità del prodotto:** Sicurezza e protezione delle identità

Le app seguenti sono nell'elenco delle [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Per altre informazioni, vedere:

- [Requisito per le app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD app-based Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Supporto di più lingue per le condizioni per l'utilizzo

**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Conformità

Gli amministratori possono ora creare nuove condizioni per l'utilizzo contenenti più documenti PDF. È possibile contrassegnare i documenti PDF con una lingua corrispondente. Gli utenti visualizzano il PDF con la lingua corrispondente in base alle proprie preferenze. Se non c'è corrispondenza, viene visualizzata la lingua predefinita.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stato del client relativamente al writeback delle password in tempo reale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Capacità del prodotto:** Autenticazione utente

È ora possibile esaminare lo stato del client relativamente al writeback delle password in locale. Questa opzione è disponibile nella sezione **Integrazione locale** della pagina [Reimpostazione della password](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Se ci sono problemi con la connessione al client di writeback locale, viene visualizzato un messaggio di errore che specifica:

- Informazioni sui motivi per cui non è possibile connettersi al client di writeback locale.
- un collegamento alla documentazione di supporto alla risoluzione del problema. 

Per ulteriori informazioni, vedere [Integrazione locale](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD app-based Conditional Access 
 
**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Capacità del prodotto:** Sicurezza e protezione delle identità

È ora possibile limitare l'accesso a Office 365 e ad altre app cloud connesse ad Azure AD alle [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) che supportano i criteri di protezione delle app di Intune usando [l'accesso condizionale basato sulle app di Azure AD.](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) I criteri di protezione app di Intune vengono usati per configurare e proteggere i dati aziendali in queste applicazioni client.

Combinando i criteri di accesso condizionale [basati su app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) e criteri di accesso condizionale basati su [dispositivo,](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) si ha la flessibilità necessaria per proteggere i dati per i dispositivi personali e aziendali.

Le condizioni e i controlli seguenti sono ora disponibili per l'uso con l'accesso condizionale basato su app:

**Condizione per le piattaforme supportate**

- iOS
- Android

**Condizione per le app client**

- App per dispositivi mobili e client desktop

**Controllo di accesso**

- Richiedere app client approvata

Per altre informazioni, vedere [Accesso condizionale basato su app di Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gestione di dispositivi di Azure AD nel portale di Azure

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Capacità del prodotto:** Sicurezza e protezione delle identità

È ora possibile individuare tutti i dispositivi connessi ad Azure AD e le attività correlate al dispositivo in un'unica posizione. È disponibile una nuova esperienza di amministrazione per gestire tutte le impostazioni e le identità dei dispositivi nel portale di Azure. In questa versione è possibile:

- Visualizzare tutti i dispositivi disponibili per l'accesso condizionale in Azure AD.
- Visualizzare le proprietà, inclusi i dispositivi aggiunti ad Azure AD ibridi.
- Trovare le chiavi di BitLocker per i dispositivi aggiunti ad Azure AD, gestire il dispositivo con Intune e altro ancora.
- Gestire le impostazioni relative al dispositivo di Azure AD.

Per altre informazioni, vedere [Manage devices by using the Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal) (Gestire dispositivi tramite il portale di Azure).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Supporto per macOS come piattaforma di dispositivi per l'accesso condizionale di Azure ADSupport for macOS as a device platform for Azure AD Conditional Access 

**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Accesso condizionale  
**Capacità del prodotto:** Sicurezza e protezione delle identità 

È ora possibile includere (o escludere) macOS come condizione della piattaforma del dispositivo nei criteri di accesso condizionale di Azure AD. Con l'aggiunta di macOS alle piattaforme di dispositivi supportati, è possibile:

- **Registrare e gestire i dispositivi macOS con Intune.** Analogamente ad altre piattaforme quali iOS e Android, un'applicazione del portale aziendale è disponibile per macOS per eseguire le registrazioni unificate. È possibile usare la nuova app del portale aziendale per macOS per registrare un dispositivo con Intune e registrarlo con Azure AD.
- **Assicurarsi che i dispositivi macOS siano conformi ai criteri di conformità dell'organizzazione definiti in Intune.** Con Intune nel portale di Azure è ora possibile impostare criteri di conformità per i dispositivi macOS. 
- **Limitare l'accesso alle applicazioni in Azure AD solo ai dispositivi macOS conformi.** La creazione di criteri di accesso condizionale ha macOS come opzione della piattaforma del dispositivo separata. A questo punto è possibile creare criteri di accesso condizionale specifici di macOS per il set di applicazioni di destinazione in Azure.Now you can author macOS-specific Conditional Access policies for the targeted application set in Azure.

Per altre informazioni, vedere:

- [Creare criteri di conformità per i dispositivi macOS con Intune](https://aka.ms/macoscompliancepolicy)
- [Accesso condizionale in Azure ADConditional Access in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Estensione di Server dei criteri di rete per Azure Multi-Factor Authentication 

**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Autenticazione a più fattori  
**Capacità del prodotto:** Autenticazione utente

L'estensione di Server dei criteri di rete per Azure Multi-Factor Authentication aggiunge funzionalità di autenticazione a più fattori basata sul cloud nell'infrastruttura di autenticazione corrente usando i server esistenti. Con l'estensione di Server dei criteri di rete, è possibile aggiungere la verifica con telefonata, messaggio di testo o app telefonica al flusso di autenticazione esistente. Pertanto, non è necessario installare, configurare e gestire nuovi server. 

Questa estensione è stata creata per le organizzazioni che vogliono proteggere le connessioni della rete privata virtuale senza distribuire il server Azure Multi-Factor Authentication. L'estensione di Server dei criteri di rete funge da adattatore tra RADIUS e Azure Multi-Factor Authentication basato su cloud per fornire un secondo fattore di autenticazione per utenti federati o sincronizzati.

Per altre informazioni, vedere [Integrate your existing Network Policy Server infrastructure with Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension) (Integrare l'infrastruttura di Server dei criteri di rete esistente con Azure Multi-Factor Authentication).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Ripristinare o rimuovere definitivamente gli utenti eliminati

**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Gestione utenti  
**Capacità del prodotto:** Directory 

Nel centro di amministrazione di Azure Active Directory è possibile:

- Ripristinare un utente eliminato. 
- Eliminare definitivamente un utente.

**Per provare il servizio:**

1. Nel centro di amministrazione di Azure Active Directory, nella sezione **Gestisci** selezionare [Tutti gli utenti](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All). 

2. Dall'elenco **Mostra** selezionare **Utenti eliminati di recente**. 

3. Selezionare uno o più utenti eliminati di recente e quindi ripristinarli oppure eliminarli definitivamente.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure ADNew approved client apps for Azure AD app-based Conditional Access
 
**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Accesso condizionale  
**Capacità del prodotto:** Sicurezza e protezione delle identità

Le app seguenti sono state aggiunte all'elenco di [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft Planner
- Azure Information Protection 

Per altre informazioni, vedere:

- [Requisito per le app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD app-based Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Usare "OR" tra i controlli in un criterio di accesso condizionaleUse "OR" between controls in a Conditional Access policy 

**Tipo:** Funzionalità modificata    
**Categoria di servizio:** Accesso condizionale  
**Capacità del prodotto:** Sicurezza e protezione delle identità
 
È ora possibile utilizzare "OR" (richiedere uno dei controlli selezionati) per i controlli di accesso condizionale. È possibile usare questa funzionalità per creare criteri usando l'operatore "OR" tra i controlli di accesso. È ad esempio possibile usare questa funzionalità per creare un criterio che richiede all'utente di accedere usando Multi-Factor Authentication o in alternativa di usare un dispositivo conforme.

Per altre informazioni, vedere Controlli in Accesso condizionale di [Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Aggregazione di rilevamenti dei rischi in tempo reale

**Tipo:** Funzionalità modificata    
**Categoria di servizio:** Protezione dell'identità  
**Capacità del prodotto:** Sicurezza e protezione delle identità

In Azure AD Identity Protection tutti i rilevamenti dei rischi in tempo reale originati dallo stesso indirizzo IP in un determinato giorno vengono ora aggregati per ogni tipo di rilevamento dei rischi. Questa modifica limita il volume di rilevamenti dei rischi visualizzati senza alcuna modifica nella sicurezza dell'utente.

Il rilevamento in tempo reale sottostante funziona ogni volta che l'utente effettua l'accesso. Se si impostano criteri di sicurezza relativi al rischio di accesso per Multi-Factor Authentication o il blocco dell'accesso, verranno attivati per ogni accesso rischioso.
 
---
 
## <a name="october-2017"></a>Ottobre 2017

### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management  

Il portale di Azure offre:

- Una nuova console di amministrazione di Azure AD.
- Nuove API per i report sulle attività e sulla sicurezza.
 
Considerando la disponibilità di queste nuove funzionalità, le API di report nell'endpoint /reports verranno ritirate il 10 dicembre 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Rilevamento automatico dei campi di accesso

**Tipo:** Correzione   
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Single Sign-On  

Azure AD supporta il rilevamento automatico del campo di accesso per le applicazioni che eseguono il rendering di un campo HTML per nome utente e password. Questi passaggi sono illustrati in [Come acquisire manualmente i campi di accesso per un'applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). È possibile trovare questa funzionalità aggiungendo un'applicazione *non nella raccolta* nella pagina **Applicazioni aziendali** del [portale di Azure](https://aad.portal.azure.com). È anche possibile configurare la modalità **Single Sign-On** in questa nuova applicazione impostandola su **Accesso Single Sign-On basato su password**, immettere un URL Web e quindi salvare la pagina.
 
A causa di un problema del servizio, questa funzionalità è stata temporaneamente disabilitata. Il problema è stato risolto e il rilevamento automatico del campo di accesso è nuovamente disponibile.

---

### <a name="new-multi-factor-authentication-features"></a>Nuove funzionalità di Multi-Factor Authentication

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazione a più fattori  
**Capacità del prodotto:** Sicurezza e protezione delle identità  

Multi-Factor Authentication (MFA) è fondamentale per proteggere l'organizzazione. Per rendere più adattive le credenziali e semplificare l'esperienza, sono state aggiunte le funzionalità seguenti: 

- I risultati della richiesta a più fattori sono integrati direttamente nel report di accesso di Azure AD, che include l'accesso a livello di codice ai risultati dell'autenticazione a più fattori.
- La configurazione di MFA è integrata in modo più approfondito nell'esperienza di configurazione di Azure AD nel portale di Azure.

Con questa versione di anteprima pubblica, la gestione e la creazione di report di MFA sono alla base dell'esperienza di configurazione di Azure Active Directory. È ora possibile gestire la funzionalità del portale di gestione di MFA nell'esperienza di Azure AD.

Per altri dettagli, vedere [Informazioni di riferimento sui report dell'autenticazione a più fattori nel portale di Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni d'uso  
**Funzionalità del prodotto:** Conformità  

È possibile usare le condizioni per l'utilizzo di Azure AD per presentare agli utenti informazioni quali dichiarazioni di non responsabilità pertinenti per requisiti legali o di conformità.

È possibile usare le condizioni per l'utilizzo di Azure AD negli scenari seguenti:

- Condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione
- Condizioni per l'utilizzo specifiche basate sugli attributi di un utente (ad esempio, medici o infermieri oppure dipendenti nazionali o internazionali, creazione tramite gruppi dinamici)
- Condizioni per l'utilizzo specifiche per l'accesso ad app aziendali a impatto elevato, come Salesforce

Per altre informazioni, vedere [Condizioni per l'utilizzo di Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Miglioramenti di Privileged Identity Management

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management  

Con Azure AD Privileged Identity Management, è possibile gestire, controllare e monitorare l'accesso alle risorse di Azure (anteprima) all'interno dell'organizzazione per:

- Sottoscrizioni
- Gruppi di risorse
- Macchine virtuali 

Tutte le risorse nel portale di Azure che usano la funzionalità Controllo degli accessi in base al ruolo di Azure possono trarre vantaggio da tutte funzionalità di sicurezza e gestione del ciclo di vita offerte da Azure AD Privileged Identity Management.

Per altre informazioni, vedere [Privileged Identity Management for Azure resources](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) (Privileged Identity Management per risorse di Azure).

---

### <a name="access-reviews"></a>Verifiche di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Conformità  

Le organizzazioni possono usare le verifiche di accesso (anteprima) per gestire in modo efficiente l'appartenenza ai gruppi e l'accesso alle applicazioni aziendali: 

- È possibile ripetere la certificazione dell'accesso degli utenti guest usando le verifiche di accesso per i rispettivi accessi alle applicazioni e le appartenenze ai gruppi. I revisori possono decidere in modo efficiente se consentire ai guest l'accesso continuo sulla base delle informazioni dettagliate fornite dalle verifiche di accesso.
- È possibile ripetere la certificazione dell'accesso dei dipendenti alle applicazioni e delle appartenenze ai gruppi con le verifiche di accesso.

È possibile raccogliere i controlli delle verifiche di accesso in programmi rilevanti per l'organizzazione, in modo da controllare le verifiche per ottenere informazioni sulla conformità o sulle applicazioni più a rischio.

Per altre informazioni, vedere [Verifiche di accesso di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Possibilità di nascondere le applicazioni di terze parti dall'utilità di avvio delle app di Office 365 e di App personali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Single Sign-On  

È ora possibile gestire meglio le app visualizzate nei portali degli utenti tramite una nuova proprietà che consente di **nascondere le app**. È possibile nascondere le app nei casi in cui vengano visualizzati riquadri delle app per i servizi back-end o riquadri duplicati che creano disordine nelle icone di avvio delle app degli utenti. È possibile abilitare o disabilitare questa proprietà nella sezione **Proprietà** dell'app di terze parti tramite l'opzione **Visibile agli utenti?**. È anche possibile nascondere un'app a livello di codice tramite PowerShell. 

Per altre informazioni, vedere [Nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Elementi disponibili**

 Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API offre funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. The data previously available through the security reports now can be accessed through the Identity Protection Risk Detections API in Microsoft Graph.


## <a name="september-2017"></a>Settembre 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix per Identity Manager

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità  

Dal 25 settembre 2017 è disponibile un pacchetto hotfix rollup (build 4.4.1642.0) per Identity Manager 2016 Service Pack 1. Questo pacchetto rollup:

- Risolve alcuni problemi e aggiunge miglioramenti.
- È un aggiornamento cumulativo che sostituisce tutti gli aggiornamenti di Identity Manager 2016 Service Pack 1 fino alla build 4.4.1459.0 per Identity Manager 2016. 
- Richiede Identity Manager 2016 build 4.4.1302.0. 

Per altre informazioni, vedere [Pacchetto hotfix rollup (build 4.4.1642.0) disponibile per Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
