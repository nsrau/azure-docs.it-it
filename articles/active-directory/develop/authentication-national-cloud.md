---
title: Autenticazione tramite Azure Active Directory nei cloud nazionali
description: Informazioni sugli endpoint di registrazione e autenticazione delle app per i cloud nazionali.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235484"
---
# <a name="national-clouds"></a>Cloud nazionali

I cloud nazionali sono isolate fisicamente le istanze di Azure. Queste aree di Azure sono progettate in modo da garantire il rispetto dei requisiti di residenza, sovranità e conformità dei dati all'interno dei confini geografici.

Tra cui il cloud globale, Azure Active Directory (Azure AD) viene distribuito nei cloud nazionali seguenti:  

- Azure Government
- Azure Germania
- 21Vianet per Azure Cina

I cloud nazionali sono univoci e un ambiente distinto da Azure globale. È importante essere a conoscenza delle differenze principali durante lo sviluppo dell'applicazione per questi ambienti. Le differenze includono la registrazione delle applicazioni, l'acquisizione di token e la configurazione degli endpoint.

## <a name="app-registration-endpoints"></a>Endpoint di registrazione di app

È un portale di Azure separato per ciascuno dei cloud nazionali. Per integrare applicazioni con la piattaforma delle identità Microsoft in un cloud nazionale, ti viene richiesto di registrare l'applicazione separatamente ogni portale di Azure che è specifico per l'ambiente.

La tabella seguente elenca gli URL di base per gli endpoint di Azure AD usati per registrare un'applicazione per ogni cloud nazionali.

| Cloud nazionale | Endpoint del portale di Azure AD |
|----------------|--------------------------|
| Azure AD for US Government | `https://portal.azure.us` |
| Azure AD Germania | `https://portal.microsoftazure.de` |
| Azure AD Cina gestito da 21Vianet | `https://portal.azure.cn` |
| Azure AD (servizio globale) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Endpoint di autenticazione di Azure AD

Tutti i cloud nazionali eseguono l'autenticazione degli utenti separatamente in ogni ambiente e hanno endpoint di autenticazione distinti.

La tabella seguente elenca gli URL di base per gli endpoint di Azure AD usati per acquisire i token per ogni cloud nazionali.

| Cloud nazionale | Endpoint di autenticazione di Azure AD |
|----------------|-------------------------|
| Azure AD for US Government | `https://login.microsoftonline.us` |
| Azure AD Germania| `https://login.microsoftonline.de` |
| Azure AD Cina gestito da 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (servizio globale)| `https://login.microsoftonline.com` |

È possibile formare richieste per l'autorizzazione di Azure AD o gli endpoint di token usando l'URL di base specifico dell'area geografica appropriata. Ad esempio, per Azure Germania:

  - L'endpoint comune di autorizzazione è `https://login.microsoftonline.de/common/oauth2/authorize`.
  - L'endpoint comune di token è `https://login.microsoftonline.de/common/oauth2/token`.

Per le applicazioni a tenant singolo, sostituire "common" nell'URL precedente con l'ID tenant o nome. Un esempio è `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> Il [autorizzazione di Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) e gli endpoint di token sono disponibili solo per il servizio globale. Siano non tutti supportati per le distribuzioni di cloud nazionali.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Per informazioni su come chiamare le API Microsoft Graph in un ambiente cloud nazionale, passare a [Microsoft Graph in distribuzioni cloud nazionali](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Alcuni servizi e le funzionalità che si trovano in aree specifiche del servizio globale potrebbero non essere disponibili in tutti i cloud nazionali. Per scoprire quali servizi sono disponibili, passare a [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Per informazioni su come compilare un'applicazione con la piattaforma delle identità Microsoft, seguire le [esercitazione di Microsoft Authentication Library (MSAL)](msal-national-cloud.md). In particolare, questa app sarà un utente di accedere e ottenere un token di accesso per chiamare l'API Microsoft Graph.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet per Azure Cina](https://docs.microsoft.com/azure/china/)
- [Azure Germania](https://docs.microsoft.com/azure/germany/)
- [Nozioni fondamentali di autenticazione AD Azure](authentication-scenarios.md)
