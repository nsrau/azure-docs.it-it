---
title: Informazioni su Azure Active Directory
description: "Usare Azure Active Directory per estendere le identità locali esistenti nel cloud o sviluppare le applicazioni integrate di Azure AD."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: jeffgilb
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.custom: it-pro
ms.openlocfilehash: e5eafd4d25d2638ab5d9f904a7e0c00b36501d68
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="what-is-azure-active-directory"></a>Informazioni su Azure Active Directory
Azure Active Directory (Azure AD) è il servizio Microsoft di gestione di identità e directory basato sul cloud e multi-tenant. Azure AD comprende servizi directory core, amministrazione avanzata delle identità e gestione dell'accesso alle applicazioni. Azure AD offre anche una piattaforma avanzata basata sugli standard che consente agli sviluppatori di fornire il controllo di accesso alle applicazioni in base a regole e criteri centralizzati. 

Azure AD offre agli amministratori IT una soluzione conveniente e facile da usare per fornire a dipendenti e partner commerciali l'accesso Single Sign-On (SSO) a [migliaia di applicazioni SaaS cloud](active-directory-saas-tutorial-list.md) , quali Office365, Salesforce.com, DropBox e Concur.

Azure AD permette agli sviluppatori di applicazioni di concentrarsi sulla creazione dell'applicazione, semplificando e velocizzando l'integrazione con una soluzione di qualità elevata per la gestione delle identità, usata da milioni di organizzazioni in tutto il mondo.

Azure AD include anche un insieme completo di funzionalità per la gestione delle identità, tra cui l'autenticazione a più fattori, la registrazione dei dispositivi, la gestione self-service delle password e dei gruppi, la gestione degli account con privilegi, il controllo degli accessi in base al ruolo, il monitoraggio dell'utilizzo dell'applicazione, il controllo avanzato e il monitoraggio e avvisi relativi alla sicurezza. Queste funzionalità possono contribuire alla protezione di applicazioni basate sul cloud, alla semplificazione dei processi IT, alla riduzione dei costi e alla soddisfazione degli obiettivi aziendali relativi alla conformità.

Con soli [quattro clic](./connect/active-directory-aadconnect-get-started-express.md), inoltre, è possibile integrare Azure AD con un'istanza esistente di Windows Server Active Directory, permettendo alle organizzazioni di sfruttare gli investimenti locali esistenti a livello di gestione delle identità per gestire l'accesso alle applicazioni SaaS basate sul cloud.

Se si è clienti di Office 365, Azure o Dynamics CRM Online, è possibile che si stia già usando Azure AD, senza esserne consapevoli. Ogni tenant di Office 365, Azure e Dynamics CRM è in effetti già un tenant di Azure AD. Quando lo si desidera, è possibile iniziare a usare tale tenant per gestire l'accesso a migliaia di altre applicazioni cloud integrate con Azure AD.

