---
title: Previsione del carico di lavoro del server con terabyte di dati - Azure | Microsoft Docs
description: Come eseguire l'addestramento di un modello di apprendimento automatico sui Big Data usando Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: c8e023d68ec2c7e40675f985d3e13b0714cec8ea
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Previsione del carico di lavoro server in terabyte di dati

Questo articolo descrive in che modo i data scientist possono usare Azure Machine Learning Workbench per sviluppare soluzioni che richiedono l'uso di Big Data. Si può iniziare da un campione di un set di dati di grandi dimensioni, eseguire un'iterazione su preparazione dati, progettazione delle funzionalità e machine learning e quindi estendere il processo all'intero set di dati di grandi dimensioni. 

Si apprenderanno le seguenti funzionalità principali di Machine Learning Workbench:
* Passare facilmente fra destinazioni di calcolo. È possibile configurare destinazioni di calcolo diverse e usarle nella sperimentazione. In questo esempio vengono usati come destinazioni di calcolo una macchina virtuale di data science (DSVM, Data Science Virtual Machine) Ubuntu e un cluster Azure HDInsight. Si può anche configurare le destinazioni di calcolo in base alla disponibilità delle risorse. In particolare, dopo aver scalato orizzontalmente il cluster Spark con più nodi del ruolo di lavoro, è possibile usare le risorse tramite Azure Machine Learning Workbench per accelerare le esecuzioni dell'esperimento.
* Rilevamento della cronologia di esecuzione. Machine Learning Workbench può essere usato per rilevare le prestazioni dei modelli di machine learning e altre metriche di interesse.
* Operazionalizzazione del modello di machine learning. È possibile usare gli strumenti integrati in Machine Learning Workbench per distribuire il modello di machine learning addestrato come servizio Web nel servizio contenitore di Azure. Si può anche usare il servizio Web per ottenere stime in mini-batch tramite chiamate alle API REST. 
* Supporto per terabyte di dati.

> [!NOTE]
> Per esempi di codice e altro materiale correlato a questo esempio, vedere [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Panoramica del caso d'uso

Prevedere il carico di lavoro dei server è un'esigenza aziendale comune per le aziende tecnologiche che gestiscono la propria infrastruttura. Per ridurre i costi di infrastruttura, i servizi che vengono eseguiti in server sottoutilizzati devono essere raggruppati, in modo da richiedere un numero minore di computer. Ai servizi che vengono eseguiti su server eccessivamente utilizzati è necessario assegnare più computer. 

In questo scenario si analizza la stima del carico di lavoro per ogni computer (o server). In particolare vengono usati i dati della sessione in ogni server per stimare la classe di carico di lavoro del server in futuro. Il carico di ciascun server viene classificato nelle categorie bassa, media o alta usando il classificatore di foreste casuali in [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Le tecniche di apprendimento automatico e il flusso di lavoro in questo esempio possono essere facilmente estesi a problemi simili. 


## <a name="prerequisites"></a>prerequisiti

I prerequisiti per eseguire questo esempio sono i seguenti:

* Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
* Una copia installata di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md). Per installare il programma e creare un'area di lavoro, vedere la [guida rapida all'installazione](./quickstart-installation.md). Se si dispone di più sottoscrizioni, è possibile [impostare quella desiderata come sottoscrizione attiva corrente](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set).
* Windows 10 (le istruzioni generali riportate in questo esempio sono le stesse per i sistemi macOS).
* Una macchina virtuale di data science (DSVM) per Linux (Ubuntu), preferibilmente nell'area Stati Uniti orientali in cui si trovano i dati. È possibile eseguire il provisioning di una macchina virtuale di data science Ubuntu seguendo [queste istruzioni](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Si può anche vedere [questa Guida rapida](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). È consigliabile usare una macchina virtuale con almeno 8 core e 32 GB di memoria. 

