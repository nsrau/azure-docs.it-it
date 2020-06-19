---
title: Autenticazione di Azure AD e cloud nazionali | Azure
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
ms.custom: aaddev,references_regions
ms.openlocfilehash: 20a158aac6a03dfe0bd8929de9fa66ea215aa29c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194125"
---
# <a name="national-clouds"></a>Cloud nazionali

I cloud nazionali sono istanze di Azure isolate fisicamente. Queste aree di Azure sono progettate in modo da garantire il rispetto dei requisiti di residenza, sovranità e conformità dei dati all'interno dei confini geografici.

Oltre al cloud globale, il servizio Azure Active Directory (Azure AD) è distribuito nei cloud nazionali seguenti:  

- Azure Government
- Azure Germania
- 21Vianet per Azure Cina

I cloud nazionali sono ambienti univoci e separati rispetto al cloud globale di Azure. È importante essere a conoscenza delle differenze principali durante lo sviluppo dell'applicazione per questi ambienti. Le differenze includono la registrazione di applicazioni, l'acquisizione di token e la configurazione di endpoint.

## <a name="app-registration-endpoints"></a>Endpoint di registrazione di app

Per ogni cloud nazionale è disponibile un portale di Azure separato. Per integrare le applicazioni con Microsoft Identity Platform in un cloud nazionale, è necessario registrare l'applicazione separatamente in ogni portale di Azure specifico per l'ambiente.

La tabella seguente elenca gli URL di base per gli endpoint di Azure AD usati per registrare un'applicazione per ogni cloud nazionale.

| Cloud nazionale | Endpoint del portale di Azure AD |
|----------------|--------------------------|
| Azure AD for US Government | `https://portal.azure.us` |
| Azure AD Germania | `https://portal.microsoftazure.de` |
| Azure AD Cina gestito da 21Vianet | `https://portal.azure.cn` |
| Azure AD (servizio globale) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Endpoint di autenticazione di Azure AD

Tutti i cloud nazionali eseguono l'autenticazione degli utenti separatamente in ogni ambiente e hanno endpoint di autenticazione distinti.

La tabella seguente elenca gli URL di base per gli endpoint di Azure AD usati per acquisire token per ogni cloud nazionale.

| Cloud nazionale | Endpoint di autenticazione di Azure AD |
|----------------|-------------------------|
| Azure AD for US Government | `https://login.microsoftonline.us` |
| Azure AD Germania| `https://login.microsoftonline.de` |
| Azure AD Cina gestito da 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (servizio globale)| `https://login.microsoftonline.com` |

Le richieste agli endpoint di autorizzazione o token di Azure AD possono essere create usando l'URL di base specifico dell'area appropriato. Ad esempio, per Azure Germania:

  - L'endpoint comune di autorizzazione è `https://login.microsoftonline.de/common/oauth2/v2.0/authorize`.
  - L'endpoint comune di token è `https://login.microsoftonline.de/common/oauth2/v2.0/token`.

Per le applicazioni a tenant singolo, sostituire "common" negli URL precedenti con l'ID o il nome del tenant. Un esempio è `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Per informazioni su come chiamare le API Microsoft Graph in un ambiente cloud nazionale, vedere [Microsoft Graph nelle distribuzioni cloud nazionali](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Alcuni servizi e funzionalità presenti in aree specifiche del servizio globale potrebbero non essere disponibili in tutti i cloud nazionali. Per scoprire quali servizi sono disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Per informazioni su come creare un'applicazione usando Microsoft Identity Platform, seguire l'[esercitazione su Microsoft Authentication Library (MSAL)](msal-national-cloud.md). In particolare, questa app consentirà a un utente di accedere e otterrà un token di accesso per chiamare l'API Microsoft Graph.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/)
- [Azure Cina (21Vianet)](https://docs.microsoft.com/azure/china/)
- [Azure Germania](https://docs.microsoft.com/azure/germany/)
- [Nozioni di base sull'autenticazione di Azure AD](authentication-scenarios.md)
