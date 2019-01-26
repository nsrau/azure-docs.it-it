---
title: Strumenti di Azure HDInsight - Usare Visual Studio Code per Hive, LLAP o PySpark | Microsoft Docs
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/15/2018
ms.openlocfilehash: cd7c4014752fb5fa014fd8b5204206cd4efbfdce
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818524"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Usare gli strumenti di Azure HDInsight per Visual Studio Code

Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code (VS Code) per creare e inviare processi batch Apache Hive, query Hive interattive e script PySpark per Apache Spark. Prima di tutto, verrà illustrato come installare gli strumenti HDInsight in VS Code, quindi si mostrerà come inviare processi Hive e Spark. 

Gli strumenti di Azure HDInsight possono essere installati sulle piattaforme supportate da VSCode, tra cui Windows, Linux e MacOS. Di seguito sono elencati i prerequisiti specifici per le diverse piattaforme.


## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure descritte in questo articolo, sono necessari gli elementi seguenti:

- Un cluster HDInsight. Per creare un cluster, vedere [Introduzione a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono è necessario solo per Linux e macOS.

## <a name="install-the-hdinsight-tools"></a>Installare gli strumenti di HDInsight
   
Dopo avere installato i prerequisiti, è possibile installare gli strumenti di Azure HDInsight per VS Code. 

### <a name="to-install-azure-hdinsight-tools"></a>Per installare gli strumenti di Azure HDInsight

1. Aprire Visual Studio Code.

2. Nel riquadro sinistro selezionare **Estensioni**. Nella casella di ricerca immettere **HDInsight**.

3. Accanto a **Strumenti di Azure HDInsight** selezionare **Installa**. Dopo alcuni secondi il pulsante **Installa** diventa **Ricarica**.

4. Selezionare **Ricarica** per attivare l'estensione degli **strumenti di Azure HDInsight**.

5. Selezionare **Ricarica finestra** per confermare. Gli **strumenti di Azure HDInsight** vengono visualizzati nel riquadro **Estensioni**.

   ![HDInsight per Visual Studio Code - Installazione di Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-work-folder"></a>Aprire la cartella di lavoro HDInsight

Per potersi connettere ad Azure, è prima necessario creare una cartella di lavoro in VS Code.

### <a name="to-open-a-work-folder"></a>Per aprire una cartella di lavoro

1. Nel menu **File** selezionare **Apri cartella**. Specificare quindi una cartella esistente come cartella di lavoro oppure crearne una nuova. La cartella viene visualizzata nel riquadro sinistro.

2. Nel riquadro sinistro selezionare l'icona **Nuovo file** accanto alla cartella di lavoro.

   ![Nuovo file](./media/hdinsight-for-vscode/new-file.png)

3. Assegnare al nuovo file l'estensione hql (query Hive) o py (script Spark). 

## <a name="connect-to-hdinsight-cluster"></a>Connettersi al cluster HDInsight

Prima di inviare script ai cluster HDInsight da Visual Studio Code, è necessario connettersi al proprio account Azure oppure collegare un cluster, usando un nome utente e una password Ambari o un account aggiunto al dominio.

### <a name="to-connect-to-azure"></a>Per connettersi ad Azure

1. Creare una nuova cartella di lavoro e un nuovo file di script, se non sono già disponibili.

2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi scegliere **HDInsight: Login**. È anche possibile premere **CTRL+MAIUSC+P** e quindi immettere **HDInsight: Login**.

    ![Accesso agli strumenti HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Per accedere, seguire le istruzioni di accesso nel riquadro **OUTPUT**.
    + Per l'ambiente globale di Azure, **HDInsight: Il comando di accesso** attiverà l'azione **Accedere ad Azure** nella finestra di esplorazione di HDInsight e viceversa.

        ![Istruzioni di accesso ad Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Per altri ambienti, seguire le istruzioni di accesso.

        ![Istruzioni di accesso per altri ambienti](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

    Dopo essersi connessi, il nome dell'account di Azure viene visualizzato sulla barra di stato nella parte inferiore sinistra della finestra di VS Code. 

    > [!NOTE]  
    > A causa di un problema di autenticazione di Azure noto, è necessario aprire un browser in modalità privata o in incognito. Se per l'account è abilitata l'autenticazione a due fattori, è consigliabile usare l'autenticazione tramite telefono anziché mediante codice PIN.
  

4. Fare clic con il pulsante destro del mouse sull'editor di script per visualizzare il menu di scelta rapida. Da questo menu di scelta rapida è possibile eseguire le attività seguenti:

    - Effettuare la disconnessione
    - Elencare cluster
    - Impostare cluster predefiniti
    - Inviare query Interactive Hive
    - Inviare script batch Hive
    - Inviare query PySpark interattive
    - Inviare script batch PySpark
    - Impostare la configurazione

<h3 id="linkcluster">Per collegare un cluster</h3>

È possibile collegare un cluster normale usando un nome utente gestito [Apache Ambari](https://ambari.apache.org/) o collegare un cluster Hadoop con Enterprise Security Pack usando un nome utente di dominio (ad esempio: user1@contoso.com).
1. Aprire il riquadro comandi premendo **CTRL+MAIUSC+P**, quindi immettere **HDInsight: Link a Cluster**.

   ![comando di collegamento di un cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Immettere l'URL del cluster HDInsight -> immettere il nome utente -> immettere la password -> selezionare il tipo di cluster -> se la verifica viene superata, vengono visualizzate le informazioni sull'esito positivo.
   
   ![finestra di dialogo di collegamento di un cluster](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]  
   > Vengono usati il nome utente e la password collegati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster. 
   
3. È possibile visualizzare un cluster collegato usando il comando **Elenca cluster**. È ora possibile inviare uno script al cluster collegato.

   ![Cluster collegato](./media/hdinsight-for-vscode/linked-cluster.png)

4. È anche possibile scollegare un cluster immettendo **HDInsight: Unlink a Cluster** dal riquadro comandi.


### <a name="to-link-a-generic-apache-livy-endpoint"></a>Per collegare un endpoint Apache Livy generico

1. Aprire il riquadro comandi premendo **CTRL+MAIUSC+P**, quindi immettere **HDInsight: Link a Cluster**.
2. Selezionare**Endpoint livy generico**.
3. Immettere l'endpoint Livy generico, ad esempio: http\://10.172.41.42:18080.
4. Selezionare **Base** quando è richiesta un'autorizzazione per l'endpoint Livy generico, in caso contrario, selezionare **Nessuna**.
5. Immettere il nome utente quando si seleziona **Base** nel passaggio 4.
6. Immettere la password quando si seleziona **Base** nel passaggio 4.
7. Endpoint livy generico collegato correttamente.

   ![cluster livy generico collegato](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>Elencare i cluster HDInsight

Per testare la connessione, è possibile elencare i cluster HDInsight:

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>Per elencare i cluster HDInsight nella sottoscrizione di Azure
1. Aprire una cartella di lavoro, quindi connettersi ad Azure. Per altre informazioni, vedere [Aprire cartella di lavoro HDInsight](#open-hdinsight-work-folder) e [Connettersi ad Azure](#connect-to-hdinsight-cluster).

2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: List Cluster** dal menu di scelta rapida. 

3. I cluster HDInsight verranno visualizzati nel riquadro **Output**.

    ![Impostare una configurazione del cluster predefinito](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Impostare un cluster predefinito
1. Aprire una cartella di lavoro, quindi connettersi ad Azure. Vedere [Aprire cartella di lavoro HDInsight](#open-hdinsight-work-folder) e [Connettersi ad Azure](#connect-to-hdinsight-cluster).

2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: Set Default Cluster**. 

3. Selezionare un cluster come predefinito per il file di script corrente. Gli strumenti aggiornano automaticamente il file di configurazione **.VSCode\settings.json**. 

   ![Impostare la configurazione del cluster predefinito](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Configurare l'ambiente di Azure
1. Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**.

2. Immettere **HDInsight: Set Azure Environment**.

3. Selezionare un ambiente, ad esempio "Azure" o "AzureChina" come voce di accesso predefinita.

4. Lo strumento, nel frattempo, ha già salvato la voce di accesso predefinita selezionata in **.VSCode\settings.json**. Aggiornare direttamente la voce anche nel file di configurazione. 

   ![Impostare la configurazione della voce di accesso predefinita](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Inviare query Hive interattive e script batch Hive

Gli strumenti HDInsight per VS Code consentono di inviare query Hive interattive e script batch Hive ai cluster di HDInsight.

1. Creare una nuova cartella di lavoro e un nuovo file di script Hive, se non sono già disponibili.

2. Connettersi all'account di Azure o collegare i cluster.

3. Copiare e incollare il codice seguente nel file Hive e salvarlo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Fare clic con il pulsante destro del mouse sull'editor di script, quindi selezionare **HDInsight: Hive Interactive** per inviare la query, oppure usare la combinazione di tasti **Ctrl + Alt + I**. Selezionare **HDInsight: Hive Batch** per inviare lo script, oppure usare la combinazione di tasti **Ctrl + Alt + H**. 

5. Se non è stato specificato un cluster predefinito, selezionare il cluster. Gli strumenti consentono anche di inviare un blocco di codice, anziché l'intero file di script, usando il menu di scelta rapida. Dopo alcuni istanti i risultati della query vengono visualizzati in una nuova scheda.

   ![Risultato di Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Pannello dei **RISULTATI**: è possibile salvare i risultati completi come file CSV, JSON o file EXCEL in un percorso locale oppure selezionare più righe.

    - Pannello dei **MESSAGGI**: se si seleziona il numero di **riga**, si passa alla prima riga dello script in esecuzione.

## <a name="submit-interactive-pyspark-queries"></a>Inviare query PySpark interattive

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>Per inviare query PySpark interattive ai cluster HDInsight Spark.

1. Creare una nuova cartella di lavoro e un nuovo file .py, se non sono già disponibili.

    > [!NOTE]
    > VSCode consiglia l'installazione dell'estensione di Python per il file con estensione py. È possibile installare l'estensione oppure chiudere la finestra di dialogo.
    > 
    >![HDInsight per Visual Studio Code - Installazione di Python](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

2. Connettersi all'account Azure personale, se non si è già connessi.

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
4. Installare l'ambiente di Python se non è ancora stato fatto. Vedere [Configurare l'ambiente PySpark Interactive per Visual Studio Code](set-up-pyspark-interactive-environment.md).

5. Evidenziare lo script. Fare clic con il pulsante destro del mouse sull'editor di script, quindi selezionare **HDInsight: PySpark Interactive**, oppure usare la combinazione di tasti **Ctrl + Alt + I**.

6. Selezionare un cluster a cui inviare la query PySpark. Il risultato della query viene visualizzato subito dopo in una nuova scheda a destra:

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
7. Lo strumento supporta anche l'esecuzione di query sulla **clausola SQL**.

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) Lo stato dell'invio viene visualizzato a sinistra della barra di stato inferiore durante l'esecuzione delle query. Non inviare altre query se lo stato è impostato su **PySpark Kernel (busy)** (Kernel PySpark - Occupato). 

>[!NOTE]  
>I cluster possono gestire le informazioni contenute nella sessione, come la variabile definita, la funzione e i valori corrispondenti, in modo che sia possibile farvi riferimento da più chiamate di servizio relative allo stesso cluster. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark 3 non è supportato con Spark 2.2/2.3

PySpark 3 non è più supportato con cluster Spark 2.2 e cluster Spark 2.3, è supportato solo "PySpark" per Python. Il fatto che l'invio a spark 2.2/2.3 abbia esito negativo con Python 3 è un problema noto.

   ![L'invio a python 3 ha esito negativo](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Seguire i passaggi per usare Python 2.x: 

1. Installare Python 2.7 sul computer locale e aggiungerlo al percorso di sistema.

2. Riavviare VSCode.

3. Passare a Python 2 facendo clic su **Python XXX** sulla barra di stato, quindi scegliere il Python di destinazione.

   ![Selezionare la versione di Python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

## <a name="submit-pyspark-batch-job"></a>Inviare un processo batch PySpark

1. Creare una nuova cartella di lavoro e un nuovo file di script con estensione py, se non sono già disponibili.

2. Connettersi all'account Azure personale, se non si è già connessi.

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
4. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: PySpark Batch**, oppure usare la combinazione di tasti **Ctrl + Alt + H**. 

5. Selezionare un cluster a cui inviare il processo PySpark. 

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Dopo aver inviato un processo Python, i log di invio vengono visualizzati nella finestra **Output** in VS Code. Vengono visualizzati anche l'**URL dell'interfaccia utente di Spark** e l'**URL dell'interfaccia utente di Yarn**. È possibile aprire l'URL in un Web browser per tenere traccia dello stato del processo.

## <a name="apache-livy-configuration"></a>Configurazione di Apache Livy

La configurazione di [Apache Livy](https://livy.incubator.apache.org/) è supportata ed è possibile impostarla in **.VSCode\settings.json** nella cartella dell'area di lavoro. Attualmente la configurazione di livy supporta solo lo script Python. Per altre informazioni, vedere [Livy LEGGIMI](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Come attivare la configurazione di livy**
   
È disponibile nel menu **File**, selezionare **Preferenze** e scegliere **Impostazioni** nel menu di scelta rapida. Fare clic sulla scheda **IMPOSTAZIONI AREA DI LAVORO**, è possibile iniziare a impostare la configurazione di livy.

È anche possibile inviare un file, si noti che la cartella con estensione vscode viene aggiunta automaticamente alla cartella di lavoro. È possibile trovare la configurazione di livy facendo clic su **.vscode\settings.json**.

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
    | proxyUser | Utente da rappresentare durante l'esecuzione del processo | stringa | 
    | className | Classe principale Java/Spark dell'applicazione | stringa |
    | args | Argomenti della riga di comando per l'applicazione | elenco di stringhe | 
    | file JAR | file JAR da usare in questa sessione | Elenco di stringhe | 
    | pyFiles | File di Python da usare in questa sessione | Elenco di stringhe |
    | input | file da usare in questa sessione | Elenco di stringhe |
    | driverMemory | Quantità di memoria da usare per il processo del driver | stringa |
    | driverCores | Numero di core da usare per il processo del driver | int |
    | executorMemory | Quantità di memoria da usare per ogni processo executor | stringa |
    | executorCores | Numero di core da usare per ogni executor | int |
    | numExecutors | Numero di executor da avviare per questa sessione | int |
    | archives | Archivi da usare in questa sessione | Elenco di stringhe |
    | coda | Nome della coda a cui YARN viene effettuato l'invio | stringa |
    | name | Nome della sessione | stringa |
    | conf | Proprietà di configurazione di Spark | Mapping della chiave = val |

    Contenuto risposta   
    Oggetto batch creato.

    | name | description | type | 
    | :- | :- | :- | 
    | id | ID sessione | int | 
    | appId | ID applicazione della sessione |  string |
    | appInfo | Informazioni dettagliate sull'applicazione | Mapping della chiave = val |
    | log | Righe di log | elenco di stringhe |
    | state |   Stato batch | stringa |

>[!NOTE]
>La configurazione di livy assegnata verrà visualizzata nel riquadro di output quando si invia lo script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Eseguire l'integrazione con Azure HDInsight da Explorer

Azure HDInsight è stato aggiunto al pannello di sinistra. È possibile esplorare e gestire direttamente il cluster.

1. Espandere **AZURE HDINSIGHT**, se l'accesso non è stato eseguito, verrà visualizzato il collegamento **Accedi ad Azure...** .

    ![Immagine del collegamento di accesso](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. Fare clic su **Accedi ad Azure**, il collegamento e il codice di accesso vengono visualizzati nella parte inferiore destra.

    ![Istruzioni di accesso per altri ambienti](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. Fare clic sul pulsante **Copia e apri** per aprire il browser, incollare il codice, fare clic sul pulsante **Continua**, verrà visualizzato il messaggio di accesso eseguito.

4. Dopo aver effettuato l'accesso, le sottoscrizioni disponibili e i cluster (sono supportati Spark, Hadoop e HBase) sono elencati in **AZURE HDINSIGHT**. 

   ![Sottoscrizione di Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. Espandere il cluster per visualizzare lo schema di database e tabella dei metadati hive.

   ![Cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="additional-features"></a>Funzionalità aggiuntive

HDInsight per VS Code supporta le funzionalità seguenti:

- **Completamento automatico di IntelliSense**. Vengono visualizzati suggerimenti per parole chiave, metodi, variabili e così via. Icone diverse rappresentano vari tipi di oggetti.

    ![Tipi di oggetto IntelliSense degli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Indicatore di errore di IntelliSense**. Il servizio di linguaggio sottolinea gli errori di modifica nello script Hive.     
- **Evidenziazioni della sintassi**. Il servizio di linguaggio usa colori diversi per differenziare variabili, parole chiave, tipi di dati, funzioni e così via. 

    ![Evidenziazioni della sintassi degli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Passaggi successivi

### <a name="demo"></a>Demo
* HDInsight per VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Apache Zeppelin con cluster Apache Spark in HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizzare i dati Apache Hive con Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualize Interactive Query Hive data with Power BI in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight).
* [Configurare l'ambiente PySpark interattivo per Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scenari
* [Apache Spark con BI: Eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](spark/apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio con dati HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con apprendimento automatico: usare Spark in HDInsight per stimare i risultati di controllo degli alimenti](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Creazione ed esecuzione di applicazioni
* [Creare un'applicazione autonoma con Scala](spark/apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](spark/apache-spark-job-debugging.md)



