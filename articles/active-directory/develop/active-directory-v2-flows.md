---
title: Tipi di app per l'endpoint v2.0 di Azure Active Directory | Documentazione Microsoft
description: Tipi di app e scenari supportati dall'endpoint v2.0 di Azure Active Directory.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 9d59e7f0e8f326c40be86e199d7712f6c565cc13
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
<a id="app-types-for-the-azure-active-directory-v20-endpoint" class="xliff"></a>

# Tipi di app per l'endpoint v2.0 di Azure Active Directory
L'endpoint v2.0 di Azure Active Directory supporta l'autenticazione di un'ampia gamma di architetture di app moderne, basate sui protocolli standard del settore [OAuth 2.0 o OpenID Connect](active-directory-v2-protocols.md). Questo articolo descrive brevemente i tipi di app che è possibile compilare usando Azure AD v2.0, indipendentemente dal linguaggio o dalla piattaforma preferita. Le informazioni contenute in questo articolo consentono di comprendere gli scenari generali prima di [iniziare a usare il codice](active-directory-appmodel-v2-overview.md#getting-started).

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint 2.0, vedere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).
> 
> 

<a id="the-basics" class="xliff"></a>

## Nozioni di base
È necessario registrare ogni app che usa l'endpoint v 2.0 nel [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com). Il processo di registrazione delle app raccoglie e assegna all'app questi valori:

* Un **ID applicazione** che identifica l'app in modo univoco
* Un **URI di reindirizzamento** che può essere usato per reindirizzare le risposte all'app
* Altri valori specifici dello scenario

