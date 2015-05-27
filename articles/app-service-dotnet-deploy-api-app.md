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
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Distribuire un'app per le API nel servizio app di Azure 

## Panoramica

In questa esercitazione verrà distribuito un progetto API Web creato nell'[esercitazione precedente](app-service-dotnet-create-api-app.md) in una nuova [app per le API](app-service-api-apps-why-best-platform.md). Verrà usato Visual Studio per creare la risorsa di app per le API nel [servizio app di Azure](app-service-value-prop-what-is.md) e per distribuire il codice dell'API Web nell'app per le API di Azure.

### Altre opzioni di distribuzione

Sono disponibili molti altri modi per distribuire le app per le API. Un'app per le API è un'[app Web](app-service-web-overview.md) con funzionalità aggiuntive per l'hosting di servizi Web e tutti i [metodi di distribuzione disponibili per le app Web](web-sites-deploy.md) possono essere usati anche con le app per le API. L'app Web che ospita un'app per le API è denominata host di app per le API nel portale di anteprima di Azure ed è possibile configurare la distribuzione usando il pannello del portale dell'host di app per le API. Per informazioni sul pannello dell'host di app per le API, vedere [Gestire un'app per le API](app-service-api-manage-in-portal.md).

Poiché le app per le API sono basate sulle app Web, sarà anche possibile distribuire alle app per le API codice scritto per piattaforme diverse da ASP.NET. Per un esempio che usa Git per distribuire codice Node.js a un'app per le API, vedere [Creare un'app per le API Node.js nel servizio app di Azure](app-service-api-nodejs-api-app.md).
 
## Distribuire l'app per le API 

In questa sezione verranno illustrati i passaggi necessari per distribuire un'app per le API in una sottoscrizione di Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto (non sulla soluzione), quindi scegliere **Pubblica**. 

	![Opzione di menu per la pubblicazione del progetto](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

2. Scegliere la scheda **Profilo** e fare clic su **App per le API di Microsoft Azure (anteprima)**.

	![Finestra di dialogo Pubblica sul Web](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

3. Fare clic su **Nuovo** per effettuare il provisioning di una nuova app per le API nella sottoscrizione di Azure.

	![Finestra di dialogo per la selezione di servizi API esistenti](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

4. Nella finestra di dialogo **Crea un'app per le API** immettere quanto segue:

	- In **Nome app per le API** immettere ContactsList. 
	- Se si dispone di più sottoscrizioni di Azure, selezionare quella da usare.
	- In **Piano di servizio app** selezionare uno dei piani di servizio app esistenti oppure selezionare **Crea nuovo piano di servizio app** e immettere il nome di un nuovo piano. 
	- In **Gruppo di risorse** selezionare i gruppi di risorse esistenti oppure selezionare **Crea nuovo gruppo di risorse** e immettere un nome. Il nome deve essere univoco; provare a usare il nome dell'app come un prefisso e ad aggiungere alcune informazioni personali, ad esempio l'ID Microsoft (senza il simbolo @).  
	- In **Livello di accesso** selezionare **Disponibile per chiunque**. Questa opzione renderà l'API pubblica completamente, il che è corretto per questa esercitazione. È possibile in un secondo momento limitare l'accesso tramite il portale di anteprima di Azure.
	- In **Area** selezionare un'area nelle vicinanze.  

	![Finestra di dialogo per la configurazione di app Web di Microsoft Azure](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

5. Fare clic su **OK** per creare l'app per le API nella sottoscrizione. Poiché questo processo può richiedere alcuni minuti, Visual Studio visualizza una finestra di conferma.

	![Messaggio di conferma dell'inizio della creazione del servizio API](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

6. Fare clic su **OK** nella finestra di conferma. Il processo di provisioning crea il gruppo di risorse e l'app per le API nella sottoscrizione di Azure. Visual Studio mostra l'avanzamento nella finestra **Attività del servizio app di Azure**.

	![Notifica sullo stato tramite la finestra Attività del servizio app di Azure](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

7. Dopo avere effettuato il provisioning dell'app per le API, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** per aprire nuovamente la finestra di dialogo di pubblicazione. Il profilo di pubblicazione creato nel passaggio precedente deve essere già selezionato. Fare clic su **Pubblica** per avviare il processo di distribuzione.

	![Distribuzione dell'app per le API](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

La finestra **Attività del servizio app di Azure** mostra l'avanzamento della distribuzione.

![Notifica sullo stato della finestra Attività del servizio app di Azure](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

## Visualizzare l'app nel portale di anteprima di Azure

In questa sezione si passerà al portale per visualizzare le impostazioni di base disponibili per le app per le API e apportare le modifiche iterative all'app per le API. Con ogni distribuzione il portale rispecchierà le modifiche che si desidera apportare all'app per le API.

1. Nel browser passare al [portale di anteprima di Azure](https://portal.azure.com). 

2. Scegliere il pulsante **Sfoglia** sulla barra laterale e selezionare **App per le API**.

	![Opzioni di esplorazione nel portale di Azure](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

3. Selezionare l'API creata dall'elenco di app per le API nella sottoscrizione.

	![Elenco di app per le API](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

4. Fare clic su **Definizione API**. Il pannello **Definizione API** mostra l'elenco di operazioni API definito al momento della creazione dell'app.

	![Definizione dell'API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. È ora possibile tornare al progetto in Visual Studio e aggiungere il codice seguente al file **ContactsController.cs**. Questo codice aggiunge un metodo **Post** che consente di registrare nuove istanze `Contact` all'API.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Aggiunta del metodo Post al controller](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

6. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

	![Menu contestuale per la pubblicazione del progetto](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

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

Si è appreso come le funzionalità di distribuzione diretta in Visual Studio consentono di eseguire rapidamente le attività di iterazione e distribuzione e di testare il corretto funzionamento dell'API. Nell'[esercitazione successiva](app-service-dotnet-remotely-debug-api-app.md) verrà illustrato come eseguire il debug dell'app per le API mentre è in esecuzione in Azure.

<!--HONumber=54-->