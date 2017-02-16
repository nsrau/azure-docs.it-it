---
title: Ripetere il training dei modelli di Machine Learning a livello di codice | Documentazione Microsoft
description: Informazioni su come ripetere il training di un modello a livello di codice e aggiornare il servizio Web per l&quot;uso del modello appena sottoposto a training in Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: raymondl;garye;v-donglo
translationtype: Human Translation
ms.sourcegitcommit: cd32cb7d2247676795f345448960d547eafba084
ms.openlocfilehash: 9290258998f6756dce55a764e020fb3278c2f8a6


---
# <a name="retrain-machine-learning-models-programmatically"></a>Ripetere il training dei modelli di Machine Learning a livello di codice
Questa procedura dettagliata descrive come ripetere il training di un servizio Web di Azure Machine Learning a livello di codice usando C# e il servizio Esecuzione batch di Machine Learning.

Dopo aver ripetuto il training del modello, attenersi alle procedure dettagliate seguenti che descrivono come aggiornare il modello nel servizio Web predittivo:

* Se è stato distribuito un servizio Web classico nel portale dei servizi Web di Machine Learning, vedere [Ripetere il training di un servizio Web classico](machine-learning-retrain-a-classic-web-service.md). 
* Se è stato distribuito un nuovo servizio Web, vedere [Ripetere il training di un nuovo servizio Web usando i cmdlet di gestione di Machine Learning](machine-learning-retrain-new-web-service-using-powershell.md).

Per una panoramica del processo di ripetizione del training, vedere [Ripetere il training di un modello di Machine Learning](machine-learning-retrain-machine-learning-model.md).

Se si vuole iniziare con il servizio Web esistente basato sul nuovo Azure Resource Manager, vedere [Retrain an existing Predictive web service](machine-learning-retrain-existing-resource-manager-based-web-service.md) (Ripetere il training di un servizio Web predittivo esistente).

## <a name="create-a-training-experiment"></a>Creare un esperimento di training
Per questo esempio, si userà "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" che fa parte degli esempi di Microsoft Azure Machine Learning. 

Per creare l'esperimento:

1. Accedere a Microsoft Azure Machine Learning Studio. 
2. Nell'angolo in basso a destra del dashboard fare clic su **New**(Nuovo).
3. Tra gli esempi di Microsoft selezionare l'esempio 5.
4. Per rinominare l'esperimento, nella parte superiore dell'area di disegno dell'esperimento selezionare il nome dell'esperimento "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset".
5. Digitare Census Model.
6. Fare clic su **Run**(Esegui) nella parte inferiore dell'area di disegno dell'esperimento.
7. Fare clic su **Set Up Web service** (Configura servizio Web) e selezionare **Retraining Web service** (Servizio Web di ripetizione del training). 

Di seguito viene illustrato l'esperimento iniziale.
   
   ![Esperimento iniziale.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Creare un esperimento predittivo e pubblicarlo come servizio Web
Ora viene creato un esperimento predicativo.

1. Nella parte inferiore dell'area di disegno dell'esperimento fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Predictive Web Service** (Servizio Web predittivo). In questo modo, il modello verrà salvato come modello con training e verranno aggiunti i moduli di input e output del servizio Web. 
2. Fare clic su **Run**. 
3. Al termine dell'esecuzione dell'esperimento, fare clic su **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]).

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Distribuire l'esperimento di training come servizio Web di training
Per ripetere il training del modello con training, è necessario distribuire l'esperimento di training creato come servizio Web di ripetizione del training. Per produrre nuovi modelli con training, questo servizio Web necessita di un modulo *Web Service Output* connesso al modulo *[Train Model][train-model]*.

1. Per tornare all'esperimento di training, fare clic sull'icona Experiments (Esperimenti) nel riquadro sinistro e quindi sull'esperimento denominato Census Model.  
2. Nella casella di ricerca Search Experiment Items (Cerca elementi esperimento) digitare Web service. 
3. Trascinare un modulo *Web Service Input* (Input servizio Web) nell'area di disegno dell'esperimento e connetterne l'output al modulo *Clean Missing Data* (Pulisci dati mancanti).  In questo modo, i dati di ripetizione del training vengono elaborati allo stesso modo dei dati di training originali.
4. Trascinare due moduli *Web service Output* (Output servizio Web) nell'area di disegno dell'esperimento. Connettere l'output del modulo *Train Model* (Modello di training) a un modulo e l'output del modulo *Evaluate Model* (Modello di valutazione) all'altro. Con l'output del servizio Web per **Train Model** (Modello di training) è possibile ottenere il nuovo modello con training. L'output collegato al modulo **Evaluate Model** (Modello di valutazione) restituisce l'output del modulo, che corrisponde ai risultati sulle prestazioni.
5. Fare clic su **Run**. 

Sarà quindi necessario distribuire l'esperimento di training come un servizio Web che produce un modello sottoposto a training e risultati di valutazione del modello. A tale scopo, il set di azioni successivo dipende dall'uso di un servizio Web classico o di un nuovo servizio Web.  

**Servizio Web classico**

Nella parte inferiore dell'area di disegno dell'esperimento fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]). Viene visualizzato il **Dashboard** del servizio Web con la chiave API e la pagina della guida dell'API per l'esecuzione batch. È possibile usare solo il metodo di esecuzione batch per la creazione di modelli di training.

**Nuovo servizio Web**

Nella parte inferiore dell'area di disegno dell'esperimento fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Il portale dei servizi Web di Azure Machine Learning visualizzerà la pagina Deploy Web service (Distribuisci servizio Web). Digitare un nome per il servizio Web e scegliere un piano di pagamento, quindi fare clic su **Distribuisci**. È possibile usare solo il metodo di esecuzione batch per la creazione di modelli di training

