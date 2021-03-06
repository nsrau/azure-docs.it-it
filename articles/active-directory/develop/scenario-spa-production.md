---
title: Spostare un'app a singola pagina in produzione-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'applicazione a singola pagina (passare alla produzione)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0a51442870fb72e2b3cd93d9f03736d2c679ed06
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442821"
---
# <a name="single-page-application-move-to-production"></a>Applicazione a pagina singola: sposta in produzione

Ora che si è appreso come acquisire un token per chiamare le API Web, vedere come passare alla produzione.

## <a name="improve-your-app"></a>Migliorare l'app

[Abilitare la registrazione](msal-logging.md) per preparare la produzione dell'app.

## <a name="test-your-integration"></a>Testare l'integrazione

Testare l'integrazione seguendo l'[elenco di controllo per l'integrazione di Microsoft Identity Platform](identity-platform-integration-checklist.md).

## <a name="deploy-your-app"></a>Distribuire l'app

Vedere un [esempio di distribuzione](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) per apprendere come distribuire i progetti di API Web e di Spa con archiviazione di Azure e i servizi di app Azure, rispettivamente. 

## <a name="next-steps"></a>Passaggi successivi

- Approfondimento dell'esempio di avvio rapido, che illustra il codice per l'accesso degli utenti e ottenere un token di accesso per chiamare l' **API Microsoft Graph** usando **MSAL.js** : [JavaScript Spa tutorial](./tutorial-v2-javascript-spa.md).

- Esempio che illustra come ottenere i token per la propria API Web back-end (ASP.NET Core) usando **MSAL.js** : [Spa con un back-end ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi).

- Esempio che illustra come convalidare i token di accesso per l'API Web back-end (Node.js) usando **Passport-Azure-ad** : [Node.js API Web (Azure ad](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2).

- Esempio che illustra come usare **MSAL.js** per l'accesso degli utenti in un'app registrata con **Azure Active Directory B2C** (Azure ad B2C): [Spa con Azure ad B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

- Esempio che illustra come usare **Passport-Azure-ad** per convalidare i token di accesso per le app registrate con **Azure Active Directory B2C** (Azure ad B2C): [Node.js API Web (Azure ad B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).
