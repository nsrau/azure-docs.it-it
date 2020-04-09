---
title: Spostare l'app Web che accede agli utenti nell'ambiente di produzione - Piattaforma di identità Microsoft . Azure
description: Informazioni su come creare un'app Web che accede agli utenti (passare all'ambiente di produzione)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9c5fd444c55a20441325088912a07eb051219b84
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881469"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>App Web che firma utenti: passare alla produzione

Ora che sai come ottenere un token per chiamare le API Web, scopri come spostarlo nell'ambiente di produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

### <a name="same-site"></a>Stesso sito

Assicurati di aver compreso i possibili problemi con le nuove versioni del browser Chrome

> [!div class="nextstepaction"]
> [Come gestire le modifiche ai cookie SameSite nel browser Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Scenario per la chiamata di API Web

Dopo che l'app Web ha eseguito l'accesso degli utenti, può chiamare le API Web per conto degli utenti connessi. La chiamata alle API Web dall'app Web è l'oggetto dello scenario seguente:

> [!div class="nextstepaction"]
> [App Web che chiama le API Web](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Approfondimento: esercitazione sull'app Web ASP.NET Core

Informazioni su altri modi per accedere agli utenti con questa esercitazione di base ASP.NET:Learn about other ways to sign in users with this ASP.NET Core tutorial: 

> [!div class="nextstepaction"]
> [Consentire alle app Web di accedere agli utenti e chiamare le API con la piattaforma di identità Microsoft per gli sviluppatori](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Questa esercitazione progressiva include codice pronto per la produzione per un'app Web, incluso come aggiungere l'accesso con gli account in:This progressive tutorial has production-ready code for a web app, including how to add sign-in with accounts in:

- La tua organizzazione
- Più organizzazioni
- Account aziendali o dell'istituto di istruzione o account Microsoft personali
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nubi nazionali

## <a name="sample-code-java-web-app"></a>Codice di esempio: app Web Java

Altre informazioni sull'app Web Java da questo esempio su GitHub:Learn more about the Java web app from this sample on GitHub: 

> [!div class="nextstepaction"]
> [Un'applicazione Web Java che accede agli utenti con la piattaforma di identità Microsoft e chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
