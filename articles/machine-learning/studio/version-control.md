---
title: Gestione del ciclo di vita delle applicazioni
titleSuffix: ML Studio (classic) - Azure
description: Applicare le procedure consigliate Application Lifecycle Management nella versione classica di Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 10/27/2016
ms.openlocfilehash: 2dd401918edc0b2d08390b1012efbf44fe2be1e9
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839425"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Application Lifecycle Management in Azure Machine Learning Studio (versione classica)
Azure Machine Learning Studio (classico) è uno strumento per lo sviluppo di esperimenti di Machine Learning che vengono operativi nella piattaforma cloud di Azure. Si tratta di una specie di fusione del servizio cloud scalabile e dell'IDE di Visual Studio in un'unica piattaforma. È possibile incorporare le procedure di Application Lifecycle Management Standard (ALM) dal controllo delle versioni di varie risorse all'esecuzione e alla distribuzione automatizzate nella versione classica di Azure Machine Learning Studio. Questo articolo descrive alcune opzioni e approcci.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Controllo della versione degli esperimenti
Esistono due metodi consigliati per controllare la versione degli esperimenti. È possibile fare affidamento sulla cronologia di esecuzione integrata o esportare l'esperimento in un formato JSON in modo da gestirlo esternamente. Ecco vantaggi e svantaggi di ogni approccio.

### <a name="experiment-snapshots-using-run-history"></a>Snapshot dell'esperimento tramite la cronologia di esecuzione
Nel modello di esecuzione della versione classica di Azure Machine Learning Studio esperimento di apprendimento, viene inviato uno snapshot non modificabile dell'esperimento all'utilità di pianificazione dei processi quando si fa clic su **Esegui** nell'editor dell'esperimento. Per visualizzare l'elenco di snapshot, fare clic su **Cronologia esecuzioni** sulla barra dei comandi dell'editor dell'esperimento.

![Pulsante Cronologia di esecuzione](./media/version-control/runhistory.png)

È quindi possibile aprire lo snapshot in modalità bloccata facendo clic sul nome che lo stesso aveva nel momento in cui l'esperimento è stato inviato per l'esecuzione e lo snapshot acquisito. Si noti che è possibile modificare solo il primo elemento nell'elenco, che rappresenta l'esperimento corrente. Si osservi anche che ogni snapshot può presentare diversi stati, tra cui Completato (esecuzione parziale), Non riuscito, Non riuscito (esecuzione parziale) o Bozza.

![Elenco Cronologia di esecuzione](./media/version-control/runhistorylist.png)

Dopo averlo aperto, è possibile salvare l'esperimento dello snapshot come nuovo esperimento e quindi modificarlo. Se lo snapshot dell'esperimento contiene risorse come modelli con training, trasformazioni o set di dati che dispongono di versioni aggiornate, lo snapshot mantiene i riferimenti alla versione originale, ovvero a quella del momento della sua acquisizione. Se si salva lo snapshot bloccato come nuovo esperimento, la versione classica di Azure Machine Learning Studio rileva l'esistenza di una versione più recente di queste risorse e le aggiorna automaticamente nel nuovo esperimento.

Se si elimina l'esperimento, vengono eliminati tutti i relativi snapshot.

### <a name="exportimport-experiment-in-json-format"></a>Esportare/importare un esperimento in formato JSON
Gli snapshot della cronologia di esecuzione conservano una versione non modificabile dell'esperimento nella versione classica di Azure Machine Learning Studio ogni volta che viene inviata per l'esecuzione. È anche possibile salvare una copia locale dell'esperimento e archiviarla nel sistema di controllo di origine preferito, come Team Foundation Server, e successivamente ricreare un esperimento da tale file locale. A questo scopo possono essere utilizzati i commandlet di [Azure Machine Learning PowerShell](https://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph).

Il file JSON è una rappresentazione testuale del grafico dell'esperimento contenente il riferimento agli asset nell'area di lavoro, ad esempio set di dati o modelli sottoposti a training. Non contiene una versione serializzata dell'asset. Se si tenta di importare nuovamente il documento JSON nell'area di lavoro, gli asset referenziati devono già esistere e avere gli stessi ID asset referenziati nell'esperimento. In caso contrario non sarà possibile accedere all'esperimento importato.

## <a name="versioning-trained-model"></a>Controllo della versione del modello sottoposto a training
Un modello sottoposto a training nella versione classica di Azure Machine Learning Studio viene serializzato in un formato noto come file iLearner (`.iLearner`) e archiviato nell'account di archiviazione BLOB di Azure associato all'area di lavoro. Un modo per ottenere una copia del file iLearner è usare l'API di ripetizione del training. [Questo articolo](/azure/machine-learning/studio/retrain-machine-learning-model) spiega il funzionamento dell'API di ripetizione del training. Procedura generale:

1. Impostare l'esperimento di training.
2. Aggiungere la porta di output del servizio Web al modulo Train Model o al modulo che genera il modello sottoposto a training, ad esempio Tune Model Hyperparameter (Regola iperparametri del modello) o Create R Model (Crea modello R).
3. Eseguire l'esperimento di training e quindi distribuirlo come servizio Web di training del modello.
4. Chiamare l'endpoint BES del servizio Web di training e specificare il nome del file iLearner desiderato, nonché la posizione dell'account di archiviazione BLOB in cui verrà archiviato.
5. Raccogliere il file iLearner generato al termine della chiamata BES.

È possibile recuperare il file iLearner anche tramite il cmdlet di PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Questa operazione potrebbe essere più semplice se si desidera ottenere una copia del file iLearner senza la necessità di ripetere il training del modello a livello di codice.

