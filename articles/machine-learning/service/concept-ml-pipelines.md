---
title: Che cosa sono le pipeline di ML
titleSuffix: Azure Machine Learning
description: Questo articolo illustra i vantaggi delle pipeline di Machine Learning che è possibile creare con Azure Machine Learning SDK per Python. Le pipeline di Machine learning (ML) vengono utilizzate dai data scientist per creare, ottimizzare e gestire i flussi di lavoro di apprendimento automatico.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: c42d2d308398d548df4b1c088819c024ff613564
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832480"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Che cosa sono le pipeline Azure Machine Learning?

Azure Machine Learning pipeline consentono di creare flussi di lavoro nei progetti di machine learning. Questi flussi di lavoro hanno diversi vantaggi: 

+ Semplicità
+ Velocità
+ Ripetibilità
+ Flessibilità
+ Controllo delle versioni e rilevamento
+ Modularità 
+ Garanzia di qualità
+ Controllo dei costi

Questi vantaggi diventano significativi non appena il progetto di Machine Learning si sposta oltre l'esplorazione pura e l'iterazione. Anche le pipeline semplici di un solo passaggio possono essere utili. I progetti di Machine Learning sono spesso in uno stato complesso e possono essere un sollievo per eseguire in modo preciso un singolo flusso di lavoro in un processo semplice.

Informazioni su come [creare la prima pipeline](how-to-create-your-first-pipeline.md).

