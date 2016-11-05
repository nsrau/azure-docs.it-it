---
title: Protocolli v2.0 di Azure AD | Microsoft Docs
description: Guida ai protocolli supportati dall'endpoint v 2.0 di Azure AD.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock

---
# Protocolli v2.0 - OAuth 2.0 e OpenID Connect
L'endpoint v2.0 può usare Azure AD per il servizio IaaS (identity-as-a-service) con protocolli standard del settore, OpenID Connect e OAuth 2.0. Anche se il servizio è conforme agli standard, possono esistere sottili differenze tra le implementazioni di questi protocolli. Le informazioni in questo argomento sono utili se si sceglie di scrivere codice inviando e gestendo direttamente le richieste HTTP o di usare una libreria open source di terze parti anziché usare una delle librerie open source di Microsoft.
<!-- TODO: Need link to libraries above -->

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint 2.0. Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
> 
> 

## Nozioni di base
In quasi tutti i flussi di OAuth e OpenID Connect, sono coinvolte nello scambio quattro parti:

![Ruoli di OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

* Il **server di autorizzazione** è l'endpoint v2.0. Garantisce l'identità dell'utente, concede e revoca l'accesso alle risorse e rilascia i token. Agisce anche come provider di identità: gestisce in modo sicuro le informazioni dell'utente, l'accesso e le relazioni di trust tra le parti di un flusso.
* Il **proprietario della risorsa** è in genere l'utente finale. È l'entità che possiede i dati e consente a terze parti di accedere a tali dati o risorse.
* Il **client OAuth** è l'app, identificata dall'ID applicazione. In genere è l'entità con cui interagisce l'utente finale e che richiede i token dal server di autorizzazione. Per accedere alla risorsa, il client deve ottenere l'autorizzazione da parte del proprietario della risorsa.
* Il **server della risorsa** è la posizione in cui si trova la risorsa o i dati. Consente al server di autorizzazione di autenticare e autorizzare il client OAuth in modo sicuro e usa i token di accesso Bearer per assicurarsi che sia garantito l'accesso a una risorsa.

## Registrazione delle app
Ogni app che usa l'endpoint v2.0 dovrà essere registrata in [apps.dev.microsoft.com](https://apps.dev.microsoft.com) per interagire tramite OAuth oppure OpenID Connect. Il processo di registrazione raccoglie e assegna all'app alcuni valori:

* Un **ID applicazione** che identifica l'app in modo univoco
* Un **URI di reindirizzamento** o un **identificatore di pacchetto** che possono essere usati per indirizzare le risposte all'app
* Altri valori specifici dello scenario

Per altri dettagli, vedere [Azure Active Directory B2C: registrare l'applicazione](active-directory-v2-app-registration.md).

## Endpoint
Dopo la registrazione, l'app comunica con Azure AD inviando richieste all'endpoint v2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Dove `{tenant}` può assumere uno dei quattro diversi valori:

| Valore | Descrizione |
| --- | --- |
| `common` |Consente agli utenti con account Microsoft e account aziendali o dell'istituto d'istruzione di Azure Active Directory di accedere all'applicazione. |
| `organizations` |Consente solo agli utenti con account aziendali o dell'istituto d'istruzione di Azure Active Directory di accedere all'applicazione. |
| `consumers` |Consente solo agli utenti con account Microsoft personali (MSA) di accedere all'applicazione. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` oppure `contoso.onmicrosoft.com` |Consente solo agli utenti con account aziendali o dell'istituto d'istruzione di un determinato tenant di Azure Active Directory di accedere all'applicazione. È possibile usare il nome di dominio descrittivo del tenant di Azure AD o l'identificatore guid. |

Per altre informazioni sull'interazione con gli endpoint, selezionare un tipo di app di seguito.

## Tokens
L'implementazione v2.0 di OAuth 0 2.0 e OpenID Connect fa un uso intensivo dei token di connessione, inclusi quelli rappresentati come JWT (Token Web JSON). Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, per "portatore" si intende qualsiasi parte che sia in grado di presentare il token. Anche se il rilascio del token di connessione è condizionato dal completamento del processo di autenticazione in Azure AD, se non vengono adottate le misure necessarie per proteggere il token durante la trasmissione e l'archiviazione, è possibile che venga intercettato e usato da parti non autorizzate. Molti token di sicurezza hanno meccanismi integrati per prevenire l'uso non autorizzato, ma i token di connessione ne sono sprovvisti e devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza essere protetto, un utente malintenzionato potrebbe usare un attacco "man in the middle" per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](http://tools.ietf.org/html/rfc6750).

Altri dettagli sui diversi tipi di token usati nell'endpoint v2.0 sono disponibili nel [riferimento al token dell'endpoint v2.0](active-directory-v2-tokens.md).

## Protocolli
Se si è pronti vedere alcuni esempi di richieste, iniziare con una delle esercitazioni di seguito. Ognuna corrisponde a uno scenario di autenticazione specifico. Se è necessario determinare quale sia il flusso giusto per l’utente, consultare [i tipi di app che è possibile compilare con v2.0](active-directory-v2-flows.md).

* [Creazione di un’applicazione Mobile e Nativa con OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
* [Creazione di Web App con collegamento ID Open](active-directory-v2-protocols-oidc.md)
* [Creazione di app a pagina singola con OAuth 2.0 flusso implicito](active-directory-v2-protocols-implicit.md)
* [Compilare daemon o processi sul lato server con il flusso di credenziali client OAuth 2.0](active-directory-v2-protocols-oauth-client-creds.md)
* Ottenere i token in un'API Web con OAuth 2.0 per conto del flusso (presto disponibile)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 

<!---HONumber=AcomDC_0928_2016-->