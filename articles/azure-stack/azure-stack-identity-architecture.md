---
title: "Architettura di identità per lo Stack di Azure | Documenti Microsoft"
description: "Informazioni sull'architettura di identità che è possibile utilizzare con lo Stack di Azure."
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
ms.date: 2/28/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 899e0fc0c1eb93d68c79c92c9cc042462ebc2fef
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Architettura di identità per lo Stack di Azure
Prima di scegliere un provider di identità da usare con lo Stack di Azure, comprendere le differenze principali tra le opzioni di Azure Active Directory (Azure AD) e Active Directory Federation Services (ADFS). 

## <a name="capabilities-and-limitations"></a>Funzionalità e limitazioni 
Il provider di identità che si sceglie può limitare le opzioni, incluso il supporto per multi-tenancy. 

  

|Uno scenario o funzionalità        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Connesso a internet     |Sì       |Facoltativo|
|Supporto per multi-tenancy     |Sì       |No       |
|Diffusione di Marketplace       |Sì       |Sì. Richiede l'uso del [offline Marketplace diffusione](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) dello strumento.|
|Supporto per Active Directory Authentication Library (ADAL) |Sì |Sì|
|Supporto per strumenti quali CLI di Azure, Visual Studio e PowerShell  |Sì |Sì|
|Creare le entità servizio tramite il portale di Azure     |Sì |No |
|Creare le entità servizio con i certificati      |Sì |Sì|
|Creare le entità servizio con segreti (chiavi)    |Sì |No |
|Le applicazioni possono utilizzare il servizio di Graph           |Sì |No |
|Le applicazioni possono utilizzare i provider di identità per l'accesso |Sì |Sì. Richiede le applicazioni per la federazione con locale le istanze di AD FS. |

## <a name="topologies"></a>Topologie
Le sezioni seguenti disco diverse topologie di identità che è possibile utilizzare.

### <a name="azure-ad-single-tenant-topology"></a>Azure Active Directory: topologia di single-tenant 
Per impostazione predefinita, quando si installa Azure Stack e utilizzare Azure AD, Azure Stack utilizza una topologia single-tenant. 

Una topologia single-tenant è utile quando:
- Tutti gli utenti fanno parte dello stesso tenant.
- Un provider del servizio include un'istanza di Azure Stack per un'organizzazione. 

![Topologia di single-tenant Stack Azure con Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Questa topologia offre le caratteristiche seguenti:
- Azure Stack registra tutte le applicazioni e servizi ad Azure stesso tenant di directory. 
- Stack Azure autentica solo gli utenti e applicazioni da tale directory, tra cui i token. 
- Le identità per gli amministratori (agli operatori cloud) e gli utenti tenant sono dello stesso tenant di directory. 
- Per abilitare un utente da un'altra directory di accedere a questo ambiente dello Stack di Azure, è necessario [invita l'utente come guest](azure-stack-identity-overview.md#guest-users) nella directory del tenant. 

### <a name="azure-ad-multi-tenant-topology"></a>Azure Active Directory: topologia di multi-tenant
Gli operatori di cloud è possono configurare dello Stack di Azure per consentire l'accesso alle applicazioni dal tenant da uno o più organizzazioni. Gli utenti di accedere alle applicazioni tramite il portale per gli utenti. In questa configurazione, il portale di amministrazione (usato dall'operatore cloud) è limitato agli utenti di una singola directory. 

Una topologia di multi-tenant è utile quando:
- Un provider di servizi desidera consentire agli utenti di accedere allo Stack di Azure più organizzazioni.

![Topologia di multi-tenant Stack Azure con Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Questa topologia offre le caratteristiche seguenti:
- Accesso alle risorse deve essere su una base per ogni organizzazione. 
- Gli utenti da un'organizzazione devono essere non è possibile concedere l'accesso alle risorse agli utenti che sono all'esterno dell'organizzazione. 
- Le identità per gli amministratori (agli operatori cloud) possono trovarsi in un tenant di directory distinto dalle identità per gli utenti. Questa separazione garantisce l'isolamento di account a livello del provider di identità. 
 
### <a name="ad-fs"></a>AD FS  
La topologia di AD FS è obbligatorio quando viene soddisfatta una delle condizioni seguenti:
- Stack di Azure non si connette a internet.
- Stack di Azure può connettersi a internet, ma si desidera utilizzare ADFS per il provider di identità.
  
![Azure topologia Stack con ADFS](media/azure-stack-identity-architecture/adfs.png)

Questa topologia offre le caratteristiche seguenti:
- Per supportare l'utilizzo di questa topologia nell'ambiente di produzione, è necessario integrare l'istanza dello Stack di Azure AD FS predefinita con un'istanza di ADFS esistente che è supportata da Active Directory, tramite una relazione di trust federativa. 
- È possibile integrare il servizio di Graph nello Stack di Azure con l'istanza di Active Directory esistente. È inoltre possibile utilizzare il servizio basato su OData API Graph che supporta le API che siano coerenti con l'API Graph di Azure AD. 

  Per interagire con l'istanza di Active Directory, l'API Graph sono necessarie credenziali utente dall'istanza di Active Directory che dispongono delle autorizzazioni di sola lettura. 
  - L'istanza di AD FS incorporato è basato su Windows Server 2016. 
  - Le istanze di AD FS e Active Directory devono essere basate su Windows Server 2012 o versione successiva. 
  
  Tra l'istanza di Active Directory e l'istanza di AD FS predefinita, le interazioni non sono limitate a OpenID Connect e usano un protocollo supportato si escludono a vicenda. 
  - Gli account utente vengono creati e gestiti nell'istanza di Active Directory locale.
  - Le entità servizio e le registrazioni per le applicazioni vengono gestite nell'istanza di Active Directory predefinito.



## <a name="next-steps"></a>Passaggi successivi
- [Panoramica dell'identità](azure-stack-identity-overview.md)   
- [Integrazione con Data Center - identità](azure-stack-integrate-identity.md)
