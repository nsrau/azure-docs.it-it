---
title: Strumenti di Azure HDInsight - Usare Visual Studio Code per Hive, LLAP o pySpark | Microsoft Docs
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
<<<<<<< HEAD
ms.openlocfilehash: 0e2cd75845eb3613b23409b6bf1ab7d37d992275
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
=======
ms.openlocfilehash: 4f0bcd0992a02e64b4b10347fc30776af17900bd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
>>>>>>> 31972e77aa0784c49b3de1fde20ec0a5e3007025
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Usare gli strumenti di Azure HDInsight per Visual Studio Code

Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code (VS Code) per creare e inviare processi batch Hive, query Hive interattive e script pySpark. Gli strumenti di Azure HDInsight possono essere installati sulle piattaforme supportate da VS Code, tra cui Windows, Linux e macOS. Sono previsti prerequisiti specifici per le diverse piattaforme.


## <a name="prerequisites"></a>prerequisiti

Per completare le procedure descritte in questo articolo, sono necessari gli elementi seguenti:

- Un cluster HDInsight.  Per creare un cluster, vedere [Introduzione a HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono è necessario solo per Linux e macOS.

## <a name="install-the-hdinsight-tools"></a>Installare gli strumenti di HDInsight
   
Dopo avere installato i prerequisiti, è possibile installare gli strumenti di Azure HDInsight per VS Code. 

**Per installare gli strumenti di Azure HDInsight**

1. Aprire Visual Studio Code.

2. Nel riquadro sinistro selezionare **Estensioni**. Nella casella di ricerca immettere **HDInsight**.

3. Accanto a **Strumenti di Azure HDInsight** selezionare **Installa**. Dopo alcuni secondi il pulsante **Installa** diventa **Ricarica**.

4. Selezionare **Ricarica** per attivare l'estensione degli **strumenti di Azure HDInsight**.

5. Selezionare **Ricarica finestra** per confermare. Gli **strumenti di Azure HDInsight** vengono visualizzati nel riquadro **Estensioni**.

   ![HDInsight per Visual Studio Code - Installazione di Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Aprire l'area di lavoro HDInsight

Per potersi connettere ad Azure, è prima necessario creare un'area di lavoro in VS Code.

**Per aprire un'area di lavoro**

1. Nel menu **File** selezionare **Apri cartella**. Specificare quindi una cartella esistente come cartella di lavoro oppure crearne una nuova. La cartella viene visualizzata nel riquadro sinistro.

2. Nel riquadro sinistro selezionare l'icona **Nuovo file** accanto alla cartella di lavoro.

   ![Nuovo file](./media/hdinsight-for-vscode/new-file.png)

3. Assegnare al nuovo file l'estensione hql (query Hive) o py (script Spark). Si noti che nella cartella di lavoro è stato automaticamente aggiunto il file di configurazione **XXXX_hdi_settings.json**.

4. Aprire **XXXX_hdi_settings.json** da **Esplora risorse** oppure fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Set Configuration** (Imposta configurazione). È possibile configurare la voce di accesso, il cluster predefinito e i parametri di invio dei processi, come illustrato nell'esempio nel file. È anche possibile lasciare vuoti gli altri parametri.

## <a name="connect-to-azure"></a>Connect to Azure

Per poter inviare script ai cluster HDInsight da VS Code, è prima necessario connettersi all'account di Azure personale.

**Connettersi ad Azure**

1. Creare una nuova cartella di lavoro e un nuovo file di script, se non sono già disponibili.

2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi scegliere **HDInsight: Login** (HDInsight: Accesso) dal menu di scelta rapida. È anche possibile premere **CTRL+MAIUSC+P** e quindi immettere **HDInsight: Login** (HDInsight: Accesso).

    ![Accesso agli strumenti HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Per accedere, seguire le istruzioni di accesso nel riquadro **OUTPUT**.

    **Azure:** ![Informazioni di accesso agli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

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
    - Impostare configurazioni

**Per collegare un cluster**

È possibile collegare un cluster normale usando un nome utente Ambari gestito e anche collegare un cluster Hadoop di sicurezza usando un nome utente di dominio (ad esempio: user1@contoso.com).
1. Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P** e quindi immettere **HDInsight: Link a cluster** (HDInsight: Collega un cluster).

   ![comando di collegamento di un cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Immettere l'URL del cluster HDInsight -> immettere il nome utente -> immettere la password -> selezionare il tipo di cluster -> se la verifica viene superata, vengono visualizzate le informazioni sull'esito positivo.
   
   ![finestra di dialogo di collegamento di un cluster](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Vengono usati il nome utente e la password collegati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster. 
   
3. È possibile visualizzare un cluster collegato usando il comando **List cluster** (Elenca cluster). È ora possibile inviare uno script al cluster collegato.

   ![Cluster collegato](./media/hdinsight-for-vscode/linked-cluster.png)

4. È anche possibile scollegare un cluster immettendo **HDInsight: Unlink a cluster** (HDInsight: Scollega un cluster) nel riquadro comandi.

## <a name="list-hdinsight-clusters"></a>Elencare i cluster HDInsight

Per testare la connessione, è possibile elencare i cluster HDInsight:

**Per elencare i cluster HDInsight nella sottoscrizione di Azure**
1. Aprire un'area di lavoro e quindi connettersi ad Azure. Per altre informazioni, vedere [Aprire l'area di lavoro HDInsight](#open-hdinsight-workspace) e [Connettersi ad Azure](#connect-to-azure).

2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi scegliere **HDInsight: List Cluster** (HDInsight: Elenca cluster) dal menu di scelta rapida. 

3. I cluster Hive e Spark verranno visualizzati nel riquadro **Output**.

    ![Impostare una configurazione del cluster predefinito](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Impostare un cluster predefinito
1. Aprire un'area di lavoro e connettersi ad Azure. Vedere [Aprire l'area di lavoro HDInsight](#open-hdinsight-workspace) e [Connettersi ad Azure](#connect-to-azure).

2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: Set Default Cluster** (HDInsight: Imposta cluster predefinito). 

3. Selezionare un cluster come predefinito per il file di script corrente. Gli strumenti aggiornano automaticamente il file di configurazione **XXXX_hdi_settings.json**. 

   ![Impostare la configurazione del cluster predefinito](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Configurare l'ambiente di Azure 
1. Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**.

2. Immettere **HDInsight: Set Azure Environment** (HDInsight: Imposta ambiente di Azure).

3. Scegliere la voce di accesso predefinita tra Azure e AzureChina.

4. Lo strumento, nel frattempo, ha già salvato la voce di accesso predefinita selezionata in **XXXX_hdi_settings.json**. Aggiornare direttamente la voce anche nel file di configurazione. 

   ![Impostare la configurazione della voce di accesso predefinita](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Inviare query Interactive Hive

Gli strumenti HDInsight per VS Code consentono di inviare query Hive interattive ai cluster Interactive Query di HDInsight.

1. Creare una nuova cartella di lavoro e un nuovo file di script Hive, se non sono già disponibili.

2. Connettersi all'account di Azure personale e quindi configurare il cluster predefinito, se non è ancora stato fatto.

3. Copiare e incollare il codice seguente nel file Hive e salvarlo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: Hive Interactive** (HDInsight: Script interattivo Hive) per inviare la query. Gli strumenti consentono anche di inviare un blocco di codice, anziché l'intero file di script, usando il menu di scelta rapida. Subito dopo, il risultato della query viene visualizzato in una nuova scheda.

   ![Risultato di Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Pannello dei **risultati**: è possibile salvare i risultati completi come file CSV, JSON o file EXCEL in un percorso locale oppure selezionare più righe.

    - Pannello dei **messaggi**: se si seleziona il numero di **riga**, si passa alla prima riga dello script in esecuzione.

Rispetto all'[esecuzione di un processo batch Hive](#submit-hive-batch-scripts), la query interattiva richiede molto meno tempo.

## <a name="submit-hive-batch-scripts"></a>Inviare script batch Hive

1. Creare una nuova cartella di lavoro e un nuovo file di script Hive, se non sono già disponibili.

2. Connettersi all'account di Azure personale e quindi configurare il cluster predefinito, se non è ancora stato fatto.

3. Copiare e incollare il codice seguente nel file Hive e salvarlo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: Hive Batch** (HDInsight: Script batch Hive) per inviare un processo Hive. 

4. Selezionare il cluster a cui si vuole inviare il processo.  

    Dopo avere inviato un processo Hive, le informazioni sull'esito dell'invio e sull'ID del processo vengono visualizzate nel pannello **OUTPUT**. Il processo Hive apre anche il pannello **WEB BROWSER**, che mostra lo stato e i log del processo in tempo reale.

   ![Risultato dell'invio del processo Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

L'[invio di query Hive interattive](#submit-interactive-hive-queries) richiede molto meno tempo rispetto all'invio di un processo batch.

## <a name="submit-interactive-pyspark-queries"></a>Inviare query PySpark interattive
Gli strumenti di HDInsight per VS Code consentono anche di inviare query PySpark interattive a cluster Spark.
1. Creare una nuova cartella di lavoro e un nuovo file di script con estensione py, se non sono già disponibili.

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
4. Evidenziare questi script, fare clic con il pulsante destro del mouse sull'editor di script e selezionare **HDInsight: PySpark Interactive** (HDInsight: Query interattiva PySpark).

5. Se non è ancora stata installata l'estensione **Python** in VS Code, selezionare il pulsante **Installa** come illustrato nella figura seguente:

    ![HDInsight per Visual Studio Code - Installazione di Python](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Installare l'ambiente Python nel sistema, se non è già stato installato. 
   - Per Windows, scaricare e installare [Python](https://www.python.org/downloads/). Assicurarsi quindi che `Python` e `pip` siano presenti nel percorso del sistema.

   - Per le istruzioni relative a macOS e Linux, vedere [Configurare l'ambiente PySpark interattivo per Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Selezionare un cluster a cui inviare la query PySpark. Il risultato della query viene visualizzato subito dopo in una nuova scheda a destra:

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Lo strumento supporta anche l'esecuzione di query sulla **clausola SQL**.

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) Lo stato dell'invio viene visualizzato a sinistra della barra di stato inferiore durante l'esecuzione delle query. Non inviare altre query se lo stato è impostato su **PySpark Kernel (busy)** (Kernel PySpark - Occupato). 

>[!NOTE]
>I cluster possono gestire le informazioni contenute nella sessione, come la variabile definita, la funzione e i valori corrispondenti, in modo che sia possibile farvi riferimento da più chiamate di servizio relative allo stesso cluster. 
 

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
4. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: PySpark Batch** (HDInsight: Script batch PySpark). 

5. Selezionare un cluster a cui inviare il processo PySpark. 

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Dopo aver inviato un processo Python, i log di invio vengono visualizzati nella finestra **Output** in VS Code. Vengono visualizzati anche l'**URL dell'interfaccia utente di Spark** e l'**URL dell'interfaccia utente di Yarn**. È possibile aprire l'URL in un Web browser per tenere traccia dello stato del processo.


   


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

* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark)](spark/apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizzare i dati Hive con Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualize Interactive Query Hive data with Power BI in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight).
* [Configurare l'ambiente PySpark interattivo per Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Usare Zeppelin per eseguire query Hive in Azure HDInsight](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](spark/apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Creazione ed esecuzione di applicazioni
* [Creare un'applicazione autonoma con Scala](spark/apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](spark/apache-spark-job-debugging.md)



