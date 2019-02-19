---
title: 'Esercitazione: Eseguire operazioni ETL tramite Azure Databricks'
description: Informazioni su come estrarre dati da Data Lake Storage Gen2 in Azure Databricks, trasformare i dati e quindi caricarli in Azure SQL Data Warehouse.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 01/24/2019
ms.openlocfilehash: 57de2d9c63a4185997ac86056b9e3189ad66e478
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893134"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Esercitazione: Estrarre, trasformare e caricare dati con Azure Databricks

In questa esercitazione viene eseguita un'operazione ETL (Extract, Transform, Load, estrazione, trasformazione e caricamento dei dati) tramite Azure Databricks. I dati vengono estratti da Azure Data Lake Storage Gen2 in Azure Databricks, vengono sottoposti a trasformazioni in Azure Databricks e dopo vengono caricati in Azure SQL Data Warehouse.

I passaggi in questa esercitazione usano il connettore di SQL Data Warehouse per Azure Databricks per trasferire i dati in Azure Databricks. Questo connettore usa a sua volta l'Archiviazione BLOB di Azure come archivio temporaneo per i dati trasferiti tra un cluster di Azure Databricks e Azure SQL Data Warehouse.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un servizio Azure Databricks.
> * Creare un cluster Spark in Azure Databricks.
> * Creare un file system e caricare i dati in Azure Data Lake Storage Gen2.
> * Creare un'entità servizio.
> * Estrarre i dati da Data Lake Store.
> * Trasformare i dati in Azure Databricks.
> * Caricare i dati in Azure SQL Data Warehouse.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Completare queste attività prima di iniziare questa esercitazione:

* Creare un'istanza di Azure SQL Data Warehouse, creare una regola del firewall a livello di server e connettersi al server come amministratore del server. Vedere [Avvio rapido: Creare un'istanza di Azure SQL Data Warehouse](../sql-data-warehouse/create-data-warehouse-portal.md).

