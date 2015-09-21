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
	ms.date="09/01/2015"
	ms.author="raymondl;garye"/>


#Ripetere il training dei modelli di Machine Learning a livello di codice  

Come parte del processo di messa in funzione dei modelli di apprendimento automatico in Azure Machine Learning, è necessario sottoporre a training e salvare un modello, quindi usarlo per creare un servizio Web per l'assegnazione di punteggi. Il servizio Web può quindi essere utilizzato in siti Web, dashboard e app per dispositivi mobili.

È spesso necessario ripetere il training del modello creato nel primo passaggio con nuovi dati. In precedenza era possibile eseguire questa operazione solo tramite l'interfaccia utente di Azure ML, ma con l'introduzione della funzionalità API per la ripetizione del training a livello di codice, è ora possibile ripetere il training del modello e aggiornare il servizio Web per l'uso del modello appena sottoposto a training usando le API per la ripetizione del training a livello di codice.

Questo documento descrive il processo precedente e illustra come usare le API per la ripetizione del training.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Motivi per cui ripetere il training: definizione del problema  
Durante il processo di training di ML, viene eseguito il training di un modello usando un set di dati. È necessario ripetere il training dei modelli nei casi in cui sono disponibili nuovi dati, in cui l'utente dell'API dispone dei propri dati per il training del modello o in cui i dati devono essere filtrati e il modello sottoposto a training con il subset di dati e così via.

In questi scenari un'API a livello di codice offre un modo pratico per consentire all'utente dell'API di creare un client in grado di ripetere il training del modello una tantum o periodicamente usando i propri dati. Sarà quindi possibile valutare i risultati della ripetizione del training e aggiornare l'API del servizio Web per l'uso del modello appena sottoposto a training.

##Come ripetere il training: il processo end-to-end  
Innanzitutto, per il processo sono necessari i componenti seguenti: un esperimento di training e un esperimento di assegnazione dei punteggi pubblicati come servizi Web. Per abilitare la ripetizione del training di un modello con training, l'esperimento di training deve essere inoltre pubblicato come servizio Web con l'output di un modello con training. In questo modo viene abilitato l'accesso dell'API al modello per la ripetizione del training. Il processo per la configurazione della ripetizione del training prevede i passaggi seguenti:

![][1]

Diagramma 1: panoramica del processo di ripetizione del training

1. *Creare un esperimento di training* Per questo esempio verrà usato l'esperimento "Esempio 5 (Eseguire il training, il test e la valutazione di una classificazione binaria: Adult Dataset)" dagli esperimenti di esempio di Azure ML. Come illustrato in seguito, l'esempio è stato semplificato rimuovendo alcuni moduli. L'esperimento è stato denominato "Modello di censimento".

 	![][2]

	Dopo aver definito questi componenti, fare clic su Run nella parte inferiore dello schermo per eseguire l'esperimento.  
2. *Creare un esperimento di assegnazione dei punteggi e pubblicarlo come servizio Web*  

	![][3]

	Al termine dell'esecuzione dell'esperimento, fare clic su Create Scoring Experiment. In questo modo verrà creato un esperimento di assegnazione dei punteggi, verrà salvato il modello come modello con training e verranno aggiunti i moduli di input e output del servizio Web, come illustrato di seguito. A questo punto, fare clic su Run.

	Al termine dell'esecuzione dell'esperimento, facendo clic su "Publish Web Service" l'esperimento di assegnazione dei punteggi verrà pubblicato come servizio Web e verrà creato un endpoint predefinito. Il modello con training in questo servizio Web è aggiornabile, come illustrato di seguito. I dettagli dell'endpoint verranno visualizzati sullo schermo.  
3. *Pubblicare l'esperimento di training come servizio Web* Per ripetere il training del modello con training, è necessario pubblicare l'esperimento di training creato nel precedente passaggio 1 come servizio Web. Per produrre nuovi modelli con training, questo servizio Web dovrà disporre di un modulo di output del servizio Web connesso al modulo [modello di training][train-model]. Fare clic sull'icona Experiments nel riquadro sinistro e quindi sull'esperimento denominato Modello di censimento per tornare all'esperimento di training.  

	Aggiungere quindi un modulo di input del servizio Web e due moduli di output del servizio Web al flusso di lavoro. Tramite l'output del servizio Web per il modello di training sarà possibile ottenere il nuovo modello con training. L'output associato al modello di valutazione restituirà l'output del modello di valutazione del modulo.

	Fare clic su Run. Al termine dell'esecuzione dell'esperimento, il flusso di lavoro risultante dovrebbe avere un aspetto simile al seguente:

	![][4]

	Fare clic sul pulsante Publish Web Service e quindi su Yes. L'esperimento di training verrà pubblicato come un servizio Web che produce un modello con training e risultati di valutazione del modello. Verrà visualizzato il dashboard del servizio Web con la chiave API e la pagina della guida dell'API per Esecuzione batch. Si noti che è possibile usare solo il metodo Esecuzione batch per la creazione di modelli di training.  