Per i dettagli, vedere come [registrare un'app](active-directory-v2-app-registration.md).

Dopo la registrazione, l'app comunica con Azure AD inviando richieste all'endpoint di Azure AD 2.0. Microsoft offre framework e librerie open source che gestiscono i dettagli di queste richieste. È sempre possibile implementare personalmente la logica di autenticazione creando richieste a questi endpoint:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

<a id="web-apps" class="xliff"></a>

## App Web
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

Per informazioni su tutti i tipi di token e attestazioni disponibili per un'app, vedere le [informazioni di riferimento sui token 2.0](active-directory-v2-tokens.md).

Nelle app del server Web, il flusso di autenticazione dell'accesso esegue i passaggi generali seguenti:

![Flusso di autenticazione dell'app Web](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

È possibile verificare l'identità dell'utente convalidando il token ID con una chiave di firma pubblica ricevuta dall'endpoint 2.0. Viene anche impostato un cookie di sessione che può essere usato per identificare l'utente nelle richieste di pagina successive.

Per osservare il funzionamento di questo scenario, provare uno degli esempi di codice di accesso per app Web nella sezione [Introduzione](active-directory-appmodel-v2-overview.md#getting-started) per la versione 2.0.

Oltre al semplice accesso, un'app per server Web potrebbe dover accedere ad altri servizi Web, ad esempio a un'API REST. In questo caso, l'app per server Web agisce in un flusso di OpenID Connect e OAuth 2.0 combinato, tramite il [flusso del codice di autorizzazione OAuth 2.0](active-directory-v2-protocols.md). Per altre informazioni su questo scenario, vedere l'[introduzione alle app Web e alle API Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

<a id="web-apis" class="xliff"></a>

## API Web
È possibile usare l'endpoint v2.0 per proteggere i servizi Web, ad esempio l'API Web RESTful dell'app. Al posto dei token ID e dei cookie di sessione, un'API Web usa un token di accesso OAuth 2.0 per proteggere i dati e autenticare le richieste in ingresso. Il chiamante di un'API Web aggiunge un token di accesso nell'intestazione dell'autorizzazione di una richiesta HTTP come illustrato di seguito:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web usa il token di accesso per verificare l'identità del chiamante dell'API ed estrarre informazioni su quest'ultimo dalle attestazioni codificate nel token di accesso. Per informazioni su tutti i tipi di token e attestazioni disponibili per un'app, vedere le [informazioni di riferimento sui token 2.0](active-directory-v2-tokens.md).

Un'API Web può consentire agli utenti di fornire il consenso o rifiutare esplicitamente specifici dati o funzionalità esponendo le autorizzazioni, note anche come [ambiti](active-directory-v2-scopes.md). Per far sì che un'app chiamante acquisisca l'autorizzazione ad accedere a un ambito, l'utente deve fornire il consenso all'ambito durante un flusso. L'endpoint 2.0 chiede l'autorizzazione all'utente e quindi registra le autorizzazioni nei token di accesso ricevuti dall'API Web. L'API Web convalida i token di accesso ricevuti ad ogni chiamata ed esegue i controlli di autorizzazione appropriati.

Un'API Web può ricevere token di accesso da tutti i tipi di app, tra cui app per server Web, desktop, per dispositivi mobili, a singola pagina, daemon sul lato server e anche altre API Web. Il flusso generale per un'API Web è simile al seguente:

![Flusso di autenticazione dell'API Web](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Per informazioni su come proteggere un'API Web con i token di accesso OAuth2, vedere gli esempi di codice dell'API Web nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).

In molti casi, le API Web devono anche effettuare richieste in uscita ad altre API Web downstream protette da Azure Active Directory.  A questo scopo, le API Web possono ricorrere al flusso **Per conto di** di Azure AD, che consente all'API Web di scambiare un token di accesso in ingresso con un altro token di accesso da usare in richieste in uscita.  Il flusso Per conto di dell'endpoint 2.0 è descritto nel [dettaglio qui](active-directory-v2-protocols-oauth-on-behalf-of.md).

<a id="mobile-and-native-apps" class="xliff"></a>

## App per dispositivi mobili e native
Le app installate in un dispositivo, ad esempio app desktop e per dispositivi mobili, devono spesso accedere a servizi back-end o ad API Web che archiviano i dati ed eseguono funzioni per conto dell'utente. Queste app possono aggiungere accesso e autorizzazioni ai servizi back-end tramite il [flusso del codice di autorizzazione OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

In questo flusso, l'app riceve un codice di autorizzazione dall'endpoint 2.0 quando l'utente effettua l'accesso. Questo codice rappresenta l'autorizzazione dell'app a chiamare servizi back-end per conto dell'utente che ha eseguito l'accesso. L'app può scambiare il codice di autorizzazione in background con un token di accesso OAuth 2.0 e un token di aggiornamento. L'app può usare il token di accesso per l'autenticazione all'API Web nelle richieste HTTP e il token di aggiornamento per ottenere nuovi token di accesso quando i precedenti scadono.

![Flusso di autenticazione dell'app nativa](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

<a id="single-page-apps-javascript" class="xliff"></a>

## App a singola pagina (JavaScript)
Molte app moderne hanno un front-end dell'app a singola pagina scritto principalmente in JavaScript. Il front-end viene scritto spesso usando un framework come AngularJS, Ember.js o Durandal. L'endpoint di Azure AD 2.0 supporta queste app tramite il [flusso implicito OAuth 2.0](active-directory-v2-protocols-implicit.md).

In questo flusso, l'app riceve i token direttamente dall'endpoint di autorizzazione 2.0, senza eseguire scambi tra server. In questo modo, tutta la logica di autenticazione e gestione della sessione avviene interamente nel client JavaScript, senza eseguire reindirizzamenti a pagine aggiuntive.

![Flusso di autenticazione implicita](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Per visualizzare questo scenario, provare uno degli esempi di codice di applicazione a singola pagina nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).

<a id="daemons-and-server-side-apps" class="xliff"></a>

## App daemon e lato server
Anche le app che contengono processi a esecuzione prolungata o che non prevedono l'interazione con l'utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Queste app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, con il flusso delle credenziali client di OAuth 2.0.

In questo flusso, l'app interagisce direttamente con l'endpoint `/token` per ottenere gli endpoint:

![Flusso di autenticazione dell'app daemon](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Per compilare un'app daemon, vedere la documentazione sulle credenziali client nella sezione [Introduzione](active-directory-appmodel-v2-overview.md#getting-started) oppure provare un'[app di esempio .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

