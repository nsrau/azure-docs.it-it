---
title: Tipi di applicazione per la piattaforma di identità Microsoft Azure
description: Tipi di app e scenari supportati dall'endpoint della piattaforma di identità Microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: def92071496716f90b24158a50e4a5233e93c994
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677993"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Tipi di applicazione per la piattaforma di identità MicrosoftApplication types for Microsoft identity platform

L'endpoint di Microsoft Identity Platform (v2.0) supporta l'autenticazione per un'ampia gamma di architetture di app moderne, tutte basate sui protocolli standard del settore [OAuth 2.0 o OpenID Connect](active-directory-v2-protocols.md). Questo articolo descrive i tipi di app che è possibile compilare utilizzando la piattaforma di identità Microsoft, indipendentemente dalla lingua o dalla piattaforma preferita. Le informazioni sono progettate per facilitare la comprensione degli scenari di alto livello prima di [iniziare a utilizzare il codice.](v2-overview.md#getting-started)

## <a name="the-basics"></a>Nozioni di base

È necessario registrare ogni app che usa l'endpoint della piattaforma di identità Microsoft nel nuovo [portale per le registrazioni](https://go.microsoft.com/fwlink/?linkid=2083908)di app. Il processo di registrazione delle app raccoglie e assegna all'app questi valori:

* Un **ID applicazione (client)** che identifica in modo univoco l'app
* Un **URI di reindirizzamento** che può essere usato per reindirizzare le risposte all'app
* Alcuni altri valori specifici dello scenario, ad esempio i tipi di account supportati

