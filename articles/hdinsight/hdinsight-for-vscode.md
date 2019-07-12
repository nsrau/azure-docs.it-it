---
title: Strumenti di HDInsight di Azure - usare Visual Studio Code per Hive, LLAP o PySpark
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ebc41fc74d24708a177bf554029df8384c49df05
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657246"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Usare gli strumenti di Azure HDInsight per Visual Studio Code

Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare processi batch Apache Hive, query Interactive Hive e script PySpark per Apache Spark. Prima di tutto, verrà illustrato come installare gli strumenti di HDInsight in Visual Studio Code e quindi si mostrerà come inviare processi a Hive e Spark.  

Gli strumenti di Azure HDInsight possono essere installati sulle piattaforme supportate da Visual Studio Code, che includono Windows, Linux e MacOS. Di seguito sono elencati i prerequisiti specifici per le diverse piattaforme.


## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure descritte in questo articolo, sono necessari gli elementi seguenti:

- Un cluster HDInsight. Per creare un cluster, vedere [Introduzione a HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono è necessario solo per Linux e macOS.
- [Estensione Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) per Visual Studio Code.
- [Configurare l'ambiente PySpark Interactive per Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Una directory locale denominata **HDexample**.  Questo articolo usa **C:\HD\HDexample**.

## <a name="install-azure-hdinsight-tools"></a>Installare gli strumenti di Azure HDInsight

Dopo avere completato i prerequisiti, è possibile installare gli strumenti di Azure HDInsight per Visual Studio Code.  Per installare gli strumenti di Azure HDInsight, completare i passaggi seguenti:

1. Aprire Visual Studio Code.

2. Dalla barra dei menu passare a **Visualizza** > **Estensioni**.

3. Nella casella di ricerca immettere **HDInsight**.

4. Selezionare **Azure HDInsight Tools** nei risultati della ricerca e quindi selezionare **Installa**.  

   ![HDInsight per Visual Studio Code - Installazione di Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selezionare **Ricarica** per attivare l'estensione degli **strumenti di Azure HDInsight** dopo l'installazione.


## <a name="open-hdinsight-work-folder"></a>Aprire la cartella di lavoro HDInsight

Completare i passaggi seguenti per aprire una cartella di lavoro e creare un file in Visual Studio Code:

1. Dalla barra dei menu passare a **File** > **Apri cartella** > **C:\HD\HDexample** e quindi selezionare il pulsante **Seleziona cartella**. La cartella viene visualizzata nella vista di **Explorer** a sinistra.

2. Dalla vista di **Explorer** selezionare la cartella, **HDexample**, e quindi l'icona **Nuovo file** accanto alla cartella di lavoro.

   ![Nuovo file](./media/hdinsight-for-vscode/new-file.png)

3. Denominare il nuovo file con il `.hql` (query Hive) o `.py` estensione file (script Spark).  Questo esempio usa **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Configurare l'ambiente di Azure

1. [Connettere](#connect-to-azure-account) a account di Azure oppure collegare un cluster, se non è ancora fatto.

2. Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Set Azure Environment**.

3. Selezionare un ambiente come voce di accesso predefinita.

4. Lo strumento, nel frattempo, ha già salvato la voce di accesso predefinita selezionata in **.VSCode\settings.json**. È anche possibile aggiornare direttamente la voce nel file di configurazione. 

   ![Impostare la configurazione della voce di accesso predefinita](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Connettersi all'account Azure

Prima di inviare script ai cluster HDInsight da Visual Studio Code, è necessario connettersi all'account Azure oppure collegare un cluster, usando un nome utente e una password Ambari o un account aggiunto al dominio.  Per connettersi ad Azure, seguire questa procedura:

1. Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Login**.

    ![Accesso agli strumenti HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Seguire le istruzioni in di accesso di **OUTPUT** riquadro.
    + Per l'ambiente globale di Azure, **HDInsight: Il comando di accesso** attiverà l'azione **Accedere ad Azure** nella finestra di esplorazione di HDInsight e viceversa.

        ![Istruzioni di accesso ad Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Per altri ambienti, seguire le istruzioni di accesso.

        ![Istruzioni di accesso per altri ambienti](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   Dopo essersi connessi, il nome dell'account Azure viene visualizzato sulla barra di stato nella parte inferiore sinistra della finestra di Visual Studio Code.  
  

## <a name="link-a-cluster"></a>Collegare un cluster

### <a name="link-azure-hdinsight"></a>Collegamento: HDInsight di Azure

È possibile collegare un cluster normale usando un nome utente gestito [Apache Ambari](https://ambari.apache.org/) o collegare un cluster Hadoop con Enterprise Security Pack usando un nome utente di dominio (ad esempio: user1@contoso.com).

1. Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Link a Cluster**.

   ![comando di collegamento di un cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selezionare il tipo di cluster collegato **Azure HDInsight**.

3. Immettere l'URL del cluster HDInsight.

4. Immettere il nome utente di Ambari, il cui valore predefinito è **admin**.

5. Immettere la password di Ambari.

6. Selezionare il tipo di cluster.

7. Esaminare la vista **OUTPUT** per verificare i dati.

   > [!NOTE]  
   > Vengono usati il nome utente e la password collegati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster.  



### <a name="link-generic-livy-endpoint"></a>Collegamento: endpoint Livy generico

1. Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Link a Cluster**.

2. Selezionare il tipo di cluster collegato**Generic Livy Endpoint** (Endpoint Livy generico).

3. Immettere l'endpoint Livy generico, ad esempio: http\://10.172.41.42:18080.

4. Selezionare il tipo di autorizzazione **Basic** (Di base) o **None** (Nessuna).  Se si seleziona **Basic** (Di base):  
    &emsp;a. Immettere il nome utente di Ambari, il cui valore predefinito è **admin**.  
    &emsp;b. Immettere la password di Ambari.

5. Esaminare la vista **OUTPUT** per verificare i dati.

## <a name="list-hdinsight-clusters"></a>Elencare i cluster HDInsight

1. Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: List Cluster**.

2. Selezionare la sottoscrizione da usare.

3. Esaminare la vista **OUTPUT**.  La vista mostrerà i cluster collegati e tutti i cluster nella sottoscrizione di Azure.

    ![Impostare una configurazione del cluster predefinito](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Impostare il cluster predefinito

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-hdinsight-work-folder), se è stata chiusa.  

2. Selezionare il file **HelloWorld.hql** creato [in precedenza](#open-hdinsight-work-folder), che verrà aperto nell'editor di script.

3. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **HDInsight: Set Default Cluster**.  

4. [Connettere](#connect-to-azure-account) a account di Azure oppure collegare un cluster, se non è ancora fatto.

5. Selezionare un cluster come predefinito per il file di script corrente. Gli strumenti aggiornano automaticamente il file di configurazione **.VSCode\settings.json**. 

   ![Impostare la configurazione del cluster predefinito](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Inviare query Hive interattive e script batch Hive

Gli strumenti di HDInsight per Visual Studio Code consentono di inviare query Interactive Hive e script batch Hive ai cluster di HDInsight.

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-hdinsight-work-folder), se è stata chiusa.  

2. Selezionare il file **HelloWorld.hql** creato [in precedenza](#open-hdinsight-work-folder), che verrà aperto nell'editor di script.


3. Copiare e incollare il codice seguente nel file Hive e salvarlo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Connettere](#connect-to-azure-account) a account di Azure oppure collegare un cluster, se non è ancora fatto.

5. Fare clic con il pulsante destro del mouse sull'editor di script, quindi selezionare **HDInsight: Hive Interactive** per inviare la query, oppure usare la combinazione di tasti **Ctrl + Alt + I**.  Selezionare **HDInsight: Hive Batch** per inviare lo script, oppure usare la combinazione di tasti **Ctrl + Alt + H**.  

6. Se non è stato specificato un cluster predefinito, selezionare il cluster. Gli strumenti consentono anche di inviare un blocco di codice, anziché l'intero file di script, usando il menu di scelta rapida. Dopo alcuni istanti i risultati della query vengono visualizzati in una nuova scheda.

   ![Risultato di Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Pannello dei **RISULTATI**: è possibile salvare i risultati completi come file CSV, JSON o file EXCEL in un percorso locale oppure selezionare più righe.

    - Pannello dei **MESSAGGI**: se si seleziona il numero di **riga**, si passa alla prima riga dello script in esecuzione.

## <a name="submit-interactive-pyspark-queries"></a>Inviare query PySpark interattive

È possibile inviare query PySpark interattive seguendo i passaggi seguenti:

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-hdinsight-work-folder), se è stata chiusa.  

2. Creare un nuovo file **HelloWorld.py** seguendo i passaggi illustrati [in precedenza](#open-hdinsight-work-folder).

3. Copiare e incollare il codice seguente nel file di script:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. [Connettere](#connect-to-azure-account) a account di Azure oppure collegare un cluster, se non è ancora fatto.

5. Selezionare tutto il codice e fare doppio clic su editor di script, selezionare **HDInsight: PySpark Interactive** per inviare la query oppure usare la combinazione di tasti **CTRL+ALT+I**.

   ![pulsante destro del mouse fare clic su pyspark interattivo](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Se non è stato specificato un cluster predefinito, selezionare il cluster. Dopo qualche istante, la **interattiva di Python comporta** vengono visualizzati in una nuova scheda. Gli strumenti consentono anche di inviare un blocco di codice, anziché l'intero file di script, usando il menu di scelta rapida. 

   ![finestra interattiva di python interattivo pyspark](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Invio **"% % info"** , quindi premere **MAIUSC + INVIO** per visualizzare informazioni sul processo. Facoltativa

   ![informazioni sul processo di visualizzazione](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Lo strumento supporta anche il **Spark SQL** query.

   ![Risultato della visualizzazione Pyspark interattivo](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Lo stato di invio viene visualizzato a sinistra della barra di stato inferiore durante l'esecuzione di query. Non inviare altre query se lo stato è impostato su **PySpark Kernel (busy)** (Kernel PySpark - Occupato).  

   > [!NOTE] 
   >
   > Quando **Python estensione abilitata** sia deselezionata nelle impostazioni (l'impostazione predefinita è selezionata), i risultati di interazione di pyspark inviato utilizzerà la finestra precedente.
   >
   > ![estensione di python interattivo pyspark disabilitata](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Inviare un processo batch PySpark

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-hdinsight-work-folder), se è stata chiusa.  

2. Creare un nuovo file **BatchFile.py** seguendo i passaggi descritti [in precedenza](#open-hdinsight-work-folder).

3. Copiare e incollare il codice seguente nel file di script:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Connettere](#connect-to-azure-account) a account di Azure oppure collegare un cluster, se non è ancora fatto.

5. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: PySpark Batch**, oppure usare la combinazione di tasti **Ctrl + Alt + H**. 

6. Selezionare un cluster a cui inviare il processo PySpark. 

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Dopo aver inviato un processo Python, i log di invio vengono visualizzati nella finestra **OUTPUT** in Visual Studio Code. Vengono visualizzati anche l'**URL dell'interfaccia utente di Spark** e l'**URL dell'interfaccia utente di Yarn**. È possibile aprire l'URL in un Web browser per tenere traccia dello stato del processo.

## <a name="apache-livy-configuration"></a>Configurazione di Apache Livy

La configurazione di [Apache Livy](https://livy.incubator.apache.org/) è supportata ed è possibile impostarla in **.VSCode\settings.json** nella cartella dell'area di lavoro. Attualmente la configurazione di livy supporta solo lo script Python. Per altre informazioni, vedere [Livy LEGGIMI](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Come attivare la configurazione di livy**

Metodo 1  
1. Dalla barra dei menu passare a **File** > **Preferenze** > **Impostazioni**.  
2. Nella casella di testo **Impostazioni ricerca** immettere **HDInsight Job Sumission: Livy Conf** (Invio processo HDInsight: Conf. Livy).  
3. Selezionare **Edit in settings.json** (Modifica in settings.json) per il risultato della ricerca pertinente.

Metodo 2   
Inviare un file e osservare che la cartella con estensione vscode viene aggiunta automaticamente alla cartella di lavoro. È possibile trovare la configurazione di livy facendo clic su **.vscode\settings.json**.

+ Impostazioni di progetto:

    ![Configurazione di Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Per le impostazioni **driverMomory** e **executorMomry**, impostare il valore unità, ad esempio 1 g o 1024 m. 

+ Configurazioni di Livy supportate:   

    **POST /batches**   
    Request Body

    | name | description | type | 
    | :- | :- | :- | 
    | file | File contenente l'applicazione da eseguire | percorso (obbligatorio) | 
    | proxyUser | Utente da rappresentare durante l'esecuzione del processo | string | 
    | className | Classe principale Java/Spark dell'applicazione | string |
    | args | Argomenti della riga di comando per l'applicazione | elenco di stringhe | 
    | jars | file JAR da usare in questa sessione | Elenco di stringhe | 
    | pyFiles | File di Python da usare in questa sessione | Elenco di stringhe |
    | files | file da usare in questa sessione | Elenco di stringhe |
    | driverMemory | Quantità di memoria da usare per il processo del driver | string |
    | driverCores | Numero di core da usare per il processo del driver | int |
    | executorMemory | Quantità di memoria da usare per ogni processo executor | string |
    | executorCores | Numero di core da usare per ogni executor | int |
    | numExecutors | Numero di executor da avviare per questa sessione | int |
    | archives | Archivi da usare in questa sessione | Elenco di stringhe |
    | coda | Nome della coda a cui YARN viene effettuato l'invio | string |
    | name | Nome della sessione | string |
    | conf | Proprietà di configurazione di Spark | Mapping della chiave = val |

    Contenuto risposta   
    Oggetto batch creato.

    | name | description | type | 
    | :- | :- | :- | 
    | id | ID sessione | int | 
    | appId | ID applicazione della sessione |  string |
    | appInfo | Informazioni dettagliate sull'applicazione | Mapping della chiave = val |
    | log | Righe di log | elenco di stringhe |
    | stato |   Stato batch | string |

>[!NOTE]
>La configurazione di livy assegnata verrà visualizzata nel riquadro di output quando si invia lo script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Eseguire l'integrazione con Azure HDInsight da Explorer

**Azure HDInsight** è stato aggiunto alla vista di Explorer. È possibile esplorare e gestire i cluster direttamente tramite **Azure HDInsight**.

1. [Connettere](#connect-to-azure-account) a account di Azure oppure collegare un cluster, se non è ancora fatto.

2. Dalla barra dei menu passare a **Visualizza** > **Explorer**.

3. Nel riquadro sinistro espandere **AZURE HDINSIGHT**.  Verranno elencati i cluster e le sottoscrizioni disponibili (sono supportati Spark, Hadoop e HBase). 

   ![Sottoscrizione di Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Espandere il cluster per visualizzare lo schema di database e tabella dei metadati hive.

   ![Cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Anteprima tabella Hive
È possibile visualizzare in anteprima tabella Hive in direttamente tramite il cluster **Azure HDInsight** explorer.
1. [Connettersi](#connect-to-azure-account) all'account Azure, se ancora non è stato fatto.

2. Fare clic su **Azure** icona dalla colonna all'estrema sinistra.

3. Nel riquadro sinistro, espandere AZURE HDINSIGHT. Verranno elencati i cluster e le sottoscrizioni disponibili.

4. Espandere il cluster per visualizzare lo schema di database e tabella dei metadati hive.

5. Pulsante destro del mouse sulla tabella Hive, ad esempio hivesampletable. Selezionare **Preview**. 

   ![HDInsight per vscode anteprima tabella di hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Il **anteprima risultati** finestra verrà aperta.

   ![Finestra dei risultati di HDInsight per Visual Studio code preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **RISULTATI** pannello

   è possibile salvare i risultati completi come file CSV, JSON o file EXCEL in un percorso locale oppure selezionare più righe.

- **I messaggi** pannello
   1. Quando il numero di righe nella tabella è maggiore di 100 righe, verrà visualizzato il messaggio: **Vengono visualizzate le prime 100 righe per la tabella Hive**.
   2. Quando il numero di righe nella tabella è minore o uguale a 100 righe, verrà visualizzato il messaggio: **60 righe vengono visualizzate per la tabella Hive**.
   3. Quando è presente alcun contenuto nella tabella, verrà visualizzato il messaggio: **riga 0 viene visualizzato per la tabella Hive**.

>[!NOTE]
>
>In Linux, installare xclip per abilitare copia i dati della tabella.
>
>![HDInsight per vscode in linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Funzionalità aggiuntive

HDInsight per Visual Studio Code supporta le funzionalità seguenti:

- **Completamento automatico IntelliSense**. Vengono visualizzati suggerimenti per parole chiave, metodi, variabili e così via. Icone diverse rappresentano vari tipi di oggetti.

    ![Tipi di oggetto IntelliSense degli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Indicatore di errore di IntelliSense**. Il servizio di linguaggio sottolinea gli errori di modifica nello script Hive.     
- **Evidenziazioni della sintassi**. Il servizio di linguaggio usa colori diversi per differenziare variabili, parole chiave, tipi di dati, funzioni e così via. 

    ![Evidenziazioni della sintassi degli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Solo ruolo di lettore

Gli utenti con cluster **Reader** **solo** **ruolo** non sarà più possibile inviare processi al cluster HDInsight né visualizzare il database Hive. È necessario contattare l'amministratore del cluster per eseguire l'aggiornamento del ruolo [ **HDInsight** **Cluster** **operatore** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) nel [ Portale di Azure](https://ms.portal.azure.com/). Se si conoscono le credenziali di Ambari, è possibile collegare manualmente il cluster seguendo le istruzioni seguenti.

### <a name="browse-hdinsight-cluster"></a>Esplorare i Cluster HDInsight  

Quando si fa clic su Esplora di HDInsight di Azure per espandere un cluster HDInsight, verrà richiesto per collegare il cluster, se si è solo ruolo di lettore per il cluster. Attenersi alla procedura seguente per collegare al cluster tramite Ambari credenziali. 

### <a name="submit-job-to-hdinsight-cluster"></a>Inviare processi al cluster HDInsight

Quando si inviano processi a un cluster HDInsight, verrà richiesto per collegare il cluster, se si è solo ruolo di lettore per il cluster. Attenersi alla procedura seguente per collegare al cluster tramite Ambari credenziali. 

### <a name="link-to-cluster"></a>Collegare al cluster

1.  Immettere il nome utente di Ambari 
2.  Immettere nome utente Ambari Password.

   ![HDInsight Tools per Visual Studio nome in codice utente](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![HDInsight Tools per Visual Studio Code Password](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>È possibile usare HDInsight: Elencare Cluster per verificare il cluster collegato.
>
>![HDInsight Tools per Visual Studio Code lettore collegato](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-reader-linked.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Store Gen2 (Azure Data Lake Store Gen2)

### <a name="browse-an-adls-gen2-account"></a>Selezionare un Account di Azure Data Lake Store Gen2

Quando si fa clic su HDInsight di Azure explorer per espandere un account Azure Data Lake Store Gen2, verrà richiesto di immettere lo spazio di archiviazione **chiave di accesso** se l'account di Azure non ha accesso alla risorsa di archiviazione Gen2. L'account Azure Data Lake Store Gen2 verrà automaticamente espanso dopo aver convalidata correttamente la chiave di accesso. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Inviare processi al cluster HDInsight con Azure Data Lake Store Gen2

Quando si inviano processi a un cluster HDInsight con Azure Data Lake Store Gen2, verrà richiesto di immettere lo spazio di archiviazione **chiave di accesso** se l'account Azure non abbia alcun accesso in scrittura alla risorsa di archiviazione Gen2.  Verrà inviato correttamente il processo dopo aver convalidata correttamente la chiave di accesso. 

![HDInsight Tools per Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>È possibile ottenere la chiave di accesso per account di archiviazione dal portale di Azure. Per informazioni, vedere [visualizzare e copiare le chiavi di accesso](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Scollegare il cluster

1. Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Unlink a Cluster**.  

2. Selezionare il cluster da scollegare.  

3. Esaminare la vista **OUTPUT** per verificare i dati.  

## <a name="sign-out"></a>Disconnessione  

Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Logout** (ADL: Disconnetti).  Verrà visualizzata una finestra popup nell'angolo in basso a destra con il messaggio **Logout successfully!** (Disconnessione eseguita).


## <a name="next-steps"></a>Passaggi successivi
Per un video che illustra l'uso di HDInsight per Visual Studio Code, vedere [HDInsight for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706) (HDInsight per Visual Studio Code)