Dopo aver creato il file iLearner contenente il modello con training, è possibile applicare la propria strategia di controllo delle versioni. La strategia può essere molto semplice, come applicare un prefisso o un suffisso quale convenzione di denominazione e lasciare il file iLearner nell'archiviazione BLOB, oppure è possibile copiarlo o importarlo nel sistema di controllo delle versioni.

Il file iLearner salvato può quindi essere usato per il punteggio tramite i servizi Web distribuiti.

## <a name="versioning-web-service"></a>Controllo della versione del servizio Web
È possibile distribuire due tipi di servizi Web da un esperimento di Azure Machine Learning Studio (classico). Il servizio Web classico è strettamente abbinato all'esperimento e all'area di lavoro. Il nuovo servizio Web usa il framework di Azure Resource Manager e non è più abbinato all'esperimento o all'area di lavoro originale.

### <a name="classic-web-service"></a>Servizio Web classico
Per controllare la versione di un servizio Web classico, è possibile usare il costrutto dell'endpoint del servizio Web. Ecco un metodo tipico:

1. Dall'esperimento predittivo, distribuire un nuovo servizio Web classico che contiene un endpoint predefinito.
2. È possibile creare un nuovo endpoint denominato ep2, che espone la versione corrente del modello sottoposto a training o dell'esperimento.
3. È necessario tornare indietro e aggiornare l'esperimento predittivo e il modello sottoposto a training.
4. È possibile ridistribuire l'esperimento predittivo, che aggiornerà l'endpoint predefinito. Ciò non incide sull'ep2.
5. Creare un endpoint aggiuntivo denominato ep3, che espone la nuova versione del modello sottoposto a training e dell'esperimento.
6. Se necessario, tornare al passaggio 3.

Nel tempo potrebbero essere creati molti endpoint nello stesso servizio Web. Ciascun endpoint rappresenta una copia temporizzata dell'esperimento contenente la versione temporizzata del modello sottoposto a training. È quindi possibile usare la logica esterna per determinare l'endpoint da chiamare, che comporta la selezione di una versione del modello sottoposto a training per l'esecuzione della valutazione.

È anche possibile creare numerosi endpoint del servizio Web identici e quindi riempire diverse versioni del file iLearner nell'endpoint per ottenere un effetto simile. [Questo articolo](create-models-and-endpoints-with-powershell.md) illustra in modo più dettagliato come eseguire questa operazione.

### <a name="new-web-service"></a>Nuovo servizio Web
Se si crea un nuovo servizio Web basato su Azure Resource Manager, il costrutto dell'endpoint non è più disponibile. In alternativa, è possibile generare file di definizione del servizio Web (WSD), in formato JSON, dall'esperimento predittivo usando il cmdlet PowerShell [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) oppure usando [*Export-AzMlWebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) PowerShell cmdlet da un servizio Web basato su Gestione risorse distribuito.

Dopo aver esportato il file WSD e aver eseguito il relativo controllo della versione, è anche possibile distribuire il file WSD come nuovo servizio Web in un diverso piano di servizio Web di un'altra area di Azure. Assicurarsi semplicemente di specificare la configurazione appropriata per l'account di archiviazione, nonché l'ID del nuovo piano di servizio Web. Per riempire diversi file iLearner, è possibile modificare il file WSD, aggiornare il riferimento al percorso del modello con training e distribuirlo come nuovo servizio Web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizzare la distribuzione e l'esecuzione dell'esperimento
Un aspetto importante di ALM è la possibilità di automatizzare il processo di distribuzione ed esecuzione dell'applicazione. Nella versione classica di Azure Machine Learning Studio, è possibile eseguire questa operazione usando il [modulo PowerShell](https://aka.ms/amlps). Di seguito è riportato un esempio di passaggi end-to-end rilevanti per un processo di esecuzione/distribuzione automatizzato ALM standard usando il [modulo di PowerShell Azure Machine Learning Studio (classico)](https://aka.ms/amlps). Ogni passaggio è collegato a uno o più commandlet di PowerShell che possono essere usati per portare a termine questa operazione.

1. [Caricare un set di dati](https://github.com/hning86/azuremlps#upload-amldataset).
2. Copiare un esperimento di training nell'area di lavoro da un'[area di lavoro](https://github.com/hning86/azuremlps#copy-amlexperiment) o dalla [raccolta](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), oppure [importare](https://github.com/hning86/azuremlps#import-amlexperimentgraph) un esperimento [esportato](https://github.com/hning86/azuremlps#export-amlexperimentgraph) dal disco locale.
3. [Aggiornare il set di dati](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) nell'esperimento di training.
4. [Eseguire l'esperimento di training](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Promuovere il modello sottoposto a training](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Copiare un esperimento predittivo](https://github.com/hning86/azuremlps#copy-amlexperiment) nell'area di lavoro.
7. [Aggiornare il modello sottoposto a training](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) nell'esperimento predittivo.
8. [Eseguire l'esperimento predittivo](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Distribuire un servizio Web](https://github.com/hning86/azuremlps#new-amlwebservice) dall'esperimento predittivo.
10. Testare l'endpoint [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) o [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) del servizio Web.

## <a name="next-steps"></a>Passaggi successivi
* Scaricare il modulo di [PowerShell Azure Machine Learning Studio (classico)](https://aka.ms/amlps) e avviare l'automazione delle attività ALM.
* Informazioni su come [creare e gestire un numero elevato di modelli ML usando un singolo esperimento](create-models-and-endpoints-with-powershell.md) tramite PowerShell e l'API di ripetizione del training.
* Altre informazioni sulla [distribuzione di servizi Web di Azure Machine Learning](deploy-a-machine-learning-web-service.md).
