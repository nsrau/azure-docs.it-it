---
title: Protocolli OAuth 2.0 e OpenID Connect - Piattaforma di identità Microsoft Azure
description: Guida ai protocolli OAuth 2.0 e OpenID Connect supportati dall'endpoint della piattaforma di identità Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bc635addc2a5cd7d3c698f6885bec0399e28743a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309663"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Protocolli OAuth 2.0 e OpenID Connect sulla piattaforma Microsoft Identity

L'endpoint della piattaforma di identità Microsoft per l'identità come servizio con protocolli standard del settore, OpenID Connect e OAuth 2.0. Anche se il servizio è conforme agli standard, possono esistere sottili differenze tra le implementazioni di questi protocolli. Le informazioni in questo argomento sono utili se si sceglie di scrivere il codice inviando e gestendo direttamente le richieste HTTP o di usare una libreria open source di terze parti anziché usare una [libreria open source](reference-v2-libraries.md) di Microsoft.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure AD sono supportati dall'endpoint della piattaforma di identità Microsoft.Non all Azure AD scenarios and features are supported by the Microsoft identity platform endpoint. Per determinare se è necessario utilizzare l'endpoint della piattaforma di identità Microsoft, leggere le informazioni sulle [limitazioni della piattaforma](active-directory-v2-limitations.md)di identità Microsoft .

## <a name="the-basics"></a>Nozioni di base

In quasi tutti i flussi di OAuth 2.0 e OpenID Connect, sono coinvolte nello scambio quattro parti:

![Diagramma che mostra i ruoli OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* Il **server di** autorizzazione è l'endpoint della piattaforma di identità Microsoft e responsabile di garantire l'identità dell'utente, concedere e revocare l'accesso alle risorse e l'emissione di token. Il server di autorizzazione agisce anche come provider di identità: gestisce in modo sicuro le informazioni dell'utente, l'accesso e le relazioni di trust tra le parti di un flusso.
* Il **proprietario della risorsa** è in genere l'utente finale. È l'entità che possiede i dati e consente a terze parti di accedere a tali dati o risorse.
* Il **client OAuth** è l'app, identificata dall'ID applicazione. In genere il client OAuth è l'entità con cui interagisce l'utente finale e che richiede i token dal server di autorizzazione. Per accedere alla risorsa, il client deve ottenere l'autorizzazione da parte del proprietario della risorsa.
* Il **server della risorsa** è la posizione in cui si trova la risorsa o i dati. Considera attendibile il server di autorizzazione per autenticare e autorizzare in modo sicuro il client OAuth e utilizza i token di accesso Bearer per garantire che sia possibile concedere l'accesso a una risorsa.

## <a name="app-registration"></a>Registrazione delle app

Ogni app che vuole accettare account personali e aziendali o dell'istituto di istruzione deve essere registrata tramite l'esperienza di registrazione delle **app** nel portale di [Azure](https://aka.ms/appregistrations) prima di poter accedere a questi utenti tramite OAuth 2.0 o OpenID Connect. Il processo di registrazione app raccoglie e assegna all'app alcuni valori:

* Un **ID applicazione** che identifica in modo univoco l'app
* UN URI di **reindirizzamento** (facoltativo) che può essere usato per indirizzare le risposte all'app
* Altri valori specifici dello scenario

Per altri dettagli, vedere [Azure Active Directory B2C: registrare l'applicazione](quickstart-register-app.md).

## <a name="endpoints"></a>Endpoint

Una volta registrata, l'app comunica con la piattaforma di identità Microsoft inviando richieste all'endpoint:

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
> Qualsiasi app registrata in Azure AD può usare l'endpoint della piattaforma di identità Microsoft, anche se non accede agli account personali.  In questo modo, è possibile eseguire la migrazione delle applicazioni esistenti alla piattaforma di identità Microsoft e [MSAL](reference-v2-libraries.md) senza ricreare l'applicazione.  

## <a name="tokens"></a>Tokens

L'implementazione della piattaforma di identità Microsoft di OAuth 2.0 e OpenID Connect fa ampio uso di token di connessione, inclusi i token di connessione rappresentati come JWT. Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, per "portatore" si intende qualsiasi parte che sia in grado di presentare il token. Anche se un'entità deve prima eseguire l'autenticazione con la piattaforma di identità Microsoft per ricevere il token di connessione, se i passaggi necessari non vengono eseguiti per proteggere il token nella trasmissione e nell'archiviazione, può essere intercettato e utilizzato da un'entità non intenzionale. Molti token di sicurezza hanno meccanismi integrati per prevenire l'uso non autorizzato, ma i token di connessione ne sono sprovvisti e devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza protezione, un utente malintenzionato può usare un attacco man-in-the-middle per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](https://tools.ietf.org/html/rfc6750).

Ulteriori dettagli sui diversi tipi di token utilizzati nell'endpoint della piattaforma di identità Microsoft sono disponibili nel [riferimento al token dell'endpoint della piattaforma di identità Microsoft.Further](v2-id-and-access-tokens.md)details of different types of tokens used in the Microsoft identity platform endpoint is available in the Microsoft identity platform endpoint reference .

## <a name="protocols"></a>Protocolli

Se si è pronti vedere alcuni esempi di richieste, iniziare con una delle esercitazioni di seguito. Ognuna corrisponde a uno scenario di autenticazione specifico. Se hai bisogno di aiuto per determinare quale è il flusso giusto per te, dai un'occhiata ai tipi di app che puoi creare con la piattaforma di [identità Microsoft.](v2-app-types.md)

* [Crea applicazioni mobili e native con OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Creare app Web con OpenID Connect](v2-protocols-oidc.md)
* [Creare app a pagina singola con il flusso implicito OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Creare daemon o processi sul lato server con il flusso di credenziali client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Ottenere token in un'API Web con il flusso on-behalf-of di OAuth 2.0](v2-oauth2-on-behalf-of-flow.md)
