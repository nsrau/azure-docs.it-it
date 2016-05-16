<properties
	pageTitle="Autenticazione e autorizzazione nel servizio app di Azure | Microsoft Azure"
	description="Riferimento concettuale e panoramica della funzionalità di Autenticazione/Autorizzazione per il servizio app di Azure"
	services="app-service"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="mahender"/>
    
# Autenticazione e autorizzazione nel servizio app di Azure

## Informazioni sull’autenticazione / autorizzazione di servizio app

L'autenticazione / autorizzazione del servizio App è una funzionalità che consente all'applicazione di far accedere utenti senza necessità di apportare modifiche al codice nel back-end dell’app. Fornisce un modo semplice per proteggere l'applicazione e utilizzare dati per-utente.

Il servizio App usa identità federate, in cui un provider di identità di terze parti archivia account e autentica utenti. L'applicazione si basa sulle informazioni di identità del provider invece ci recuperare direttamente le informazioni. Il servizio App supporta cinque provider di identità pronti all’uso: _Azure Active Directory_, _Facebook_, _Google_, _Account Microsoft_, e _Twitter_. L'app può utilizzare un numero qualsiasi di questi provider di identità, per offrire agli utenti diverse opzioni di accesso. È anche possibile espandere il supporto predefinito integrando un altro provider di identità o una [soluzione di identità personalizzata][custom-auth].

Se si vuole iniziare subito, vedere una delle esercitazioni seguenti:

- [Aggiungere l'autenticazione all'app iOS][iOS] (o [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] o [Cordova])
- [Autenticazione utente per le app per le API nel servizio app di Azure][apia-user]

## Funzionamento dell'autenticazione nel servizio app

Per l'autenticazione tramite uno dei provider di identità, è necessario innanzitutto configurare il provider di identità per l'applicazione. Il provider di identità fornirà quindi gli ID e i segreti da restituire al servizio app. Questo completa la relazione di trust e consente al servizio app di convalidare le asserzioni utente dal provider di identità, ad esempio i token di autenticazione.

Per consentire l'accesso a un utente con uno di questi provider, è necessario che sia reindirizzato a un endpoint di accesso per tale provider. Se i clienti usano un Web browser, è possibile configurare il servizio app affinché indirizzi automaticamente tutti gli utenti non autenticati all'endpoint di accesso. In caso contrario, sarà necessario indirizzare i clienti a `{your App Service base URL}/.auth/login/<provider>`, dove `<provider>` è uno dei provider _aad_, _facebook_, _google_, _microsoft_ e _twitter_. Le sezioni seguenti illustrano scenari di API e dispositivi mobili.

