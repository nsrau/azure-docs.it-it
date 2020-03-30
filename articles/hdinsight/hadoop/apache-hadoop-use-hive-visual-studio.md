---
title: Apache Hive & strumenti Data Lake per Visual Studio - Azure HDInsight
description: Informazioni su come usare gli strumenti Data Lake per Visual Studio per eseguire query Apache Hive con Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687794"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Eseguire query Apache Hive usando gli strumenti Data Lake per Visual Studio

Informazioni su come usare gli strumenti Data Lake per Visual Studio per eseguire query su Apache Hive. Gli strumenti Data Lake consentono di creare, inviare e monitorare facilmente query Hive in Apache Hadoop in Azure HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Per informazioni sulla creazione di questo elemento, vedere Creare un cluster Apache Hadoop in Azure HDInsight usando il modello di Resource Manager.For information about creating this item, see [Create Apache Hadoop cluster in Azure HDInsight using Resource Manager template.](./apache-hadoop-linux-tutorial-get-started.md)

* [Visual Studio](https://visualstudio.microsoft.com/vs/). I passaggi descritti in questo articolo usano Visual Studio 2019.The steps in this article use Visual Studio 2019.

* Strumenti HDInsight per Visual Studio o Azure Data Lake Tools per Visual Studio. Per informazioni sull'installazione e la configurazione degli strumenti, vedere [Installare Data Lake Tools per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a> Eseguire query Apache Hive usando Visual Studio

Per la creazione e l'esecuzione di query Hive sono disponibili due opzioni:

* Creare query ad hoc.
* Creare un'applicazione Hive.Create a Hive application.

### <a name="create-an-ad-hoc-hive-query"></a>Creare una query Hive ad hocCreate an ad-hoc Hive query

Le query ad hoc possono essere eseguite in modalità **Batch** o **Interattiva.**

1. Avviare **Visual Studio** e selezionare **Continua senza codice**.

2. In **Esplora server**fare clic con il pulsante destro del mouse su **Azure**, scegliere Connetti a sottoscrizione di **Microsoft Azure...** e completare il processo di accesso.

3. Espandere **HDInsight**, fare clic con il pulsante destro del mouse sul cluster in cui si desidera eseguire la query e quindi scegliere **Scrivi una query Hive**.

4. Immettere la query hive seguente:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Scegliere **Execute**(Esegui). Il valore predefinito della modalità di esecuzione è **Interactive**.

    ![Eseguire query Hive interattive, Visual StudioExecute interactive Hive query, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Per eseguire la stessa query in modalità **Batch,** attivare o disattivare l'elenco a discesa da **Interattivo** a **Batch**. Il pulsante di esecuzione cambia da **Esegui** a **Invia**.

    ![Invia query Hive batch, Visual StudioSubmit batch Hive query, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    L'editor Hive supporta IntelliSense. Strumenti Data Lake per Visual Studio supporta il caricamento di metadati remoti quando si modifica lo script Hive. Se ad esempio `SELECT * FROM`si digita , IntelliSense elenca tutti i nomi di tabella suggeriti. Quando si specifica un nome di tabella, IntelliSense elenca i nomi delle colonne. Gli strumenti supportano la maggior parte delle funzioni definite dall'utente predefinite, delle sottoquery e delle istruzioni DML Hive. IntelliSense suggerisce solo i metadati del cluster selezionato nella barra degli strumenti HDInsight.

7. Nella barra degli strumenti della query, ovvero l'area sotto la scheda della query e sopra il testo della query, selezionare **Invia**oppure selezionare la freccia a discesa accanto a **Invia** e scegliere **Avanzate** dall'elenco a discesa. Se si seleziona quest'ultima opzione,

8. Se è stata selezionata l'opzione di invio avanzato, configurare **Nome processo**, **Argomenti**, **Configurazioni aggiuntive**e Directory **di stato** nella finestra di dialogo **Invia script** . Selezionare **Invia**.

    ![Finestra di dialogo Invia script, query HDInsight Hadoop Hive](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Creare un'applicazione Hive

Per eseguire una query Hive creando un'applicazione Hive, attenersi alla seguente procedura:

1. Aprire **Visual Studio**.

2. Nella finestra **iniziale** selezionare **Crea un nuovo progetto**.

3. Nella casella **Cerca modelli** della finestra Crea un **nuovo progetto** *immettere Hive*. Quindi scegliere **Hive Application** e selezionare **Next**.

4. Nella finestra **Configura il nuovo progetto** immettere un nome di **progetto,** selezionare o creare un **percorso** per il nuovo progetto e quindi selezionare **Crea**.

5. Aprire il file **Script.hql** creato con il progetto e incollarvi le seguenti istruzioni HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Queste istruzioni eseguire le azioni seguenti:These statements do the following actions:

    * `DROP TABLE`: elimina la tabella, se esistente.

    * `CREATE EXTERNAL TABLE`: crea una nuova tabella "esterna" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. (I dati vengono lasciati nella posizione originale.)

        > [!NOTE]  
        > Le tabelle esterne devono essere usate quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo MapReduce o un servizio di Azure.External tables should be used when you expect the underlying data to be updated by an external source, such as a MapReduce job or an Azure service.
        >
        > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

    * `ROW FORMAT`: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

    * `STORED AS TEXTFILE LOCATION`: indica a Hive che i dati sono archiviati nella directory *di esempio/dati* e che vengono archiviati come testo.

    * `SELECT`: seleziona un conteggio di `t4` tutte le `[ERROR]`righe in cui la colonna contiene il valore . Questa istruzione restituisce `3`un valore pari a , poiché tre righe contengono questo valore.

    * `INPUT__FILE__NAME LIKE '%.log'`: indica a Hive di restituire solo i dati dai file che terminano con .log. Questa clausola limita la ricerca al file *sample.log* che contiene i dati.

6. Dalla barra degli strumenti del file di query , che ha un aspetto simile alla barra degli strumenti di query ad hoc, selezionare il cluster HDInsight che si desidera utilizzare per questa query. Quindi modificare **Interattivo** in Batch (se necessario) e selezionare Invia per eseguire le istruzioni come processo Hive.Then change Interactive to **Batch** (if necessary) and select **Submit** to run the statements as a Hive job.

   Verrà visualizzata una finestra di **riepilogo del processo Hive** con informazioni relative al processo in esecuzione. Usare il collegamento **Aggiorna** per aggiornare le informazioni del processo finché il campo **Stato processo** non viene impostato su **Completato**.

   ![Riepilogo del processo Hive completato, applicazione Hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Selezionare **Output processo** per visualizzare l'output del processo. Mostra `[ERROR] 3`, ovvero il valore restituito dalla query.

### <a name="additional-example"></a>Altro esempio

Nell'esempio seguente si `log4jLogs` basa sulla tabella creata nella procedura precedente, [Create a Hive application](#create-a-hive-application).

1. In **Esplora server**fare clic con il pulsante destro del mouse sul cluster e scegliere Scrivi una query **Hive**.

2. Immettere la query hive seguente:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Queste istruzioni eseguire le azioni seguenti:These statements do the following actions:

    * `CREATE TABLE IF NOT EXISTS`: crea una tabella se non esiste già. Poiché `EXTERNAL` la parola chiave non viene utilizzata, questa istruzione crea una tabella interna. Le tabelle interne vengono archiviate nel data warehouse di Hive e sono gestite da Hive.

        > [!NOTE]  
        > A differenza delle tabelle `EXTERNAL`, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

    * `STORED AS ORC`: memorizza i dati in formato ORC *(Row Columnar) ottimizzato.* ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.

    * `INSERT OVERWRITE ... SELECT`: seleziona le righe della tabella `log4jLogs` contenenti `[ERROR]`, quindi inserisce i dati nella tabella `errorLogs`.

3. Se necessario, impostare **Interattivo** su **Batch,** quindi selezionare **Invia**.

4. Per verificare che il processo ha creato la tabella, passare a **Esplora server** ed espandere **Azure** > **HDInsight.** Espandere il cluster HDInsight e quindi espandere **Hive Databases** > **default**. Vengono elencate la tabella **errorLogs** e la tabella **log4jLogs**.

## <a name="next-steps"></a>Passaggi successivi

Come si può notare, gli strumenti HDInsight per Visual Studio forniscono un modo semplice per lavorare con le query Hive in HDInsight.

* Per informazioni generali su Hive in HDInsight, vedere [Che cos'è Apache Hive e HiveQL in Azure HDInsight?](hdinsight-use-hive.md)

* Per informazioni su altri modi in cui è possibile usare Hadoop su HDInsight, vedere [Usare MapReduce in Apache Hadoop in HDInsightFor](hdinsight-use-mapreduce.md) information about other ways you can work with Hadoop on HDInsight, see Use MapReduce in Apache Hadoop on HDInsight

* Per altre informazioni sugli strumenti HDInsight per Visual Studio, vedere[Usare Data Lake Tools per Visual Studio per connettersi ad Azure HDInsight ed eseguire query Apache HiveFor](apache-hadoop-visual-studio-tools-get-started.md) more information about the HDInsight tools for Visual Studio, see Use Data Lake Tools for Visual Studio to connect to Azure HDInsight and run Apache Hive queries
