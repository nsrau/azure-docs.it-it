---
title: Ripetere il training di un servizio Web predittivo esistente | Documentazione Microsoft
description: Informazioni su come ripetere il training di un modello e aggiornare il servizio Web per usare il modello appena sottoposto a training in Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: v-donglo
ms.openlocfilehash: 4f117e7099ffd0a8f85aa96f0fd075d4bcbeb6b4
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="retrain-an-existing-predictive-web-service"></a>Ripetere il training di un servizio Web predittivo esistente
Questo documento descrive il processo di ripetizione del training nello scenario seguente:

* Si dispone di un esperimento di training e di un esperimento predittivo distribuito come un servizio Web operativo.
* Si hanno a disposizione nuovi dati che si vuole vengano usati dal servizio Web predittivo per assegnarne il punteggio.

> [!NOTE] 
> Per distribuire un nuovo servizio Web è necessario disporre delle autorizzazioni sufficienti nella sottoscrizione a cui si sta distribuendo il servizio Web. Per altre informazioni, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md). 

Seguire questa procedura usando il servizio Web e gli esperimenti esistenti:

1. Aggiornare il modello.
   1. Modificare l'esperimento di training per consentire l'uso di input e output del servizio Web.
   2. Distribuire l'esperimento di training come servizio Web di ripetizione del training.
   3. Usare il servizio Esecuzione batch dell'esperimento di training per ripetere il training del modello.
2. Usare i cmdlet di PowerShell per Azure Machine Learning per aggiornare l'esperimento predittivo.
   1. Accedere con l'account di Azure Resource Manager.
   2. Ottenere la definizione del servizio Web.
   3. Esportare la definizione del servizio Web in un file in formato JSON.
   4. Aggiornare il riferimento al BLOB ilearner nel file JSON.
   5. Importare il file JSON in una definizione del servizio Web.
   6. Aggiornare il servizio Web con la nuova definizione.

## <a name="deploy-the-training-experiment"></a>Distribuire l'esperimento di training
Per distribuire l'esperimento di training come servizio Web di ripetizione del training, è necessario aggiungere input e output del servizio Web al modello. Se si collega un modulo di *output del servizio Web* al modulo *[Train Model][train-model]* dell'esperimento, si consente all'esperimento di training di generare un nuovo modello sottoposto a training che sarà possibile usare in un esperimento predittivo. Se si dispone di un modulo di *valutazione del modello*, è possibile collegare l'output del servizio Web anche per ottenere i risultati della valutazione come output.

Per aggiornare l'esperimento di training:

1. Connettere un modulo di *input del servizio Web* al proprio input di dati (ad esempio, un modulo di *pulizia dei dati mancanti*). In genere, infatti, si vuole che i dati di input vengano elaborati allo stesso modo dei dati di training originali.
2. Connettere un modulo di *output del servizio Web* all'output del modulo *Training modello*.
3. Se si ha un modulo di *valutazione del modello* e si vuole eseguire l'output dei risultati della valutazione, connettere un modulo di *output del servizio Web* all'output del proprio modulo di *valutazione del modello*.

Eseguire l'esperimento.

Sarà quindi necessario distribuire l'esperimento di training come un servizio Web che produce un modello sottoposto a training e risultati di valutazione del modello.  

Nella parte inferiore dell'area di disegno dell'esperimento fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Il portale dei servizi Web Microsoft Azure Machine Learning visualizzerà la pagina **Deploy Web Service** (Distribuisci servizio Web). Digitare un nome per il servizio Web, scegliere un piano di pagamento e quindi fare clic su **Deploy**(Distribuisci). È possibile usare solo il metodo Esecuzione batch per la creazione di modelli di training.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Ripetere il training del modello con nuovi dati usando il servizio Esecuzione batch
In questo esempio si userà il linguaggio C# per creare l'applicazione di ripetizione del training. Per eseguire questa attività, tuttavia, è possibile usare anche il codice di esempio Python o R.

Per chiamare le API per la ripetizione del training:

1. Creare un'applicazione console C# in Visual Studio. A tale scopo, selezionare **Nuovo** > **Progetto** > **Visual C#** > **Desktop classico di Windows** > **Applicazione console (.NET Framework)**.
2. Accedere al portale dei servizi Web Machine Learning.
3. Fare clic sul servizio Web usato.
4. Fare clic su **Consume**(Uso).
5. Nella sezione **Sample Code** (Codice di esempio) nella parte inferiore della pagina **Consume** (Uso) fare clic su **Batch**.
6. Copiare il codice C# di esempio per l'esecuzione batch e incollarlo nel file Program.cs, verificando che lo spazio dei nomi rimanga invariato.

