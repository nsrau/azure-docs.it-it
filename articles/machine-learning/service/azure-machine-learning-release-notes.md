---
title: Novità della versione
titleSuffix: Azure Machine Learning service
description: Informazioni sugli aggiornamenti più recenti al servizio Azure Machine Learning e agli SDK di Azure Machine Learning per Python e per la preparazione dei dati.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: 8a67a20beff306cd23b08e1d651ab5dc4c779fd2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742763"
---
# <a name="azure-machine-learning-service-release-notes"></a>Note sulla versione del servizio Azure Machine Learning

Questo articolo fornisce informazioni sulle versioni del servizio Azure Machine Learning. 

## <a name="2018-12-20"></a>20-12-2018: 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK per Python v1.0.6

+ **Documentazione di riferimento per l'SDK**: https://aka.ms/aml-sdk

+ **Correzioni di bug**: questa versione contiene principalmente correzioni di bug minori.

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **Documentazione di riferimento per l'SDK**: https://aka.ms/data-prep-sdk

+ **Nuove funzionalità**
  + La funzione `to_bool` consente ora la conversione di valori non corrispondenti in valori di errore. Questo è il nuovo comportamento predefinito per `to_bool` e `set_column_types` in caso di mancata corrispondenza. Il comportamento predefinito precedente consisteva nel convertire i valori non corrispondenti in valori False.
  + Quando si chiama `to_pandas_dataframe`, è disponibile una nuova opzione per interpretare i valori Null o mancanti nelle colonne numeriche come NaN.
  + È stata aggiunta la possibilità controllare il tipo restituito di alcune espressioni per garantire la coerenza dei tipi e la risposta anticipata agli errori.
  + È ora possibile chiamare `parse_json` per analizzare i valori in una colonna come oggetti JSON ed espanderli in più colonne.

+ **Correzioni di bug**
  + È stato corretto un bug che causava l'arresto anomalo di `set_column_types` in Python 3.5.2.
  + È stato corretto un bug che causava l'arresto anomalo del sistema durante la connessione all'archivio dati con un'immagine di Azure Machine Learning.

+ **Aggiornamenti**
  * [Notebook di esempio](https://aka.ms/aml-data-prep-notebooks) per esercitazioni introduttive, case study e guide pratiche.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Disponibilità generale

Il servizio Azure Machine Learning è ora disponibile a livello generale.

### <a name="azure-machine-learning-compute"></a>Ambiente di calcolo di Azure Machine Learning
Con questa versione, Microsoft presenta una nuova esperienza di calcolo gestita tramite l'[ambiente di calcolo di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Questa destinazione di calcolo sostituisce l'ambiente di calcolo di Azure Batch per intelligenza artificiale per Azure Machine Learning. 

La nuova destinazione di calcolo presenta queste caratteristiche:
+ Viene usata per il training dei modelli e l'inferenza dei batch
+ È un ambiente di calcolo a uno o più nodi
+ Esegue la gestione del cluster e la pianificazione dei processi per l'utente
+ Viene ridimensionata automaticamente per impostazione predefinita
+ Include il supporto per le risorse di CPU e GPU 
+ Consente l'uso di macchine virtuali con priorità bassa per un risparmio sui costi

L'ambiente di calcolo di Azure Machine Learning può essere creato in Python, tramite il portale di Azure o l'interfaccia della riga di comando. Deve essere creato nella zona dell'area di lavoro e non può essere collegato ad altre aree di lavoro. Questa destinazione di calcolo usa un contenitore Docker per l'esecuzione e crea pacchetti delle dipendenze per replicare lo stesso ambiente in tutti i nodi.

> [!Warning]
> È consigliabile creare una nuova area di lavoro per usare l'ambiente di calcolo di Azure Machine Learning. C'è una possibilità remota che gli utenti che tentano di creare l'ambiente di calcolo di Azure Machine Learning da un'area di lavoro esistente visualizzino un errore. L'ambiente di calcolo esistente nell'area di lavoro deve continuare a lavorare senza problemi.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK per Python v1.0.2
+ **Modifiche di rilievo**
  + Con questa versione, si rimuoverà il supporto per la creazione di una macchina virtuale da Azure Machine Learning. È comunque possibile associare una macchina virtuale del cloud esistente o un server remoto in locale. 
  + Verrà rimosso anche il supporto per BatchAI, che ora dovrà essere supportato tramite l'ambiente di calcolo di Azure Machine Learning.

+ **Nuovo**
  + Per le pipeline di apprendimento automatico:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Aggiornato**
  + Per le pipeline di apprendimento automatico:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) ora accetta runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) ora copia da e verso un'origine dati SQL
    + Pianificare la funzionalità in SDK per creare e aggiornare le pianificazioni per l'esecuzione di pipeline pubblicate

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **Modifiche di rilievo** 
  * `SummaryFunction.N` è stato rinominato `SummaryFunction.Count`.
  
