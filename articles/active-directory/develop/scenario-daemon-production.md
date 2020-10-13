---
title: Spostare un'app daemon che chiama API Web in produzione-piattaforma di identità Microsoft | Azure
description: Informazioni su come spostare un'app daemon che chiama API Web nell'ambiente di produzione
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 72e6d0a8677f7a8175223b80541c99026a9eb16f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119165"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>App daemon che chiama le API Web-passa all'ambiente di produzione

Ora che si è appreso come acquisire e usare un token per una chiamata da servizio a servizio, vedere come spostare l'app nell'ambiente di produzione.

## <a name="deployment---multitenant-daemon-apps"></a>Distribuzione-app daemon multi-tenant

Se si è un ISV che crea un'applicazione daemon che può essere eseguita in più tenant, è necessario assicurarsi che l'amministratore del tenant:

- Effettua il provisioning di un'entità servizio per l'applicazione.
- Concede il consenso all'applicazione.

È necessario spiegare ai clienti come eseguire queste operazioni. Per altre informazioni, vedere [richiesta di consenso per un intero tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati alcuni collegamenti che consentono di ottenere ulteriori informazioni:

# <a name="net"></a>[.NET](#tab/dotnet)

- Guida introduttiva: [acquisire un token e chiamare Microsoft Graph API da un'app console usando l'identità dell'app](./quickstart-v2-netcore-daemon.md).
- Documentazione di riferimento per:
  - Creazione di un'istanza di [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Chiamata a [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Altri esempi/esercitazioni:
  - [Microsoft-Identity-Platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) include una semplice applicazione console daemon .NET Core che visualizza gli utenti di un tenant che esegue una query Microsoft Graph.

    ![Topologia dell'app daemon di esempio](media/scenario-daemon-app/daemon-app-sample.svg)

    Nello stesso esempio viene inoltre illustrata una variante con i certificati:

    ![Esempio di topologia di app daemon-certificati](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) è dotato di un'applicazione web MVC ASP.NET che sincronizza i dati da Microsoft Graph usando l'identità dell'applicazione anziché per conto di un utente. Questo esempio illustra anche il processo di consenso dell'amministratore.

    ![Topologia](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Provare la Guida introduttiva [acquisire un token e chiamare Microsoft Graph API da un'app console Python usando l'identità dell'app](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

MSAL Java è attualmente disponibile in anteprima pubblica. Per altre informazioni, vedere [MSAL Java dev Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---