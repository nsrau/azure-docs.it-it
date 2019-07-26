---
title: Azure HDInsight per Visual Studio Code
description: Informazioni su come usare Spark & hive Tools (Azure HDInsight) per Visual Studio Code per creare e inviare query e script.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: da5cdd36e70166d274d50fcb093c0889cf534172
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489010"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Usare gli strumenti di Spark & hive per Visual Studio Code

Informazioni su come usare gli strumenti di Spark & hive per Visual Studio Code per creare e inviare Apache Hive processi batch, query hive interattive e script PySpark per Apache Spark. Prima di tutto verrà descritto come installare gli strumenti hive di Spark & in Visual Studio Code e verrà illustrato come inviare i processi ad hive e Spark.  

Spark & gli strumenti hive possono essere installati su piattaforme supportate da Visual Studio Code, tra cui Windows, Linux e macOS. Di seguito sono elencati i prerequisiti specifici per le diverse piattaforme.


## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure descritte in questo articolo, sono necessari gli elementi seguenti:

- Un cluster HDInsight. Per creare un cluster, vedere [Introduzione a HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). O un cluster Spark/hive che supporta l'endpoint Livio.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono è necessario solo per Linux e macOS.
- [Configurare l'ambiente PySpark Interactive per Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Una directory locale denominata **HDexample**.  Questo articolo usa **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Installare Spark & gli strumenti hive

Dopo aver completato i prerequisiti, è possibile installare Spark & hive Tools for Visual Studio Code.  Completare i passaggi seguenti per installare Spark & hive Tools:

1. Aprire Visual Studio Code.

2. Dalla barra dei menu passare a **Visualizza** > **Estensioni**.

3. Nella casella di ricerca immettere **Spark & hive**.

4. Selezionare **Spark & hive Tools** nei risultati della ricerca e quindi selezionare **Install (installa**).  

   ![Spark & hive per l'installazione di Visual Studio Code Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. **Ricarica** quando necessario.


## <a name="open-work-folder"></a>Apri cartella di lavoro

Completare i passaggi seguenti per aprire una cartella di lavoro e creare un file in Visual Studio Code:

1. Dalla barra dei menu passare a **File** > **Apri cartella** > **C:\HD\HDexample** e quindi selezionare il pulsante **Seleziona cartella**. La cartella viene visualizzata nella vista di **Explorer** a sinistra.

2. Dalla vista di **Explorer** selezionare la cartella, **HDexample**, e quindi l'icona **Nuovo file** accanto alla cartella di lavoro.

   ![Nuovo file](./media/hdinsight-for-vscode/new-file.png)

3. Assegnare un nome al nuovo file con `.hql` l'estensione di file (query `.py` hive) o (script Spark).  Questo esempio usa **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Configurare l'ambiente di Azure

Per l'utente del cloud nazionale, seguire la procedura per impostare prima di tutto l' **ambiente di Azure e quindi usare Azure:**  Comando accedi per accedere ad Azure.
   
1. Fare clic su **File\Preferences\Settings**.
2. Cerca **in Azure: Cloud**
3. Selezionare il cloud nazionale dall'elenco.

   ![Impostare la configurazione della voce di accesso predefinita](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Connettersi all'account Azure

Prima di poter inviare script ai cluster da Visual Studio Code, è necessario connettersi all'account Azure o collegare un cluster (usando il nome utente/password di Ambari o l'account aggiunto al dominio).  Per connettersi ad Azure, seguire questa procedura:

1. Dalla barra dei menu passare a **Visualizza** > **riquadro comandi**e immettere **Azure:** Accedi.

    ![Strumenti di Spark & hive per Visual Studio Code login](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Seguire le istruzioni di accesso per accedere ad Azure. Dopo la connessione, il nome dell'account Azure viene visualizzato sulla barra di stato nella parte inferiore della finestra di Visual Studio Code.  
  

## <a name="link-a-cluster"></a>Collegare un cluster

### <a name="link-azure-hdinsight"></a>Collegamento: HDInsight di Azure

È possibile collegare un cluster normale usando un nome utente gestito [Apache Ambari](https://ambari.apache.org/) o collegare un cluster Hadoop con Enterprise Security Pack usando un nome utente di dominio (ad esempio: `user1@contoso.com`).

1. Dalla barra dei menu passare a **Visualizza** > **riquadro comandi**e immettere **Spark/hive: Link a Cluster**.

   ![comando di collegamento di un cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selezionare il tipo di cluster collegato **Azure HDInsight**.

3. Immettere l'URL del cluster HDInsight.

4. Immettere il nome utente di Ambari, il cui valore predefinito è **admin**.

5. Immettere la password di Ambari.

6. Selezionare il tipo di cluster.

7. Impostare il nome visualizzato del cluster (facoltativo).

8. Esaminare la vista **OUTPUT** per verificare i dati.

   > [!NOTE]  
   > Vengono usati il nome utente e la password collegati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster.  


### <a name="link-generic-livy-endpoint"></a>Collegamento: endpoint Livy generico

1. Dalla barra dei menu passare a **Visualizza** > **riquadro comandi**e immettere **Spark/hive: Link a Cluster**.

2. Selezionare il tipo di cluster collegato**Generic Livy Endpoint** (Endpoint Livy generico).

3. Immettere l'endpoint Livy generico, ad esempio: http\://10.172.41.42:18080.

4. Selezionare il tipo di autorizzazione **Basic** (Di base) o **None** (Nessuna).  Se si seleziona **Basic** (Di base):  
    &emsp;a. Immettere il nome utente di Ambari, il cui valore predefinito è **admin**.  
    &emsp;b. Immettere la password di Ambari.

5. Esaminare la vista **OUTPUT** per verificare i dati.

## <a name="list-clusters"></a>Elencare cluster

1. Dalla barra dei menu passare a **Visualizza** > **riquadro comandi**e immettere **Spark/hive: List Cluster**.

2. Selezionare la sottoscrizione da usare.

3. Esaminare la vista **OUTPUT**.  La vista mostrerà i cluster collegati e tutti i cluster nella sottoscrizione di Azure.

    ![Impostare una configurazione del cluster predefinito](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Impostare il cluster predefinito

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-work-folder), se è stata chiusa.  

2. Selezionare il file **HelloWorld.hql** creato [in precedenza](#open-work-folder), che verrà aperto nell'editor di script.

3. Fare clic con il pulsante destro del mouse sull' **editor di script e selezionare Spark/hive: Set Default Cluster**.  

4. [Connettersi](#connect-to-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Selezionare un cluster come predefinito per il file di script corrente. Gli strumenti aggiornano automaticamente il file di configurazione **.VSCode\settings.json**. 

   ![Impostare la configurazione del cluster predefinito](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Inviare query Hive interattive e script batch Hive

Con Spark & gli strumenti hive per Visual Studio Code è possibile inviare query hive interattive e script batch hive ai cluster.

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-work-folder), se è stata chiusa.  

2. Selezionare il file **HelloWorld.hql** creato [in precedenza](#open-work-folder), che verrà aperto nell'editor di script.


3. Copiare e incollare il codice seguente nel file Hive e salvarlo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Connettersi](#connect-to-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Fare clic con il pulsante destro del mouse **sull'editor di script e selezionare hive: Interattivo** per inviare la query oppure usare il tasto di scelta rapida **CTRL + ALT + I**.  Selezionare **hive: Batch** per inviare lo script oppure usare il tasto di scelta rapida **CTRL + ALT + H**.  

6. Se non è stato specificato un cluster predefinito, selezionare il cluster. Gli strumenti consentono anche di inviare un blocco di codice, anziché l'intero file di script, usando il menu di scelta rapida. Dopo alcuni istanti i risultati della query vengono visualizzati in una nuova scheda.

   ![Risultato di Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Pannello dei **RISULTATI**: è possibile salvare i risultati completi come file CSV, JSON o file EXCEL in un percorso locale oppure selezionare più righe.

    - Pannello dei **MESSAGGI**: se si seleziona il numero di **riga**, si passa alla prima riga dello script in esecuzione.

## <a name="submit-interactive-pyspark-queries"></a>Inviare query PySpark interattive

È possibile inviare query PySpark interattive attenendosi alla procedura seguente:

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-work-folder), se è stata chiusa.  

2. Creare un nuovo file **HelloWorld.py** seguendo i passaggi illustrati [in precedenza](#open-work-folder).

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

4. [Connettersi](#connect-to-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Scegliere tutto il codice e fare clic con il pulsante destro del mouse **sull'editor di script e selezionare Spark: PySpark Interactive** per inviare la query oppure usare la combinazione di tasti **CTRL+ALT+I**.

   ![menu di scelta rapida interattivo pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Se non è stato specificato un cluster predefinito, selezionare il cluster. Dopo alcuni istanti, i risultati di **Python Interactive** vengono visualizzati in una nuova scheda. Gli strumenti consentono anche di inviare un blocco di codice, anziché l'intero file di script, usando il menu di scelta rapida. 

   ![finestra interattiva di Python interattiva di pyspark](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Immettere **"%% info"** , quindi premere **MAIUSC + INVIO** per visualizzare le informazioni sul processo. (Facoltativo)

   ![Visualizza informazioni sui processi](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Lo strumento supporta anche la query **SQL Spark** .

   ![Risultato della visualizzazione interattiva di Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Lo stato dell'invio viene visualizzato a sinistra della barra di stato inferiore durante l'esecuzione delle query. Non inviare altre query se lo stato è impostato su **PySpark Kernel (busy)** (Kernel PySpark - Occupato).  

   > [!NOTE] 
   >
   > Quando l' **estensione Python abilitata** è deselezionata nelle impostazioni (l'impostazione predefinita è selezionata), i risultati dell'interazione pyspark inviati utilizzeranno la finestra precedente.
   >
   > ![estensione Python interattiva di pyspark disabilitata](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Inviare un processo batch PySpark

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-work-folder), se è stata chiusa.  

2. Creare un nuovo file **BatchFile.py** seguendo i passaggi descritti [in precedenza](#open-work-folder).

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

4. [Connettersi](#connect-to-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Fare clic con il pulsante destro del mouse sull'editor **di script e quindi selezionare Spark: PySpark Batch**, oppure usare la combinazione di tasti **Ctrl + Alt + H**. 

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

1. [Connettersi](#connect-to-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

2. Dalla barra dei menu passare a **Visualizza** > **Explorer**.

3. Nel riquadro sinistro espandere **AZURE HDINSIGHT**.  Verranno elencati i cluster e le sottoscrizioni disponibili (sono supportati Spark, Hadoop e HBase). 

   ![Sottoscrizione di Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Espandere il cluster per visualizzare lo schema di database e tabella dei metadati hive.

   ![Cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Anteprima tabella hive
È possibile visualizzare l'anteprima della tabella hive nei cluster direttamente tramite **Azure HDInsight** Explorer.
1. [Connettersi](#connect-to-azure-account) all'account Azure, se ancora non è stato fatto.

2. Fare clic sull'icona di **Azure** dalla colonna più a sinistra.

3. Nel riquadro sinistro espandere **AZURE HDINSIGHT**. Verranno elencate le sottoscrizioni e i cluster disponibili.

4. Espandere il cluster per visualizzare lo schema di database e tabella dei metadati hive.

5. Fare clic con il pulsante destro del mouse sulla tabella hive, ad esempio hivesampletable. Selezionare **Anteprima**. 

   ![Spark & hive per Visual Studio Code Anteprima tabella hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Verrà aperta la finestra **Anteprima risultati** .

   ![Finestra risultati anteprima di Spark & hive per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- Pannello **risultati**

   è possibile salvare i risultati completi come file CSV, JSON o file EXCEL in un percorso locale oppure selezionare più righe.

- Pannello **messaggi**
   1. Quando il numero di righe nella tabella è maggiore di 100 righe, viene visualizzato il messaggio seguente: **Per la tabella hive vengono visualizzate le prime 100 righe**.
   2. Quando il numero di righe nella tabella è minore o uguale a 100 righe, viene visualizzato il messaggio seguente: **60 righe visualizzate per la tabella hive**.
   3. Se nella tabella non è presente alcun contenuto, viene visualizzato il messaggio seguente: **per la tabella hive viene visualizzata la riga 0**.

>[!NOTE]
>
>In Linux installare XRITAGLIA per abilitare la copia dei dati della tabella.
>
>![Spark & hive per Visual Studio Code in Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Funzionalità aggiuntive

Spark & hive per Visual Studio Code supporta le funzionalità seguenti:

- **Completamento automatico di IntelliSense**. Vengono visualizzati suggerimenti per parole chiave, metodi, variabili e così via. Icone diverse rappresentano vari tipi di oggetti.

    ![Spark & gli strumenti hive per Visual Studio Code tipi di oggetti IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Indicatore di errore di IntelliSense**. Il servizio di linguaggio sottolinea gli errori di modifica nello script Hive.     
- **Evidenziazioni della sintassi**. Il servizio di linguaggio usa colori diversi per differenziare variabili, parole chiave, tipi di dati, funzioni e così via. 

    ![Highlights Spark & hive Tools for Visual Studio Code Syntax](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Ruolo solo lettore

Gli utenti con   **ruolo** solo lettore cluster non possono più inviare il processo al cluster HDInsight né visualizzare il database hive. Contattare l'amministratore del cluster per aggiornare il ruolo all' [ **operatore** **cluster** **HDInsight** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) nel [portale di Azure](https://ms.portal.azure.com/). Se si conoscono le credenziali di Ambari, è possibile collegare manualmente il cluster seguendo le istruzioni riportate di seguito.

### <a name="browse-hdinsight-cluster"></a>Sfoglia cluster HDInsight  

Quando si fa clic su Azure HDInsight Explorer per espandere un cluster HDInsight, verrà richiesto di collegare il cluster se si usa il ruolo di lettore solo per il cluster. Attenersi alla procedura seguente per eseguire il collegamento al cluster tramite le credenziali di Ambari. 

### <a name="submit-job-to-hdinsight-cluster"></a>Inviare un processo al cluster HDInsight

Quando si invia un processo a un cluster HDInsight, verrà richiesto di collegare il cluster se si usa il ruolo di lettore solo per il cluster. Attenersi alla procedura seguente per eseguire il collegamento al cluster tramite le credenziali di Ambari. 

### <a name="link-to-cluster"></a>Collega a cluster

1.  Immettere il nome utente Ambari 
2.  Immettere la password utente di Ambari.

   ![Spark & gli strumenti hive per Visual Studio Code nome utente](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & gli strumenti hive per Visual Studio Code password](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>È possibile usare Spark/hive: Elencare il cluster per controllare il cluster collegato.
>
>![Strumenti di Spark & hive per Visual Studio Code Reader collegati](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>Sfogliare un account ADLS Gen2

Quando si fa clic su Azure HDInsight Explorer per espandere un account ADLS Gen2, verrà richiesto di immettere la chiave di **accesso** alle risorse di archiviazione se l'account Azure non ha accesso all'archiviazione Gen2. L'account ADLS Gen2 verrà espanso automaticamente dopo la convalida corretta della chiave di accesso. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Inviare processi al cluster HDInsight con ADLS Gen2

Quando si invia un processo a un cluster HDInsight con ADLS Gen2, verrà richiesto di immettere la **chiave di accesso** alle archiviazione se l'account Azure non ha accesso in scrittura alla risorsa di archiviazione Gen2.  Il processo verrà inviato correttamente dopo la convalida corretta della chiave di accesso. 

![Spark & strumenti hive per Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>È possibile ottenere la chiave di accesso per l'account di archiviazione dal portale di Azure. Per informazioni, vedere [visualizzare e copiare le chiavi di accesso](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Scollegare il cluster

1. Dalla barra dei menu passare a **Visualizza** > **riquadro comandi...** , quindi immettere **Spark/hive: Unlink a Cluster**.  

2. Selezionare il cluster da scollegare.  

3. Esaminare la vista **OUTPUT** per verificare i dati.  

## <a name="sign-out"></a>Esci  

Dalla barra dei menu passare a **Visualizza** > **riquadro comandi...** e quindi immettere **Azure: Disconnettersi**.


## <a name="next-steps"></a>Passaggi successivi
Per un video dimostrativo sull'uso di Spark & hive per Visual Studio Code, vedere [spark & hive per Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
