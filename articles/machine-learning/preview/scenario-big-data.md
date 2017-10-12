---
title: Previsione del carico di lavoro del server con terabyte di dati - Azure | Microsoft Docs
description: Come eseguire il training di un modello di apprendimento automatico sui Big Data usando Azure ML Workbench.
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: b76253fad43be231591023c4d4466bf6e3f329a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="server-workload-forecasting-on-terabytes-data"></a>Previsione del carico di lavoro del server con terabyte di dati

Questo esempio illustra in che modo i data scientist possono usare Azure ML Workbench per sviluppare soluzioni che richiedono l'uso di Big Data. Viene illustrato in che modo un utente che usa Azure ML Workbench può seguire un semplice percorso e iniziare da un campione di un set di dati di grandi dimensioni, eseguire l'iterazione della preparazione dei dati, progettare funzionalità e usare l'apprendimento automatico e infine estendere il processo all'intero set di dati di grandi dimensioni. 

Nel corso dell'articolo verranno illustrate le funzionalità chiave di Azure ML Workbench seguenti:
* Semplice passaggio tra destinazioni di calcolo: l'utente può configurare destinazioni di calcolo diverse e usarle nella sperimentazione. In questo esempio vengono usati come destinazioni di calcolo una macchina virtuale di data science (DSVM, Data Science Virtual Machine) Ubuntu e un cluster HDInsight. Viene inoltre illustrato all'utente come configurare le destinazioni di calcolo a seconda della disponibilità delle risorse. In particolare, dopo aver scalato orizzontalmente il cluster Spark (tramite l'aggiunta di più nodi del ruolo di lavoro nel cluster Spark), viene illustrato come l'utente può usare le risorse tramite Azure ML Workbench per accelerare le esecuzioni dell'esperimento.
* Monitoraggio della cronologia di esecuzione: viene illustrato all'utente come usare Azure ML Workbench per monitorare le prestazioni dei modelli di apprendimento automatico e altre metriche di interesse.
* Operazionalizzazione del modello di apprendimento automatico: viene illustrato come usare gli strumenti predefiniti di Azure ML Workbench per distribuire il modello di apprendimento automatico sottoposto a training come servizio Web nel servizio contenitore di Azure. Viene inoltre spiegato come usare il servizio Web per ottenere le stime in mini-batch tramite chiamate alle API REST. 
* Supporto per terabyte di dati.



## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte

Il repository GitHub pubblico per questo esempio contiene tutti i materiali, inclusi gli esempi di codice: 
 
[https://github.com/Azure/MachineLearningSamples-BigData](https://github.com/Azure/MachineLearningSamples-BigData)



## <a name="use-case-overview"></a>Panoramica del caso d'uso


Prevedere il carico di lavoro dei server è un'esigenza aziendale comune per le aziende tecnologiche che gestiscono la propria infrastruttura. Per ridurre i costi dell'infrastruttura, i servizi in esecuzione nei server sottoutilizzati devono essere raggruppati per l'esecuzione in un numero minore di computer, mentre per i servizi in esecuzione nei server con carico elevato devono essere messi a disposizione più computer. In questo scenario viene analizzata la stima del carico di lavoro per ogni computer (o server). In particolare, vengono usati i dati della sessione in ogni server per stimare la classe di carico di lavoro del server in futuro. Il carico di ciascun server viene classificato in classi, bassa, media e alta, usando il classificatore di foreste casuali in [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Le tecniche di apprendimento automatico e il flusso di lavoro in questo esempio possono essere facilmente estesi a problemi simili. 


## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per eseguire questo esempio sono i seguenti:

* Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
* Una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installata seguendo la [guida introduttiva all'installazione](./quickstart-installation.md) per installare il programma e creare un'area di lavoro.
* Questo scenario presuppone che Azure Machine Learning (ML) Workbench sia in esecuzione in Windows 10. Se si usa macOS, le istruzioni sono in gran parte uguali.
* Una macchina virtuale di data science (DSVM) per Linux (Ubuntu). È possibile effettuare il provisioning di una macchina virtuale di data science Ubuntu seguendo le [istruzioni](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Fare clic [qui](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu) per informazioni introduttive. È consigliabile usare una macchina virtuale con almeno 8 core e 32 GB di memoria.  Per questo esempio sono necessari indirizzo IP della macchina virtuale di data science, nome utente e password. Conservare la tabella seguente con le informazioni sulla macchina virtuale di data science per i passaggi successivi:

 Nome campo| Valore |  
 |------------|------|
Indirizzo IP macchina virtuale di data science | xxx|
 Nome utente  | xxx|
 Password   | xxx|

 È possibile scegliere di usare qualsiasi macchina virtuale (VM) con [motore Docker](https://docs.docker.com/engine/) installato.

* Un cluster Spark HDInsight con HDP versione 3.6 e Spark versione 2.1.x. Vedere [Creare un cluster Apache Spark in Azure HDInsight] (https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) per informazioni dettagliate su come creare cluster HDInsight. È consigliabile usare un cluster con tre ruoli di lavoro, che abbiano ognuno 16 core e 112 GB di memoria. In alternativa, è possibile scegliere il tipo di macchina virtuale "`D12 V2`" per il nodo head e il tipo "`D14 V2`" per il nodo del ruolo di lavoro. La distribuzione del cluster richiede circa 20 minuti. Per questo esempio sono necessari nome del cluster, nome utente SSH e password. Conservare la tabella seguente con le informazioni sul cluster Azure HDInsight per i passaggi successivi:

 Nome campo| Valore |  
 |------------|------|
 Nome del cluster| xxx|
 Nome utente  | xxx (per impostazione predefinita, il nome è sshuser)|
 Password   | xxx|


* Un account dell'Archiviazione di Azure. Seguire le [istruzioni](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) per creare un account di archiviazione di Azure. Creare inoltre due contenitori BLOB privati denominati "`fullmodel`" e "`onemonthmodel`" in questo account di archiviazione. L'account di archiviazione viene usato per salvare i risultati di calcolo intermedi e i modelli di apprendimento automatico. Per questo esempio sono necessari nome dell'account di archiviazione e chiave di accesso. Conservare la tabella seguente con le informazioni sull'account di archiviazione di Azure per i passaggi successivi:

 Nome campo| Valore |  
 |------------|------|
 Nome dell'account di archiviazione| xxx|
 Chiave di accesso  | xxx|


La macchina virtuale di data science Ubuntu e il cluster Azure HDInsight creati nell'elenco dei prerequisiti sono le destinazioni di calcolo. Le destinazioni di calcolo sono la risorsa di calcolo nel contesto di Azure ML Workbench e possono essere diverse dal computer in cui è in esecuzione Azure ML Workbench.   

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto)
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) digitare "Workload Forecasting on Terabytes Data" (Previsione carico di lavoro con terabyte di dati) e selezionare il modello
5.  Fare clic su **Crea**

È possibile creare un progetto di Azure ML Workbench con un repository Git creato in precedenza seguendo queste [istruzioni](./tutorial-classifying-iris-part-1.md).  
Eseguire il comando git status per esaminare lo stato dei file per il monitoraggio delle versioni.

## <a name="data-description"></a>Descrizione dei dati

I dati usati in questo scenario sono dati sintetizzati relativi al carico di lavoro del server e sono ospitati in un account di archiviazione BLOB di Azure accessibile pubblicamente. Le informazioni specifiche dell'account di archiviazione sono disponibili nel campo `dataFile` di [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json). È possibile usare i dati direttamente da Archiviazione BLOB di Azure. Nel caso in cui la risorsa di archiviazione sia usata da molti utenti contemporaneamente, è possibile scegliere di usare [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) per scaricare i dati nello spazio di archiviazione locale. 

Le dimensioni totali dei dati sono di circa 1 TB. Ogni file ha dimensioni di circa 1-3 GB ed è in formato CSV senza intestazione. Ogni riga di dati rappresenta il carico di una transazione in un determinato server.  Le informazioni dettagliate dello schema dei dati sono le seguenti:

Numero di colonna | Nome campo| Tipo | Descrizione |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Datetime |    Ora di inizio della sessione
2  |`SessionEnd`    | Datetime | Ora di fine della sessione
3 |`ConcurrentConnectionCounts` | Integer | Numero di connessioni simultanee
4 | `MbytesTransferred` | Double | Dati trasferiti normalizzati in MB
5 | `ServiceGrade` | Integer |  Livello di servizio per la sessione
6 | `HTTP1` | Integer|  Indicazione del fatto che la sessione usi HTTP1 o HTTP2
7 |`ServerType` | Integer   |Tipo di server
8 |`SubService_1_Load` | Double |   Carico servizio secondario 1
9 | `SubService_1_Load` | Double |  Carico servizio secondario 2
10 | `SubService_1_Load` | Double |     Carico servizio secondario 3
11 |`SubService_1_Load` | Double |  Carico servizio secondario 4
12 | `SubService_1_Load`| Double |      Carico servizio secondario 5
13 |`SecureBytes_Load`  | Double | Carico byte sicuri
14 |`TotalLoad` | Double | Carico totale nel server
15 |`ClientIP` | String|    Indirizzo IP client
16 |`ServerIP` | String|    Indirizzo IP del server



Si noti che, sebbene nella tabella precedente siano indicati i tipi di dati previsti, a causa di valori mancanti e problemi di dati modificati ma non salvati (dirty), non c'è garanzia che i tipi di dati corrispondano a quelli previsti e ciò deve essere tenuto in considerazione nell'elaborazione dei dati. 


## <a name="scenario-structure"></a>Struttura dello scenario

I file in questo esempio sono organizzati come indicato di seguito.

| File Name | Tipo | Descrizione |
|-----------|------|-------------|
| `Code` | Cartella | Cartella contenente tutto il codice dell'esempio |
| `Config` | Cartella | Cartella contenente i file di configurazione |
| `Image` | Cartella | Cartella usata per salvare le immagini per il file leggimi |
| `Model` | Cartella | Cartella usata per salvare i file di modello scaricati da Archiviazione BLOB di Azure |
| `Code/etl.py` | File Python | File Python usato per la preparazione dei dati e la progettazione delle funzionalità |
| `Code/train.py` | File Python | File Python usato per eseguire il training di un modello di classificazione multipla a tre classi  |
| `Code/webservice.py` | File Python | File Python usato per l'operazionalizzazione  |
| `Code/scoring_webservice.py` | File Python |  File Python usato per la trasformazione dei dati e la chiamata del servizio Web |
| `Code/O16Npreprocessing.py` | File Python | File Python usato per la pre-elaborazione dei dati per scoring_webservice.py.  |
| `Code/util.py` | File Python | File Python contenente il codice per la lettura e la scrittura di BLOB di Azure.  
| `Config/storageconfig.json` | File JSON | File di configurazione per il contenitore BLOB di Azure che archivia i risultati intermedi e il modello per l'elaborazione e il training sui dati di un mese |
| `Config/fulldata_storageconfig.json` | File JSON |  File di configurazione per il contenitore BLOB di Azure che archivia i risultati intermedi e il modello per l'elaborazione e il training sul set di dati completo|
| `Config/webservice.json` | File JSON | File di configurazione per scoring_webservice.py|
| `Config/conda_dependencies.yml` | File YAML | File delle dipendenze Conda |
| `Config/conda_dependencies_webservice.yml` | File YAML | File delle dipendenze Conda per il servizio Web|
| `Config/dsvm_spark_dependencies.yml` | File YAML | File delle dipendenze Spark per la macchina virtuale di data science Ubuntu |
| `Config/hdi_spark_dependencies.yml` | File YAML | File delle dipendenze Spark per il cluster Spark HDInsight |
| `README.md` | File markdown | File markdown leggimi |
| `Code/download_model.py` | File Python | File Python usato per scaricare i file di modello dal BLOB di Azure nel disco locale |
| `Docs/DownloadModelsFromBlob.md` | File markdown | File markdown contenente le istruzioni per eseguire `Code/download_model.py` |



### <a name="data-flow"></a>Flusso di dati

Il codice in [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) carica i dati dal contenitore accessibile pubblicamente (campo `dataFile` di [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Include la preparazione dei dati e la progettazione delle funzionalità e salva i modelli e i risultati di calcolo intermedi in un contenitore privato. Il codice in [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) carica i risultati di calcolo intermedi dal contenitore privato, esegue il training del modello di classificazione multiclasse e infine scrive il modello di apprendimento automatico sottoposto a training nel contenitore privato. È consigliabile che l'utente usi un contenitore per la sperimentazione sul set di dati di un mese e un altro per la sperimentazione sul set di dati completo. Poiché i dati e i modelli vengono salvati come file PARQUET, ogni file è in realtà una cartella nel contenitore, che include più BLOB. Il contenitore risultante è simile al seguente:

| Nome prefisso BLOB | Tipo | Descrizione |
|-----------|------|-------------|
| featureScaleModel | PARQUET | Modello di convertitore in scala standard per funzionalità numeriche |
| stringIndexModel | PARQUET | Modello di indicizzatore di stringa per funzionalità numeriche|
| oneHotEncoderModel|PARQUET | Modello di codificatore one-hot per funzionalità categoriche |
| mlModel | PARQUET | Modello di apprendimento automatico sottoposto a training |
| info| File di conversione Python | Informazioni relative ai dati trasformati, inclusi l'inizio del training, la fine del training, la durata, il timestamp per la separazione del test di training e le colonne per l'indicizzazione e la codifica one-hot.

Tutti i file/BLOB nella tabella precedente vengono usati per l'operazionalizzazione.


### <a name="model-development"></a>Sviluppo del modello

#### <a name="architecture-diagram"></a>Diagramma dell'architettura


Il diagramma seguente mostra il flusso di lavoro end-to-end dell'uso di Azure ML Workbench per sviluppare il modello: ![architettura](media/scenario-big-data/architecture.PNG)



Di seguito viene illustrato lo sviluppo del modello usando la funzionalità di destinazione di calcolo remota in Azure ML Workbench. Prima di tutto, viene caricato un piccolo campione di dati e viene eseguito lo script [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) in una macchina virtuale di data science Ubuntu per una rapida iterazione. È possibile limitare ulteriormente le operazioni eseguite in [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) passando un argomento aggiuntivo per un'iterazione più veloce. Alla fine, viene usato un cluster HDInsight per eseguire il training con i dati completi.     

Il file [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) esegue le attività di caricamento dei dati, preparazione dei dati e progettazione delle funzionalità. Accetta due argomenti: (1) un file di configurazione per il contenitore di archiviazione BLOB di Azure per l'archiviazione dei modelli e dei risultati di calcolo intermedi, (2) una configurazione di debug per un'iterazione più veloce.

Il primo argomento, `configFilename`, è un file di configurazione locale in cui si archiviano le informazioni di Archiviazione BLOB di Azure e si specifica dove caricare i dati. Per impostazione predefinita è [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json) e verrà usato per l'esecuzione sui dati di un mese. Si include anche [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), che è necessario usare per l'esecuzione sul set di dati completo. Il contenuto della configurazione è il seguente: 

| Campo | Tipo | Descrizione |
|-----------|------|-------------|
| storageAccount | String | Nome dell'account di archiviazione di Azure |
| storageContainer | String | Contenitore nell'account di archiviazione di Azure per archiviare i risultati intermedi |
| storageKey | String |Chiave di accesso dell'account di archiviazione di Azure |
| dataFile|String | File dell'origine dati  |
| duration| String | Durata dei dati nei file dell'origine dati|

Modificare sia `Config/storageconfig.json` che `Config/fulldata_storageconfig.json` per configurare l'account di archiviazione, la chiave di archiviazione e il contenitore BLOB per l'archiviazione dei risultati intermedi. Per impostazione predefinita, il contenitore BLOB per l'esecuzione sui dati di un mese è "`onemonthmodel`" e il contenitore BLOB per l'esecuzione sul set di dati completo è "`fullmodel`". Assicurarsi di creare questi due contenitori nell'account di archiviazione. Il campo `"dataFile"` in [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) configura i dati caricati in [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) e `"duration"` configura l'intervallo compreso nei dati. Se la durata è impostata su "ONE_MONTH", i dati caricati devono essere solo un file CSV dei sette file di dati di giugno 2016. Se la durata è "FULL", viene caricato il set di dati completo, con dimensioni di 1 TB. Non è necessario modificare `"dataFile"` e `"duration"` in questi due file di configurazione.

Il secondo argomento è DEBUG. Se lo si imposta su "FILTER_IP", l'iterazione è più veloce. L'uso di questo parametro è utile quando si vuole eseguire il debug dello script.

> [!NOTE]
> Sostituire qualsiasi variabile di argomento in tutti i comandi seguenti con il valore effettivo.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Sviluppo del modello nella macchina virtuale di data science Ubuntu in Docker

#####  <a name="1-setting-up-the-compute-target-for-docker-on-ubuntu-dsvm"></a>1. Configurazione della destinazione di calcolo per Docker nella macchina virtuale di data science Ubuntu

Avviare la riga di comando da Azure ML Workbench facendo clic sul menu "File" nell'angolo superiore sinistro di Azure ML Workbench e scegliendo il comando per l'apertura del prompt dei comandi e quindi eseguire questo comando: 

```az ml computetarget attach --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password --type remotedocker```

Al termine dell'esecuzione della riga di comando, si noterà che nella cartella aml_config del progetto sono stati creati i due file seguenti:

    dockerdsvm.compute: contains the connection and configuration information for a remote execution target
    dockerdsvm.runconfig: set of run options used when executing within the Azure ML Workbench application

Passare a dockerdsvm.runconfig e modificare la configurazione dei campi seguenti come illustrato sotto:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Preparare l'ambiente del progetto eseguendo:

```az ml experiment prepare -c dockerdsvm```


Impostando "PrepareEnvironment" su true, si consente ad Azure ML Workbench di creare l'ambiente di runtime ogni volta che si invia un processo. `Config/conda_dependencies.yml` e `Config/dsvm_spark_dependencies.yml` contengono la personalizzazione dell'ambiente di runtime. È sempre possibile modificare le dipendenze Conda, la configurazione di Spark e le dipendenze Spark modificando questi due file YMAL. Per questo esempio, sono stati aggiunti `azure-storage` e `azure-ml-api-sdk` come pacchetti Python extra in `Config/conda_dependencies.yml` e sono stati aggiunti "`spark.default.parallelism`", "`spark.executor.instances`", "`spark.executor.cores`" e così via in `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Preparazione dei dati e progettazione delle funzionalità nella macchina virtuale di data science Docker

Eseguire lo script `etl.py` nella macchina virtuale di data science Docker con il parametro di debug che filtra i dati caricati con indirizzi IP del server specifici:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Passare al pannello laterale e fare clic sul comando "Run" (Esegui) per visualizzare la cronologia di esecuzione di `etl.py`. Si noti che il tempo di esecuzione è di circa due minuti. Se si prevede di modificare il codice per includere nuove funzionalità, fornendo FILTER_IP come secondo argomento si ottiene un'iterazione più veloce. Potrebbe essere necessario eseguire questo passaggio più volte quando si gestiscono i problemi di apprendimento automatico per esplorare il set di dati o creare nuove funzionalità. Con la restrizione personalizzata sui dati da caricare e l'ulteriore filtro dei dati da elaborare, è possibile accelerare il processo di iterazione nello sviluppo del modello. Durante l'esperimento, è consigliabile salvare periodicamente le modifiche del codice nel repository Git.  Si noti che è stato usato il codice seguente in `etl.py` per consentire l'accesso al contenitore privato:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Eseguire quindi lo script `etl.py` nella macchina virtuale di data science Docker senza il parametro di debug FILTER_IP

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Passare al pannello laterale e fare clic sul comando "Run" (Esegui) per visualizzare la cronologia di esecuzione di `etl.py`. Si noti che il tempo di esecuzione è di circa quattro minuti. Il risultato elaborato di questo passaggio viene salvato nel contenitore e caricato per il training in train.py. Gli indicizzatori di stringa, le pipeline del codificatore e i convertitori in scala standard vengono anch'essi salvati nel contenitore privato e usati per l'operazionalizzazione (O16N). 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Training del modello nella macchina virtuale di data science Docker

Eseguire lo script `train.py` nella macchina virtuale di data science Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Questo passaggio carica i risultati di calcolo intermedi dall'esecuzione di `etl.py` ed esegue il training di un modello di apprendimento automatico. Questo passaggio richiede circa due minuti.

Dopo aver completato correttamente la sperimentazione sui dati di piccole dimensioni, è possibile continuare a eseguirla sul set di dati completo. È possibile iniziare con lo stesso codice e quindi eseguire l'esperimento modificando l'argomento e la destinazione di calcolo.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Sviluppo del modello nel cluster HDInsight

##### <a name="1-create-compute-target-in-azure-ml-workbench-for-the-hdinsight-cluster"></a>1. Creare la destinazione di calcolo in Azure ML Workbench per il cluster HDInsight

```az ml computetarget attach --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password --type cluster```

Al termine dell'esecuzione della riga di comando, nella cartella aml_config saranno presenti i due file seguenti:
    
    myhdo.compute: contains connection and configuration information for a remote execution target
    myhdi.runconfig: set of run options used when executing within the Azure ML Workbench application


Passare a myhdi.runconfig e modificare la configurazione dei campi seguenti come illustrato sotto:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Preparare l'ambiente del progetto eseguendo:

```az ml experiment prepare -c myhdi```

Questo passaggio può richiedere fino a sette minuti.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Preparazione dei dati e progettazione delle funzionalità nel cluster HDInsight

Eseguire lo script `etl.py` con il parametro fulldata nel cluster HDInsight

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Poiché questo processo dura per un tempo relativamente lungo (circa due ore), è possibile usare l'opzione "-a" per disabilitare il flusso di output. Al termine del processo, nella cronologia di esecuzione è possibile esaminare il log del driver e il log del controller. Se il cluster è più grande, è sempre possibile modificare le configurazioni in `Config/hdi_spark_dependencies.yml` per usare più istanze o più core. Se, ad esempio, si ha un cluster con quattro nodi del ruolo di lavoro, è possibile aumentare il valore di "`spark.executor.instances`" da 5 a 7. È possibile visualizzare l'output di questo passaggio nel contenitore "fullmodel" nell'account di archiviazione. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Training del modello nel cluster HDInsight

Eseguire lo script `train.py` nel cluster HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Poiché questo processo dura per un tempo relativamente lungo (circa mezz'ora), usare l'opzione "-a" per disabilitare il flusso di output.

#### <a name="run-history-exploration"></a>Esplorazione della cronologia di esecuzione

La cronologia di esecuzione è una funzionalità che consente di monitorare la sperimentazione in Azure ML Workbench. Per impostazione predefinita, viene monitorata la durata della sperimentazione. In questo esempio specifico, quando si passa al set di dati completo per "`Code/etl.py`" nella sperimentazione, è possibile notare che la durata aumenta in modo significativo. È anche possibile registrare metriche specifiche per il monitoraggio. Per abilitare il monitoraggio delle metriche, aggiungere le righe di codice seguenti all'inizio del file Python:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Ecco un esempio per il monitoraggio di una metrica specifica:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Passare alle esecuzioni sulla barra laterale destra di Azure ML Workbench per visualizzare la cronologia di esecuzione per ogni file Python. Passare inoltre al repository GitHub. Viene creato un nuovo ramo il cui nome inizia con "AMLHistory" per il monitoraggio delle modifiche apportate allo script in ogni esecuzione. 


### <a name="operationalization"></a>Operazionalizzazione

In questa sezione si operazionalizza il modello creato nei passaggi precedenti come servizio Web e viene illustrato come usare il servizio Web per stimare il carico di lavoro. Vengono usate le interfacce della riga di comando di operazionalizzazione di Azure ML per creare un pacchetto di codice e dipendenze come immagini Docker e il modello viene pubblicato come servizio Web in contenitore. Per altre informazioni, vedere [Operationalization Overview](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/operationalization-overview.md) (Panoramica dell'operazionalizzazione). È possibile usare il prompt della riga di comando in Azure ML Workbench per eseguire le interfacce della riga di comando di operazionalizzazione di Azure ML.  È anche possibile eseguire le interfacce della riga di comando di operazionalizzazione di Azure ML in Ubuntu Linux seguendo la [guida di installazione](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md). 

> [!NOTE]
> Sostituire qualsiasi variabile di argomento in tutti i comandi seguenti con il valore effettivo. Per il completamento di questa sezione sono necessari circa 40 minuti.
> 


Scegliere una stringa univoca come ambiente per l'operazionalizzazione. Per rappresentare la stringa scelta verrà usata la stringa "[unique]".

1. Creare l'ambiente per l'operazionalizzazione e il gruppo di risorse.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Si sceglie ora di usare il servizio contenitore di Azure come ambiente usando `--cluster` nel comando `az ml env setup`. Si sceglie di operazionalizzare il modello di apprendimento automatico nel [servizio contenitore di Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes) come se usasse [Kubernetes](https://kubernetes.io/) per l'automazione della distribuzione, la scalabilità e la gestione delle applicazioni in contenitore. Questo comando richiede circa 20 minuti per l'esecuzione. Uso 

        az ml env show -g [unique]rg -n [unique]

   Per controllare se la distribuzione è stata completata correttamente.

   Impostare l'ambiente di distribuzione come quello appena creato eseguendo:

        az ml env set -g [unique]rg -n [unique]

2. Creare un account di Gestione modelli e usarlo.

   Creare un account di Gestione modelli eseguendo:

    az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Usare Gestione modelli per l'operazionalizzazione eseguendo:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   L'account di Gestione modelli viene usato per gestire i modelli e i servizi Web. Nel portale di Azure è possibile notare che viene creato un nuovo account di Gestione modelli che può essere usato per vedere i modelli, i manifesti, le immagini Docker e i servizi creati usando questo account di Gestione modelli.

3. Scaricare e registrare i modelli.

   Scaricare i modelli nel contenitore "fullmodel" nel computer locale nella directory del codice. Non scaricare il file di dati PARQUET denominato "vmlSource.parquet" in quanto non è un file di modello, ma un risultato di calcolo intermedio. È anche possibile riutilizzare i file di modello che sono stati inclusi nel repository Git. Visitare [DownloadModelsFromBlob.md](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md) per informazioni dettagliate sul download di file PARQUET. 

   Passare alla cartella `Model` nell'interfaccia della riga di comando e registrare i modelli come indicato di seguito:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   L'output di ogni comando fornisce un ID di modello che è necessario nel passaggio successivo. Salvare gli ID in un file di testo per l'uso futuro.

4. Creare un manifesto per il servizio Web.

   Il manifesto contiene le informazioni usate per creare l'immagine Docker per i contenitori del servizio Web. Include il codice per il servizio Web, tutti i modelli di apprendimento automatico e le dipendenze dell'ambiente di runtime.  Passare alla cartella `Code` nell'interfaccia della riga di comando ed eseguire la riga di comando:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   L'output fornisce un ID di manifesto per il passaggio successivo. 

   Rimanere nella directory `Code` dove è possibile testare webservice.py eseguendo: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Creare un'immagine Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   L'output fornisce un ID di immagine per il passaggio successivo, in quanto l'immagine Docker viene usata in ACS. 

6. Distribuire il servizio Web nel cluster ACS

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   L'output fornisce un ID di servizio, che è necessario usare per ottenere la chiave di autorizzazione e l'URL del servizio.

7. Chiamare il servizio Web nel codice Python per assegnare un punteggio in mini-batch.

   Usare il comando seguente per ottenere la chiave di autorizzazione:

         az ml service keys realtime -i $ServiceID 

   e usare il comando seguente per ottenere l'URL di assegnazione del punteggio del servizio:

        az ml service usage realtime -i $ServiceID

   Modificare il contenuto in `./Config/webservice.json` con l'URL di assegnazione del punteggio del servizio e la chiave di autorizzazione appropriati (mantenere "Bearer" nel file originale e sostituire la parte "xxx"). 
   
   Passare alla directory radice del progetto e testare il servizio Web per l'assegnazione del punteggio in mini-batch usando:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Ridimensionare il servizio Web. 

   Vedere [How to scale operationalization on your ACS cluster](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md) (Come ridimensionare l'operazionalizzazione nel cluster ACS) per ridimensionare il servizio Web.
 

## <a name="conclusion"></a>Conclusioni

Questo esempio illustra come usare Azure ML Workbench per il training di un modello di apprendimento automatico su Big Data e come operazionalizzare il modello sottoposto a training. In particolare, è stato illustrato quanto segue:

* Configurazione e uso di destinazioni di calcolo diverse.

* Cronologia di esecuzione di metriche di monitoraggio ed esecuzioni diverse.

* Operazionalizzazione.

Gli utenti possono estendere il codice per esplorare la convalida incrociata e l'ottimizzazione di iperparametri. Per altre informazioni sulla convalida incrociata e sull'ottimizzazione di iperparametri, visitare https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning.  
Per altre informazioni sulla previsione in serie temporale, visitare https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting.
