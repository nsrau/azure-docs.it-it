---
title: Architettura di identità per Azure Stack | Microsoft Docs
description: Informazioni sull'architettura di identità che è possibile usare con Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/07/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: a16a6596d6bc33200f87a1dfd3b2ea5b02628e10
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277818"
---
# <a name="identity-architecture-for-azure-stack"></a>Architettura di identità per Azure Stack
Prima di scegliere un provider di identità da usare con Azure Stack, comprendere le differenze principali tra le opzioni di Azure Active Directory (Azure AD) e Active Directory Federation Services (ADFS). 

## <a name="capabilities-and-limitations"></a>Funzionalità e limitazioni 
Il provider di identità scelto può limitare le opzioni, tra cui il supporto per multi-tenancy. 

  

|Scenario o funzionalità        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Connesso a internet     |Sì       |Facoltativo|
|Supporto per multi-tenancy     |Sì       |No       |
|Oggetti offerta in Marketplace |Sì       |Sì. Richiede l'uso del [offline Marketplace diffusione](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) dello strumento.|
|Supporto per Active Directory Authentication Library (ADAL) |Sì |Sì|
|Supporto per gli strumenti, ad esempio di comando di Azure, Visual Studio e PowerShell  |Sì |Sì|
|Creare entità servizio tramite il portale di Azure     |Sì |No |
|Creare le entità servizio con i certificati      |Sì |Sì|
|Creare le entità servizio con i segreti (chiavi)    |Sì |No |
|Le applicazioni possono usare il servizio Graph           |Sì |No |
|Le applicazioni possono usare provider di identità per l'accesso |Sì |Sì. Le applicazioni per la federazione locale le istanze di AD FS. |

## <a name="topologies"></a>Topologie
Le sezioni seguenti illustrano le diverse topologie di identità che è possibile usare.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: topologia a tenant singolo 
Per impostazione predefinita, quando si installa Azure Stack e si usa Azure AD, Azure Stack Usa una topologia a tenant singolo. 

Una topologia a tenant singolo è utile quando:
- Tutti gli utenti fanno parte dello stesso tenant.
- Un provider di servizi ospita un'istanza di Azure Stack per un'organizzazione. 

![Topologia a tenant singolo Stack Azure con Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Questa topologia offre le caratteristiche seguenti:
- Stack di Azure registra tutte le applicazioni e servizi di Azure ad stesso tenant di directory. 
- Azure Stack autentica solo gli utenti e applicazioni da tale directory, tra cui i token. 
- Le identità per gli amministratori (operatori di cloud) e gli utenti tenant sono nello stesso tenant di directory. 
- Per consentire agli utenti da un'altra directory di accedere all'ambiente Azure Stack, è necessario [invitare l'utente come guest](azure-stack-identity-overview.md#guest-users) per la directory del tenant. 

### <a name="azure-ad-multi-tenant-topology"></a>Azure Active Directory: topologia di multi-tenant
Gli operatori cloud è possono configurare Azure Stack per consentire l'accesso alle applicazioni dal tenant da una o più organizzazioni. Gli utenti accedere alle applicazioni tramite il portale utenti. In questa configurazione, il portale di amministrazione (usato dall'operatore cloud) è limitato agli utenti in una singola directory. 

Una topologia di multi-tenant è utile quando:
- Un provider del servizio vuole consentire agli utenti più organizzazioni di accedere a Azure Stack.

![Topologia di multi-tenant Azure Stack con Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Questa topologia offre le caratteristiche seguenti:
- Accesso alle risorse deve essere su una base per ogni organizzazione. 
- Gli utenti da un'organizzazione devono essere non è possibile concedere l'accesso alle risorse per gli utenti esterni all'organizzazione. 
- Le identità per gli amministratori (operatori di cloud) possono trovarsi in un tenant di directory separate le identità per gli utenti. Questa separazione garantisce l'isolamento di account a livello del provider di identità. 
 
### <a name="ad-fs"></a>AD FS  
La topologia di AD FS è obbligatorio quando viene soddisfatta una delle condizioni seguenti:
- Azure Stack non si connette a internet.
- Azure Stack può connettersi a internet, ma si sceglie di usare ADFS per il provider di identità.
  
![Topologia dello Stack di Azure tramite AD FS](media/azure-stack-identity-architecture/adfs.png)

Questa topologia offre le caratteristiche seguenti:
- Per supportare l'uso di questa topologia nell'ambiente di produzione, è necessario integrare l'istanza di ADFS di Azure Stack predefinita con un'istanza di AD FS esistente che è supportata da Active Directory, tramite una relazione di trust federativa. 
- È possibile integrare il servizio Graph in Azure Stack con l'istanza di Active Directory esistente. È anche possibile usare il servizio API Graph basata su OData che supporta le API che siano coerenti con l'API Graph di Azure AD. 

  Per interagire con l'istanza di Active Directory, l'API Graph richiede le credenziali utente dall'istanza di Active Directory che dispongono delle autorizzazioni di sola lettura. 
  - L'istanza di AD FS incorporato è basato su Windows Server 2016. 
  - Le istanze di AD FS e Active Directory devono essere basate su Windows Server 2012 o versioni successive. 
  
  Tra l'istanza di Active Directory e l'istanza di AD FS predefinita, le interazioni non sono limitate a OpenID Connect ed è possibile usare qualsiasi protocollo supportato si escludono a vicenda. 
  - Gli account utente sono creati e gestiti nell'istanza di Active Directory locale.
  - Le entità servizio e le registrazioni per le applicazioni vengono gestite nell'istanza di Active Directory predefinito.



## <a name="next-steps"></a>Passaggi successivi
- [Panoramica dell'identità](azure-stack-identity-overview.md)   
- [Datacenter integration - identità](azure-stack-integrate-identity.md)
