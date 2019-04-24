---
title: Autenticazione tramite Azure AD nei cloud nazionali
description: Informazioni sugli endpoint di registrazione e autenticazione delle app per i cloud nazionali.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4feaf97de7b833514113af6c91b3745be0503eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411114"
---
# <a name="national-clouds"></a>Cloud nazionali

I cloud nazionali (detti anche cloud sovrani) sono istanze di Azure isolate fisicamente. Queste aree di Azure sono progettate in modo da garantire il rispetto dei requisiti di residenza, sovranità e conformità dei dati all'interno dei confini geografici.

Oltre al cloud globale, il servizio Azure Active Directory è distribuito nei cloud nazionali seguenti:  

- Azure per enti pubblici statunitensi
- Azure Germania
- 21Vianet per Azure Cina

I cloud nazionali sono ambienti univoci e diversi rispetto al cloud Azure globale. Quando si sviluppa un'applicazione per questi ambienti, è quindi importante tenere presenti alcune differenze fondamentali, ad esempio la registrazione delle applicazioni, l'acquisizione dei token e la configurazione degli endpoint.

## <a name="app-registration-endpoints"></a>Endpoint di registrazione di app

Per ogni cloud nazionale è disponibile un portale di Azure separato. Per integrare le applicazioni con Microsoft Identity Platform in un cloud nazionale, è necessario registrare l'applicazione separatamente in ogni portale di Azure specifico per l'ambiente.

La tabella seguente elenca gli URL di base per gli endpoint di Azure Active Directory (Azure AD) usati per registrare un'applicazione per ogni cloud nazionale.

| Cloud nazionale | Endpoint del portale di Azure AD
| --- | --- |
| Azure AD for US Government |`https://portal.azure.us`
|Azure AD Germania |`https://portal.microsoftazure.de`
|Azure AD Cina gestito da 21Vianet |`https://portal.azure.cn`
|Azure AD (servizio globale)|`https://portal.azure.com` 

## <a name="azure-ad-authentication-endpoints"></a>Endpoint di autenticazione di Azure AD

Tutti i cloud nazionali eseguono l'autenticazione degli utenti separatamente in ogni ambiente e hanno endpoint di autenticazione distinti.

La tabella seguente elenca gli URL di base per gli endpoint di Azure Active Directory (Azure AD) usati per acquisire token per ogni cloud nazionale.

| Cloud nazionale | Endpoint di autenticazione di Azure AD
| --- | --- |
| Azure AD for US Government |`https://login.microsoftonline.us`
|Azure AD Germania| `https://login.microsoftonline.de`
|Azure AD Cina gestito da 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (servizio globale)|`https://login.microsoftonline.com`

- Le richieste agli endpoint di autorizzazione o token di Azure AD possono essere create usando l'URL di base specifico dell'area appropriato. Ad esempio, per Azure Germania:

  - L'endpoint comune di autorizzazione è `https://login.microsoftonline.de/common/oauth2/authorize`.
  - L'endpoint comune di token è `https://login.microsoftonline.de/common/oauth2/token`.

- Per le applicazioni a tenant singolo, sostituire common negli URL precedenti con l'ID o il nome del tenant, ad esempio `https://login.microsoftonline.de/contoso.com`.

>[!NOTE]
> Gli endpoint di token e [autorizzazione di Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) sono disponibili solo per il servizio globale. Il supporto non è ancora disponibile per le distribuzioni cloud nazionali.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Per informazioni su come chiamare le API Microsoft Graph nell'ambiente cloud nazionale, vedere [Microsoft Graph nel cloud nazionale](https://developer.microsoft.com/graph/docs/concepts/deployments).



> [!IMPORTANT]
> Alcuni servizi e funzionalità presenti in aree specifiche del servizio globale potrebbero non essere disponibili in tutti i cloud nazionali. Per scoprire quali servizi sono disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/).
- Altre informazioni su [Azure Cina 21Vianet](https://docs.microsoft.com/azure/china/).
- Altre informazioni su [Azure Germania](https://docs.microsoft.com/azure/germany/).
- [Nozioni di base sull'autenticazione in Azure AD](authentication-scenarios.md).
