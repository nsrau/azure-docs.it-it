---
title: Ripetere il training di un nuovo servizio Web usando i cmdlet di gestione di PowerShell per Machine Learning | Microsoft Docs
description: Informazioni su come ripetere il training di un modello a livello di codice e aggiornare il servizio Web per l'uso del modello appena sottoposto a training in Azure Machine Learning con i cmdlet di gestione di PowerShell.
services: machine-learning
documentationcenter: ''
author: vDonGlover
manager: raymondlaghaeian
editor: ''

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: v-donglo

---
# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Ripetere il training di un nuovo servizio Web usando i cmdlet di gestione di PowerShell per Machine Learning
Quando si ripete il training di un nuovo servizio Web, si aggiorna la definizione del servizio Web predittivo perché faccia riferimento al nuovo modello con training.  

## <a name="prerequisites"></a>Prerequisiti
È necessario impostare un esperimento di training e un esperimento predittivo come illustrato in Ripetere il training dei modelli di Machine Learning a livello di codice. Per informazioni sulla creazione degli esperimenti predittivo e di training, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](machine-learning-retrain-models-programmatically.md).

Questo processo richiede che siano stati installati i cmdlet di Azure Machine Learning. Per l'installazione dei cmdlet di Machine Learning, vedere le informazioni di riferimento sui [cmdlet di Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt767952.aspx) in MSDN.

È necessario aver copiato le informazioni seguenti dall'output di ripetizione del training:

* BaseLocation
* RelativeLocation

I passaggi da eseguire sono:

1. Accedere con l'account di Azure Resource Manager.
2. Ottenere la definizione del servizio Web
3. Esportare la definizione del servizio Web in un file in formato JSON
4. Aggiornare il riferimento al BLOB ilearner nel file JSON.
5. Importare il file JSON in una definizione del servizio Web
6. Aggiornare il servizio Web con la nuova definizione

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Accedere con l'account di Azure Resource Manager
È prima necessario accedere al proprio account Azure dall'interno dell'ambiente di PowerShell tramite il cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition"></a>Ottenere la definizione del servizio Web
Ottenere quindi il servizio Web chiamando il cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . La definizione del servizio Web è una rappresentazione interna del modello con training del servizio Web e non è direttamente modificabile. Assicurarsi di recuperare la definizione del servizio Web per l'esperimento predittivo e non per l'esperimento di training.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Per determinare il nome del gruppo di risorse di un servizio Web esistente, eseguire il cmdlet Get-AzureRmMlWebService senza parametri per visualizzare i servizi Web nella sottoscrizione. Individuare il servizio Web e quindi osservare l'ID del servizio Web. Il nome del gruppo di risorse è il quarto elemento dell'ID, subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

In alternativa, per determinare il nome del gruppo di risorse di un servizio Web esistente, accedere al portale di Microsoft Azure Machine Learning Web Services (Servizi Web di Microsoft Azure Machine Learning). Selezionare il servizio Web. Il nome del gruppo di risorse è il quinto elemento dell'URL del servizio Web, subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Esportare la definizione del servizio Web in un file in formato JSON
Per modificare la definizione per l'uso del modello appena sottoposto a training, è prima necessario usare il cmdlet [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) per esportare la definizione in un file in formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json."></a>Aggiornare il riferimento al BLOB ilearner nel file JSON.
Negli asset individuare il [modello con training] e aggiornare il valore *uri* nel nodo *locationInfo* con l'URI del BLOB ilearner. L'URI viene generato combinando i valori di *BaseLocation* e *RelativeLocation* dell'output della chiamata di ripetizione del training del servizio Esecuzione batch.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>Importare il file JSON in una definizione del servizio Web
È necessario usare il cmdlet [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) per convertire di nuovo il file JSON modificato in una definizione del servizio Web che è possibile usare per aggiornare l'esperimento predicativo.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Aggiornare il servizio Web con la nuova definizione
Usare infine il cmdlet [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) per aggiornare l'esperimento predittivo.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Riepilogo
Usando i cmdlet di gestione PowerShell per Machine Learning, è possibile aggiornare il modello con training di un servizio Web predittivo abilitando scenari come:

* Ripetizione periodica del training del modello con nuovi dati.
* Distribuzione di un modello ai clienti per fare in modo che possano ripetere il training del modello con i propri dati.

<!--HONumber=Oct16_HO2-->


