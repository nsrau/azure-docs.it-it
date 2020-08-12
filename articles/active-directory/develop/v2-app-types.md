---
title: Tipi di applicazioni per Microsoft Identity Platform | Azure
description: Tipi di app e scenari supportati dall'endpoint di Microsoft Identity Platform (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7b89add55a060c7ba0ef9488f1f6438090b8d3d2
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121171"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Tipi di applicazioni per Microsoft Identity Platform

L'endpoint di Microsoft Identity Platform (v2.0) supporta l'autenticazione per un'ampia gamma di architetture di app moderne, tutte basate sui protocolli standard del settore [OAuth 2.0 o OpenID Connect](active-directory-v2-protocols.md). Questo articolo descrive brevemente i tipi di app che è possibile compilare usando Microsoft Identity Platform, indipendentemente dal linguaggio o dalla piattaforma preferita. Le informazioni consentono di comprendere gli scenari generali prima di [iniziare a usare il codice](v2-overview.md#getting-started).

## <a name="the-basics"></a>Nozioni di base

È necessario registrare ogni app che usa l'endpoint di Microsoft Identity Platform in [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) nel portale di Azure. Il processo di registrazione delle app raccoglie e assegna all'app questi valori:

* Un **ID applicazione (client)**  che identifica l'app in modo univoco
* Un **URI di reindirizzamento** che può essere usato per reindirizzare le risposte all'app
* Altri valori specifici dello scenario, ad esempio i tipi di account supportati

