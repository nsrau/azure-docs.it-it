---
title: Informazioni su Azure Active Directory - Azure Active Directory | Microsoft Docs
description: Panoramica e informazioni concettuali su Azure Active Directory, come terminologia, licenze disponibili e un elenco di funzionalità associate con i collegamenti per altre informazioni.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.date: 05/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce8fad7f0dc76aad306e0f2a8e26692ec997952c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470351"
---
# <a name="what-is-azure-active-directory"></a>Informazioni su Azure Active Directory

Azure Active Directory (Azure AD) è il servizio Microsoft basato sul cloud per la gestione delle identità e dell'accesso, che consente ai dipendenti di accedere e usare le risorse in:

- Risorse esterne, tra cui Microsoft Office 365, il portale di Azure e migliaia di altre applicazioni SaaS.

- Risorse interne, tra cui app nella rete e nella Intranet aziendale, oltre a eventuali app cloud sviluppate dall'organizzazione.

È possibile usare i diversi poster della [serie su Microsoft Cloud per Enterprise Architects](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) per ottenere informazioni più dettagliate sui servizi di gestione delle identità principali di Azure, Azure AD e Office 365.

## <a name="who-uses-azure-ad"></a>Utenti di Azure AD

Azure AD è destinato agli utenti seguenti:

