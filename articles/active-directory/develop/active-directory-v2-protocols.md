---
title: Informazioni sui protocolli di autorizzazione supportati dalla piattaforma di identità Microsoft | Azure
description: Guida ai protocolli OAuth 2,0 e OpenID Connect supportati dall'endpoint della piattaforma di identità Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446d7178973c1d43d55ff89c429b05c2a10118ba
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835284"
---
# <a name="microsoft-identity-platform-protocols"></a>Protocolli della piattaforma di identità Microsoft

Endpoint della piattaforma di identità Microsoft per l'identità come servizio con protocolli standard del settore, OpenID Connect e OAuth 2,0. Anche se il servizio è conforme agli standard, possono esistere sottili differenze tra le implementazioni di questi protocolli. Le informazioni in questo argomento sono utili se si sceglie di scrivere il codice inviando e gestendo direttamente le richieste HTTP o di usare una libreria open source di terze parti anziché usare una [libreria open source](reference-v2-libraries.md) di Microsoft.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure AD sono supportati dall'endpoint della piattaforma Microsoft Identity. Per determinare se è necessario usare l'endpoint della piattaforma Microsoft Identity, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Nozioni di base

In quasi tutti i flussi di OAuth 2.0 e OpenID Connect, sono coinvolte nello scambio quattro parti:

![Diagramma che illustra i ruoli OAuth 2,0](./media/active-directory-v2-flows/protocols-roles.svg)

* Il **server di autorizzazione** è l'endpoint della piattaforma di identità Microsoft e ha la responsabilità di garantire l'identità dell'utente, concedere e revocare l'accesso alle risorse e rilasciare i token. Il server di autorizzazione agisce anche come provider di identità: gestisce in modo sicuro le informazioni dell'utente, l'accesso e le relazioni di trust tra le parti di un flusso.
* Il **proprietario della risorsa** è in genere l'utente finale. È l'entità che possiede i dati e consente a terze parti di accedere a tali dati o risorse.
* Il **client OAuth** è l'app, identificata dall'ID applicazione. In genere il client OAuth è l'entità con cui interagisce l'utente finale e che richiede i token dal server di autorizzazione. Per accedere alla risorsa, il client deve ottenere l'autorizzazione da parte del proprietario della risorsa.
* Il **server della risorsa** è la posizione in cui si trova la risorsa o i dati. Considera attendibile il server di autorizzazione per autenticare e autorizzare il client OAuth in modo sicuro e usa i token di accesso di connessione per garantire che sia possibile concedere l'accesso a una risorsa.

## <a name="app-registration"></a>Registrazione delle app

Ogni app che vuole accettare gli account personali e aziendali o dell'Istituto di istruzione deve essere registrata tramite l'esperienza **registrazioni app** nel [portale di Azure](https://aka.ms/appregistrations) prima di poter firmare questi utenti usando OAuth 2,0 o OpenID Connect. Il processo di registrazione app raccoglie e assegna all'app alcuni valori:

* Un **ID applicazione** che identifica l'app in modo univoco
* Un **URI di reindirizzamento** (facoltativo) che può essere usato per indirizzare le risposte all'app
* Altri valori specifici dello scenario

Per altri dettagli, vedere [Azure Active Directory B2C: registrare l'applicazione](quickstart-register-app.md).

## <a name="endpoints"></a>Endpoint

Una volta eseguita la registrazione, l'app comunica con la piattaforma di identità Microsoft inviando richieste all'endpoint:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Dove `{tenant}` può assumere uno dei quattro diversi valori:

| Value | Descrizione |
| --- | --- |
| `common` | Consente agli utenti con account Microsoft e account aziendali o dell'istituto d'istruzione di Azure AD di accedere all'applicazione. |
| `organizations` | Consente solo agli utenti con account aziendali o dell'istituto d'istruzione di Azure AD di accedere all'applicazione. |
| `consumers` | Consente solo agli utenti con account Microsoft personali (MSA) di accedere all'applicazione. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` | Consente solo agli utenti con account aziendali o dell'istituto d'istruzione di un determinato tenant di Azure AD di accedere all'applicazione. È possibile usare il nome di dominio descrittivo del tenant di Azure AD o l'identificatore GUID. |

Per informazioni su come interagire con questi endpoint, scegliere un tipo di app specifico nella sezione [Protocolli](#protocols) e seguire i collegamenti per altre informazioni.

> [!TIP]
> Qualsiasi app registrata in Azure AD può usare l'endpoint della piattaforma di identità Microsoft, anche se non eseguono l'accesso agli account personali.  In questo modo, è possibile eseguire la migrazione delle applicazioni esistenti a Microsoft Identity Platform e [MSAL](reference-v2-libraries.md) senza ricreare l'applicazione.  

## <a name="tokens"></a>token

L'implementazione della piattaforma di identità Microsoft di OAuth 2,0 e OpenID Connect fanno ampio uso dei token di connessione, inclusi i token di connessione rappresentati come token JWT. Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, per "portatore" si intende qualsiasi parte che sia in grado di presentare il token. Sebbene un'entità debba eseguire prima l'autenticazione con la piattaforma di identità Microsoft per ricevere il bearer token, se non vengono eseguite le operazioni necessarie per proteggere il token nella trasmissione e nell'archiviazione, è possibile intercettarlo e usarlo da parte di un'entità non intenzionale. Molti token di sicurezza hanno meccanismi integrati per prevenire l'uso non autorizzato, ma i token di connessione ne sono sprovvisti e devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza protezione, un utente malintenzionato può usare un attacco man-in-the-middle per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](https://tools.ietf.org/html/rfc6750).

Altre informazioni sui diversi tipi di token usati nell'endpoint della piattaforma Microsoft Identity sono disponibili nella Guida di [riferimento al token dell'endpoint della piattaforma di identità Microsoft](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocolli

Se si è pronti vedere alcuni esempi di richieste, iniziare con una delle esercitazioni di seguito. Ognuna corrisponde a uno scenario di autenticazione specifico. Se è necessario contribuire a determinare quale sia il flusso corretto, vedere [i tipi di app che è possibile compilare con la piattaforma di identità Microsoft](v2-app-types.md).

* [Creare un'applicazione nativa e per dispositivi mobili con OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Creare app Web con OpenID Connect](v2-protocols-oidc.md)
* [Creare app a pagina singola con il flusso implicito OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Creare daemon o processi sul lato server con il flusso di credenziali client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Ottenere token in un'API Web con il flusso on-behalf-of di OAuth 2.0](v2-oauth2-on-behalf-of-flow.md)
