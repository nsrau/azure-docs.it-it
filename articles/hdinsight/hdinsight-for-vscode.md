---
title: Strumenti di Azure HDInsight - Usare Visual Studio Code per Hive, LLAP o PySpark | Microsoft Docs
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
Keywords: Visual Studio Code, gli strumenti di Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 04e607517bc806b60d7e76e5076f9d3518e530eb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098338"
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

3. Assegnare al nuovo file l'estensione hql (query Hive) o py (script Spark).  Questo esempio usa **HelloWorld.hql**.

## <a name="connect-to-hdinsight-cluster"></a>Connettersi al cluster HDInsight

Prima di inviare script ai cluster HDInsight da Visual Studio Code, è necessario connettersi all'account Azure oppure collegare un cluster, usando un nome utente e una password Ambari o un account aggiunto al dominio.  Per connettersi ad Azure, seguire questa procedura:

1. Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Login**.

    ![Accesso agli strumenti HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Seguire le istruzioni di accesso nel riquadro **OUTPUT**.
    + Per l'ambiente globale di Azure, **HDInsight: Il comando di accesso** attiverà l'azione **Accedere ad Azure** nella finestra di esplorazione di HDInsight e viceversa.

        ![Istruzioni di accesso ad Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Per altri ambienti, seguire le istruzioni di accesso.

        ![Istruzioni di accesso per altri ambienti](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   Dopo essersi connessi, il nome dell'account Azure viene visualizzato sulla barra di stato nella parte inferiore sinistra della finestra di Visual Studio Code.  
  

<h2 id="linkcluster">Creare un collegamento: HDInsight di Azure</h2>

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


## <a name="create-link-generic-livy-endpoint"></a>Creare un collegamento: endpoint Livy generico

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

3. [Connettersi](#connect-to-hdinsight-cluster) all'account Azure, se ancora non è stato fatto.

4. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **HDInsight: Set Default Cluster**.  

5. Selezionare un cluster come predefinito per il file di script corrente. Gli strumenti aggiornano automaticamente il file di configurazione **.VSCode\settings.json**. 

   ![Impostare la configurazione del cluster predefinito](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Configurare l'ambiente di Azure

1. [Connettersi](#connect-to-hdinsight-cluster) all'account Azure, se ancora non è stato fatto.

2. Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Set Azure Environment**.

3. Selezionare un ambiente come voce di accesso predefinita.

4. Lo strumento, nel frattempo, ha già salvato la voce di accesso predefinita selezionata in **.VSCode\settings.json**. È anche possibile aggiornare direttamente la voce nel file di configurazione. 

   ![Impostare la configurazione della voce di accesso predefinita](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Inviare query Hive interattive e script batch Hive

Gli strumenti di HDInsight per Visual Studio Code consentono di inviare query Interactive Hive e script batch Hive ai cluster di HDInsight.

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-hdinsight-work-folder), se è stata chiusa.  

2. Selezionare il file **HelloWorld.hql** creato [in precedenza](#open-hdinsight-work-folder), che verrà aperto nell'editor di script.

3. [Connettersi](#connect-to-hdinsight-cluster) all'account Azure, se ancora non è stato fatto.

4. Copiare e incollare il codice seguente nel file Hive e salvarlo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. Fare clic con il pulsante destro del mouse sull'editor di script, quindi selezionare **HDInsight: Hive Interactive** per inviare la query, oppure usare la combinazione di tasti **Ctrl + Alt + I**.  Selezionare **HDInsight: Hive Batch** per inviare lo script, oppure usare la combinazione di tasti **Ctrl + Alt + H**.  

6. Se non è stato specificato un cluster predefinito, selezionare il cluster. Gli strumenti consentono anche di inviare un blocco di codice, anziché l'intero file di script, usando il menu di scelta rapida. Dopo alcuni istanti i risultati della query vengono visualizzati in una nuova scheda.

   ![Risultato di Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Pannello dei **RISULTATI**: è possibile salvare i risultati completi come file CSV, JSON o file EXCEL in un percorso locale oppure selezionare più righe.

    - Pannello dei **MESSAGGI**: se si seleziona il numero di **riga**, si passa alla prima riga dello script in esecuzione.

## <a name="submit-interactive-pyspark-queries"></a>Inviare query PySpark interattive

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-hdinsight-work-folder), se è stata chiusa.  

2. Creare un nuovo file **HelloWorld.py** seguendo i passaggi illustrati [in precedenza](#open-hdinsight-work-folder).

3. Verrà visualizzata una finestra di dialogo che consiglia l'estensione Python, se non è stata ancora eseguita l'installazione di Python indicata nei prerequisiti.  Installare e ricaricare Visual Studio Code per completare l'installazione.

    >![HDInsight per Visual Studio Code - Installazione di Python](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [Connettersi](#connect-to-hdinsight-cluster) all'account Azure, se ancora non è stato fatto.

5. Copiare e incollare il codice seguente nel file di script:
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

6. Fare clic con il pulsante destro del mouse sull'editor di script, quindi selezionare **HDInsight: PySpark Interactive** per inviare la query oppure usare la combinazione di tasti **CTRL+ALT+I**.  

7. Se non è stato specificato un cluster predefinito, selezionare il cluster. Gli strumenti consentono anche di inviare un blocco di codice, anziché l'intero file di script, usando il menu di scelta rapida. Dopo alcuni istanti i risultati della query vengono visualizzati in una nuova scheda.

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. Lo strumento supporta anche l'esecuzione di query sulla **clausola SQL**.

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) Lo stato dell'invio viene visualizzato a sinistra della barra di stato inferiore durante l'esecuzione delle query. Non inviare altre query se lo stato è impostato su **PySpark Kernel (busy)** (Kernel PySpark - Occupato).  

>[!NOTE]  
>I cluster possono gestire le informazioni contenute nella sessione, come la variabile definita, la funzione e i valori corrispondenti, in modo che sia possibile farvi riferimento da più chiamate di servizio relative allo stesso cluster. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark 3 non è supportato con Spark 2.2/2.3

PySpark 3 non è più supportato con cluster Spark 2.2 e cluster Spark 2.3, è supportato solo "PySpark" per Python. Il fatto che l'invio a spark 2.2/2.3 abbia esito negativo con Python 3 è un problema noto.

   ![L'invio a python 3 ha esito negativo](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Seguire i passaggi per usare Python 2.x: 

1. Installare Python 2.7 sul computer locale e aggiungerlo al percorso di sistema.

2. Riavviare Visual Studio Code.

3. Passare a Python 2 facendo clic su **Python XXX** sulla barra di stato, quindi scegliere il Python di destinazione.

   ![Selezionare la versione di Python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>Inviare un processo batch PySpark

1. Riaprire la cartella **HDexample** creata [in precedenza](#open-hdinsight-work-folder), se è stata chiusa.  

2. Creare un nuovo file **BatchFile.py** seguendo i passaggi descritti [in precedenza](#open-hdinsight-work-folder).

3. [Connettersi](#connect-to-hdinsight-cluster) all'account Azure, se ancora non è stato fatto.

4. Copiare e incollare il codice seguente nel file di script:

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

4. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: PySpark Batch**, oppure usare la combinazione di tasti **Ctrl + Alt + H**. 

5. Selezionare un cluster a cui inviare il processo PySpark. 

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
    | file JAR | file JAR da usare in questa sessione | Elenco di stringhe | 
    | pyFiles | File di Python da usare in questa sessione | Elenco di stringhe |
    | input | file da usare in questa sessione | Elenco di stringhe |
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
    | state |   Stato batch | string |

>[!NOTE]
>La configurazione di livy assegnata verrà visualizzata nel riquadro di output quando si invia lo script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Eseguire l'integrazione con Azure HDInsight da Explorer

**Azure HDInsight** è stato aggiunto alla vista di Explorer. È possibile esplorare e gestire i cluster direttamente tramite **Azure HDInsight**.

1. [Connettersi](#connect-to-hdinsight-cluster) all'account Azure, se ancora non è stato fatto.

2. Dalla barra dei menu passare a **Visualizza** > **Explorer**.

3. Nel riquadro sinistro espandere **AZURE HDINSIGHT**.  Verranno elencati i cluster e le sottoscrizioni disponibili (sono supportati Spark, Hadoop e HBase). 

   ![Sottoscrizione di Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Espandere il cluster per visualizzare lo schema di database e tabella dei metadati hive.

   ![Cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>Funzionalità aggiuntive

HDInsight per Visual Studio Code supporta le funzionalità seguenti:

- **Completamento automatico di IntelliSense**. Vengono visualizzati suggerimenti per parole chiave, metodi, variabili e così via. Icone diverse rappresentano vari tipi di oggetti.

    ![Tipi di oggetto IntelliSense degli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Indicatore di errore di IntelliSense**. Il servizio di linguaggio sottolinea gli errori di modifica nello script Hive.     
- **Evidenziazioni della sintassi**. Il servizio di linguaggio usa colori diversi per differenziare variabili, parole chiave, tipi di dati, funzioni e così via. 

    ![Evidenziazioni della sintassi degli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>Scollegare il cluster

1. Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Unlink a Cluster**.  

2. Selezionare il cluster da scollegare.  

3. Esaminare la vista **OUTPUT** per verificare i dati.  


## <a name="logout"></a>Disconnessione  

Dalla barra dei menu passare a **Visualizza** > **Riquadro comandi** e immettere **HDInsight: Logout** (ADL: Disconnetti).  Verrà visualizzata una finestra popup nell'angolo in basso a destra con il messaggio **Logout successfully!** (Disconnessione eseguita).


## <a name="next-steps"></a>Passaggi successivi
Per un video che illustra l'uso di HDInsight per Visual Studio Code, vedere [HDInsight for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706) (HDInsight per Visual Studio Code)
