---
title: Che cosa sono le pipeline MLWhat are ML Pipelines
titleSuffix: Azure Machine Learning
description: In questo articolo vengono apprende i vantaggi delle pipeline di Machine Learning (ML) che è possibile compilare con Azure Machine Learning SDK per Python. Le pipeline di apprendimento automatico vengono usate dagli scienziati dei dati per creare, ottimizzare e gestire i flussi di lavoro di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: da45c0db027dffc89bd058b70331a4bd6d093b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336967"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Che cosa sono le pipeline di Azure Machine Learning?

Le pipeline di Azure Machine Learning consentono di creare flussi di lavoro nei progetti di Machine Learning.Azure Machine Learning pipelines allow you to create workflows in your machine learning projects. Questi flussi di lavoro presentano una serie di vantaggi: 

+ Semplicità
+ speed
+ Ripetibilità
+ Flessibilità
+ Controllo delle versioni e rilevamento
+ Modularità 
+ Controllo qualità
+ Controllo dei costi

Questi vantaggi diventano significativi non appena il progetto di apprendimento automatico va oltre l'esplorazione pura e in iterazione. Anche semplici pipeline a un solo passaggio possono essere preziose. I progetti di apprendimento automatico sono spesso in uno stato complesso e può essere un sollievo per rendere il risultato preciso di un singolo flusso di lavoro un processo semplice.

Informazioni su come [creare la prima pipeline](how-to-create-your-first-pipeline.md).