Aggiungere il pacchetto NuGet Microsoft.AspNet.WebApi.Client come specificato nei commenti. Per aggiungere il riferimento a Microsoft.WindowsAzure.Storage.dll, è possibile che sia prima necessario installare la [libreria client per i servizi di archiviazione di Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

La schermata seguente illustra la pagina **Consume** (Utilizzo) del portale di servizi Web Azure Machine Learning.

![Pagina Consume (Utilizzo)][1]

### <a name="update-the-apikey-declaration"></a>Aggiornare la dichiarazione apikey
Individuare la dichiarazione **apikey**:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Nella sezione **Basic consumption info** (Informazioni di base sul consumo) della pagina **Consume** (Uso) individuare la chiave primaria e copiarla nella dichiarazione **apikey**.

### <a name="update-the-azure-storage-information"></a>Aggiornare le informazioni di archiviazione di Azure
Il codice di esempio BES carica un file da un'unità locale (ad esempio, "C:\temp\CensusIpnput.csv") in Archiviazione di Azure, lo elabora e scrive i risultati in Archiviazione di Azure.  

Dopo aver eseguito l'esperimento, il flusso di lavoro risultante dovrebbe essere simile al seguente:

![Flusso di lavoro risultante dopo l'esecuzione][4]

1. Accedere al portale di Azure.
2. Nella colonna a sinistra fare clic su **Altri servizi** e usare **Account di archiviazione** per cercare e selezionare un account.
3. Nell'elenco degli account di archiviazione selezionarne uno per l'archiviazione del modello per il quale è stato ripetuto il training.
4. Nella colonna di spostamento a sinistra fare clic su **Chiavi di accesso**.
5. Copiare e salvare la **chiave di accesso primaria**.
6. Nella colonna di spostamento a sinistra fare clic su **Contenitori**.
7. Selezionare un contenitore esistente oppure crearne uno nuovo e salvare il nome.

Individuare le dichiarazioni *StorageAccountName*, *StorageAccountKey* e *StorageContainerName* e aggiornare i valori salvati dal portale.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

È necessario anche assicurarsi che il file di input sia disponibile nella posizione specificata nel codice.

### <a name="specify-the-output-location"></a>Specificare il percorso di output
Quando si specifica il percorso di output nel payload della richiesta, l'estensione del file specificata in *RelativeLocation* deve essere indicata come `ilearner`. Vedere l'esempio seguente:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Di seguito è riportato un output di ripetizione del training di esempio:

![Output della ripetizione del training.][6]

## <a name="evaluate-the-retraining-results"></a>Valutare i risultati della ripetizione del training
Quando si esegue l'applicazione, l'output include l'URL e il token di firma di accesso condiviso necessari per accedere ai risultati della valutazione.

È possibile visualizzare i risultati sulle prestazioni del modello sottoposto nuovamente a training combinando *BaseLocation*, *RelativeLocation* e *SasBlobToken* dai risultati di output per *output2* (come illustrato nell'immagine precedente dell'output di ripetizione del training) e incollando l'URL completo nella barra degli indirizzi del browser.  

Esaminare i risultati per determinare se le prestazioni del modello appena sottoposto a training sono abbastanza elevate da sostituire quello esistente.

Copiare *BaseLocation*, *RelativeLocation* e *SasBlobToken* dai risultati di output.

## <a name="retrain-the-web-service"></a>Ripetere il training del servizio Web
Quando si ripete il training di un nuovo servizio Web, si aggiorna la definizione del servizio Web predittivo perché faccia riferimento al nuovo modello sottoposto a training. La definizione del servizio Web è una rappresentazione interna del modello sottoposto a training del servizio Web e non è direttamente modificabile. Assicurarsi di recuperare la definizione del servizio Web per l'esperimento predittivo e non per l'esperimento di training.

## <a name="sign-in-to-azure-resource-manager"></a>Accedere a Azure Resource Manager
È prima necessario accedere al proprio account Azure dall'interno dell'ambiente di PowerShell tramite il cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx).

## <a name="get-the-web-service-definition-object"></a>Ottenere l'oggetto definizione del servizio Web
È quindi necessario ottenere l'oggetto definizione del servizio Web chiamando il cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx).

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Per determinare il nome del gruppo di risorse di un servizio Web esistente, eseguire il cmdlet Get-AzureRmMlWebService senza parametri per visualizzare i servizi Web nella sottoscrizione. Individuare il servizio Web e quindi osservare l'ID del servizio Web. Il nome del gruppo di risorse è il quarto elemento dell'ID, subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

In alternativa, per determinare il nome del gruppo di risorse di un servizio Web esistente, accedere al portale dei servizi Web Azure Machine Learning. Selezionare il servizio Web. Il nome del gruppo di risorse è il quinto elemento dell'URL del servizio Web, subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Esportare l'oggetto definizione del servizio Web in un file in formato JSON
Per modificare la definizione in modo da poter usare il modello appena sottoposto a training, è prima necessario usare il cmdlet [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) per esportare la definizione in un file in formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Aggiornare il riferimento al BLOB ilearner
Negli asset individuare il [modello con training] e aggiornare il valore *uri* nel nodo *locationInfo* con l'URI del BLOB ilearner. L'URI viene generato combinando i valori di *BaseLocation* e *RelativeLocation* dell'output della chiamata di ripetizione del training del servizio Esecuzione batch.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importare il file JSON in un oggetto definizione del servizio Web
È necessario usare il cmdlet [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) per convertire di nuovo il file JSON modificato in un oggetto definizione del servizio Web che è possibile usare per aggiornare l'esperimento predicativo.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Aggiornare il servizio Web
Usare infine il cmdlet [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) per aggiornare l'esperimento predittivo.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
