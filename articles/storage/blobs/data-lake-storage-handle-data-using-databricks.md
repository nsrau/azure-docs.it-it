---
title: Informazioni su come estrarre, caricare e trasferire operazioni tramite Azure Databricks
description: Informazioni su come estrarre dati dalla versione di anteprima di Azure Data Lake Storage Gen2 in Azure Databricks, trasformare i dati e quindi caricarli in Azure SQL Data Warehouse.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 12/06/2018
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 108495c367bdb50ed4799aab929e4d26ad14ab87
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974184"
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>Esercitazione: Estrarre, trasformare e caricare dati tramite Azure Databricks

In questa esercitazione viene eseguita un'operazione di estrazione, trasformazione e caricamento (ETL) dei dati per spostarli da un account di archiviazione di Azure con Azure Data Lake Storage Gen2 abilitato ad Azure SQL Data Warehouse tramite Azure Databricks.

L'illustrazione seguente mostra il flusso dell'applicazione:

![Azure Databricks con Data Lake Storage Gen2 e SQL Data Warehouse](./media/data-lake-storage-handle-data-using-databricks/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks con Data Lake Storage Gen2 e SQL Data Warehouse")

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un'area di lavoro di Azure Databricks
> * Creare un cluster Spark in Azure Databricks
> * Creare un account con supporto di Azure Data Lake Storage Gen2
> * Caricare dati in Azure Data Lake Storage Gen2
> * Creare un notebook in Azure Databricks
> * Estrarre dati da Data Lake Storage Gen2
> * Trasformare dati in Azure Databricks
> * Caricare i dati in Azure SQL Data Warehouse

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* Creare un'istanza di Azure SQL Data Warehouse, creare una regola del firewall a livello di server e connettersi al server come amministratore del server. Seguire le istruzioni in [Avvio rapido: Creare un'istanza di Azure SQL Data Warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md)
* Creare una chiave master del database per Azure SQL Data Warehouse. Seguire le istruzioni riportate in [Creare una chiave master di un database](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
* [Creare un account di Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

In questa sezione viene creata un'area di lavoro di Azure Databricks usando il portale di Azure.

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **Azure Databricks**.

    ![Databricks nel portale di Azure](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Databricks nel portale di Azure")

2. Nella pagina **Servizio Azure Databricks** specificare i valori per creare un'area di lavoro di Databricks.

    ![Creare un'area di lavoro di Azure Databricks](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Creare un'area di lavoro di Azure Databricks")

    Specificare i valori seguenti:

    |Proprietà  |Descrizione  |
    |---------|---------|
    |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks.        |
    |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Posizione**     | Selezionare **Stati Uniti occidentali 2**. Per le altre aree disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).        |
    |**Piano tariffario**     |  Scegliere tra **Standard** e **Premium**. Per altre informazioni su questi piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selezionare **Aggiungi al dashboard** e quindi **Crea**.

3. La creazione dell'account richiede alcuni minuti, durante i quali il portale visualizza il riquadro **Invio della distribuzione per Azure Databricks** a destra. Potrebbe essere necessario scorrere verso destra nel dashboard per visualizzare il riquadro. È presente anche un indicatore di stato nella parte superiore della schermata. È possibile esaminare lo stato di avanzamento nelle due aree.

    ![Riquadro di distribuzione Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Riquadro di distribuzione Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Creare un cluster Spark in Databricks

1. Nel portale di Azure passare all'area di lavoro di Databricks creata e quindi selezionare **Avvia area di lavoro**.

2. Si verrà reindirizzati al portale di Azure Databricks. Nel portale selezionare **Cluster**.

    ![Databricks in Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks in Azure")

3. Nella pagina **New cluster** (Nuovo cluster) specificare i valori per creare un cluster.

    ![Creare il cluster Databricks Spark in Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Creare il cluster Databricks Spark in Azure")

    Specificare i valori per i campi seguenti e accettare i valori predefiniti per gli altri campi:

    * Immettere un nome per il cluster.
    * Per questo articolo creare un cluster con il runtime **4.2**.
    * Assicurarsi di selezionare la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo ___ minuti di attività). Specificare una durata in minuti per terminare il cluster, se questo non viene usato.

    Selezionare **Crea cluster**. Quando il cluster è in esecuzione, è possibile collegare blocchi appunti al cluster ed eseguire processi Spark.

## <a name="create-storage-account-file-system"></a>Creare il file system per l'account di archiviazione

In questa sezione viene creato un notebook nell'area di lavoro di Azure Databricks e quindi vengono eseguiti frammenti di codice per configurare l'account di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) passare all'area di lavoro di Azure Databricks creata e quindi selezionare **Launch Workspace** (Avvia l'area di lavoro).

2. Nel riquadro a sinistra selezionare **Workspace** (Area di lavoro). Nell'elenco a discesa **Workspace** (Area di lavoro) selezionare **Create (Crea)** > **Notebook**.

    ![Creare un blocco appunti in Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Creare un blocco appunti in Databricks")

3. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome per il notebook. Selezionare **Scala** come linguaggio e quindi selezionare il cluster Spark creato in precedenza.

    ![Creare un blocco appunti in Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Creare un blocco appunti in Databricks")

    Selezionare **Create**.

4. Immettere il codice seguente nella prima cella ed eseguire il codice. Ricordarsi di sostituire i valori segnaposto racchiusi tra parentesi nell'esempio con valori personalizzati:

        ```scala
        %python%
        configs = {"fs.azure.account.auth.type": "OAuth",
            "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
            "fs.azure.account.oauth2.client.id": "<service-client-id>",
            "fs.azure.account.oauth2.client.secret": "<service-credentials>",
            "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
     
        dbutils.fs.mount(
            source = "abfss://<file-system-name>@<account-name>.dfs.core.windows.net/[<directory-name>]",
            mount_point = "/mnt/<mount-name>",
            extra_configs = configs)
        ```

5. Premere **MAIUSC+INVIO** per eseguire la cella di codice.

A questo punto è stato creato il file system per l'account di archiviazione.

## <a name="upload-data-to-the-storage-account"></a>Caricare i dati nell'account di archiviazione

Il passaggio successivo consiste nel caricare un file di dati di esempio nell'account di archiviazione per trasformarlo successivamente in Azure Databricks. 

> [!NOTE]
> Se non hai già un account in grado di supportare Azure Data Lake Storage Gen2, seguire le [Guida introduttiva per creare uno](./data-lake-storage-quickstart-create-account.md).

1. Scaricare (**small_radio_json**) dalle [esempi U-SQL e gestione problemi](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) repository e prendere nota del percorso in cui salvare il file.

2. Caricare i dati nell'account di archiviazione. Il metodo usato per caricare i dati nell'account di archiviazione è diverso a seconda che sia abilitato o meno lo spazio dei nomi gerarchico.

    Se lo spazio dei nomi gerarchico è abilitato per l'account di archiviazione di Azure, è possibile usare Azure Data Factory, distp o AzCopy (versione 10) per gestire il caricamento. Attualmente AzCopy versione 10 è disponibile solo in anteprima. Per usare AzCopy, copiare il codice seguente in una finestra di comando:

    ```bash
    set ACCOUNT_NAME=<ACCOUNT_NAME>
    set ACCOUNT_KEY=<ACCOUNT_KEY>
    azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
    ```
    
## <a name="extract-data-from-azure-storage"></a>Estrarre i dati da Archiviazione di Azure

Tornare al notebook di Databricks e immettere il codice seguente in una nuova cella:

1. Aggiungere il frammento di codice seguente in una cella di codice vuota e sostituire i valori segnaposto con quelli salvati in precedenza dall'account di archiviazione.

    ```scala
    dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
    dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
    ```

    Premere **MAIUSC+INVIO** per eseguire la cella di codice.

2. È ora possibile caricare il file JSON di esempio come frame di dati in Azure Databricks. Incollare il codice seguente in una nuova cella e quindi premere **MAIUSC+INVIO** (assicurandosi di sostituire i valori segnaposto):

    ```scala
    val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
    ```

3. Eseguire il codice seguente per visualizzare il contenuto del frame di dati.

    ```scala
    df.show()
    ```

    L'output visualizzato dovrebbe essere simile al frammento di codice seguente:

    ```bash
    +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
    |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
    +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
    | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
    | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
    | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
    ...
    ...
    ```

I dati sono stati estratti da Azure Data Lake Storage Gen2 in Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Trasformare dati in Azure Databricks

I dati di esempio non elaborati del file **small_radio_json.json** sono relativi agli ascoltatori di una stazione radio e includono diverse colonne. In questa sezione vengono trasformati i dati in modo da recuperare solo colonne specifiche dal set di dati.

1. Recuperare prima di tutto solo le colonne *firstName*, *lastName*, *gender*, *location* e *level* dal frame di dati già creato.

    ```scala
    val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
    ```

    L'output visualizzato sarà simile al frammento di codice seguente:

    ```bash
    +---------+----------+------+--------------------+-----+
    |firstname|  lastname|gender|            location|level|
    +---------+----------+------+--------------------+-----+
    | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
    |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
    |     Liam|     Watts|     M|New York-Newark-J...| paid|
    |     Tess|  Townsend|     F|Nashville-Davidso...| free|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
    |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    |     Tess|  Townsend|     F|Nashville-Davidso...| free|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |     Liam|     Watts|     M|New York-Newark-J...| paid|
    |     Liam|     Watts|     M|New York-Newark-J...| paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    +---------+----------+------+--------------------+-----+
    ```

2.  È possibile trasformare ulteriormente questi dati per rinominare la colonna **level** in **subscription_type**.

    ```scala
    val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
    renamedColumnsDF.show()
    ```

    L'output visualizzato sarà simile al frammento di codice seguente.

    ```bash
    +---------+----------+------+--------------------+-----------------+
    |firstname|  lastname|gender|            location|subscription_type|
    +---------+----------+------+--------------------+-----------------+
    | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
    |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
    |     Liam|     Watts|     M|New York-Newark-J...|             paid|
    |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
    |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |     Liam|     Watts|     M|New York-Newark-J...|             paid|
    |     Liam|     Watts|     M|New York-Newark-J...|             paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    +---------+----------+------+--------------------+-----------------+
    ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>Caricare i dati in Azure SQL Data Warehouse

In questa sezione i dati trasformati vengono caricati in Azure SQL Data Warehouse. Usando il connettore di Azure SQL Data Warehouse per Azure Databricks, è possibile caricare direttamente un frame di dati come tabella in SQL Data Warehouse.

Come indicato in precedenza, il connettore di SQL Data Warehouse usa l'Archiviazione BLOB di Azure come archivio temporaneo per caricare i dati tra Azure Databricks e Azure SQL Data Warehouse. Specificare quindi prima di tutto la configurazione per la connessione all'account di archiviazione. È necessario che l'account sia già stato creato come parte dei prerequisiti per questo articolo.

1. Specificare la configurazione per l'accesso all'account di archiviazione di Azure da Azure Databricks.

    ```scala
    val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
    val fileSystemName = "<FILE_SYSTEM_NAME>"
    val accessKey =  "<ACCESS_KEY>"
    ```

2. Specificare una cartella temporanea che verrà usata durante lo spostamento dei dati tra Azure Databricks e Azure SQL Data Warehouse.

    ```scala
    val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
    ```

3. Eseguire il frammento di codice seguente per archiviare le chiavi di accesso dell'Archiviazione BLOB di Azure nella configurazione. In questo modo si assicura che non sia necessario mantenere la chiave di accesso nel notebook in testo normale.

    ```scala
    val acntInfo = "fs.azure.account.key."+ storageURI
    sc.hadoopConfiguration.set(acntInfo, accessKey)
    ```

4. Specificare i valori per la connessione all'istanza di Azure SQL Data Warehouse. È necessario che un'istanza di SQL Data Warehouse sia stata creata come parte dei prerequisiti.

    ```scala
    //SQL Data Warehouse related settings
    val dwDatabase = "<DATABASE NAME>"
    val dwServer = "<DATABASE SERVER NAME>" 
    val dwUser = "<USER NAME>"
    val dwPass = "<PASSWORD>"
    val dwJdbcPort =  "1433"
    val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
    val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
    val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
    ```

5. Eseguire il frammento di codice seguente per caricare il frame di dati trasformato, **renamedColumnsDF**, come tabella in SQL Data Warehouse. Il frammento di codice crea una tabella denominata **SampleTable** nel database SQL.

    ```scala
    spark.conf.set(
        "spark.sql.parquet.writeLegacyFormat",
        "true")
    
    renamedColumnsDF.write
        .format("com.databricks.spark.sqldw")
        .option("url", sqlDwUrlSmall) 
        .option("dbtable", "SampleTable")
        .option( "forward_spark_azure_storage_credentials","True")
        .option("tempdir", tempDir)
        .mode("overwrite")
        .save()
    ```

6. Connettersi al database SQL e verificare che sia visualizzata una tabella **SampleTable**.

    ![Verificare la tabella di esempio](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Verificare la tabella di esempio")

7. Eseguire una query selezionata per verificare i contenuti della tabella. Dovrebbe includere gli stessi dati del frame di dati **renamedColumnsDF**.

    ![Verificare i contenuti della tabella di esempio](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Verificare i contenuti della tabella di esempio")

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver concluso l'esecuzione per l'esercitazione è possibile terminare il cluster. A questo scopo, nel riquadro sinistro dell'area di lavoro di Azure Databricks fare clic su **Clusters** (Cluster). Per il cluster che si vuole terminare, posizionare il cursore sui puntini di sospensione sotto la colonna **Actions** (Azioni) e fare clic sull'icona **Terminate** (Termina).

![Arrestare un cluster Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Arrestare un cluster Databricks")

Se non viene terminato manualmente, il cluster si arresterà automaticamente se è stata selezionata la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo ___ minuti di attività) durante la creazione del cluster. In tal caso, il cluster viene automaticamente arrestato se è rimasto inattivo per il tempo specificato.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come:

> [!div class="checklist"]
> * Creare un'area di lavoro di Azure Databricks
> * Creare un cluster Spark in Azure Databricks
> * Creare un account con supporto di Azure Data Lake Storage Gen2
> * Caricare dati in Azure Data Lake Storage Gen2
> * Creare un notebook in Azure Databricks
> * Estrarre dati da Data Lake Storage Gen2
> * Trasformare dati in Azure Databricks
> * Caricare i dati in Azure SQL Data Warehouse

Passare all'esercitazione successiva per ottenere informazioni sullo streaming di dati in tempo reale in Azure Databricks tramite Hub eventi di Azure.

> [!div class="nextstepaction"]
>[Trasmettere dati in Azure Databricks tramite Hub eventi](../../azure-databricks/databricks-stream-from-eventhubs.md)