4. *Aggiungere un nuovo Endpoint* Il servizio Web di assegnazione dei punteggi pubblicato nel precedente passaggio 2 è stato creato con un endpoint predefinito. Gli endpoint predefiniti vengono mantenuti sincronizzati con gli esperimenti di training e di assegnazione dei punteggi di origine, pertanto un modello con training dell'endpoint predefinito non può essere sostituito. Per creare un endpoint aggiornabile, visitare il portale di Azure e fare clic su Aggiungi endpoint (altri dettagli sono disponibili [qui](machine-learning-create-endpoint.md)).

5. *Ripetere il training del modello con nuovi dati e con il servizio Esecuzione batch* Per chiamare le API per la ripetizione del training, creare una nuova applicazione console C# in Visual Studio (Nuovo->Progetto->Windows Desktop->Applicazione console).

	Copiare quindi il codice C# di esempio dalla pagina della guida dell'API del servizio Web di training per l'esecuzione di batch (creato nel precedente passaggio 3) e incollarlo nel file Program.cs, verificando che lo spazio dei nomi rimanga invariato.

	Si noti che il codice di esempio contiene commenti che indicano le parti del codice per cui sono necessari aggiornamenti. Inoltre, quando si specifica la posizione "uscita1" nel Payload richiesto, l'estensione di file di "RelativeLocation" deve essere impostata per un ".ilearner" come in:

	```c#
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
	```
	1. Specificare informazioni sull'archiviazione di Azure. Il codice di esempio per il servizio Esecuzione batch caricherà un file da un'unità locale (ad esempio "C:\\temp\\CensusIpnput.csv") in Archiviazione di Azure, lo elaborerà e scriverà i risultati in Archiviazione di Azure.  

		A tale scopo, è necessario recuperare il nome dell'account di archiviazione, la chiave e le informazioni relative al contenitore dal portale di gestione di Azure e quindi aggiornare qui il codice. È necessario anche assicurarsi che il file di input sia disponibile nella posizione specificata nel codice.

		Questo esperimento di training è stato impostato con due output, in modo che i risultati includano le informazioni sul percorso di archiviazione per entrambi, come indicato di seguito. "output1" è l'output del modello con training e "output2" è l'output del modello di valutazione. Si noti inoltre che l'estensione di file dell'output per il modello con training (Output1) è ".ilearner" e non ".csv".

		![][6]

6. *Valutare i risultati di ripetizione del training* Usando la combinazione di BaseLocation, RelativeLocaiton e SasBlobToken dai risultati di output illustrati in precedenza per "output2", sarà possibile visualizzare i risultati relativi alle prestazioni del modello sottoposto nuovamente a training incollando l'URL completo nella barra degli indirizzi del browser.

	In questo modo sarà possibile sapere se le prestazioni del modello appena sottoposto a training sono abbastanza elevate da sostituire quello esistente.

7. *Aggiornare il modello con training dell'endpoint aggiunto* Per completare il processo, è necessario aggiornare il modello con training dell'endpoint di assegnazione dei punteggi creato nel precedente passaggio 4.

	L'output del servizio Esecuzione batch sopra riportato mostra le informazioni relative al risultato della ripetizione del training per "output1", contenente le informazioni sul percorso del modello sottoposto nuovamente a training. A questo punto è necessario aggiornare l'endpoint di assegnazione dei punteggi creato nel precedente passaggio 4. Segue il codice di esempio:

	```C#
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
	```

	I valori "apiKey" e "endpointUrl" per questa chiamata sono visibili nel dashboard dell'endpoint.

	Se la chiamata ha esito positivo, il nuovo endpoint verrà avviato entro circa 15 secondi usando un modello sottoposto nuovamente a training.

##Riepilogo  
Mediante le API per la ripetizione del training è possibile aggiornare il modello con training di un servizio Web predittivo, rendendo possibili scenari come la ripetizione periodica del training con nuovi dati o la distribuzione di modelli agli utenti allo scopo di consentire la ripetizione del training del modello usando i propri dati.

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=Sept15_HO2-->