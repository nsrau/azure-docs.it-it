---
title: Quali sono le dipendenze dei servizi nell'accesso condizionale di Azure Active Directory? | Microsoft Docs
description: Informazioni su come vengono usate le condizioni nell'accesso condizionale di Azure Active Directory per attivare un criterio.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f727fc7133ebc9ee124e63253e8a266862b0d908
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60354359"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Quali sono le dipendenze dei servizi nell'accesso condizionale di Azure Active Directory? 


Con i criteri di accesso condizionale, è possibile specificare i requisiti di accesso per siti Web e servizi. Ad esempio, i requisiti di accesso possono includere che richiedono l'autenticazione a più fattori (MFA) o [i dispositivi gestiti](require-managed-devices.md). 


Quando si accede direttamente un sito o un servizio, è in genere semplice valutare l'impatto di un criterio correlato. Ad esempio, se si dispone di un criterio che richiede l'autenticazione a più fattori per SharePoint Online configurati, autenticazione a più fattori viene applicata per ogni Accedi al portale web di SharePoint. Tuttavia, non è sempre semplice per valutare l'impatto di un criterio perché sono presenti App cloud con dipendenze da altre App cloud. Ad esempio, Microsoft Teams si basa su SharePoint online. Pertanto, quando si accede a Microsoft Teams in questo scenario corrente, si è soggetti anche criteri MFA di SharePoint.   


## <a name="policy-enforcement"></a>Imposizione dei criteri 

Se si dispone di una dipendenza del servizio configurata, i criteri possono essere applicati tramite imposizione ad associazione anticipata oppure con associazione tardiva. 

**Imposizione dei criteri di associazione anticipata** significa che un utente deve soddisfare i criteri di servizio dipendenti prima di accedere all'app chiamante. Ad esempio, un utente deve soddisfare i criteri di SharePoint prima di accedere al MS Teams. 

**Imposizione dei criteri di associazione tardiva** si verifica dopo che l'utente acceda all'App chiamante. Imposizione viene rinviato ai quando si chiamano le richieste di app, un token per il servizio downstream. Gli esempi includono l'accesso a Microsoft Planner e l'accesso a SharePoint Office.com MS Teams. 

Il diagramma seguente illustra le dipendenze dei servizi MS Teams. Le frecce continue indicano ad associazione anticipata imposizione la freccia tratteggiata per Planner indica imposizione con associazione tardiva. 



![Dipendenze dei servizi MS Teams](./media/service-dependencies/01.png)



  

Come procedura consigliata, è consigliabile impostare i criteri comuni tra le app correlate e i servizi laddove possibile. Presenza di una condizione di sicurezza coerente offre la migliore esperienza utente. Ad esempio, l'impostazione di criteri comuni in Exchange Online, SharePoint Online, MS Teams e Skype per business riduce notevolmente richieste impreviste che potrebbero emergere dai diversi criteri applicati ai servizi downstream. 

La tabella seguente sono elencate le dipendenze del servizio aggiuntive, in cui devono soddisfare le app client  

| App client         | Servizio downstream                          | rinforzo |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gestione di Microsoft Azure (portale e API) | Associazione anticipata |
| Classroom Microsoft | Exchange                                    | Associazione anticipata |
|                     | SharePoint                                  | Associazione anticipata  |
| Microsoft Teams     | Exchange                                    | Associazione anticipata |
|                     | Microsoft Planner MS                                  | Associazione tardiva  |
|                     | SharePoint                                  | Associazione anticipata |
|                     | Skype for Business Online                   | Associazione anticipata |
| Portale di Office       | Exchange                                    | Associazione tardiva  |
|                     | SharePoint                                  | Associazione tardiva  |
| Gruppi di Outlook      | Exchange                                    | Associazione anticipata |
|                     | SharePoint                                  | Associazione anticipata |
| PowerApps           | Gestione di Microsoft Azure (portale e API) | Associazione anticipata |
|                     | Microsoft Azure Active Directory              | Associazione anticipata |
| Project             | Dynamics CRM                                | Associazione anticipata |
| Skype for Business Online  | Exchange                                    | Associazione anticipata |
| Visual Studio       | Gestione di Microsoft Azure (portale e API) | Associazione anticipata |



## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come implementare l'accesso condizionale nell'ambiente in uso, vedere [Pianificare la distribuzione dell'accesso condizionale in Azure Active Directory](plan-conditional-access.md).
