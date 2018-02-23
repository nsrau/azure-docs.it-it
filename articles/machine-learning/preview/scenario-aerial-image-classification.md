---
title: Classificazione delle immagini aeree | Microsoft Docs
description: Fornisce istruzioni per uno scenario reale sulla classificazione delle immagini aeree
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ms.openlocfilehash: 76c706496b3bcdbc1604661be85dc31000873ad3
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="aerial-image-classification"></a>Classificazione delle immagini aeree

Questo esempio illustra come usare Azure Machine Learning Workbench per coordinare l'addestramento distribuito e l'operazionalizzazione di modelli di classificazione delle immagini. Si usano due approcci per il training: (i) definizione di una DNN (Deep Neural Network, rete neurale avanzata) usando un cluster GPU di [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) e (ii) utilizzo del pacchetto [Microsoft Machine Learning for Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) per definire le caratteristiche delle immagini usando modelli CNTK di cui è stato eseguito il training preliminare e per eseguire il training di classificatori usando le caratteristiche derivate. Si applicano quindi in parallelo i modelli sottoposti a training a set di immagini di grandi dimensioni nel cloud usando un cluster [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/), che consente di ridimensionare la velocità di training e l'operazionalizzazione mediante l'aggiunta o la rimozione di nodi di lavoro.

Questo esempio illustra due approcci per il trasferimento dell'apprendimento, che sfruttano i modelli sottoposti a training preliminare per evitare i costi relativi al training delle DNN da zero. La riesecuzione del training di una DNN richiede in genere il calcolo GPU, ma si può ottenere una maggiore accuratezza se il set di training è sufficientemente grande. Il training di un classificatore semplice su immagini con caratteristiche definite non richiede il calcolo GPU, è intrinsecamente veloce e scalabile in modo arbitrario e si adatta a un numero minore di parametri. Questo metodo è pertanto un'ottima scelta quando sono disponibili pochi campioni di addestramento, come accade spesso i per casi d'uso personalizzati. 

Il cluster Spark usato in questo esempio ha 40 nodi di lavoro e il costo del relativo funzionamento è di circa $ 40/ora. Le risorse del cluster Batch AI includono otto GPU e il costo del relativo funzionamento è di circa $ 10/ora. Per completare questa procedura dettagliata sono necessarie circa tre ore. Al termine, seguire le istruzioni di pulizia per rimuovere le risorse che sono state create e interrompere gli addebiti.

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub della raccolta

Il repository GitHub pubblico per questo scenario reale contiene tutti i materiali, inclusi gli esempi di codice necessari per questo esempio:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Descrizione del caso d'uso

In questo scenario si addestrano modelli di machine learning per classificare il tipo di terreno mostrato nelle immagini aeree di tracciati di 224 x 224 metri. I modelli di classificazione degli utilizzi del suolo possono essere usati per tenere traccia dell'urbanizzazione, la deforestazione, la perdita di paludi e altre tendenze ambientali principali usando immagini aeree raccolte periodicamente. Sono stati preparati set di immagini di addestramento e convalida riguardanti gli Stati Uniti. Programma Nazionale delle Immagini Agricole ed etichette degli utilizzi del suolo pubblicate dagli Stati Uniti. Database nazionale della copertura del suolo. Le immagini di esempio in ogni classe di utilizzo del suolo sono illustrate di seguito:

![Aree di esempio per ogni etichetta di utilizzo del suolo](media/scenario-aerial-image-classification/example-labels.PNG)

Dopo l'addestramento e la convalida del modello di classificazione, lo si applicherà alle immagini aeree che riguardano la Contea di Middlesex, MA, sede del New England Research & Development (NERD) Center di Microsoft, per illustrare come questi modelli possono essere usati per analizzare le tendenze dello sviluppo urbano.

Per produrre un classificatore di immagini usando il transfer learning, i data scientist spesso costruiscono più modelli con una gamma di metodi e selezionano il modello con le massime prestazioni. Azure Machine Learning Workbench può aiutare i data scientist a coordinare l'addestramento fra diversi ambienti di calcolo, a tenere traccia e confrontare le prestazioni di più modelli e ad applicare un modello scelto a set di dati di grandi dimensioni nel cloud.

