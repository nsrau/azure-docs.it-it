---
title: Tipi di applicazioni per la piattaforma di identità Microsoft | Azure
description: Tipi di app e scenari supportati dall'endpoint della piattaforma Microsoft Identity (v 2.0).
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
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6799e604b9e5e2acc3af35e4038ea6f14271d5c8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834724"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Tipi di applicazioni per la piattaforma di identità Microsoft

L'endpoint di Microsoft Identity Platform (v2.0) supporta l'autenticazione per un'ampia gamma di architetture di app moderne, tutte basate sui protocolli standard del settore [OAuth 2.0 o OpenID Connect](active-directory-v2-protocols.md). Questo articolo descrive i tipi di app che è possibile compilare usando la piattaforma di identità Microsoft, indipendentemente dal linguaggio o dalla piattaforma preferita. Le informazioni sono progettate per semplificare la comprensione degli scenari di alto livello prima di [iniziare a usare il codice](v2-overview.md#getting-started).

> [!NOTE]
> L'endpoint della piattaforma Microsoft Identity non supporta tutti gli scenari e le funzionalità di Azure Active Directory (Azure AD). Per determinare se è necessario usare l'endpoint della piattaforma Microsoft Identity, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Nozioni di base

È necessario registrare ogni app che usa l'endpoint della piattaforma di identità Microsoft nel nuovo [portale di registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908). Il processo di registrazione delle app raccoglie e assegna all'app questi valori:

* ID di un' **applicazione (client)** che identifica in modo univoco l'app
* Un **URI di reindirizzamento** che può essere usato per reindirizzare le risposte all'app
* Altri valori specifici dello scenario, ad esempio i tipi di conto supportati

