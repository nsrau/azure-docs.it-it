<properties
	pageTitle="Ripetere il training dei modelli di Machine Learning a livello di codice | Microsoft Azure"
	description="Informazioni su come ripetere il training di un modello a livello di codice e aggiornare il servizio Web per l'uso del modello appena sottoposto a training in Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="raymondl;garye;v-donglo"/>  


#Ripetere il training dei modelli di Machine Learning a livello di codice  

Come parte del processo di messa in funzione dei modelli di apprendimento automatico in Azure Machine Learning, è necessario sottoporre a training e salvare il modello. Lo si userà quindi per creare un servizio Web predicativo. Il servizio Web può quindi essere utilizzato in siti Web, dashboard e app per dispositivi mobili.

I modelli creati con Machine Learning in genere non sono statici. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello. Oppure potrebbe essere necessario applicare filtri per ottenere un subset dei dati e ripetere il training del modello.

La ripetizione del training può avvenire di frequente. Con la funzionalità delle API ripetizione del training a livello di codice, è possibile ripetere il training a livello di codice del modello usando le API di ripetizione del training e aggiornare il servizio Web con un modello di cui è stato appena eseguito il training.

Questo documento descrive il processo di ripetizione del training e illustra come usare le API per la ripetizione del training.

## Motivi per cui ripetere il training: definizione del problema  

Durante il processo di training di ML, viene eseguito il training di un modello usando un set di dati. I modelli creati con Machine Learning in genere non sono statici. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello. In altri scenari potrebbe essere necessario applicare filtri per ottenere un subset dei dati e ripetere il training del modello.

In questi scenari un'API a livello di codice offre un modo pratico per consentire all'utente dell'API di creare un client in grado di ripetere il training del modello una tantum o periodicamente usando i propri dati. Sarà quindi possibile valutare i risultati della ripetizione del training e aggiornare l'API del servizio Web per l'uso del modello appena sottoposto a training.

##Come ripetere il training: il processo end-to-end  

Per il processo sono innanzitutto necessari i componenti seguenti: un esperimento di training e un esperimento predittivo pubblicato come servizio Web. Per abilitare la ripetizione del training di un modello con training, l'esperimento di training deve essere pubblicato come servizio Web con l'output di un modello con training. In questo modo viene abilitato l'accesso dell'API al modello per la ripetizione del training.

Il processo per la configurazione della ripetizione del training prevede i passaggi seguenti:

![Panoramica del processo di ripetizione del training][1]

Diagramma 1: panoramica del processo di ripetizione del training

## Creare un esperimento di training
 
Per questo esempio, si userà "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" che fa parte degli esempi di Microsoft Azure Machine Learning.
	
Per creare l'esperimento:

1.	Accedere a Microsoft Azure Machine Learning Studio.
2.	Nell'angolo in basso a destra del dashboard fare clic su **New** (Nuovo).
3.	Tra gli esempi di Microsoft selezionare l'esempio 5.
4.	Per rinominare l'esperimento, nella parte superiore dell'area di disegno dell'esperimento selezionare il nome dell'esperimento "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset".
5.	Digitare Census Model.
6.	Fare clic su **Run** (Esegui) nella parte inferiore dell'area di disegno dell'esperimento.
7.	Fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Retraining Web Service** (Servizio Web di ripetizione del training).

 	![Esperimento iniziale.][2]  

Diagramma 2: esperimento iniziale.

## Creare un esperimento di assegnazione dei punteggi e pubblicarlo come servizio Web  

Ora viene creato un esperimento predicativo.

1.	Nella parte inferiore dell'area di disegno dell'esperimento, fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Predictive Web Service** (Servizio Web predittivo). In questo modo verrà salvato il modello come modello con training e verranno aggiunti i moduli di input e output del servizio Web.
2.	Fare clic su **Run**.
3.	Al termine dell'esecuzione dell'esperimento, fare clic su **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]). L'esperimento predittivo viene distribuito come servizio Web classico.

## Distribuire l'esperimento di training come servizio Web di training

Per ripetere il training del modello con training, è necessario distribuire l'esperimento di training creato come servizio Web di ripetizione del training. Per produrre nuovi modelli con training, questo servizio Web dovrà disporre di un modulo di output del servizio Web connesso al modulo [modello di training][train-model].

