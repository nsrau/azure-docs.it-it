<properties 
	pageTitle="Concetti chiave di Gestione API" 
	description="Informazioni su API, prodotti, ruoli, gruppi e altri concetti chiave di Gestione API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Come importare la definizione di un'API con le operazioni in Gestione API di Azure

In Gestione API è possibile creare nuove API e aggiungere manualmente le operazioni oppure è possibile importare l'API insieme alle operazioni in un unico passaggio.

Le API e le relative operazioni possono essere importate usando i seguenti formati.

-	WADL
-	Swagger

In questa guida viene illustrato come creare una nuova API e importarne le operazioni in un unico passaggio. Per informazioni su come creare un'API e aggiungere le operazioni, vedere [Come creare le API][] e [Come aggiungere operazioni a un'API][].

## <a name="import-api"> </a>Importare un'API

Le API vengono create e configurate nel portale di pubblicazione. Per accedere al portale di pubblicazione, fare clic su **Gestisci** nel portale di Azure per il servizio Gestione API. Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][] nell'esercitazione [Introduzione a Gestione API di Azure][].

![Portale di pubblicazione][api-management-management-console]

Fare clic su **API** dal menu **Gestione API** sulla sinistra, quindi scegliere **Importa API**.

![Importa API][api-management-import-apis]

La finestra **Importa API** contiene tre schede che corrispondono alle tre modalità con cui è possibile fornire la specifica API.

-	**Da Appunti** consente di incollare la specifica API nella casella di testo indicata.
-	**Da file** consente di sfogliare e selezionare il file che contiene la specifica API.
-	**Da URL** consente di fornire l'URL alla specifica per l'API.

![Formato di importazione API][api-management-import-api-clipboard]

Dopo aver fornito la specifica API, usare i pulsanti di opzione a destra per indicare il formato della specifica. Sono supportati i seguenti formati.

-	WADL
-	Swagger

Quindi, immettere un **Suffisso dell'URL dell'API Web**. Il suffisso viene aggiunto all'URL di base del servizio Gestione API. L'URL di base è comune a tutte le API ospitate in un'istanza di un servizio Gestione API. Gestione API distingue le API in base al suffisso, quindi è necessario che questo sia univoco per ciascuna API in ciascuna istanza del servizio Gestione API.

Dopo aver immesso tutti i valori, fare clic su **Salva** per creare l'API e le operazioni associate.

>[AZURE.NOTE]Per un'esercitazione relativa all'importazione di un'API di calcolatrice di base in formato Swagger, vedere [Gestire la prima API in Gestione API di Azure](api-management-get-started.md).

## <a name="export-api"> </a> Esportare un'API

Oltre a importare nuove API, è possibile esportare le definizioni delle API dal portale di pubblicazione. Per farlo, fare clic su **Esporta API** dalla scheda **Riepilogo** dell'**API**.

![Esporta API][api-management-export-api]

Le API possono essere esportate con WADL o Swagger. Selezionare il formato desiderato, fare clic su **Salva** e scegliere il percorso in cui salvare il file.

![Formato di esportazione API][api-management-export-api-format]

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver creato un'API ed importato le operazioni, è possibile rivedere e configurare tutte le impostazioni aggiuntive, aggiungere l'API a un prodotto e pubblicarla in modo che sia disponibile per gli sviluppatori. Per altre informazioni, vedere le seguenti guide.

-	[Come configurare le impostazioni API][]
-	[Come creare e pubblicare un prodotto][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Introduzione a Gestione API di Azure]: api-management-get-started.md
[Creare un'istanza del servizio Gestione API]: api-management-get-started.md#create-service-instance

[Come aggiungere operazioni a un'API]: api-management-howto-add-operations.md
[Come creare e pubblicare un prodotto]: api-management-howto-add-products.md
[Come creare le API]: api-management-howto-create-apis.md
[Come configurare le impostazioni API]: api-management-howto-create-apis.md#configure-api-settings

<!---HONumber=62-->