![Stack di Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Affidabilità di Azure AD
La progettazione multi-tenant, geograficamente distribuita e a disponibilità elevata di Azure AD offre l'affidabilità adeguata per le esigenze aziendali più importanti. Grazie all'esecuzione in 28 data center in tutto il mondo e al failover automatizzato, Azure AD offre la certezza di un'affidabilità elevata. Anche in caso di problemi in un data center, le copie dei dati delle directory saranno sempre disponibili in almeno due data center dislocati in aree geografiche diverse, consentendo l'accesso immediato.

Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Scegliere un'edizione
Tutti i servizi aziendali di Microsoft Online si basano su Azure Active Directory (Azure AD) per le esigenze di gestione delle identità e degli accessi. Sottoscrivendo qualsiasi servizio aziendale di Microsoft Online, ad esempio Office 365 o Microsoft Azure, si ottiene Azure AD con accesso a tutte le funzionalità gratuite. Con l'edizione Free di Azure Active Directory, è possibile gestire utenti e gruppi, eseguire la sincronizzazione con le directory locali, disporre dell'accesso Single Sign-On per Azure, Office 365 e numerose applicazioni SaaS note, ad esempio Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e molte altre. 

Per migliorare Azure Active Directory, è possibile aggiungere funzionalità a pagamento mediante Azure Active Directory Basic Edition, Premium P1 Edition e Premium P2 Edition. Le edizioni a pagamento di Azure Active Directory, realizzate a partire dalla directory gratuita esistente, forniscono funzionalità aziendali avanzate che estendono alla forza lavoro mobile servizi come la gestione self-service, il monitoraggio avanzato, la creazione di report di sicurezza, Multi-Factor Authentication (MFA) e l'accesso sicuro.

> [!NOTE]
> Per le opzioni relative ai prezzi di queste edizioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Le edizioni Azure Active Directory Premium P1 e Premium P2 e Azure Active Directory Basic non sono attualmente supportate in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il forum di Azure Active Directory.
>

* **Azure Active Directory Basic** : progettata per gli utenti che svolgono attività basate sul cloud, questa edizione garantisce l'accesso alle applicazioni incentrate su cloud e soluzioni di gestione delle identità self-service. L'edizione Basic di Azure Active Directory offre funzionalità per il miglioramento della produttività e la riduzione dei costi, ad esempio la gestione degli accessi basata sui gruppi, la reimpostazione delle password self-service per le applicazioni cloud e il proxy dell'applicazione di Azure Active Directory (per pubblicare applicazioni Web locali usando Azure Active Directory), supportate da un contratto di servizio a livello aziendale che garantisce un tempo di attività del 99,9%.
* **Azure Active Directory Premium P1** : progettata per le organizzazioni con esigenze più complesse di gestione delle identità e degli accessi, Azure Active Directory Premium Edition include capacità aggiuntive di gestione delle identità a livello aziendale con numerose funzionalità e consente agli utenti ibridi di accedere facilmente a funzionalità locali e cloud. Questa edizione include tutte le funzionalità necessarie per gli Information Worker e gli amministratori di identità in ambienti ibridi, tra cui accesso alle applicazioni, gestione delle identità e degli accessi self-service, protezione e sicurezza dell'identità nel cloud. Supporta risorse avanzate di amministrazione e delega come i gruppi dinamici e la gestione self-service dei gruppi. Include Microsoft Identity Manager, una suite locale di gestione delle identità e degli accessi, e offre soluzioni con funzionalità cloud di writeback come la reimpostazione self-service delle password per gli utenti locali.
* **Azure Active Directory Premium P2** : progettata con protezione avanzata per tutti gli utenti e gli amministratori, questa nuova offerta include tutte le funzionalità di Azure AD Premium P1 e le nuove Identity Protection e Privileged Identity Management. Azure Active Directory Identity Protection sfrutta miliardi di segnali per fornire l'accesso condizionale basato sui rischi per le applicazioni e i dati aziendali critici. Consente anche di gestire e proteggere account privilegiati con Azure Active Directory Privileged Identity Management in modo che sia possibile individuare, limitare e monitorare gli amministratori e il relativo accesso alle risorse e fornire accesso just-in-time quando è necessario.  

> [!NOTE]
> Tramite le edizioni con pagamento a consumo sono disponibili diverse funzionalità di Azure Active Directory:
>
> * Active Directory B2C è la soluzione di gestione delle identità e degli accessi per le applicazioni rivolte ai consumatori. Per altre informazioni, vedere la pagina relativa ad [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Il servizio Azure Multi-Factor Authentication può essere usato tramite provider per utente o provider per autenticazione. Per altre informazioni, vedere la pagina relativa ad [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Operazioni iniziali

**Amministratori IT:**

* [provarlo,](https://azure.microsoft.com/trial/get-started-active-directory/) : è possibile ottenere subito gratuitamente una versione di valutazione di 30 giorni e distribuire la prima soluzione cloud in meno di 5 minuti, usando il collegamento seguente

* Per suggerimenti e consigli su come rendere rapidamente operativo un tenant di Azure AD, vedere l'[introduzione ad Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium)

**Sviluppatori:**
 
* Vedere la [Guida per gli sviluppatori](active-directory-developers-guide.md) per Azure Active Directory

* [Iniziare a usare una versione di valutazione](https://azure.microsoft.com/trial/get-started-active-directory/): è possibile ottenere subito gratuitamente una versione di valutazione di 30 giorni e iniziare a integrare la propria app con Azure AD.

## <a name="next-steps"></a>Passaggi successivi
[Altre nozioni di base sulla gestione delle identità e degli accessi di Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