1. Per tornare all'esperimento di training, fare clic sull'icona Experiments (Esperimenti) nel riquadro sinistro e quindi sull'esperimento denominato Census Model.
2. Nella casella di ricerca Search Experiment Items (Cerca elementi esperimento) digitare Web Service.
3. Trascinare un modulo Web Service Input sull'area di disegno dell'esperimento e connetterne l'output al modulo Clean Missing Data.
4. Trascinare due moduli *Web Service Output* sull'area di disegno dell'esperimento. Connettere l'output del modulo *Train Model* a uno e l'output del modulo *Evaluate Model* all'altro. Con l'output del servizio Web per il modello di training è possibile ottenere il nuovo modello con training. L'output associato al modello di valutazione restituisce l'output del modello di valutazione del modulo.
5. Fare clic su **Run**.
6. Nella parte inferiore dell'area di disegno dell'esperimento, fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Predictive Web Service** (Servizio Web di ripetizione del training). L'esperimento di training viene distribuito come un servizio Web che produce un modello con training e risultati di valutazione del modello. Viene visualizzato il **Dashboard** del servizio Web con la chiave API e la pagina della guida dell'API per l'esecuzione batch. È possibile usare solo il metodo di esecuzione batch per la creazione di modelli di training.
  
Al termine dell'esecuzione dell'esperimento, il flusso di lavoro risultante dovrebbe avere un aspetto simile al seguente:

![Flusso di lavoro risultante dopo l'esecuzione.][4]  

Diagramma 3: flusso di lavoro risultante dopo l'esecuzione.

## Aggiungere un nuovo endpoint
 
Il servizio Web predittivo distribuito è l'endpoint dei punteggi predefinito. Gli endpoint predefiniti vengono mantenuti sincronizzati con gli esperimenti di training e di assegnazione dei punteggi di origine, quindi il modello con training per l'endpoint predefinito non può essere sostituito.

Per creare un nuovo endpoint dei punteggi, nel servizio Web predittivo che può essere aggiornato con il modello con training:

>[AZURE.NOTE] Assicurarsi di aggiungere l'endpoint al servizio Web predittivo, non al servizio Web di training. Se sono stati distribuiti correttamente sia un servizio Web di training che uno predittivo, verranno visualizzati due servizi Web elencati separatamente. Il servizio Web predittivo deve terminare con "[predictive exp.]".

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. Nel menu a sinistra fare clic su **Machine Learning**.
3. In Nome fare clic sull'area di lavoro e quindi su **Servizi Web**.
4. In Nome fare clic su **Census Model [predictive exp.]**.
5. Nella parte inferiore della pagina fare clic su **Aggiungi endpoint**. Per altre informazioni sull'aggiunta di endpoint, vedere [Creazione di endpoint](machine-learning-create-endpoint.md).

È anche possibile aggiungere gli endpoint di assegnazione dei punteggi usando il codice di esempio disponibile in questo [repository GitHub](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

## Ripetere il training del modello con i nuovi dati usando BES

Per chiamare le API per la ripetizione del training:

1. Creare un'applicazione console C# in Visual Studio. A tale scopo, selezionare Nuovo->Progetto->Windows Desktop->Applicazione console.
2. Copiare il codice C# di esempio dalla pagina della guida dell'API del servizio Web di training per l'esecuzione batch e incollarlo nel file Program.cs, verificando che lo spazio dei nomi rimanga invariato.
3. Il codice di esempio contiene commenti che indicano le parti del codice che è necessario aggiornare.
4. Quando si specifica il percorso di output nel payload della richiesta, l'estensione del file specificata in *RelativeLocation* deve passare da CSV a ILEARNER. Vedere l'esempio seguente.

		Outputs = new Dictionary<string, AzureBlobDataReference>()
		{
			{
				"output1",
				new AzureBlobDataReference()
				{
					ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
					RelativeLocation = "mycontainer/output1results.ilearner"
				}
			},
		},

>[AZURE.NOTE] I nomi dei percorsi di output possono essere diversi da quelli di questa procedura dettagliata a seconda dell'ordine in cui sono stati aggiunti i moduli di output del servizio Web. Dato che questo esperimento di training è stato configurato con due output, i risultati includono le informazioni sul percorso di archiviazione per entrambi.

### Aggiornare le informazioni di archiviazione di Azure

Il codice di esempio BES carica un file da un'unità locale (ad esempio, "C:\\temp\\CensusIpnput.csv") in Archiviazione di Azure, lo elabora e scrive i risultati in Archiviazione di Azure.

Per eseguire questa attività, è necessario recuperare il nome dell'account di archiviazione, la chiave e le informazioni relative al contenitore dal portale di Azure classico e quindi aggiornare i valori corrispondenti nel codice.

È necessario anche assicurarsi che il file di input sia disponibile nella posizione specificata nel codice.

![Output della ripetizione del training.][6]  

Diagramma 4: output della ripetizione del training.

## Valutare i risultati della ripetizione del training
 
Quando si esegue l'applicazione, l'output include l'URL e il token di firma di accesso condiviso necessari per accedere ai risultati della valutazione.

È possibile visualizzare i risultati relativi alle prestazioni del modello sottoposto nuovamente a training combinando *BaseLocation*, *RelativeLocation* e *SasBlobToken* dai risultati di output per *output2* (come illustrato nell'immagine precedente dell'output della ripetizione del training) e incollando l'URL completo nella barra degli indirizzi del browser.

Esaminare i risultati per determinare se le prestazioni del modello appena sottoposto a training sono abbastanza elevate da sostituire quello esistente.

## Aggiornare il modello con training dell'endpoint aggiunto

Per completare il processo di ripetizione del training, è necessario aggiornare il modello con training del nuovo endpoint aggiunto.

* Se il nuovo endpoint è stato aggiunto usando il portale di Azure, è possibile fare clic sul nome del nuovo endpoint nel portale di Azure, quindi sul collegamento **UpdateResource** per ottenere l'URL necessario per aggiornare il modello dell'endpoint.
* Se l'endpoint è stato aggiunto usando il codice di esempio, è inclusa la posizione dell'URL della guida identificato dal valore *HelpLocationURL* nell'output.

Per recuperare l'URL del percorso:

1. Copiare e incollare l'URL nel browser.
2. Fare clic sul collegamento Aggiorna risorsa.
3. Copiare l'URL POST della richiesta PATCH. ad esempio:

		PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Ora è possibile usare il modello con training per aggiornare l'endpoint dei punteggi creato prima.

Il codice di esempio seguente illustra come usare *BaseLocation*, *RelativeLocation*, *SasBlobToken* e L'URL PATCH per aggiornare l'endpoint.

	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}

I valori *apiKey* ed *endpointUrl* per la chiamata possono essere ottenuti dal dashboard dell'endpoint.

Il valore del parametro *Name* in *Resources* deve corrispondere al nome risorsa del modello sottoposto a training e salvato nell'esperimento predittivo. Per ottenere il nome della risorsa:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. Nel menu a sinistra fare clic su **Machine Learning**.
3. In Nome fare clic sull'area di lavoro e quindi su **Servizi Web**.
4. In Nome fare clic su **Census Model [predictive exp.]**.
5. Fare clic sul nuovo endpoint aggiunto.
6. Nel dashboard dell'endpoint fare clic su *Aggiorna risorsa*.
7. Nella pagina di aggiornamento della documentazione dell'API di risorsa del servizio Web è possibile trovare **Nome risorsa** in **Updatable Resources** (Risorse aggiornabili).

Se il token di firma di accesso condiviso scade prima di avere terminato l'aggiornamento dell'endpoint, è necessario eseguire un'operazione GET con l'ID processo per ottenere un nuovo token.

Al termine dell'esecuzione del codice, il nuovo endpoint verrà avviato con il modello di cui è stato ripetuto il training in circa 30 secondi.

##Riepilogo  
Usando le API per la ripetizione del training, è possibile aggiornare il modello con training di un servizio Web predittivo abilitando scenari come:

* Ripetizione periodica del training del modello con nuovi dati.
* Distribuzione di un modello ai clienti per fare in modo che possano ripetere il training del modello con i propri dati.

## Passaggi successivi
[Risoluzione dei problemi relativi alla ripetizione del training di un servizio Web classico di Azure Machine Learning](machine-learning-troubleshooting-retraining-models.md)

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->  
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0824_2016-->