![Pipeline di Machine Learning in Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Quale tecnologia di pipeline di Azure è consigliabile usare?

Il cloud di Azure offre diverse altre pipeline, ognuna con uno scopo diverso. Nella tabella seguente sono elencate le diverse pipeline e per cosa vengono utilizzate:

| Scenario | Persona primaria | Offerta di Azure | Offerta OSS | Tubo canonico | Punti di forza | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Orchestrazione del modello (Machine Learning) | Data scientist | Azure Machine Learning Pipelines | Condotte Kubeflow | Modello > dati | Distribuzione, memorizzazione nella cache, code-first, riutilizzo | 
| Orchestrazione dei dati (preparazione dei dati)Data orchestration (Data prep) | Data engineer | [Pipeline di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache Airflow | Dati -> | Movimento fortemente tipizzato. Attività incentrate sui dati. |
| Codice & orchestrazione dell'app (CI/CD)Code & app orchestration (CI/CD) | Sviluppatore di app / Ops | [Pipeline Di DevOps di AzureAzure DevOps Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Codice - Modello -> App/Servizio | Il supporto più aperto e flessibile per le attività, le code di approvazione, le fasi con | 


## <a name="what-can-azure-ml-pipelines-do"></a>Cosa possono fare le pipeline di Azure ML?

Una pipeline di Azure Machine Learning è un flusso di lavoro eseguibile indipendente di un'attività di apprendimento automatico completa. Le sottoattività sono incapsulate come una serie di passaggi all'interno della pipeline. Una pipeline di Azure Machine Learning può essere semplice come una che chiama uno script Python, quindi _può_ fare qualsiasi cosa. Le pipeline devono concentrarsi sulle attività di apprendimento automatico, ad esempio:Pipelines _should_ focus on machine learning tasks such as:

+ Preparazione dei dati, tra cui importazione, convalida e pulizia, munging e trasformazione, normalizzazione e gestione temporanea
+ Configurazione del training, tra cui parametrizzazione di argomenti, percorsi file e configurazioni di registrazione/report
+ Formazione e convalida in modo efficiente e ripetuto. L'efficienza potrebbe derivare dalla specifica di sottoinsiemi di dati specifici, risorse di elaborazione hardware diverse, elaborazione distribuita e monitoraggio dello stato di avanzamento
+ Distribuzione, inclusi controllo delle versioni, ridimensionamento, provisioning e controllo degli accessi 

Le fasi indipendenti consentono a più data scientist di lavorare contemporaneamente sulla stessa pipeline senza tassare eccessivamente le risorse di calcolo. Passaggi separati semplificano inoltre l'uso di tipi/dimensioni di calcolo diversi per ogni passaggio.

Dopo che la pipeline è stata progettata, vi è spesso un'ulteriore ottimizzazione di tutto il ciclo di training della pipeline. Quando si esegue nuovamente una pipeline, l'esecuzione passa ai passaggi che devono essere rieseguiti, ad esempio uno script di training aggiornato. I passaggi che non devono essere rieseguiti vengono ignorati. La stessa analisi si applica agli script non modificati utilizzati per la realizzazione del passaggio. Questa funzionalità di riutilizzo consente di evitare l'esecuzione di passaggi costosi e dispendiosi in termini di tempo, ad esempio l'inserimento e la trasformazione dei dati se i dati sottostanti non sono stati modificati.

Con Azure Machine Learning è possibile usare vari toolkit e framework, ad esempio PyTorch o TensorFlow, per ogni passaggio nella pipeline. Azure coordina le varie destinazioni di [calcolo](concept-azure-machine-learning-architecture.md) usate, in modo che i dati intermedi possano essere condivisi con le destinazioni di calcolo a valle.

È possibile [tenere traccia delle metriche per gli esperimenti della pipeline](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) direttamente nel portale di Azure o nella pagina di destinazione dell'area di lavoro [(anteprima).](https://ml.azure.com) Dopo la pubblicazione di una pipeline, è possibile configurare un endpoint REST, che consente di rieseguire la pipeline da qualsiasi piattaforma o stack.

In breve, tutte le attività complesse del ciclo di vita dell'apprendimento automatico possono essere aiutate con le pipeline. Altre tecnologie di pipeline di Azure hanno i propri punti di forza, ad esempio pipeline di [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) per l'uso di dati e pipeline di [Azure](https://azure.microsoft.com/services/devops/pipelines/) per l'integrazione e la distribuzione continue. Tuttavia, se lo stato attivo è l'apprendimento automatico, è probabile che le pipeline di Azure Machine Learning siano la scelta migliore per le esigenze del flusso di lavoro. 

## <a name="what-are-azure-ml-pipelines"></a>Che cosa sono le pipeline di Azure ML?

Una pipeline di Azure ML esegue un flusso di lavoro logico completo con una sequenza ordinata di passaggi. Ogni passaggio è un'azione di elaborazione discreta. Le pipeline vengono eseguite nel contesto di un esperimento di Azure Machine [Learning.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

Nelle prime fasi di un progetto di ML, è consigliabile disporre di un singolo blocco appunti Jupyter o di uno script Python che esegue tutte le operazioni di configurazione dell'area di lavoro e delle risorse di Azure, la preparazione dei dati, la configurazione di esecuzione, il training e la convalida. Ma proprio come le funzioni e le classi diventano rapidamente preferibili a un singolo blocco imperativo di codice, i flussi di lavoro di ML diventano rapidamente preferibili a un blocco appunti o uno script monolitico. 

Modulare le attività di ML, le pipeline supportano l'imperativo di Analisi computerizzata che un componente deve "fare (solo) una cosa bene." La modularità è chiaramente vitale per il successo del progetto durante la programmazione in team, ma anche quando si lavora da soli, anche un piccolo progetto ML comporta attività separate, ognuna con una buona quantità di complessità. Le attività includono: configurazione dell'area di lavoro e accesso ai dati, preparazione dei dati, definizione e configurazione del modello e distribuzione. Mentre gli output di una o più attività formano gli input per un altro, i dettagli di implementazione esatti di qualsiasi attività sono, nella migliore delle posizioni, distrazioni irrilevanti nella successiva. Nel peggiore dei casi, lo stato di calcolo di un'attività può causare un bug in un'altra. 

### <a name="analyzing-dependencies"></a>Analisi delle dipendenze

Molti ecosistemi di programmazione dispongono di strumenti che orchestrano dipendenze di risorse, librerie o compilazioni. In genere, questi strumenti utilizzano timestamp di file per calcolare le dipendenze. Quando un file viene modificato, vengono aggiornati solo e i relativi dipendenti (scaricati, ricompilati o inclusi nel pacchetto). Le pipeline di Azure ML estendono notevolmente questo concetto. Analogamente agli strumenti di compilazione tradizionali, le pipeline calcolano le dipendenze tra i passaggi ed eseguono solo i ricalcoli necessari. 

L'analisi delle dipendenze nelle pipeline di Azure ML è più sofisticata dei timestamp semplici. Ogni passaggio può essere eseguito in un ambiente hardware e software diverso. La preparazione dei dati potrebbe essere un processo che richiede molto tempo, ma non è necessario eseguire su hardware con GPU potenti, alcuni passaggi potrebbero richiedere software specifico del sistema operativo, potrebbe essere necessario utilizzare la formazione distribuita e così via. Anche se il risparmio sui costi per l'ottimizzazione delle risorse può essere significativo, può essere necessario destreggiarsi manualmente tra tutte le diverse varianti di risorse hardware e software. E 'ancora più difficile fare tutto ciò senza mai fare un errore nei dati si trasferiscono tra i passaggi. 

Le pipeline risolvono questo problema. Azure Machine Learning orchestra automaticamente tutte le dipendenze tra i passaggi della pipeline. Questa orchestrazione può includere la rotazione su e giù di immagini Docker, il collegamento e la disconnessione di risorse di calcolo e lo spostamento dei dati tra i passaggi in modo coerente e automatico.

### <a name="reusing-results"></a>Riutilizzo dei risultati

Inoltre, l'output di un passaggio può, se lo si desidera, essere riutilizzato. Se si specifica il riutilizzo come possibilità e non sono presenti dipendenze a monte che attivano il ricalcolo, il servizio della pipeline utilizzerà una versione memorizzata nella cache dei risultati del passaggio. Tale riutilizzo può ridurre drasticamente i tempi di sviluppo. Se si dispone di un'attività di preparazione dei dati complessa, è probabile che venga rieseguita più spesso di quanto sia strettamente necessario. Le pipeline ti sollevano da questa preoccupazione: se necessario, il passaggio verrà eseguito, in caso contrario, non lo farà.

Tutta questa analisi delle dipendenze, orchestrazione e attivazione vengono gestite da Azure Machine `Experiment`Learning quando `submit()`si crea un'istanza di un [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) oggetto, passarlo a un metodo , e chiamare . 

### <a name="coordinating-the-steps-involved"></a>Coordinamento delle fasi coinvolte

Quando si crea `Pipeline` ed esegue un oggetto, si verificano i seguenti passaggi di alto livello:

+ Per ogni passaggio, il servizio calcola i requisiti per:
    + Risorse di elaborazione hardware
    + Risorse del sistema operativo (immagini Docker)
    + Risorse software (dipendenze Conda / virtualenv)
    + Immissioni dati 
+ Il servizio determina le dipendenze tra i passaggi, generando un grafico di esecuzione dinamica
+ Quando ogni nodo nel grafico di esecuzione viene eseguito:When each node in the execution graph runs:
    + Il servizio configura l'ambiente hardware e software necessario (ad esempio riutilizzando le risorse esistenti)
    + Il passaggio viene eseguito, fornendo `Experiment` informazioni di registrazione e monitoraggio all'oggetto che lo contiene
    + Al termine del passaggio, le sue uscite vengono preparate come input per il passaggio successivo e/o scritte
    + Le risorse non più necessarie vengono finalizzate e scollegate

![Passaggi della pipelinePipeline steps](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Compilazione di pipeline con Python SDK

In [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)una pipeline `azureml.pipeline.core` è un oggetto Python definito nel modulo. Oggetto [Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) oggetto contiene una sequenza ordinata di uno o più [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) oggetti. La `PipelineStep` classe è astratta e i passaggi effettivi saranno di sottoclassi quali [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)o [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). La classe [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) contiene una sequenza riutilizzabile di passaggi che possono essere condivisi tra le pipeline. Un `Pipeline` oggetto viene `Experiment`eseguito come parte di un file .

Una pipeline di Azure ML è associata a un'area di lavoro di Azure Machine Learning e un passaggio della pipeline è associato a una destinazione di calcolo disponibile all'interno di tale area di lavoro. Per altre informazioni, vedere [Creare e gestire aree di lavoro](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) di Azure Machine Learning nel portale di Azure o Quali sono le destinazioni di [calcolo in Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

In Azure Machine Learning, una destinazione di calcolo è l'ambiente in cui si verifica una fase di Machine Learning.In Azure Machine Learning, a compute target is the environment in which an ML phase occurs. L'ambiente software può essere una macchina virtuale remota, un computer di Azure Machine Learning, Azure Databricks, Azure Batch e così via. L'ambiente hardware può anche variare notevolmente, a seconda del supporto GPU, memoria, archiviazione e così via. È possibile specificare la destinazione di calcolo per ogni passaggio, che offre un controllo granulare sui costi. È possibile usare risorse più o meno potenti per le esigenze specifiche di azione, volume di dati e prestazioni del progetto. 

## <a name="building-pipelines-with-the-designer"></a>Compilazione di pipeline con la finestra di progettazioneBuilding pipelines with the designer

Gli sviluppatori che preferiscono un'area di progettazione visiva possono usare la finestra di progettazione di Azure Machine Learning per creare pipeline. È possibile accedere a questo strumento dalla selezione **Designer** nella home page dell'area di lavoro.  La finestra di progettazione consente di trascinare i passaggi nell'area di progettazione. Per uno sviluppo rapido, è possibile utilizzare i moduli esistenti in tutto lo spettro delle attività mL; I moduli esistenti coprono tutto, dalla trasformazione dei dati alla selezione degli algoritmi, dal training alla distribuzione. In alternativa, è possibile creare una pipeline completamente personalizzata combinando i propri passaggi definiti negli script Python.

Quando si progettano visivamente le pipeline, gli input e gli output di un passaggio vengono visualizzati in modo visibilmente. È possibile trascinare e rilasciare le connessioni dati, consentendo di comprendere e modificare rapidamente il flusso di dati della pipeline.
 
![Esempio di finestra di progettazione di Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Informazioni sul grafico di esecuzione

I passaggi all'interno di una pipeline possono avere dipendenze da altri passaggi. Il servizio pipeline di Azure ML esegue il lavoro di analisi e orchestrazione di queste dipendenze. I nodi nel "grafico di esecuzione" risultante sono passaggi di elaborazione. Ogni passaggio può comportare la creazione o il riutilizzo di una particolare combinazione di hardware e software, il riutilizzo dei risultati memorizzati nella cache e così via. L'orchestrazione e l'ottimizzazione di questo grafico di esecuzione del servizio possono accelerare significativamente una fase di ML e ridurre i costi. 

Poiché i passaggi vengono eseguiti in modo indipendente, gli oggetti per contenere i dati di input e output che passano tra i passaggi devono essere definiti esternamente. Questo è il ruolo di [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)e delle classi associate. Questi oggetti dati sono associati a un oggetto [Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) che incapsula la configurazione di archiviazione. La `PipelineStep` classe base viene `name` sempre creata con `inputs`una stringa, `outputs`un elenco di e un elenco di . Di solito, ha anche `arguments` un elenco di e `resource_inputs`spesso avrà un elenco di . Le sottoclassi di solito hanno anche `PythonScriptStep` argomenti aggiuntivi (ad esempio, richiede il nome file e il percorso dello script per l'esecuzione). 

Il grafico di esecuzione è aciclico, ma le pipeline possono essere eseguite in base a una pianificazione ricorrente e possono eseguire script Python in grado di scrivere informazioni sullo stato nel file system, consentendo di creare profili complessi. Se si progetta la pipeline in modo che determinati passaggi possano essere eseguiti in parallelo o in modo asincrono, Azure Machine Learning gestisce in modo trasparente l'analisi delle dipendenze e il coordinamento del fan-out e del fan-in. In genere non è necessario preoccuparsi con i dettagli del grafico di esecuzione, ma è disponibile tramite il [Pipeline.graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) attributo. 


### <a name="a-simple-python-pipeline"></a>Una semplice pipeline Python

Questo frammento mostra gli oggetti e le `Pipeline`chiamate necessarie per creare ed eseguire un file di base:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Il frammento inizia con gli `Workspace`oggetti `Datastore`comuni di Azure Machine `Experiment`Learning, , , , [,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py), , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , , Quindi, il codice crea `input_data` gli `output_data`oggetti da contenere e . La `steps` matrice contiene un `PythonScriptStep` singolo elemento, che utilizzerà `compute_target`gli oggetti dati ed verrà eseguito sul file . Quindi, il codice `Pipeline` crea un'istanza dell'oggetto stesso, passando l'area di lavoro e la matrice di passaggi. La chiamata `experiment.submit(pipeline)` a avvia l'esecuzione della pipeline di Azure ML. La chiamata `wait_for_completion()` a blocchi fino al termine della pipeline. 

Per altre informazioni sulla connessione della pipeline ai dati, vedere gli articoli [Come accedere ai dati](how-to-access-data.md) e Come [registrare i set di dati](how-to-create-register-datasets.md). 

## <a name="best-practices-when-using-pipelines"></a>Procedure consigliate per l'uso delle pipelineBest practices when using pipelines

Come si può vedere, la creazione di una pipeline di Azure ML è un po' più complessa rispetto all'avvio di uno script. Le pipeline richiedono la configurazione e la creazione di alcuni oggetti Python.Pipelines require a few Python objects be configured and created. 

Alcune situazioni che suggeriscono l'uso di una pipeline:Some situations that suggest using a pipeline:

* In un ambiente di team: dividere le attività di ML in più passaggi indipendenti in modo che gli sviluppatori possano lavorare ed evolvere i propri programmi in modo indipendente. 

* Quando si è in fase o vicino alla distribuzione: inchiodare la configurazione e usare le operazioni pianificate e guidate dagli eventi per rimanere sempre al di sopra della modifica dei dati.

* Nelle prime fasi di un progetto di ML o lavorare da soli: usare le pipeline per automatizzare la compilazione. Se hai iniziato a preoccuparti di ricreare la configurazione e lo stato di calcolo prima di implementare una nuova idea, questo è un segnale che potresti prendere in considerazione l'uso di una pipeline per automatizzare il flusso di lavoro. 

È facile diventare entusiasti del riutilizzo dei risultati memorizzati nella cache, del controllo granulare sui costi di elaborazione e dell'isolamento dei processi, ma le pipeline hanno costi. Alcuni anti-modelli includono:

* L'utilizzo delle pipeline come unico mezzo per separare i problemi. Le funzioni, gli oggetti e i moduli integrati di Python vanno molto lontano per evitare confusione dello stato programmatico! Un passaggio della pipeline è molto più costoso di una chiamata di funzione.

* Accoppiamento pesante tra gradini di conduttura. Se il refactoring di un passaggio dipendente richiede spesso la modifica degli output di un passaggio precedente, è probabile che i passaggi separati siano attualmente più di un costo che di un vantaggio. Un altro indizio che i passaggi sono eccessivamente accoppiati è argomenti di un passaggio che non sono dati, ma flag per controllare l'elaborazione. 

* Ottimizzazione prematura delle risorse di calcolo. Per esempio, ci sono spesso diverse fasi per la preparazione dei dati e si `MpiStep` può spesso vedere "Oh, ecco un luogo `PythonScriptStep` dove potrei usare un per la programmazione parallela, ma qui è un posto dove potrei usare un con una destinazione di calcolo meno potente," e così via. E forse, a lungo termine, la creazione di passaggi granulari come questo potrebbe rivelarsi utile, soprattutto se c'è la possibilità di utilizzare i risultati memorizzati nella cache piuttosto che ricalcolare sempre. Ma le pipeline non sono destinate a `multiprocessing` sostituire il modulo nativo di Python. 

Fino a quando un progetto non diventa di grandi dimensioni o si avvicina alla distribuzione, le pipeline devono essere più grossolane piuttosto che con granularità fine. Se si considera il progetto di ML come _un'organizzazione_ di fasi e una pipeline come come se fornissi un flusso di lavoro completo per spostarsi in una fase specifica, ci si è sulla strada giusta. 

## <a name="key-advantages"></a>Vantaggi principali

I vantaggi principali dell'uso delle pipeline per i flussi di lavoro di Machine Learning sono:The key advantages of using pipelines for your machine learning workflows are:

|Vantaggi principali|Descrizione|
|:-------:|-----------|
|**Esecuzione&nbsp;automatica**|Pianificare i passaggi da eseguire in parallelo o in sequenza in modo affidabile e non gestito. La preparazione e la modellazione dei dati possono durare giorni o settimane e le pipeline consentono di concentrarsi su altre attività durante l'esecuzione del processo. |
|**Calcolo eterogenoo**|Usa più pipeline coordinate in modo affidabile tra risorse di elaborazione e posizioni di archiviazione eterogenee e scalabili. Usare in modo efficiente le risorse di calcolo disponibili eseguendo singoli passaggi della pipeline in destinazioni di calcolo diverse, ad esempio HDInsight, macchine virtuali GPU data Science e Databricks.Make efficient use of available compute resources by running individual pipeline steps on different compute targets, such as HDInsight, GPU Data Science VMs, and Databricks.|
|**Riutilizzabilità**|Creare modelli di pipeline per scenari specifici, ad esempio la riqualificazione e l'assegnazione del punteggio in batch. Attivare pipeline pubblicate da sistemi esterni tramite semplici chiamate REST.|
|**Rilevamento e controllo delle versioni**|Invece di tenere traccia manualmente dei dati e dei percorsi dei risultati durante l'iterazione, usare l'SDK delle pipeline per denominare esplicitamente e controllare la versione di origini dati, input e output. È anche possibile gestire gli script e i dati separatamente per una maggiore produttività.|
| **Modularità** | Separare le aree di preoccupazioni e isolare i cambiamenti consente al software di evolversi a una velocità più veloce con una qualità superiore. | 
|**Collaborazione**|Le pipeline consentono agli scienziati dei dati di collaborare in tutte le aree del processo di progettazione dell'apprendimento automatico, pur essendo in grado di lavorare contemporaneamente sui passaggi della pipeline.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>Scelta della sottoclasse PipelineStep appropriataChoosing the proper PipelineStep subclass

Il `PythonScriptStep` è la sottoclasse più `PipelineStep`flessibile dell'astratto . Altre sottoclassi, `EstimatorStep` ad esempio `DataTransferStep` sottoclassi e possono eseguire attività specifiche con meno codice. Ad esempio, `EstimatorStep` è possibile creare un oggetto può essere `Estimator`creato semplicemente passando un nome per il passaggio, un metodo e una destinazione di calcolo. In alternativa, è possibile eseguire l'override di input e output, parametri della pipeline e argomenti. Per altre informazioni, vedere [Eseguire il test aree relè di modelli con Azure Machine Learning usando estimator](how-to-train-ml-models.md). 

Semplifica `DataTransferStep` lo spostamento dei dati tra origini dati e sink. Il codice per eseguire questa operazione manualmente è semplice ma ripetitivo. Al contrario, è `DataTransferStep` possibile creare semplicemente un con un nome, riferimenti a un'origine dati e un sink di dati e una destinazione di calcolo. Il blocco appunti [Azure Machine Learning Pipeline with DataTransferStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) dimostra questa flessibilità.

## <a name="modules"></a>Moduli

Mentre i passaggi della pipeline consentono il riutilizzo dei risultati di un'esecuzione precedente, in molti casi la costruzione del passaggio presuppone che gli script e i file dipendenti necessari debbano essere disponibili localmente. Se un data scientist desidera basarsi sul codice esistente, gli script e le dipendenze spesso devono essere clonati da un repository separato.

I moduli sono simili nell'utilizzo ai passaggi della pipeline, ma forniscono il controllo delle versioni facilitato tramite l'area di lavoro, che consente la collaborazione e la riusabilità su larga scala. I moduli sono progettati per essere riutilizzati in più pipeline e possono evolversi per adattare un calcolo specifico a diversi casi d'uso. Gli utenti possono eseguire le attività seguenti tramite l'area di lavoro, senza utilizzare repository esterni:

* Creare nuovi moduli e pubblicare nuove versioni di moduli esistenti
* Deprecare le versioni esistenti
* Contrassegnare le versioni disabilitate per impedire ai consumer di utilizzare tale versione
* Designare le versioni predefinite
* Recuperare i moduli in base alla versione dall'area di lavoro, per garantire che i team utilizzino lo stesso codiceRetrieve modules by version by the workspace, to ensure teams are using the same code

Vedere il [blocco appunti](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) per esempi di codice su come creare, connettere e usare moduli nelle pipeline di Azure Machine Learning.See the notebook for code examples on how to create, connect, and use modules in Azure Machine Learning pipelines.

## <a name="next-steps"></a>Passaggi successivi

Le pipeline di Azure ML sono una potente funzionalità che inizia a fornire valore nelle prime fasi di sviluppo. Il valore aumenta man mano che il team e il progetto crescono. Questo articolo ha spiegato come le pipeline vengono specificate con Azure Machine Learning Python SDK e orchestrate in Azure.This article has explained how pipelines are specified with the Azure Machine Learning Python SDK and orchestrated on Azure. Hai visto del codice sorgente di base ed `PipelineStep` è stato introdotto ad alcune delle classi disponibili. È necessario avere un'idea di quando usare le pipeline di Azure ML e come vengono eseguite da Azure.You should have a sense of when to use Azure ML pipelines and how Azure runs them. 


+ Informazioni su come [creare la prima pipeline](how-to-create-your-first-pipeline.md).

+ Informazioni su come [eseguire stime batch su dati](tutorial-pipeline-batch-scoring-classification.md )di grandi dimensioni.

+ Vedere la documentazione di riferimento SDK per i passaggi principali della [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e [della pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Provare i blocchi appunti Jupyter di esempio che mostrano le pipeline di [Azure Machine Learning.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) Informazioni su come [eseguire blocchi appunti per esplorare questo servizio.](samples-notebooks.md)