Gli utenti che interagiscono con l'applicazione tramite un Web browser avranno un cookie impostato in modo da poter rimanere autenticati durante lo spostamento nell'applicazione. Per altri tipi di client, ad esempio quelli mobili, verrà generato un token Web JSON (JWT), che dovrà essere presentato nell'intestazione `X-ZUMO-AUTH`. Gli SDK client delle app per dispositivi mobili gestiranno questa operazione automaticamente. In alternativa, un token di identità di Azure Active Directory o un token di accesso può essere incluso direttamente nell'intestazione `Authorization` come un [token di connessione](https://tools.ietf.org/html/rfc6750).

Il servizio app convaliderà qualsiasi cookie o token emesso dall'applicazione, consentendo agli utenti di essere autenticati. Per limitare l'accesso all'applicazione, vedere la sezione [Autorizzazione](#authorization) seguente.

### Autenticazione per dispositivi mobili con un SDK del provider

Dopo avere completato tutte le configurazioni nel back-end, è possibile modificare il client mobile per l'accesso con il servizio app. Di seguito, sono disponibili due approcci:

- Utilizzo di un SDK pubblicato da un provider di identità specificata per stabilire l'identità e quindi accedere al servizio App.
- Utilizzo di una singola riga di codice che consentire all’SDK del client di App per dispositivi mobili di fare accedere gli utenti.

>[AZURE.TIP] La maggior parte delle applicazioni devono utilizzare un provider SDK per ottenere un'esperienza di accesso più nativa e sfruttare il supporto dell’aggiornamento e altri vantaggi specifici del provider.

L’utilizzo di un SDK del provider consente all'esperienza di accesso di interagire più strettamente con la piattaforma del sistema operativo su cui è in esecuzione l'app. Inoltre, offre un token del provider e alcune informazioni utente sul client, che rende molto più semplice utilizzare le API Graph e personalizzare l'esperienza utente. In alcuni casi su blog e forum questo verrà definito come “flusso client" o "flusso verso il client" poichè il codice sul client gestisce l'accesso e il codice client ha accesso a un token del provider.

Dopo aver ottenuto un token del provider, deve essere inviato al servizio App per la convalida. Dopo che il servizio App ha convalidato il token, crea un nuovo token del servizio app che viene restituito al client. L'SDK client delle app per dispositivi mobili include metodi helper per la gestione di questo scambio e l'associazione automatica del token a tutte le richieste per il back-end dell'applicazione. Lo sviluppatore, se vuole, può anche memorizzare un riferimento al token del provider:

### Autenticazione per dispositivi mobili senza un SDK del provider

Se non si vuole configurare l'SDK di un provider, è possibile consentire alle app per dispositivi mobili del servizio app di eseguire l'accesso per conto l'utente. L’SDK del client di App per dispositivi mobili aprirà una visualizzazione web per il provider di propria scelta e completerà l'accesso. In alcuni casi su blog e forum questo verrà definito "flusso server" o "flusso verso il server", perché il server gestisce l'accesso e l'SDK del client non riceve mai il token del provider.

Il codice necessario per avviare questo flusso è illustrato nell'esercitazione di autenticazione per ogni piattaforma. Alla fine del flusso, l'SDK del client ha un token del servizio app che viene associato automaticamente a tutte le richieste per il back-end dell'applicazione.

### Autenticazione da servizio a servizio

Oltre a concedere agli utenti l'accesso all'applicazione, è anche possibile consentire a un'altra applicazione attendibile di chiamare l'API personalizzata. Aad esempio, è possibile che un'applicazione di servizio App chiami un'API in un'altra. In questo scenario si ottiene un token usando le credenziali per un account del servizio invece delle credenziali dell'utente finale. Un account del servizio è noto anche come *entità servizio* in Azure Active Directory e l'autenticazione che usa un account di questo tipo è nota anche come scenario da servizio a servizio.

>[AZURE.IMPORTANT] Poiché vengono eseguite su dispositivi del cliente, le applicazioni per dispositivi mobili _non_ sono considerate applicazioni attendibili e non devono usare un flusso dell'entità servizio. È invece necessario usare uno dei flussi utente descritti sopra.

Per gli scenari da servizio a servizio, il servizio app può proteggere l'applicazione con Azure Active Directory. L'applicazione chiamante deve fornire solo un token di autorizzazione dell'entità servizio AAD ottenuto fornendo l'ID client e il segreto client da AAD. Un esempio di questo scenario che usa le app per le API ASP.NET è illustrato nell'esercitazione [Autenticazione dell'entità servizio per app per le API][apia-service].

