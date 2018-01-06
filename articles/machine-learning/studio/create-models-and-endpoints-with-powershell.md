---
title: "Creare più modelli da un esperimento | Documentazione Microsoft"
description: "Usare PowerShell per creare più modelli di Machine Learning ed endpoint di servizio Web con lo stesso algoritmo ma con set di dati di training diversi."
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: garye;haining
ms.openlocfilehash: 3386e3bab7a92b080276c4f03f0b006bd5f68e98
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Creare più modelli di Machine Learning ed endpoint di servizio Web da un esperimento usando PowerShell
In uno scenario di apprendimento automatico comune, si potrebbe voler creare più modelli che usano lo stesso flusso di lavoro di training e lo stesso algoritmo, ma con set di dati di training diversi come input. Questo articolo illustra come eseguire questa operazione su larga scala in Azure Machine Learning Studio usando un singolo esperimento.

Si supponga, ad esempio, di essere proprietari di un franchising globale di noleggio di biciclette. Si vuole compilare un modello di regressione per prevedere la domanda di noleggio in base ai dati storici. Si dispone di 1.000 ubicazioni di noleggio in tutto il mondo. È stato raccolto un set di dati per ogni percorso che include funzionalità importanti, ad esempio data, ora, meteo e il traffico che sono specifiche per ogni posizione.

Si potrebbe eseguire il training del modello una sola volta usando una versione unita di tutti i set di dati di tutte le località. Ma dal momento che ogni località ha un ambiente unico, è preferibile eseguire il training del modello di regressione separatamente usando i set di dati delle singole località. In questo modo, ogni modello con training può prendere in considerazione le dimensioni di archivio diverso, volume, geography, popolamento, ambiente traffico bike semplice e più.

Questo potrebbe essere l'approccio migliore, ma creare 1.000 esperimenti di training in Azure Machine Learning che rappresentano le singole località non è molto pratico. Oltre a essere un'attività piuttosto complessa, è inoltre sembra poco efficiente, poiché ogni esperimento avranno gli stessi componenti ad eccezione del set di dati di training.

Fortunatamente, è possibile effettuare questa operazione utilizzando il [ripetizione di training API di Azure Machine Learning](retrain-models-programmatically.md) e automatizzare l'attività con [Azure Machine Learning PowerShell](powershell-module.md).

> [!NOTE]
> Per eseguire l'esempio più velocemente, ridurre il numero di percorsi da 1.000 a 10. ma gli stessi principi e procedure si applicano anche a 1.000 località. L'unica differenza è che per eseguire il training di 1.000 set di dati è consigliabile eseguire in parallelo lo script di PowerShell riportato di seguito. Come eseguire questa operazione non rientra nell'ambito di questo articolo, ma in Internet è possibile trovare esempi di multithreading con PowerShell.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Configurare l'esperimento di training
Usare l'esempio [esperimento di training](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) che si trova nel [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com). Aprire l'esperimento nell'area di lavoro di [Azure Machine Learning Studio](https://studio.azureml.net) .

> [!NOTE]
> Per poter proseguire con l'esempio è consigliabile usare un'area di lavoro standard anziché una gratuita. Creare un endpoint per ogni cliente - per un totale di 10 endpoint - e che richiede un'area di lavoro standard, poiché un'area di lavoro gratuito è limitato a 3 endpoint. Se si dispone solo di un'area di lavoro disponibile, è sufficiente modificare gli script per consentire solo 3 posizioni.
> 
> 

L’esperimento utilizza un modulo **Import Data** per importare il set di dati di training *customer001.csv* da un account di archiviazione di Azure. Si supponga di aver raccolto i set di dati di training da tutti i percorsi di noleggio di bicicletta e li archiviati nello stesso percorso di archiviazione blob con nomi di file compreso *rentalloc001.csv* a *rentalloc10.csv*.

