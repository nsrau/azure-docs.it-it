---
title: 'Guida introduttiva: Introduzione a Hadoop e Hive in Azure HDInsight usando il modello di Resource Manager | Microsoft Docs'
description: Informazioni su come creare cluster HDInsight ed eseguire query sui dati con Hive.
keywords: introduzione a Hadoop, Hadoop basato su Linux, guida introduttiva a Hadoop, introduzione a Hive, guida introduttiva a Hive
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: c49371d194b3b6f94076cb00595ec9feaeb3fb44
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096401"
---
# <a name="quickstart-get-started-with-hadoop-and-hive-in-azure-hdinsight-using-resource-manager-template"></a>Guida introduttiva: Introduzione a Hadoop e Hive in Azure HDInsight usando il modello di Resource Manager

Questo articolo illustra come creare cluster [Hadoop](http://hadoop.apache.org/) in HDInsight usando un modello di Resource Manager e quindi eseguire i processi Hive in HDInsight. La maggior parte dei processi Hadoop è costituita da processi batch. Viene creato un cluster, si eseguono alcuni processi e quindi si elimina il cluster. In questo articolo vengono eseguite tutte e tre le attività.

In questa guida introduttiva si userà un modello di Resource Manager per creare un cluster Hadoop HDInsight. È anche possibile creare un cluster nel [portale di Azure](apache-hadoop-linux-create-cluster-get-started-portal.md).

HDInsight attualmente viene fornito con [sette diversi tipi di cluster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Ogni tipo di cluster supporta un set diverso di componenti. Tutti i tipi di cluster supportano Hive. Per un elenco dei componenti supportati in HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight](../hdinsight-component-versioning.md)  

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Creare un cluster Hadoop

In questa sezione viene creato un cluster Hadoop in HDInsight usando un modello di Azure Resource Manager. Per seguire questo articolo non è necessario conoscere il modello di Resource Manager. 

1. Fare clic sul pulsante **Deploy to Azure** (Distribuisci in Azure) seguente per accedere ad Azure e aprire il modello di Resource Manager nel portale di Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Immettere o selezionare i valori, come illustrato nello screenshot seguente:

    > [!NOTE]
    > I valori immessi devono essere univoci e devono seguire le linee guida sulle convenzioni di denominazione. Il modello non esegue controlli di convalida. Se i valori immessi sono già in uso o non seguono le linee guida, viene visualizzato un errore dopo l'invio del modello.       
    > 
    >
    
    ![Modello iniziale di Resource Manager per HDInsight basato su Linux nel portale](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuire cluster Hadoop in HDInsight usando il portale di Azure e un modello di gestione del gruppo di risorse")

    Immettere o selezionare i valori seguenti:
    
    |Proprietà  |DESCRIZIONE  |
    |---------|---------|
    |**Sottoscrizione**     |  Selezionare la sottoscrizione di Azure. |
    |**Gruppo di risorse**     | Creare un gruppo di risorse o selezionarne uno esistente.  Un gruppo di risorse è un contenitore di componenti di Azure.  In questo caso, il gruppo di risorse contiene il cluster HDInsight e l'account di Archiviazione di Azure dipendente. |
    |**Posizione**     | Selezionare una posizione di Azure in cui si vuole creare il cluster.  Scegliere una località vicina all'utente per ottenere prestazioni migliori. |
    |**Tipo di cluster**     | Selezionare **hadoop**. |
    |**Nome del cluster**     | Immettere un nome per il cluster Hadoop. Poiché tutti i cluster in HDInsight condividono lo stesso spazio dei nomi DNS, è necessario che questo nome sia univoco. Il nome può includere al massimo 59 caratteri, tra cui lettere, numeri e trattini. Si noti che il primo e l'ultimo carattere del nome non possono essere trattini. |
    |**Nome di accesso e password del cluster**     | Il nome di accesso predefinito è **admin**. La password deve avere una lunghezza minima di 10 caratteri e deve contenere almeno una cifra, una lettera maiuscola, una lettera minuscola e un carattere non alfanumerico, ad eccezione di ' " ` \). Assicurarsi di **non fornire** password comuni, ad esempio "Pass@word1".|
    |**Nome utente e password SSH**     | Il nome utente predefinito è **sshuser**.  È possibile rinominare il nome utente SSH.  Per la password utente SSH sono previsti gli stessi requisiti della password di accesso al cluster.|
       
    Alcune proprietà sono state impostate come hardcoded nel modello.  È possibile configurare questi valori dal modello. Per una spiegazione più approfondita di queste proprietà, vedere l'articolo su come [create cluster Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Selezionare **Accetto le condizioni riportate sopra** e **Aggiungi al dashboard** e quindi selezionare **Acquisto**. Verrà visualizzato un nuovo riquadro denominato **Invio della distribuzione** nel dashboard del portale. La creazione di un cluster richiede circa 20 minuti.

    ![Stato di avanzamento della distribuzione del modello](./media/apache-hadoop-linux-tutorial-get-started/deployment-progress-tile.png "Stato di avanzamento della distribuzione del modello di Azure")

4. Dopo la creazione del cluster, la didascalia del riquadro viene cambiata nel nome del gruppo di risorse specificato. Il riquadro elenca inoltre il cluster HDInsight creato all'interno del gruppo di risorse. 
   
    ![Gruppo di risorse iniziale in HDInsight basato su Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Gruppo di risorse cluster in Azure HDInsight")
    
5. Il riquadro elenca anche la risorsa di archiviazione predefinita associata al cluster. Ogni cluster ha una dipendenza da un [account di archiviazione di Azure](../hdinsight-hadoop-use-blob-storage.md) o da un [account Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Viene indicato come account di archiviazione predefinito. Il cluster HDInsight e l'account di archiviazione predefinito devono avere un percorso condiviso nella stessa area di Azure. L'eliminazione dei cluster non comporta l'eliminazione dell'account di archiviazione.
    

> [!NOTE]
> Per altri metodi di creazione di cluster e per informazioni sulle proprietà usate in questa esercitazione, vedere [Creare cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).       
> 
>

## <a name="use-vscode-to-run-hive-queries"></a>Usare VS Code per eseguire query Hive

Per ottenere gli strumenti HDInsight in Visual Studio Code, vedere [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).

### <a name="submit-interactive-hive-queries"></a>Inviare query Interactive Hive

Gli strumenti HDInsight per VS Code consentono di inviare query interactive Hive ai cluster Interactive Query di HDInsight.

1. Creare una nuova cartella di lavoro e un nuovo file di script Hive, se non sono già disponibili.

2. Connettersi all'account di Azure personale e quindi configurare il cluster predefinito, se non è ancora stato fatto.

3. Copiare e incollare il codice seguente nel file Hive e salvarlo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: Hive Interactive** (HDInsight: Script interattivo Hive) per inviare la query. Gli strumenti consentono anche di inviare un blocco di codice, anziché l'intero file di script, usando il menu di scelta rapida. Subito dopo, il risultato della query viene visualizzato in una nuova scheda.

   ![Risultato di Interactive Hive](./media/apache-hadoop-linux-tutorial-get-started/interactive-hive-result.png)

    - Pannello dei **risultati**: è possibile salvare i risultati completi come file CSV, JSON o file EXCEL in un percorso locale oppure selezionare più righe.

    - Pannello dei **messaggi**: se si seleziona il numero di **riga**, si passa alla prima riga dello script in esecuzione.

Rispetto all'[esecuzione di un processo batch Hive](#submit-hive-batch-scripts), la query interattiva richiede molto meno tempo.

### <a name="submit-hive-batch-scripts"></a>Inviare script batch Hive

1. Creare una nuova cartella di lavoro e un nuovo file di script Hive, se non sono già disponibili.

2. Connettersi all'account di Azure personale e quindi configurare il cluster predefinito, se non è ancora stato fatto.

3. Copiare e incollare il codice seguente nel file Hive e salvarlo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **HDInsight: Hive Batch** (HDInsight: Script batch Hive) per inviare un processo Hive. 

5. Selezionare il cluster a cui si vuole inviare il processo.  

    Dopo avere inviato un processo Hive, le informazioni sull'esito dell'invio e sull'ID del processo vengono visualizzate nel pannello **OUTPUT**. Il processo Hive apre anche il pannello **WEB BROWSER**, che mostra lo stato e i log del processo in tempo reale.

   ![Risultato dell'invio del processo Hive](./media/apache-hadoop-linux-tutorial-get-started/submit-Hivejob-result.png)

L'[invio di query Hive interattive](#submit-interactive-hive-queries) richiede molto meno tempo rispetto all'invio di un processo batch.

## <a name="use-visualstudio-to-run-hive-queries"></a>Usare VisualStudio per eseguire query Hive

Per ottenere gli strumenti HDInsight per Visual Studio, vedere [Usare Strumenti di Data Lake per Visual Studio](./apache-hadoop-visual-studio-tools-get-started.md).

### <a name="run-hive-queries"></a>Eseguire query Hive

Per la creazione e l'esecuzione di query Hive sono disponibili due opzioni:

* Creare query ad hoc
* Creare un'applicazione Hive

Per creare ed eseguire query ad hoc:

1. In **Esplora server** selezionare **Azure** > **Cluster HDInsight**.

2. Fare clic con il pulsante destro del mouse sul cluster in cui si vuole eseguire la query, quindi scegliere **Scrivi una query Hive**.  

3. Immettere le query Hive. 

    L'editor Hive supporta IntelliSense. Strumenti Data Lake per Visual Studio supporta il caricamento di metadati remoti quando si modifica lo script Hive. Se ad esempio si digita **SELECT * FROM**, IntelliSense elenca tutti i nomi di tabella suggeriti. Quando si specifica un nome di tabella, IntelliSense elenca i nomi delle colonne. Gli strumenti supportano la maggior parte delle funzioni definite dall'utente predefinite, delle sottoquery e delle istruzioni DML Hive.
   
    ![Screenshot 1 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-linux-tutorial-get-started/vs-intellisense-table-name.png "IntelliSense in U-SQL")
   
    ![Screenshot 2 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-linux-tutorial-get-started/vs-intellisense-column-name.png "IntelliSense in U-SQL")
   
   > [!NOTE]
   > IntelliSense suggerisce solo i metadati del cluster selezionato nella barra degli strumenti HDInsight.
   > 
   
4. Selezionare **Submit** (Invio) o **Submit (Advanced)** (Invio - Avanzato). 
   
    ![Screenshot del processo di invio di una query Hive](./media/apache-hadoop-linux-tutorial-get-started/vs-batch-query.png)

   Se si seleziona l'opzione di invio avanzato, configurare le impostazioni per **Nome processo**, **Argomenti**, **Configurazioni aggiuntive** e **Directory di stato** per lo script:

    ![Screenshot di una query Hive Hadoop HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Inviare query")

   Eseguire query Interactive Hive

   * Fare clic sulla freccia in giù per scegliere **Interactive**. 
   
   * Fare clic su **Execute**.

   ![Screenshot dell'esecuzione di query Interactive Hive](./media/apache-hadoop-linux-tutorial-get-started/vs-execute-hive-query.png)

Per creare ed eseguire una soluzione Hive:

1. Scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.
2. Selezionare **HDInsight** nel riquadro sinistro. Nel riquadro centrale selezionare **Hive Application** (Applicazione Hive). Immettere le proprietà e quindi selezionare **OK**.
   
    ![Screenshot di un nuovo progetto Hive in Strumenti di Visual Studio per HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Creare applicazioni Hive da Visual Studio")
3. In **Esplora soluzioni** fare doppio clic su **Script.hql** per aprire lo script.
4. Immettere le query Hive e inviare. (Fare riferimento al passaggio 3&4 precedente)  



## <a name="run-hive-queries"></a>Eseguire query Hive

[Apache Hive](hdinsight-use-hive.md) è il componente più diffuso usato in HDInsight. Esistono diversi modi per eseguire processi Hive in HDInsight. In questa esercitazione si usa la visualizzazione Hive di Ambari dal portale. Per altri metodi di esecuzione di processi Hive, vedere [Usare Hive in HDInsight](hdinsight-use-hive.md).

1. Per aprire Ambari, nello screenshot precedente selezionare **Dashboard cluster**.  È anche possibile passare a **https://&lt;NomeCluster>.azurehdinsight.net**, dove &lt;NomeCluster> è il cluster creato nella sezione precedente.

    ![Guida introduttiva di HDInsight Linux al dashboard del cluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "Guida introduttiva di HDInsight Linux al dashboard del cluster")

2. Immettere il nome utente e la password Hadoop specificati durante la creazione del cluster. Il nome utente predefinito è **admin**.

3. Aprire la **visualizzazione Hive** come illustrato nella schermata seguente:
   
    ![Selezione delle visualizzazioni di Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Menu di visualizzazione di Hive di HDInsight")

4. Nella scheda **QUERY** incollare le istruzioni HiveQL seguenti nel foglio di lavoro:
   
        SHOW TABLES;

    ![Visualizzazioni Hive di HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Editor di query della visualizzazione Hive di HDInsight")
   
   > [!NOTE]
   > Il punto e virgola è obbligatorio per Hive.       
   > 
   > 

5. Scegliere **Execute**(Esegui). Viene visualizzata una scheda **RESULTS** (RISULTATI) sotto la scheda **QUERY** e vengono visualizzate informazioni sul processo. 
   
    Al termine dell'elaborazione della query, nella scheda **QUERY** vengono visualizzati i risultati dell'operazione. Verrà visualizzata una tabella denominata **hivesampletable**. Questa tabella Hive di esempio è disponibile in tutti i cluster HDInsight.
   
    ![Visualizzazioni Hive di HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor di query della visualizzazione Hive di HDInsight")

6. Ripetere i passaggi 4 e 5 per eseguire questa query:
   
        SELECT * FROM hivesampletable;
   
7. È anche possibile salvare i risultati della query. Selezionare il pulsante del menu a destra e specificare se si vuole scaricare i risultati come file CSV o archiviarli nell'account di archiviazione associato al cluster.

    ![Salvare il risultato di una query Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Salvare il risultato di una query Hive")

Dopo aver completato un processo Hive, è possibile [esportare i risultati in un database SQL di Azure o in un database di SQL Server](apache-hadoop-use-sqoop-mac-linux.md). È anche possibile [visualizzare i risultati in Excel](apache-hadoop-connect-excel-power-query.md). Per altre informazioni sull'uso di Hive in HDInsight, vedere [Usare Hive e HiveQL con Hadoop in HDInsight per analizzare un file Apache log4j di esempio](hdinsight-use-hive.md).

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine dell'articolo, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. 

> [!NOTE]
> Se si procede *subito* con l'esercitazione successiva per imparare come eseguire le operazioni ETL mediante Hadoop in HDInsight, è possibile mantenere il cluster in esecuzione, poiché nell'esercitazione è necessario creare nuovamente un cluster Hadoop. Se invece non si prevede di passare subito all'esercitazione successiva, è necessario eliminare il cluster ora.
> 
> 

**Per eliminare il cluster e/o l'account di archiviazione predefinito**

1. Tornare alla scheda del browser in cui è visualizzato il portale di Azure. Occorre visualizzare la pagina di panoramica del cluster. Se si vuole solo eliminare il cluster ma conservare l'account di archiviazione predefinito, scegliere **Elimina**.

    ![Eliminazione del cluster HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Eliminazione del cluster HDInsight")

2. Se si intende eliminare il cluster, nonché l'account di archiviazione predefinito, selezionare il nome del gruppo di risorse (evidenziato nello screenshot precedente) per aprire la pagina di gruppo di risorse.

3. Selezionare **Elimina gruppo di risorse** per eliminare il gruppo di risorse che contiene il cluster e l'account di archiviazione predefinito. Si noti che l'eliminazione del gruppo di risorse comporta l'eliminazione dell'account di archiviazione. Se si vuole mantenere l'account di archiviazione, scegliere di eliminare solo il cluster.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come creare un cluster HDInsight basato su Linux usando un modello di Resource Manager ed eseguire query Hive di base. Passare all'articolo successivo per informazioni su come eseguire un'operazione di estrazione, trasformazione e caricamento (ETL) usando Hadoop in HDInsight.

> [!div class="nextstepaction"]
>[Estrarre, trasformare e caricare dati Apache Hive in HDInsight ](../hdinsight-analyze-flight-delay-data-linux.md)

Se si è pronti per iniziare a usare i dati, ma sono necessarie altre informazioni sulle modalità di archiviazione dei dati in HDInsight o sulle procedure di importazione dei dati in HDInsight, vedere gli articoli seguenti:

* Per informazioni sul modo in cui HDInsight usa Archiviazione di Azure, vedere [Usare Archiviazione di Azure con HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Per informazioni su come creare un cluster HDInsight con Data Lake Storage, vedere [Avvio rapido: configurare cluster in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* Per informazioni su come caricare i dati in HDInsight, vedere [Caricare dati in HDInsight](../hdinsight-upload-data.md).

Per altre informazioni sull'analisi dei dati con HDInsight, vedere gli articoli seguenti:

* Per altre informazioni sull'uso di Hive con HDInsight, incluse le procedure per eseguire query Hive da Visual Studio, vedere [Usare Hive con HDInsight](hdinsight-use-hive.md).
* Per informazioni su Pig, un linguaggio usato per la trasformazione dei dati, vedere [Usare Pig con HDInsight](hdinsight-use-pig.md).
* Per altre informazioni su MapReduce, un framework software che consente di scrivere programmi per l'elaborazione dei dati in Hadoop, vedere [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md).
* Per altre informazioni sull'uso di HDInsight Tools per Visual Studio per analizzare i dati in HDInsight, vedere [Introduzione all'uso di Hadoop Tools per Visual Studio per HDInsight](apache-hadoop-visual-studio-tools-get-started.md).
* Per altre informazioni sull'uso degli strumenti HDInsight per VS Code per analizzare i dati in HDInsight, vedere [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).


Per altre informazioni sulla creazione o la gestione di un cluster HDInsight, vedere gli articoli seguenti:

* Per altre informazioni sulla gestione di cluster HDInsight basati su Linux, vedere [Gestire i cluster HDInsight tramite Ambari](../hdinsight-hadoop-manage-ambari.md).
* Per altre informazioni sulle opzioni che è possibile selezionare durante la creazione di un cluster HDInsight, vedere [Creare cluster Hadoop basati su Linux in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


