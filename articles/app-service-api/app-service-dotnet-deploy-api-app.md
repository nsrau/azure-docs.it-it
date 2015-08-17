<properties 
	pageTitle="Distribuire un'app per le API nel servizio app di Azure" 
	description="Informazioni su come distribuire un progetto app per le API nella sottoscrizione di Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="bradyg"/>

# Distribuire un'app per le API nel servizio app di Azure 

## Panoramica

In questa esercitazione verrà distribuito un progetto API Web creato nell'[esercitazione precedente](app-service-dotnet-create-api-app.md) in una nuova [app per le API](app-service-api-apps-why-best-platform.md). Verrà usato Visual Studio per creare la risorsa di app per le API nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md) e per distribuire il codice dell'API Web nell'app per le API di Azure.

### Altre opzioni di distribuzione

Sono disponibili molti altri modi per distribuire le app per le API. Un'app per le API è un'[app Web](../app-service-web/app-service-web-overview.md) con funzionalità aggiuntive per l'hosting di servizi Web e tutti i [metodi di distribuzione disponibili per le app Web](../app-service-web/web-sites-deploy.md) possono essere usati anche con le app per le API. L'app Web che ospita un'app per le API è denominata host di app per le API nel portale di anteprima di Azure ed è possibile configurare la distribuzione usando il pannello del portale dell'host di app per le API. Per informazioni sul pannello dell'host di app per le API, vedere [Gestire un'app per le API](app-service-api-manage-in-portal.md).

Poiché le app per le API sono basate sulle app Web, sarà anche possibile distribuire alle app per le API codice scritto per piattaforme diverse da ASP.NET. Per un esempio che usa Git per distribuire codice Node.js a un'app per le API, vedere [Creare un'app per le API Node.js nel servizio app di Azure](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Creare un'app per le API in Azure 

In questa sezione si userà la procedura guidata **Pubblica sul Web** di Visual Studio per creare un'app per le API in Azure. Quando le istruzioni richiedono l'immissione di un nome per l'app per le API, indicare *ContactsList*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>Distribuire il codice nella nuova app per le API

Usare la stessa procedura guidata **Pubblica sul Web** per distribuire il codice nella nuova app per le API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Visualizzare l'app nel portale di anteprima di Azure

In questa sezione si visualizzeranno le impostazioni di base disponibili nel portale per le app per le API e si apporteranno modifiche iterative all'app per le API. Con ogni distribuzione il portale rispecchia le modifiche apportate all'app per le API.

1. Nel [portale di anteprima di Azure](https://portal.azure.com) passare al pannello **App per le API** relativo all'app per le API distribuita.

4. Fare clic su **Definizione API**.
 
	Il pannello **Definizione API** mostra l'elenco di operazioni API definito al momento della creazione dell'app.

	![Definizione dell'API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. È ora possibile tornare al progetto in Visual Studio e aggiungere il codice seguente al file **ContactsController.cs**.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Aggiunta del metodo Post al controller](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

	Questo codice aggiunge un metodo **Post** che consente di registrare nuove istanze `Contact` all'API.

6. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

	![Menu di scelta rapida per la pubblicazione del progetto](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. Fare clic sulla scheda **Impostazioni**.

8. Dall'elenco a discesa **Configurazione** selezionare **Debug**.

	![Impostazioni per Pubblica sul Web](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. Fare clic sulla scheda **Anteprima**.

10. Fare clic su **Avvia anteprima** per visualizzare le modifiche che verranno apportate.

	![Finestra di dialogo Pubblica sul Web](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. Fare clic su **Pubblica**.

12. Al termine del processo di pubblicazione, tornare al portale e chiudere e riaprire il pannello **Definizione API**. Verrà visualizzato il nuovo endpoint API appena creato e distribuito direttamente nella sottoscrizione di Azure.

	![Definizione dell'API](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Passaggi successivi

Si è appreso come le funzionalità di distribuzione diretta in Visual Studio consentono di eseguire rapidamente le attività di iterazione e distribuzione e di testare il corretto funzionamento dell'API. Nell'[esercitazione successiva](../app-service-dotnet-remotely-debug-api-app.md) verrà illustrato come eseguire il debug dell'app per le API mentre è in esecuzione in Azure.
 

<!---HONumber=August15_HO6-->