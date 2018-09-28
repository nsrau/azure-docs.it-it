---
title: Autenticazione tramite Azure AD nei cloud nazionali
description: Informazioni sugli endpoint di registrazione e autenticazione delle app per i cloud nazionali.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982002"
---
# <a name="national-clouds"></a>Cloud nazionali

I cloud nazionali (detti anche cloud sovrani) sono istanze di Azure isolate fisicamente. Queste aree di Azure sono progettate in modo da garantire il rispetto dei requisiti di residenza, sovranità e conformità dei dati all'interno dei confini geografici.

Oltre al cloud globale, il servizio Azure Active Directory è distribuito nei cloud nazionali seguenti:  

- Azure per enti pubblici statunitensi
- Azure Germania
- 21Vianet per Azure Cina

## <a name="app-registration-endpoints"></a>Endpoint di registrazione di app

La tabella seguente elenca gli URL di base per gli endpoint di Azure Active Directory (Azure AD) usati per registrare un'applicazione per ogni cloud nazionale.

| Cloud nazionale | Endpoint del portale di Azure AD
| --- | --- |
| Azure AD for US Government |https://portal.azure.us
|Azure AD Germania |https://portal.microsoftazure.de
|Azure AD Cina gestito da 21Vianet |https://portal.azure.cn
|Azure AD (servizio globale)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Endpoint di autenticazione di Azure AD

La tabella seguente elenca gli URL di base per gli endpoint di Azure Active Directory (Azure AD) usati per acquisire token per chiamare Microsoft Graph per ogni cloud nazionale.

| Cloud nazionale | Endpoint di autenticazione di Azure AD
| --- | --- |
| Azure AD for US Government |`https://login.microsoftonline.us`
|Azure AD Germania| `https://login.microsoftonline.de`
|Azure AD Cina gestito da 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (servizio globale)|`https://login.microsoftonline.com`

Le richieste agli endpoint di autorizzazione o token di Azure AD possono essere create usando l'URL di base specifico dell'area appropriato. Ad esempio, nel caso della Germania:

- L'endpoint comune di autorizzazione è `https://login.microsoftonline.de/common/oauth2/authorize`
- L'endpoint comune di token è `https://login.microsoftonline.de/common/oauth2/token` 

Per le applicazioni a tenant singolo, sostituire common negli URL precedenti con l'ID o il nome del tenant, ad esempio `https://login.microsoftonline.de/contoso.com`

>[!NOTE]
> Gli endpoint di token e [autorizzazione di Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) sono disponibili solo per il servizio globale. Il supporto non è ancora disponibile per le distribuzioni cloud nazionali.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/)
- Altre informazioni su [Azure Cina 21Vianet](https://docs.microsoft.com/azure/china/)
- Altre informazioni su [Azure Germania](https://docs.microsoft.com/azure/germany/)
- [Nozioni di base sull'autenticazione in Azure AD](authentication-scenarios.md)
- Altre informazioni sulla [distribuzione di Microsoft Graph nel cloud nazionale](https://developer.microsoft.com/graph/docs/concepts/deployments)