- **Amministratori IT.** Gli amministratori IT possono usare Azure AD per controllare l'accesso alle app e alle risorse delle app in base ai requisiti aziendali. È ad esempio possibile usare Azure AD per richiedere Multi-Factor Authentication in caso di accesso a risorse importanti dell'organizzazione. È inoltre possibile usare Azure AD per automatizzare il provisioning utenti tra le app Windows Server AD e cloud esistenti, incluso Office 365. Azure AD offre infine strumenti avanzati per contribuire automaticamente alla protezione delle identità e delle credenziali degli utenti e per la conformità con i requisiti di accesso definiti dalla governance. Per iniziare, iscriversi per ottenere una [versione di valutazione gratuita di 30 giorni di Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Sviluppatori di app.** Azure AD offre agli sviluppatori di app un approccio basato sugli standard per l'aggiunta dell'accesso Single Sign-On (SSO) all'app, consentendone il funzionamento con le credenziali già esistenti dell'utente. Azure AD fornisce anche API utili per la creazione di esperienze di app personalizzate usando i dati esistenti dell'organizzazione. Per iniziare, iscriversi per ottenere una [versione di valutazione gratuita di 30 giorni di Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Per altre informazioni, vedere anche [Azure Active Directory per sviluppatori](../develop/index.yml).

- **Sottoscrittori di Microsoft 365, Office 365, Azure o Dynamics CRM Online.** I sottoscrittori usano già Azure AD. Ogni tenant di Microsoft 365, Office 365, Azure e Dynamics CRM Online è automaticamente un tenant di Azure AD. È possibile iniziare immediatamente a gestire l'accesso alle app cloud integrate.

## <a name="what-are-the-azure-ad-licenses"></a>Informazioni sulle licenze di Azure AD

I servizi aziendali di Microsoft Online, come Office 365 o Microsoft Azure, richiedono Azure AD per l'accesso e per la protezione delle identità. Se si sottoscrive un servizio aziendale di Microsoft Online, si ottiene quindi automaticamente Azure AD con accesso a tutte le funzionalità gratuite.

Per migliorare l'implementazione di Azure AD, è anche possibile aggiungere funzionalità a pagamento effettuando l'aggiornamento alle licenze di Azure Active Directory Basic Edition, Premium P1 Edition o Premium P2. Le licenze a pagamento di Azure AD sono basate sulla directory gratuita esistente e offrono agli utenti mobili funzionalità di monitoraggio self-service avanzato, creazione di report di sicurezza e accesso sicuro.

>[!Note]
>Per le opzioni relative ai prezzi di queste licenze, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
>
>Le edizioni Azure Active Directory Premium P1 e Premium P2 e Azure Active Directory Basic non sono attualmente supportate in Cina. Per altre informazioni sui prezzi di Azure AD, visitare il [forum di Azure Active Directory](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Active Directory Free.** Offre funzionalità di gestione di utenti e gruppi, sincronizzazione di directory locali, report di base e accesso Single Sign-On in Azure, Office 365 e molte delle app SaaS più diffuse.

- **Azure Active Directory Basic.** Oltre alle funzionalità del livello Gratuito, il livello Basic offre anche l'accesso alle app incentrato sul cloud, la gestione dell'accesso basata sui gruppi, la reimpostazione della password self-service per app cloud e Azure AD Application Proxy, che consente di pubblicare app Web locali con Azure AD.

- **Azure Active Directory Premium P1.** Oltre alle funzionalità dei livelli Gratuito e Basic, il livello P1 consente agli utenti ibridi di accedere alle risorse locali e sul cloud. Supporta anche funzionalità di amministrazione avanzate, tra cui gruppi dinamici, gestione dei gruppi self-service, Microsoft Identity Manager (famiglia di programmi per la gestione delle identità e dell'accesso in locale) e funzionalità di writeback cloud, che consentono la reimpostazione della password self-service per gli utenti locali.

- **Azure Active Directory Premium P2.** Oltre alle funzionalità dei livelli Gratuito, Basic e P1, il livello P2 offre anche [Azure Active Directory Identity Protection](../identity-protection/enable.md) per fornire alle app e ai dati aziendali cruciali l'accesso condizionale basato sul rischio e [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) per consentire l'individuazione, la limitazione e il monitoraggio degli amministratori e del rispettivo accesso alle risorse e per fornire l'accesso Just-In-Time quando necessario.

- **Licenze per le funzionalità "Con pagamento in base al consumo".** È anche possibile ottenere licenze per le funzionalità aggiuntive, ad esempio Azure Active Directory Business-to-Customer (B2C). B2C consente di fornire soluzioni di gestione delle identità e dell'accesso per le app rivolte ai clienti. Per altre informazioni, vedere la [documentazione di Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Per altre informazioni sull'associazione di una sottoscrizione di Azure ad Azure AD, vedere [Procedura: Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md). Per altre informazioni sull'assegnazione di licenze agli utenti, vedere [Procedura: Assegnare o rimuovere licenze di Azure Active Directory](license-users-groups.md).

## <a name="terminology"></a>Terminologia

Per una migliore comprensione di Azure AD e della rispettiva documentazione, è consigliabile esaminare la terminologia seguente.

|Termine o concetto|DESCRIZIONE|
|---------------|-----------|
|Sottoscrizione di Azure| Usata per pagare i servizi cloud di Azure. È possibile avere più sottoscrizioni, collegate a una carta di credito.|
|Tenant di Azure| Istanza attendibile e dedicata di Azure AD che viene creata automaticamente quando l'organizzazione si registra a una sottoscrizione di un servizio cloud Microsoft, ad esempio Microsoft Azure, Microsoft Intune oppure Office 365. Un tenant di Azure rappresenta una singola organizzazione.|
|Tenant singolo| I tenant di Azure che accedono ad altri servizi in un ambiente dedicato vengono considerati tenant singoli.|
|Multi-tenant| I tenant di Azure che accedono ad altri servizi in un ambiente condiviso in più organizzazioni sono considerati multi-tenant.|
|Directory di Azure AD|Ogni tenant di Azure ha una directory di Azure AD dedicata e attendibile. La directory di Azure AD include gli utenti, i gruppi e le app del tenant e viene usata per eseguire funzioni di gestione delle identità e dell'accesso per le risorse del tenant.|
|Account Azure AD | Identità creata tramite Azure AD o un altro servizio cloud Microsoft, ad esempio Office 365. Le identità vengono archiviate in Azure AD e sono accessibili alle sottoscrizioni dei servizi cloud dell'organizzazione. Questo account viene a volte definito account aziendale o dell'istituto di istruzione.|
|Dominio personalizzato|Ogni nuova directory di Azure AD include un dominio iniziale, domainname.onmicrosoft.com. Oltre a tale dominio iniziale, è possibile aggiungere all'elenco i nomi di dominio dell'organizzazione, che includono i nomi usati per le attività aziendali e i nomi usati dagli utenti per accedere alle risorse dell'organizzazione. L'aggiunta di nomi di dominio personalizzati consente di creare nomi familiari per gli utenti, ad esempio alain@contoso.com.|
|Amministratore dell'account|Il ruolo Amministratore della sottoscrizione classica corrisponde concettualmente al proprietario di fatturazione di una sottoscrizione. Questo ruolo può accedere al [Centro account di Azure](https://account.azure.com/Subscriptions) e consente di gestire tutte le sottoscrizioni di un account. Per altre informazioni, vedere [Ruoli di amministratore sottoscrizione classico, di controllo degli accessi in base al ruolo di Azure e di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Amministratore del servizio|Il ruolo Amministratore della sottoscrizione classica consente di gestire tutte le risorse di Azure, incluso l'accesso. Questo ruolo ha un accesso equivalente a quello di un utente cui viene assegnato il ruolo di proprietario nell'ambito della sottoscrizione. Per altre informazioni, vedere [Ruoli di amministratore sottoscrizione classico, di controllo degli accessi in base al ruolo di Azure e di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Proprietario|Questo ruolo consente di gestire tutte le risorse di Azure, incluso l'accesso. Si tratta di un ruolo basato su un sistema di autorizzazione più recente definito controllo degli accessi in base al ruolo, che fornisce una gestione con granularità fine dell'accesso alle risorse di Azure. Per altre informazioni, vedere [Ruoli di amministratore sottoscrizione classico, di controllo degli accessi in base al ruolo di Azure e di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Amministrazione globale di Azure AD|Questo ruolo di amministratore viene assegnato automaticamente a chiunque abbia creato il tenant di Azure AD. Gli amministratori globali possono eseguire tutte le funzioni amministrative per Azure AD ed eventuali servizi federati con Azure AD, come Exchange Online, SharePoint Online e Skype for Business Online. È possibile avere più amministratori globali ma solo gli amministratori globali possono assegnare ruoli di amministratore, inclusi altri ruoli di amministratore globale, agli utenti.<br><br>**Nota**<br>Questo ruolo di amministratore è definito Amministratore globale nel portale di Azure, ma è definito **Amministratore società** nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell.<br><br>Per altre informazioni sui diversi ruoli di amministratore, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Account Microsoft|Account personali che forniscono l'accesso ai prodotti e ai servizi cloud Microsoft destinati agli utenti privati, ad esempio Outlook, OneDrive, Xbox LIVE oppure Office 365. L'account Microsoft viene creato e archiviato nel sistema di account delle identità degli utenti gestito da Microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Funzionalità disponibili in Azure AD

Dopo la scelta della licenza di Azure AD, sarà possibile accedere ad alcune o a tutte le funzionalità seguenti per l'organizzazione:

|Categoria|DESCRIZIONE|
|-------|-----------|
|Gestione delle applicazioni|Consente di gestire le app cloud e locali con Application Proxy, accesso Single Sign-On, portale App personali (definito anche pannello di accesso) e app SaaS (Software as a Service). Per altre informazioni, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](../manage-apps/application-proxy.md) e la [Documentazione della gestione delle applicazioni](../manage-apps/index.yml).|
|Authentication|Consente di gestire la reimpostazione della password self-service di Azure Active Directory, Multi-Factor Authentication, l'elenco personalizzato di password escluse e il blocco intelligente. Per altre informazioni, vedere la [Documentazione di Autenticazione di Azure AD](../authentication/index.yml).|
|Business-to-Business (B2B)|Consente di gestire gli utenti guest e i partner esterni, mantenendo al tempo stesso il controllo sui propri dati aziendali. Per altre informazioni, vedere la [Documentazione di Azure Active Directory B2B](../b2b/index.yml).|
|Business-to-Customer (B2C)|Consente di personalizzare e controllare la modalità con cui gli utenti accedono e gestiscono i propri profili quando usano le app. Per altre informazioni, vedere la [documentazione di Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Accesso condizionale|Consente di gestire l'accesso alle app cloud. Per altre informazioni, vedere la [Documentazione sull'accesso condizionale di Azure AD](../conditional-access/index.yml).|
|Azure Active Directory per sviluppatori|Consente di creare app che accedono a tutte le identità Microsoft e ottengono token per chiamare Graph, altre API Microsoft o API personalizzate. Per altre informazioni, vedere [Microsoft identity platform (Azure Active Directory per sviluppatori)](../develop/index.yml).|
|Gestione dei dispositivi|Consente di gestire il modo in cui i dispositivi cloud o locali accedono ai dati aziendali. Per altre informazioni, vedere la [Documentazione sulla gestione dei dispositivi in Azure AD](../devices/index.yml).|
|Servizi di dominio|Consentono di aggiungere macchine virtuali di Azure a un dominio senza usare controller di dominio. Per altre informazioni, vedere la [Documentazione di Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Utenti Enterprise|Consentono di gestire l'assegnazione di licenze e l'accesso alle app e di configurare i delegati usando i gruppi e i ruoli di amministratore. Per altre informazioni, vedere la [Documentazione sulla gestione degli utenti in Azure Active Directory](../users-groups-roles/index.yml).|
|Identità ibrida|Consente di usare Azure Active Directory Connect e Connect Health per fornire una singola identità utente per l'autenticazione e l'autorizzazione per tutte le risorse, indipendentemente dalla posizione (sul cloud o in locale). Per altre informazioni, vedere la [Documentazione di identità ibrida](../hybrid/index.yml).|
|Identity Governance|Consente di gestire l'identità dell'organizzazione tramite controllo di accesso per dipendenti, partner aziendali, fornitori, servizi e app. È anche possibile eseguire verifiche di accesso. Per altre informazioni, vedere la [Documentazione di Azure AD Identity Governance](../governance/identity-governance-overview.md) e [Verifiche di accesso di Azure AD](../governance/access-reviews-overview.md).|
|Identity Protection|Consente di rilevare potenziali vulnerabilità che interessano le identità dell'organizzazione, di configurare criteri per rispondere ad azioni sospette e quindi di intraprendere le azioni appropriate per risolverle. Per altre informazioni, vedere [Azure AD Identity Protection](../identity-protection/index.yml).|
|Identità gestite per le risorse di Azure|Fornisce ai servizi di Azure un'identità gestita automaticamente in Azure AD in grado di autenticare qualsiasi servizio di autenticazione supportato da Azure AD, incluso Key Vault. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](../managed-identities-azure-resources/overview.md).|
|Privileged identity management (PIM)|Consente di gestire, controllare e monitorare l'accesso all'interno dell'organizzazione. Questa funzionalità include l'accesso alle risorse di Azure AD e di Azure, oltre che ad altri Microsoft Online Services, come Office 365 o Intune. Per altre informazioni, vedere [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Report e monitoraggio|Consente di ottenere informazioni dettagliate sulla sicurezza e sui modelli di utilizzo dell'ambiente. Per altre informazioni, vedere [Report e monitoraggio di Azure Active Directory](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>Passaggi successivi

- [Effettuare l'iscrizione ad Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Associare una sottoscrizione di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Accedere ad Azure Active Directory per creare un nuovo tenant](active-directory-access-create-new-tenant.md)

- [Elenco di controllo della distribuzione delle funzionalità di Azure Active Directory Premium P2](active-directory-deployment-checklist-p2.md)
