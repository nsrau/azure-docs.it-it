---
title: Che cosa sono le pipeline Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni sulle pipeline di Machine Learning (ML) che consentono di creare, ottimizzare e gestire i flussi di lavoro di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 08/17/2020
ms.custom: devx-track-python
ms.openlocfilehash: c29ee87ab177357f4289134bb39353c764a0d75b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535300"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Che cosa sono le pipeline Azure Machine Learning?

Questo articolo illustra come Azure Machine Learning pipeline consentono di creare, ottimizzare e gestire i flussi di lavoro di machine learning. Questi flussi di lavoro hanno diversi vantaggi: 

+ Semplicità
+ speed
+ Ripetibilità
+ Flessibilità
+ Controllo delle versioni e rilevamento
+ Modularità 
+ Controllo di qualità
+ Controllo dei costi

Questi vantaggi diventano significativi non appena il progetto di Machine Learning si sposta oltre l'esplorazione pura e l'iterazione. Anche le pipeline semplici di un solo passaggio possono essere utili. I progetti di Machine Learning sono spesso in uno stato complesso e possono essere un sollievo per eseguire in modo preciso un singolo flusso di lavoro in un processo semplice.

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Quale tecnologia della pipeline di Azure è consigliabile usare?

Il cloud di Azure fornisce diverse altre pipeline, ognuna con uno scopo diverso. Nella tabella seguente sono elencate le diverse pipeline e le relative operazioni di utilizzo:

| Scenario | Persona primaria | Offerta di Azure | Offerta OSS | Pipe canonica | Punti di forza | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Orchestrazione del modello (Machine Learning) | Data scientist | Azure Machine Learning Pipelines | Pipeline Kubeflow | Modello di > dati | Distribuzione, memorizzazione nella cache, Code-First, riutilizzo | 
| Orchestrazione dati (preparazione dati) | Data engineer | [Pipeline di Azure Data Factory](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | Dati > dati | Spostamento fortemente tipizzato, attività incentrate sui dati |
| Orchestrazione dell'app & codice (CI/CD) | App Developer/Ops | [Pipeline di Azure DevOps](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Codice + modello-> app/servizio | Supporto delle attività più aperte e flessibili, code di approvazione, fasi con controllo | 

## <a name="what-can-azure-ml-pipelines-do"></a>Che cosa possono fare le pipeline di Azure ML?

Una pipeline Azure Machine Learning è un flusso di lavoro eseguibile indipendente di un'attività di Machine Learning completa. Le sottoattività sono incapsulate come una serie di passaggi all'interno della pipeline. Una pipeline Azure Machine Learning può essere semplice come una pipeline che chiama uno script Python, quindi _può_ eseguire qualsiasi operazione. Le pipeline _devono_ concentrarsi sulle attività di Machine Learning, ad esempio:

+ Preparazione dei dati, tra cui importazione, convalida e pulizia, munging e trasformazione, normalizzazione e gestione temporanea
+ Configurazione del training, tra cui parametrizzazione di argomenti, percorsi file e configurazioni di registrazione/report
+ Formazione e convalida in modo efficiente e ripetuto. L'efficienza potrebbe derivare dalla specifica di subset di dati specifici, risorse di calcolo hardware diverse, elaborazione distribuita e monitoraggio dello stato di avanzamento
+ Distribuzione, inclusi controllo delle versioni, ridimensionamento, provisioning e controllo degli accessi

I passaggi indipendenti consentono a più data scientist di lavorare nella stessa pipeline nello stesso momento senza sovraccaricare le risorse di calcolo. I passaggi distinti semplificano anche l'uso di diversi tipi/dimensioni di calcolo per ogni passaggio.

Dopo che la pipeline è stata progettata, vi è spesso un'ulteriore ottimizzazione di tutto il ciclo di training della pipeline. Quando si esegue nuovamente una pipeline, l'esecuzione passa ai passaggi che devono essere rieseguiti, ad esempio uno script di training aggiornato. I passaggi che non devono essere rieseguiti vengono ignorati. 

Con le pipeline, è possibile scegliere di usare hardware diverso per diverse attività. Azure coordina le varie [destinazioni di calcolo](concept-azure-machine-learning-architecture.md) usate, quindi i dati intermedi passano facilmente alle destinazioni di calcolo downstream.

È possibile [tenere traccia della metrica per gli esperimenti della pipeline](./how-to-track-experiments.md) direttamente in portale di Azure o nella [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com). Dopo la pubblicazione di una pipeline, è possibile configurare un endpoint REST, che consente di eseguire di nuovo la pipeline da qualsiasi piattaforma o stack.

In breve, tutte le complesse attività del ciclo di vita di machine learning possono essere aiutate con le pipeline. Altre tecnologie di pipeline di Azure hanno propri punti di forza. [Azure Data Factory pipeline](../data-factory/concepts-pipelines-activities.md) eccelle nell'utilizzo dei dati e [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) è lo strumento ideale per l'integrazione e la distribuzione continue. Tuttavia, se l'obiettivo è l'apprendimento automatico, è probabile che Azure Machine Learning pipeline siano la scelta migliore per le esigenze del flusso di lavoro. 

### <a name="analyzing-dependencies"></a>Analisi delle dipendenze

Molti ecosistemi di programmazione hanno strumenti che orchestrano le dipendenze di risorse, librerie o compilazione. In genere, questi strumenti usano i timestamp dei file per calcolare le dipendenze. Quando un file viene modificato, solo l'IT e i relativi dipendenti vengono aggiornati (scaricati, ricompilati o inclusi in un pacchetto). Le pipeline di Azure ML estendono questo concetto. Analogamente agli strumenti di compilazione tradizionali, le pipeline calcolano le dipendenze tra i passaggi ed eseguono solo i ricalcoli necessari. 

L'analisi delle dipendenze nelle pipeline di Azure ML è tuttavia più sofisticata rispetto ai timestamp semplici. Ogni passaggio può essere eseguito in un ambiente hardware e software diverso. La preparazione dei dati potrebbe essere un processo che richiede molto tempo, ma non deve essere eseguito su hardware con GPU potenti, alcuni passaggi potrebbero richiedere software specifico del sistema operativo, potrebbe essere necessario usare il training distribuito e così via. 

Azure Machine Learning Orchestra automaticamente tutte le dipendenze tra i passaggi della pipeline. Questa orchestrazione può includere l'attivazione e la disattivazione delle immagini Docker, il collegamento e lo scollegamento delle risorse di calcolo e lo scorrimento dei dati tra i passaggi in modo coerente e automatico.

### <a name="coordinating-the-steps-involved"></a>Coordinamento dei passaggi necessari

Quando si crea ed esegue un `Pipeline` oggetto, si verificano i passaggi di alto livello seguenti:

+ Per ogni passaggio, il servizio calcola i requisiti per:
    + Risorse di calcolo hardware
    + Risorse del sistema operativo (immagini Docker)
    + Risorse software (dipendenze conda/virtualenv)
    + Input di dati 
+ Il servizio determina le dipendenze tra i passaggi, ottenendo un grafico di esecuzione dinamica
+ Quando viene eseguito ogni nodo del grafico di esecuzione:
    + Il servizio configura l'hardware e l'ambiente software necessari, probabilmente riutilizzando le risorse esistenti.
    + Il passaggio viene eseguito, fornendo le informazioni di registrazione e monitoraggio all' `Experiment` oggetto contenitore
    + Al termine del passaggio, gli output vengono preparati come input per il passaggio successivo e/o scritti nell'archiviazione
    + Le risorse non più necessarie vengono finalizzate e scollegate

![Passaggi della pipeline](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Creazione di pipeline con Python SDK

In [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), una pipeline è un oggetto Python definito nel `azureml.pipeline.core` modulo. Un oggetto [pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?preserve-view=true&view=azure-ml-py) contiene una sequenza ordinata di uno o più oggetti [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py) . La `PipelineStep` classe è astratta e i passaggi effettivi saranno di sottoclassi come [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?preserve-view=true&view=azure-ml-py), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?preserve-view=true&view=azure-ml-py)o [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py). La classe [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?preserve-view=true&view=azure-ml-py) include una sequenza riutilizzabile di passaggi che possono essere condivisi tra le pipeline. Un oggetto `Pipeline` viene eseguito come parte di un oggetto `Experiment` .

Una pipeline di Azure ML è associata a un'area di lavoro Azure Machine Learning e un passaggio della pipeline è associato a una destinazione di calcolo disponibile all'interno dell'area di lavoro. Per altre informazioni, vedere [creare e gestire aree di lavoro Azure Machine Learning nell'portale di Azure](./how-to-manage-workspace.md) o informazioni sulle [destinazioni di calcolo in Azure Machine Learning](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>Una semplice pipeline Python

Questo frammento di codice Mostra gli oggetti e le chiamate necessari per creare ed eseguire `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Il frammento inizia con gli oggetti Azure Machine Learning comuni, un oggetto, un oggetto `Workspace` `Datastore` , un [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py)e un oggetto `Experiment` . Quindi, il codice crea gli oggetti da conservare `input_data` e `output_data` . La matrice `steps` include un singolo elemento, un oggetto che utilizzerà `PythonScriptStep` gli oggetti dati e viene eseguito su `compute_target` . Quindi, il codice crea un'istanza dell' `Pipeline` oggetto stesso, passando l'area di lavoro e la matrice Steps. La chiamata a `experiment.submit(pipeline)` Avvia l'esecuzione della pipeline di Azure ml. La chiamata a `wait_for_completion()` viene bloccata fino al termine della pipeline. 

Per altre informazioni sulla connessione della pipeline ai dati, vedere gli articoli [accesso ai dati in Azure Machine Learning](concept-data.md) e [trasferimento dei dati in e tra i passaggi della pipeline di ml (Python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="building-pipelines-with-the-designer"></a>Creazione di pipeline con la finestra di progettazione

Gli sviluppatori che preferiscono un'area di progettazione visiva possono utilizzare la finestra di progettazione Azure Machine Learning per creare pipeline. È possibile accedere a questo strumento dalla selezione della **finestra di progettazione** nella Home page dell'area di lavoro.  La finestra di progettazione consente di trascinare e rilasciare i passaggi nell'area di progettazione. 

Quando si progettano le pipeline visivamente, gli input e gli output di un passaggio vengono visualizzati in maniera visibile. È possibile trascinare e rilasciare le connessioni dati, consentendo di comprendere e modificare rapidamente il flusso di dati della pipeline.

![Esempio di finestra di progettazione di Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Vantaggi principali

I vantaggi principali dell'uso delle pipeline per i flussi di lavoro di Machine Learning sono:

|Vantaggi principali|Descrizione|
|:-------:|-----------|
|**Esecuzione&nbsp;automatica**|Pianificare i passaggi da eseguire in parallelo o in sequenza in modo affidabile e automatico. La preparazione e la modellazione dei dati possono durare giorni o settimane e le pipeline consentono di concentrarsi su altre attività durante l'esecuzione del processo. |
|**Calcolo eterogeneo**|Usare più pipeline che vengono coordinate in modo affidabile tra le risorse di calcolo eterogenee e scalabili e i percorsi di archiviazione. Usare in modo efficiente le risorse di calcolo disponibili eseguendo singoli passaggi della pipeline su destinazioni di calcolo diverse, ad esempio HDInsight, VM di Data Science GPU e databricks.|
|**Riusabilità**|Creare modelli di pipeline per scenari specifici, ad esempio la ripetizione del training e l'assegnazione dei punteggi di batch. Attivare pipeline pubblicate da sistemi esterni tramite semplici chiamate REST.|
|**Rilevamento e controllo delle versioni**|Invece di tenere traccia manualmente dei dati e dei percorsi dei risultati durante l'iterazione, usare l'SDK delle pipeline per denominare esplicitamente e controllare la versione di origini dati, input e output. È anche possibile gestire gli script e i dati separatamente per una maggiore produttività.|
| **Modularità** | Separare le aree problematiche e isolare le modifiche consente di evolvere il software a una velocità più elevata con maggiore qualità. | 
|**Collaborazione**|Le pipeline consentono ai data scientist di collaborare in tutte le aree del processo di progettazione di Machine Learning, pur potendo lavorare simultaneamente sui passaggi della pipeline.|

## <a name="next-steps"></a>Passaggi successivi

Le pipeline di Azure ML sono una potente funzionalità che inizia a produrre valore nelle prime fasi di sviluppo. Il valore aumenta man mano che il team e il progetto crescono. Questo articolo ha illustrato come vengono specificate le pipeline con il Azure Machine Learning Python SDK e orchestrato in Azure. È stato illustrato un codice sorgente semplice ed è stato introdotto un numero ridotto di `PipelineStep` classi disponibili. È necessario avere un'idea di quando usare le pipeline di Azure ML e il modo in cui vengono eseguite da Azure. 


+ Informazioni su come [creare la prima pipeline](how-to-create-your-first-pipeline.md).

+ Informazioni su come [eseguire stime batch su dati di grandi dimensioni](tutorial-pipeline-batch-scoring-classification.md ).

+ Vedere la documentazione di riferimento dell'SDK per i [passaggi](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py)relativi a pipeline [Core](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) e pipeline.

+ Provare ad esempio Jupyter Notebooks che illustrano [pipeline Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Informazioni su come [eseguire i notebook per esplorare questo servizio](samples-notebooks.md).