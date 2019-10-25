---
title: Autenticazione con Azure Active Directory nei cloud nazionali
titleSuffix: Microsoft identity platform
description: Informazioni sugli endpoint di registrazione e autenticazione delle app per i cloud nazionali.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70f1f80744043893dd7206bc6388b8e080c07d0f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803895"
---
# <a name="national-clouds"></a>Cloud nazionali

I cloud nazionali sono istanze fisicamente isolate di Azure. Queste aree di Azure sono progettate in modo da garantire il rispetto dei requisiti di residenza, sovranità e conformità dei dati all'interno dei confini geografici.

Incluso il cloud globale, Azure Active Directory (Azure AD) viene distribuito nei cloud nazionali seguenti:  

- Azure per enti pubblici
- Azure Germania
- 21Vianet per Azure Cina

I cloud nazionali sono univoci e un ambiente separato da Azure globale. È importante tenere presenti le differenze principali durante lo sviluppo dell'applicazione per questi ambienti. Le differenze includono la registrazione di applicazioni, l'acquisizione di token e la configurazione degli endpoint.

## <a name="app-registration-endpoints"></a>Endpoint di registrazione di app

Esiste una portale di Azure separata per ognuno dei cloud nazionali. Per integrare le applicazioni con la piattaforma di identità Microsoft in un cloud nazionale, è necessario registrare l'applicazione separatamente in ogni portale di Azure specifico per l'ambiente.

La tabella seguente elenca gli URL di base per gli endpoint Azure AD usati per registrare un'applicazione per ogni cloud nazionale.

| Cloud nazionale | Endpoint del portale di Azure AD |
|----------------|--------------------------|
| Azure AD for US Government | `https://portal.azure.us` |
| Azure AD Germania | `https://portal.microsoftazure.de` |
| Azure AD Cina gestito da 21Vianet | `https://portal.azure.cn` |
| Azure AD (servizio globale) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Endpoint di autenticazione di Azure AD

Tutti i cloud nazionali eseguono l'autenticazione degli utenti separatamente in ogni ambiente e hanno endpoint di autenticazione distinti.

La tabella seguente elenca gli URL di base per gli endpoint Azure AD usati per acquisire i token per ogni cloud nazionale.

| Cloud nazionale | Endpoint di autenticazione Azure AD |
|----------------|-------------------------|
| Azure AD for US Government | `https://login.microsoftonline.us` |
| Azure AD Germania| `https://login.microsoftonline.de` |
| Azure AD Cina gestito da 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (servizio globale)| `https://login.microsoftonline.com` |

È possibile creare richieste per l'autorizzazione Azure AD o gli endpoint del token usando l'URL di base specifico dell'area. Ad esempio, per Azure Germania:

  - L'endpoint comune di autorizzazione è `https://login.microsoftonline.de/common/oauth2/authorize`.
  - L'endpoint comune di token è `https://login.microsoftonline.de/common/oauth2/token`.

Per le applicazioni a tenant singolo, sostituire "Common" negli URL precedenti con l'ID o il nome del tenant. Un esempio è `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Per informazioni su come chiamare le API di Microsoft Graph in un ambiente cloud nazionale, vedere [Microsoft Graph nelle distribuzioni cloud nazionali](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Alcuni servizi e funzionalità che si trovano in aree specifiche del servizio globale potrebbero non essere disponibili in tutti i cloud nazionali. Per informazioni sui servizi disponibili, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Per informazioni su come compilare un'applicazione con la piattaforma di identità Microsoft, seguire l' [esercitazione Microsoft Authentication Library (MSAL)](msal-national-cloud.md). In particolare, questa app consente di accedere a un utente e ottenere un token di accesso per chiamare l'API Microsoft Graph.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure Cina 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germania](https://docs.microsoft.com/azure/germany/)
- [Nozioni di base sull'autenticazione Azure AD](authentication-scenarios.md)
