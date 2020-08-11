---
title: Autenticazione e autorizzazione
description: Informazioni sul supporto integrato per l'autenticazione e l'autorizzazione nel servizio app Azure e funzioni di Azure e su come può aiutare a proteggere l'app da accessi non autorizzati.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 8362cc3b8f8477f77d8ec672144e7c68d2e3434d
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080729"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Autenticazione e autorizzazione nel servizio app Azure e funzioni di Azure

Il Servizio app di Azure fornisce supporto integrato per l'autenticazione e l'autorizzazione ed è quindi possibile consentire l'accesso degli utenti e l'accesso ai dati senza scrivere codice, o con una minima quantità di codice, nell'app Web, nell'API RESTful, nel back-end per dispositivi mobili e anche in [Funzioni di Azure](../azure-functions/functions-overview.md). Questo articolo descrive in che modo il servizio app aiuta a semplificare l'autenticazione e l'autorizzazione per l'app.

Per consentire processi sicuri di autenticazione e autorizzazione, è necessario conoscere a fondo i concetti correlati alla sicurezza, tra cui federazione, crittografia, gestione dei [token JSON Web (JWT)](https://wikipedia.org/wiki/JSON_Web_Token), [tipi di concessione](https://oauth.net/2/grant-types/) e così via. Il servizio app fornisce queste utilità che consentono agli sviluppatori di dedicare più tempo e lavoro alla creazione di valore aziendale per il cliente.

> [!IMPORTANT]
> Non è necessario usare questa funzionalità per l'autenticazione e l'autorizzazione. È possibile usare le funzionalità di sicurezza in bundle nel framework Web preferito oppure è possibile scrivere utilità personalizzate. Tuttavia, tenere presente che [Chrome 80 sta apportando modifiche di rilievo all'implementazione di navigava sullostesso sito per i cookie](https://www.chromestatus.com/feature/5088147346030592) (data di rilascio intorno al 2020 marzo) e l'autenticazione remota personalizzata o altri scenari che si basano sulla pubblicazione di cookie tra siti possono interrompersi quando si aggiornano i browser Chrome del client. La soluzione alternativa è complessa perché deve supportare diversi comportamenti navigava sullostesso sito per browser diversi. 
>
> Il ASP.NET Core 2,1 e versioni successive ospitate dal servizio app è già stato modificato per questa modifica di rilievo e gestire in modo appropriato Chrome 80 e i browser meno recenti. Inoltre, la stessa patch per ASP.NET Framework 4.7.2 è stata distribuita nelle istanze del servizio app nell'intero gennaio 2020. Per ulteriori informazioni, vedere [app Azure Service navigava sullostesso sito cookie Update](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

> [!NOTE]
> La funzionalità di autenticazione/autorizzazione viene anche chiamata "Easy auth".

> [!NOTE]
> L'abilitazione di questa funzionalità provocherà automaticamente il reindirizzamento di **tutte** le richieste HTTP non sicure all'applicazione a HTTPS, indipendentemente dall'impostazione di configurazione del servizio app per [applicare HTTPS](configure-ssl-bindings.md#enforce-https). Se necessario, è possibile disabilitare questa impostazione usando l' `requireHttps` impostazione nel [file di configurazione delle impostazioni di autenticazione](app-service-authentication-how-to.md#configuration-file-reference), ma è necessario assicurarsi che non venga mai trasmesso alcun token di sicurezza su connessioni HTTP non protette.

Per informazioni specifiche per le app per dispositivi mobili native, vedere [Autenticazione e autorizzazione per le app per dispositivi mobili in Servizio app di Azure](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Funzionamento

### <a name="on-windows"></a>In Windows

Il modulo di autenticazione e autorizzazione viene eseguito nello stesso ambiente sandbox del codice dell'applicazione. Quando è abilitato, ogni richiesta HTTP in ingresso passa attraverso tale modulo prima di essere gestita dal codice dell'applicazione.

![Diagramma dell'architettura che mostra le richieste intercettate da un processo nella sandbox del sito che interagisce con i provider di identità prima di consentire il traffico verso il sito distribuito](media/app-service-authentication-overview/architecture.png)

Il modulo gestisce diversi aspetti dell'app:

- Autentica gli utenti con il provider specificato
- Convalida, archivia e aggiorna i token
- Gestisce la sessione autenticata
- Inserisce le informazioni relative all'identità nelle intestazioni delle richieste

Il modulo viene eseguito separatamente dal codice dell'applicazione e viene configurato usando le impostazioni dell'app. Non sono necessari SDK, linguaggi specifici o modifiche al codice dell'applicazione. 

### <a name="on-containers"></a>Nei contenitori

Il modulo di autenticazione e autorizzazione viene eseguito in un contenitore separato, isolato dal codice dell'applicazione. L'uso di ciò che è noto come [modello Ambassador](https://docs.microsoft.com/azure/architecture/patterns/ambassador), interagisce con il traffico in ingresso per eseguire funzionalità simili a quelle di Windows. Poiché non viene eseguito in-process, non è possibile l'integrazione diretta con Framework di linguaggio specifici. Tuttavia, le informazioni rilevanti richieste dall'app vengono passate usando le intestazioni della richiesta, come illustrato di seguito.

### <a name="userapplication-claims"></a>Attestazioni utente/applicazione

Per tutti i Framework di linguaggio, il servizio app rende disponibili al codice le attestazioni nel token in ingresso, sia che si tratti di un utente finale autenticato o di un'applicazione client, inserendole nelle intestazioni della richiesta. Per le app ASP.NET 4.6, il servizio app popola [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) con le attestazioni dell'utente autenticato, quindi è possibile seguire il modello di codice .NET standard, incluso l'attributo `[Authorize]`. Analogamente, per le app PHP, il servizio app popola la variabile `_SERVER['REMOTE_USER']`. Per le app Java, le attestazioni sono [accessibili dal servlet Tomcat](configure-language-java.md#authenticate-users-easy-auth).

Per [funzioni di Azure](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` non viene popolato per il codice .NET, ma è comunque possibile trovare le attestazioni utente nelle intestazioni della richiesta o ottenere l' `ClaimsPrincipal` oggetto dal contesto della richiesta o anche tramite un parametro di associazione. Per ulteriori informazioni, vedere [utilizzo delle identità client](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) .

Per altre informazioni, vedere [Accedere alle attestazioni utente](app-service-authentication-how-to.md#access-user-claims).

> [!NOTE]
> Al momento, ASP.NET Core attualmente non supporta il popolamento dell'utente corrente con la funzionalità di autenticazione/autorizzazione. Tuttavia, per colmare questo gap sono disponibili [componenti middleware open source](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) .
>

### <a name="token-store"></a>Archivio di token

Il servizio app fornisce un archivio di token predefinito, ovvero un repository di token associati agli utenti delle app Web, delle API o delle app per dispositivi mobili native. Quando si abilita l'autenticazione con qualsiasi provider, questo archivio di token diventa immediatamente disponibile per l'app. Se il codice dell'applicazione deve accedere ai dati di questi provider per conto dell'utente, ad esempio: 

- pubblicare sul diario di Facebook dell'utente autenticato
- leggere i dati aziendali dell'utente usando l'API Microsoft Graph

In genere è necessario scrivere codice per raccogliere, archiviare e aggiornare questi token nell'applicazione. Con l'archivio di token, è sufficiente [recuperare i token](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando sono necessari e [fare in modo che il servizio app li aggiorni](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando non sono più validi. 

I token ID, i token di accesso e i token di aggiornamento vengono memorizzati nella cache per la sessione autenticata ed è possibile accedervi solo dall'utente associato.  

Se non è necessario usare i token nell'app, è possibile disabilitare l'archivio di token.

### <a name="logging-and-tracing"></a>Registrazione e traccia

Se si [abilita la registrazione delle applicazioni](troubleshoot-diagnostic-logs.md), le tracce di autenticazione e autorizzazione possono essere visualizzate direttamente nei file di log. Se viene visualizzato un errore di autenticazione non previsto, è possibile trovare facilmente tutti i dettagli esaminando i log delle applicazioni esistenti. Se si abilita la [traccia delle richieste non riuscite](troubleshoot-diagnostic-logs.md), è possibile vedere esattamente il ruolo che il modulo di autenticazione e autorizzazione ha avuto nella mancata riuscita della richiesta. Nei log di traccia cercare i riferimenti a un modulo denominato `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Provider di identità

Il servizio app usa l'[identità federata](https://en.wikipedia.org/wiki/Federated_identity), in cui un provider di identità di terze parti gestisce le identità utente e il flusso di autenticazione. Per impostazione predefinita sono disponibili cinque provider di identità: 

| Provider | Endpoint di accesso |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Account Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |
| Qualsiasi provider [OpenID Connect](https://openid.net/connect/) (anteprima) | `/.auth/login/<providerName>` |

Quando si abilitano l'autenticazione e l'autorizzazione con uno di questi provider, il relativo endpoint di accesso è disponibile per l'autenticazione utente e per la convalida dei token di autenticazione del provider. È possibile offrire facilmente agli utenti tutte le opzioni di accesso desiderate.

Un [percorso di estendibilità legacy][custom-auth] esiste per l'integrazione con altri provider di identità o una soluzione di autenticazione personalizzata, ma questa operazione non è consigliata. È invece consigliabile usare il supporto di OpenID Connect.

## <a name="authentication-flow"></a>Flusso di autenticazione

Il flusso di autenticazione è uguale per tutti i provider, ma varia a in base al fatto che si desideri o meno accedere con l'SDK del provider:

- Senza SDK del provider: l'applicazione delega l'accesso federato al servizio app. Ciò avviene, in genere, con le app basate su browser, che possono presentare all'utente la pagina di accesso del provider. Il codice server gestisce il processo di accesso, quindi si parla anche di _flusso diretto dal server_ oppure _flusso server_. Questo caso si applica alle app basate su browser. Si applica anche alle app native che consentono l'accesso agli utenti con l'SDK client di App per dispositivi mobili, perché l'SDK consente di aprire una visualizzazione Web per l'accesso degli utenti con l'autenticazione del servizio app. 
- Con l'SDK del provider: l'applicazione consente l'accesso manuale degli utenti al provider e quindi invia il token di autenticazione al servizio app per la convalida. Ciò avviene, in genere, con le app senza browser, che non possono presentare all'utente la pagina di accesso del provider. Il codice dell'applicazione gestisce il processo di accesso, quindi si parla anche di _flusso diretto dal client_ oppure _flusso client_. Questo caso si applica alle API REST, a [Funzioni di Azure](../azure-functions/functions-overview.md) e ai client browser JavaScript, oltre che alle app basate su browser che richiedono una maggiore flessibilità nel processo di accesso. Si applica anche alle app per dispositivi mobili native che consentono l'accesso degli utenti con l'SDK del provider.

> [!NOTE]
> Le chiamate da un'app browser attendibile nel servizio app a un'altra API REST nel servizio app o in [funzioni di Azure](../azure-functions/functions-overview.md) possono essere autenticate usando il flusso indirizzato al server. Per altre informazioni, vedere [Personalizzare l'autenticazione e l'autorizzazione in Servizio app di Azure](app-service-authentication-how-to.md).
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

![Screenshot che mostra l'elenco a discesa "azione da eseguire quando la richiesta non è autenticata"](media/app-service-authentication-overview/authorization-flow.png)

I titoli seguenti descrivono le opzioni.

### <a name="allow-anonymous-requests-no-action"></a>Consenti richieste anonime (nessuna azione)

Questa opzione rinvia l'autorizzazione del traffico non autenticato al codice dell'applicazione. Per le richieste autenticate, il servizio app passa anche le informazioni di autenticazione nelle intestazioni HTTP. 

Questa opzione offre maggiore flessibilità nella gestione delle richieste anonime. Ad esempio consente di [presentare più opzioni di accesso](app-service-authentication-how-to.md#use-multiple-sign-in-providers) agli utenti. Tuttavia richiede di scrivere codice. 

### <a name="allow-only-authenticated-requests"></a>Consentire solo le richieste autenticate

L'opzione è **accesso con \<provider> **. Il servizio app reindirizza tutte le richieste anonime a `/.auth/login/<provider>` per il provider scelto. Se la richiesta anonima proviene da un'app per dispositivi mobili nativa, verrà restituita la risposta `HTTP 401 Unauthorized`.

Con questa opzione non è necessario scrivere codice di autenticazione nell'app. È possibile gestire un livello di autorizzazione più specifico, ad esempio l'autorizzazione specifica dei ruoli, esaminando le attestazioni utente (vedere [Accedere alle attestazioni utente](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderate per le app che vogliono un home page disponibile pubblicamente, come in molte applicazioni a singola pagina.

## <a name="more-resources"></a>Altre risorse

[Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure (Windows)](tutorial-auth-aad.md)  
[Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure per Linux](containers/tutorial-auth-aad.md)  
[Personalizzare l'autenticazione e l'autorizzazione nel servizio app](app-service-authentication-how-to.md) 
 [Integrazione di .NET Core di Azure AppService EasyAuth (terze parti)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) 
 [Come usare l'autenticazione del servizio app Azure con .NET Core (terze parti)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

Guide alle procedure specifiche del provider:

* [Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory][AAD]
* [Come configurare un'applicazione per usare l'account di accesso di Facebook][Facebook]
* [Come configurare un'applicazione per usare l'account di accesso di Google][Google]
* [Come configurare un'applicazione per usare l'account di accesso Microsoft][MSA]
* [Come configurare un'applicazione per usare l'account di accesso di Twitter][Twitter]
* [Come configurare l'app per l'uso di un provider OpenID Connect per l'accesso (anteprima)][OIDC]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md
[OIDC]: configure-authentication-provider-openid-connect.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