Seguire le [istruzioni](known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present) per abilitare l'accesso a sudoers senza password nella macchina virtuale per Azure Machine Learning Workbench.  È possibile scegliere di usare l'[autenticazione basata su chiave SSH per la creazione e l'uso della macchina virtuale in Azure Machine Learning Workbench](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets). In questo esempio viene usata la password per accedere alla macchina virtuale.  Conservare la tabella seguente con le informazioni sulla macchina virtuale di data science per i passaggi successivi:

 Nome campo| Valore |  
 |------------|------|
Indirizzo IP macchina virtuale di data science | xxx|
 Nome utente  | xxx|
 Password   | xxx|


 Si può scegliere di usare qualsiasi VM con [motore Docker](https://docs.docker.com/engine/) installato.

* Un cluster Spark HDInsight con Hortonworks Data Platform versione 3.6 e Spark versione 2.1.x, preferibilmente nell'area Stati Uniti orientali in cui si trovano i dati. Per informazioni dettagliate su come creare cluster HDInsight, vedere [Creare un cluster Apache Spark in Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). È consigliabile usare un cluster con tre ruoli di lavoro che abbiano ognuno 16 core e 112 GB di memoria. In alternativa, è possibile scegliere il tipo di macchina virtuale `D12 V2` per il nodo head e il tipo `D14 V2` per il nodo del ruolo di lavoro. La distribuzione del cluster richiede circa 20 minuti. Per questo esempio sono necessari nome del cluster, nome utente SSH e password. Conservare la tabella seguente con le informazioni sul cluster Azure HDInsight per i passaggi successivi:

 Nome campo| Valore |  
 |------------|------|
 Nome del cluster| xxx|
 Nome utente  | xxx (sshuser per impostazione predefinita)|
 Password   | xxx|


* Un account dell'Archiviazione di Azure. Per creare una macchina virtuale, seguire [queste istruzioni](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account). Creare inoltre due contenitori BLOB privati denominati `fullmodel` e `onemonthmodel` in questo account di archiviazione. L'account di archiviazione viene usato per salvare i risultati di calcolo intermedi e i modelli di apprendimento automatico. Per questo esempio sono necessari nome dell'account di archiviazione e chiave di accesso. Conservare la tabella seguente con le informazioni sull'account di archiviazione di Azure per i passaggi successivi:

 Nome campo| Valore |  
 |------------|------|
 Nome dell'account di archiviazione| xxx|
 Chiave di accesso  | xxx|


La macchina virtuale di data science Ubuntu e il cluster Azure HDInsight creati nell'elenco dei prerequisiti sono le destinazioni di calcolo. Le destinazioni di calcolo sono la risorsa di calcolo nel contesto di Machine Learning Workbench e possono essere diverse dal computer in cui è in esecuzione Workbench.   

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Machine Learning Workbench.
2.  Nella pagina **Projects** (Progetti) selezionare il segno **+** e selezionare **New Project** (Nuovo progetto).
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto.
4.  Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) digitare **Workload Forecasting on Terabytes Data** (Previsione carico di lavoro con terabyte di dati) e selezionare il modello.
5.  Selezionare **Create**.

È possibile creare un progetto di Workbench con un repository Git creato in precedenza seguendo queste [istruzioni](./tutorial-classifying-iris-part-1.md).  
Eseguire il comando `git status` per esaminare lo stato dei file per il monitoraggio delle versioni.

## <a name="data-description"></a>Descrizione dei dati

I dati utilizzati in questo esempio sono dati del carico di lavoro del server sintetizzati. Sono ospitati in un account di archiviazione BLOB di Azure accessibile pubblicamente nell'area Stati Uniti orientali. Le informazioni specifiche dell'account di archiviazione sono disponibili nel campo `dataFile` di [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) nel formato "wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>". È possibile usare i dati direttamente dall'archiviazione BLOB di Azure. Se la risorsa di archiviazione viene usata da molti utenti contemporaneamente, è possibile usare [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) per scaricare i dati nello spazio di archiviazione locale per una migliore esperienza di sperimentazione. 

La dimensione totale dei dati è circa 1 TB. Ogni file ha una dimensione di circa 1-3 GB ed è in formato CSV senza intestazione. Ogni riga di dati rappresenta il carico di una transazione in un determinato server. Le informazioni dettagliate dello schema dei dati sono le seguenti:

Numero di colonna | Nome campo| type | DESCRIZIONE |  
|------------|------|-------------|---------------|
1  | `SessionStart` | DateTime |    Ora di inizio della sessione
2  |`SessionEnd`    | DateTime | Ora di fine della sessione
3 |`ConcurrentConnectionCounts` | Integer | Numero di connessioni simultanee
4 | `MbytesTransferred` | Double | Dati normalizzati trasferiti in megabyte
5 | `ServiceGrade` | Integer |  Livello di servizio per la sessione
6 | `HTTP1` | Integer|  La sessione usa HTTP1 o HTTP2
7 |`ServerType` | Integer   |Tipo di server
8 |`SubService_1_Load` | Double |   Carico servizio secondario 1
9 | `SubService_2_Load` | Double |  Carico servizio secondario 2
10 | `SubService_3_Load` | Double |     Carico servizio secondario 3
11 |`SubService_4_Load` | Double |  Carico servizio secondario 4
12 | `SubService_5_Load`| Double |      Carico servizio secondario 5
13 |`SecureBytes_Load`  | Double | Carico byte sicuri
14 |`TotalLoad` | Double | Carico totale nel server
15 |`ClientIP` | string|    Indirizzo IP client
16 |`ServerIP` | string|    Indirizzo IP del server



I tipi di dati previsti sono elencati nella tabella precedente. A causa di problemi di dati mancanti o dirty, non c'è garanzia che i tipi di dati siano in realtà quelli previsti. L'elaborazione dei dati deve tenere in considerazione questo. 


## <a name="scenario-structure"></a>Struttura dello scenario

I file in questo esempio sono organizzati come indicato di seguito.

| Nome file | type | DESCRIZIONE |
|-----------|------|-------------|
| `Code` | Cartella | La cartella contenente tutto il codice dell'esempio. |
| `Config` | Cartella | La cartella contenente i file di configurazione. |
| `Image` | Cartella | La cartella utilizzata per salvare le immagini per il file README. |
| `Model` | Cartella | La cartella utilizzata per salvare i file di modello scaricati dall'archiviazione BLOB. |
| `Code/etl.py` | File Python | Il file Python utilizzato per la preparazione dei dati e la progettazione delle funzionalità. |
| `Code/train.py` | File Python | Il file Python utilizzato per addestrare un modello di classificazione multipla a tre classi.  |
| `Code/webservice.py` | File Python | Il file Python utilizzato per l'operazionalizzazione.  |
| `Code/scoring_webservice.py` | File Python |  Il file Python utilizzato per la trasformazione dei dati e la chiamata del servizio Web. |
| `Code/O16Npreprocessing.py` | File Python | File Python usato per la pre-elaborazione dei dati per scoring_webservice.py.  |
| `Code/util.py` | File Python | Il file Python contenente il codice per la lettura e la scrittura dei BLOB di Azure.  
| `Config/storageconfig.json` | File JSON | Il file di configurazione per il contenitore BLOB di Azure che archivia i risultati intermedi e il modello per l'elaborazione e l'addestramento sui dati di un mese. |
| `Config/fulldata_storageconfig.json` | File JSON | Il file di configurazione per il contenitore BLOB di Azure che archivia i risultati intermedi e il modello per l'elaborazione e l'addestramento sul set di dati completo.|
| `Config/webservice.json` | File JSON | Il file di configurazione per scoring_webservice.py.|
| `Config/conda_dependencies.yml` | File YAML | Il file delle dipendenze Conda. |
| `Config/conda_dependencies_webservice.yml` | File YAML | Il file delle dipendenze Conda per il servizio Web.|
| `Config/dsvm_spark_dependencies.yml` | File YAML | Il file delle dipendenze Spark per la macchina virtuale di data science Ubuntu. |
| `Config/hdi_spark_dependencies.yml` | File YAML | Il file delle dipendenze Spark per il cluster Spark HDInsight. |
| `README.md` | File markdown | Il file markdown README. |
| `Code/download_model.py` | File Python | Il file Python utilizzato per scaricare i file di modello dal BLOB di Azure nel disco locale. |
| `Docs/DownloadModelsFromBlob.md` | File markdown | Il file markdown contenente le istruzioni su come eseguire `Code/download_model.py`. |



### <a name="data-flow"></a>Flusso di dati

Il codice in [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) carica i dati dal contenitore accessibile pubblicamente (campo `dataFile` di [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Include la preparazione dei dati e la progettazione delle funzionalità e salva i modelli e i risultati di calcolo intermedi in un contenitore privato. Il codice in [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) carica i risultati di calcolo intermedi dal contenitore privato, addestra il modello di classificazione multiclasse e scrive il modello di apprendimento automatico addestrato nel contenitore privato. 

Si deve usare un contenitore per la sperimentazione sul set di dati di un mese e un altro per la sperimentazione sul set di dati completo. Poiché i dati e i modelli vengono salvati come file PARQUET, ogni file è in realtà una cartella nel contenitore, che include più BLOB. Il contenitore risultante è simile al seguente:

| Nome di prefisso BLOB | type | DESCRIZIONE |
|-----------|------|-------------|
| featureScaleModel | Parquet | Modello di convertitore in scala standard per funzionalità numeriche. |
| stringIndexModel | Parquet | Modello di indicizzatore di stringa per funzionalità numeriche.|
| oneHotEncoderModel|Parquet | Modello di codificatore one-hot per funzionalità categoriche. |
| mlModel | Parquet | Modello di apprendimento automatico addestrato. |
| info| File di conversione Python | Informazioni relative ai dati trasformati, inclusi l'inizio dell'addestramento, la fine dell'addestramento, la durata, il timestamp per la separazione del test di addestramento e le colonne per l'indicizzazione e la codifica one-hot.

Tutti i file e BLOB nella tabella precedente vengono impiegati per l'operazionalizzazione.


### <a name="model-development"></a>Sviluppo del modello

#### <a name="architecture-diagram"></a>Diagramma dell'architettura


Il diagramma seguente mostra il flusso di lavoro end-to-end dell'uso di Machine Learning Workbench per sviluppare il modello: ![architettura](media/scenario-big-data/architecture.PNG)

Nelle sezioni seguenti viene illustrato lo sviluppo del modello usando la funzionalità di destinazione di calcolo remota in Machine Learning Workbench. Prima di tutto viene caricata una piccola quantità di dati campione e viene eseguito lo script [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) in una macchina virtuale di data science Ubuntu per una rapida iterazione. È possibile limitare ulteriormente le operazioni eseguite in [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) passando un argomento aggiuntivo per un'iterazione più veloce. Alla fine viene usato un cluster HDInsight per eseguire l'addestramento con i dati completi.     

Il file [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) viene caricato, prepara i dati ed esegue la progettazione delle funzionalità. Accetta due argomenti:
* Un file di configurazione per il contenitore dell'archiviazione Blob, per archiviare i risultati intermedi di calcolo e i modelli.
* Un argomento di configurazione di debug per un'iterazione più veloce.

Il primo argomento, `configFilename`, è un file di configurazione locale in cui si archiviano le informazioni dell'archiviazione BLOB e si specifica dove caricare i dati. Per impostazione predefinita è [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json) e verrà usato per l'esecuzione sui dati di un mese. Si include anche [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), che deve essere usato per l'esecuzione sul set di dati completo. Il contenuto della configurazione è il seguente: 

| Campo | type | DESCRIZIONE |
|-----------|------|-------------|
| storageAccount | string | Nome dell'account di archiviazione di Azure |
| storageContainer | string | Contenitore nell'account di archiviazione di Azure per archiviare i risultati intermedi |
| storageKey | string |Chiave di accesso dell'account di archiviazione di Azure |
| dataFile|string | File dell'origine dati  |
| duration| string | Durata dei dati nei file dell'origine dati|

Modificare sia `Config/storageconfig.json` che `Config/fulldata_storageconfig.json` per configurare l'account di archiviazione, la chiave di archiviazione e il contenitore BLOB per l'archiviazione dei risultati intermedi. Per impostazione predefinita, il contenitore BLOB per l'esecuzione sui dati di un mese è `onemonthmodel` e il contenitore BLOB per l'esecuzione sul set di dati completo è `fullmodel`. Assicurarsi di creare questi due contenitori nell'account di archiviazione. Il campo `dataFile` in [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) determina quali dati vengono caricati in [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). Il campo `duration` determina l'intervallo che i dati includono. Se la durata è impostata su ONE_MONTH, i dati caricati devono corrispondere a uno solo dei sette file .csv dei dati di giugno 2016. Se la durata è FULL, viene caricato il set di dati completo, con dimensione di 1 TB. Non è necessario modificare `dataFile` e `duration` in questi due file di configurazione.

Il secondo argomento è DEBUG. Se lo si imposta su FILTER_IP, l'iterazione è più veloce. L'uso di questo parametro è utile quando si vuole eseguire il debug dello script.

> [!NOTE]
> In tutti i comandi seguenti sostituire qualsiasi variabile di argomento con il valore effettivo.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Sviluppo del modello nella macchina virtuale di data science Ubuntu in Docker

#####  <a name="1-set-up-the-compute-target"></a>1. Configurare la destinazione di calcolo

Avviare la riga di comando da Machine Learning Workbench selezionando **File** > **Apri prompt dei comandi**. Quindi eseguire: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

I due file seguenti vengono creati nella cartella aml_config del progetto:

-  dockerdsvm.compute: questo file contiene le informazioni di connessione e configurazione per una destinazione di esecuzione remota.
-  dockerdsvm.runconfig: questo file è un set di opzioni di esecuzione utilizzato all'interno dell'applicazione Workbench.

Passare a dockerdsvm.runconfig e modificare la configurazione di questi campi come segue:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Preparare l'ambiente del progetto eseguendo:

```az ml experiment prepare -c dockerdsvm```


Con `PrepareEnvironment` impostato su true, Machine Learning Workbench crea l'ambiente di runtime ogni volta che si invia un processo. `Config/conda_dependencies.yml` e `Config/dsvm_spark_dependencies.yml` contengono la personalizzazione dell'ambiente di runtime. È sempre possibile modificare le dipendenze Conda, la configurazione di Spark e le dipendenze Spark modificando questi due file YMAL. Per questo esempio sono stati aggiunti `azure-storage` e `azure-ml-api-sdk` come pacchetti Python extra in `Config/conda_dependencies.yml`. Sono stati aggiunti anche `spark.default.parallelism`, `spark.executor.instances` e `spark.executor.cores` in `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Preparazione dei dati e progettazione delle funzionalità nella macchina virtuale di data science Docker

Eseguire lo script `etl.py` nella macchina virtuale di data science Docker. Usare il parametro di debug che filtra i dati caricati con indirizzi IP del server specifici:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Passare al pannello laterale e selezionare il comando **Run** (Esegui) per visualizzare la cronologia di esecuzione di `etl.py`. Si noti che il tempo di esecuzione è di circa due minuti. Se si prevede di modificare il codice per includere nuove funzionalità, fornendo FILTER_IP come secondo argomento si ottiene un'iterazione più veloce. Durante il trattamento di problemi di apprendimento automatico potrebbe essere necessario eseguire questo passaggio più volte per esplorare il set di dati o creare nuove funzionalità. 

Con la restrizione personalizzata sui dati da caricare e l'ulteriore filtro dei dati da elaborare, è possibile accelerare il processo di iterazione nello sviluppo del modello. Durante l'esperimento è consigliabile salvare periodicamente le modifiche del codice nel repository Git. Si noti che è stato usato il codice seguente in `etl.py` per consentire l'accesso al contenitore privato:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Eseguire quindi lo script `etl.py` nella macchina virtuale di data science Docker senza il parametro di debug FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Passare al pannello laterale e selezionare il comando **Run** (Esegui) per visualizzare la cronologia di esecuzione di `etl.py`. Si noti che il tempo di esecuzione è di circa quattro minuti. Il risultato elaborato di questo passaggio viene salvato nel contenitore e caricato per il training in train.py. Gli indicizzatori di stringa, le pipeline del codificatore e i convertitori in scala standard vengono salvati nel contenitore privato. Sono utilizzati per l'operazionalizzazione. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Training del modello nella macchina virtuale di data science Docker

Eseguire lo script `train.py` nella macchina virtuale di data science Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Questo passaggio carica i risultati di calcolo intermedi dall'esecuzione di `etl.py` ed esegue il training di un modello di apprendimento automatico. Questo passaggio richiede circa due minuti.

Dopo aver completato correttamente la sperimentazione sui dati di piccole dimensioni, è possibile continuare a eseguirla sul set di dati completo. È possibile iniziare con lo stesso codice e quindi eseguire l'esperimento modificando l'argomento e la destinazione di calcolo.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Sviluppo del modello nel cluster HDInsight

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Creare la destinazione di calcolo in Machine Learning Workbench per il cluster HDInsight

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

I due file seguenti vengono creati nella cartella aml_config:
    
-  myhdi.compute: questo file contiene le informazioni di connessione e configurazione per una destinazione di esecuzione remota.
-  myhdi.runconfig: questo file è un set di opzioni di esecuzione utilizzato all'interno dell'applicazione Workbench.


Passare a myhdi.runconfig e modificare la configurazione di questi campi come segue:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Preparare l'ambiente del progetto eseguendo:

```az ml experiment prepare -c myhdi```

Questo passaggio può richiedere fino a sette minuti.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Preparazione dei dati e progettazione delle funzionalità nel cluster HDInsight

Eseguire lo script `etl.py` con il parametro fulldata nel cluster HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Poiché questo processo dura per un tempo relativamente lungo (circa due ore), è possibile usare l'opzione `-a` per disabilitare il flusso di output. Quando il processo è terminato, in **Cronologia di esecuzione** è possibile visualizzare i log di driver e controller. Se il cluster è più grande, si può sempre modificare le configurazioni in `Config/hdi_spark_dependencies.yml` per usare più istanze o core. Se, ad esempio, si ha un cluster con quattro nodi del ruolo di lavoro, si può aumentare il valore di `spark.executor.instances` da 5 a 7. È possibile visualizzare l'output di questo passaggio nel contenitore **fullmodel** nell'account di archiviazione. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Training del modello nel cluster HDInsight

Eseguire lo script `train.py` nel cluster HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Poiché questo processo dura per un tempo relativamente lungo (circa 30 minuti), è possibile usare l'opzione `-a` per disabilitare il flusso di output.

#### <a name="run-history-exploration"></a>Esplorazione della cronologia di esecuzione

La cronologia di esecuzione è una funzionalità che consente di monitorare la sperimentazione in Machine Learning Workbench. Per impostazione predefinita, viene monitorata la durata della sperimentazione. In questo esempio specifico, quando si passa al set di dati completo per `Code/etl.py` nella sperimentazione, è possibile notare che la durata aumenta in modo significativo. È anche possibile registrare metriche specifiche per il monitoraggio. Per abilitare il monitoraggio delle metriche, aggiungere le righe di codice seguenti all'inizio del file Python:
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

Sulla barra laterale destra di Workbench passare a **Esecuzioni** per visualizzare la cronologia di esecuzione per ogni file Python. Si può anche passare alla pagina del repository GitHub. Viene creato un nuovo ramo il cui nome inizia con "AMLHistory" per il monitoraggio delle modifiche apportate allo script in ogni esecuzione. 


### <a name="operationalize-the-model"></a>Operazionalizzare il modello

In questa sezione si operazionalizza il modello creato nei passaggi precedenti come servizio Web. Si vedrà anche come usare il servizio Web per stimare il carico di lavoro. Usare le interfacce della riga di comando di operazionalizzazione di Machine Language per creare un pacchetto del codice e le dipendenze come immagini Docker e per pubblicare il modello come servizio Web in contenitori.

È possibile usare il prompt della riga di comando nell'area di Machine Learning Workbench per eseguire l'interfaccia della riga di comando.  È anche possibile eseguire le interfacce della riga di comando in Ubuntu Linux seguendo la [guida di installazione](./deployment-setup-configuration.md#using-the-cli). 

> [!NOTE]
> In tutti i comandi seguenti sostituire qualsiasi variabile di argomento con il valore effettivo. Per il completamento di questa sezione sono necessari circa 40 minuti.
> 

Scegliere una stringa univoca come ambiente per l'operazionalizzazione. In questo caso si usa la stringa "[unique]" per rappresentare la stringa scelta.

1. Creare l'ambiente per l'operazionalizzazione e creare il gruppo di risorse.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Si noti che è possibile usare il servizio contenitore di Azure come ambiente usando `--cluster` nel comando `az ml env setup`. Si può operazionalizzare il modello di machine learning nel [servizio contenitore di Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Esso usa [Kubernetes](https://kubernetes.io/) per automatizzare la distribuzione, il ridimensionamento e la gestione delle applicazioni in contenitori. Questo comando richiede circa 20 minuti per l'esecuzione. Per controllare se la distribuzione è stata completata correttamente, usare il codice seguente: 

        az ml env show -g [unique]rg -n [unique]

   Configurare l'ambiente di distribuzione come quello appena creato eseguendo il codice seguente:

        az ml env set -g [unique]rg -n [unique]

2. Creare e usare un account di Gestione modelli. Per creare un account di Gestione modelli, eseguire il codice seguente:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Usare Gestione modelli per l'operazionalizzazione eseguendo il codice seguente:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   L'account di Gestione modelli viene usato per gestire i modelli e i servizi Web. Dal portale di Azure si può vedere che è stato creato un nuovo account di Gestione modelli. È possibile visualizzare i modelli, manifesti, immagini Docker e servizi che vengono creati usando questo account di Gestione modelli.

3. Scaricare e registrare i modelli.

   Scaricare i modelli nel contenitore **fullmodel** nel computer locale nella directory del codice. Non scaricare il file di dati parquet con il nome "vmlSource.parquet". Non è un file di modello, ma un risultato intermedio di calcolo. È anche possibile riutilizzare i file di modello inclusi nel repository Git. Per altre informazioni, vedere [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Passare alla cartella `Model` nell'interfaccia della riga di comando e registrare i modelli come indicato di seguito:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   L'output di ogni comando fornisce un ID modello che è necessario nel passaggio successivo. Salvare gli ID in un file di testo per l'uso futuro.

4. Creare un manifesto per il servizio Web.

   Un manifesto include il codice per il servizio Web, tutti i modelli di apprendimento automatico e le dipendenze dell'ambiente di runtime. Passare alla cartella `Code` nell'interfaccia della riga di comando ed eseguire il comando seguente:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   L'output fornisce un ID di manifesto per il passaggio successivo. 

   Rimanere nella directory `Code` dove è possibile testare webservice.py eseguendo il codice seguente: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Creare un'immagine Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   L'output fornisce un ID immagine per il passaggio successivo e questa immagine Docker viene usata nel servizio contenitore. 

6. Distribuire il servizio Web nel cluster del servizio contenitore.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   L'output fornisce un ID servizio. È necessario usarlo per ottenere la chiave di autorizzazione e l'URL del servizio.

7. Chiamare il servizio Web nel codice Python per assegnare un punteggio in mini-batch.

   Usare il comando seguente per ottenere la chiave di autorizzazione:

         az ml service keys realtime -i $ServiceID 

   Usare il comando seguente per ottenere l'URL di assegnazione del punteggio del servizio:

        az ml service usage realtime -i $ServiceID

   Modificare il contenuto in `./Config/webservice.json` con l'URL del punteggio e la chiave di autorizzazione del servizio corretti. Mantenere "Bearer" nel file originale e sostituire la parte "xxx". 
   
   Passare alla directory radice del progetto e testare il servizio Web per l'assegnazione del punteggio in mini-batch usando il codice seguente:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Ridimensionare il servizio Web. 

   Per altre informazioni, vedere [How to scale operationalization on your Azure Container Service cluster](how-to-scale-clusters.md) (Come scalare l'operazionalizzazione nel cluster del servizio contenitore di Azure).
 

## <a name="next-steps"></a>Passaggi successivi

Questo esempio illustra come usare Machine Learning Workbench per il training di un modello di apprendimento automatico su Big Data e come operazionalizzare il modello sottoposto a training. In particolare è stato descritto come configurare e usare diverse destinazioni di calcolo, eseguire la cronologia di rilevamento delle metriche e usare diverse esecuzioni.

È possibile estendere il codice per esplorare la convalida incrociata e l'ottimizzazione degli iperparametri. Per altre informazioni sulla convalida incrociata e l'ottimizzazione degli iperparametri, vedere [questa risorsa GitHub](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Per altre informazioni sulle previsioni di serie temporali, vedere [questa risorsa GitHub](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).
