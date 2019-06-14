---
title: Chiamare il metodo app daemon web API (passare alla produzione) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione daemon che chiama web API (passare alla produzione)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545394"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>App daemon che chiama web API: passare alla produzione

Ora che sai come acquisire e usare un token per una chiamata al servizio, informazioni su come spostare l'app nell'ambiente di produzione.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Distribuzione di case di App daemon multi-tenant:

Se sei un fornitore di software indipendente crea un'applicazione daemon che possono essere eseguiti in diversi tenant, è necessario assicurarsi che gli amministratori tenant:

- Effettua il provisioning di un'entità servizio per l'applicazione
- Concede il consenso all'applicazione

È necessario spiegare ai clienti come eseguire queste operazioni. Per altre informazioni, vedi [richiesta di consenso per un intero tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

Ecco alcuni collegamenti per altre informazioni:

### <a name="net"></a>.NET

- Se non è ancora disponibile, provare a eseguire l'avvio rapido [acquisire un token e chiamare l'API Microsoft Graph da un'app console usando l'identità dell'app](./quickstart-v2-netcore-daemon.md).
- Documentazione di riferimento per:
  - Creazione di un'istanza [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - La chiamata [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Altri esempi/esercitazioni:
  - [Microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) dotato di una semplice applicazione di .NET Core daemon console che consente di visualizzare gli utenti di un tenant di Microsoft Graph l'esecuzione di query.

    ![Topologia](media/scenario-daemon-app/daemon-app-sample.svg)

    Lo stesso esempio vengono inoltre illustrate le variazioni con i certificati.

    ![Topologia](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-WebApp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) dotato di un'applicazione web ASP.NET MVC che consente di sincronizzare dati da Microsoft Graph usando l'identità dell'applicazione anziché per conto di un utente. L'esempio illustra anche il processo di consenso dell'amministratore.

    ![Topologia](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

MSAL Python è attualmente in anteprima pubblica. Per altre informazioni, vedi [esempio nel repository relativo alle credenziali client Python di MSAL](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py).

### <a name="java"></a>Java

MSAL Python è attualmente in anteprima pubblica. Per altre informazioni, vedi [esempi nel repository Java MSAL](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples).