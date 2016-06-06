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

L'autenticazione/autorizzazione del servizio app è una funzionalità che consente all'applicazione di eseguire la procedura di accesso degli utenti in modo che non sia necessario modificare il codice nel back-end dell'app. Fornisce un modo semplice per proteggere l'applicazione e utilizzare dati per-utente.

Il servizio app usa identità federate, in cui un provider di identità di terze parti archivia gli account e autentica gli utenti. L'applicazione si basa sulle informazioni di identità del provider in modo da non doverle archiviare. Il servizio app supporta cinque provider di identità predefiniti: Azure Active Directory, Facebook, Google, Account Microsoft e Twitter. L'app può usare un numero qualsiasi di questi provider di identità per offrire agli utenti diverse opzioni di accesso. Per espandere il supporto predefinito è possibile integrare un altro provider di identità o una [soluzione di gestione delle identità personalizzata][custom-auth].

Se si vuole iniziare subito, vedere una delle esercitazioni seguenti:

- [Aggiungere l'autenticazione all'app iOS][iOS] (o [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] o [Cordova])
- [Autenticazione utente per le app per le API nel servizio app di Azure][apia-user]
- [Introduzione a Servizio app di Azure - Parte 2][web-getstarted]

## Funzionamento dell'autenticazione nel servizio app

Per eseguire l'autenticazione con uno dei provider di identità, è prima necessario configurare il provider di identità per l'applicazione. Il provider di identità fornirà quindi gli ID e i segreti da includere nel servizio app. Questo completa la relazione di trust e consente al servizio app di convalidare le asserzioni utente dal provider di identità, ad esempio i token di autenticazione.

Per consentire a un utente di accedere tramite uno di questi provider, è necessario che l'utente sia reindirizzato a un apposito endpoint per tale provider. Se i clienti usano un Web browser, è possibile configurare il servizio app in modo che indirizzi automaticamente tutti gli utenti non autenticati all'endpoint che esegue la procedura di accesso degli utenti. In caso contrario, sarà necessario indirizzare i clienti a `{your App Service base URL}/.auth/login/<provider>`, dove `<provider>` è uno dei valori seguenti: aad, facebook, google, microsoft o twitter. Gli scenari relativi alle API e ai dispositivi mobili sono illustrati nelle sezioni successive di questo articolo.

