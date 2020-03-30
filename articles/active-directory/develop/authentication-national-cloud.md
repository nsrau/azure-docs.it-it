---
title: Autenticazione di Azure AD & cloud nazionali Azure
titleSuffix: Microsoft identity platform
description: Informazioni sugli endpoint di registrazione e autenticazione delle app per i cloud nazionali.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262997"
---
# <a name="national-clouds"></a>Nubi nazionali

Le nuvole nazionali sono istanze fisicamente isolate di Azure.National clouds are physically isolated instances of Azure. Queste aree di Azure sono progettate in modo da garantire il rispetto dei requisiti di residenza, sovranità e conformità dei dati all'interno dei confini geografici.

Includendo il cloud globale, Azure Active Directory (Azure AD) viene distribuito nei cloud nazionali seguenti:Including the global cloud, Azure Active Directory (Azure AD) is deployed in the following national clouds:  

- Azure Government
- Azure Germania
- 21Vianet per Azure Cina

I cloud nazionali sono univoci e un ambiente separato da quello globale di Azure.National clouds are unique and a separate environment from Azure global. È importante tenere presente le differenze principali durante lo sviluppo dell'applicazione per questi ambienti. Le differenze includono la registrazione delle applicazioni, l'acquisizione di token e la configurazione degli endpoint.

## <a name="app-registration-endpoints"></a>Endpoint di registrazione di app

È disponibile un portale di Azure separato per ognuno dei cloud nazionali. Per integrare le applicazioni con la piattaforma di identità Microsoft in un cloud nazionale, è necessario registrare l'applicazione separatamente in ogni portale di Azure specifico per l'ambiente.

Nella tabella seguente sono elencati gli URL di base per gli endpoint di Azure AD usati per registrare un'applicazione per ogni cloud nazionale.

| Cloud nazionale | Endpoint del portale di Azure AD |
|----------------|--------------------------|
| Azure AD for US Government | `https://portal.azure.us` |
| Azure AD Germania | `https://portal.microsoftazure.de` |
| Azure AD Cina gestito da 21Vianet | `https://portal.azure.cn` |
| Azure AD (servizio globale) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Endpoint di autenticazione di Azure AD

Tutti i cloud nazionali eseguono l'autenticazione degli utenti separatamente in ogni ambiente e hanno endpoint di autenticazione distinti.

Nella tabella seguente sono elencati gli URL di base per gli endpoint di Azure AD usati per acquisire token per ogni cloud nazionale.

| Cloud nazionale | Endpoint di autenticazione di Azure ADAzure AD authentication endpoint |
|----------------|-------------------------|
| Azure AD for US Government | `https://login.microsoftonline.us` |
| Azure AD Germania| `https://login.microsoftonline.de` |
| Azure AD Cina gestito da 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (servizio globale)| `https://login.microsoftonline.com` |

È possibile formare le richieste agli endpoint di autorizzazione o token di Azure AD usando l'URL di base specifico dell'area appropriata. Ad esempio, per Azure Germania:

  - L'endpoint comune di autorizzazione è `https://login.microsoftonline.de/common/oauth2/authorize`.
  - L'endpoint comune di token è `https://login.microsoftonline.de/common/oauth2/token`.

Per le applicazioni single-tenant, sostituire "common" negli URL precedenti con l'ID o il nome del tenant. Un esempio è `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Per informazioni su come chiamare le API di Microsoft Graph in un ambiente cloud nazionale, visitare [Microsoft Graph nelle distribuzioni cloud nazionali.](https://developer.microsoft.com/graph/docs/concepts/deployments)

> [!IMPORTANT]
> Alcuni servizi e funzionalità che si trovano in aree specifiche del servizio globale potrebbero non essere disponibili in tutti i cloud nazionali. Per scoprire quali servizi sono disponibili, vai a [Prodotti disponibili per regione](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Per informazioni su come compilare un'applicazione utilizzando la piattaforma di identità Microsoft, seguire [l'esercitazione Microsoft Authentication Library (MSAL).](msal-national-cloud.md) In particolare, questa app accederà a un utente e otterrà un token di accesso per chiamare l'API Microsoft Graph.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germania](https://docs.microsoft.com/azure/germany/)
- [Nozioni di base sull'autenticazione di Azure ADAzure AD authentication basics](authentication-scenarios.md)
