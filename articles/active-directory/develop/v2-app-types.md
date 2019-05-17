---
title: Tipi di applicazioni per la piattaforma delle identità di Microsoft | Azure
description: I tipi di App e scenari supportati dall'endpoint di Microsoft identity platform (v2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ec10359c6f684b1d7fb008946b7ceca2ed626ba
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545368"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Tipi di applicazioni per la piattaforma delle identità Microsoft

L'endpoint di Microsoft identity platform (v2.0) supporta l'autenticazione per un'ampia gamma di architetture di app moderne, basate su protocolli standard del settore [OAuth 2.0 oppure OpenID Connect](active-directory-v2-protocols.md). Questo articolo descrive i tipi di App che è possibile compilare usando piattaforma delle identità Microsoft, indipendentemente dal proprio linguaggio preferito o piattaforma. Le informazioni sono progettate per aiutare a comprendere gli scenari generali prima [iniziare a usare il codice](v2-overview.md#getting-started).

> [!NOTE]
> L'endpoint di piattaforma di identità di Microsoft non supporta tutti gli scenari di Azure Active Directory (Azure AD) e le funzionalità. Per determinare se è necessario usare l'endpoint di piattaforma Microsoft identity, a conoscenza [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Nozioni di base

È necessario registrare ogni app che usa l'endpoint di piattaforma di identità Microsoft nel nuovo [portale di registrazioni per l'App](https://go.microsoft.com/fwlink/?linkid=2083908). Il processo di registrazione delle app raccoglie e assegna all'app questi valori:

* Un' **ID applicazione (client)** che identifica in modo univoco l'app
* Un **URI di reindirizzamento** che può essere usato per reindirizzare le risposte all'app
* Alcuni altri tipi di account supportati, ad esempio i valori specifici dello scenario

Per i dettagli, vedere come [registrare un'app](quickstart-register-app.md).

Dopo aver registrato l'app, l'app comunica con la piattaforma delle identità Microsoft inviando richieste all'endpoint. Microsoft offre framework e librerie open source che gestiscono i dettagli di queste richieste. È sempre possibile implementare personalmente la logica di autenticazione creando richieste a questi endpoint:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>App a singola pagina (JavaScript)

Molte app moderne hanno un front-end dell'app a singola pagina scritto principalmente in JavaScript. Il front-end viene scritto spesso usando un framework come AngularJS, Ember.js o Durandal. L'endpoint di Microsoft identity platform supporta queste App tramite il [flusso implicito OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

In questo flusso, l'app riceve i token direttamente da piattaforma delle identità Microsoft autorizza un endpoint, senza eventuali scambi server-to-server. In questo modo, tutta la logica di autenticazione e gestione della sessione avviene interamente nel client JavaScript, senza eseguire reindirizzamenti a pagine aggiuntive.

![Flusso di autenticazione implicita](./media/v2-app-types/convergence-scenarios-implicit.svg)

Per visualizzare questo scenario, provare uno degli esempi di codice app a pagina singola di [piattaforma delle identità Microsoft introduttiva](v2-overview.md#getting-started) sezione.

## <a name="web-apps"></a>App Web

Per le app Web (.NET, PHP, Java, Ruby, Python, Node) accessibili tramite un browser, è possibile eseguire l'accesso utente tramite [OpenID Connect](active-directory-v2-protocols.md). In OpenID Connect, l'app Web riceve un token ID. Un token ID è un token di sicurezza che verifica l'identità dell'utente e fornisce informazioni sull'utente sotto forma di attestazioni:

```
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

Sono disponibili in altri dettagli sui diversi tipi di token usati nell'endpoint della piattaforma Microsoft identity il [token di accesso](access-tokens.md) riferimento e [id_token riferimento](id-tokens.md)

Nelle app del server Web, il flusso di autenticazione dell'accesso esegue i passaggi generali seguenti:

![Flusso di autenticazione dell'app Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

È possibile verificare l'identità dell'utente convalidando il token ID con una chiave di firma pubblica ricevuta dall'endpoint di Microsoft identity platform. Viene anche impostato un cookie di sessione che può essere usato per identificare l'utente nelle richieste di pagina successive.

Per visualizzare questo scenario, provare uno degli esempi di codice di accesso delle app web nel [piattaforma delle identità Microsoft introduttiva](v2-overview.md#getting-started) sezione.

Oltre al semplice accesso, un'app per server Web potrebbe dover accedere ad altri servizi Web, ad esempio a un'API REST. In questo caso, l'app per server Web agisce in un flusso di OpenID Connect e OAuth 2.0 combinato, tramite il [flusso del codice di autorizzazione OAuth 2.0](active-directory-v2-protocols.md). Per altre informazioni su questo scenario, vedere l'[introduzione alle app Web e alle API Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>API Web

È possibile utilizzare l'endpoint di piattaforma di identità di Microsoft per proteggere i servizi web, ad esempio API Web RESTful dell'app. Al posto dei token ID e dei cookie di sessione, un'API Web usa un token di accesso OAuth 2.0 per proteggere i dati e autenticare le richieste in ingresso. Il chiamante di un'API Web aggiunge un token di accesso nell'intestazione dell'autorizzazione di una richiesta HTTP come illustrato di seguito:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web usa il token di accesso per verificare l'identità del chiamante dell'API ed estrarre informazioni su quest'ultimo dalle attestazioni codificate nel token di accesso. Sono disponibili in altri dettagli sui diversi tipi di token usati nell'endpoint della piattaforma Microsoft identity il [token di accesso](access-tokens.md) riferimento e [id_token riferimento](id-tokens.md)

Un'API Web può consentire agli utenti di fornire il consenso o rifiutare esplicitamente specifici dati o funzionalità esponendo le autorizzazioni, note anche come [ambiti](v2-permissions-and-consent.md). Per far sì che un'app chiamante acquisisca l'autorizzazione ad accedere a un ambito, l'utente deve fornire il consenso all'ambito durante un flusso. L'endpoint di Microsoft identity platform chiede all'utente l'autorizzazione e quindi Registra le autorizzazioni in tutti i token di accesso ricevuti dall'API Web. L'API Web convalida i token di accesso ricevuti ad ogni chiamata ed esegue i controlli di autorizzazione appropriati.

Un'API Web può ricevere token di accesso da tutti i tipi di app, tra cui app per server Web, desktop, per dispositivi mobili, a singola pagina, daemon sul lato server e anche altre API Web. Il flusso generale per un'API Web è simile al seguente:

![Flusso di autenticazione dell'API Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Per informazioni su come proteggere un'API Web usando token di accesso OAuth2, consultare gli esempi di codice API Web nel [piattaforma delle identità Microsoft introduttiva](v2-overview.md#getting-started) sezione.

In molti casi, le API web è necessario anche effettuare richieste in uscita per altri downstream API web protette da piattaforma delle identità Microsoft. A tale scopo, le API web può sfruttare il **On-Behalf-Of** flusso, che consente all'API web per lo scambio di token di accesso in ingresso per un altro token di accesso da utilizzare nelle richieste in uscita. Per altre informazioni, vedi [piattaforma delle identità Microsoft e il flusso di OAuth 2.0 On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>App per dispositivi mobili e native

Le app installate in un dispositivo, ad esempio app desktop e per dispositivi mobili, devono spesso accedere a servizi back-end o ad API Web che archiviano i dati ed eseguono funzioni per conto dell'utente. Queste app possono aggiungere accesso e autorizzazioni ai servizi back-end tramite il [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md).

In questo flusso, l'app riceve un codice di autorizzazione dall'endpoint di Microsoft identity platform quando l'utente esegue l'accesso. Questo codice rappresenta l'autorizzazione dell'app a chiamare servizi back-end per conto dell'utente che ha eseguito l'accesso. L'app può scambiare il codice di autorizzazione in background con un token di accesso OAuth 2.0 e un token di aggiornamento. L'app può usare il token di accesso per l'autenticazione all'API Web nelle richieste HTTP e il token di aggiornamento per ottenere nuovi token di accesso quando i precedenti scadono.

![Flusso di autenticazione dell'app nativa](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>App daemon e lato server

Anche le app che contengono processi a esecuzione prolungata o che non prevedono l'interazione con l'utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Queste app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, con il flusso delle credenziali client di OAuth 2.0. È possibile dimostrare l'identità dell'app usando un certificato o un segreto client. Per altre informazioni, vedi [l'autenticazione alla piattaforma delle identità Microsoft nelle App daemon con certificati](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

In questo flusso, l'app interagisce direttamente con il `/token` endpoint per ottenere l'accesso:

![Flusso di autenticazione dell'app daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Per compilare un'app daemon, vedere la [documentazione sulle credenziali client](v2-oauth2-client-creds-grant-flow.md) oppure provare un'[app .NET di esempio](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