+ **Correzioni di bug**
  * Usare il più recente token di esecuzione di Azure Machine Learning durante la lettura e la scrittura negli archivi dati in esecuzioni remote. Precedentemente, se il Token di esecuzione di Azure Machine Learning viene aggiornato in Python, il runtime di preparazione dei dati non viene aggiornato con il token di esecuzione di Azure Machine Learning aggiornato.
  * Altri messaggi di errore più chiari
  * Non si verifica più l'arresto anomalo di to_spark_dataframe() quando Spark usa la serializzazione `Kryo`
  * L'ispettore del conteggio del valore può ora mostrare più di 1000 valori univoci
  * La Divisione casuale non commette più errori se il flusso di dati originale non ha un nome  

+ **Altre informazioni**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentazione e notebook
+ Pipeline di Machine Learning
  + Notebook nuovi e aggiornati per iniziare con le pipeline, la definizione dell'ambito di batch e gli esempi di trasferimenti di stile: https://aka.ms/aml-pipeline-notebooks
  + Informazioni su come [creare la prima pipeline](how-to-create-your-first-pipeline.md)
  + Informazioni su come [eseguire previsioni in batch utilizzando le pipeline](how-to-run-batch-predictions.md)
+ Destinazione di calcolo di Azure Machine Learning
  + I [notebook di esempio] (https://aka.ms/aml-notebooks) sono ora aggiornati per l'uso del nuovo ambiente di calcolo gestito.
  + [Informazioni su questo ambiente di calcolo](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portale di Azure: nuove funzionalità
+ Creare e gestire i tipi di [Ambiente di calcolo di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) nel portale.
+ Monitorare l'utilizzo della quota e [richiedere una quota](how-to-manage-quotas.md) per l'ambiente di calcolo di Azure Machine Learning.
+ Visualizzare lo stato del cluster dell'ambiente di calcolo di Azure Machine Learning in tempo reale.
+ È stato aggiunto il supporto di rete virtuale per la creazione dell'ambiente di calcolo di Azure Machine Learning e del servizio Azure Kubernetes.
+ Eseguire di nuovo le pipeline pubblicate con i parametri esistenti.
+ Nuovi [grafici automatizzati di apprendimento automatico](how-to-track-experiments.md#auto) per i modelli di classificazione (accuratezza, miglioramenti, calibrazione, grafico dell'importanza delle funzionalità con la spiegabilità del modello) e i modelli di regressione (valori residui e grafico di importanza della funzionalità con la spiegabilità del modello). 
+ Le pipeline possono essere visualizzate nel portale di Azure




## <a name="2018-11-20"></a>20-11-2018

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK per Python v0.1.80

+ **Modifiche di rilievo** 
  * Lo spazio dei nomi *azureml.train.widgets* è stato spostato in *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* imposta come deprecate le classi *azureml.core.compute.BatchAICompute* e *azureml.core.compute.DSVMCompute*. La seconda verrà rimossa in versioni successive. La classe AmlCompute ha ora una definizione più semplice, necessita solo dei parametri vm_size e max_nodes ed è in grado di ridimensionare automaticamente il cluster da 0 al valore di max_nodes quando viene inviato un processo. I [notebook di esempio] (https://github.com/Azure/MachineLearningNotebooks/tree/master/training) sono stati aggiornati con queste informazioni e dovrebbero offrire esempi di utilizzo. Ci auguriamo che gli utenti apprezzeranno questa semplificazione e le interessanti funzionalità che verranno aggiunte nelle prossime versioni.

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1 

Per altre informazioni su Data Prep SDK vedere la [documentazione di riferimento](https://aka.ms/data-prep-sdk).
+ **Nuove funzionalità**
   * È stata creata una nuova interfaccia della riga di comando DataPrep per eseguire i pacchetti DataPrep e visualizzare il profilo dati relativo a un set di dati o un flusso di dati
   * L'API SetColumnType è stata riprogettata per migliorarne l'usabilità
   * smart_read_file è stato rinominato in auto_read_file
   * Il profilo dati include ora i valori di asimmetria e curtosi
   * È supportato il campionamento stratificato
   * È possibile leggere da file ZIP che contengono file CSV
   * È possibile suddividere i set di dati riga per riga con la suddivisione casuale, ad esempio in set di test-training
   * È possibile ottenere tutti i tipi di dati delle colonne da un flusso di dati o un profilo dati chiamando `.dtypes`
   * È possibile ottenere il conteggio delle righe da un flusso di dati o un profilo dati chiamando `.row_count`

+ **Correzioni di bug**
   * È stata corretta la conversione da tipo di dati long a double 
   * È stata corretta un'asserzione dopo qualsiasi aggiunta di colonna 
   * È stato corretto un problema di Raggruppamento fuzzy che impediva il rilevamento dei gruppi in alcuni casi
   * È stata corretta la funzione di ordinamento per rispettare l'ordinamento a più colonne
   * Le espressioni and/or sono state corrette in modo che siano simili a come vengono gestite da `pandas`
   * È stata corretta la lettura dal percorso dbfs
   * I messaggi di errore sono stati resi più comprensibili 
   * Ora non si verificano più errori di lettura su una destinazione di calcolo remota tramite il token AML
   * Non si verificano più errori in Data Science Virtual Machine Linux
   * Non si verifica più un arresto anomalo quando predicati di stringa contengono valori non di tipo stringa
   * Gli errori di asserzione in caso di errore del flusso di dati vengono ora gestiti correttamente
   * Le posizioni di archiviazione montate in dbutils sono ora supportate in Azure Databricks

## <a name="2018-11-05"></a>05-11-2018

### <a name="azure-portal"></a>Portale di Azure 
Il portale di Azure per il servizio di Azure Machine Learning include gli aggiornamenti seguenti:
  * Una nuova scheda **Pipeline** per le pipeline pubblicate.
  * Aggiunta del supporto per il collegamento di un cluster HDInsight esistente come destinazione di calcolo.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK per Python v0.1.74

+ **Modifiche di rilievo** 
  * *Workspace.compute_targets, datastores, experiments, images, models e *webservices* sono diventate proprietà anziché metodi. Ad esempio, *Workspace.compute_targets()* dovrà essere sostituito con *Workspace.compute_targets*.
  * *Run.get_context* depreca *Run.get_submitted_run*. Il secondo metodo verrà rimosso nelle versioni successive.
  * La classe *PipelineData* prevede ora un oggetto datastore come parametro anziché datastore_name. Analogamente, *Pipeline* accetta default_datastore anziché default_datastore_name.

+ **Nuove funzionalità**
  * Il [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) di Azure Machine Learning Pipelines usa ora i passaggi MPI.
  * Il widget RunDetails per notebook Jupyter è stato aggiornato in modo da mostrare una visualizzazione della pipeline.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0 
 
+ **Nuove funzionalità**
  * Numero di tipi aggiunti al profilo dati 
  * Sono ora disponibili istogramma e conteggio dei valori
  * Altri percentili nel profilo dati
  * Il valore Median è disponibile in Summarize
  * Python 3.7 è ora supportato
  * Quando si salva un flusso di dati che contiene archivi dati in un pacchetto DataPrep, le informazioni degli archivi dati verranno rese persistenti come parte del pacchetto DataPrep
  * La scrittura in un archivio dati è ora supportata 
        
+ **Bug risolto**
  * Gli overflow di interi senza segno a 64 bit vengono ora gestiti correttamente in Linux
  * L'etichetta di testo errata per i file di testo normale è stata corretta in smart_read
  * Il tipo di colonna a stringa viene mostrata ora nella visualizzazione metrica
  * Il numero di tipi è stato corretto in modo da mostrare ValueKinds mappato a un unico FieldType invece che ai singoli
  * Write_to_csv non genera più errore quando il percorso viene specificato come stringa
  * Quando si usa Sostituisci, lasciare il campo "trova" vuoto non genera più un errore 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK per Python v0.1.68

+ **Nuove funzionalità**
  * Supporto di più tenant per la creazione di una nuova area di lavoro.

+ **Bug risolti**
  * Non è più necessario bloccare la versione della libreria pynacl durante la distribuzione di un servizio Web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning Data Prep SDK v0.3.0

+ **Nuove funzionalità**
  * È stato aggiunto il metodo transform_partition_with_file(script_path), che consente agli utenti di passare il percorso di un file Python da eseguire

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK per Python v0.1.65
La [versione 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) include nuove funzionalità, ulteriore documentazione, correzioni di bug e nuovi [blocchi appunti di esempio](https://aka.ms/aml-notebooks).

Per informazioni sui bug noti e le soluzioni alternative, vedere l'[elenco dei problemi noti](resource-known-issues.md).

+ **Modifiche di rilievo**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images e Workspace.web_services restituiscono un dizionario, mentre in precedenza restituivano un elenco. Vedere la documentazione dell'API [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py).

  * Le funzionalità automatizzate di Machine Learning hanno rimosso l'errore quadratico medio normalizzato dalle metriche principali.

+ **HyperDrive**
  * Diverse correzioni di bug di HyperDrive per Bayes, miglioramenti alle prestazioni delle chiamate di recupero delle metriche. 
  * Aggiornamento di Tensorflow da 1.9 a 1.10 
  * Ottimizzazione dell'immagine Docker per l'avvio a freddo. 
  * I processi ora segnalano lo stato corretto anche se terminano con un codice di errore diverso da 0. 
  * Convalida dell'attributo RunConfig in SDK. 
  * L'oggetto esecuzione HyperDrive supporta l'annullamento in modo analogo a una normale esecuzione, senza bisogno di passare alcun parametro. 
  * Miglioramenti ai widget per gestire lo stato dei valori degli elenchi a discesa per le esecuzioni distribuite e le esecuzioni di HyperDrive. 
  * Supporto di TensorBoard e di altri file di log risolto per il server dei parametri. 
  * Supporto di Intel(R) MPI sul lato servizi. 
  * Correzione di bug dell'ottimizzazione dei parametri per l'esecuzione distribuita durante la convalida in Batch per intelligenza artificiale. 
  * Gestione contesto ora identifica l'istanza primaria. 

+ **Esperienza del portale di Azure**
  * log_table() e log_row() sono supportati nei dettagli dell'esecuzione. 
  * Creazione automatica di grafici per tabelle e righe con 1, 2 o 3 colonne numeriche e una colonna di categoria facoltativa.

+ **Funzionalità automatizzate di Machine Learning**
  * Miglioramenti alla gestione degli errori e alla relativa documentazione 
  * Correzione di problemi di prestazioni relativi al recupero delle proprietà di esecuzione. 
  * Correzione di un problema di esecuzione continua. 
  * Correzione di problemi di iterazione di insiemi.
  * Correzione di un bug di blocco del training in MAC OS.
  * Sottocampionamento della curva di precisione-recupero/ROC media della macro in uno scenario di convalida personalizzata.
  * Rimozione della logica di indicizzazione eccedente.
  * Rimozione del filtro dall'API get_output.

+ **Pipeline**
  * È stato aggiunto un metodo Pipeline.publish() per pubblicare una pipeline direttamente, senza un'esecuzione preventiva.   
  * È stato aggiunto un metodo PipelineRun.get_pipeline_runs() per recuperare le esecuzioni di pipeline generate da una pipeline pubblicata.

+ **Project Brainwave**
  * Supporto aggiornato per i nuovi modelli di intelligenza artificiale disponibili in FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Prep SDK v0.2.0
La [versione 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) include le funzionalità e le correzioni di bug seguenti:

+ **Nuove funzionalità**
  * Supporto della codifica one-hot
  * Supporto della trasformazione dei quantili
   
+ **Bug risolto:**
  * Funziona con qualsiasi versione di Tornado, senza bisogno di effettuare il downgrade della versione di Tornado in uso
  * Vengono conteggiati tutti i valori, non solo i primi tre

## <a name="2018-09-public-preview-refresh"></a>2018-09 (aggiornamento dell'anteprima pubblica)

Una nuova versione aggiornata di Azure Machine Learning: Per altre informazioni su questa versione: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/

## <a name="older-notes-sept-2017---jun-2018"></a>Note precedenti: Settembre 2017 - giugno 2018
### <a name="2018-05-sprint-5"></a>Sprint 5 05-2018

Con questa versione di Azure Machine Learning, è possibile:
+ Creare funzionalità dalle immagini con una versione quantizzata di ResNet 50, eseguire il training di un classificatore in base a tali funzionalità e [distribuire tale modello in un dispositivo FPGA in Azure](../service/how-to-deploy-fpga-web-service.md) per inferenze a latenza estremamente bassa.

+ Creare e distribuire rapidamente modelli di Machine Learning e Deep Learning estremamente accurati usando [pacchetti di Azure Machine Learning personalizzati](../desktop-workbench/reference-python-package-overview.md)

### <a name="2018-03-sprint-4"></a>Sprint 4 03-2018
**Numero di versione**: 0.1.1801.24353  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Trovare la versione in uso](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Molti degli aggiornamenti riportati di seguito sono una conseguenza diretta dei commenti ricevuti. Si prega di continuare a inviarli!

**Nuove funzionalità e modifiche importanti**

- Supporto per l'esecuzione degli script su VM Ubuntu remote in modo nativo nel proprio ambiente oltre all'esecuzione basata su Docker remoto.
- Le funzionalità dell'ambiente nell'app Workbench consentono ora di creare destinazioni di calcolo e configurazioni di esecuzione, oltre all'esperienza basata sull'interfaccia della riga di comando.
![Scheda degli ambienti](media/azure-machine-learning-release-notes/environment-page.png)
- Report sulla cronologia di esecuzione personalizzabili ![Immagine dei nuovi report sulla cronologia di esecuzione](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Aggiornamenti dettagliati**

Di seguito è riportato un elenco degli aggiornamenti dettagliati in ogni area dei componenti di Azure Machine Learning in questo sprint.

#### <a name="workbench-ui"></a>Interfaccia utente di Workbench
- Report sulla cronologia di esecuzione personalizzabili
  - Configurazione dei grafici migliorata per i report sulla cronologia di esecuzione
    - I punti di ingresso usati possono essere modificati
    - I filtri di primo livello possono essere aggiunti e modificati ![Aggiungere i filtri](media/azure-machine-learning-release-notes/add-filters.jpg)
    - I grafici e le statistiche possono essere aggiunti o modificati e il trascinamento della selezione può essere ridisposto.
    ![Creazione di nuovi grafici](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD per i report sulla cronologia di esecuzione
  - Tutti i file di configurazione esistenti per la visualizzazione elenco della cronologia di esecuzione sono stati spostati nel report lato server, che funziona come pipeline nelle esecuzioni dai punti di ingresso selezionati.

- Scheda degli ambienti
  - Aggiungere facilmente la nuova destinazione di calcolo e i file di configurazione di esecuzione al progetto ![Nuova destinazione di calcolo](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Gestire a aggiornare i file di configurazione usando una semplice esperienza utente basata su form
  - Nuovo pulsante per preparare gli ambienti per l'esecuzione

- Miglioramenti delle prestazioni apportati all'elenco di file nella barra laterale

#### <a name="data-preparation"></a>Preparazione dei dati 
- Azure Machine Learning Workbench ora consente di cercare una colonna usando il nome di una colonna nota.


#### <a name="experimentation"></a>Sperimentazione
- Azure Machine Learning Workbench ora supporta l'esecuzione degli script in modo nativo nell'ambiente python o pyspark. Per questa capacità, l'utente crea e gestisce i propri ambienti dalla VM remota e usa Azure Machine Learning Workbench per eseguire gli script in tale destinazione. Vedere [Configurazione del servizio Sperimentazione di Azure Machine Learning](../desktop-workbench/experimentation-service-configuration.md) 

#### <a name="model-management"></a>Gestione modelli
- Supporto per la personalizzazione dei contenitori distribuiti: consente di personalizzare l'immagine del contenitore permettendo l'installazione di librerie esterne con apt-get e così via. Non è più limitato alle librerie installabili con pip. Per altre informazioni, vedere la [documentazione](../desktop-workbench/model-management-custom-container.md).
  - Usare il flag `--docker-file myDockerStepsFilename` e il nome file con i comandi per la creazione del manifesto, dell'immagine o del servizio.
  - L'immagine di base è Ubuntu e non può essere modificata.
  - Comando di esempio: 
  
    ```shell
    $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
    ```

### <a name="2018-01-sprint-3"></a>Sprint 3 01-2018 
**Numero di versione**: 0.1.1712.18263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Trovare la versione in uso](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Di seguito è riportato un elenco degli aggiornamenti e miglioramenti in questo sprint. Molti degli aggiornamenti sono una conseguenza diretta del feedback ricevuto. 

### <a name="2017-12-sprint-2"></a>Sprint 2 12-2017
**Numero di versione**: 0.1.1711.15263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Trovare la versione in uso](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Questa versione è il terzo aggiornamento di Azure Machine Learning, che include miglioramenti all'app Workbench, all'interfaccia della riga di comando e ai servizi back-end. Si ringrazia per i commenti inviati. Molti degli aggiornamenti riportati di seguito sono una conseguenza diretta dei commenti ricevuti. 

### <a name="2017-11-sprint-1"></a>Sprint 1 11-2017 
**Numero di versione**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Trovare la versione in uso](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

In questa versione sono stati apportati miglioramenti alla sicurezza, alla stabilità e alla manutenibilità nell'app Workbench, dell'interfaccia della riga di comando e del livello di servizi back-end. 

### <a name="2017-10-sprint-0"></a>Sprint 0 10-2017 
**Numero di versione**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Trovare la versione in uso](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Questa versione è il primo aggiornamento di Azure Machine Learning Workbench dopo la versione di anteprima pubblica iniziale in occasione della conferenza Microsoft Ignite 2017. Gli aggiornamenti principali di questa versione sono costituiti da correzioni relative all'affidabilità e alla stabilizzazione. 

## <a name="next-steps"></a>Passaggi successivi

Leggere la panoramica di [Azure Machine Learning](../service/overview-what-is-azure-ml.md).