* Creare una chiave master del database per Azure SQL Data Warehouse. Vedere [Creare una chiave master del database](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Creare un account di Azure Data Lake Storage Gen2. Vedere [Creare un account di Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Creare un account di archiviazione BLOB di Azure e un contenitore all'interno di tale account. Recuperare anche la chiave di accesso per accedere all'account di archiviazione. Vedere [Avvio rapido: Creare un account di archiviazione BLOB di Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

* Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-service"></a>Creare un servizio Azure Databricks

In questa sezione si crea un servizio Azure Databricks con il portale di Azure.

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **Azure Databricks**.

    ![Databricks nel portale di Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks nel portale di Azure")

2. In **Servizio Azure Databricks** specificare i valori seguenti per creare un servizio Databricks:

    |Proprietà  |Descrizione  |
    |---------|---------|
    |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks.        |
    |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Posizione**     | Selezionare **Stati Uniti occidentali 2**.  Per le altre aree disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).      |
    |**Piano tariffario**     |  Selezionare **Standard**.     |

3. Selezionare **Aggiungi al dashboard** e quindi **Crea**.

4. La creazione dell'account richiede alcuni minuti, durante i quali il portale visualizza il riquadro **Invio della distribuzione per Azure Databricks** a destra. Per monitorare lo stato dell'operazione, visualizzare l'indicatore di stato nella parte superiore.

    ![Riquadro di distribuzione Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Riquadro di distribuzione Databricks")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Creare un cluster Spark in Azure Databricks

1. Nel portale di Azure passare al servizio Databricks creato e selezionare **Launch Workspace** (Avvia l'area di lavoro).

2. Si verrà reindirizzati al portale di Azure Databricks. Nel portale selezionare **Cluster**.

    ![Databricks in Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks in Azure")

3. Nella pagina **New cluster** (Nuovo cluster) specificare i valori per creare un cluster.

    ![Creare il cluster Databricks Spark in Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Creare il cluster Databricks Spark in Azure")

4. Specificare i valori per i campi seguenti e accettare i valori predefiniti per gli altri campi:

    * Immettere un nome per il cluster.

    * Per questo articolo, creare un cluster con il runtime **5.1**.

    * Assicurarsi di selezionare la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di inattività). Se il cluster non viene usato, specificare una durata in minuti per terminarlo.

    * Selezionare **Crea cluster**. Quando il cluster è in esecuzione, è possibile collegarvi notebook ed eseguire processi Spark.

## <a name="create-a-file-system-and-upload-sample-data"></a>Creare un file system e caricare i dati di esempio

Creare prima di tutto un file system nell'account di Data Lake Storage Gen2. Quindi è possibile caricare un file di dati di esempio in Data Lake Store. Questo file viene usato in seguito in Azure Databricks per eseguire alcune trasformazioni.

1. Scaricare il file di dati di esempio [small_radio_json.json](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) nel file system locale.

2. Nel [portale di Azure](https://portal.azure.com/) passare all'account di Data Lake Storage Gen2 creato come prerequisito di questa esercitazione.

3. Nella pagina **Panoramica** dell'account di archiviazione selezionare **Apri in Explorer**.

   ![Aprire Storage Explorer](./media/databricks-extract-load-sql-data-warehouse/data-lake-storage-open-storage-explorer.png "Aprire Storage Explorer")

4. Selezionare **Apri Azure Storage Explorer** per aprire Storage Explorer.

   ![Secondo prompt per aprire Storage Explorer](./media/databricks-extract-load-sql-data-warehouse/data-lake-storage-open-storage-explorer-2.png "Secondo prompt per aprire Storage Explorer")

   Viene visualizzato Storage Explorer. È possibile creare un file system e caricare i dati di esempio seguendo le indicazioni di questo argomento: [Guida introduttiva: Usare Azure Storage Explorer per gestire i dati in un account di Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-explorer.md).

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Creare un'entità servizio seguendo le indicazioni fornite in questo argomento: [Procedura: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Mentre si completano le procedure descritte in tale articolo è necessario eseguire alcune operazioni.

:heavy_check_mark: Quando si esegue la procedura descritta nella sezione [Assegnare l'applicazione a un ruolo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) dell'articolo, assicurarsi di assegnare l'applicazione al **Ruolo di collaboratore di archiviazione Blob**.

:heavy_check_mark: Quando si esegue la procedura descritta nella sezione [Ottenere i valori per l'accesso](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) dell'articolo, incollare l'ID tenant, l'ID applicazione e i valori della chiave di autenticazione in un file di testo. Saranno necessari a breve.
Creare prima di tutto un notebook nell'area di lavoro di Azure Databricks e quindi eseguire frammenti di codice per creare il file system nell'account di archiviazione.

## <a name="extract-data-from-the-data-lake-store"></a>Estrarre i dati da Data Lake Store

In questa sezione viene creato un notebook nell'area di lavoro di Azure Databricks e quindi vengono eseguiti frammenti di codice per estrarre dati da Data Lake Store in Azure Databricks.

1. Nel [portale di Azure](https://portal.azure.com) passare al servizio Azure Databricks creato e selezionare **Launch Workspace** (Avvia l'area di lavoro).

2. A sinistra selezionare **Workspace** (Area di lavoro). Nell'elenco a discesa **Workspace** (Area di lavoro) selezionare **Create (Crea)** > **Notebook**.

    ![Creare un notebook in Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Creare un notebook in Databricks")

3. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome per il notebook. Selezionare **Scala** come linguaggio e quindi selezionare il cluster Spark creato in precedenza.

    ![Specificare i dettagli per un notebook in Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Specificare i dettagli per un notebook in Databricks")

4. Selezionare **Create**.

5. Copiare e incollare il blocco di codice seguente nella prima cella.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   ```

6. In questo blocco di codice sostituire i valori segnaposto `application-id`, `authentication-id` e `tenant-id` con i valori raccolti completando i passaggi descritti in Esaminare l'account di archiviazione. Sostituire il valore segnaposto `storage-account-name` con il nome del proprio account di archiviazione.

7. Premere **MAIUSC + INVIO** per eseguire il codice in questo blocco.

8. È ora possibile caricare il file JSON di esempio come dataframe in Azure Databricks. Incollare il codice seguente in una nuova cella. Sostituire i segnaposto tra parentesi quadre con i valori.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Sostituire il valore segnaposto `file-system-name` con il nome assegnato al file system in Storage Explorer.

   * Sostituire il segnaposto `storage-account-name` con il nome del proprio account di archiviazione.

9. Premere **MAIUSC + INVIO** per eseguire il codice in questo blocco.

10. Eseguire il codice seguente per visualizzare il contenuto del dataframe:

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

I dati di esempio non elaborati del file **small_radio_json.json** sono relativi agli ascoltatori di una stazione radio e includono diverse colonne. In questa sezione i dati vengono trasformati in modo da recuperare solo colonne specifiche dal set di dati.

1. Recuperare prima di tutto solo le colonne **firstName**, **lastName**, **gender**, **location** e **level** dal dataframe già creato.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   L'output restituito sarà simile al frammento di codice seguente:

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

2. È possibile trasformare ulteriormente questi dati per rinominare la colonna **level** in **subscription_type**.

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   L'output restituito sarà simile al frammento di codice seguente.

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

In questa sezione i dati trasformati vengono caricati in Azure SQL Data Warehouse. Usare il connettore di Azure SQL Data Warehouse per Azure Databricks per caricare direttamente un dataframe come tabella in SQL Data Warehouse.

Come indicato in precedenza, il connettore di SQL Data Warehouse usa l'Archiviazione BLOB di Azure come archivio temporaneo per caricare i dati tra Azure Databricks e Azure SQL Data Warehouse. Specificare quindi prima di tutto la configurazione per la connessione all'account di archiviazione. È necessario che l'account sia già stato creato come parte dei prerequisiti per questo articolo.

1. Specificare la configurazione per l'accesso all'account di archiviazione di Azure da Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Specificare una cartella temporanea da usare durante lo spostamento dei dati tra Azure Databricks e Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Eseguire il frammento di codice seguente per archiviare le chiavi di accesso dell'Archiviazione BLOB di Azure nella configurazione. In questo modo si assicura che non sia necessario mantenere la chiave di accesso nel notebook in testo normale.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Specificare i valori per la connessione all'istanza di Azure SQL Data Warehouse. È necessario che sia stata creata un'istanza di SQL Data Warehouse come prerequisito.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Eseguire il frammento di codice seguente per caricare il dataframe trasformato, **renamedColumnsDf**, come tabella in SQL Data Warehouse. Il frammento di codice crea una tabella denominata **SampleTable** nel database SQL.

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

6. Connettersi al database SQL e verificare che sia visualizzato un database denominato **SampleTable**.

   ![Verificare la tabella di esempio](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Verificare la tabella di esempio")

7. Eseguire una query selezionata per verificare i contenuti della tabella. Dovrebbe includere gli stessi dati del dataframe **renamedColumnsDf**.

    ![Verificare il contenuto della tabella di esempio](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Verificare il contenuto della tabella di esempio")

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver concluso l'esercitazione è possibile terminare il cluster. Nell'area di lavoro di Azure Databricks selezionare **Clusters** a sinistra. Per terminare il cluster, in **Actions** (Azioni) selezionare i puntini di sospensione (...) e quindi l'icona **Terminate** (Terminate).

![Arrestare un cluster Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Arrestare un cluster Databricks")

Se non viene terminato manualmente, il cluster si arresta automaticamente se è stata selezionata la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di inattività) quando è stato creato. In tal caso, il cluster viene automaticamente arrestato se è rimasto inattivo per il tempo specificato.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un servizio Azure Databricks
> * Creare un cluster Spark in Azure Databricks
> * Creare un notebook in Azure Databricks
> * Estrarre i dati da un account Data Lake Storage Gen2
> * Trasformare dati in Azure Databricks
> * Caricare i dati in Azure SQL Data Warehouse

Passare all'esercitazione successiva per ottenere informazioni sullo streaming di dati in tempo reale in Azure Databricks tramite Hub eventi di Azure.

> [!div class="nextstepaction"]
>[Trasmettere dati in Azure Databricks tramite Hub eventi](databricks-stream-from-eventhubs.md)
