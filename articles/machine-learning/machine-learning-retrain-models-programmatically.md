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
	ms.date="09/28/2016"
	ms.author="raymondl;garye;v-donglo"/>  


#Ripetere il training dei modelli di Machine Learning a livello di codice  

Come parte del processo di messa in funzione dei modelli di apprendimento automatico in Azure Machine Learning, è necessario sottoporre a training e salvare il modello. Lo si userà quindi per creare un servizio Web predicativo. Il servizio Web potrà quindi essere utilizzato in siti Web, dashboard e app per dispositivi mobili.

I modelli creati con Machine Learning in genere non sono statici. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello. Oppure potrebbe essere necessario applicare filtri per ottenere un subset dei dati e ripetere il training del modello.

La ripetizione del training può avvenire di frequente. Con la funzionalità delle API di ripetizione del training a livello di codice, è possibile ripetere il training del modello a livello di codice usando le API di ripetizione del training e aggiornare il servizio Web con un modello di cui è stato appena eseguito il training.

Questo documento descrive il processo di ripetizione del training e illustra come usare le API per la ripetizione del training.

## Motivi per cui ripetere il training: definizione del problema  

Durante il processo di training di Machine Learning viene eseguito il training di un modello usando un set di dati. I modelli creati con Machine Learning in genere non sono statici. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello. In altri scenari potrebbe essere necessario applicare filtri per ottenere un subset dei dati e ripetere il training del modello.

In questi scenari un'API a livello di codice offre un modo pratico per consentire all'utente dell'API di creare un client in grado di ripetere il training del modello una tantum o periodicamente usando i propri dati. Sarà quindi possibile valutare i risultati della ripetizione del training e aggiornare l'API del servizio Web per l'uso del modello appena sottoposto a training.

##Come ripetere il training: il processo end-to-end  

Per iniziare, il processo include i componenti seguenti: un esperimento di training e un esperimento predittivo pubblicato come servizio Web. Per abilitare la ripetizione del training di un modello con training, l'esperimento di training deve essere pubblicato come servizio Web con l'output di un modello con training. In questo modo viene abilitato l'accesso dell'API al modello per la ripetizione del training.

Il processo per la configurazione della ripetizione del training per un servizio Web classico prevede i passaggi seguenti:

![Panoramica del processo di ripetizione del training][1]  

Diagramma 1: panoramica del processo di ripetizione del training per un servizio Web classico

Il processo per la configurazione della ripetizione del training per un nuovo servizio Web prevede i passaggi seguenti:

![Panoramica del processo di ripetizione del training][7]  

Diagramma 2: panoramica del processo di ripetizione del training per un nuovo servizio Web

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

1.	Nella parte inferiore dell'area di disegno dell'esperimento, fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Predictive Web Service** (Servizio Web predittivo). In questo modo, il modello verrà salvato come modello con training e verranno aggiunti i moduli di input e output del servizio Web.
2.	Fare clic su **Run**.
3.	Al termine dell'esecuzione dell'esperimento, fare clic su **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o su **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]).

## Distribuire l'esperimento di training come servizio Web di training

Per ripetere il training del modello con training, è necessario distribuire l'esperimento di training creato come servizio Web di ripetizione del training. Per produrre nuovi modelli con training, questo servizio Web necessita di un modulo *Web service output* connesso al modulo *[Train Model][train-model]*.

1. Per tornare all'esperimento di training, fare clic sull'icona Experiments (Esperimenti) nel riquadro sinistro e quindi sull'esperimento denominato Census Model.
2. Nella casella di ricerca Search Experiment Items (Cerca elementi esperimento) digitare Web service.
3. Trascinare un modulo *Web Service Input* nell'area di disegno dell'esperimento e connetterne l'output al modulo *Clean Missing Data* (Pulisci dati mancanti).
4. Trascinare due moduli *Web service Output* nell'area di disegno dell'esperimento. Connettere l'output del modulo *Train Model* a uno e l'output del modulo *Evaluate Model* all'altro. Con l'output del servizio Web per **Train Model** è possibile ottenere il nuovo modello con training. L'output associato al modulo **Evaluate Model** restituisce l'output del modulo.
5. Fare clic su **Run**.

Sarà quindi necessario distribuire l'esperimento di training come un servizio Web che produce un modello con training e risultati di valutazione del modello. A tale scopo, il set di azioni successivo dipende dall'uso di un servizio Web classico o di un nuovo servizio Web.
  
**Servizio Web classico**

Nella parte inferiore dell'area di disegno dell'esperimento, fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]). Viene visualizzato il **Dashboard** del servizio Web con la chiave API e la pagina della guida dell'API per l'esecuzione batch. È possibile usare solo il metodo di esecuzione batch per la creazione di modelli di training.

**Nuovo servizio Web**

Nella parte inferiore dell'area di disegno dell'esperimento, fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Il portale di Microsoft Azure Machine Learning Web Services (Servizi Web Microsoft Azure Machine Learning) visualizzerà la pagina Deploy Web service (Distribuisci servizio Web). Digitare un nome per il servizio Web e scegliere un piano di pagamento, quindi fare clic su **Deploy** (Distribuisci). È possibile usare solo il metodo di esecuzione batch per la creazione di modelli di training

Al termine dell'esecuzione dell'esperimento, il flusso di lavoro sarà in ogni caso simile al seguente:

