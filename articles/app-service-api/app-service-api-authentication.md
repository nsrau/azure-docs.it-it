<properties
	pageTitle="Autenticazione e autorizzazione per app per le API nel servizio app di Azure | Microsoft Azure"
	description="Informazioni sui servizi di autenticazione e autorizzazione forniti dal servizio app di Azure per app per le API."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# Autenticazione e autorizzazione per app per le API nel servizio app di Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Panoramica 

Questo articolo descrive le opzioni per la gestione dell'autenticazione e dell'autorizzazione per le app per le API nel servizio app di Azure.

Il diagramma seguente illustra alcune caratteristiche chiave dell'autenticazione del servizio app:

* Pre-elabora le richieste API in ingresso e offre diverse opzioni per la quantità di autenticazione da eseguire nel proprio codice. 
* Supporta cinque provider di autenticazione: Azure Active Directory, Facebook, Google, Twitter e account Microsoft.
* Funziona per l'autenticazione sia dell'utente finale che dell'entità servizio. 
* Presenta lo stesso funzionamento per le app per le API, le app Web e le app per dispositivi mobili.

![](./media/app-service-api-authentication/api-apps-overview.png)

## Pre-elaborazione di richieste in ingresso

Il servizio app può impedire che le richieste HTTP anonime raggiungano l'app per le API o autenticare le richieste con token prima che raggiungano l'app per le API. Per un'app per le API è possibile configurare una delle tre opzioni seguenti:

1. Consentire solo alle richieste autenticate di raggiungere l'app per le API.

	Se viene ricevuta una richiesta anonima da un browser, il servizio app la reindirizza a una pagina di accesso.

	L'opzione è utile se si conosce in anticipo il provider di autenticazione (Google, Twitter e così via) da usare ed è possibile configurare il servizio app per la gestione del processo di accesso. In alternativa, è possibile specificare un URL a cui il servizio app deve reindirizzare le richieste anonime. Si può quindi offrire agli utenti la possibilità di scegliere il provider di autenticazione.

	Con questa opzione non è necessario scrivere codice di autenticazione nell'app e l'autorizzazione risulta semplificata perché le attestazioni più importanti sono fornite nelle intestazioni HTTP.

2. Consentire a tutte le richieste di raggiungere l'app per le API, ma convalidare le richieste autenticate e passare le informazioni di autenticazione nelle intestazioni HTTP.

	Questa opzione offre una maggiore flessibilità nella gestione delle richieste anonime e semplifica la scrittura di codice che richiede l'accesso alle attestazioni più comuni. A differenza dell'opzione 1, per impedire agli utenti anonimi di usare l'API è necessario scrivere codice.

3. Consentire a tutte le richieste di raggiungere l'API e non eseguire alcuna operazione sulle informazioni di autenticazione nelle richieste.

	Questa opzione lascia le attività di autenticazione e autorizzazione interamente al codice dell'applicazione.

Le opzioni sono selezionabili nel pannello **Autenticazione/Autorizzazione** del [portale di Azure](https://portal.azure.com/).

![](./media/app-service-api-authentication/authblade.png)

Per le opzioni 1 e 2, attivare l'**autenticazione del servizio app**, e scegliere **Accedi** o **Consenti richiesta (nessuna azione)** nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata**. Se si seleziona **Accedi**, è necessario scegliere un provider di autenticazione e configurarlo.

![](./media/app-service-api-authentication/actiontotake.png)
 
## Indipendente dal linguaggio

L'elaborazione dell'autenticazione del servizio app viene eseguita prima che le richieste raggiungano l'app per le API, ciò significa che le funzionalità di autenticazione sono compatibili con app per le API scritte in qualsiasi linguaggio o framework. L'API può essere basata su ASP.NET, Java, Node.js o qualsiasi framework che supporta il servizio app.

Il servizio app passa il token JWT nell'intestazione dell'autorizzazione di una richiesta HTTP e il codice scritto in qualsiasi linguaggio o framework può ottenere le informazioni necessarie dal token. Il servizio app permette anche di accedere più facilmente alle attestazioni più usate impostando alcune intestazioni speciali, ad esempio:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
In un'API .NET è possibile usare l'attributo `Authorize` e per l'autorizzazione con granularità fine è possibile scrivere facilmente codice basato su attestazioni, perché le informazioni sulle attestazioni sono popolate automaticamente nelle classi .NET.

## <a id="internal"></a> Autenticazione dell'account del servizio

È possibile usare l'autenticazione del servizio app anche per scenari interni, ad esempio per la chiamata da un'app per le API a un'altra. In questo scenario per l'autenticazione si possono usare le credenziali per un account del servizio anziché le credenziali dell'utente finale. Un account del servizio è noto anche come *entità servizio* in Azure Active Directory e l'autenticazione che usa un account di questo tipo è nota anche come scenario da servizio a servizio.

Per gli scenari da servizio a servizio è possibile proteggere l'app per le API chiamata usando Azure Active Directory e fornire un token AAD di autorizzazione dell'entità servizio durante la chiamata all'app per le API. Per richiedere il token è possibile fornire l'ID client e il segreto client dell'applicazione AAD. Non è necessario alcun codice speciale di Azure, come quello usato per la gestione del token zumo dei servizi mobili. Un esempio di questo scenario che usa le app per le API ASP.NET è illustrato nell'esercitazione [Autenticazione dell'entità servizio per app per le API](app-service-api-dotnet-service-principal-auth.md).

Per gestire uno scenario da servizio a servizio senza l'autenticazione del servizio app, è possibile usare i certificati client o l'autenticazione di base. Per informazioni sui certificati client in Azure, vedere [Come configurare l'autenticazione reciproca TLS per un'app Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Per informazioni su come configurare l'autenticazione di base in ASP.NET, vedere il blog sui [filtri di autenticazione nell'API Web 2 ASP.NET ](http://www.asp.net/web-api/overview/security/authentication-filters).

L'autenticazione dell'account da un'app per la logica del servizio app per un'app per le API è un caso speciale ed è illustrata nell'articolo [Uso dell'API personalizzata ospitata nel servizio app con App per la logica](../app-service-logic/app-service-logic-custom-hosted-api.md).

## Altre informazioni

Per altre informazioni sui servizi di autenticazione e autorizzazione nel servizio app di Azure, vedere il blog sull'[espansione di autenticazione e autorizzazione del servizio app](/blog/announcing-app-service-authentication-authorization/).

## Passaggi successivi

Questo articolo ha illustrato le funzionalità di autenticazione e autorizzazione delle app per le API del servizio app.

Se si segue la sequenza introduttiva di esercitazioni per ASP.NET e le app per le API, provare queste funzionalità nell'esercitazione successiva [Autenticazione utente per le app per le API del servizio app di Azure](app-service-api-dotnet-user-principal-auth.md).

Per altre informazioni sull'uso di Node e di Java nel servizio app di Azure, vedere il [centro per sviluppatori di Node.js](/develop/nodejs/) e il [centro per sviluppatori di Java](/develop/java/).

<!---HONumber=AcomDC_1217_2015-->