Per i dettagli, vedere come [registrare un'app](quickstart-register-app.md).

Dopo la registrazione, l'app comunica con Microsoft Identity Platform inviando richieste all'endpoint. Microsoft offre framework e librerie open source che gestiscono i dettagli di queste richieste. È sempre possibile implementare personalmente la logica di autenticazione creando richieste a questi endpoint:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>App a singola pagina (JavaScript)

Molte app moderne hanno un front-end dell'app a singola pagina scritto principalmente in JavaScript, spesso con un framework come Angular, React o Vue. L'endpoint di Microsoft Identity Platform supporta queste app usando il [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md).

In questo flusso, l'app riceve un codice dall’endpoint di Microsoft Identity Platform `authorize` e lo riscatta per i token e i token di aggiornamento usando le richieste Web tra siti. Il token di aggiornamento scade ogni 24 ore e l'app deve richiedere un altro codice.

![Flusso di codice per le applicazioni a pagina singola](media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.png)

Per osservare il funzionamento di questo scenario, consultare l’[Esercitazione: Eseguire l'accesso degli utenti e chiamare l'API Microsoft Graph da un'applicazione a pagina singola JavaScript usando il flusso del codice di autenticazione](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Flusso del codice di autorizzazione e flusso implicito

Per la maggior parte dei casi nella cronologia di OAuth 2.0, il [flusso implicito](v2-oauth2-implicit-grant-flow.md) era il metodo consigliato per creare app a pagina singola. Con la rimozione dei [cookie di terze parti](reference-third-party-cookies-spas.md) e la [maggiore attenzione](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) posta sui problemi di sicurezza relativi al flusso implicito, è stato adottato il flusso del codice di autorizzazione per le app a pagina singola.

Per garantire la compatibilità dell'app in Safari e in altri browser compatibili con le funzionalità di privacy, non è più consigliabile usare il flusso implicito ma si consiglia invece di utilizzare il flusso del codice di autorizzazione.

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

Altre informazioni sui diversi tipi di token usati nell'endpoint di Microsoft Identity Platform sono disponibili nelle informazioni di riferimento per il [token di accesso](access-tokens.md) e per [id_token](id-tokens.md)

Nelle app del server Web, il flusso di autenticazione dell'accesso esegue i passaggi generali seguenti:

![Mostra il flusso di autenticazione dell'app Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

È possibile verificare l'identità dell'utente convalidando il token ID con una chiave di firma pubblica ricevuta dall'endpoint di Microsoft Identity Platform. Viene anche impostato un cookie di sessione che può essere usato per identificare l'utente nelle richieste di pagina successive.

Per osservare il funzionamento di questo scenario, provare uno degli esempi di codice di accesso per app Web nella [sezione introduttiva a Microsoft Identity Platform](v2-overview.md#getting-started).

Oltre al semplice accesso, un'app per server Web potrebbe dover accedere ad altri servizi Web, ad esempio a un'API REST. In questo caso, l'app per server Web agisce in un flusso di OpenID Connect e OAuth 2.0 combinato, tramite il [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md). Per altre informazioni su questo scenario, vedere l'[introduzione alle app Web e alle API Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet).


## <a name="web-apis"></a>API Web

È possibile usare l'endpoint di Microsoft Identity Platform per proteggere i servizi Web, ad esempio l'API Web RESTful dell'app. Le API Web possono essere implementate in numerose piattaforme e linguaggi. Possono inoltre essere implementate usando trigger HTTP in Funzioni di Azure. Al posto dei token ID e dei cookie di sessione, un'API Web usa un token di accesso OAuth 2.0 per proteggere i dati e autenticare le richieste in ingresso. Il chiamante di un'API Web aggiunge un token di accesso nell'intestazione dell'autorizzazione di una richiesta HTTP come illustrato di seguito:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web usa il token di accesso per verificare l'identità del chiamante dell'API ed estrarre informazioni su quest'ultimo dalle attestazioni codificate nel token di accesso. Altre informazioni sui diversi tipi di token usati nell'endpoint di Microsoft Identity Platform sono disponibili nelle informazioni di riferimento per il [token di accesso](access-tokens.md) e per [id_token](id-tokens.md).

Un'API Web può consentire agli utenti di fornire il consenso o rifiutare esplicitamente specifici dati o funzionalità esponendo le autorizzazioni, note anche come [ambiti](v2-permissions-and-consent.md). Per far sì che un'app chiamante acquisisca l'autorizzazione ad accedere a un ambito, l'utente deve fornire il consenso all'ambito durante un flusso. L'endpoint di Microsoft Identity Platform chiede l'autorizzazione all'utente e quindi registra le autorizzazioni nei token di accesso ricevuti dall'API Web. L'API Web convalida i token di accesso ricevuti ad ogni chiamata ed esegue i controlli di autorizzazione appropriati.

Un'API Web può ricevere token di accesso da tutti i tipi di app, tra cui app per server Web, desktop, per dispositivi mobili, a singola pagina, daemon sul lato server e anche altre API Web. Il flusso generale per un'API Web è simile al seguente:

![Mostra il flusso di autenticazione dell'API Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Per informazioni su come proteggere un'API Web con i token di accesso OAuth2, vedere gli esempi di codice dell'API Web nella [sezione introduttiva a Microsoft Identity Platform](v2-overview.md#getting-started).

In molti casi, le API Web devono anche effettuare richieste in uscita ad altre API Web downstream protette da Microsoft Identity Platform. A questo scopo, le API Web possono ricorrere al flusso **On-Behalf-Of**, che consente all'API Web di scambiare un token di accesso in ingresso con un altro token di accesso da usare in richieste in uscita. Per altre informazioni, vedere [Microsoft Identity Platform e flusso On-Behalf-Of di OAuth 2.0](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>App per dispositivi mobili e native

Le app installate in un dispositivo, ad esempio app desktop e per dispositivi mobili, devono spesso accedere a servizi back-end o ad API Web che archiviano i dati ed eseguono funzioni per conto dell'utente. Queste app possono aggiungere accesso e autorizzazioni ai servizi back-end tramite il [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md).

In questo flusso, l'app riceve un codice di autorizzazione dall'endpoint di Microsoft Identity Platform quando l'utente effettua l'accesso. Questo codice rappresenta l'autorizzazione dell'app a chiamare servizi back-end per conto dell'utente che ha eseguito l'accesso. L'app può scambiare il codice di autorizzazione in background con un token di accesso OAuth 2.0 e un token di aggiornamento. L'app può usare il token di accesso per l'autenticazione all'API Web nelle richieste HTTP e il token di aggiornamento per ottenere nuovi token di accesso quando i precedenti scadono.

![Mostra il flusso di autenticazione dell'app nativa](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>App daemon e lato server

Anche le app che contengono processi a esecuzione prolungata o che non prevedono l'interazione con l'utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Queste app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, con il flusso delle credenziali client di OAuth 2.0. È possibile dimostrare l'identità dell'app usando un certificato o un segreto client. Per altre informazioni, vedere [Applicazione console daemon .NET Core con Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

In questo flusso, l'app interagisce direttamente con l'endpoint `/token` per ottenere l’accesso:

![Mostra il flusso di autenticazione dell'app daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Per compilare un'app daemon, vedere la [documentazione sulle credenziali client](v2-oauth2-client-creds-grant-flow.md) oppure provare un'[app .NET di esempio](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="next-steps"></a>Passaggi successivi

Una volta acquisita familiarità con i tipi di applicazioni supportati da Microsoft Identity Platform, è possibile consultare altre informazioni su [OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) per ottenere dettagli sui componenti dei protocolli usati dai diversi scenari.