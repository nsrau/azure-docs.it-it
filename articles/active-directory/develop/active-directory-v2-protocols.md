---
title: Protocolli OAuth 2.0 e OpenID Connect su Microsoft Identity Platform | Azure
titleSuffix: Microsoft identity platform
description: Guida ai protocolli OAuth 2.0 e OpenID Connect supportati dall'endpoint di Microsoft Identity Platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 2be68a858773dd4e76126ba6cd04ad98a2fd6a06
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87313439"
---
# <a name="oauth-20-and-openid-connect-protocols-on-microsoft-identity-platform"></a>Protocolli OAuth 2.0 e OpenID Connect su Microsoft Identity Platform

L'endpoint della piattaforma di identità Microsoft per l'identità come servizio implementa l'autenticazione e l'autorizzazione con i protocolli standard del settore OpenID Connect (OIDC) e OAuth 2,0, rispettivamente. Anche se il servizio è conforme agli standard, possono esistere sottili differenze tra le implementazioni di questi protocolli. Le informazioni in questo argomento sono utili se si sceglie di scrivere il codice inviando e gestendo direttamente le richieste HTTP o di usare una libreria open source di terze parti anziché una [libreria open source](reference-v2-libraries.md) di Microsoft.

## <a name="the-basics"></a>Nozioni di base

In quasi tutti i flussi di OAuth 2.0 e OpenID Connect, sono coinvolte nello scambio quattro parti:

![Diagramma che mostra i ruoli di OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* Il **server di autorizzazione** è l'endpoint di Microsoft Identity Platform e garantisce l'identità dell'utente, concede e revoca l'accesso alle risorse e rilascia i token. Il server di autorizzazione agisce anche come provider di identità: gestisce in modo sicuro le informazioni dell'utente, l'accesso e le relazioni di trust tra le parti di un flusso.
* Il **proprietario della risorsa** è in genere l'utente finale. Si tratta della parte che possiede i dati e ha la possibilità di consentire ai client di accedere a tali dati o risorse.
* Il **client OAuth** è l'app, identificata dall'ID applicazione. In genere il client OAuth è l'entità con cui interagisce l'utente finale e che richiede i token dal server di autorizzazione. Per accedere alla risorsa, il client deve ottenere l'autorizzazione da parte del proprietario della risorsa.
* Il **server della risorsa** è la posizione in cui si trova la risorsa o i dati. Consente al server di autorizzazione di autenticare e autorizzare il client OAuth in modo sicuro e usa i token di accesso bearer per assicurarsi che sia garantito l'accesso a una risorsa.

## <a name="app-registration"></a>Registrazione delle app

Ogni app che vuole accettare gli account personali e aziendali o dell'istituto di istruzione deve essere registrata tramite l'esperienza **Registrazioni app** nel [portale di Azure](https://aka.ms/appregistrations) prima di poter concedere l'accesso a questi utenti con OAuth 2.0 oppure OpenID Connect. Il processo di registrazione app raccoglie e assegna all'app alcuni valori:

* Un **ID applicazione** che identifica l'app in modo univoco
* Un **URI di reindirizzamento** (facoltativo) che può essere usato per reindirizzare le risposte all'app
* Altri valori specifici dello scenario

Per altri dettagli, vedere [Azure Active Directory B2C: registrare l'applicazione](quickstart-register-app.md).

## <a name="endpoints"></a>Endpoint

Dopo la registrazione, l'app comunica con Microsoft Identity Platform inviando richieste all'endpoint:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Dove `{tenant}` può assumere uno dei quattro diversi valori:

| valore | Descrizione |
| --- | --- |
| `common` | Consente agli utenti con account Microsoft e account aziendali o dell'istituto d'istruzione di Azure AD di accedere all'applicazione. |
| `organizations` | Consente solo agli utenti con account aziendali o dell'istituto d'istruzione di Azure AD di accedere all'applicazione. |
| `consumers` | Consente solo agli utenti con account Microsoft personali (MSA) di accedere all'applicazione. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` | Consente solo agli utenti con account aziendali o dell'istituto d'istruzione di un determinato tenant di Azure AD di accedere all'applicazione. È possibile usare il nome di dominio descrittivo del tenant di Azure AD o l'identificatore GUID. |

Per informazioni su come interagire con questi endpoint, scegliere un tipo di app specifico nella sezione [Protocolli](#protocols) e seguire i collegamenti per altre informazioni.

> [!TIP]
> Qualsiasi app registrata in Azure AD può usare l'endpoint di Microsoft Identity Platform, anche se non si esegue l'accesso agli account personali.  In questo modo, è possibile eseguire la migrazione delle applicazioni esistenti a Microsoft Identity Platform e a [MSAL](reference-v2-libraries.md) senza ricreare l'applicazione.

## <a name="tokens"></a>Tokens

OAuth 2,0 e OpenID Connect fanno uso estensivo dei **token**di connessione, generalmente rappresentati come [token JWT (token Web JSON)](https://tools.ietf.org/html/rfc7519). Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, il "portatore" è chiunque ottenga una copia del token. Anche se il rilascio del bearer token è condizionato dal completamento del processo di autenticazione in Microsoft Identity Platform, se non vengono adottate le misure necessarie per proteggere il token durante la trasmissione e l'archiviazione, è possibile che venga intercettato e usato da parti non autorizzate. Molti token di sicurezza hanno meccanismi integrati per prevenire l'uso non autorizzato, ma i token di connessione ne sono sprovvisti e devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza protezione, un utente malintenzionato può usare un attacco man-in-the-middle per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](https://tools.ietf.org/html/rfc6750).

In OAuth 2,0/OIDC sono usati principalmente 3 tipi di token:

* [Token di accesso](access-tokens.md) : token che un server di risorse riceve da un client, che contiene le autorizzazioni concesse al client.  
* [Token ID](id-tokens.md) : token ricevuti da un client dal server di autorizzazione, usati per accedere a un utente e ottenere informazioni di base su di essi.
* Aggiornare i token: usati da un client per ottenere nuovi token di accesso e ID nel tempo.  Si tratta di stringhe opache e sono comprensibili solo al server di autorizzazione.

## <a name="protocols"></a>Protocolli

Se si è pronti per visualizzare alcune richieste di esempio, iniziare con uno dei documenti di protocollo seguenti. Ognuna corrisponde a uno scenario di autenticazione specifico. Se è necessario determinare quale sia il flusso giusto per l'utente, consultare [i tipi di app che è possibile compilare con Microsoft Identity Platform](v2-app-types.md).

* [Crea applicazioni per dispositivi mobili, native e Web con OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Consentire agli utenti di accedere con OpenID Connect](v2-protocols-oidc.md)
* [Creare daemon o processi sul lato server con il flusso di credenziali client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Ottenere token in un'API Web con il flusso on-behalf-of di OAuth 2.0](v2-oauth2-on-behalf-of-flow.md)
* [Compilare app a singola pagina con il flusso implicito OAuth 2,0](v2-oauth2-implicit-grant-flow.md)