Per i dettagli, vedere come [registrare un'app](quickstart-register-app.md).

Dopo la registrazione dell'app, l'app comunica con la piattaforma di identità Microsoft inviando richieste all'endpoint. Microsoft offre framework e librerie open source che gestiscono i dettagli di queste richieste. È sempre possibile implementare personalmente la logica di autenticazione creando richieste a questi endpoint:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>App a singola pagina (JavaScript)

Molte app moderne hanno un front-end dell'app a singola pagina scritto principalmente in JavaScript. Spesso, è scritto utilizzando un framework come Angular, React, o Vue. L'endpoint della piattaforma di identità Microsoft supporta queste app utilizzando il [flusso implicito OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

In questo flusso, l'app riceve i token direttamente dall'endpoint di autorizzazione della piattaforma di identità Microsoft, senza scambi da server a server. In questo modo, tutta la logica di autenticazione e gestione della sessione avviene interamente nel client JavaScript, senza eseguire reindirizzamenti a pagine aggiuntive.

![Mostra il flusso di autenticazione implicita](./media/v2-app-types/convergence-scenarios-implicit.svg)

Per vedere questo scenario in azione, prova uno degli esempi di codice dell'app a pagina singola nella sezione introduttiva della piattaforma di [identità Microsoft.To see](v2-overview.md#getting-started) this scenario in action, try one of the single-page app code samples in the Microsoft identity platform getting started section.

## <a name="web-apps"></a>App Web

Per le app Web (.NET, PHP, Java, Ruby, Python, Node) accessibili tramite un browser, è possibile eseguire l'accesso utente tramite [OpenID Connect](active-directory-v2-protocols.md). In OpenID Connect, l'app Web riceve un token ID. Un token ID è un token di sicurezza che verifica l'identità dell'utente e fornisce informazioni sull'utente sotto forma di attestazioni:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Ulteriori dettagli sui diversi tipi di token utilizzati nell'endpoint della piattaforma di identità Microsoft sono disponibili nel riferimento ai token di [accesso](access-tokens.md) e [id_token riferimento](id-tokens.md) ai token

Nelle app del server Web, il flusso di autenticazione dell'accesso esegue i passaggi generali seguenti:

![Mostra il flusso di autenticazione dell'app Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

È possibile garantire l'identità dell'utente convalidando il token ID con una chiave di firma pubblica ricevuta dall'endpoint della piattaforma di identità Microsoft.You can ensure the user's identity by validating the ID token with a public signing key that is received from the Microsoft identity platform endpoint. Viene anche impostato un cookie di sessione che può essere usato per identificare l'utente nelle richieste di pagina successive.

Per vedere questo scenario in azione, provare uno degli esempi di codice di accesso dell'app Web nella sezione introduttiva della piattaforma di [identità Microsoft.To](v2-overview.md#getting-started) see this scenario in action, try one of the web app sign-in code samples in the Microsoft identity platform getting started section.

Oltre al semplice accesso, un'app per server Web potrebbe dover accedere ad altri servizi Web, ad esempio a un'API REST. In questo caso, l'app per server Web agisce in un flusso di OpenID Connect e OAuth 2.0 combinato, tramite il [flusso del codice di autorizzazione OAuth 2.0](active-directory-v2-protocols.md). Per ulteriori informazioni su questo scenario, vedere Introduzione alle app Web e alle [API Web.](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

## <a name="web-apis"></a>API Web

È possibile usare l'endpoint della piattaforma di identità Microsoft per proteggere i servizi Web, ad esempio l'API Web RESTful dell'app. Le API Web possono essere implementate in numerose piattaforme e linguaggi. Possono anche essere implementati usando i trigger HTTP in Funzioni di Azure.They can also be implemented using HTTP Triggers in Azure Functions. Invece dei token ID e dei cookie di sessione, un'API Web utilizza un token di accesso OAuth 2.0 per proteggere i dati e autenticare le richieste in ingresso. Il chiamante di un'API Web aggiunge un token di accesso nell'intestazione di autorizzazione di una richiesta HTTP, in questo modo:The caller of a web API appends an access token in the authorization header of an HTTP request, like this:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web utilizza il token di accesso per verificare l'identità del chiamante API ed estrarre informazioni sul chiamante dalle attestazioni codificate nel token di accesso. Ulteriori dettagli sui diversi tipi di token utilizzati nell'endpoint della piattaforma di identità Microsoft sono disponibili nel riferimento ai token di accesso e nel [riferimento a id_token i token](id-tokens.md) di [accesso.](access-tokens.md)

Un'API Web può dare agli utenti la possibilità di acconsentire esplicitamente o rifiutare esplicitamente funzionalità o dati specifici esponendo le autorizzazioni, note anche come [ambiti.](v2-permissions-and-consent.md) Per far sì che un'app chiamante acquisisca l'autorizzazione ad accedere a un ambito, l'utente deve fornire il consenso all'ambito durante un flusso. L'endpoint della piattaforma di identità Microsoft richiede all'utente l'autorizzazione e quindi registra le autorizzazioni in tutti i token di accesso ricevuti dall'API Web. L'API Web convalida i token di accesso ricevuti a ogni chiamata ed esegue i controlli di autorizzazione.

Un'API Web può ricevere token di accesso da tutti i tipi di app, tra cui app server Web, app desktop e per dispositivi mobili, app a pagina singola, daemon sul lato server e anche altre API Web. Il flusso di alto livello per un'API Web è simile al seguente:The high-level flow for a web API looks like this:

![Mostra il flusso di autenticazione dell'API Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Per informazioni su come proteggere un'API Web usando i token di accesso OAuth2, consulta gli esempi di codice delle API Web nella sezione introduttiva della piattaforma di [identità Microsoft.To](v2-overview.md#getting-started) learn how to secure a web API by using OAuth2 access tokens, check out the web API code samples in the Microsoft identity platform getting started section.

In molti casi, le API Web devono anche effettuare richieste in uscita ad altre API Web downstream protette dalla piattaforma di identità Microsoft.In many cases, web APIs also need to make outbound requests to other downstream web APIs secured by Microsoft identity platform. A tale scopo, le API Web possono sfruttare il flusso **Per conto di,** che consente all'API Web di scambiare un token di accesso in ingresso per un altro token di accesso da utilizzare nelle richieste in uscita. Per altre info, vedi Piattaforma di [identità Microsoft e flusso di oAuth 2.0 per conto di](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>App per dispositivi mobili e native

Le app installate dai dispositivi, ad esempio le app per dispositivi mobili e desktop, spesso devono accedere ai servizi back-end o alle API Web che archiviano i dati ed eseguono funzioni per conto di un utente. Queste app possono aggiungere accesso e autorizzazioni ai servizi back-end tramite il [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md).

In questo flusso, l'app riceve un codice di autorizzazione dall'endpoint della piattaforma di identità Microsoft quando l'utente esegue l'accesso. Questo codice rappresenta l'autorizzazione dell'app a chiamare servizi back-end per conto dell'utente che ha eseguito l'accesso. L'app può scambiare il codice di autorizzazione in background con un token di accesso OAuth 2.0 e un token di aggiornamento. L'app può usare il token di accesso per l'autenticazione alle API Web nelle richieste HTTP e usare il token di aggiornamento per ottenere nuovi token di accesso alla scadenza dei token di accesso meno recenti.

![Mostra il flusso di autenticazione dell'app nativa](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>App daemon e lato server

Anche le app con processi a esecuzione prolungata o che operano senza interazione con un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web.Apps that have long-running processes or that operate without interaction with a user also need a way to access secured resources, such as web APIs. Queste app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, con il flusso delle credenziali client di OAuth 2.0. È possibile dimostrare l'identità dell'app usando un certificato o un segreto client. Per ulteriori informazioni, vedere [Applicazione console daemon .NET Core che utilizza Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

In questo flusso, l'app `/token` interagisce direttamente con l'endpoint per ottenere l'accesso:In this flow, the app interacts directly with the endpoint to obtain access:

![Mostra il flusso di autenticazione dell'app daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Per compilare un'app daemon, vedere la [documentazione sulle credenziali client](v2-oauth2-client-creds-grant-flow.md) oppure provare un'[app .NET di esempio](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
