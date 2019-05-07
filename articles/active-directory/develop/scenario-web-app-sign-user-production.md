---
title: "App che esegue l'accesso per gli utenti Web autonomamente (passare alla produzione): piattaforma delle identità Microsoft"
description: Informazioni su come compilare un'app web di accesso degli utenti (passare alla produzione)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074711"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>App Web che consente l'accesso degli utenti, passare alla produzione

Ora che sai come acquisire un token per chiamare le API web, informazioni su come spostare nell'ambiente di produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

### <a name="calling-web-apis-scenario"></a>Scenario di API web di chiamare il metodo

Una volta l'app esegue l'accesso gli utenti web, può chiamare API web per conto di utenti connessi. Le API web chiamata dall'app web è l'oggetto dello scenario seguente:

> [!div class="nextstepaction"]
> [App Web che chiama l'API web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Approfondimento - esercitazione sulle app web

Informazioni su altre modalità di accesso, agli utenti con l'esercitazione di ASP.NET Core: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Si tratta di un'esercitazione progressiva con codice di produzione pronto per un'app web incluse le procedure aggiungere l'accesso.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
