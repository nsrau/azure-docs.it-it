---
title: Informazioni su Azure Active Directory (Azure AD) | Microsoft Docs
description: Usare Azure Active Directory per estendere le identità locali esistenti nel cloud o sviluppare le applicazioni integrate di Azure AD.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 01183a2c85b748d593146f6b52a7d4dbd34524cb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-active-directory"></a>Informazioni su Azure Active Directory
Azure Active Directory (Azure AD) è la directory basata sul cloud multi-tenant e il servizio di gestione delle identità di Microsoft, che combina i servizi di directory fondamentali, la gestione dell'accesso delle applicazioni e la protezione delle identità in un'unica soluzione. Azure AD offre anche una piattaforma avanzata basata sugli standard che consente agli sviluppatori di fornire il controllo di accesso alle applicazioni in base a regole e criteri centralizzati.

![Stack di Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Per gli amministratori IT.** Azure AD offre una soluzione più sicura alle organizzazioni grazie all'uso di funzionalità di gestione delle identità avanzate e all'accesso Single Sign-On (SSO) a migliaia di [app SaaS basate sul cloud](active-directory-saas-tutorial-list.md) e app locali. Tramite queste app sarà anche possibile ottenere funzionalità di sicurezza per le app basate sul cloud, accesso trasparente, collaborazione avanzata e automazione del ciclo di vita delle identità per i dipendenti, per migliorare sia la conformità che la sicurezza.

    Inoltre, bastano solo [quattro clic](./connect/active-directory-aadconnect-get-started-express.md) per integrare Azure AD con un'istanza esistente di Windows Server Active Directory, consentendo all'organizzazione di sfruttare gli investimenti per la gestione delle identità in locale esistenti anche per la gestione dell'accesso alle app SaaS basate sul cloud.

- **Per gli sviluppatori di app.** Azure AD consente di concentrarsi sullo sviluppo delle app, grazie alla possibilità di integrazione con una soluzione di gestione delle identità usata da milioni di organizzazioni in tutto il mondo.

- **Per i clienti di Office 365, Azure o Dynamics CRM Online.** Questi clienti usano già Azure AD. Ogni tenant di Office 365, Azure e Dynamics CRM Online è in effetti un tenant di Azure AD e ciò consente di iniziare immediatamente a gestire l'accesso dei dipendenti alle app cloud integrate.

## <a name="how-reliable-is-azure-ad"></a>Affidabilità di Azure AD
La progettazione multi-tenant, geograficamente distribuita e a disponibilità elevata di Azure AD offre l'affidabilità adeguata per le esigenze aziendali più importanti. Grazie all'esecuzione in 28 data center in tutto il mondo e al failover automatizzato, Azure AD offre la certezza di un'affidabilità elevata. Anche in caso di problemi in un data center, le copie dei dati delle directory saranno sempre disponibili in almeno due data center dislocati in aree geografiche diverse, consentendo l'accesso immediato.

Per altre informazioni sui contratti di servizio, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Scegliere un'edizione
Tutti i servizi aziendali di Microsoft Online si basano su Azure Active Directory (Azure AD) per le esigenze di gestione delle identità e degli accessi. Sottoscrivendo qualsiasi servizio aziendale di Microsoft Online, ad esempio Office 365 o Microsoft Azure, si ottiene Azure AD con accesso a tutte le funzionalità gratuite. Con l'edizione Free di Azure Active Directory, è possibile gestire utenti e gruppi, eseguire la sincronizzazione con le directory locali, disporre dell'accesso Single Sign-On per Azure, Office 365 e numerose applicazioni SaaS note, ad esempio Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e molte altre. 

Per migliorare Azure Active Directory, è possibile aggiungere funzionalità a pagamento mediante Azure Active Directory Basic Edition, Premium P1 Edition e Premium P2 Edition. Le edizioni a pagamento di Azure Active Directory, realizzate a partire dalla directory gratuita esistente, forniscono funzionalità aziendali avanzate che estendono alla forza lavoro mobile servizi come la gestione self-service, il monitoraggio avanzato, la creazione di report di sicurezza, Multi-Factor Authentication (MFA) e l'accesso sicuro.

> [!NOTE]
> Per le opzioni relative ai prezzi di queste edizioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Le edizioni Azure Active Directory Premium P1 e Premium P2 e Azure Active Directory Basic non sono attualmente supportate in Cina. Per altre informazioni sui prezzi di Azure AD, è possibile contattare il forum di Azure Active Directory.
>

* **Azure Active Directory Basic**: progettata per gli utenti che svolgono attività basate sul cloud, questa edizione garantisce l'accesso alle applicazioni incentrate su cloud e soluzioni di gestione delle identità self-service. L'edizione Basic di Azure Active Directory offre funzionalità per il miglioramento della produttività e la riduzione dei costi, ad esempio la gestione degli accessi basata sui gruppi, la reimpostazione delle password self-service per le applicazioni cloud e il proxy dell'applicazione di Azure Active Directory (per pubblicare applicazioni Web locali usando Azure Active Directory), supportate da un contratto di servizio a livello aziendale che garantisce un tempo di attività del 99,9%.
* **Azure Active Directory Premium P1** : progettata per le organizzazioni con esigenze più complesse di gestione delle identità e degli accessi, Azure Active Directory Premium Edition include capacità aggiuntive di gestione delle identità a livello aziendale con numerose funzionalità e consente agli utenti ibridi di accedere facilmente a funzionalità locali e cloud. Questa edizione include tutte le funzionalità necessarie per gli Information Worker e gli amministratori di identità in ambienti ibridi, tra cui accesso alle applicazioni, gestione delle identità e degli accessi self-service, protezione e sicurezza dell'identità nel cloud. Supporta risorse avanzate di amministrazione e delega come i gruppi dinamici e la gestione self-service dei gruppi. Include Microsoft Identity Manager, una suite locale di gestione delle identità e degli accessi, e offre soluzioni con funzionalità cloud di writeback come la reimpostazione self-service delle password per gli utenti locali.
* **Azure Active Directory Premium P2**: progettata con protezione avanzata per tutti gli utenti e gli amministratori, questa nuova offerta include tutte le funzionalità di Azure AD Premium P1 oltre a Identity Protection e Privileged Identity Management. Azure Active Directory Identity Protection sfrutta miliardi di segnali per fornire l'accesso condizionale basato sui rischi per le applicazioni e i dati aziendali critici. Consente anche di gestire e proteggere account privilegiati con Azure Active Directory Privileged Identity Management in modo che sia possibile individuare, limitare e monitorare gli amministratori e il relativo accesso alle risorse e fornire accesso just-in-time quando è necessario.  

> [!NOTE]
> Tramite le edizioni con pagamento a consumo sono disponibili diverse funzionalità di Azure Active Directory:
>
> * Active Directory B2C è la soluzione di gestione delle identità e degli accessi per le applicazioni rivolte ai consumatori. Per altre informazioni, vedere [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).
> * Il servizio Azure Multi-Factor Authentication può essere usato tramite provider per utente o provider per autenticazione. Per altre informazioni, vedere [Informazioni su Azure Multi-Factor Authentication](authentication/multi-factor-authentication.md).
>

## <a name="how-can-i-get-started"></a>Operazioni iniziali

**Amministratori IT:**

* [provarlo,](https://azure.microsoft.com/trial/get-started-active-directory/) : è possibile ottenere subito gratuitamente una versione di valutazione di 30 giorni e distribuire la prima soluzione cloud in meno di cinque minuti, usando il collegamento seguente

* Per suggerimenti e consigli su come rendere rapidamente operativo un tenant di Azure AD, vedere l'[introduzione ad Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium)

**Sviluppatori:**
 
* Vedere la [Guida per gli sviluppatori](active-directory-developers-guide.md) per Azure Active Directory

* [Iniziare a usare una versione di valutazione](https://azure.microsoft.com/trial/get-started-active-directory/): è possibile ottenere subito gratuitamente una versione di valutazione di 30 giorni e iniziare a integrare la propria app con Azure AD

## <a name="next-steps"></a>Passaggi successivi
[Altre nozioni di base sulla gestione delle identità e degli accessi di Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
