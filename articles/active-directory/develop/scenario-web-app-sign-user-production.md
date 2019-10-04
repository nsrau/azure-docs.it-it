---
title: App Web che esegue l'accesso agli utenti (passa alla produzione)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app Web per l'accesso degli utenti (passare alla produzione)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086540"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>App Web che consente di accedere all'ambiente di produzione

Ora che si è appreso come acquisire un token per chiamare le API Web, vedere come spostarlo in produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

### <a name="calling-web-apis-scenario"></a>Scenario di chiamata alle API Web

Dopo che l'app Web ha eseguito l'accesso agli utenti, può chiamare le API Web per conto degli utenti connessi. La chiamata di API Web dall'app Web è l'oggetto dello scenario seguente:

> [!div class="nextstepaction"]
> [App Web che chiama le API Web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Approfondimento-esercitazione sull'app Web ASP.NET Core

Informazioni sulle altre modalità di accesso degli utenti con l'ASP.NET Core esercitazione: [MS-Identity-aspnetcore-WebApp-Tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Questo esempio è un'esercitazione progressiva con codice pronto per la produzione per un'app Web che include come aggiungere l'accesso con gli account in:

- l'organizzazione,
- più organizzazioni,
- account aziendali o dell'Istituto di istruzione o Personal account Microsoft
- con [Azure ad B2C](https://aka.ms/aadb2c),
- o nei cloud nazionali.

### <a name="sample-code---java-web-app"></a>Codice di esempio-app Web Java

Per altre informazioni sull'app Web Java, vedere l'esempio su GitHub: [Applicazione Web Java che esegue l'accesso agli utenti con la piattaforma di identità Microsoft e chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
