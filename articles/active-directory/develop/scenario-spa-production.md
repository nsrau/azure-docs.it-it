---
title: 'Applicazione a singola pagina (passare alla produzione): piattaforma delle identità Microsoft'
description: Informazioni su come compilare un'applicazione a singola pagina (passare alla produzione)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075011"
---
# <a name="single-page-application---move-to-production"></a>Applicazione a singola pagina - spostamento nell'ambiente di produzione

Ora che sai come acquisire un token per chiamare le API Web, informazioni su come spostare nell'ambiente di produzione.

## <a name="improve-your-app"></a>Migliorare l'app

Seguire i passaggi necessari per preparare l'ambiente di produzione di app.

- [Abilitare la registrazione](msal-logging.md) nell'applicazione.

## <a name="test-your-integration"></a>Test di integrazione

- Testare l'integrazione seguendo l'[elenco di controllo per l'integrazione di Microsoft Identity Platform](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Passaggi successivi

Ecco alcuni altri esempi/esercitazioni:

- Per un'analisi approfondita di avvio rapido di esempio che illustra il codice sull'accesso degli utenti e ottenere un token di accesso per chiamare l'API Graph di Microsoft usando msal. js

    > [!div class="nextstepaction"]
    > [Esercitazione su SPA di JavaScript](./tutorial-v2-javascript-spa.md)

- Esempio che illustra come ottenere i token per l'API web back-end usando msal. js

     > [!div class="nextstepaction"]
     > [Applicazione a singola pagina con un back-end ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Esempio per mostrare come usare msal per consentire agli utenti in un'app registrata in Azure AD B2C

    > [!div class="nextstepaction"]
    > [Applicazione a singola pagina con Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
