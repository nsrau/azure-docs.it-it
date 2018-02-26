---
title: "Architettura di identità per lo Stack di Azure | Documenti Microsoft"
description: "Informazioni sull'architettura di identità, che è possibile utilizzare con lo Stack di Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 0f3e28f7726afab02211902b5ba2e478ae8065df
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Architettura di identità per lo Stack di Azure
Prima di scegliere un provider di identità da usare con lo Stack di Azure, comprendere le differenze importanti tra le opzioni di Azure Active Directory (Azure AD) e Active Directory Federated Services (ADFS). 

## <a name="capabilities-and-limitations"></a>Funzionalità e limitazioni 
Il provider di identità desiderato è possibile limitare le opzioni, incluso il supporto per multi-tenancy. 

  

|Uno scenario o funzionalità        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Connesso a internet     |Sì       |Facoltativo|
|Supporto per multi-tenancy     |Sì       |No       |
|Diffusione di Marketplace       |Sì       |Sì - richiede l'uso del [offline diffusione Marketplace](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) strumento.|
|Supporto per Active Directory Authentication Library (ADAL) |Sì |Sì|
|Supporto per strumenti come Azure interfaccia della riga di comando (CLI), Visual Studio (VS) e PowerShell  |Sì |Sì|
|Creare le entità servizio tramite il portale     |Sì |No |
|Creare le entità servizio con i certificati      |Sì |Sì|
|Creare le entità servizio con segreti (chiavi)    |Sì |No |
|Le applicazioni possono utilizzare il servizio di Graph           |Sì |No |
|Le applicazioni possono utilizzare i provider di identità per l'accesso |Sì |Sì - richiede applicazioni per la federazione con locale ADFS. |

## <a name="topologies"></a>Topologie
Le sezioni seguenti forniscono informazioni sulle topologie di identità, che è possibile utilizzare.

### <a name="azure-ad--single-tenant"></a>Azure Active Directory-single-tenant 
Per impostazione predefinita, quando si installa Azure Stack e utilizzare Azure AD, Azure Stack utilizza una topologia single-tenant. 

Una topologia single-tenant è utile quando:
- Tutti gli utenti fanno parte dello stesso tenant.
- Un provider del servizio include un'istanza di Azure Stack per un'organizzazione.  

![Utilizzo di una topologia single-tenant con Azure AD Azure topologia di Stack](media/azure-stack-identity-architecture/single-tenant.png)

Con questa topologia:
- Azure Stack registra tutte le applicazioni e servizi ad Azure stesso tenant di directory. 
- Azure Stack autentica solo gli utenti e applicazioni da tale directory, tra cui i token. 
- Le identità per gli amministratori (agli operatori cloud) e gli utenti tenant sono dello stesso tenant di directory. 
- Per abilitare un utente da un'altra directory di accedere a questo ambiente dello Stack di Azure, è necessario [invita l'utente come guest](azure-stack-identity-overview.md#guest-users) nella directory del tenant.  

### <a name="azure-ad--multi-tenant"></a>Azure Active Directory-multi-tenant
Gli operatori di cloud è possono configurare dello Stack di Azure per consentire l'accesso alle applicazioni dal tenant da uno o più organizzazioni. Gli utenti di accedere alle applicazioni tramite il portale per gli utenti. In questa configurazione, il portale di amministrazione (usato dall'operatore cloud) è limitato agli utenti di una singola directory. 

Una topologia di multi-tenant è utile quando:
- Un provider di servizi desidera consentire agli utenti di accedere allo Stack di Azure più organizzazioni.

![Utilizzo di una topologia di multi-tenant con Azure AD Azure topologia di Stack](media/azure-stack-identity-architecture/multi-tenant.png)

Con questa topologia:
- Accesso alle risorse deve essere su una base per ogni organizzazione. 
- Gli utenti da un'organizzazione non devono essere in grado di concedere l'accesso alle risorse per gli utenti esterni all'organizzazione.  
- Le identità per gli amministratori (agli operatori cloud) possono trovarsi in un tenant di directory distinto rispetto alle identità per gli utenti. Questa separazione garantisce l'isolamento di account a livello del provider di identità. 
 
### <a name="ad-fs"></a>AD FS  
La topologia di AD FS è obbligatorio quando viene soddisfatta una delle condizioni seguenti:
- Stack di Azure non si connette a Internet.
- Stack di Azure può connettersi a Internet, ma si desidera utilizzare ADFS per il provider di identità.
  
![Azure topologia Stack con ADFS](media/azure-stack-identity-architecture/adfs.png)

Con questa topologia:
- Per supportare l'utilizzo nell'ambiente di produzione, è necessario integrare l'istanza dello Stack di Azure AD FS predefinita con un'istanza esistente di AD FS supportata da Active Directory (AD), tramite una relazione di trust federativa. 
- È possibile integrare il servizio grafico nello Stack di Azure con Active Directory esistente.  È inoltre possibile utilizzare OData basato su servizio API Graph che supporta le API che siano coerenti con l'API Graph di Azure AD.  

  Per interagire con Active Directory, l'API Graph richiede credenziali utente da Active Directory che dispone dell'autorizzazione di sola lettura di Active Directory. 
  - ADFS incorporato è basato su Server 2016. 
  - ADFS e il deve essere basata su Server 2012 o versioni precedenti. 
  
  Tra Active Directory e ADFS predefinite, interazioni non sono limitate a OpenID Connect e possono utilizzare qualsiasi protocollo reciprocamente supportato.  
  - Gli account utente vengono creati e gestiti in locale Active Directory.
  - Le entità servizio e le registrazioni per le applicazioni vengono gestite in Active Directory predefinito.



## <a name="next-steps"></a>Passaggi successivi
- [Panoramica dell'identità](azure-stack-identity-overview.md)   
- [Integrazione di Data Center - identità](azure-stack-integrate-identity.md)