Al termine dell'esecuzione dell'esperimento, il flusso di lavoro sarà in ogni caso simile al seguente:

![Flusso di lavoro risultante dopo l'esecuzione.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Ripetere il training del modello con i nuovi dati usando BES
In questo esempio viene usato C# per creare l'applicazione di ripetizione del training. Per eseguire questa attività, è anche possibile usare il codice di esempio Python o R.

Per chiamare le API per la ripetizione del training:

1. Creare un'applicazione console C# in Visual Studio. A tale scopo, selezionare Nuovo->Progetto->Windows Desktop->Applicazione console.
2. Accedere al portale dei servizi Web di Machine Learning.
3. Se si usa un servizio Web classico, fare clic su **Classic Web Services**(Servizi Web classici).
   1. Fare clic sul servizio Web in uso.
   2. Fare clic sull'endpoint predefinito.
   3. Fare clic su **Consume**(Uso).
   4. Nella sezione **Sample Code** (Codice di esempio) nella parte inferiore della pagina **Consume** (Uso) fare clic su **Batch**.
   5. Procedere al passaggio 5 di questa procedura.
4. Se si usa un nuovo servizio Web, fare clic su **Servizi Web**.
   1. Fare clic sul servizio Web in uso.
   2. Fare clic su **Consume**(Uso).
   3. Nella sezione **Sample Code** (Codice di esempio) nella parte inferiore della pagina Consume (Uso) fare clic su **Batch**.
5. Copiare il codice C# di esempio per l'esecuzione batch e incollarlo nel file Program.cs, verificando che lo spazio dei nomi rimanga invariato.

Aggiungere il pacchetto NuGet Microsoft.AspNet.WebApi.Client come specificato nei commenti. Per aggiungere il riferimento a Microsoft.WindowsAzure.Storage.dll, potrebbe essere prima necessario installare la libreria client per i servizi di archiviazione di Microsoft Azure. Per altre informazioni, vedere i [servizi di archiviazione Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Aggiornare la dichiarazione apikey
Individuare la dichiarazione **apikey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Nella sezione **Basic consumption info** (Informazioni di base sul consumo) della pagina **Consume** (Uso) individuare la chiave primaria e copiarla nella dichiarazione **apikey**.

### <a name="update-the-azure-storage-information"></a>Aggiornare le informazioni di archiviazione di Azure
Il codice di esempio BES carica un file da un'unità locale (ad esempio, "C:\temp\CensusIpnput.csv") in Archiviazione di Azure, lo elabora e scrive i risultati in Archiviazione di Azure.  

Per eseguire questa attività è necessario recuperare il nome dell'account di archiviazione, la chiave e le informazioni sul per l'account di archiviazione dal portale di Azure classico e quindi aggiornare i valori corrispondenti nel codice. 

1. Accedere al portale di Azure classico.
2. Nella colonna di spostamento a sinistra fare clic su **Archiviazione**.
3. Nell'elenco degli account di archiviazione selezionarne uno per l'archiviazione del modello per il quale è stato ripetuto il training.
4. Nella parte inferiore della pagina fare clic su **Gestisci chiavi di accesso**.
5. Copiare e salvare la **chiave di accesso primaria** , quindi chiudere la finestra di dialogo. 
6. Nella parte superiore della pagina fare clic su **Contenitori**.
7. Selezionare un contenitore esistente oppure crearne uno nuovo e salvare il nome.

Individuare le dichiarazioni *StorageAccountName*, *StorageAccountKey* e *StorageContainerName* e aggiornare i valori salvati dal portale di Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

È necessario anche assicurarsi che il file di input sia disponibile nella posizione specificata nel codice. 

### <a name="specify-the-output-location"></a>Specificare il percorso di output
Quando si specifica il percorso di output nel payload della richiesta, l'estensione del file specificata in *RelativeLocation* deve essere indicata come ilearner. 

Vedere l'esempio seguente:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> I nomi dei percorsi di output possono essere diversi da quelli di questa procedura dettagliata a seconda dell'ordine in cui sono stati aggiunti i moduli di output del servizio Web. Dato che questo esperimento di training è stato configurato con due output, i risultati includono le informazioni sul percorso di archiviazione per entrambi.  
> 
> 

![Output della ripetizione del training.][6]

Diagramma 4: output della ripetizione del training.

## <a name="evaluate-the-retraining-results"></a>Valutare i risultati della ripetizione del training
Quando si esegue l'applicazione, l'output include l'URL e il token di firma di accesso condiviso necessari per accedere ai risultati della valutazione.

È possibile visualizzare i risultati sulle prestazioni del modello sottoposto nuovamente a training combinando *BaseLocation*, *RelativeLocation* e *SasBlobToken* dai risultati di output per *output2* (come mostrato nell'immagine precedente dell'output della ripetizione del training) e incollando l'URL completo nella barra degli indirizzi del browser.  

Esaminare i risultati per determinare se le prestazioni del modello appena sottoposto a training sono abbastanza elevate da sostituire quello esistente.

Copiare *BaseLocation*, *RelativeLocation* e *SasBlobToken* dai risultati di output. Verranno usati durante il processo di ripetizione del training.

## <a name="next-steps"></a>Passaggi successivi
Se è stato distribuito il servizio Web predittivo facendo clic su **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]), vedere [Ripetere il training di un servizio Web classico](machine-learning-retrain-a-classic-web-service.md).

Se è stato distribuito un nuovo servizio Web facendo clic su **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]), vedere [Ripetere il training di un nuovo servizio Web usando i cmdlet di gestione di Machine Learning](machine-learning-retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/



<!--HONumber=Jan17_HO2-->


