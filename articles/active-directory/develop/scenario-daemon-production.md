---
title: Spostare un'app daemon che chiama le API Web nell'ambiente di produzione - Piattaforma di identità Microsoft . Azure
description: Informazioni su come spostare un'app daemon che chiama le API Web nell'ambiente di produzione
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
ms.openlocfilehash: db5f52c95daf4e93c140b4c93f39dad19971319d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262620"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>App Daemon che chiama le API Web - passa all'ambiente di produzione

Ora che sai come acquisire e usare un token per una chiamata da servizio a servizio, scopri come spostare l'app nell'ambiente di produzione.

## <a name="deployment---multitenant-daemon-apps"></a>Distribuzione - app daemon multi-tenant

Se si è un ISV che crea un'applicazione daemon che può essere eseguita in diversi tenant, è necessario assicurarsi che l'amministratore tenant:

- Esegue il provisioning di un'entità servizio per l'applicazione.
- Concede il consenso all'applicazione.

Dovrai spiegare ai tuoi clienti come eseguire queste operazioni. Per altre info, vedi [Richiesta di consenso per un intero tenant.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

Ecco alcuni link per aiutarti a saperne di più:

# <a name="net"></a>[.NET](#tab/dotnet)

- Guida introduttiva: [acquisisci un token e chiama l'API Microsoft Graph da un'app console usando l'identità dell'app.](./quickstart-v2-netcore-daemon.md)
- Documentazione di riferimento per:
  - Creazione di un'istanza [di ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Chiamata a [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Altri esempi/esercitazioni:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) dispone di una semplice applicazione console daemon .NET Core che visualizza gli utenti di un tenant che esegue query su Microsoft Graph.

    ![Topologia dell'app daemon di esempio](media/scenario-daemon-app/daemon-app-sample.svg)

    Lo stesso esempio illustra anche una variante con i certificati:The same sample also illustrates a variation with certificates:

    ![Topologia di app daemon di esempio - certificati](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) include un'applicazione Web MVC ASP.NET che sincronizza i dati da Microsoft Graph utilizzando l'identità dell'applicazione anziché per conto di un utente. In questo esempio viene illustrato anche il processo di consenso dell'amministratore.

    ![Topologia](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Prova la guida introduttiva [Acquisire un token e chiamare l'API Microsoft Graph da un'app console Python usando l'identità dell'app.](./quickstart-v2-python-daemon.md)

# <a name="java"></a>[Java](#tab/java)

MSAL Java è attualmente in anteprima pubblica. Per altre info, vedi Esempi di [sviluppo Java MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
