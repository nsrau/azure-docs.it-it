---
title: Azure AD B2C | Documentazione Microsoft
description: "Tipi di applicazioni che è possibile compilare in Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: bb9d4abe-0db7-4bd9-b0c4-2f43b2c9cf33
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: dastrock
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 762af7e09342f1bb51352e6c3d104bd4d8944e65
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C: tipi di applicazioni
Azure Active Directory (Azure AD) B2C supporta l'autenticazione per un'ampia gamma di architetture di app moderne. Tutte le architetture sono basate sui protocolli standard [OAuth 2.0](active-directory-b2c-reference-protocols.md) o [OpenID Connect](active-directory-b2c-reference-protocols.md). Questo documento descrive brevemente i tipi di app che è possibile compilare, indipendente dal linguaggio o dalla piattaforma preferita. Illustra anche gli scenari generali per [iniziare a compilare applicazioni](active-directory-b2c-overview.md#get-started).

## <a name="the-basics"></a>Nozioni di base
Ogni app che usa Azure AD B2C deve essere registrata nella [directory B2C](active-directory-b2c-get-started.md) con il [portale di Azure](https://portal.azure.com/). Il processo di registrazione app raccoglie e assegna all'app alcuni valori:

* Un **ID applicazione** che identifica l'app in modo univoco.
* Un **URI di reindirizzamento** che può essere usato per indirizzare le risposte all'app.
* Altri valori specifici dello scenario. Per altri dettagli, vedere [Azure Active Directory B2C: registrare l'applicazione](active-directory-b2c-app-registration.md).

Dopo la registrazione, l'app comunica con Azure AD inviando richieste all'endpoint Azure AD 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Ogni richiesta inviata ad Azure AD B2C specifica un **criterio**. Il comportamento di Azure AD è regolato da criteri. Gli endpoint possono anche essere usati per creare un set personalizzabile di esperienze utente. Tra i criteri comuni sono inclusi quelli per l'iscrizione, l'accesso e la modifica del profilo. Se non si ha familiarità con i criteri, prima di continuare, vedere [Azure Active Directory B2C: framework di criteri estendibile](active-directory-b2c-reference-policies.md).

Ogni interazione di app con l'endpoint 2.0 segue un modello generale simile al seguente:

1. L'app indirizza l'utente all'endpoint 2.0 per l'esecuzione dei [criteri](active-directory-b2c-reference-policies.md).
2. L'utente completa i criteri in base alla relativa definizione.
3. L'app riceve un token di sicurezza dall'endpoint 2.0.
4. L'app usa il token di sicurezza per accedere a informazioni protette o a una risorsa protetta.
5. Il server delle risorse convalida il token di sicurezza per verificare che sia possibile concedere l'accesso.
6. L'app aggiorna periodicamente il token di sicurezza.

<!-- TODO: Need a page for libraries to link to -->
Questi passaggi possono presentare lievi differenze a seconda del tipo di applicazione che si sta compilando. Le librerie open source consentono di risolvere i dettagli.

## <a name="web-apps"></a>App Web
Per app Web (ad esempio .NET, PHP, Java, Ruby, Python e Node.js) ospitate in un server e accessibili tramite un browser, Azure AD B2C supporta [OpenID Connect](active-directory-b2c-reference-protocols.md) per tutte le esperienze utente, inclusa la gestione dell'iscrizione, dell'accesso e del profilo utente. Nell'implementazione di OpenID Connect in Azure AD B2C l'app Web avvia queste esperienze utente inviando richieste di autenticazione ad Azure AD. Il risultato della richiesta è un `id_token`. Questo token di sicurezza rappresenta l'identità dell'utente e fornisce informazioni sull'utente sotto forma di attestazioni:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Per altre informazioni sui tipi di token e attestazioni disponibili per un'app, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).

Nelle app Web, ogni esecuzione di [criteri](active-directory-b2c-reference-policies.md) include i passaggi generali seguenti:

![Immagine di corsie di app Web](./media/active-directory-b2c-apps/webapp.png)

La convalida di `id_token` con una chiave di firma pubblica ricevuta da Azure AD è sufficiente per verificare l'identità dell'utente. Questo imposta anche un cookie di sessione che può essere usato per identificare l'utente nelle richieste di pagina successive.

Per osservare il funzionamento di questo scenario, provare uno degli esempi di codice di accesso per app Web nella sezione [Introduzione](active-directory-b2c-overview.md#get-started).

Oltre al semplice accesso, un'app per server Web può anche avere bisogno di accedere a un servizio Web back-end. In questo caso, l'app Web può seguire un [flusso di OpenID Connect](active-directory-b2c-reference-oidc.md) leggermente diverso e acquisire i token usando codici di autorizzazione e token di aggiornamento. Questo scenario è illustrato di seguito nella sezione [API Web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>API Web
È possibile usare Azure AD B2C per proteggere i servizi Web, ad esempio l'API Web RESTful dell'app. Le API Web possono usare OAuth 2.0 per proteggere i propri dati, autenticando le richieste HTTP in ingresso con token. Il chiamante di un'API Web aggiunge un token all'intestazione dell'autorizzazione di una richiesta HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web può quindi usare il token per verificare l'identità del chiamante dell'API ed estrarre informazioni su quest'ultimo dalle attestazioni codificate nel token. Per altre informazioni sui tipi di token e attestazioni disponibili per un'app, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> Azure AD B2C supporta attualmente solo API Web accessibili da propri client noti. Ad esempio, l'app completa può includere un'app per iOS, un'app per Android e un'API Web back-end. Questa architettura è completamente supportata. L'accesso alla stessa API Web da parte di un client di partner, ad esempio un'altra app iOS, attualmente non è supportato. Tutti i componenti dell'app completa devono condividere un singolo ID applicazione.
>
>

Un'API Web può ricevere token da molti tipi di client, tra cui app Web, app desktop e per dispositivi mobili, app a singola pagina, daemon sul lato server e altre API Web. Di seguito è riportato un esempio di flusso completo di un'app Web che chiama un'API Web.

![Immagine di corsie di API Web di app Web](./media/active-directory-b2c-apps/webapi.png)

Per altre informazioni su codici di autorizzazione, token di aggiornamento e procedure di recupero dei token, vedere l'articolo relativo al [protocollo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Per informazioni su come proteggere un'API Web con Azure AD B2C, vedere le esercitazioni relative alle API Web nella sezione [Introduzione](active-directory-b2c-overview.md#get-started).

## <a name="mobile-and-native-apps"></a>App per dispositivi mobili e native
Le app installate nei dispositivi, ad esempio le app desktop e per dispositivi mobili, devono spesso accedere a servizi back-end o ad API Web per conto degli utenti. È possibile aggiungere esperienze personalizzate di gestione delle identità alle app native ed eseguire chiamate sicure ai servizi back-end usando Azure AD B2C e il [flusso del codice di autorizzazione OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

In questo flusso l'app esegue i [criteri](active-directory-b2c-reference-policies.md) e riceve un `authorization_code` da Azure AD dopo che l'utente ha completato i criteri. L'oggetto `authorization_code` rappresenta l'autorizzazione dell'app a chiamare servizi back-end per conto dell'utente che ha eseguito l'accesso. L'app può quindi scambiare l'oggetto `authorization_code` in background con un `id_token` e un `refresh_token`.  L'app può usare l'oggetto `id_token` per eseguire l'autenticazione a un'API Web back-end nelle richieste HTTP. Può anche usare `refresh_token` per ottenere un nuovo `id_token` alla scadenza di quello precedente.

> [!NOTE]
> Azure AD B2C attualmente supporta solo i token usati per accedere al servizio Web back-end di un'app. Ad esempio, l'app completa può includere un'app per iOS, un'app per Android e un'API Web back-end. Questa architettura è completamente supportata. L'accesso a un'API Web di partner da parte di un'app iOS con i token di accesso OAuth 2.0 attualmente non è supportato. Tutti i componenti dell'app completa devono condividere un singolo ID applicazione.
>
>

![Immagine di corsie di app native](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitazioni correnti
Azure AD B2C attualmente non supporta i tipi di app seguenti, ma tale supporto è in programma. 

### <a name="daemonsserver-side-apps"></a>App sul lato server o daemon
Anche le app che contengono processi a esecuzione prolungata o che funzionano senza la presenza di un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Tali app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, e il flusso delle credenziali client di OAuth 2.0.

Questo flusso non è attualmente supportato da Azure AD B2C. Le app possono ottenere i token solo dopo che si è verificato un flusso utente interattivo.

### <a name="web-api-chains-on-behalf-of-flow"></a>Catene di API Web (flusso On-Behalf-Of)
Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client nativi che hanno un back-end dell'API Web. Quest'ultimo chiama a sua volta un servizio online Microsoft, ad esempio l'API Graph di Azure AD.

Questo scenario di API Web concatenata può essere supportato usando la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of.  Il flusso On-Behalf-Of, tuttavia, non è attualmente implementato in Azure AD B2C.

