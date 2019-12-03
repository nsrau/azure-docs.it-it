---
title: Autenticazione e autorizzazione
description: Informazioni sul supporto integrato per l'autenticazione e l'autorizzazione nel servizio app Azure e su come è possibile proteggere l'app da accessi non autorizzati.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: ff0eb102d37f285279c041ff91b7a89e157259eb
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672251"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticazione e autorizzazione in Servizio app di Azure

> [!NOTE]
> A questo punto, AAD V2 (incluso MSAL) non è supportato per servizi app Azure e funzioni di Azure. Verificare la disponibilità di aggiornamenti.
>

Il Servizio app di Azure fornisce supporto integrato per l'autenticazione e l'autorizzazione ed è quindi possibile consentire l'accesso degli utenti e l'accesso ai dati senza scrivere codice, o con una minima quantità di codice, nell'app Web, nell'API RESTful, nel back-end per dispositivi mobili e anche in [Funzioni di Azure](../azure-functions/functions-overview.md). Questo articolo descrive in che modo il servizio app aiuta a semplificare l'autenticazione e l'autorizzazione per l'app.

Per consentire processi sicuri di autenticazione e autorizzazione, è necessario conoscere a fondo i concetti correlati alla sicurezza, tra cui federazione, crittografia, gestione dei [token JSON Web (JWT)](https://wikipedia.org/wiki/JSON_Web_Token), [tipi di concessione](https://oauth.net/2/grant-types/) e così via. Il servizio app fornisce queste utilità che consentono agli sviluppatori di dedicare più tempo e lavoro alla creazione di valore aziendale per il cliente.

> [!IMPORTANT]
> Non è necessario usare il servizio app per AuthN/autho. Molti framework Web offrono funzionalità di sicurezza integrate che è possibile usare, se lo si preferisce. Se è necessaria più flessibilità di quella offerta dal servizio app, è anche possibile scrivere utilità personalizzate.  
>
> Tuttavia, se si usa una delle opzioni del servizio non app per l'autenticazione remota, tenere presente che [Chrome 80 sta apportando modifiche di rilievo all'implementazione di navigava sullostesso sito per i cookie](https://www.chromestatus.com/feature/5088147346030592) (data di rilascio intorno al 2020 marzo) e il meccanismo di autenticazione dell'app potrebbe interrompersi quando i browser client vengono aggiornati. La documentazione di ASP.NET Core contiene informazioni su come risolvere questo problema nell'app, all'indirizzo [http: browser navigava sullostesso sito changes Impact Authentication](/dotnet/core/compatibility/3.0-3.1#http-browser-samesite-changes-impact-authentication). Contiene indicazioni utili su come testare questa modifica sostanziale rispetto ai browser principali, indipendentemente dal fatto che si stia usando ASP.NET Core.
>

Per informazioni specifiche per le app per dispositivi mobili native, vedere [Autenticazione e autorizzazione per le app per dispositivi mobili in Servizio app di Azure](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Come funziona

Il modulo di autenticazione e autorizzazione viene eseguito nello stesso ambiente sandbox del codice dell'applicazione. Quando è abilitato, ogni richiesta HTTP in ingresso passa attraverso tale modulo prima di essere gestita dal codice dell'applicazione.

![](media/app-service-authentication-overview/architecture.png)

Il modulo gestisce diversi aspetti dell'app:

- Autentica gli utenti con il provider specificato
- Convalida, archivia e aggiorna i token
- Gestisce la sessione autenticata
- Inserisce le informazioni relative all'identità nelle intestazioni delle richieste

Il modulo viene eseguito separatamente dal codice dell'applicazione e viene configurato usando le impostazioni dell'app. Non sono necessari SDK, linguaggi specifici o modifiche al codice dell'applicazione. 

### <a name="user-claims"></a>Attestazioni utente

Per tutti i framework di linguaggio, il servizio app rende disponibili le attestazioni utente nel codice inserendole nelle intestazioni delle richieste. Per le app ASP.NET 4.6, il servizio app popola [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) con le attestazioni dell'utente autenticato, quindi è possibile seguire il modello di codice .NET standard, incluso l'attributo `[Authorize]`. Analogamente, per le app PHP, il servizio app popola la variabile `_SERVER['REMOTE_USER']`. Per le app Java, le attestazioni sono [accessibili dal servlet Tomcat](containers/configure-language-java.md#authenticate-users-easy-auth).

Per [Funzioni di Azure](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` non è idratato per il codice .NET, ma è comunque possibile trovare le attestazioni utente nelle intestazioni delle richieste.

Per altre informazioni, vedere [Accedere alle attestazioni utente](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Archivio di token

Il servizio app fornisce un archivio di token predefinito, ovvero un repository di token associati agli utenti delle app Web, delle API o delle app per dispositivi mobili native. Quando si abilita l'autenticazione con qualsiasi provider, questo archivio di token diventa immediatamente disponibile per l'app. Se il codice dell'applicazione deve accedere ai dati di questi provider per conto dell'utente, ad esempio: 

- pubblicare sul diario di Facebook dell'utente autenticato
- leggere i dati aziendali dell'utente dall'API Graph di Azure Active Directory o anche da Microsoft Graph

In genere è necessario scrivere codice per raccogliere, archiviare e aggiornare questi token nell'applicazione. Con l'archivio di token, è sufficiente [recuperare i token](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando sono necessari e [fare in modo che il servizio app li aggiorni](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando non sono più validi. 

I token ID, i token di accesso e i token di aggiornamento vengono memorizzati nella cache per la sessione autenticata e sono accessibili solo da parte degli utenti associati.  

Se non è necessario usare i token nell'app, è possibile disabilitare l'archivio di token.

### <a name="logging-and-tracing"></a>Registrazione e traccia

Se si [abilita la registrazione delle applicazioni](troubleshoot-diagnostic-logs.md), le tracce di autenticazione e autorizzazione possono essere visualizzate direttamente nei file di log. Se viene visualizzato un errore di autenticazione non previsto, è possibile trovarne facilmente tutti i dettagli esaminando i log dell'applicazione esistenti. Se si abilita la [traccia delle richieste non riuscite](troubleshoot-diagnostic-logs.md), è possibile vedere esattamente il ruolo che il modulo di autenticazione e autorizzazione ha avuto nella mancata riuscita della richiesta. Nei log di traccia cercare i riferimenti a un modulo denominato `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Provider di identità

Il servizio app usa l'[identità federata](https://en.wikipedia.org/wiki/Federated_identity), in cui un provider di identità di terze parti gestisce le identità utente e il flusso di autenticazione. Per impostazione predefinita sono disponibili cinque provider di identità: 

| Provider | Endpoint di accesso |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Account Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Quando si abilitano l'autenticazione e l'autorizzazione con uno di questi provider, il relativo endpoint di accesso è disponibile per l'autenticazione utente e per la convalida dei token di autenticazione del provider. È possibile offrire facilmente agli utenti tutte le opzioni di accesso desiderate. È anche possibile integrare un altro provider di identità o [una soluzione di identità personalizzata][custom-auth].

## <a name="authentication-flow"></a>Flusso di autenticazione

Il flusso di autenticazione è uguale per tutti i provider, ma varia a in base al fatto che si desideri o meno accedere con l'SDK del provider:

- Senza SDK del provider: l'applicazione delega l'accesso federato al servizio app. Ciò avviene, in genere, con le app basate su browser, che possono presentare all'utente la pagina di accesso del provider. Il codice server gestisce il processo di accesso, quindi si parla anche di _flusso diretto dal server_ oppure _flusso server_. Questo caso si applica alle app basate su browser. Si applica anche alle app native che consentono l'accesso agli utenti con l'SDK client di App per dispositivi mobili, perché l'SDK consente di aprire una visualizzazione Web per l'accesso degli utenti con l'autenticazione del servizio app. 
- Con l'SDK del provider: l'applicazione consente l'accesso manuale degli utenti al provider e quindi invia il token di autenticazione al servizio app per la convalida. Ciò avviene, in genere, con le app senza browser, che non possono presentare all'utente la pagina di accesso del provider. Il codice dell'applicazione gestisce il processo di accesso, quindi si parla anche di _flusso diretto dal client_ oppure _flusso client_. Questo caso si applica alle API REST, a [Funzioni di Azure](../azure-functions/functions-overview.md) e ai client browser JavaScript, oltre che alle app basate su browser che richiedono una maggiore flessibilità nel processo di accesso. Si applica anche alle app per dispositivi mobili native che consentono l'accesso degli utenti con l'SDK del provider.

> [!NOTE]
> Le chiamate da un'app browser attendibile nel servizio app e le chiamate da un'altra API REST nel servizio app o in [Funzioni di Azure](../azure-functions/functions-overview.md) possono essere autenticate tramite il flusso diretto dal server. Per altre informazioni, vedere [Personalizzare l'autenticazione e l'autorizzazione in Servizio app di Azure](app-service-authentication-how-to.md).
>

La tabella seguente illustra i passaggi del flusso di autenticazione.

| Passaggio | Senza SDK del provider | Con SDK del provider |
| - | - | - |
| 1. accesso utente | Reindirizza il client a `/.auth/login/<provider>`. | Il codice client consente l'accesso utente direttamente con l'SDK del provider e riceve un token di autenticazione. Per informazioni, vedere la documentazione del provider. |
| 2. post-autenticazione | Il provider reindirizza il client a `/.auth/login/<provider>/callback`. | Il codice client [inserisce il token del provider](app-service-authentication-how-to.md#validate-tokens-from-providers) in `/.auth/login/<provider>` per la convalida. |
| 3. stabilire una sessione autenticata | Il servizio app aggiunge il cookie autenticato alla risposta. | Il servizio app restituisce il proprio token di autenticazione al codice client. |
| 4. gestire il contenuto autenticato | Il client include il cookie di autenticazione nelle richieste successive (gestite automaticamente dal browser). | Il codice client presenta il token di autenticazione nell'intestazione `X-ZUMO-AUTH` (gestita automaticamente dagli SDK client per app per dispositivi mobili). |

Per i browser client, il servizio app può indirizzare automaticamente tutti gli utenti non autenticati a `/.auth/login/<provider>`. È anche possibile presentare agli utenti uno o più collegamenti a `/.auth/login/<provider>` per consentire di accedere all'app con il provider desiderato.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Comportamento di autorizzazione

Nel [portale di Azure](https://portal.azure.com)è possibile configurare l'autorizzazione del servizio app con diversi comportamenti quando la richiesta in ingresso non è autenticata.

![](media/app-service-authentication-overview/authorization-flow.png)

I titoli seguenti descrivono le opzioni.

### <a name="allow-anonymous-requests-no-action"></a>Consenti richieste anonime (nessuna azione)

Questa opzione rinvia l'autorizzazione del traffico non autenticato al codice dell'applicazione. Per le richieste autenticate, il servizio app passa anche le informazioni di autenticazione nelle intestazioni HTTP. 

Questa opzione offre maggiore flessibilità nella gestione delle richieste anonime. Ad esempio consente di [presentare più opzioni di accesso](app-service-authentication-how-to.md#use-multiple-sign-in-providers) agli utenti. Tuttavia richiede di scrivere codice. 

### <a name="allow-only-authenticated-requests"></a>Consentire solo le richieste autenticate

L'opzione è **Accedi con \<provider>** . Il servizio app reindirizza tutte le richieste anonime a `/.auth/login/<provider>` per il provider scelto. Se la richiesta anonima proviene da un'app per dispositivi mobili nativa, verrà restituita la risposta `HTTP 401 Unauthorized`.

Con questa opzione non è necessario scrivere codice di autenticazione nell'app. È possibile gestire un livello di autorizzazione più specifico, ad esempio l'autorizzazione specifica dei ruoli, esaminando le attestazioni utente (vedere [Accedere alle attestazioni utente](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderate per le app che vogliono un home page disponibile pubblicamente, come in molte applicazioni a singola pagina.

## <a name="more-resources"></a>Altre risorse

[Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure (Windows)](app-service-web-tutorial-auth-aad.md)  
[Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure per Linux](containers/tutorial-auth-aad.md)  
[Personalizzare l'autenticazione e l'autorizzazione nel servizio app](app-service-authentication-how-to.md)

Guide alle procedure specifiche del provider:

* [Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory][AAD]
* [Come configurare un'applicazione per usare l'account di accesso di Facebook][Facebook]
* [Come configurare un'applicazione per usare l'account di accesso di Google][Google]
* [Come configurare un'applicazione per usare l'account di accesso Microsoft][MSA]
* [Come configurare un'applicazione per usare l'account di accesso di Twitter][Twitter]
* [Procedura: Usare l'autenticazione personalizzata per la propria applicazione][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
