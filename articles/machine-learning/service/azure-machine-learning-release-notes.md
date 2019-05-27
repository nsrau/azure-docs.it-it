---
title: Novità della versione
titleSuffix: Azure Machine Learning service
description: Informazioni sugli aggiornamenti più recenti al servizio Azure Machine Learning e agli SDK di Azure Machine Learning per Python e per la preparazione dei dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 3acaf86123f2cab871bc2f99cc873a73015875e2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989855"
---
# <a name="azure-machine-learning-service-release-notes"></a>Note sulla versione del servizio Azure Machine Learning

Questo articolo fornisce informazioni sulle versioni del servizio Azure Machine Learning.  Per una descrizione completa di ogni SDK, vedere la documentazione di riferimento per:
+ Azure Machine Learning [ **principale SDK per Python**](https://aka.ms/aml-sdk)
+ [**SDK di preparazione dei dati**](https://aka.ms/data-prep-sdk) di Azure Machine Learning

Per informazioni sui bug noti e le soluzioni alternative, vedere l'[elenco dei problemi noti](resource-known-issues.md).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK per Python v1.0.39
+ **Modifiche**
  + Opzione di configurazione di esecuzione auto_prepare_environment verrà deprecata, si consiglia di preparare diventando il valore predefinito automatico.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>SDK v1.1.3 della preparazione dei dati di Azure Machine Learning

+ **Nuove funzionalità**
  + Aggiunta del supporto per la lettura da un database PostgresSQL, mediante la chiamata a read_postgresql o tramite un archivio dati.
    + Vedere gli esempi nelle guide pratiche:
      + [Notebook per l'inserimento dei dati](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Notebook di archivio dati](https://aka.ms/aml-data-prep-datastore-nb)

+ **Correzioni di bug e miglioramenti**
  + Risoluzione dei problemi con conversione del tipo di colonna:
  + A questo punto converte correttamente una colonna numerica o booleana in una colonna booleana.
  + A questo punto non è normale durante il tentativo di impostare una colonna di date come tipo Data.
  + Tipi di valore JoinType e migliorati che accompagna la documentazione di riferimento. Quando si uniscono due flussi di dati, è ora possibile specificare uno di questi tipi di join:
    + NESSUNO, CORRISPONDE, INTERNA, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, COMPLETO.
  + Tipo di dati migliorati inferenza per riconoscere più formati di Data.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure, è possibile:
+ Creare ed eseguire gli esperimenti di Machine Learning automatizzati 
+ Creare una VM di Notebook per provare i notebook di Jupyter di esempio o personalizzati.
+ Nuova sezione di creazione e modifica (anteprima) nell'area di lavoro di servizio di Machine Learning, che include automatizzati di Machine Learning, l'interfaccia visiva e ospitate le macchine virtuali Notebook
    + Creare automaticamente un modello di apprendimento automatico usando 
    + Utilizzare un il trascinamento interfaccia visiva per eseguire gli esperimenti
    + Creare una VM di Notebook per esplorare i dati, creare modelli e distribuire i servizi.
+ Live grafico e una metrica l'aggiornamento in eseguire i report ed eseguire le pagine dei dettagli
+ Visualizzatore file aggiornato per i log, output e gli snapshot nelle pagine di dettagli di esecuzione.
+ La creazione di report nuova e migliorata esperienza nella scheda esperimenti. 
+ Aggiunta della possibilità scaricare il file config. JSON dalla pagina di panoramica dell'area di lavoro del servizio Azure Machine Learning.
+ Supporta la creazione dell'area di lavoro di Machine Learning servizio dall'area di lavoro Azure Databricks 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK per Python v1.0.33
+ **Nuove funzionalità**
  + Il _Workspace.create_ metodo accetta ora le configurazioni cluster predefinite per i cluster CPU e GPU.
  + Se la creazione dell'area di lavoro non riesce, vengono eliminate le risorse dipendenti.
  + Basic è stato attivato dello SKU registro contenitori di Azure predefinito.
  + Registro contenitori di Azure viene creato in modo differito, quando necessario per la creazione di immagine o esecuzione.
  + Supporto per gli ambienti per le esecuzioni di training.

### <a name="notebook-virtual-machine"></a>Macchina virtuale di notebook 

Usare una macchina virtuale Notebook come ambiente host sicuro, a livello aziendale per i notebook di Jupyter in cui è possibile programmare esperimenti di machine learning, distribuire i modelli come endpoint web ed eseguire tutte le altre operazioni supportate da Azure Machine Learning SDK usando Python. Fornisce diverse funzionalità:
+ [Crea rapidamente un notebook VM preconfigurato](quickstart-run-cloud-notebook.md) con la versione più recente del SDK di Azure Machine Learning e i pacchetti correlati.
+ L'accesso è protetto tramite tecnologie collaudate, ad esempio HTTPS, l'autenticazione di Azure Active Directory e l'autorizzazione.
+ Archiviazione cloud affidabile dei blocchi appunti e code nell'account di archiviazione blob dell'area di lavoro di Azure Machine Learning. È possibile eliminare in modo sicuro i notebook della macchina virtuale senza perdere il lavoro.
+ Preinstallato notebook di esempio per esplorare e sperimentare con le funzionalità del servizio Azure Machine Learning.
+ Funzionalità di personalizzazione completo di macchine virtuali di Azure, qualsiasi tipo di macchina virtuale, tutti i pacchetti, tutti i driver. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK per v1.0.33 Python rilasciati.

+ Azure Machine Learning con accelerazione i modelli di Hardware sul [FPGA](concept-accelerate-with-fpgas.md) disponibile a livello generale.
  + Ora puoi [usare il pacchetto di modelli di Azure ml-accelerazione](how-to-deploy-fpga-web-service.md) per:
    + Eseguire il training i pesi di una rete neurale profonda supportato (ResNet 50 ResNet 152, DenseNet 121, VGG-16 e unità SSD VGG)
    + Utilizzare trasferimento induttivo con la rete neurale profonda supportata
    + Registrare il modello con il servizio di gestione del modello e distribuire il modello in un contenitore
    + Distribuire il modello in una macchina virtuale di Azure con un FPGA in un cluster Azure Kubernetes Service (AKS)
  + Distribuire il contenitore per un [finestra di dati Azure Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) dispositivi server
  + Classificare i dati con l'endpoint gRPC con questo [esempio](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

+ Funzionalità di iperparametri per abilitare in modo dinamico aggiungendo featurizers per ottimizzare le prestazioni. Nuovo featurizers: funzionamento incorporamenti, peso di evidenza, le codifiche di destinazione, la codifica di destinazione del testo, distanza del cluster
+ CV intelligente per gestire train/valido si divide all'interno di Machine Learning automatizzati
+ Alcune modifiche di ottimizzazione della memoria e miglioramento delle prestazioni di runtime
+ Miglioramento delle prestazioni in altri termini di modello
+ Conversione di modello ONNX per l'esecuzione locale
+ Aggiunta del supporto Subsampling
+ L'arresto di quando è stato definito alcun criterio di uscita intelligente
+ Insiemi in pila

+ Previsione in serie temporale
  + Nuova funzione previsione di stima   
  + È ora possibile usare la convalida incrociata in sequenza-origin nei dati delle serie temporali
  + Nuove funzionalità aggiunte per configurare l'ora serie rimane 
  + Nuove funzionalità aggiunte per supportare funzioni di aggregazione finestra in sequenza
  + Nuova funzionalità di rilevamento di festività e featurizer quando codice paese è definito in provare le impostazioni

+ Azure Databricks
  + Abilitato serie storiche e funzionalità explainabilty/interpretazione del modello
  + È ora possibile annullare e resume (continua) ML automatizzati esperimenti
  + Aggiunta del supporto per l'elaborazione multicore

### <a name="mlops"></a>MLOps
+ **Distribuzione locale e debug per la valutazione dei contenitori**<br/> È ora possibile distribuire un modello ML in locale ed eseguire rapidamente l'iterazione nel file di assegnazione dei punteggi e le dipendenze per verificare che funzionino come previsto.

+ **InferenceConfig introdotte & Model.deploy()**<br/> A questo punto di distribuzione del modello supporta la specifica di una cartella di origine con uno script, lo stesso come un RunConfig.  Inoltre, la distribuzione del modello è stata semplificata in un unico comando.

+ **Rilevamento di riferimenti GIT**<br/> I clienti sono stati richiedere base funzionalità di integrazione di Git per alcuni minuti che aiuta a mantenere un itinerario di controllo end-to-end. Sono state implementate rilevamento tra le entità principali in Azure Machine Learning per i metadati correlate a Git (repository, commit, lo stato originario). Queste informazioni verranno raccolti automaticamente dal SDK e della riga di comando.

+ **Servizio di profilatura e la convalida del modello**<br/> I clienti spesso di ricevere lamentele da parte di difficoltà per il dimensionamento corretto le risorse di calcolo associata con il relativo servizio inferenza. Con il modello di servizio di profilatura, il cliente possa fornire gli input di esempio e si eseguirà il profiling tra 16 CPU diversi o configurazioni di memoria per la determinazione ottimale di ridimensionamento per la distribuzione.

+ **Portare la propria immagine di base per l'inferenza**<br/> Un'altra lamentela comune è la difficoltà di passaggio da sperimentazione a dipendenze condivisione RE inferenza. Con la nuova immagine di base funzionalità di condivisione, è ora possibile riutilizzare le immagini di base di sperimentazione, dipendenze e tutti, per l'inferenza. Questo dovrebbe velocizzare le distribuzioni e ridurre il divario da interna al ciclo esterno.

+ **Esperienza migliorata per la generazione dello schema Swagger**<br/> La generazione swagger precedente metodo è soggetto a errori e impossibili da automatizzare. È disponibile un nuovo modo di inline di generazione degli schemi di swagger da qualsiasi funzione Python tramite gli elementi Decorator. È stata open source questo codice e il protocollo di generazione dello schema non è associato alla piattaforma di Azure Machine Learning.

+ **Interfaccia della riga di comando di Azure Machine Learning è disponibile a livello generale (GA)**<br/> Ora i modelli possono essere distribuiti con una singola riga di comando. Abbiamo comuni commenti e suggerimenti dei clienti che non consente di distribuire un modello di Machine Learning da un notebook di Jupyter. Il [ **documentazione di riferimento della riga di comando** ](https://aka.ms/azmlcli) è stata aggiornata.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK per v1.0.30 Python rilasciati.

Il [ `PipelineEndpoint` ](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) era introdurre per aggiungere una nuova versione di una pipeline pubblicata mantenendo stesso endpoint.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>SDK v1.1.2 della preparazione dei dati di Azure Machine Learning

Note: SDK Python di preparazione dei dati non sono più installerà `numpy` e `pandas` pacchetti. Visualizzare [aggiornate le istruzioni di installazione](https://aka.ms/aml-data-prep-installation).

+ **Nuove funzionalità**
  + È ora possibile usare la trasformazione Pivot.
    + Guida alle procedure: [Notebook pivot](https://aka.ms/aml-data-prep-pivot-nb)
  + È ora possibile usare espressioni regolari in funzioni native.
    + Esempi:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + È ora possibile usare `to_upper`  e `to_lower`  funzioni nel linguaggio delle espressioni.
  + È ora possibile visualizzare il numero di valori univoci di ogni colonna in un profilo dei dati.
  + Per alcuni dei passaggi di lettore di uso comune, è possibile ora passare il `infer_column_types` argomento. Se è impostato su `True`, preparazione dei dati verrà eseguito un tentativo di rilevare e convertire automaticamente i tipi di colonna.
    + `inference_arguments` è ora deprecata.
  + È ora possibile chiamare `Dataflow.shape`.

+ **Correzioni di bug e miglioramenti**
  + `keep_columns` ora accetta un argomento facoltativo aggiuntivo `validate_column_exists`, che controlla se il risultato di `keep_columns` conterrà tutte le colonne.
  + Tutti i passaggi di lettura (che leggono da un file) ora accetta un argomento facoltativo aggiuntivo `verify_exists`.
  + Miglioramento delle prestazioni di lettura da dataframe di pandas e i profili dati della configurazione.
  + Risolto un bug in cui sezionamento di un unico passaggio da un flusso di dati non è riuscita con un singolo indice.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portale di Azure
  + È ora possibile inviare di nuovo uno Script esistente eseguito in un cluster di calcolo remoto esistente. 
  + È ora possibile eseguire una pipeline pubblicata con nuovi parametri nella scheda della pipeline. 
  + Dettagli delle esecuzioni supporta ora un nuovo Visualizzatore file di Snapshot. Quando è stato inviato a un'esecuzione specifica, è possibile visualizzare uno snapshot della directory. È anche possibile scaricare il notebook che è stato inoltrato all'avvio dell'esecuzione.
  + È ora possibile annullare le esecuzioni padre dal portale di Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK per Python v1.0.23

+ **Nuove funzionalità**
  + il SDK di Azure Machine Learning supporta ora 3.7 di Python.
  + Azure Machine Learning DNN Businesss ora offrono il supporto di più versioni predefinito. Ad esempio, `TensorFlow`  estimator ora accetta un `framework_version` parametro e gli utenti possono specificare versione '1.10' o '1.12'. Per un elenco delle versioni supportate per la versione SDK corrente, chiamare `get_supported_versions()` sulla classe framework desiderata (ad esempio, `TensorFlow.get_supported_versions()`).
  Per un elenco delle versioni supportate dalla versione di SDK più recente, vedere la [documentazione di rete neurale profonda Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>1.1.1 SDK di preparazione dei dati di Azure Machine Learning

+ **Nuove funzionalità**
  + È possibile leggere più origini di archivio dati/PercorsoDati/DataReference tramite read_ * trasformazioni.
  + È possibile eseguire le operazioni seguenti nelle colonne per creare una nuova colonna: division, floor, modulo, potenza di lunghezza.
  + Preparazione dei dati fa ora parte della famiglia di prodotti di diagnostica di Azure Machine Learning e registreranno le informazioni di diagnostica per impostazione predefinita.
    + Per disattivare questa funzionalità, impostare questa variabile di ambiente su true: DISABLE_DPREP_LOGGER

+ **Correzioni di bug e miglioramenti**
  + Documentazione di codice migliorata per funzioni e classi comunemente usate.
  + Risolto un bug in auto_read_file che non è riuscito a leggere i file di Excel.
  + Aggiunta l'opzione per sovrascrivere la cartella in read_pandas_dataframe.
  + Miglioramento delle prestazioni di installazione delle dipendenze dotnetcore2 e il supporto aggiunto per Fedora 27/28 e Ubuntu 1804.
  + Miglioramento delle prestazioni di lettura da BLOB di Azure.
  + Rilevamento del tipo colonna ora supporta le colonne di tipo Long.
  + Risolto un bug in cui sono stati visualizzati alcuni valori di data come timestamp anziché gli oggetti datetime Python.
  + Risolto un bug in cui sono stati visualizzati alcune conteggi di tipo come valori Double invece di numeri interi.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK per Python v1.0.21

+ **Nuove funzionalità**
  + Il *azureml.core.Run.create_children* metodo consente la creazione di bassa latenza di più elemento figlio viene eseguito con una singola chiamata.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>SDK versione 1.1.0 della preparazione dei dati di Azure Machine Learning

+ **Modifiche di rilievo**
  + Il concetto del pacchetto di preparazione dei dati è stato deprecato e non è più supportato. Anziché rendere persistente più flussi di dati in un unico pacchetto, è possibile rendere persistenti i flussi di dati singolarmente.
    + Guida alle procedure: [Notebook di apertura e salvataggio di flussi di dati](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nuove funzionalità**
  + Preparazione dei dati ora può riconoscere le colonne che corrispondono a un particolare tipo di semantica e suddividere di conseguenza. Il STypes attualmente supportati includono: messaggio di posta elettronica indirizzo coordinate geografiche (latitudine e longitudine), gli indirizzi IPv4 e IPv6, il numero di telefono degli Stati Uniti e codice postale degli Stati Uniti.
    + Guida alle procedure: [Semantica notebook di tipi](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Preparazione dei dati ora supporta le operazioni seguenti per generare una colonna risultante di due colonne numeriche: subtract, multiply e divide e modulo.
  + È possibile chiamare `verify_has_data()` in un flusso di dati per verificare se il flusso di dati potrebbe produrre record se eseguita.

+ **Correzioni di bug e miglioramenti**
  + È ora possibile specificare il numero di contenitori da usare in un istogramma per i profili di colonna numerica.
  + Il `read_pandas_dataframe` trasformazione richiede ora il frame di dati di stringa - o - byte digitato i nomi di colonna.
  + Risolto un bug nella `fill_nulls` trasformazione, in cui i valori sono non stati compilati correttamente se la colonna è mancante.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK per Python v1.0.18

 + **Modifiche**
   + Il pacchetto azureml-tensorboard sostituisce azureml-contrib-tensorboard.
   + Con questa versione, è possibile impostare un account utente nel cluster di calcolo gestita (amlcompute), durante la sua creazione. Questa operazione può essere eseguita mediante il passaggio di queste proprietà nella configurazione del provisioning. È possibile trovare altre informazioni, vedere la [documentazione di riferimento SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>SDK v1.0.17 della preparazione dei dati di Azure Machine Learning

+ **Nuove funzionalità**
  + Ora supporta l'aggiunta di due colonne numeriche per generare una colonna risultante utilizzando il linguaggio delle espressioni.

+ **Correzioni di bug e miglioramenti**
  + Miglioramenti significativi alla documentazione e il controllo dei parametri per random_split.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>SDK v1.0.16 della preparazione dei dati di Azure Machine Learning

+ **Correzione di bug**
  + Risolto un problema di autenticazione che è stato causato un'entità servizio da modificare l'API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK per Python v1.0.17

+ **Nuove funzionalità**

  + Azure Machine Learning fornisce ora supporto avanzato per i framework DNN più diffusi Chainer. Usando [ `Chainer` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) agli utenti di classe possono facilmente eseguire il training e distribuire modelli Chainer.
    + Informazioni su come [eseguire l'addestramento distribuito con ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Informazioni su come [eseguire Ottimizzazione degli iperparametri con Chainer usando HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Pipeline di Azure Machine Learning aggiunti trigger consente un'esecuzione della Pipeline basata su modifiche di archivio dati. La pipeline [notebook pianificazione](https://aka.ms/pl-schedule) viene aggiornato per illustrare questa funzionalità.

+ **Correzioni di bug e miglioramenti**
  + È stato aggiunto supporto per le pipeline di Azure Machine Learning per l'impostazione della proprietà source_directory_data_store in un archivio dati desiderato (ad esempio, un archivio blob) in [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) che vengono specificate per il [ PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Per impostazione predefinita questa procedura Usa File di Azure store come archivio dati sottostante, che è possibile riscontrare problemi di limitazioni quando viene eseguito simultaneamente un numero elevato di passaggi.

### <a name="azure-portal"></a>Portale di Azure

+ **Nuove funzionalità**
  + Esperienza dell'editor per i report di tabella nuova operazione di trascinamento. Gli utenti potranno trascinare una colonna dal corretto per l'area della tabella in cui verrà visualizzata un'anteprima della tabella. Le colonne possono essere riorganizzate.
  + Nuovo Visualizzatore file di log
  + Collegamenti a sperimentare esecuzioni, calcolo, modelli, immagini e le distribuzioni dalla scheda attività

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>SDK v1.0.15 della preparazione dei dati di Azure Machine Learning

+ **Nuove funzionalità**
  + Ora supporta la scrittura di file i flussi provenienti da un flusso di dati di preparazione dei dati. Offre inoltre la possibilità di modificare i nomi di flusso di file per creare nuovi nomi di file.
    + Guida alle procedure: [Notebook di utilizzo con flussi di File](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Correzioni di bug e miglioramenti**
  + Miglioramento delle prestazioni di t-Digest su grandi set di dati.
  + Preparazione dei dati ora supporta la lettura di dati da un percorso dati.
  + Una codifica a caldo ora funziona con le colonne numeriche e booleane.
  + Altre correzioni di bug varie.

## <a name="2019-02-11"></a>11 febbraio 2019

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK per Python v1.0.15

+ **Nuove funzionalità**
  + Pipeline di Azure Machine Learning aggiunto AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (notebook), la funzionalità di programmazione basati su tempo e ([notebook](https://aka.ms/pl-schedule)).
  +  È stato effettuato l'aggiornamento di DataTranferStep per garantire il funzionamento con il server SQL Azure e il database di Azure per PostgreSQL ([notebook](https://aka.ms/pl-data-trans)).

+ **Modifiche**
  + `PublishedPipeline.get_published_pipeline` deprecato a favore di `PublishedPipeline.get`.
  + `Schedule.get_schedule` deprecato a favore di `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Nuove funzionalità**
  + Data Prep ora supporta la lettura da un database SQL di Azure con l'archivio dati.
 
+ **Modifiche**
  + Migliorate le prestazioni di memoria di determinate operazioni su grandi quantità di dati.
  + Per la specifica di `read_pandas_dataframe()` è ora necessario `temp_folder`.
  + La proprietà `name` in `ColumnProfile` è deprecata. Usare `column_name`.

## <a name="2019-01-28"></a>28 gennaio 2019

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK per Python v1.0.10

+ **Modifiche**: 
  + Azure ML SDK non dispone più di pacchetti azure-cli come dipendenza. In particolare, le dipendenze di azure-cli-core e azure-cli-profilo sono state rimosse da azureml-core. Le modifiche con un impatto per l'utente sono:
    + Se si esegue "az login" e quindi usando Azure ml-sdk, il SDK eseguirà il registro del codice del browser o un dispositivo in un'altra volta. Non utilizza nessuno stato di credenziali creato da "az login".
    + Per l'autenticazione di Azure CLI, ad esempio usando "az login", usare la classe _azureml.core.authentication.AzureCliAuthentication_. Per l'autenticazione di Azure CLI, eseguire _i parametri di inizializzazione del programma per installare azure-cli_ nell'ambiente di Python in cui è stato installato azureml-sdk.
    + Se si esegue "az login" usando un'entità di servizio per l'automazione, è consigliabile usare la classe _azureml.core.authentication.ServicePrincipalAuthentication_, in quanto azure ml-sdk non userà credenziali di stato create dal comando Azure CLI. 

+ **Correzioni di bug**: questa versione contiene principalmente correzioni di bug minori.

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **Correzioni di bug**
  + Miglioramento delle prestazioni di recupero di profili dati.
  + Bug secondari correlati alla segnalazione degli errori risolti.
  
### <a name="azure-portal-new-features"></a>Portale di Azure: nuove funzionalità
+ Nuova funzionalità di trascinamento della traccia per i report. Gli utenti possono trascinare una colonna o un attributo dal pozzo all'area del grafico in cui il sistema selezionerà automaticamente il tipo di grafico appropriato per l'utente in base al tipo di informazioni. Gli utenti possono modificare il tipo di grafico in altri tipi applicabili o aggiungere altri attributi.

    Tipi di grafico supportati:
    - Grafico a linee
    - Istogramma
    - Grafico a barre in pila
    - Box plot
    - Tracciato a dispersione
    - Grafico a bolle
+ Il portale a questo punto genera in modo dinamico i report per gli esperimenti. Quando un utente invia un'esecuzione a un esperimento, verrà generato automaticamente un report contenente i grafici e le metriche registrate per consentire il confronto tra diverse esecuzioni. 

## <a name="2019-01-14"></a>14-01-2019

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK per Python v1.0.8

+ **Correzioni di bug**: questa versione contiene principalmente correzioni di bug minori.

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **Nuove funzionalità**
  + Miglioramenti dell'archivio dati (documentati in [Manuale delle procedure dell'archivio dati](https://aka.ms/aml-data-prep-datastore-nb))
    + Maggiore capacità di lettura e scrittura di Azure File Share e archivi dati ADLS nel ridimensionamento verticale.
    + Quando si usano archivi dati, la preparazione dei dati ora supporta l'utilizzo dell'autenticazione principale del servizio invece dell'autenticazione interattiva.
    + Aggiunto supporto per gli URL wasb e wasbs.

## <a name="2019-01-09"></a>09-01-2019

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **Correzioni di bug**
  + Correzione di un bug relativo alla lettura da contenitori BLOB di Azure leggibili pubblicamente in Spark

## <a name="2018-12-20"></a>20-12-2018 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK per Python v1.0.6
+ **Correzioni di bug**: questa versione contiene principalmente correzioni di bug minori.

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

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
+ Viene usato per il modello corsi di formazione e batch inferenza/punteggio
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
  + I [notebook di esempio](https://aka.ms/aml-notebooks) sono ora aggiornati per l'uso del nuovo ambiente di calcolo gestito.
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
  * *azureml.core.compute.AmlCompute* imposta come deprecate le classi *azureml.core.compute.BatchAICompute* e *azureml.core.compute.DSVMCompute*. La seconda verrà rimossa in versioni successive. La classe AmlCompute ha ora una definizione più semplice, necessita solo dei parametri vm_size e max_nodes ed è in grado di ridimensionare automaticamente il cluster da 0 al valore di max_nodes quando viene inviato un processo. I [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) sono stati aggiornati con queste informazioni e dovrebbero offrire esempi di utilizzo. Ci auguriamo che gli utenti apprezzeranno questa semplificazione e le interessanti funzionalità che verranno aggiunte nelle prossime versioni.

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


## <a name="next-steps"></a>Passaggi successivi

Leggere la panoramica del [servizio Azure Machine Learning](../service/overview-what-is-azure-ml.md).
