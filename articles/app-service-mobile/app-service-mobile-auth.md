<properties
	pageTitle="Autenticazione e autorizzazione in App per dispositivi mobili di Azure | Microsoft Azure"
	description="Riferimento concettuale e panoramica della funzionalità di autenticazione / autorizzazione nelle app per dispositivi mobili di Azure"
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikref"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="mahender"/>

# Autenticazione e autorizzazione in App per dispositivi mobili di Azure

## Informazioni sull’autenticazione / autorizzazione di servizio app

> [AZURE.NOTE] Questo argomento verrà trasferito in un unico argomento [Autenticazione/autorizzazione del servizio app](../app-service/app-service-authentication-overview.md), che illustra l'autenticazione e l'autorizzazione per app Web, app per dispositivi mobili e app per le API.

L'autenticazione / autorizzazione del servizio App è una funzionalità che consente all'applicazione di far accedere utenti senza necessità di apportare modifiche al codice nel back-end dell’app. Fornisce un modo semplice per proteggere l'applicazione e utilizzare dati per-utente.

Il servizio App usa identità federate, in cui un **provider di identità** di terze parti ("IDP") archivia account e autentica utenti e l'applicazione utilizza questa identità anziché la propria. Il servizio App supporta cinque provider di identità pronti all’uso: _Azure Active Directory_, _Facebook_, _Google_, _Account Microsoft_, e _Twitter_. È anche possibile espandere il supporto per le app integrando un altro provider di identità o una soluzione di identità personalizzata.

L’app può utilizzare un numero qualsiasi di questi provider di identità, pertanto è possibile offrire agli utenti finali diverse opzioni di modalità di accesso.

Se si desidera iniziare subito, vedere una delle esercitazioni seguenti:

- [Aggiungere l'autenticazione all'app iOS]
- [Aggiungere l'autenticazione all'app Xamarin.iOS]
- [Aggiungere l'autenticazione all'app Xamarin.Android]
- [Aggiungere l'autenticazione all'app Windows]

## Funzionamento dell'autenticazione

Per l'autenticazione tramite uno dei provider di identità, è necessario innanzitutto configurare il provider di identità per l'applicazione. Il provider di identità quindi fornirà gli ID e i segreti da rimandare all'applicazione. Questo completa la relazione di trust e consente al servizio App di convalidare l'identità fornita.

Questi passaggi vengono descritti negli argomenti seguenti:

- [Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory]
- [Come configurare un'applicazione per usare l'account di accesso di Facebook]
- [Come configurare un'applicazione per usare l'account di accesso di Google]
- [Come configurare un'applicazione per usare l'account di accesso Microsoft]
- [Come configurare un'applicazione per usare l'account di accesso di Twitter]

Dopo che sono state effettuate tutte le configurazioni nel back-end, è possibile modificare il client per l’accesso. Di seguito, sono disponibili due approcci:

- Utilizzo di una singola riga di codice che consentire all’SDK del client di App per dispositivi mobili di fare accedere gli utenti.
- Utilizzo di un SDK pubblicato da un provider di identità specificata per stabilire l'identità e quindi accedere al servizio App.

>[AZURE.TIP] La maggior parte delle applicazioni devono utilizzare un provider SDK per ottenere un'esperienza di accesso più nativa e sfruttare il supporto dell’aggiornamento e altri vantaggi specifici del provider.

### Funzionamento dell'autenticazione senza un SDK del provider

Se non si desidera configurare un SDK del provider, è possibile consentire ad App per dispositivi mobili di eseguire l'accesso per l'utente. L’SDK del client di App per dispositivi mobili aprirà una visualizzazione web per il provider di propria scelta e completerà l'accesso. In alcuni casi su blog e forum questo verrà definito "flusso server" o "flusso verso il server" perché il server gestisce l'accesso e l’SDK del client non riceve mai il token del provider.

Il codice necessario per avviare questo flusso è illustrato nell'esercitazione di autenticazione per ogni piattaforma. Alla fine del flusso, l’SDK del client dispone di un token del servizio App e il token viene associato automaticamente a tutte le richieste per il back-end.

