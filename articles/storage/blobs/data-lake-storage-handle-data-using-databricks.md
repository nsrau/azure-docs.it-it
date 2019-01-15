---
title: 'Esercitazione: Informazioni su come eseguire operazioni di estrazione, caricamento e trasferimento con Azure Databricks'
description: In questa esercitazione si apprenderà come estrarre dati dalla versione di anteprima di Azure Data Lake Storage Gen2 in Azure Databricks, trasformarli e quindi caricarli in Azure SQL Data Warehouse.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 12/06/2018
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 6b2812e31174c4e5d61ae9941563e39357de9522
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107090"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Esercitazione: Estrarre, trasformare e caricare dati con Azure Databricks

In questa esercitazione si userà Azure Databricks per eseguire un'operazione ETL (Extract, Transform, Load), ossia estrazione, trasformazione e caricamento dei dati. I dati verranno spostati da un account di Archiviazione di Azure con Azure Data Lake Storage Gen2 abilitato ad Azure SQL Data Warehouse.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'area di lavoro di Azure Databricks.
> * Creare un cluster Spark in Azure Databricks.
> * Creare un account con supporto di Azure Data Lake Storage Gen2.
> * Caricare dati in Azure Data Lake Storage Gen2.
> * Creare un notebook in Azure Databricks.
> * Estrarre i dati da Data Lake Storage Gen2.
> * Trasformare i dati in Azure Databricks.
> * Caricare i dati in Azure SQL Data Warehouse.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* Creare un'istanza di Azure SQL Data Warehouse, creare una regola del firewall a livello di server e connettersi al server come amministratore del server. Seguire le istruzioni contenute nell'articolo [Avvio rapido: Creare un'istanza di Azure SQL Data Warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md).
* Creare una chiave master del database per Azure SQL Data Warehouse. Seguire le istruzioni riportate nell'articolo [Creare una chiave master di un database](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
* [Creare un account di Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* Scaricare (**small_radio_json**) dalle [esempi U-SQL e gestione problemi](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) repository e prendere nota del percorso in cui salvare il file.
* Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-the-workspace"></a>Creare l'area di lavoro

In questa sezione viene creata un'area di lavoro di Azure Databricks usando il portale di Azure.

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **Azure Databricks**.

    ![Databricks nel portale di Azure](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Databricks nel portale di Azure")

1. Nella pagina **Servizio Azure Databricks** specificare i valori per creare un'area di lavoro di Databricks:

    |Proprietà  |Descrizione  |
    |---------|---------|
    |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks.        |
    |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Posizione**     | Selezionare **Stati Uniti occidentali 2**.        |
    |**Piano tariffario**     |  Selezionare **Standard**.     |

    ![Creare un'area di lavoro di Azure Databricks](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Creare un'area di lavoro di Azure Databricks")

1. Selezionare **Aggiungi al dashboard** e quindi **Crea**.

1. La creazione dell'account richiede alcuni minuti, durante i quali il portale visualizza il riquadro **Invio della distribuzione per Azure Databricks** a destra. Per monitorare lo stato dell'operazione, visualizzare l'indicatore di stato nella parte superiore.

    ![Riquadro di distribuzione Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Riquadro di distribuzione Databricks")

## <a name="create-the-spark-cluster"></a>Creare il cluster Spark

Per eseguire le operazioni di questa esercitazione, è necessario un cluster Spark. Usare i passaggi seguenti per crearlo.

1. Nel portale di Azure passare all'area di lavoro di Databricks creata e selezionare **Avvia area di lavoro**.

1. Si verrà reindirizzati al portale di Azure Databricks. Nel portale selezionare **Cluster**.

    ![Databricks in Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks in Azure")

1. Nella pagina **New cluster** (Nuovo cluster) specificare i valori per creare un cluster.

    ![Creare il cluster Databricks Spark in Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Creare il cluster Databricks Spark in Azure")

1. Specificare i valori per i campi seguenti e accettare i valori predefiniti per gli altri campi:

    * Immettere un nome per il cluster.
    * Per questo articolo, creare un cluster con il runtime **5.1**.
    * Assicurarsi di selezionare la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo ___ minuti di inattività). Se il cluster non viene usato, specificare una durata in minuti per terminarlo.

1. Selezionare **Crea cluster**.

Quando il cluster è in esecuzione, è possibile collegarvi notebook ed eseguire processi Spark.

## <a name="create-a-file-system"></a>Creare un file system

Per archiviare i dati nell'account di archiviazione di Data Lake Storage Gen2, è necessario creare un file system.

Creare prima di tutto un notebook nell'area di lavoro di Azure Databricks e quindi eseguire frammenti di codice per creare il file system nell'account di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) passare all'area di lavoro di Azure Databricks creata e selezionare **Avvia area di lavoro**.

1. A sinistra selezionare **Workspace** (Area di lavoro). Nell'elenco a discesa **Workspace** (Area di lavoro) selezionare **Create (Crea)** > **Notebook**.

    ![Creare un notebook in Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Creare un notebook in Databricks")

1. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome per il notebook. Selezionare **Scala** come linguaggio e quindi selezionare il cluster Spark creato in precedenza.

    ![Specificare i dettagli per un notebook in Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Specificare i dettagli per un notebook in Databricks")

    Selezionare **Create**.

1. Immettere il codice seguente nella prima cella del notebook ed eseguirlo. Sostituire i valori segnaposto racchiusi tra parentesi nell'esempio con i propri valori:

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

1. Premere MAIUSC+INVIO per eseguire il codice.

A questo punto è stato creato il file system per l'account di archiviazione.

## <a name="upload-the-sample-data"></a>Caricare i dati di esempio

Il passaggio successivo consiste nel caricare un file di dati di esempio nell'account di archiviazione per trasformarlo successivamente in Azure Databricks.

Caricare i dati di esempio scaricati nell'account di archiviazione. Il metodo usato per caricare i dati nell'account di archiviazione è diverso a seconda che sia abilitato o meno lo spazio dei nomi gerarchico.

Per il caricamento, è possibile usare Azure Data Factory, distp o AzCopy (versione 10). La versione 10 di AzCopy è attualmente disponibile tramite anteprima. Per usare AzCopy, copiare il codice seguente in una finestra di comando:

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>Estrarre i dati

Per usare i dati di esempio in Databricks, è necessario estrarli dall'account di archiviazione.

Tornare al notebook di Databricks e immettere il codice seguente in una nuova cella.

Aggiungere il frammento seguente in una cella di codice vuota. Sostituire i segnaposto tra parentesi quadre con i valori salvati in precedenza dall'account di archiviazione.

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

Premere MAIUSC+INVIO per eseguire il codice.

È ora possibile caricare il file JSON di esempio come frame di dati in Azure Databricks. Incollare il codice seguente in una nuova cella. Sostituire i segnaposto tra parentesi quadre con i valori.

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

Premere MAIUSC+INVIO per eseguire il codice.

Eseguire il codice seguente per visualizzare il contenuto del dataframe:

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

## <a name="transform-the-data"></a>Trasformare i dati

I dati di esempio non elaborati del file **small_radio_json.json** sono relativi agli ascoltatori di una stazione radio e includono diverse colonne. In questa sezione i dati vengono trasformati in modo da recuperare solo colonne specifiche dal set di dati.

Recuperare prima di tutto solo le colonne **firstName**, **lastName**, **gender**, **location** e **level** dal dataframe già creato.

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
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

È possibile trasformare ulteriormente questi dati per rinominare la colonna **level** in **subscription_type**.

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

## <a name="load-the-data"></a>Caricare i dati

In questa sezione i dati trasformati vengono caricati in Azure SQL Data Warehouse. Usare il connettore di Azure SQL Data Warehouse per Azure Databricks per caricare direttamente un dataframe come tabella in SQL Data Warehouse.

Il connettore di SQL Data Warehouse usa l'archiviazione BLOB di Azure come archivio temporaneo per caricare i dati tra Azure Databricks e Azure SQL Data Warehouse. Specificare quindi prima di tutto la configurazione per la connessione all'account di archiviazione. È necessario che l'account sia già stato creato come parte dei prerequisiti per questo articolo.

Specificare la configurazione per l'accesso all'account di archiviazione di Azure da Azure Databricks.

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

Specificare una cartella temporanea da usare durante lo spostamento dei dati tra Azure Databricks e Azure SQL Data Warehouse.

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

Eseguire il frammento di codice seguente per archiviare le chiavi di accesso dell'Archiviazione BLOB di Azure nella configurazione. In questo modo si assicura che non sia necessario mantenere la chiave di accesso nel notebook in testo normale.

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

Specificare i valori per la connessione all'istanza di Azure SQL Data Warehouse. È necessario che sia stata creata un'istanza di SQL Data Warehouse come prerequisito.

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

Eseguire il frammento di codice seguente per caricare il dataframe trasformato, **renamedColumnsDf**, come tabella in SQL Data Warehouse. Il frammento di codice crea una tabella denominata **SampleTable** nel database SQL.

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

Connettersi al database SQL e verificare che sia visualizzato un database denominato **SampleTable**.

![Verificare la tabella di esempio](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Verificare la tabella di esempio")

Eseguire una query selezionata per verificare i contenuti della tabella. Dovrebbe includere gli stessi dati del dataframe **renamedColumnsDf**.

![Verificare il contenuto della tabella di esempio](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Verificare il contenuto della tabella di esempio")

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver concluso l'esercitazione è possibile terminare il cluster. Nell'area di lavoro di Azure Databricks selezionare **Clusters** a sinistra. Per terminare il cluster, in **Actions** (Azioni) selezionare i puntini di sospensione (...) e quindi l'icona **Terminate** (Terminate).

![Arrestare un cluster Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Arrestare un cluster Databricks")

Se non viene terminato manualmente, il cluster si arresta automaticamente se è stata selezionata la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo __ minuti di inattività) quando è stato creato. In tal caso, il cluster viene automaticamente arrestato se è rimasto inattivo per il tempo specificato.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per informazioni su come trasmettere dati in tempo reale in Azure Databricks tramite Hub eventi di Azure.

> [!div class="nextstepaction"]
>[Trasmettere dati in Azure Databricks tramite Hub eventi](../../azure-databricks/databricks-stream-from-eventhubs.md)
