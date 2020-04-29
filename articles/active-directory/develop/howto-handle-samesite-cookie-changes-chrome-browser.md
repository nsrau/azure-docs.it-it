---
title: Come gestire le modifiche ai cookie navigava sullostesso sito nel browser Chrome | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come gestire le modifiche ai cookie navigava sullostesso sito nel browser Chrome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: f28d3722d56582bd925d31b43b4a0219bca2ae30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534602"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Gestire le modifiche ai cookie SameSite nel browser Chrome

## <a name="what-is-samesite"></a>Che cos'è navigava sullostesso sito?

`SameSite`è una proprietà che può essere impostata in cookie HTTP per evitare attacchi di richiesta intersito falsa (CSRF) nelle applicazioni Web:

- Quando `SameSite` è impostato su **LAX**, il cookie viene inviato nelle richieste all'interno dello stesso sito e in richieste Get da altri siti. Non viene inviato nelle richieste GET che sono tra domini.
- Il valore **strict** garantisce che il cookie venga inviato nelle richieste solo all'interno dello stesso sito.

Per impostazione predefinita, `SameSite` il valore non è impostato nei browser ed è per questo motivo che non esistono restrizioni per i cookie inviati nelle richieste. Un'applicazione deve acconsentire esplicitamente alla protezione CSRF impostando **LAX** o **strict** in base ai requisiti.

## <a name="samesite-changes-and-impact-on-authentication"></a>Navigava sullostesso sito modifiche e conseguenze sull'autenticazione

[Gli aggiornamenti recenti agli standard in navigava sullostesso sito](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) propongono la protezione delle `SameSite` app rendendo il comportamento predefinito quando nessun valore è impostato su LAX. Questa attenuazione significa che i cookie verranno limitati alle richieste HTTP, ad eccezione del fatto che vengono effettuate da altri siti. Viene inoltre introdotto un valore **None** per rimuovere le restrizioni relative ai cookie inviati. Questi aggiornamenti verranno presto rilasciati in una versione imminente del browser Chrome.

Quando le app Web eseguono l'autenticazione con la piattaforma di identità Microsoft usando la modalità di risposta "form_post", il server di accesso risponde all'applicazione usando un HTTP POST per inviare i token o il codice di autenticazione. Poiché questa richiesta è una richiesta tra domini (ad esempio `login.microsoftonline.com` `https://contoso.com/auth`, da al dominio), i cookie impostati dall'app ora rientrano nelle nuove regole in Chrome. I cookie che devono essere usati negli scenari tra siti sono cookie che contengono i valori di *stato* e *nonce* , che vengono inviati anche nella richiesta di accesso. Sono presenti altri cookie rilasciati da Azure AD per mantenere la sessione.

Se non si aggiornano le app Web, questo nuovo comportamento comporterà errori di autenticazione.

## <a name="mitigation-and-samples"></a>Mitigazione ed esempi

Per risolvere gli errori di autenticazione, le app Web che eseguono l'autenticazione con la piattaforma di `SameSite` identità Microsoft `None` possono impostare la proprietà su per i cookie usati negli scenari tra domini durante l'esecuzione nel browser Chrome.
Altri browser (vedere [qui](https://www.chromium.org/updates/same-site/incompatible-clients) per un elenco completo) seguono il comportamento precedente di `SameSite` e non includono i cookie se `SameSite=None` è impostato.
Per questo motivo, per supportare l'autenticazione su più browser, le app Web dovranno impostare `SameSite` il valore `None` su solo in Chrome e lasciare vuoto il valore in altri browser.

Questo approccio viene illustrato negli esempi di codice riportati di seguito.

# <a name="net"></a>[.NET](#tab/dotnet)

La tabella seguente illustra le richieste pull che hanno aggirato le modifiche apportate a navigava sullostesso sito negli esempi di ASP.NET e ASP.NET Core.

| Esempio | Richiesta pull |
| ------ | ------------ |
|  [Esercitazione incrementale di ASP.NET Core app Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Correzione #261 dello stesso cookie del sito](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [Esempio di app Web MVC ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Correzione #35 dello stesso cookie del sito](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active-Directory-DotNet-admin-Restricted-Scopes-V2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Correzione #28 dello stesso cookie del sito](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

per informazioni dettagliate su come gestire i cookie navigava sullostesso sito in ASP.NET e ASP.NET Core, vedere anche:

- [Usare i cookie navigava sullostesso sito in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [Blog di ASP.NET sul problema navigava sullostesso sito](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Esempio |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Esempio | Richiesta pull |
| ------ | ------------ |
|  [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Correzione #24 dello stesso cookie del sito](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-Identity-Java-WebAPI](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Correzione #4 dello stesso cookie del sito](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su navigava sullostesso sito e sullo scenario dell'app Web:

> [!div class="nextstepaction"]
> [Domande frequenti su Google Chrome su navigava sullostesso sito](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Pagina navigava sullostesso sito di Chromium](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Scenario: app Web che accede agli utenti](scenario-web-app-sign-user-overview.md)