## <a name="scenario-structure"></a>Struttura dello scenario

In questo esempio i dati di immagine e i modelli pre-addestrati sono collocati in un account di archiviazione di Azure. Un cluster Azure HDInsight Spark legge questi file e costruisce un modello di classificazione delle immagini tramite MMLSpark. Il modello addestrato e le sue previsioni vengono quindi scritti nell'account di archiviazione, dove possono essere analizzati e visualizzati da un notebook Jupyter eseguito localmente. Azure Machine Learning Workbench coordina l'esecuzione remota degli script nel cluster Spark. Tiene inoltre traccia delle metriche di accuratezza per più modelli addestrati con metodi diversi, consentendo all'utente di selezionare il modello con le massime prestazioni.

![Schema per lo scenario reale di classificazione delle immagini aeree](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Queste istruzioni passo per passo iniziano con la creazione e la preparazione di un account di archiviazione di Azure e di un cluster Spark, incluso il trasferimento dei dati e l'installazione delle dipendenze. Quindi descrivono come avviare i processi di addestramento e confrontare le prestazioni dei modelli risultanti. Infine illustrano come applicare un modello scelto a un set di immagini di grandi dimensioni nel cluster Spark e analizzare i risultati predittivi in locale.


## <a name="set-up-the-execution-environment"></a>Configurare l'ambiente di esecuzione

Le istruzioni seguenti consentono di eseguire il processo di configurazione dell'ambiente di esecuzione per questo esempio.

### <a name="prerequisites"></a>prerequisiti
- Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
    - Si creerà un cluster HDInsight Spark con 40 nodi di lavoro (168 core in totale). Assicurarsi che l'account disponga di core sufficienti controllando la scheda "Utilizzo + quote" per la sottoscrizione nel portale di Azure.
       - Se si dispone di meno core, è possibile modificare il modello del cluster HDInsight per ridurre il numero di ruoli di lavoro di cui eseguire il provisioning. Le istruzioni per come procedere sono disponibili nella sezione "Creare il cluster HDInsight Spark".
    - Questo esempio crea un cluster di training Batch AI con due macchine virtuali NC6 (1 GPU, 6 vCPU). Assicurarsi che l'account disponga di core sufficienti nell'area Stati Uniti orientali controllando la scheda "Utilizzo + quote" per la sottoscrizione nel portale di Azure.
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - Seguire la [Guida introduttiva all'installazione e alla creazione](quickstart-installation.md) per installare Azure Machine Learning Workbench e creare gli account di sperimentazione e di gestione dei modelli.
- [Batch AI](https://github.com/Azure/BatchAI) Python SDK e interfaccia della riga di comando di Azure 2.0
    - Completare le sezioni seguenti del [file leggimi per i recipe di Batch per intelligenza artificiale](https://github.com/Azure/BatchAI/tree/master/recipes):
        - "Prerequisites" (Prerequisiti)
        - "Create and get your Azure Active Directory (AAD) application" (Creare e ottenere l'applicazione di Azure Active Directory)
        - "Register BatchAI Resource Providers" (Registrare i provider di risorse di Batch per intelligenza artificiale), in "Run Recipes Using Azure CLI 2.0" (Eseguire i recipe con l'interfaccia della riga di comando di Azure 2.0)
        - "Install Azure Batch AI Management Client" (Installare il client di gestione di Azure Batch per intelligenza artificiale)
        - "Install Azure Python SDK" (Installare Azure Python SDK)
    - Registrare ID client, segreto e ID tenant dell'applicazione di Azure Active Directory da creare. Tali credenziali verranno usate più avanti in questa esercitazione.
    - Al momento della stesura di questo articolo, Azure Machine Learning Workbench e Azure Batch per intelligenza artificiale usano fork separati dell'interfaccia della riga di comando di Azure 2.0. Per maggiore chiarezza, verrà fatto riferimento alla versione dell'interfaccia della riga di comando di Workbench come "interfaccia della riga di comando avviata da Azure Machine Learning Workbench" e alla versione generale (che include Batch AI) come "interfaccia della riga di comando di Azure 2.0".
- [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy), un'utilità gratuita per il coordinamento del trasferimento dei file fra account di archiviazione di Azure
    - Verificare che la cartella contenente il file eseguibile AzCopy sia nella variabile di ambiente PATH del sistema (istruzioni su come modificare le variabili di ambiente sono disponibili [qui](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp)).
- Un client SSH. È consigliabile [PuTTY](http://www.putty.org/).

Questo esempio è stato testato in un PC Windows 10. Sarà possibile eseguirlo da qualsiasi computer Windows, tra cui le macchine virtuali per data science di Azure. L'interfaccia della riga di comando di Azure 2.0 è stata installata da un file msi in base a [queste istruzioni](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Potrebbero essere necessarie piccole modifiche (ad esempio modifiche a filepaths) durante l'esecuzione di questo esempio in macOS.

### <a name="set-up-azure-resources"></a>Configurare le risorse di Azure

Questo esempio richiede un cluster HDInsight Spark e un account di archiviazione di Azure per ospitare i file rilevanti. Seguire queste istruzioni per creare queste risorse in un nuovo gruppo di risorse di Azure:

#### <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto)
3.  Nel riquadro **Crea nuovo progetto** specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Cerca modelli di progetto** digitare "Classificazione immagini aeree" e selezionare il modello
5.  Fare clic su **Crea**
 
#### <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

1. Dopo avere caricato il progetto in Azure Machine Learning Workbench, aprire l'interfaccia della riga di comando (CLI) facendo clic su File -> Apri prompt dei comandi.
    Usare questa versione dell'interfaccia della riga di comando per la maggior parte dell'esercitazione. Dove indicato, viene richiesto di aprire un'altra versione dell'interfaccia della riga di comando per preparare le risorse di Batch AI.

1. Dall'interfaccia della riga di comando accedere al proprio account di Azure usando il comando seguente:

    ```
    az login
    ```

    Verrà chiesto di visitare un URL e digitare un codice temporaneo fornito. Il sito Web richiede le credenziali dell'account di Azure.
    
1. Dopo avere eseguito l'accesso, tornare all'interfaccia della riga di comando ed eseguire il comando seguente per determinare quali sottoscrizioni di Azure sono disponibili per l'account di Azure:

    ```
    az account list
    ```

    Questo comando elenca tutte le sottoscrizioni associate all'account di Azure. Individuare l'ID della sottoscrizione da usare. Scrivere l'ID della sottoscrizione dove indicato nel comando seguente, quindi impostare la sottoscrizione attiva eseguendo il comando:

    ```
    az account set --subscription [subscription ID]
    ```

1. Le risorse di Azure create in questo esempio vengono archiviate insieme in un gruppo di risorse di Azure. Scegliere un nome di gruppo di risorse univoco e scriverlo dove indicato, quindi eseguire entrambi i comandi per creare il gruppo di risorse di Azure:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Creare l'account di archiviazione

È ora possibile creare l'account di archiviazione che ospita i file di progetto a cui HDInsight Spark deve accedere.

1. Scegliere un nome di account di archiviazione univoco e scriverlo dove indicato nel comando `set` seguente, quindi creare un account di archiviazione di Azure eseguendo entrambi i comandi:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Elencare le chiavi dell'account di archiviazione usando il comando seguente:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Registrare il valore di `key1` come chiave di archiviazione nel comando seguente, quindi eseguire il comando per archiviare il valore.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Creare una condivisione di file denominata `baitshare` nell'account di archiviazione con il comando seguente:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. Nell'editor di testo che si preferisce, caricare il file `settings.cfg` dalla sottodirectory "Code" del progetto di Azure Machine Learning Workbench e inserire il nome e la chiave dell'account di archiviazione come indicato. Salvare e chiudere il file `settings.cfg`.
1. Se non è già stato fatto, scaricare e installare l'utilità [AzCopy](http://aka.ms/downloadazcopy). Verificare che l'eseguibile di AzCopy si trovi nel percorso di sistema digitando "AzCopy" e premendo INVIO per visualizzare la relativa documentazione.
1. Eseguire i comandi seguenti per copiare tutti i dati di esempio, i modelli di cui è stato eseguito il training preliminare e gli script di training del modello nelle posizioni appropriate nell'account di archiviazione:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Per il trasferimento dei file prevedere circa un'ora. Durante l'attesa, è possibile passare alla sezione successiva. Potrebbe essere necessario aprire un'altra interfaccia della riga di comando tramite Workbench e ridefinire le variabili temporanee.

#### <a name="create-the-hdinsight-spark-cluster"></a>Creare il cluster HDInsight Spark

Il metodo consigliato per creare un cluster HDInsight consiste nell'usare il modello di gestione delle risorse cluster HDInsight Spark incluso nella sottocartella "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" del progetto.

1. Il modello di cluster HDInsight Spark è il file "template.json" nella sottocartella "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" del progetto. Per impostazione predefinita, il modello crea un cluster Spark con 40 nodi di lavoro. Se è necessario modificare questo numero, aprire il modello nell'editor di testo preferito e sostituire tutte le istanze di "40" con il numero desiderato di nodi del ruolo di lavoro.
    - Se il numero di nodi del ruolo di lavoro è inferiore, potrebbero verificarsi in seguito errori di memoria insufficiente. Per evitare gli errori di memoria, è possibile eseguire gli script di training e operazionalizzazione su un subset dei dati disponibili, come descritto più avanti in questo documento.
2. Scegliere un nome univoco e una password per il cluster HDInsight e scriverli dove indicato nel comando seguente. Creare quindi il cluster eseguendo i comandi:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

La distribuzione del cluster può richiedere fino a 30 minuti (inclusi il provisioning e l'esecuzione delle azioni script).

### <a name="set-up-batch-ai-resources"></a>Impostare le risorse di Batch AI

Mentre si attende che i file dell'account di archiviazione vengano trasferiti e che la distribuzione del cluster Spark venga completata, è possibile preparare il cluster GPU e NFS (file server di rete) di Batch AI. Aprire un prompt dei comandi dell'interfaccia della riga di comando di Azure 2.0 ed eseguire questo comando:

```
az --version 
```

Verificare che `batchai` sia elencato tra i moduli installati. In caso contrario, è possibile che si usi un'altra interfaccia della riga di comando, ad esempio una aperta tramite Workbench.

Controllare quindi che la registrazione del provider sia stata completata. La registrazione del provider richiede fino a 15 minuti e potrebbe essere ancora in corso se di recente sono state completate le [istruzioni di configurazione di Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes). Verificare che "Microsoft.Batch" e "Microsoft.BatchAI" vengano entrambi visualizzati con stato "Registered" (Registrato) nell'output del comando seguente:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

In caso contrario, usare i comandi di registrazione del provider seguenti e attendere circa 15 minuti per il completamento della registrazione.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Modificare i comandi seguenti in modo da sostituire le espressioni tra parentesi quadre con i valori usati in precedenza durante la creazione dell'account di archiviazione e del gruppo di risorse. Archiviare quindi i valori come variabili usando i comandi seguenti:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Identificare la cartella contenente il progetto di Azure Machine Learning (ad esempio, `C:\Users\<your username>\AzureML\aerialimageclassification`). Sostituire il valore tra parentesi quadre con il percorso file della cartella (senza barra rovesciata finale) ed eseguire il comando:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
A questo punto si è pronti per creare le risorse di Batch AI necessarie per questa esercitazione.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Preparare il file server di rete di Batch AI

Il cluster Batch AI accede ai dati di training in un file server di rete. L'accesso ai dati può essere molto più veloce se si accede ai file da un file server di rete (NFS) anziché da una condivisione di file di Azure o un'istanza di Archiviazione BLOB di Azure.

1. Usare il comando seguente per creare un nuovo file server di rete:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Verificare lo stato di provisioning del file server di rete usando il comando seguente:

    ```
    az batchai file-server list
    ```

    Quando "provisioningState" del file server di rete denominato "landuseclassifier" è "succeeded" (riuscito), il file server di rete sarà pronto per l'utilizzo. È possibile che il provisioning richieda circa cinque minuti.
1. Individuare l'indirizzo IP del file server di rete (NFS) nell'output del comando precedente (proprietà "fileServerPublicIp" in "mountSettings"). Scrivere l'indirizzo IP dove indicato nel comando seguente, quindi archiviare il valore eseguendo il comando:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Usando lo strumento SSH desiderato (il comando di esempio seguente usa [PuTTY](http://www.putty.org/)), eseguire lo script `prep_nfs.sh` del progetto su NFS per trasferire l'immagine di training e di convalida impostata.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Se gli aggiornamenti dello stato dell'estrazione e del download dei dati scorrono così rapidamente nella finestra della shell da risultare illeggibili, non è un problema.

Se si desidera, è possibile verificare che il trasferimento dei dati è stato eseguito come previsto accedendo al file server con lo strumento SSH preferito e controllando il contenuto della directory `/mnt/data`. Devono essere presenti due cartelle, training_images e validation_images, ognuna delle quali contiene sottocartelle denominate in base alle categorie di uso dei terreni.  I set di training e di convalida devono contenere circa 44.000 e 11.000 immagini, rispettivamente.

#### <a name="create-a-batch-ai-cluster"></a>Creare un cluster Batch AI

1. Creare il cluster tramite il comando seguente:

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p Dem0Pa$$w0rd --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. Usare il comando seguente per verificare lo stato di provisioning del cluster:

    ```
    az batchai cluster list
    ```

    Quando lo stato di allocazione del cluster denominato "landuseclassifier" diventa stazionario, è possibile inviare i processi. L'esecuzione dei processi viene tuttavia avviata solo dopo che tutte le macchine virtuali nel cluster non saranno più nello stato di preparazione. Se la proprietà relativa agli errori del cluster non è null, si è verificato un errore durante la creazione del cluster e il cluster non deve essere usato.

#### <a name="record-batch-ai-training-credentials"></a>Registrare le credenziali di training di Batch AI

Mentre si attende il completamento dell'allocazione del cluster, aprire il file `settings.cfg` dalla sottodirectory "Code" di questo progetto nell'editor di testo scelto. Aggiornare le variabili seguenti con le proprie credenziali:
- `bait_subscription_id` (ID di sottoscrizione di Azure di 36 caratteri)
- `bait_aad_client_id` (ID client/applicazione Azure Active Directory indicato nella sezione "Prerequisiti")
- `bait_aad_secret` (segreto dell'applicazione Azure Active Directory indicato nella sezione "Prerequisiti")
- `bait_aad_tenant` (ID tenant di Azure Active Directory indicato nella sezione "Prerequisiti")
- `bait_region` (nell'ambito di questo articolo, l'unica opzione è eastus)
- `bait_resource_group_name` (gruppo di risorse scelto in precedenza)

Dopo aver assegnato questi valori, le righe modificate del file settings.cfg devono essere simili al testo seguente:

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

Salvare e chiudere `settings.cfg`.

È ora possibile chiudere la finestra dell'interfaccia della riga di comando in cui sono stati eseguiti i comandi di creazione delle risorse di Batch AI. Tutti i passaggi successivi di questa esercitazione usano l'interfaccia della riga di comando avviata da Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Preparare l'ambiente di esecuzione di Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Registrare il cluster HDInsight come destinazione di calcolo di Azure Machine Learning Workbench

Una volta completata la creazione del cluster HDInsight, registrare il cluster come destinazione di calcolo per il progetto, come indicato di seguito:

1.  Eseguire il comando riportato di seguito dall'interfaccia della riga di comando di Azure Machine Learning:

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    Questo comando aggiunge due file, `myhdi.runconfig` e `myhdi.compute`, alla cartella `aml_config` del progetto.

1. Aprire il file `myhdi.compute` nell'editor di testo che si preferisce. Modificare la riga `yarnDeployMode: cluster` in `yarnDeployMode: client`, quindi salvare e chiudere il file.
1. Eseguire questo comando per preparare l'ambiente HDInsight per l'uso:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Installare le dipendenze locali

Aprire un'interfaccia della riga di comando da Azure Machine Learning Workbench e installare le dipendenze necessarie per l'esecuzione locale mediante il comando seguente:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Acquisizione e comprensione dei dati

Questo scenario usa i dati delle immagini aeree del [National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) (NAIP) disponibili pubblicamente, con una risoluzione a 1 metro. Sono stati generati set di file PNG di 224 pixel x 224 pixel ritagliati dai dati originali del programma NAIP e ordinati in base alle etichette relative all'uso dei terreni del [database National Land Cover](https://www.mrlc.gov/nlcd2011.php). Ecco un'immagine di esempio con etichetta "Developed" con dimensioni reali:

![Riquadro di esempio di terreno valorizzato](media/scenario-aerial-image-classification/sample-tile-developed.png)

I set di immagini di ~44k e 11k con bilanciamento delle classi vengono usati, rispettivamente, per il training e la convalida del modello. Viene illustrata la distribuzione di un modello con un set di immagini di ~67k relative alla contea di Middlesex, MA, sede del centro Microsoft New England Research and Development (NERD). Per altre informazioni sulle modalità di creazione di questi set di immagini, vedere il [repository Git Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Posizione della contea di Middlesex, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Durante l'installazione, i set di immagini aeree usati in questo esempio sono stati trasferiti all'account di archiviazione creato. Le immagini di training, convalida e operazionalizzazione sono tutte costituite da file PNG di 224 pixel x 224 pixel, con una risoluzione di un pixel per metro quadrato. Le immagini di training e di convalida sono state organizzate in sottocartelle in base all'etichetta relativa all'uso dei terreni. Le etichette relative all'uso dei terreni delle immagini di operazionalizzazione sono sconosciute e in molti casi ambigue. Alcune di queste immagini contengono più tipi di terreni. Per altre informazioni sulle modalità di creazione di questi set di immagini, vedere il [repository Git Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Per visualizzare le immagini di esempio nell'account di archiviazione di Azure (facoltativo):
1. Accedere al [Portale di Azure](https://portal.azure.com).
1. Cercare il nome dell'account di archiviazione nella barra di ricerca nella parte superiore dello schermo. Fare clic sull'account di archiviazione nei risultati della ricerca.
2. Fare clic sul collegamento "BLOB" nel riquadro principale dell'account di archiviazione.
3. Fare clic sul contenitore denominato "train". Verrà visualizzato un elenco di directory il cui nome dipende dall'uso dei terreni.
4. Fare clic su una qualsiasi di queste directory per caricare l'elenco di immagini contenute.
5. Fare clic su qualsiasi immagine e scaricarla per visualizzarla.
6. Se si vuole, fare clic sui contenitori denominati "test" e "middlesexma2016" per visualizzarne il contenuto.

## <a name="modeling"></a>Modellazione

### <a name="training-models-with-azure-batch-ai"></a>Modelli di training con Azure Batch AI

Lo script `run_batch_ai.py` nella sottocartella "Code\02_Modeling" del progetto di Workbench viene usato per avviare un processo di training di Batch AI. Questo processo riesegue il training di una DNN (Deep Neural Network) di classificazione delle immagini scelta dall'utente (AlexNet o 18 ResNet di cui è stato eseguito il training preliminare su ImageNet). È inoltre possibile specificare la profondità di riesecuzione del training: rieseguendo il training solo sull'ultimo livello di rete può ridurre l'overfitting quando sono disponibili pochi esempi di training, mentre l'ottimizzazione dell'intera rete (o, per AlexNet, dei livelli completamente connessi) può aumentare le prestazioni del modello quando il set di training è sufficientemente grande.

Al termine del processo di training, questo script salva il modello (insieme a un file che descrive il mapping tra output intero del modello ed etichette di stringa) e le stime nell'archiviazione BLOB. Il file di log del processo BAIT viene analizzato per estrarre i tempi di miglioramento della percentuale di errore rispetto ai periodi di training. I tempi di miglioramento della percentuale di errore vengono registrati nella funzionalità per la cronologia di esecuzione di AML Workbench per analisi successive.

Selezionare un nome per il modello sottoposto a training, un tipo di modello di cui è eseguito il training preliminare e una profondità di riesecuzione del training. Scrivere le selezioni dove indicato nel comando seguente, quindi iniziare a eseguire di nuovo il training eseguendo il comando da un'interfaccia della riga di comando di Azure ML:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Il completamento dell'esecuzione di Azure Machine Learning dovrebbe richiedere circa mezz'ora. È consigliabile eseguire alcuni comandi simili (modificando il nome di modello di output, il tipo di modello con training preliminare e la profondità di riesecuzione del training), in modo che sia possibile confrontare le prestazioni dei modelli sottoposti a training con metodi diversi.

### <a name="training-models-with-mmlspark"></a>Modelli di training con MMLSpark

Lo script `run_mmlspark.py` nella sottocartella "Code\02_Modeling" del progetto di Workbench viene usato per il training di un modello [MMLSpark](https://github.com/Azure/mmlspark) per la classificazione delle immagini. Lo script prima di tutto definisce le caratteristiche delle immagini del set di training usando una DNN (Deep Neural Network) di classificazione delle immagini di cui è stato eseguito il training preliminare sul set di dati ImageNet (AlexNet o ResNet a 18 livelli). Lo script usa quindi le immagini di cui sono state definite le caratteristiche per eseguire il training di un modello MMLSpark (una foresta casuale o un modello di regressione logistica) per classificare le immagini. Vengono quindi definite le caratteristiche del set di immagini di test e viene loro assegnato un punteggio con il modello sottoposto a training. L'accuratezza delle stime del modello nel set di test viene calcolata e registrata nella funzionalità di cronologia di esecuzione di Azure Machine Learning Workbench. Infine, il modello MMLSpark sottoposto a training e le relative stime sul set di test vengono salvati nell'archiviazione BLOB.

Selezionare un nome di modello di output univoco per il modello sottoposto a training, uno tipo di modello sottoposto a training preliminare e un tipo di modello MMLSpark. Scrivere le selezioni dove indicato nel modello di comando seguente, quindi iniziare a eseguire di nuovo il training eseguendo il comando da un'interfaccia della riga di comando di Azure ML:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

È possibile usare un parametro `--sample_frac` aggiuntivo per eseguire il training e il test del modello con un subset dei dati disponibili. Usando una piccola frazione del campione, si riducono il tempo di esecuzione e i requisiti di memoria, a scapito dell'accuratezza del modello sottoposto a training. Un'esecuzione con `--sample_frac 0.1` dovrebbe ad esempio richiedere circa venti minuti. Per altre informazioni su questo e altri parametri, eseguire `python Code\02_Modeling\run_mmlspark.py -h`.

È consigliabile eseguire questo script più volte con parametri di input diversi. Le prestazioni dei modelli risultanti possono quindi essere confrontate nella funzionalità di cronologia di esecuzione di Azure Machine Learning Workbench.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Confronto delle prestazioni dei modelli con la funzionalità di cronologia di esecuzione di Workbench

Dopo due o più esecuzioni di training di uno dei tipi disponibili, passare alla funzionalità di cronologia di esecuzione in Workbench e fare clic sull'icona a forma di orologio sulla barra dei menu a sinistra. Selezionare `run_mmlspark.py` nell'elenco di script a sinistra. Viene caricato un riquadro in cui viene messa a confronto l'accuratezza dei set di test per tutte le esecuzioni. Per visualizzare altri dettagli, scorrere verso il basso e fare clic sul nome di una singola esecuzione.

## <a name="deployment"></a>Distribuzione

Per applicare uno dei modelli sottoposti a training alle immagini aeree della contea di Middlesex, MA usando l'esecuzione remota in HDInsight, inserire il nome del modello desiderato nel comando seguente ed eseguirlo:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

È possibile usare un parametro `--sample_frac` aggiuntivo per rendere operativo il modello con un subset dei dati disponibili. Usando una piccola frazione del campione, si riducono il tempo di esecuzione e i requisiti di memoria, a scapito della completezza della stima. Per altre informazioni su questo e altri parametri, eseguire `python Code\03_Deployment\batch_score_spark -h`.

Questo script scrive le stime del modello nell'account di archiviazione. Le stime possono essere esaminate come descritto nella sezione successiva.

## <a name="visualization"></a>Visualizzazione

Il notebook Jupyter "Model prediction analysis" nella sottocartella "Code\04_Result_Analysis" del progetto di Workbench visualizza le stime di un modello. Caricare ed eseguire il notebook come indicato di seguito:
1. Aprire il progetto in Workbench e fare clic sull'icona di cartella nel menu a sinistra per caricare l'elenco delle directory.
2. Passare alla sottocartella "Code\04_Result_Analysis" e fare clic sul notebook denominato "Model prediction analysis". Dovrebbe venire visualizzato un rendering di anteprima del notebook.
3. Fare clic su "Start Notebook Server" (Avvia server notebook) per caricare il notebook.
4. Nella prima cella immettere, dove indicato, il nome del modello di cui si vuole analizzare i risultati.
5. Fare clic su "Cell -> Run All" (Cella -> Esegui tutto) per eseguire tutte le celle nel notebook.
6. Esaminare il notebook per altre informazioni sulle analisi e sulle visualizzazioni disponibili.

## <a name="cleanup"></a>Pulizia
Dopo aver completato l'esempio, si consiglia di eliminare tutte le risorse create eseguendo il comando seguente dall'interfaccia della riga di comando di Azure:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Riferimenti

- [Repository Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Descrive la costruzione del set di dati dalle immagini e dalle etichette disponibili gratuitamente
- Repository GitHub [MMLSpark](https://github.com/Azure/mmlspark)
   - Contiene altri esempi di training e valutazione dei modelli con MMLSpark

## <a name="conclusions"></a>Conclusioni

Azure Machine Learning Workbench aiuta i data scientist a distribuire facilmente il codice in destinazioni di calcolo remote. In questo esempio il codice di training MMLSpark locale è stato distribuito per l'esecuzione remota in un cluster HDInsight e uno script locale ha avviato un processo di training in un cluster GPU di Azure Batch AI. La funzionalità di cronologia di esecuzione di Azure Machine Learning Workbench ha monitorato le prestazioni di più modelli per consentire di identificare il modello più accurato. La funzionalità di notebook Jupyter di Workbench ha offerto supporto per la visualizzazione delle stime dei modelli in un ambiente grafico interattivo.

## <a name="next-steps"></a>Passaggi successivi
Per approfondire questo esempio:
- Nella funzionalità di cronologia di esecuzione di Azure Machine Learning Workbench fare clic sui simboli degli ingranaggi per scegliere i grafici e le metriche da visualizzare.
- Esaminare gli script di esempio per le istruzioni chiamando `run_logger`. Assicurarsi di aver compreso come viene registrata ogni metrica.
- Esaminare gli script di esempio per le istruzioni chiamando `blob_service`. Assicurarsi di aver compreso le modalità di archiviazione e recupero dal cloud delle stime e dei modelli sottoposti a training.
- Esplorare il contenuto dei contenitori creati nell'account di archiviazione BLOB. Assicurarsi di aver compreso quale script o comando è responsabile della creazione di ogni gruppo di file.
- Modificare lo script di training per eseguire il training di un tipo di modello MMLSpark diverso o per modificare gli iperparametri del modello. Usare la funzionalità di cronologia di esecuzione per determinare se le modifiche hanno comportato un aumento o una diminuzione dell'accuratezza del modello.
