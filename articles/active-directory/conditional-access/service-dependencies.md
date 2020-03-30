---
title: Dipendenze del servizio di accesso condizionale - Azure Active DirectoryConditional Access service dependencies - Azure Active Directory
description: Informazioni sull'uso delle condizioni in Azure Active Directory Conditional Access per attivare un criterio.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380020"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Che cosa sono le dipendenze del servizio in Azure Active Directory Conditional Access? 

Con i criteri di accesso condizionale è possibile specificare i requisiti di accesso a siti Web e servizi. Ad esempio, i requisiti di accesso possono includere la richiesta di autenticazione a più fattori (MFA) o [dispositivi gestiti](require-managed-devices.md). 

Quando si accede direttamente a un sito o a un servizio, l'impatto di criteri correlati è in genere semplice da valutare. Ad esempio, se si dispone di un criterio che richiede l'autenticazione a più fattori per SharePoint Online configurato, l'autenticazione a più fattori viene applicata per ogni accesso al portale Web di SharePoint. Tuttavia, non è sempre semplice valutare l'impatto di un criterio perché sono presenti app cloud con dipendenze da altre app cloud. Ad esempio, Microsoft Teams può fornire l'accesso alle risorse in SharePoint Online.For example, Microsoft Teams can provide access to resources in SharePoint Online. Pertanto, quando si accede a Microsoft Teams nello scenario corrente, si è anche soggetti ai criteri di autenticazione a più fattori di SharePoint.   

## <a name="policy-enforcement"></a>Imposizione dei criteri 

Se si dispone di una dipendenza del servizio configurata, i criteri possono essere applicati utilizzando l'imposizione con associazione anticipata o ad associazione tardiva. 

- **L'applicazione anticipata dei criteri** significa che un utente deve soddisfare i criteri del servizio dipendente prima di accedere all'app chiamante. Ad esempio, un utente deve soddisfare i criteri di SharePoint prima di accedere a MS Teams. 
- **L'applicazione dei criteri ad associazione tardiva** si verifica dopo che l'utente accede all'app chiamante. L'imposizione viene posticipata quando si chiamano le richieste dell'app, un token per il servizio downstream. Gli esempi includono MS Teams che accedono a Planner e Office.com l'accesso a SharePoint. 

Il diagramma seguente illustra le dipendenze del servizio MS Teams. Le frecce continue indicano l'imposizione con associazione anticipata della freccia tratteggiata per Planner indica l'imposizione ad associazione tardiva. 

![Dipendenze del servizio MS Teams](./media/service-dependencies/01.png)

Come procedura consigliata, è consigliabile impostare criteri comuni tra le app e i servizi correlati quando possibile. Avere una posizione di sicurezza coerente offre la migliore esperienza utente. Ad esempio, l'impostazione di un criterio comune in Exchange Online, SharePoint Online, Microsoft Teams e Skype for Business riduce significativamente i prompt imprevisti che possono derivare da criteri diversi applicati ai servizi downstream. 

La tabella seguente elenca le dipendenze del servizio aggiuntive, in cui le app client devono  

| App client         | Servizio downstream                          | Imposizione |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gestione di Microsoft Azure (portale e API) | Associazione anticipata |
| Classe Microsoft | Exchange                                    | Associazione anticipata |
|                     | SharePoint                                  | Associazione anticipata |
| Microsoft Teams     | Exchange                                    | Associazione anticipata |
|                     | Pianificatore MS                                  | Associazione tardiva  |
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
| Microsoft Forms     | Exchange                                    | Associazione anticipata |
|                     | SharePoint                                  | Associazione anticipata |
| Microsoft To-Do     | Exchange                                    | Associazione anticipata |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come implementare l'accesso condizionale nell'ambiente in uso, vedere [Pianificare la distribuzione dell'accesso condizionale in Azure Active Directory](plan-conditional-access.md).
