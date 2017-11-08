---
title: Strumenti di Azure HDInsight - Usare Visual Studio Code per Hive, LLAP o pySpark | Microsoft Docs
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
Keywords: VSCode,strumenti di Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
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
ms.openlocfilehash: 36ce117076ed5c15ddff850485d8f8912ec53caf
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>Usare gli strumenti di Azure HDInsight per Visual Studio Code

Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code (VSCode) per creare e inviare processi batch Hive, query Interactive Hive e script pySpark. Gli strumenti di Azure HDInsight possono essere installati sulle piattaforme supportate da VSCode, tra cui Windows, Linux e MacOS. Sono previsti prerequisiti specifici per le diverse piattaforme.


## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, sono necessari gli elementi seguenti:

- Un cluster HDInsight.  Per creare un cluster, vedere [Introduzione a HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono è necessario solo per Linux e MacOS.

## <a name="install-the-hdinsight-tools"></a>Installare gli strumenti di HDInsight
   
Dopo avere installato i prerequisiti, è possibile installare gli strumenti di Azure HDInsight per VSCode. 

**Per installare gli strumenti di Azure HDInsight**

1. Aprire **Visual Studio Code**.
2. Fare clic su **Estensioni** nel riquadro sinistro. Immettere **HDInsight** nella casella di ricerca.
3. Fare clic su **Installa** accanto a **Strumenti di Azure HDInsight**. Dopo alcuni secondi, il pulsante **Installa** verrà sostituito dal pulsante **Ricarica**.
4. Fare clic su **Ricarica** per attivare l'estensione degli **strumenti di Azure HDInsight**.
5. Fare clic su **Ricarica finestra** per confermare. Gli **strumenti di Azure HDInsight** saranno presenti nel riquadro Estensioni.

   ![HDInsight per Visual Studio Code - Installazione di Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Aprire l'area di lavoro HDInsight

Per potersi connettere ad Azure, è prima necessario creare un'area di lavoro in VSCode.

**Per aprire un'area di lavoro**

1. Scegliere **Apri cartella** dal menu **File** e specificare una cartella esistente oppure creare una nuova cartella da usare come cartella di lavoro. La cartella viene visualizzata nel riquadro sinistro.
2. Nel riquadro sinistro fare clic sull'icona **Nuovo file** accanto alla cartella di lavoro.

   ![Nuovo file](./media/hdinsight-for-vscode/new-file.png)
3. Assegnare al nuovo file l'estensione hql (query Hive) o py (script Spark). Si noti che nella cartella di lavoro viene aggiunto automaticamente un file di configurazione **XXXX_hdi_settings.json**.
4. Aprire **XXXX_hdi_settings.json** da **EXPLORER** oppure fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Set Configuration** (Imposta configurazione). È possibile configurare la voce di accesso, il cluster predefinito e i parametri di invio dei processi, come illustrato nell'esempio nel file. È anche possibile lasciare vuoti gli altri parametri.

## <a name="connect-to-azure"></a>Connect to Azure

Per poter inviare script ai cluster HDInsight da VSCode, è necessario connettersi al proprio account di Azure.

**Connettersi ad Azure**

1. Creare una nuova cartella di lavoro e un nuovo file di script, se non sono già disponibili.
2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi scegliere **HDInsight: Login** (HDInsight: Accesso) dal menu di scelta rapida. È anche possibile premere **CTRL+MAIUSC+P** e immettere **HDInsight: Login** (HDInsight: Accesso).

    ![Accesso agli strumenti HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. Seguire le istruzioni di accesso nel riquadro **OUTPUT** per accedere.

    **Azure:** ![Informazioni di accesso agli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Dopo essersi connessi, il nome dell'account di Azure viene visualizzato sulla barra di stato nella parte inferiore della finestra di VSCode. 

    > [!NOTE]
    > Aprire il browser in modalità privata o in incognito a causa di un problema di autenticazione di Azure noto. Se per l'account è abilitata l'autenticazione a due fattori, è consigliabile usare l'autenticazione tramite telefono invece del PIN.
  

4. Fare clic con il pulsante destro del mouse sull'editor di script per visualizzare il menu di scelta rapida. Da questo menu di scelta rapida è possibile eseguire le attività seguenti:

    - logout
    - Elencare i cluster
    - Impostare il cluster predefinito
    - Inviare query Interactive Hive
    - Inviare script batch Hive
    - Inviare query PySpark interattive
    - Inviare uno script batch PySpark
    - Impostare la configurazione

## <a name="list-hdinsight-clusters"></a>Elencare i cluster HDInsight

Per testare la connessione, è possibile elencare i cluster HDInsight:

**Per elencare i cluster HDInsight nella sottoscrizione di Azure**
1. Aprire un'area di lavoro e connettersi ad Azure. Vedere [Aprire l'area di lavoro HDInsight](#open-hdinsight-workspace) e [Connettersi ad Azure](#connect-to-azure).
2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi scegliere **HDInsight: List Cluster** (HDInsight: Elenca cluster) dal menu di scelta rapida. 
3. I cluster Hive e Spark verranno visualizzati nel riquadro **Output**.

    ![Impostare la configurazione del cluster predefinito](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Impostare il cluster predefinito
1. Aprire un'area di lavoro e connettersi ad Azure. Vedere [Aprire l'area di lavoro HDInsight](#open-hdinsight-workspace) e [Connettersi ad Azure](#connect-to-azure).
2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi scegliere **HDInsight: Set Default Cluster** (HDInsight: Imposta cluster predefinito). 
3. Selezionare un cluster come predefinito per il file di script corrente. Gli strumenti aggiornano automaticamente il file di configurazione, **XXXX_hdi_settings.json**. 

   ![Impostare la configurazione del cluster predefinito](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>Impostare l'ambiente di Azure 
1. Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**.
2. Immettere **HDInsight: Set Azure Environment** (HDInsight: Imposta ambiente di Azure).
3. Scegliere la voce di accesso predefinita tra Azure e AzureChina.
4. Lo strumento nel frattempo ha già salvato la voce di accesso predefinita selezionata in **XXXX_hdi_settings.json**. Aggiornare direttamente la voce anche nel file di configurazione. 

   ![Impostare la configurazione della voce di accesso predefinita](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Inviare query Interactive Hive

Gli strumenti HDInsight per VSCode consentono di inviare query Interactive Hive ai cluster Interactive Query HDInsight.

1. Creare una nuova cartella di lavoro e un nuovo file di script Hive, se non sono già disponibili.
2. Connettersi al proprio account di Azure e quindi configurare il cluster predefinito, se non è ancora stato fatto.
3. Copiare e incollare il codice seguente nel file Hive e quindi salvare.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Fare clic con il pulsante destro del mouse sull'editor di script e quindi scegliere **HDInsight: Hive Interactive** (HDInsight: Script interattivo Hive) per inviare la query. Gli strumenti consentono anche di inviare un blocco di codice invece dell'intero file di script usando il menu di scelta rapida. Subito dopo, il risultato della query viene visualizzato in una nuova scheda:

   ![Risultato di Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Pannello dei **risultati**: è possibile salvare i risultati completi come file CSV, JSON o EXCEL in un percorso locale oppure selezionare più righe.
    - Pannello dei **messaggi**: facendo clic sul numero di **riga**, si passa alla prima riga dello script in esecuzione.

Rispetto all'[esecuzione di un processo batch Hive](#submit-hive-batch-scripts), la query interattiva richiede molto meno tempo.

## <a name="submit-hive-batch-scripts"></a>Inviare script batch Hive

1. Creare una nuova cartella di lavoro e un nuovo file di script Hive, se non sono già disponibili.
2. Connettersi al proprio account di Azure e quindi configurare il cluster predefinito, se non è ancora stato fatto.
3. Copiare e incollare il codice seguente nel file Hive e quindi salvare.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Fare clic con il pulsante destro del mouse sull'editor di script e quindi scegliere **HDInsight: Hive Batch** (HDInsight: Script batch Hive) per inviare un processo Hive. 
4. Selezionare un cluster come destinazione per l'invio.  

    Dopo avere inviato un processo Hive, le informazioni sull'esito dell'invio e sull'ID del processo sono visualizzate nel pannello **OUTPUT**. Verrà aperto il **WEB BROWSER** che mostra lo stato e i log del processo in tempo reale.

   ![Risultato dell'invio del processo Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

Rispetto all'[invio di query Interactive Hive](#submit-interactive-hive-queries), il processo batch richiede molto più tempo.

## <a name="submit-interactive-pyspark-queries"></a>Inviare query PySpark interattive
Gli strumenti di HDInsight per VSCode consentono anche id inviare query interattive PySpark a cluster Spark.
1. Creare una nuova cartella di lavoro e un nuovo file di script con estensione py, se non sono già disponibili.
2. Connettersi al proprio account Azure, se non si è già connessi.
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
4. Evidenziare questi script, fare doppio clic con il pulsante destro del mouse nell'editor di script e quindi fare clic su **HDInsight: PySpark Interactive** (HDInsight: Query interattiva PySpark).
5. Fare clic sul pulsante **Installa** se non è stata installata l'estensione **Python** in VSCode.
    ![HDInsight per Visual Studio Code - Installazione di Python](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Configurare l'ambiente Python nel sistema se non è già stato installato. 
   - Per Windows, scaricare e installare [Python](https://www.python.org/downloads/). Assicurarsi che `Python` e `pip` siano presenti nel percorso del sistema.
   - Per le istruzioni relative a MacOS e Linux, vedere [Configurare l'ambiente PySpark interattivo per Visual Studio Code](set-up-pyspark-interactive-environment.md).
7. Selezionare un cluster per inviare la query PySpark. Il risultato della query viene visualizzato subito dopo in una nuova scheda a destra:

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Lo strumento supporta anche l'esecuzione di query sulla **clausola SQL**.

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) Lo stato dell'invio viene visualizzato a sinistra della barra di stato nella parte inferiore durante l'esecuzione delle query. Non è possibile inviare altre query con stato **PySpark Kernel (busy)** (Kernel PySpark (occupato)). In caso contrario, l'esecuzione viene bloccato.
9. I cluster possono gestire una sessione. Ad esempio, **a=100** gestisce già questa sessione nel cluster. Si eseguirà solo **print a** sul cluster.
 

## <a name="submit-pyspark-batch-job"></a>Inviare un processo batch PySpark

1. Creare una nuova cartella di lavoro e un nuovo file di script con estensione py, se non sono già disponibili.
2. Connettersi al proprio account Azure, se non si è già connessi.
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
4. Fare clic con il pulsante destro del mouse sull'editor di script e quindi scegliere **HDInsight: PySpark Batch** (HDInsight: Script batch PySpark). 
5. Selezionare un cluster per l'invio del processo PySpark. 

   ![Risultato dell'invio del processo Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Dopo l'invio di un processo Python, i log di invio vengono visualizzati nella finestra **OUTPUT** in VSCode. Vengono visualizzati anche l'**URL dell'interfaccia utente di Spark** e l'**URL dell'interfaccia utente di Yarn**. È possibile aprire l'URL in un Web browser per tenere traccia dello stato del processo.


## <a name="additional-features"></a>Funzionalità aggiuntive

HDInsight per VSCode supporta le funzionalità seguenti:

- **Completamento automatico di IntelliSense**. I suggerimenti appaiono intorno a parole chiave, metodi, variabili e così via. Le icone differenti rappresentano tipi di oggetti diversi:

    ![Tipi di oggetto IntelliSense degli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Indicatore di errore di IntelliSense**. Il servizio di linguaggio sottolinea gli errori nello script Hive.     
- **Evidenziazioni della sintassi**. Il servizio di linguaggio usa colori diversi per differenziare variabili, parole chiave, tipi di dati, funzioni e così via. 

    ![Evidenziazioni della sintassi degli strumenti di HDInsight per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Passaggi successivi

### <a name="demo"></a>Demo
* HDInsight per VSCode: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Configurare l'ambiente PySpark interattivo per Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Usare Azure Toolkit for IntelliJ per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark)](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Visualizzare i dati Hive con Microsoft Power BI in Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Usare Zeppelin per eseguire query Hive in Azure HDInsight](./hdinsight-connect-hive-zeppelin.md).

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: Usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Creazione ed esecuzione di applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>Gestione delle risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)



