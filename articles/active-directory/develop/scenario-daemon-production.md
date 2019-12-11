---
title: Spostare l'app daemon chiamando le API Web nell'ambiente di produzione-piattaforma di identità Microsoft | Azure
description: Informazioni su come spostare un'app daemon che chiama API Web nell'ambiente di produzione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 961928499008445207df3a34a51fc016723c294b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962594"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>App daemon che chiama le API Web-passa all'ambiente di produzione

Ora che si è appreso come acquisire e usare un token per una chiamata da servizio a servizio, vedere come spostare l'app nell'ambiente di produzione.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Distribuzione-caso di app daemon multi-tenant

Se si è un ISV che crea un'applicazione daemon che può essere eseguita in più tenant, è necessario assicurarsi che gli amministratori del tenant:

- Effettua il provisioning di un'entità servizio per l'applicazione
- Concede il consenso all'applicazione

È necessario spiegare ai clienti come eseguire queste operazioni. Per altre informazioni, vedere [richiesta di consenso per un intero tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati alcuni collegamenti per ulteriori informazioni:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- Se non è già stato fatto, provare la Guida introduttiva [acquisire un token e chiamare Microsoft Graph API da un'app console usando l'identità dell'app](./quickstart-v2-netcore-daemon.md).
- Documentazione di riferimento per:
  - Creazione di un'istanza di [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Chiamata di [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Altri esempi/esercitazioni:
  - [Microsoft-Identity-Platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) include una semplice applicazione console daemon .NET Core che visualizza gli utenti di un tenant che eseguono query sul Microsoft Graph.

    ![Topologia](media/scenario-daemon-app/daemon-app-sample.svg)

    Lo stesso esempio illustra anche la variazione con i certificati.

    ![Topologia](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) è dotato di un'applicazione web MVC ASP.NET che sincronizza i dati da Microsoft Graph usando l'identità dell'applicazione anziché per conto di un utente. Nell'esempio viene inoltre illustrato il processo di consenso dell'amministratore.

    ![Topologia](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

Provare la Guida introduttiva [acquisire un token e chiamare Microsoft Graph API da un'app console Python usando l'identità dell'app](./quickstart-v2-python-daemon.md).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java è attualmente disponibile in anteprima pubblica. Per altre informazioni, vedere [MSAL Java dev Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
