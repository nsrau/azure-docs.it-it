---
title: Come gestire le modifiche ai cookie SameSite nel browser Chrome Azure
titleSuffix: Microsoft identity platform
description: Scopri come gestire le modifiche ai cookie SameSite nel browser Chrome.
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 056b787bbbcde6ba7f9510043deabdcf85ac7467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050534"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Gestire le modifiche ai cookie SameSite nel browser Chrome

## <a name="what-is-samesite"></a>Che cos'è SameSite?

`SameSite`è una proprietà che può essere impostata nei cookie HTTP per prevenire gli attacchi Cross Site Request Forgery(CSRF) nelle applicazioni Web:

- Quando `SameSite` è impostato su **Lax**, il cookie viene inviato nelle richieste all'interno dello stesso sito e nelle richieste GET da altri siti. Non viene inviato nelle richieste GET che sono tra domini.
- Il valore **Strict** garantisce che il cookie venga inviato nelle richieste solo all'interno dello stesso sito.

Per impostazione `SameSite` predefinita, il valore NON è impostato nei browser ed è per questo che non ci sono restrizioni sui cookie inviati nelle richieste. Un'applicazione dovrebbe acconsentire esplicitamente alla protezione CSRF impostando **Lax** o **Strict** in base alle proprie esigenze.

## <a name="samesite-changes-and-impact-on-authentication"></a>Modifiche di SameSite e impatto sull'autenticazione

Gli aggiornamenti recenti [agli standard su SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) propongono la protezione delle app rendendo il comportamento predefinito di `SameSite` quando nessun valore è impostato su Lax. Questa mitigazione significa che i cookie saranno limitati sulle richieste HTTP tranne GET effettuate da altri siti. Inoltre, viene introdotto un valore **None** per rimuovere le restrizioni sui cookie inviati. Questi aggiornamenti saranno presto rilasciati in una prossima versione del browser Chrome.

Quando le app Web eseguono l'autenticazione con la piattaforma Microsoft Identity usando la modalità di risposta "form_post", il server di accesso risponde all'applicazione usando un HTTP POST per inviare i token o il codice di autenticazione. Poiché questa richiesta è una `login.microsoftonline.com` richiesta tra domini `https://contoso.com/auth`(ad esempio, da un dominio), i cookie impostati dall'app ora rientrano nelle nuove regole di Chrome. I cookie che devono essere utilizzati in scenari intersito sono cookie che contengono i valori *di stato* e *nonce,* che vengono inviati anche nella richiesta di accesso. Ci sono altri cookie eliminati da Azure AD per tenere la sessione.

Se non si aggiornano le app Web, questo nuovo comportamento comporterà errori di autenticazione.

## <a name="mitigation-and-samples"></a>Mitigazione e campioni

Per superare gli errori di autenticazione, le app Web `SameSite` che `None` eseguono l'autenticazione con la piattaforma di identità Microsoft possono impostare la proprietà su per i cookie utilizzati in scenari tra domini durante l'esecuzione nel browser Chrome.
Altri browser (vedi [qui](https://www.chromium.org/updates/same-site/incompatible-clients) per un elenco completo) seguono il comportamento precedente di `SameSite` e non includeranno i cookie se `SameSite=None` è impostato.
Ecco perché, per supportare l'autenticazione su più `SameSite` browser `None` applicazioni web dovranno impostare il valore solo su Chrome e lasciare il valore vuoto su altri browser.

Questo approccio è illustrato negli esempi di codice riportati di seguito.

# <a name="net"></a>[.NET](#tab/dotnet)

La tabella seguente presenta le richieste pull che aggiunsi alle modifiche di SameSite negli esempi di ASP.NET e ASP.NET Core.

| Esempio | Richiesta pull |
| ------ | ------------ |
|  [esercitazione incrementale di ASP.NET Core Web App](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Stesso sito cookie risolvere #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET'esempio di MVC Web App](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Stesso sito cookie correggere #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Stesso sito cookie risolvere #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

per informazioni dettagliate su come gestire i cookie SameSite in ASP.NET e ASP.NET Core, vedere anche:

- [Utilizzare i cookie SameSite in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [ASP.NET problema di Blog su SameSite](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Esempio |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Esempio | Richiesta pull |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Stesso sito cookie fix #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Stesso sito cookie risolvere #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su SameSite e sullo scenario dell'app Web:

> [!div class="nextstepaction"]
> [Domande frequenti su Google Chrome su SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Pagina Chromium SameSite](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Scenario: app Web che consente l'accesso agli utenti](scenario-web-app-sign-user-overview.md)