Per i dettagli, vedere come [registrare un'app](quickstart-register-app.md).

Dopo la registrazione dell'app, l'app comunica con la piattaforma di identità Microsoft inviando richieste all'endpoint. Microsoft offre framework e librerie open source che gestiscono i dettagli di queste richieste. È sempre possibile implementare personalmente la logica di autenticazione creando richieste a questi endpoint:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>App a singola pagina (JavaScript)

Molte app moderne hanno un front-end dell'app a singola pagina scritto principalmente in JavaScript. Spesso viene scritto usando un Framework come angolare, React o VME. L'endpoint della piattaforma Microsoft Identity supporta queste app usando il [flusso implicito OAuth 2,0](v2-oauth2-implicit-grant-flow.md).

In questo flusso, l'app riceve i token direttamente dall'endpoint di autorizzazione della piattaforma di identità Microsoft, senza scambi da server a server. In questo modo, tutta la logica di autenticazione e gestione della sessione avviene interamente nel client JavaScript, senza eseguire reindirizzamenti a pagine aggiuntive.

![Mostra il flusso di autenticazione implicita](./media/v2-app-types/convergence-scenarios-implicit.svg)

Per vedere questo scenario in azione, provare uno degli esempi di codice dell'app a singola pagina nella sezione introduttiva della [piattaforma Microsoft Identity](v2-overview.md#getting-started) .

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

Altre informazioni sui diversi tipi di token usati nell'endpoint della piattaforma Microsoft Identity sono disponibili nelle informazioni di riferimento sui [token di accesso](access-tokens.md) e [token ID](id-tokens.md)

Nelle app del server Web, il flusso di autenticazione dell'accesso esegue i passaggi generali seguenti:

![Mostra il flusso di autenticazione dell'app Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

È possibile verificare l'identità dell'utente convalidando il token ID con una chiave di firma pubblica ricevuta dall'endpoint della piattaforma di identità Microsoft. Viene anche impostato un cookie di sessione che può essere usato per identificare l'utente nelle richieste di pagina successive.

Per vedere questo scenario in azione, provare uno degli esempi di codice di accesso all'app Web nella sezione introduttiva della [piattaforma Microsoft Identity](v2-overview.md#getting-started) .

Oltre al semplice accesso, un'app per server Web potrebbe dover accedere ad altri servizi Web, ad esempio a un'API REST. In questo caso, l'app per server Web agisce in un flusso di OpenID Connect e OAuth 2.0 combinato, tramite il [flusso del codice di autorizzazione OAuth 2.0](active-directory-v2-protocols.md). Per altre informazioni su questo scenario, vedere l'[introduzione alle app Web e alle API Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>API Web

È possibile usare l'endpoint di Microsoft Identity Platform per proteggere i servizi Web, ad esempio l'API Web RESTful dell'app. Al posto dei token ID e dei cookie di sessione, un'API Web usa un token di accesso OAuth 2.0 per proteggere i dati e autenticare le richieste in ingresso. Il chiamante di un'API Web aggiunge un token di accesso nell'intestazione dell'autorizzazione di una richiesta HTTP come illustrato di seguito:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web usa il token di accesso per verificare l'identità del chiamante dell'API ed estrarre informazioni su quest'ultimo dalle attestazioni codificate nel token di accesso. Altre informazioni sui diversi tipi di token usati nell'endpoint della piattaforma Microsoft Identity sono disponibili nelle informazioni di riferimento sui [token di accesso](access-tokens.md) e [token ID](id-tokens.md)

Un'API Web può consentire agli utenti di fornire il consenso o rifiutare esplicitamente specifici dati o funzionalità esponendo le autorizzazioni, note anche come [ambiti](v2-permissions-and-consent.md). Per far sì che un'app chiamante acquisisca l'autorizzazione ad accedere a un ambito, l'utente deve fornire il consenso all'ambito durante un flusso. L'endpoint della piattaforma Microsoft Identity chiede all'utente l'autorizzazione e quindi registra le autorizzazioni in tutti i token di accesso ricevuti dall'API Web. L'API Web convalida i token di accesso ricevuti ad ogni chiamata ed esegue i controlli di autorizzazione appropriati.

Un'API Web può ricevere token di accesso da tutti i tipi di app, tra cui app per server Web, desktop, per dispositivi mobili, a singola pagina, daemon sul lato server e anche altre API Web. Il flusso generale per un'API Web è simile al seguente:

![Mostra il flusso di autenticazione dell'API Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Per informazioni su come proteggere un'API Web usando i token di accesso OAuth2, vedere gli esempi di codice dell'API Web nella sezione [Introduzione a Microsoft Identity Platform](v2-overview.md#getting-started) .

In molti casi, le API Web devono anche effettuare richieste in uscita ad altre API Web downstream protette dalla piattaforma di identità Microsoft. A tale scopo, le API Web possono sfruttare il flusso per **conto di** , che consente all'API Web di scambiare un token di accesso in ingresso per un altro token di accesso da usare nelle richieste in uscita. Per altre informazioni, vedere [Microsoft Identity Platform e OAuth 2,0 on-conto-of Flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>App per dispositivi mobili e native

Le app installate in un dispositivo, ad esempio app desktop e per dispositivi mobili, devono spesso accedere a servizi back-end o ad API Web che archiviano i dati ed eseguono funzioni per conto dell'utente. Queste app possono aggiungere accesso e autorizzazioni ai servizi back-end tramite il [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md).

In questo flusso, l'app riceve un codice di autorizzazione dall'endpoint della piattaforma di identità Microsoft quando l'utente esegue l'accesso. Questo codice rappresenta l'autorizzazione dell'app a chiamare servizi back-end per conto dell'utente che ha eseguito l'accesso. L'app può scambiare il codice di autorizzazione in background con un token di accesso OAuth 2.0 e un token di aggiornamento. L'app può usare il token di accesso per l'autenticazione all'API Web nelle richieste HTTP e il token di aggiornamento per ottenere nuovi token di accesso quando i precedenti scadono.

![Mostra il flusso di autenticazione dell'app nativa](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>App daemon e lato server

Anche le app che contengono processi a esecuzione prolungata o che non prevedono l'interazione con l'utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Queste app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, con il flusso delle credenziali client di OAuth 2.0. È possibile dimostrare l'identità dell'app usando un certificato o un segreto client. Per altre informazioni, vedere [autenticazione alla piattaforma di identità Microsoft nelle app daemon con certificati](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

In questo flusso, l'app interagisce direttamente con l' `/token` endpoint per ottenere l'accesso:

![Mostra il flusso di autenticazione dell'app daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Per compilare un'app daemon, vedere la [documentazione sulle credenziali client](v2-oauth2-client-creds-grant-flow.md) oppure provare un'[app .NET di esempio](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
