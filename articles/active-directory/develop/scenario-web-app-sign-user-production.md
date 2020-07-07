---
title: Spostare l'app Web che esegue l'accesso degli utenti all'ambiente di produzione-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'app Web per l'accesso degli utenti (passare alla produzione)
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
ms.openlocfilehash: 47a45b52ac10a44b6efd54c41b3fec1e61a47a35
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82181631"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>App Web che esegue l'accesso agli utenti: passa alla produzione

Ora che si è appreso come ottenere un token per chiamare le API Web, si apprenderà come spostarlo in produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

### <a name="troubleshooting"></a>Risoluzione dei problemi

> [!NOTE]
> Quando gli utenti accedono all'applicazione Web per la prima volta, devono fornire il consenso. Tuttavia, in alcune organizzazioni, gli utenti possono visualizzare un messaggio simile al seguente:
>
> *AppName richiede le autorizzazioni per accedere alle risorse dell'organizzazione che solo un amministratore può concedere. Chiedere a un amministratore di concedere l'autorizzazione per questa app prima di poterla usare.*
>
> Questo perché l'amministratore tenant ha **disabilitato** la possibilità per gli utenti di fornire il consenso. In tal caso, è necessario contattare gli amministratori tenant in modo che eseguano il consenso dell'amministratore per gli ambiti richiesti dall'applicazione.

### <a name="same-site"></a>Stesso sito

Assicurarsi di comprendere i possibili problemi con le nuove versioni del browser Chrome

> [!div class="nextstepaction"]
> [Come gestire le modifiche ai cookie navigava sullostesso sito nel browser Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

Il pacchetto NuGet Microsoft. Identity. Web gestisce i problemi più comuni di navigava sullostesso sito.

### <a name="scenario-for-calling-web-apis"></a>Scenario per la chiamata di API Web

Dopo che l'app Web ha effettuato l'accesso agli utenti, può chiamare le API Web per conto degli utenti connessi. La chiamata di API Web dall'app Web è l'oggetto dello scenario seguente:

> [!div class="nextstepaction"]
> [App Web che chiama le API Web](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Approfondimento: esercitazione sull'app Web ASP.NET Core

Altre informazioni su altri modi per accedere agli utenti con questa ASP.NET Core esercitazione: 

> [!div class="nextstepaction"]
> [Consentire alle app Web di eseguire l'accesso agli utenti e chiamare le API con la piattaforma di identità Microsoft per gli sviluppatori](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Questa esercitazione progressiva include codice pronto per la produzione per un'app Web, incluso come aggiungere l'accesso con gli account in:

- Organizzazione
- Più organizzazioni
- Account aziendali o dell'Istituto di istruzione o account Microsoft personali
- [Azure AD B2C](https://aka.ms/aadb2c)
- Cloud nazionali

## <a name="sample-code-java-web-app"></a>Codice di esempio: app Web Java

Per altre informazioni sull'app Web Java, vedere questo esempio in GitHub: 

> [!div class="nextstepaction"]
> [Applicazione Web Java che esegue l'accesso agli utenti con la piattaforma di identità Microsoft e chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