Per gestire uno scenario da servizio a servizio senza l'autenticazione del servizio app, è possibile usare i certificati client o l'autenticazione di base. Per informazioni sui certificati client in Azure, vedere [Come configurare l'autenticazione reciproca TLS per un'app Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Per informazioni sull'autenticazione di base in ASP.NET, vedere il blog sui [filtri di autenticazione nell'API Web 2 ASP.NET](http://www.asp.net/web-api/overview/security/authentication-filters).

L'autenticazione dell'account del servizio da un'app per la logica del servizio app a un'app per le API è un caso speciale ed è illustrata nell'articolo [Uso dell'API personalizzata ospitata nel servizio app con le app per la logica](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Funzionamento dell'autorizzazione nel servizio app

Si ha il controllo completo sulle richieste consentite per accedere all'applicazione. Autenticazione servizio app/L'autorizzazione può essere configurata con uno qualsiasi dei comportamenti seguenti:

- Consentire solo alle richieste autenticate di raggiungere l'applicazione.

	Se una richiesta anonima viene ricevuta da un browser, il servizio app la reindirizzerà alla pagina di accesso del provider di identità scelto. Se la richiesta proviene da un dispositivo mobile, verrà restituita una risposta HTTP _401 non autorizzato_.

	Con questa opzione non è necessario scrivere alcun codice di autenticazione nell'app. Se è necessaria un'autorizzazione più granulare, le informazioni sull'utente sono disponibili nel codice.

- Consentire alle richieste di raggiungere l'applicazione, ma convalidare le richieste autenticate e passare le informazioni di autenticazione nelle intestazioni HTTP.

	Questa opzione rinvia le decisioni di autorizzazione al codice dell'applicazione. Offre maggiore flessibilità nella gestione delle richieste anonime, ma richiede la scrittura di codice.
	
- Consentire a tutte le richieste di raggiungere l'applicazione e non eseguire alcuna operazione sulle informazioni di autenticazione nelle richieste.

	In questo caso la funzionalità Autenticazione/Autorizzazione è disabilitata. Le attività di autenticazione e autorizzazione dipendono interamente dal codice dell'applicazione.

I comportamenti precedenti sono controllati dall'opzione **Azione da eseguire quando la richiesta non è autenticata** nel portale. La scelta di "Accedi con" per uno dei provider comporterà l'autenticazione di tutte le richieste. "Consenti richiesta (nessuna azione)" rinvia la decisione di autorizzazione al codice, ma fornisce comunque informazioni di autenticazione. Se si vuole che il codice gestisca tutti gli elementi, è possibile disabilitare la funzionalità Autenticazione/Autorizzazione.

## Utilizzo delle identità utente nell'applicazione

Il servizio app passa informazioni relative agli utenti all'applicazione usando intestazioni speciali. Queste intestazioni non sono consentite da richieste esterne e saranno presenti solo se impostate per l'autenticazione/autorizzazione dal servizio app. Ecco alcune intestazioni di esempio:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Il codice scritto in qualsiasi linguaggio o framework può ottenere le informazioni necessarie da queste intestazioni. Per le app ASP.NET 4.6, ClaimsPrincipal viene impostata automaticamente con i valori appropriati.

L'applicazione può anche ottenere altri dettagli sull'utente tramite una richiesta HTTP GET sull'endpoint `/.auth/me` dell'applicazione. Se un token valido è stato incluso nella richiesta, verrà restituito un payload JSON con informazioni dettagliate sul provider in uso, il token del provider sottostante e altre informazioni sull'utente. Gli SDK del server delle app per dispositivi mobili forniscono metodi helper per l'utilizzo di questi dati ([Node.js](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md/#howto-tables-getidentity), [.NET](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md/#user-info)).

## Documentazione e risorse aggiuntive

### Provider di identità
Le esercitazioni seguenti illustrano come configurare il servizio App per sfruttare provider di autenticazione diversi:

- [Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory][AAD]
- [Come configurare un'applicazione per usare l'account di accesso di Facebook][Facebook]
- [Come configurare un'applicazione per usare l'account di accesso di Google][Google]
- [Come configurare un'applicazione per usare l'account di accesso Microsoft][MSA]
- [Come configurare un'applicazione per usare l'account di accesso di Twitter][Twitter]

Se si vuole usare un sistema di identità diverso da quelli qui forniti, è anche possibile sfruttare l'[anteprima del supporto per l'autenticazione personalizzata nell'SDK del server .NET per app per dispositivi mobili di Azure][custom-auth] che è possibile usare nelle app Web, per dispositivi mobili o per le API.

### Applicazioni per dispositivi mobili
Le esercitazioni seguenti illustrano come aggiungere l'autenticazione ai client per dispositivi mobili mediante il flusso verso il server:

- [Aggiungere l'autenticazione all'app iOS][iOS]
- [Aggiungere l'autenticazione all'app Android][Android]
- [Aggiungere l'autenticazione all'app Windows][Windows]
- [Aggiungere l'autenticazione all'app Xamarin.iOS][Xamarin.iOS]
- [Aggiungere l'autenticazione all'app Xamarin.Android][Xamarin.Android]
- [Aggiungere l'autenticazione all'app Xamarin.Forms][Xamarin.Forms]
- [Aggiungere l'autenticazione all'app Cordova][Cordova]

Se si vuole usare il l flusso verso il client per AAD:

- [Usare Active Directory Authentication Library per iOS][ADAL-iOS]
- [Usare Active Directory Authentication Library per Android][ADAL-Android]
- [Usare Active Directory Authentication Library per Windows e Xamarin][ADAL-dotnet]

### Applicazioni per le API
Le esercitazioni seguenti illustrano come proteggere le app per le API:

- [Autenticazione utente per le app per le API nel servizio app di Azure][apia-user]
- [Autenticazione dell'entità servizio per app per le API nel servizio app di Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md/#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md/#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md/#adal

<!---HONumber=AcomDC_0504_2016-->