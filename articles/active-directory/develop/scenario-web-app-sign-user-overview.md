---
title: Accedere agli utenti da un'app Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'app Web che accede agli utenti (panoramica)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 65d254cec5735c54e19f5adfde57fb6aed776a2c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881486"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: app Web che consente l'accesso agli utenti

Informazioni su tutto ciò che serve per creare un'app Web che usa la piattaforma di identità Microsoft per accedere agli utenti.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Se si desidera creare la prima app Web portatile (ASP.NET Core) che accede agli utenti, seguire questa guida introduttiva:

> [!div class="nextstepaction"]
> [Guida introduttiva: ASP.NET'app Web di base che consente di accedere agli utenti](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Se si desidera comprendere come aggiungere l'accesso a un'applicazione Web ASP.NET esistente, provare a eseguire la guida introduttiva seguente:

> [!div class="nextstepaction"]
> [Guida introduttiva: ASP.NET'app Web che consente l'accesso agli utenti](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Se sei uno sviluppatore Java, prova la guida introduttiva seguente:

> [!div class="nextstepaction"]
> [Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web Java](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Se si sviluppa con Python, provare la seguente guida introduttiva:

> [!div class="nextstepaction"]
> [Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Panoramica

Aggiungere l'autenticazione all'app Web in modo che possa accedere agli utenti. L'aggiunta dell'autenticazione consente all'app Web di accedere a informazioni limitate del profilo per personalizzare l'esperienza per gli utenti. 

Le app Web autenticano un utente in un Web browser. In questo scenario, l'app Web indica al browser dell'utente di accedere ad Azure Active Directory (Azure AD). Azure AD restituisce una risposta di accesso tramite il browser dell'utente, che contiene attestazioni sull'utente in un token di sicurezza. Gli utenti che effettuano l'accesso sfruttano il protocollo standard [Open ID Connect,](./v2-protocols-oidc.md) semplificato dall'utilizzo di [librerie](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)middleware.

![App Web che concede l'accesso agli utenti](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

In una seconda fase, è possibile consentire all'applicazione di chiamare le API Web per conto dell'utente connesso. Questa fase successiva è uno scenario diverso, che è presente [nell'app Web che chiama le API Web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> L'aggiunta dell'accesso a un'app Web riguarda la protezione dell'app Web e la convalida di un token utente, ovvero le operazioni eseguite dalle librerie **middleware.** Nel caso di .NET, questo scenario non richiede ancora la libreria di autenticazione Microsoft (MSAL), che consiste nell'acquisire un token per chiamare le API protette. Le librerie di autenticazione verranno introdotte nello scenario di completamento, quando l'app Web deve chiamare le API Web.

## <a name="specifics"></a>Specifiche

- Durante la registrazione dell'applicazione, dovrai fornire uno o più URI di risposta (se distribuisci l'app in più posizioni). In alcuni casi (ASP.NET e ASP.NET Core), è necessario abilitare il token ID. Infine, è consigliabile configurare un URI di disconnessione in modo che l'applicazione reagisca alla disconnessione degli utenti.
- Nel codice dell'applicazione è necessario fornire l'autorità a cui l'app Web delega l'accesso. È possibile personalizzare la convalida del token (in particolare, negli scenari dei partner).
- Le applicazioni Web supportano qualsiasi tipo di account. Per ulteriori informazioni, vedere [Tipi di account supportati](v2-supported-account-types.md).

## <a name="next-steps"></a>Passaggi successivi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Registrazione delle app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Registrazione delle app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Registrazione delle app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Registrazione delle app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