![immagine](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Si noti che al modulo **Train Model** (Modello di training) è stato aggiunto un modulo **Web service output** (Output servizio Web).
Quando questo esperimento viene distribuito come un servizio web, l'endpoint associato che l'output restituisce il modello con training nel formato di un file .ilearner.

Si noti inoltre che configuri un parametro del servizio web per l'URL che il **l'importazione dei dati** modulo Usa. In questo modo è possibile utilizzare il parametro per specificare i set di dati di training singoli per il training del modello per ogni posizione.
Esistono altri modi in cui è stato fatto. È possibile utilizzare una query SQL con un parametro di servizio web per ottenere dati da un database di SQL Azure, oppure utilizzare un **Input del servizio Web** modulo per passare un set di dati al servizio web.

![immagine](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Provare a eseguire l'esperimento di training usando il valore predefinito *rental001.csv* come set di dati di training. Se si visualizza l'output del **Evaluate** modulo (fare clic su di output e selezionare **Visualizza**), è possibile visualizzare un ragionevole delle prestazioni di *AUC* = 0.91. A questo punto, si è pronti per distribuire un servizio web da questo esperimento di training.

## <a name="deploy-the-training-and-scoring-web-services"></a>Distribuire il training e assegnare punteggi ai servizi Web
Per distribuire il servizio Web di training, fare clic sul pulsante **Set Up Web Service** (Configura servizio Web) sotto l'area di disegno dell'esperimento e selezionare **Deploy Web Service** (Distribuisci servizio Web). Denominare il servizio Web "Bike Rental Training".

È necessario distribuire il servizio web di assegnazione dei punteggi.
A tale scopo, fare clic su **di servizio Web** sotto l'area di disegno e selezionare **servizio Web predittivo**. Verrà creato un esperimento di assegnazione dei punteggi.
È necessario apportare alcune modifiche minime per consentire il funzionamento come servizio web. Rimuovere la colonna di etichetta "cnt" dai dati di input e limitare l'output solo l'id di istanza e il corrispondente valore stimato.

Per comodità è possibile aprire l' [esperimento predittivo](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) che è stato preparato nella raccolta.

Per distribuire il servizio Web eseguire l'esperimento predittivo, quindi fare clic sul pulsante **Deploy Web Service** sotto l'area di disegno. Denominare il servizio Web di attribuzione dei punteggi "Bike Rental Scoring".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Creare 10 endpoint di servizio Web identici con PowerShell
Questo servizio Web include un endpoint predefinito, Ma non si è interessati come endpoint predefinito perché non può essere aggiornata. È necessario eseguire è creare 10 altri endpoint, uno per ogni posizione. È possibile farlo con PowerShell.

Innanzitutto, configurare l'ambiente di PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Quindi, eseguire il comando di PowerShell seguente:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Ora è stato creato un 10 endpoint e contengono tutti lo stesso stato sottoposto a training modello sottoposto a training sui *customer001.csv*. È possibile visualizzarli nel portale di Azure.

![immagine](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aggiornare gli endpoint per l'uso di set di dati di training separati tramite PowerShell
Il passaggio successivo consiste nell'aggiornare gli endpoint con i modelli di cui è stato eseguito il training univoco sui dati dei singoli clienti. Ma è necessario innanzitutto generare questi modelli dal **Bike noleggio Training** servizio web. Tornare al servizio Web **Bike Rental Training** . è necessario chiamare il relativo endpoint BES 10 volte con 10 set di dati di training diversi per produrre 10 modelli diversi. A tale scopo, usare il cmdlet **InovkeAmlWebServiceBESEndpoint** di PowerShell.

È inoltre necessario fornire le credenziali per l'account di archiviazione BLOB in `$configContent`, ovvero i campi `AccountName`, `AccountKey` e `RelativeLocation`. Il `AccountName` può essere uno dei nomi di account, come illustrato nel **portale di Azure** (*archiviazione* scheda). Dopo avere fatto clic su un account di archiviazione,è possibile trovare il relativo `AccountKey` scegliendo il pulsante **Gestisci chiavi di accesso** nella parte inferiore e copiando il valore di *Chiave di accesso primaria*. `RelativeLocation` è il percorso relativo della risorsa di archiviazione in cui verrà archiviato un nuovo modello. Ad esempio, il percorso `hai/retrain/bike_rental/` nello script seguente punta a un contenitore denominato `hai` e `/retrain/bike_rental/` sono le sottocartelle. Attualmente, non è possibile creare sottocartelle tramite l'interfaccia utente del portale, ma esistono diversi [Strumenti di esplorazione degli archivi di Azure](../../storage/common/storage-explorers.md) che consentono di eseguire questa operazione. È consigliabile creare un nuovo contenitore nella risorsa di archiviazione per archiviare i nuovi modelli sottoposti a training (file con estensione ilearner) come segue: dalla pagina Archiviazione fare clic sul pulsante **Aggiungi** nella parte inferiore e denominarlo `retrain`. In breve, le modifiche necessarie allo script seguente si riferiscono a `AccountName`, `AccountKey` e `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> L'endpoint BES è l'unica modalità supportata per questa operazione. Non è possibile usare la modalità RRS per la creazione di modelli di training.
> 
> 

Come si può notare, anziché costruire 10 diversi BES processo json i file di configurazione in modo dinamico invece di creare la stringa di configurazione e utilizzarla per la *jobConfigString* parametro il  **InvokeAmlWebServceBESEndpoint** cmdlet, poiché non è effettivamente necessario per mantenere una copia su disco.

Se tutto va bene, dopo poco tempo nell'account di archiviazione di Azure dovrebbero essere disponibili 10 file con estensione ilearner, da *model001.ilearner* a *model010.ilearner*. A questo punto è possibile aggiornare i 10 endpoint di servizio Web di attribuzione dei punteggi con tali modelli, usando il cmdlet **Patch-AmlWebServiceEndpoint** di PowerShell. Ricorda nuovamente è solo possibile applicare una patch gli endpoint non predefinito a livello di codice creato in precedenza.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

L'esecuzione dovrebbe essere abbastanza rapida. Al termine saranno disponibili 10 endpoint di servizio Web predittivi, ognuno dei quali contiene un modello di cui è stato eseguito il training in modo univoco sul set di dati specifico di un punto di noleggio. Tutto questo a partire da un unico esperimento di training. Per eseguire una verifica, è possibile chiamare gli endpoint con il cmdlet **InvokeAmlWebServiceRRSEndpoint**, fornendo gli stessi dati di input. Il risultato dovrebbe essere costituito da stime diverse, dato che per il training di ogni modello è stato usato un set di dati di training diverso.

## <a name="full-powershell-script"></a>Script di PowerShell completo
Di seguito è riportato il listato del codice sorgente completo:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
