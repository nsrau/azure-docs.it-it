<properties 
	pageTitle="Come creare API in Gestione API di Azure" 
	description="Informazioni su come creare e configurare API in Gestione API di Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/25/2016" 
	ms.author="sdanie"/>

# Come creare API in Gestione API di Azure

Un'API in Gestione API rappresenta un set di operazioni che possono essere richiamate dalle applicazioni client. Le nuove API vengono create nel portale di pubblicazione e quindi vengono aggiunte le operazioni desiderate. Dopo l'aggiunta delle operazioni, l'API viene aggiunta a un prodotto e può essere pubblicata. Dopo la pubblicazione l'API può essere sottoscritta e usata dagli sviluppatori.

Questa guida illustra il primo passaggio del processo per creare e configurare una nuova API in Gestione API. Per altre informazioni sull'aggiunta di operazioni e sulla pubblicazione di un prodotto, vedere [Come aggiungere operazioni a un'API][] e [Come creare e pubblicare un prodotto][].

## <a name="create-new-api"> </a>Creare una nuova API

Le API vengono create e configurate nel portale di pubblicazione. Per accedere al portale di pubblicazione, fare clic su **Gestisci** nel portale di Azure classico per il servizio Gestione API.

![Portale di pubblicazione][api-management-management-console]

>Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][] nell'esercitazione [Introduzione a Gestione API di Azure][].

Fare clic su **API** dal menu **Gestione API** sulla sinistra, quindi scegliere **Aggiungi API**.

![Create API][api-management-create-api]

Usare la finestra **Aggiungi nuova API** per configurare la nuova API.

![Add new API][api-management-add-new-api]

Per configurare la nuova API, vengono usati i campi seguenti.

-	La casella **Titolo API Web** consente di specificare un nome univoco e descrittivo per l'API, che viene visualizzato nel portale di pubblicazione e in quello per sviluppatori.
-	Il valore specificato nella casella **URL del servizio Web** fa riferimento al servizio HTTP che implementa l'API e corrisponde all'indirizzo a cui Gestione API inoltra le richieste.
-	Il valore della casella **Suffisso dell'URL dell'API Web** viene aggiunto all'URL di base del servizio Gestione API. L'URL di base è comune a tutte le API ospitate da un'istanza del servizio Gestione API. Gestione API distingue le API in base al suffisso, quindi è necessario che questo sia univoco per ciascuna API di un editore specifico.
-	L'opzione **Schema URL API Web** determina il protocollo da usare per l'accesso all'API. Per impostazione predefinita è specificato il valore HTTPs.
-	Per aggiungere facoltativamente questa nuova API a un prodotto, fare clic sull’elenco a discesa **Prodotti (facoltativo)** e selezionare un prodotto. Questo passaggio può essere ripetuto più volte per aggiungere l'API a più prodotti.

Dopo avere configurato i valori desiderati, fare clic su **Salva**. Una volta creata la nuova API, la pagina di riepilogo dell'API viene visualizzata nel portale di pubblicazione.

![API summary][api-management-api-summary]

## <a name="configure-api-settings"> </a>Configurare le impostazioni API

È possibile usare la scheda **Impostazioni** per verificare e modificare la configurazione di un'API. **Titolo API Web**, **URL servizio Web** e **Suffisso dell'URL dell'API Web** vengono impostati inizialmente alla creazione dell'API e possono essere modificati in questa scheda. **Descrizione** fornisce una descrizione facoltativa e l'opzione **Schema URL API Web** determina il protocollo da usare per l'accesso all'API.

![Impostazioni API][api-management-api-settings]

Per configurare l’autenticazione gateway per il servizio di back-end che implementa l'API, selezionare la scheda **Sicurezza**. L'elenco a discesa **Con credenziali** può essere usato per configurare l'**HTTP di base** o l'autenticazione con **Certificati client**. Per usare l'autenticazione HTTP di base, è sufficiente immettere le credenziali desiderate. Per informazioni sull'uso dell'autenticazione con certificati client, vedere [Come proteggere i servizi back-end usando l'autenticazione con certificati client in Gestione API di Azure][].

La scheda **Sicurezza** può essere usata anche per configurare l'**Autorizzazione utente** con OAuth 2.0. Per altre informazioni, vedere [Come autorizzare gli account per sviluppatori usando OAuth 2.0 in Gestione API di Azure][].

![Impostazioni dell'autenticazione di base][api-management-api-settings-credentials]

Fare clic su **Salva** per salvare le modifiche apportate alle impostazioni API.

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver creato un'API e configurato le impostazioni, i passaggi successivi consentono di aggiungere le operazioni all'API, aggiungere l'API a un prodotto e pubblicarla in modo che sia disponibile per gli sviluppatori. Per altre informazioni, vedere gli articoli seguenti.

-	[Come aggiungere operazioni a un'API][]
-	[Come creare e pubblicare un prodotto][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Come aggiungere operazioni a un'API]: api-management-howto-add-operations.md
[Come creare e pubblicare un prodotto]: api-management-howto-add-products.md

[Introduzione a Gestione API di Azure]: api-management-get-started.md
[Creare un'istanza del servizio Gestione API]: api-management-get-started.md#create-service-instance
[Come proteggere i servizi back-end usando l'autenticazione con certificati client in Gestione API di Azure]: api-management-howto-mutual-certificates.md
[Come autorizzare gli account per sviluppatori usando OAuth 2.0 in Gestione API di Azure]: api-management-howto-oauth2.md

<!---HONumber=AcomDC_0525_2016-->