![Flusso di lavoro risultante dopo l'esecuzione.][4]  

Diagramma 3: flusso di lavoro risultante dopo l'esecuzione.

## Ripetere il training del modello con i nuovi dati usando BES

Per chiamare le API per la ripetizione del training:

1. Creare un'applicazione console C# in Visual Studio. A tale scopo, selezionare Nuovo->Progetto->Windows Desktop->Applicazione console.
2.	Accedere al portale dei servizi Web di Machine Learning.
3.	Se si usa un servizio Web classico, fare clic su **Classic Web Services** (Servizi Web classici).
	1.	Fare clic sul servizio Web usato.
	2.	Fare clic sull'endpoint predefinito.
	3.	Fare clic su **Consume** (Uso).
	4.	Nella parte inferiore della pagina **Consume** (Utilizzo), nella sezione **Sample Code** (Codice di esempio), fare clic su **Batch**.
	5.	Procedere al passaggio 5 di questa procedura.
4.	Se si usa un nuovo servizio Web, fare clic su **Web Services** (Servizi Web).
	1.	Fare clic sul servizio Web usato.
	2.	Fare clic su **Consume** (Uso).
	3.	Nella parte inferiore della pagina Consume (Utilizzo), nella sezione **Sample Code** (Codice di esempio), fare clic su **Batch**.
5.	Copiare il codice C# di esempio per l'esecuzione batch e incollarlo nel file Program.cs, verificando che lo spazio dei nomi rimanga invariato.

Aggiungere il pacchetto NuGet Microsoft.AspNet.WebApi.Client come specificato nei commenti. Per aggiungere il riferimento a Microsoft.WindowsAzure.Storage.dll, potrebbe essere prima necessario installare la libreria client per i servizi di archiviazione di Microsoft Azure. Per altre informazioni, vedere i [servizi di archiviazione Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### Aggiornare la dichiarazione apikey

Individuare la dichiarazione **apikey**.

	const string apiKey = "abc123"; // Replace this with the API key for the web service

Nella sezione **Basic consumption info** (Informazioni di base sul consumo) della pagina **Consume** (Utilizzo) individuare la chiave primaria e copiarla nella dichiarazione **apikey**.

### Aggiornare le informazioni di archiviazione di Azure

Il codice di esempio BES carica un file da un'unità locale (ad esempio, "C:\\temp\\CensusIpnput.csv") in Archiviazione di Azure, lo elabora e scrive i risultati in Archiviazione di Azure.

Per eseguire questa attività è necessario recuperare il nome dell'account di archiviazione, la chiave e le informazioni sul per l'account di archiviazione dal portale di Azure classico e quindi aggiornare i valori corrispondenti nel codice.

1. Accedere al portale di Azure classico.
1. Nella colonna di spostamento a sinistra fare clic su **Archiviazione**.
1. Nell'elenco degli account di archiviazione selezionarne uno per l'archiviazione del modello per il quale è stato ripetuto il training.
1. Nella parte inferiore della pagina fare clic su **Gestisci chiavi di accesso**.
1. Copiare e salvare la **chiave di accesso primaria**, quindi chiudere la finestra di dialogo.
1. Nella parte superiore della pagina fare clic su **Contenitori**.
1. Selezionare un contenitore esistente oppure crearne uno nuovo e salvare il nome.

Individuare le dichiarazioni *StorageAccountName*, *StorageAccountKey* e *StorageContainerName* dichiarazioni e aggiornare i valori salvati dal portale di Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
È necessario anche assicurarsi che il file di input sia disponibile nella posizione specificata nel codice.

### Specificare il percorso di output

Quando si specifica il percorso di output nel payload della richiesta, l'estensione del file specificata in *RelativeLocation* deve essere indicata come ilearner. Vedere l'esempio seguente.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] I nomi dei percorsi di output possono essere diversi da quelli di questa procedura dettagliata a seconda dell'ordine in cui sono stati aggiunti i moduli di output del servizio Web. Dato che questo esperimento di training è stato configurato con due output, i risultati includono le informazioni sul percorso di archiviazione per entrambi.

![Output della ripetizione del training.][6]  

Diagramma 4: output della ripetizione del training.

## Valutare i risultati della ripetizione del training
 
Quando si esegue l'applicazione, l'output include l'URL e il token di firma di accesso condiviso necessari per accedere ai risultati della valutazione.

È possibile visualizzare i risultati relativi alle prestazioni del modello sottoposto nuovamente a training combinando *BaseLocation*, *RelativeLocation* e *SasBlobToken* dai risultati di output per *output2* (come illustrato nell'immagine precedente dell'output della ripetizione del training) e incollando l'URL completo nella barra degli indirizzi del browser.

Esaminare i risultati per determinare se le prestazioni del modello appena sottoposto a training sono abbastanza elevate da sostituire quello esistente.

Copiare *BaseLocation*, *RelativeLocation* e *SasBlobToken* dai risultati di output. Verranno usati durante il processo di ripetizione del training.

## Passaggi successivi

[Ripetere il training di un servizio Web classico](machine-learning-retrain-a-classic-web-service.md)

[Ripetere il training di un nuovo servizio Web usando i cmdlet di gestione per Machine Learning](machine-learning-retrain-new-web-service-using-powershell.md)

<!-- Retrain a New Web service using the Machine Learning Management REST API -->  


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->  
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0928_2016-->