![Pipeline di Machine Learning in Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Quale tecnologia della pipeline di Azure è consigliabile usare?

Il cloud di Azure fornisce diverse altre pipeline, ognuna con uno scopo diverso. Nella tabella seguente sono elencate le diverse pipeline e le relative operazioni di utilizzo:

| Pipeline | Risultato | Pipe canonica |
| ---- | ---- | ---- |
| Pipeline di Azure Machine Learning | Definisce flussi di lavoro di Machine Learning riutilizzabili che possono essere usati come modello per gli scenari di apprendimento automatico. | Modello di > dati |
| [Pipeline di Data Factory di Azure](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Raggruppa le attività di spostamento, trasformazione e controllo dei dati necessarie per eseguire un'attività.  | Dati > dati |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Integrazione e distribuzione continue dell'applicazione in qualsiasi piattaforma o cloud  | App/servizio > di codice |

## <a name="what-can-azure-ml-pipelines-do"></a>Che cosa possono fare le pipeline di Azure ML?

Una pipeline Azure Machine Learning è un flusso di lavoro eseguibile indipendente di un'attività di Machine Learning completa. Le sottoattività sono incapsulate come una serie di passaggi all'interno della pipeline. Una pipeline Azure Machine Learning può essere semplice come una pipeline che chiama uno script Python, quindi _può_ eseguire qualsiasi operazione. Le pipeline _devono_ concentrarsi sulle attività di Machine Learning, ad esempio:

+ Preparazione dei dati, tra cui importazione, convalida e pulizia, elaborazione degli e trasformazione, normalizzazione e gestione temporanea
+ Configurazione di training, inclusi argomenti parametrizzazione, percorsi FilePath e configurazioni di registrazione/Reporting
+ Formazione e convalida in modo efficiente e ripetitivo, che può includere la specifica di subset di dati specifici, risorse di calcolo hardware diverse, elaborazione distribuita e monitoraggio dello stato di avanzamento
+ Distribuzione, tra cui il controllo delle versioni, il ridimensionamento, il provisioning e il controllo degli accessi 

I passaggi indipendenti consentono a più data scientist di lavorare nella stessa pipeline nello stesso momento senza sovraccaricare le risorse di calcolo. I passaggi distinti semplificano anche l'uso di diversi tipi/dimensioni di calcolo per ogni passaggio.

Dopo che la pipeline è stata progettata, vi è spesso un'ulteriore ottimizzazione di tutto il ciclo di training della pipeline. Quando si esegue nuovamente una pipeline, l'esecuzione passa ai passaggi che devono essere rieseguiti, ad esempio uno script di training aggiornato. I passaggi che non devono essere rieseguiti vengono ignorati. La stessa analisi è valida per gli script non modificati usati per l'esecuzione del passaggio. Questa funzionalità di riutilizzo consente di evitare l'esecuzione di passaggi costosi e con tempi intensivi come l'inserimento e la trasformazione dei dati se i dati sottostanti non sono stati modificati.

Con Azure Machine Learning, è possibile usare diversi Toolkit e Framework, ad esempio PyTorch o TensorFlow, per ogni passaggio della pipeline. Azure coordina le varie [destinazioni di calcolo](concept-azure-machine-learning-architecture.md) usate, quindi i dati intermedi possono essere condivisi con le destinazioni di calcolo downstream.

È possibile [tenere traccia della metrica per gli esperimenti della pipeline](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) direttamente in portale di Azure o nella [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com). Dopo la pubblicazione di una pipeline, è possibile configurare un endpoint REST, che consente di eseguire di nuovo la pipeline da qualsiasi piattaforma o stack.

In breve, tutte le complesse attività del ciclo di vita di machine learning possono essere aiutate con le pipeline. Altre tecnologie di pipeline di Azure hanno propri punti di forza, ad esempio [Azure Data Factory pipeline](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) per lavorare con i dati e [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) per l'integrazione e la distribuzione continue. Tuttavia, se l'obiettivo è l'apprendimento automatico, è probabile che Azure Machine Learning pipeline siano la scelta migliore per le esigenze del flusso di lavoro. 

## <a name="what-are-azure-ml-pipelines"></a>Che cosa sono le pipeline di Azure ML?

Una pipeline di Azure ML esegue un flusso di lavoro logico completo con una sequenza ordinata di passaggi. Ogni passaggio è un'azione di elaborazione discreta. Le pipeline vengono eseguite nel contesto di un [esperimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)di Azure Machine Learning.

Nelle fasi iniziali di un progetto ML, è consigliabile avere un singolo notebook Jupyter o uno script Python che esegua tutte le operazioni di configurazione delle risorse e dell'area di lavoro di Azure, la preparazione dei dati, la configurazione di esecuzione, il training e la convalida. Tuttavia, proprio come le funzioni e le classi diventano rapidamente preferibili a un singolo blocco di codice imperativo, i flussi di lavoro ML diventano rapidamente preferibili a un notebook o a uno script monolitico. 

Grazie alle attività di modularizing ML, le pipeline supportano l'imperativa dell'informatica che un componente deve "solo". La modularità è chiaramente essenziale per proiettare il successo quando si programma in team, ma anche quando si lavora da soli, anche un piccolo progetto ML implica attività separate, ognuna con una buona quantità di complessità. Le attività includono: configurazione dell'area di lavoro e accesso ai dati, preparazione dei dati, definizione e configurazione del modello e distribuzione. Mentre gli output di una o più attività formano gli input per un altro, i dettagli di implementazione esatti di una qualsiasi attività sono, al meglio, distrazioni irrilevanti nell'oggetto successivo. Nella peggiore delle ipotesi, lo stato di calcolo di un'attività può causare un bug in un altro. 

### <a name="analyzing-dependencies"></a>Analisi delle dipendenze

Molti ecosistemi di programmazione hanno strumenti che orchestrano le dipendenze di risorse, librerie o compilazione. In genere, questi strumenti usano i timestamp dei file per calcolare le dipendenze. Quando un file viene modificato, solo l'IT e i relativi dipendenti vengono aggiornati (scaricati, ricompilati o inclusi in un pacchetto). Le pipeline di Azure ML estendono questo concetto in modo significativo. Analogamente agli strumenti di compilazione tradizionali, le pipeline calcolano le dipendenze tra i passaggi ed eseguono solo i ricalcoli necessari. 

L'analisi delle dipendenze nelle pipeline di Azure ML è tuttavia più sofisticata rispetto ai timestamp semplici. Ogni passaggio può essere eseguito in un ambiente hardware e software diverso. La preparazione dei dati potrebbe essere un processo che richiede molto tempo, ma non deve essere eseguito su hardware con GPU potenti, alcuni passaggi potrebbero richiedere software specifico del sistema operativo, potrebbe essere necessario usare il training distribuito e così via. Sebbene la riduzione dei costi per l'ottimizzazione delle risorse possa essere significativa, può risultare eccessiva per manipolare manualmente tutte le diverse varianti delle risorse hardware e software. È ancora più difficile eseguire questa operazione senza mai commettere un errore nei dati trasferiti tra le fasi. 

Le pipeline risolvono questo problema. Azure Machine Learning Orchestra automaticamente tutte le dipendenze tra i passaggi della pipeline. Questa orchestrazione può includere l'attivazione e la disattivazione delle immagini Docker, il collegamento e lo scollegamento delle risorse di calcolo e lo scorrimento dei dati tra i passaggi in modo coerente e automatico.

### <a name="reusing-results"></a>Riutilizzo di risultati

Inoltre, se si sceglie, l'output di un passaggio può essere riutilizzato. Se si specifica riuso come possibilità e non sono presenti dipendenze upstream che attivano il ricalcolo, il servizio pipeline utilizzerà una versione memorizzata nella cache dei risultati del passaggio. Questo riuso può ridurre significativamente i tempi di sviluppo. Se si dispone di un'attività di preparazione dei dati complessa, è probabile che venga rieseguita più spesso di quanto sia strettamente necessario. Le pipeline evitano il problema: se necessario, il passaggio verrà eseguito; in caso contrario, non sarà possibile.

Tutte le analisi delle dipendenze, l'orchestrazione e l'attivazione vengono gestite da Azure Machine Learning quando si crea un'istanza di un oggetto [pipeline](https://docs.microsoft.com/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) , lo si passa a un `Experiment`e si chiama `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Coordinamento dei passaggi necessari

Quando si crea ed esegue un oggetto `Pipeline`, si verificano i passaggi di alto livello seguenti:

+ Per ogni passaggio, il servizio calcola i requisiti per:
    + Risorse di calcolo hardware
    + Risorse del sistema operativo (immagini Docker)
    + Risorse software (dipendenze conda/virtualenv)
    + Input di dati 
+ Il servizio determina le dipendenze tra i passaggi, ottenendo un grafico di esecuzione dinamica
+ Quando viene eseguito ogni nodo del grafico di esecuzione:
    + Il servizio configura l'hardware e l'ambiente software necessari, probabilmente riutilizzando le risorse esistenti.
    + Il passaggio viene eseguito, fornendo le informazioni di registrazione e monitoraggio all'oggetto contenitore `Experiment`
    + Al termine del passaggio, gli output vengono preparati come input per il passaggio successivo e/o scritti nell'archiviazione
    + Le risorse non più necessarie vengono finalizzate e scollegate

![Passaggi della pipeline](media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Creazione di pipeline con Python SDK

In [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), una pipeline è un oggetto Python definito nel modulo di `azureml.pipeline.core`. Un oggetto [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) contiene una sequenza ordinata di uno o più oggetti [PipelineStep](https://docs.microsoft.com/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) . La classe `PipelineStep` è astratta e i passaggi effettivi saranno di sottoclassi come [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)o [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). La classe [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) include una sequenza riutilizzabile di passaggi che possono essere condivisi tra le pipeline. Un `Pipeline` viene eseguito come parte di un `Experiment`.

Una pipeline di Azure ML è associata a un'area di lavoro Azure Machine Learning e un passaggio della pipeline è associato a una destinazione di calcolo disponibile all'interno dell'area di lavoro. Per altre informazioni, vedere [creare e gestire aree di lavoro Azure Machine Learning nell'portale di Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) o informazioni sulle [destinazioni di calcolo in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).

In Azure Machine Learning, una destinazione di calcolo è l'ambiente in cui si verifica una fase di ML. L'ambiente software può essere una macchina virtuale remota, Azure Machine Learning calcolo, Azure Databricks, Azure Batch e così via. L'ambiente hardware può anche variare significativamente, a seconda del supporto della GPU, della memoria, dell'archiviazione e così via. È possibile specificare la destinazione di calcolo per ogni passaggio, che offre un controllo granulare sui costi. È possibile utilizzare risorse più o meno potenti per l'azione specifica, il volume dei dati e le esigenze di prestazioni del progetto. 

## <a name="building-pipelines-with-the-designer"></a>Creazione di pipeline con la finestra di progettazione

Gli sviluppatori che preferiscono un'area di progettazione visiva possono utilizzare la finestra di progettazione Azure Machine Learning per creare pipeline. È possibile accedere a questo strumento dalla selezione della **finestra di progettazione** nella Home page dell'area di lavoro.  La finestra di progettazione consente di trascinare e rilasciare i passaggi nell'area di progettazione. Per un rapido sviluppo, è possibile usare i moduli esistenti attraverso la gamma di attività di Machine Learning; i moduli esistenti coprono tutti gli elementi, dalla trasformazione dei dati alla selezione dell'algoritmo alla formazione alla distribuzione. In alternativa, è possibile creare una pipeline completamente personalizzata combinando i propri passaggi definiti negli script Python.

Quando si progettano le pipeline visivamente, gli input e gli output di un passaggio vengono visualizzati in maniera visibile. È possibile trascinare e rilasciare le connessioni dati, consentendo di comprendere e modificare rapidamente il flusso di dati della pipeline.
 
![Esempio di Azure Machine Learning Designer](./media/concept-ml-pipelines/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Informazioni sul grafico di esecuzione

I passaggi all'interno di una pipeline possono avere dipendenze da altri passaggi. Il servizio pipeline di Azure ML esegue le operazioni di analisi e orchestrazione di queste dipendenze. I nodi nel "grafico di esecuzione" risultante sono passaggi di elaborazione. Ogni passaggio può comportare la creazione o il riutilizzo di una particolare combinazione di hardware e software, il riutilizzo dei risultati memorizzati nella cache e così via. L'orchestrazione e l'ottimizzazione del servizio di questo grafico di esecuzione possono velocizzare significativamente una fase di ML e ridurre i costi. 

Poiché i passaggi vengono eseguiti in modo indipendente, gli oggetti che contengono i dati di input e di output che passano tra i passaggi devono essere definiti esternamente. Questo è il ruolo di [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)e le classi associate. Questi oggetti dati sono associati a un oggetto [Archivio](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) dati che incapsula la configurazione di archiviazione. La classe di base `PipelineStep` viene sempre creata con una stringa `name`, un elenco di `inputs` e un elenco di `outputs`. In genere, dispone anche di un elenco di `arguments` e spesso avrà un elenco di `resource_inputs`. Le sottoclassi avranno in genere anche argomenti aggiuntivi (ad esempio, `PythonScriptStep` richiede il nome del file e il percorso dello script da eseguire). 

Il grafico di esecuzione è aciclici, ma le pipeline possono essere eseguite in base a una pianificazione ricorrente e possono eseguire script Python che possono scrivere informazioni sullo stato nel file system, rendendo possibile la creazione di profili complessi. Se si progetta la pipeline in modo che alcuni passaggi possano essere eseguiti in parallelo o in modo asincrono, Azure Machine Learning gestisce in modo trasparente l'analisi delle dipendenze e il coordinamento di fan-out e fan-in. In genere non è necessario preoccuparsi dei dettagli del grafico di esecuzione, ma è disponibile tramite l'attributo [pipeline. Graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) . 


### <a name="a-simple-python-pipeline"></a>Una semplice pipeline Python

Questo frammento di codice Mostra gli oggetti e le chiamate necessari per creare ed eseguire un `Pipeline`di base:

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

Il frammento inizia con oggetti Azure Machine Learning comuni, un `Workspace`, un `Datastore`, un [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)e un `Experiment`. Quindi, il codice crea gli oggetti da conservare `input_data` e `output_data`. La matrice `steps` include un singolo elemento, un `PythonScriptStep` che utilizzerà gli oggetti dati ed eseguito in `compute_target`. Quindi, il codice crea un'istanza dell'oggetto `Pipeline`, passando l'area di lavoro e la matrice dei passaggi. La chiamata a `experiment.submit(pipeline)` inizia l'esecuzione della pipeline di Azure ML. La chiamata a `wait_for_completion()` si blocca fino al completamento della pipeline. 

## <a name="best-practices-when-using-pipelines"></a>Procedure consigliate per l'uso di pipeline

Come si può notare, la creazione di una pipeline di Azure ML è un po' più complessa rispetto all'avvio di uno script. Per le pipeline sono necessari alcuni oggetti Python da configurare e creare. 

Alcune situazioni che suggeriscono l'uso di una pipeline:

* In un ambiente team: suddividere le attività di Machine Learning in più passaggi indipendenti, in modo che gli sviluppatori possano lavorare e sviluppare i propri programmi in modo indipendente. 

* Quando si esegue la distribuzione in o vicino: è possibile inchiodare la configurazione e usare operazioni pianificate e guidate dagli eventi per restare aggiornati sui dati modificati.

* Nelle fasi iniziali di un progetto ML o in modalità di lavoro da solo: usare le pipeline per automatizzare la compilazione. Se si è iniziato a preoccuparsi della ricreazione della configurazione e dello stato di calcolo prima di implementare una nuova idea, è possibile prendere in considerazione l'uso di una pipeline per automatizzare il flusso di lavoro. 

È facile entusiasmare il riutilizzo dei risultati memorizzati nella cache, il controllo con granularità fine sui costi di calcolo e l'isolamento dei processi, ma le pipeline hanno costi. Alcuni anti-pattern includono:

* Usare le pipeline come unico mezzo per separare i problemi. Funzioni, oggetti e moduli predefiniti di Python ti consentono di evitare confusione nello stato programmatico. Un passaggio della pipeline è molto più costoso di una chiamata di funzione.

* Accoppiamento elevato tra i passaggi della pipeline. Se il refactoring di un passaggio dipendente richiede spesso la modifica degli output di un passaggio precedente, è probabile che i passaggi distinti siano attualmente più costosi rispetto a un vantaggio. Un altro indizio che i passaggi sono eccessivamente associati sono gli argomenti di un passaggio che non sono dati, ma i flag per controllare l'elaborazione. 

* Ottimizzazione prematura delle risorse di calcolo. Ad esempio, ci sono spesso diverse fasi per la preparazione dei dati e spesso è possibile vedere "Oh, qui è possibile usare un `MpiStep` per la programmazione parallela, ma in questo caso è possibile usare una `PythonScriptStep` con una destinazione di calcolo meno potente , "e così via. E forse, a lungo termine, la creazione di passaggi con granularità fine, ad esempio, potrebbe rivelarsi utile, soprattutto se esiste la possibilità di usare risultati memorizzati nella cache anziché ricalcolare sempre. Tuttavia, le pipeline non sono destinate a sostituire il modulo `multiprocessing`. 

Fino a quando un progetto non viene ampliato o si avvicina alla distribuzione, le pipeline devono essere più grossolane anziché granulari. Se si ritiene che il progetto ML includa _fasi_ e una pipeline per fornire un flusso di lavoro completo per spostarsi in una fase specifica, il percorso è quello giusto. 

## <a name="key-advantages"></a>Vantaggi principali

I vantaggi principali dell'uso delle pipeline per i flussi di lavoro di Machine Learning sono:

|Vantaggi principali|Descrizione|
|:-------:|-----------|
|**Esecuzione&nbsp;automatica**|Pianificare i passaggi da eseguire in parallelo o in sequenza in modo affidabile e automatico. La preparazione e la modellazione dei dati possono durare giorni o settimane e le pipeline consentono di concentrarsi su altre attività durante l'esecuzione del processo. |
|**Calcolo eterogeneo**|Usare più pipeline che vengono coordinate in modo affidabile tra le risorse di calcolo eterogenee e scalabili e i percorsi di archiviazione. Usare in modo efficiente le risorse di calcolo disponibili eseguendo singoli passaggi della pipeline su destinazioni di calcolo diverse, ad esempio HDInsight, VM di Data Science GPU e databricks.|
|**Riutilizzo**|Creare modelli di pipeline per scenari specifici, ad esempio la ripetizione del training e l'assegnazione dei punteggi di batch. Attivare pipeline pubblicate da sistemi esterni tramite semplici chiamate REST.|
|**Rilevamento e controllo delle versioni**|Invece di tenere traccia manualmente dei dati e dei percorsi dei risultati durante l'iterazione, usare l'SDK delle pipeline per denominare esplicitamente e controllare la versione di origini dati, input e output. È anche possibile gestire gli script e i dati separatamente per una maggiore produttività.|
| **Modularità** | Separare le aree problematiche e isolare le modifiche consente di evolvere il software a una velocità più elevata con maggiore qualità. | 
|**Collaborazione**|Le pipeline consentono ai data scientist di collaborare in tutte le aree del processo di progettazione di Machine Learning, pur potendo lavorare simultaneamente sui passaggi della pipeline.|

## <a name="next-steps"></a>Passaggi successivi

Le pipeline di Azure ML sono una potente funzionalità che inizia a produrre valore nelle prime fasi di sviluppo. Il valore aumenta man mano che il team e il progetto crescono. Questo articolo ha illustrato come vengono specificate le pipeline con il Azure Machine Learning Python SDK e orchestrato in Azure. Sono stati introdotti alcuni esempi di codice sorgente di base e sono state introdotte alcune delle classi `PipelineStep` disponibili. È necessario avere un'idea di quando usare le pipeline di Azure ML e il modo in cui vengono eseguite da Azure. 


+ Informazioni su come [creare la prima pipeline](how-to-create-your-first-pipeline.md).

+ Informazioni su come [eseguire stime batch su dati di grandi dimensioni](tutorial-pipeline-batch-scoring-classification.md ).

+ Vedere la documentazione di riferimento dell'SDK per i [passaggi](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)relativi a pipeline [Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e pipeline.

+ Provare ad esempio Jupyter Notebooks che illustrano [pipeline Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Informazioni su come [eseguire i notebook per esplorare questo servizio](samples-notebooks.md).
