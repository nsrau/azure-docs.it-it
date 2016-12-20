---
title: ALM in Azure ML| Documentazione Microsoft
description: Applicare le procedure consigliate per la gestione del ciclo di vita dell&quot;applicazione in Azure Machine Learning Studio
keywords: ALM, AML, Azure ML, Gestione del ciclo di vita dell&quot;applicazione, Controllo delle versioni
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: haining
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 614aa45b365abe90fcc2e75fcde50f1c62a95ed8


---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Gestione del ciclo di vita dell'applicazione in Azure Machine Learning Studio
Azure Machine Learning Studio è uno strumento per lo sviluppo di esperimenti di Machine Learning e per renderli operativi nel cloud di Azure. Si tratta di una specie di fusione dei servizi hosting Servizio Web scalabile e dell'IDE di Visual Studio in un'unica piattaforma. È naturale/logico incorporare le procedure ALM standard (gestione del ciclo di vita dell'applicazione), dal controllo delle versioni delle varie risorse all'esecuzione e alla distribuzione automatici. in Azure Machine Learning Studio. Questo articolo descrive alcune opzioni e approcci. 

## <a name="versioning-experiment"></a>Controllo della versione degli esperimenti
Esistono due metodi consigliati per controllare la versione degli esperimenti. È possibile fare affidamento alla cronologia di esecuzione integrata o esportare l'esperimento nel formato JSON e gestirlo esternamente. Ecco vantaggi e svantaggi di ogni approccio.

### <a name="experiment-snapshots-using-run-history"></a>Snapshot dell'esperimento tramite la cronologia di esecuzione
Il modello di esecuzione dell'esperimento Azure Machine Learning consiste nel fatto che, ogni volta che si fa clic sul pulsante Esegui nell'editor dell'esperimento, viene inviato uno snapshot non modificabile dell'esperimento all'utilità di pianificazione del processo. Per visualizzare l'elenco di snapshot, fare clic sul pulsante "CRONOLOGIA DI ESECUZIONE" sulla barra dei comandi dell'editor dell'esperimento.

![Pulsante Cronologia di esecuzione](media/machine-learning-version-control/runhistory.png)

È quindi possibile aprire lo snapshot in modalità bloccata facendo clic sul nome che lo stesso disponeva nel momento in cui l'esperimento è stato inviato per l'esecuzione e lo snapshot acquisito. Si noti che è possibile modificare solo il primo elemento nell'elenco, che rappresenta l'esperimento corrente. Si noti anche che ogni snapshot può essere presentare diversi stati, tra cui Completato (esecuzione parziale), Non riuscito, Non riuscito (esecuzione parziale) o Bozza.

![Elenco CRONOLOGIA DI ESECUZIONE](media/machine-learning-version-control/runhistorylist.png)

Una volta aperto, è possibile salvare l'esperimento dello snapshot come nuovo esperimento e quindi modificarlo. Si noti che se lo snapshot dell'esperimento contiene risorse come modelli sottoposti a training, trasformazione, set di dati e così via, che dispongono quindi di versioni aggiornate, lo snapshot mantiene i riferimenti della versione originale, ovvero del momento dell'acquisizione dello snapshot. Tuttavia, se si salva lo snapshot bloccato come nuovo esperimento, ML Studio rileva l'esistenza di una versione più recente di queste risorse e le aggiorna automaticamente alla versione più recente. 

Si noti anche che se si elimina l'esperimento, vengono eliminati tutti gli snapshot dell'esperimento.

### <a name="exportimport-experiment-in-json-format"></a>Esportare/importare un esperimento in formato JSON
Sebbene la cronologia di esecuzione mantenga una versione non modificabile dell'esperimento in ML Studio ogni volta che l'esperimento viene inviato per l'esecuzione, a volte potrebbe essere necessario salvare una copia locale dell'esperimento e controllarlo nel sistema di controllo di origine preferito, ad esempio Team Foundation Server, per ricreare l'esperimento da tale file locale in un secondo momento. A questo scopo, è possibile usare i commandlet di [Azure ML PowerShell](http://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph).

Si noti anche che il file JSON è una rappresentazione testuale del grafico dell'esperimento contenente il riferimento agli asset nell'area di lavoro, ad esempio set di dati o modelli sottoposti a training. Esso NON contiene la versione serializzata di tali asset. Se si tenta quindi di importare nuovamente il documento JSON nell'area di lavoro, questi asset con riferimento devono già esistere con gli stessi ID dell'asset con riferimento nell'esperimento. In caso contrario non è possibile accedere all'esperimento importato.

## <a name="versioning-trained-model"></a>Controllo della versione del modello sottoposto a training
Un modello sottoposto a training in Azure ML viene serializzato in un formato noto come file .iLearner e archiviato nell'account di archiviazione BLOB di Azure associato all'area di lavoro. Per mantenere una copia del file iLearner, usare l'API di ripetizione del training. Questo [articolo](machine-learning-retrain-models-programmatically.md) illustra in dettaglio il funzionamento dell'API di ripetizione del trainig. I passaggi più importanti sono:

1. Impostare l'esperimento di training.
2. Aggiungere la porta di output del servizio Web al modulo Tdel modello sotto posto a training o al modulo che genera il modello sottoposto a training, ad esempio il modulo Tune Model Hyperparameter (Regola iperparametri del modello) o Create R Model (Crea modello R).
3. Eseguire l'esperimento di training e quindi distribuirlo come servizio Web di training del modello. 
4. Chiamare l'endpoint BES del servizio Web di training e specificare il nome del file .iLearner desiderato, nonché la posizione dell'account di archiviazione BLOB di Azure in cui verrà archiviato.
5. Raccogliere il file .iLearner generato al termine della chiamata BES.

È possibile recuperare il file .iLearner tramite il commandlet di PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Questa operazione potrebbe essere più semplice se si desidera ottenere una copia del file iLearner senza la necessità di ripetere il training del modello a livello di codice.

Dopo aver creato il file .iLearner contenente il modello sottoposto a training, è possibile usare la propria strategia di controllo delle versioni, come la semplice applicazione di prefisso/suffisso come convenzione di denominazione e lasciando il file .iLearner nell'archiviazione BLOB di Azure o la copia/importazione nel sistema di controllo delle versioni in uso.

Il file .iLearner salvato può quindi essere usato per la valutazione tramite i servizi Web distribuiti.

## <a name="versioning-web-service"></a>Controllo della versione del servizio Web
È possibile distribuire due tipi di servizi Web da un esperimento Azure ML. Il servizio Web classico è strettamente abbinato all'esperimento e all'area di lavoro. Il nuovo servizio Web usa il framework di Azure Resource Management e non è più abbinato all'esperimento originale e all'area di lavoro. 

### <a name="classic-web-service"></a>Servizio Web classico
Per controllare la versione di un servizio Web classico, è possibile usare il costrutto dell'endpoint del servizio Web. Ecco un metodo tipico:

1. Dall'esperimento predittivo, distribuire un nuovo servizio Web classico che contiene un endpoint predefinito.
2. È quindi possibile creare un nuovo endpoint denominato ep2, che espone la versione corrente del modello sottoposto a trining o dell'esperimento.
3. Tornare indietro e aggiornare l'esperimento predittivo e il modello sottoposto a training.
4. È quindi possibile ridistribuire l'esperimento predittivo, che aggiornerà l'endpoint predefinito. Ciò non incide sull'ep2.
5. È ora possibile creare un ep3 aggiuntivo dell'endpoint, che espone la nuova versione del modello sottoposto a training ed dell'esperimento. 
6. Se necessario, tornare al passaggio 3.

Nel corso del tempo, è possibile creare numerosi endpoint nello stesso servizio Web, dove ciascuno rappresenta una copia temporizzata dell'esperimento contenente la versione temporizzata del modello sottoposto a training. È quindi possibile usare la logica esterna per determinare l'endpoint da chiamare, che comporta la selezione di una versione del modello sottoposto a training per l'esecuzione della valutazione.

È anche possibile creare numerosi endpoint del servizio Web identici e quindi riempire diverse versioni del file .iLearner nell'endpoint per ottenere un effetto simile. Questo [articolo](machine-learning-create-models-and-endpoints-with-powershell.md) illustra in modo più dettagliato come eseguire questa operazione.

### <a name="new-web-service"></a>Nuovo servizio Web
Se si sta creando un nuovo servizio Web basato su Azure Resource Manager, la costruzione dell'endpoint non è più disponibile. È possibile generare il file WSD (definizione del servizio Web) in formato JSON dall'esperimento predittivo tramite il commandlet di PowerShell [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) o con il commandlet di PowerShell [*Export-AzureRmMlWebservice*](https://msdn.microsoft.com/library/azure/mt767935.aspx) da un servizio Web basato su Resource Manager già distribuito. 

Dopo avere esportato il file WSD e la versione, controllarlo. È anche possibile distribuire il file WSD come nuovo servizio Web in un piano di servizio Web diverso, in un'area di Azure differente. Assicurarsi semplicemente di specificare la configurazione appropriata per l'account di archiviazione, nonché l'ID del nuovo piano di servizio Web. Per riempire diversi file .iLearner, è possibile modificare il file WSD e aggiornare il riferimento al percorso del modello sottoposto a training, da distribuire come nuovo servizio Web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizzare la distribuzione e l'esecuzione dell'esperimento
Un aspetto importante di ALM è la possibilità di automatizzare il processo di distribuzione ed esecuzione dell'applicazione. In Azure ML, è possibile eseguire questa operazione tramite il [modulo di PowerShell](http://aka.ms/amlps). Di seguito è riportato un esempio di procedure complete e rilevanti per un processo automatizzato di esecuzione/sviluppo standard di ALM tramite il [modulo PowerShell di Azure ML Studio](http://aka.ms/amlps). Ogni passaggio è collegato a uno o più commandlets PowerShell, da usare per portare a termine questa operazione.

1. [Caricare un set di dati](https://github.com/hning86/azuremlps#upload-amldataset). 
2. Copiare un esperimento di training nell'area di lavoro da un'[area di lavoro](https://github.com/hning86/azuremlps#copy-amlexperiment) o dalla [raccolta](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), oppure [importare](https://github.com/hning86/azuremlps#import-amlexperimentgraph) un esperimento [esportato](https://github.com/hning86/azuremlps#export-amlexperimentgraph) dal disco locale.
3. [Aggiornare il set di dati](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) nell'esperimento di training.
4. [Eseguire l'esperimento di training](https://github.com/hning86/azuremlps#start-amlexperiment)
5. [Promuovere il modello sottoposto a training](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Copiare un esperimento predittivo](https://github.com/hning86/azuremlps#copy-amlexperiment) nell'area di lavoro.
7. [Aggiornare il modello sottoposto a training](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) nell'esperimento predittivo.
8. [Eseguire l'esperimento predittivo](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Distribuire un servizio Web](https://github.com/hning86/azuremlps#new-amlwebservice) dall'esperimento predittivo.
10. Testare l'endpoint [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) o [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) del servizio Web 

## <a name="next-steps"></a>Passaggi successivi
* Scaricare il modulo [Azure ML Studio PowerShell](http://aka.ms/amlps) e avviare l'automazione delle attività ALM.
* Informazioni su come [creare e gestire un numero elevato di modelli ML usando un singolo esperimento](machine-learning-create-models-and-endpoints-with-powershell.md) tramite PowerShell e l'API di ripetizione del training.
* Altre informazioni sulla [distribuzione di servizi Web di Azure ML](machine-learning-publish-a-machine-learning-web-service.md).




<!--HONumber=Nov16_HO3-->