### Funzionamento dell'autenticazione con un SDK del provider

L’utilizzo di un SDK del provider consente all'esperienza di accesso di interagire più strettamente con la piattaforma del sistema operativo su cui è in esecuzione l'app. Inoltre, offre un token del provider e alcune informazioni utente sul client, che rende molto più semplice utilizzare le API Graph e personalizzare l'esperienza utente. In alcuni casi su blog e forum questo verrà definito come “flusso client" o "flusso verso il client" poichè il codice sul client gestisce l'accesso e il codice client ha accesso a un token del provider.

Dopo aver ottenuto un token del provider, deve essere inviato al servizio App per la convalida. Alla fine del flusso, l’SDK del client dispone di un token del servizio App e il token viene associato automaticamente a tutte le richieste per il back-end. Lo sviluppatore, se vuole, può anche memorizzare un riferimento al token del provider:

## Funzionamento dell’autorizzazione

L'autenticazione / autorizzazione del servizio App espone diverse opzioni relative all’ **Azione da intraprendere quando la richiesta non è autenticata**. Prima che il codice ricevi una richiesta specifica, è possibile verificare il servizio App per vedere se la richiesta viene autenticata e se non, rifiutarla e tentare di richiedere all'utente di accedere prima di riprovare.

Una possibilità consiste nel reindirizzare le richieste non autenticate a uno dei provider di identità. In un browser web, questa operazione porterebbe effettivamente l'utente in una nuova pagina. Tuttavia, in questo modo il client per dispositivi mobili non può essere reindirizzato e le risposte non autenticate visualizzeranno una risposta HTTP _401 Unauthorized_. Detto questo, la prima richiesta che il client esegue dovrebbe sempre essere diretta all'endpoint di accesso e successivamente sarà possibile effettuare chiamate a qualsiasi altra API. Se si tenta di chiamare un'altra API prima dell'accesso, il client riceverà un errore.

Se si desidera avere un controllo più granulare su quali endpoint richiedono l'autenticazione, è inoltre possibile selezionare "Nessuna azione (consentire richiesta)" per le richieste non autenticate. In questo caso, tutte le decisioni relative all’autenticazione vengono rinviate al codice dell'applicazione. Ciò consente anche di consentire l'accesso a utenti specifici in base a regole di autorizzazione personalizzate.

## Documentazione

Le esercitazioni seguenti illustrano come aggiungere l’autenticazione ai client mobili mediante il servizio app:

- [Aggiungere l'autenticazione all'app iOS]
- [Aggiungere l'autenticazione all'app Xamarin.iOS]
- [Aggiungere l'autenticazione all'app Xamarin.Android]
- [Aggiungere l'autenticazione all'app Windows]

Le esercitazioni seguenti illustrano come configurare il servizio App per sfruttare provider di autenticazione diversi:

- [Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory]
- [Come configurare un'applicazione per usare l'account di accesso di Facebook]
- [Come configurare un'applicazione per usare l'account di accesso di Google]
- [Come configurare un'applicazione per usare l'account di accesso Microsoft]
- [Come configurare un'applicazione per usare l'account di accesso di Twitter]

Se si desidera utilizzare un sistema di identità diverso da quelli qui forniti, è anche possibile sfruttare l’[Anteprima supporto per l'autenticazione personalizzata nell’SDK del server .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Aggiungere l'autenticazione all'app iOS]: app-service-mobile-ios-get-started-users.md
[Aggiungere l'autenticazione all'app Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Aggiungere l'autenticazione all'app Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Aggiungere l'autenticazione all'app Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Come configurare un'applicazione per usare l'account di accesso di Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Come configurare un'applicazione per usare l'account di accesso di Google]: app-service-mobile-how-to-configure-google-authentication.md
[Come configurare un'applicazione per usare l'account di accesso Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Come configurare un'applicazione per usare l'account di accesso di Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

<!---HONumber=AcomDC_0511_2016-->