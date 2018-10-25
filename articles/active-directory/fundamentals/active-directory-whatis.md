---
title: Informazioni su Azure Active Directory (Azure AD) | Microsoft Docs
description: Informazioni su come usare Azure Active Directory per estendere le identità locali esistenti nel cloud o sviluppare app integrate di Azure AD.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 406baeac60c7c0cdf5f74876e5fc29ea23d3d6f6
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957548"
---
# <a name="what-is-azure-active-directory"></a>Informazioni su Azure Active Directory
Azure Active Directory (Azure AD) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Azure Active Directory combina servizi directory di base, gestione dell'accesso delle applicazioni e protezione delle identità in un'unica soluzione, che offre una piattaforma basata su standard che consente agli sviluppatori di implementare il controllo di accesso per le app in base a regole e criteri centralizzati.

![Stack di Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Vantaggi di Azure AD
Azure AD consente di:

-   Creare e gestire un'identità unica per ogni utente in tutta l'azienda, mantenendo utenti, gruppi e dispositivi sincronizzati con [Azure AD Connect](../connect/active-directory-aadconnect.md).

-   Offrire l'accesso Single Sign-On alle app, incluse migliaia di app SaaS preintegrate e l'accesso remoto più sicuro alle applicazioni SaaS locali tramite [Azure Active Directory Application Proxy](../manage-apps/application-proxy.md).

-   Abilitare la sicurezza dell'accesso alle applicazioni applicando criteri di [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) basati su regole sia per le app locali che per quelle nel cloud.

-   Migliorare la produttività degli utenti con la [reimpostazione delle password self-service](../user-help/user-help-reset-password.md) e il [portale delle app personali](../user-help/active-directory-saas-access-panel-introduction.md) per le richieste di accesso a gruppi e applicazioni.

-   Sfruttare [la disponibilità elevata e l'affidabilità](https://docs.microsoft.com/azure/architecture/checklist/availability) di una soluzione internazionale di gestione degli accessi e delle identità basata sul cloud di livello aziendale.

## <a name="who-uses-azure-ad"></a>Chi usa Azure AD
Azure AD è destinato agli amministratori IT, agli sviluppatori di app e agli utenti di Office 365, Azure o Dynamics CRM Online.

- **Amministratori IT.** Azure AD offre una soluzione più sicura alle organizzazioni grazie all'uso di funzionalità di gestione delle identità avanzate e all'accesso Single Sign-On (SSO) a migliaia di [app SaaS basate sul cloud](../saas-apps/tutorial-list.md) e app locali. Tramite queste app sarà anche possibile ottenere funzionalità di sicurezza per le app basate sul cloud, accesso trasparente, collaborazione avanzata e automazione del ciclo di vita delle identità per gli utenti, per migliorare sia la conformità che la sicurezza.

    Inoltre, con [Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md) è possibile integrare Azure AD con un'istanza esistente di Windows Server Active Directory, consentendo all'organizzazione di sfruttare gli investimenti per la gestione delle identità in locale esistenti anche per la gestione dell'accesso alle app SaaS basate sul cloud.

- **Per gli sviluppatori di app.** Azure AD consente di concentrarsi sullo sviluppo delle app, grazie alla possibilità di integrazione con una soluzione di gestione delle identità usata da milioni di organizzazioni in tutto il mondo.

- **Per i clienti di Office 365, Azure o Dynamics CRM Online.** Questi clienti usano già Azure AD. Ogni tenant di Office 365, Azure e Dynamics CRM Online è in effetti un tenant di Azure AD e ciò consente di iniziare immediatamente a gestire l'accesso degli utenti alle app cloud integrate.

## <a name="how-reliable-is-azure-ad"></a>Affidabilità di Azure AD
La progettazione multi-tenant, geograficamente distribuita e a disponibilità elevata di Azure AD offre l'affidabilità adeguata per le esigenze aziendali più importanti. Azure AD viene eseguito in 28 data center in tutto il mondo con failover automatico. Questo significa che anche se un data center diventa non disponibile, le copie dei dati di directory sono disponibili per l'accesso immediato in almeno altri due data center dislocati nell'area.

Per altre informazioni sui contratti di servizio, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Scegliere un'edizione
Tutti i servizi aziendali di Microsoft Online si basano su Azure AD per le esigenze di gestione delle identità e degli accessi. Sottoscrivendo qualsiasi servizio aziendale di Microsoft Online, ad esempio Office 365 o Microsoft Azure, si ottiene automaticamente Azure AD con accesso a tutte le funzionalità gratuite. Con l'edizione Azure Active Directory Free, è possibile gestire utenti e gruppi, eseguire la sincronizzazione con le directory locali, disporre dell'accesso Single Sign-On per Azure, Office 365 e migliaia di app SaaS note, ad esempio Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e molte altre. 

Per migliorare l'implementazione di Azure AD, è anche possibile aggiungere funzionalità a pagamento effettuando l'aggiornamento ad Azure Active Directory Basic Edition, Premium P1 Edition o Premium P2 Edition. Le edizioni a pagamento di Azure AD, realizzate a partire dalla directory gratuita esistente, forniscono funzionalità aziendali avanzate che estendono alla forza lavoro mobile servizi come la gestione self-service, il monitoraggio avanzato, la creazione di report di sicurezza, Multi-Factor Authentication (MFA) e l'accesso sicuro.

> [!NOTE]
> Per le opzioni relative ai prezzi di queste edizioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Le edizioni Azure Active Directory Premium P1 e Premium P2 e Azure Active Directory Basic non sono attualmente supportate in Cina. Per altre informazioni sui prezzi di Azure AD, è possibile contattare il forum di Azure Active Directory.

- **Azure Active Directory Basic.** Destinata ai dipendenti operativi con esigenze prevalentemente di tipo cloud, questa edizione fornisce soluzioni di gestione delle identità self-service e dell'accesso ad applicazioni incentrate sul cloud. L'edizione Basic offre funzionalità per il miglioramento della produttività e la riduzione dei costi, ad esempio la gestione degli accessi basata sui gruppi, la reimpostazione delle password self-service per le app cloud e Azure Active Directory Application Proxy (per pubblicare app Web locali usando Azure AD), tutte supportate da un contratto di servizio aziendale che garantisce un tempo di attività del 99,9%.

- **Azure Active Directory Premium P1.** Progettata per offrire alle organizzazioni strumenti in grado di soddisfare esigenze più complesse di gestione delle identità e degli accessi, l'edizione Azure Active Directory Premium include funzionalità di gestione delle identità di livello aziendale ricche di opzioni e consente agli utenti di ambienti ibridi di accedere facilmente a funzionalità locali e cloud. Questa edizione include tutte le funzionalità necessarie per gli Information Worker e gli amministratori di identità in ambienti ibridi, tra cui accesso alle applicazioni, gestione delle identità e degli accessi self-service, protezione e sicurezza dell'identità nel cloud. Supporta risorse avanzate di amministrazione e delega come i gruppi dinamici e la gestione self-service dei gruppi. Include Microsoft Identity Manager, una suite locale di gestione delle identità e degli accessi, e offre soluzioni con funzionalità cloud di writeback come la reimpostazione self-service delle password per gli utenti locali.

- **Azure Active Directory Premium P2.** Progettata prima di tutto per offrire protezione avanzata a utenti e amministratori, questa nuova offerta include tutte le funzionalità di Azure AD Premium P1 oltre a Identity Protection e Privileged Identity Management. Azure Active Directory Identity Protection sfrutta miliardi di segnali per fornire l'accesso condizionale basato sui rischi per le app e i dati aziendali critici. Consente anche di gestire e proteggere account privilegiati con Azure Active Directory Privileged Identity Management in modo che sia possibile individuare, limitare e monitorare gli amministratori e il relativo accesso alle risorse e fornire accesso just-in-time quando è necessario.  

> [!NOTE]
> Tramite le edizioni con pagamento a consumo sono disponibili anche diverse funzionalità di Azure Active Directory:<ul><li>**Azure Active Directory B2C.** Soluzione di gestione delle identità e degli accessi per le app rivolte ai consumatori. Per altre informazioni, vedere [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).</li><li>**Azure Multi-Factor Authentication.** Uso del provider per utente o per autenticazione. Per altre informazioni, vedere [Informazioni su Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md).

## <a name="as-an-admin-how-do-i-get-started"></a>Come può iniziare un amministratore?
Iscriversi a una versione di valutazione gratuita di 30 giorni e distribuire la prima soluzione cloud. Vedere [Versione di valutazione di Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="as-a-developer-how-do-i-get-started"></a>Come può iniziare uno sviluppatore?
Iscriversi a una versione di valutazione gratuita di 30 giorni e iniziare a integrare le app con Azure AD. Vedere [Versione di valutazione di Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Per altre informazioni, è anche possibile vedere la [Guida per gli sviluppatori](../develop/v1-overview.md) per Azure Active Directory.

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni sui concetti fondamentali della gestione delle identità e degli accessi di Azure](identity-fundamentals.md).

- [Integrare Azure AD con Windows Server Active Directory](../hybrid/how-to-connect-install-express.md).