Gli utenti che interagiscono con l'applicazione tramite un Web browser avranno un cookie impostato in modo da rimanere autenticati mentre esplorano l'applicazione. Per altri tipi di client, ad esempio quelli per dispositivi mobili, verrà rilasciato un token Web JSON (JWT), che dovrà essere presentato nell'intestazione `X-ZUMO-AUTH`. Gli SDK client delle app per dispositivi mobili gestiranno questa operazione automaticamente. In alternativa, un token di identità di Azure Active Directory o un token di accesso può essere incluso direttamente nell'intestazione `Authorization` come [token di connessione](https://tools.ietf.org/html/rfc6750).

Il servizio app convaliderà qualsiasi cookie o token rilasciato dall'applicazione per l'autenticazione degli utenti. Per limitare l'accesso all'applicazione, vedere la sezione sul [funzionamento dell'autorizzazione](#authorization) più avanti in questo articolo.

### Autenticazione per dispositivi mobili con un SDK del provider

Dopo aver completato tutte le configurazioni nel back-end, è possibile modificare i client per dispositivi mobili per l'accesso con il servizio app. Di seguito, sono disponibili due approcci:

- Uso di un SDK pubblicato da un determinato provider di identità per stabilire l'identità e quindi accedere al servizio app.
- Uso di una singola riga di codice per consentire all'SDK client delle app per dispositivi mobili di eseguire la procedura di accesso degli utenti.

>[AZURE.TIP] La maggior parte delle applicazioni deve usare un SDK del provider per offrire agli utenti un'esperienza di accesso più coerente e per usufruire del supporto dell'aggiornamento e di altri vantaggi specificati dal provider.

Quando si usa un SDK del provider, gli utenti possono accedere a un'esperienza più integrata con il sistema operativo in cui viene eseguita l'app. Inoltre, offre un token del provider e alcune informazioni utente sul client, che rende molto più semplice utilizzare le API Graph e personalizzare l'esperienza utente. In blog e forum si trova talvolta la definizione "flusso client" o "flusso verso il client", perché il codice sul client esegue la procedura di accesso degli utenti e il codice client ha accesso a un token del provider.

Dopo che è stato ottenuto un token del provider, questo deve essere inviato al servizio app per la convalida. Dopo che il servizio app ha convalidato il token, crea un nuovo token del servizio app che viene restituito al client. L'SDK client delle app per dispositivi mobili include metodi helper per la gestione di questo scambio e l'associazione automatica del token a tutte le richieste per il back-end dell'applicazione. Facoltativamente, gli sviluppatori possono anche mantenere un riferimento al token del provider.

### Autenticazione per dispositivi mobili senza un SDK del provider

Se non si vuole configurare un SDK del provider, è possibile consentire alla funzionalità App per dispositivi mobili del servizio app di Azure di eseguire la procedura di accesso per conto dell'utente. L'SDK client delle app per dispositivi mobili aprirà una visualizzazione Web per il provider scelto e completerà l'accesso dell'utente. In blog e forum si trova talvolta la definizione "flusso server" o "flusso verso il server", perché il server gestisce il processo di accesso degli utenti e l'SDK client non riceve mai il token del provider.

Il codice necessario per avviare questo flusso è incluso nell'esercitazione sull'autenticazione per ogni piattaforma. Alla fine del flusso, l'SDK del client ha un token del servizio app che viene associato automaticamente a tutte le richieste per il back-end dell'applicazione.

### Autenticazione da servizio a servizio

Pur potendo concedere agli utenti l'accesso all'applicazione, è anche possibile considerare attendibile un'altra applicazione per la chiamata all'API. È ad esempio possibile che un'app Web chiami un'API in un'altra app Web. In questo scenario, per ottenere un token si usano le credenziali di un account del servizio invece di credenziali utente. Un account del servizio è noto anche come *entità servizio* in Azure Active Directory, mentre l'autenticazione che usa un account di questo tipo è nota anche come scenario da servizio a servizio.

>[AZURE.IMPORTANT] Dato che le app per dispositivi mobili vengono eseguite su dispositivi del cliente, tali applicazioni _non_ sono considerate applicazioni attendibili e non devono usare un flusso dell'entità servizio. È invece necessario usare un flusso utente descritto sopra.

Per gli scenari da servizio a servizio, il servizio app può proteggere l'applicazione con Azure Active Directory. L'applicazione chiamante deve solo fornire un token di autorizzazione dell'entità servizio di Azure Active Directory ottenuto fornendo l'ID client e il segreto client da Azure Active Directory. Un esempio di questo scenario che usa le app per le API ASP.NET è illustrato nell'esercitazione [Autenticazione dell'entità servizio per app per le API][apia-service].

Per gestire uno scenario da servizio a servizio con l'autenticazione del servizio app, è possibile usare i certificati client o l'autenticazione di base. Per informazioni sui certificati client in Azure, vedere [Come configurare l'autenticazione reciproca TLS per un'app Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Per informazioni sull'autenticazione di base in ASP.NET, vedere il blog sui [filtri di autenticazione nell'API Web 2 ASP.NET](http://www.asp.net/web-api/overview/security/authentication-filters).

L'autenticazione dell'account del servizio da un'app per la logica del servizio app a un'app per le API è un caso speciale ed è illustrata nell'articolo [Uso dell'API personalizzata ospitata nel servizio app con App per la logica](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Funzionamento dell'autorizzazione nel servizio app

Si ha il controllo completo sulle richieste che possono accedere all'applicazione. Autenticazione servizio app/L'autorizzazione può essere configurata con uno qualsiasi dei comportamenti seguenti:

- Consentire solo alle richieste autenticate di raggiungere l'applicazione.

	Se un browser riceve una richiesta anonima, il servizio app la reindirizzerà a una pagina del provider di identità scelto per consentire l'accesso degli utenti. Se la richiesta proviene da un dispositivo mobile, verrà restituita una risposta HTTP _401 Non autorizzato_.

	Con questa opzione non è necessario scrivere alcun codice di autenticazione nell'app. Se è necessaria un'autorizzazione più specifica, le informazioni sull'utente sono disponibili nel codice.

- Consentire alle richieste di raggiungere l'applicazione, ma convalidare le richieste autenticate e passare le informazioni di autenticazione nelle intestazioni HTTP.

	Questa opzione rinvia le decisioni di autorizzazione al codice dell'applicazione. Offre maggiore flessibilità nella gestione delle richieste anonime, ma è necessario scrivere codice.

- Consentire a tutte le richieste di raggiungere l'applicazione e non eseguire alcuna azione sulle informazioni di autenticazione nelle richieste.

	In questo caso la funzionalità Autenticazione/Autorizzazione è disabilitata. Le attività di autenticazione e autorizzazione dipendono interamente dal codice dell'applicazione.

I comportamenti precedenti sono controllati dall'opzione **Azione da eseguire quando la richiesta non è autenticata** nel portale di Azure. Se si sceglie **Accedi con *nome provider***, tutte le richieste devono essere autenticate. **Consenti richiesta (nessuna azione)** rinvia la decisione di autorizzazione al codice, ma fornisce comunque informazioni di autenticazione. Se si vuole che il codice gestisca tutti gli elementi, è possibile disabilitare la funzionalità Autenticazione/Autorizzazione.

## Utilizzo delle identità utente nell'applicazione

Il servizio app passa alcune informazioni utente all'applicazione usando intestazioni speciali. Le richieste esterne non consentono queste intestazioni e saranno presenti solo se impostate dall'autenticazione/autorizzazione del servizio app. Ecco alcune intestazioni di esempio:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Il codice scritto in qualsiasi linguaggio o framework può ottenere le informazioni necessarie da queste intestazioni. Per le app ASP.NET 4.6, i valori appropriati per **ClaimsPrincipal** vengono impostati automaticamente.

L'applicazione può anche ottenere altri dettagli sull'utente tramite una richiesta HTTP GET sull'endpoint `/.auth/me` dell'applicazione. Un token valido incluso nella richiesta restituirà un payload JSON con informazioni dettagliate sul provider in uso, il token del provider sottostante e altre informazioni utente. Gli SDK server delle app per dispositivi mobili forniscono metodi di supporto per l'uso di questi dati. Per altre informazioni, vedere [Come usare Node.js SDK per App per dispositivi mobili di Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) e [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## Documentazione e risorse aggiuntive

### Provider di identità
Le esercitazioni seguenti illustrano come configurare il servizio app per usare diversi provider di autenticazione:

- [Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory][AAD]
- [Come configurare un'applicazione per usare l'account di accesso di Facebook][Facebook]
- [Come configurare un'applicazione per usare l'account di accesso di Google][Google]
- [Come configurare un'applicazione per usare l'account di accesso Microsoft][MSA]
- [Come configurare un'applicazione per usare l'account di accesso di Twitter][Twitter]

Se si vuole usare un sistema di gestione delle identità diverso da quelli qui forniti, è anche possibile sfruttare il [supporto per l'anteprima per l'autenticazione personalizzata dell'SDK del server .NET per app per dispositivi mobili di Azure][custom-auth], che può essere usato in app Web, app per dispositivi mobili o app per le API.

### Applicazioni Web
Le esercitazioni seguenti illustrano come aggiungere l'autenticazione a un'applicazione Web:

- [Introduzione a Servizio app di Azure - Parte 2][web-getstarted]

### Applicazioni per dispositivi mobili
Le esercitazioni seguenti illustrano come aggiungere l'autenticazione ai client per dispositivi mobili usando il flusso verso il server:

- [Aggiungere l'autenticazione all'app iOS][iOS]
- [Aggiungere l'autenticazione all'app Android][Android]
- [Aggiungere l'autenticazione all'app Windows][Windows]
- [Aggiungere l'autenticazione all'app Xamarin.iOS][Xamarin.iOS]
- [Aggiungere l'autenticazione all'app Xamarin.Android][Xamarin.Android]
- [Aggiungere l'autenticazione all'app Xamarin.Forms][Xamarin.Forms]
- [Aggiungere l'autenticazione all'app Cordova][Cordova]

Se si vuole usare il flusso verso il client per Azure Active Directory, consultare le risorse seguenti:

- [Usare Active Directory Authentication Library per iOS][ADAL-iOS]
- [Usare Active Directory Authentication Library per Android][ADAL-Android]
- [Usare Active Directory Authentication Library per Windows e Xamarin][ADAL-dotnet]

### Applicazioni per le API
Le esercitazioni seguenti illustrano come proteggere le app per le API:

- [Autenticazione utente per le app per le API nel servizio app di Azure][apia-user]
- [Autenticazione dell'entità servizio per app per le API nel servizio app di Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal

<!---HONumber=AcomDC_0525_2016-->