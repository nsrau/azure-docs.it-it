---
title: 'Esercitazione: Accedere ai dati di Azure Data Lake Storage Gen2 con Azure Databricks usando Spark | Microsoft Docs'
description: Questa esercitazione mostra come eseguire query Spark su un cluster Azure Databricks per accedere ai dati in un account di archiviazione Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: b332c11e76ad335772cc607edcf569f896acb873
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951389"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>Esercitazione: Accedere ai dati di Data Lake Storage Gen2 con Azure Databricks usando Spark

Questa esercitazione illustra come connettere un cluster di Azure Databricks ai dati archiviati in un account di archiviazione di Azure per cui è abilitato Azure Data Lake Storage Gen2. Questa connessione permette di eseguire in modo nativo query e analisi dal cluster sui dati.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un cluster di Databricks
> * Inserire dati non strutturati in un account di archiviazione
> * Eseguire analisi sui dati nell'archivio BLOB

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Creare un account di Azure Data Lake Storage Gen2.

  Vedere [Creare un account di Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Verificare che all'account utente sia assegnato il [ruolo di collaboratore ai dati del BLOB di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac).

* Installare AzCopy v10. Vedere [Trasferire dati con AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* Creare un'entità servizio. Vedere [Procedura: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Mentre si completano le procedure descritte in tale articolo è necessario eseguire alcune operazioni specifiche.

  :heavy_check_mark: Quando si esegue la procedura descritta nella sezione [Assegnare l'applicazione a un ruolo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) dell'articolo, assicurarsi di assegnare il ruolo **Collaboratore ai dati del BLOB di archiviazione** all'entità servizio.

  > [!IMPORTANT]
  > Assicurarsi di assegnare il ruolo nell'ambito dell'account di archiviazione Data Lake Storage Gen2. È possibile assegnare un ruolo al gruppo di risorse padre o alla sottoscrizione, ma si riceveranno errori relativi alle autorizzazioni fino a quando tali assegnazioni di ruolo non si propagheranno all'account di archiviazione.

  :heavy_check_mark: Quando si esegue la procedura descritta nella sezione [Ottenere i valori per l'accesso](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) dell'articolo, incollare l'ID tenant, l'ID app e i valori della password in un file di testo. Saranno necessari a breve.

### <a name="download-the-flight-data"></a>Scaricare i dati relativi ai voli

In questa esercitazione vengono usati dati sui voli del Bureau of Transportation Statistics per dimostrare come eseguire un'operazione ETL. Per completare l'esercitazione, è necessario scaricare questi dati.

1. Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Selezionare la casella di controllo **Prezipped File** (File precompresso) per selezionare tutti i campi dati.

3. Selezionare il pulsante **Download** (Scarica) e salvare i risultati sul computer in uso. 

4. Decomprimere il contenuto del file compresso e prendere nota del nome e del percorso del file. Queste informazioni saranno necessarie in un passaggio successivo.

## <a name="create-an-azure-databricks-service"></a>Creare un servizio Azure Databricks

In questa sezione si crea un servizio Azure Databricks con il portale di Azure.

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **Azure Databricks**.

    ![Databricks nel portale di Azure](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks nel portale di Azure")

2. In **Servizio Azure Databricks** specificare i valori seguenti per creare un servizio Databricks:

    |Proprietà  |Descrizione  |
    |---------|---------|
    |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks.  |
    |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Posizione**     | Selezionare **Stati Uniti occidentali 2**. Per le altre aree disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).       |
    |**Piano tariffario**     |  Selezionare **Standard**.     |

    ![Creare un'area di lavoro di Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Creare un servizio Azure Databricks")

3. La creazione dell'account richiede alcuni minuti, Per monitorare lo stato dell'operazione, visualizzare l'indicatore di stato nella parte superiore.

4. Selezionare **Aggiungi al dashboard** e quindi **Crea**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Creare un cluster Spark in Azure Databricks

1. Nel portale di Azure passare al servizio Databricks creato e selezionare **Launch Workspace** (Avvia l'area di lavoro).

2. Si verrà reindirizzati al portale di Azure Databricks. Nel portale selezionare **Cluster**.

    ![Databricks in Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks in Azure")

3. Nella pagina **New cluster** (Nuovo cluster) specificare i valori per creare un cluster.

    ![Creare il cluster Databricks Spark in Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Creare il cluster Databricks Spark in Azure")

4. Specificare i valori per i campi seguenti e accettare i valori predefiniti per gli altri campi:

    * Immettere un nome per il cluster.

    * Per questo articolo, creare un cluster con il runtime **5.1**.

    * Assicurarsi di selezionare la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di inattività). Se il cluster non viene usato, specificare una durata in minuti per terminarlo.

    * Selezionare **Crea cluster**. Quando il cluster è in esecuzione, è possibile collegarvi notebook ed eseguire processi Spark.

## <a name="ingest-data"></a>Inserire dati

### <a name="copy-source-data-into-the-storage-account"></a>Copiare i dati di origine nell'account di archiviazione

Usare AzCopy per copiare i dati dal file con estensione *csv* all'account Data Lake Storage Gen2.

1. Aprire una finestra del prompt dei comandi e immettere il comando seguente per accedere all'account di archiviazione.

   ```bash
   azcopy login
   ```

   Seguire le istruzioni visualizzate nella finestra del prompt dei comandi per autenticare l'account utente.

2. Per copiare dati dal file con estensione *csv* all'account, immettere il comando seguente.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<file-system-name>/folder1/On_Time.csv
   ```

   * Sostituire il valore segnaposto `<csv-folder-path>` con il percorso del file *CSV*.

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

   * Sostituire il segnaposto `<file-system-name>` con il nome che si vuole assegnare al file system.

## <a name="create-a-file-system-and-mount-it"></a>Creare e montare un file system

In questa sezione si creeranno un file system e una cartella nell'account di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) passare al servizio Azure Databricks creato e selezionare **Launch Workspace** (Avvia l'area di lavoro).

2. A sinistra selezionare **Workspace** (Area di lavoro). Nell'elenco a discesa **Workspace** (Area di lavoro) selezionare **Create (Crea)**  > **Notebook**.

    ![Creare un notebook in Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Creare un notebook in Databricks")

3. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome per il notebook. Selezionare **Python** come linguaggio e quindi il cluster Spark creato in precedenza.

4. Selezionare **Create**.

5. Copiare e incollare il blocco di codice seguente nella prima cella, ma non eseguirlo ancora.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<password>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. In questo blocco di codice sostituire i valori segnaposto `appId`, `password`, `tenant` e `storage-account-name` con i valori raccolti completando i prerequisiti di questa esercitazione. Sostituire il valore segnaposto `file-system-name` con il nome assegnato al file system ADLS nel passaggio precedente.

Usare questi valori per sostituire i segnaposto menzionati.

   * `appId` e `password` provengono dall'app che è stata registrata con Active Directory durante la creazione di un'entità servizio.

   * `tenant-id` proviene dalla sottoscrizione.

   * `storage-account-name` è il nome dell'account di archiviazione Azure Data Lake Storage Gen2.

   * Sostituire il segnaposto `file-system-name` con il nome che si vuole assegnare al file system.

   > [!NOTE]
   > In un ambiente di produzione è consigliabile archiviare la password in Azure Databricks. Aggiungere quindi una chiave di ricerca al blocco di codice invece della password. Per alcuni esempi di questo approccio, vedere l'articolo [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) nel sito Web di Azure Databricks.

19. Premere **MAIUSC + INVIO** per eseguire il codice in questo blocco.

   Tenere aperto il notebook, perché in seguito vi si aggiungeranno comandi.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Usare un notebook di Databricks per convertire il formato CSV in Parquet

Nel notebook creato in precedenza aggiungere una nuova cella e incollarvi il codice seguente. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Esplorazione dei dati

In una nuova cella incollare il codice seguente per ottenere un elenco dei file CSV caricati tramite AzCopy.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Per creare un nuovo file ed elencare i file nella cartella *parquet/flights*, eseguire questo script:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Con questi esempi di codice è stata esaminata la natura gerarchica di HDFS usando i dati archiviati in un account di archiviazione con Azure Data Lake Storage Gen2 abilitato.

## <a name="query-the-data"></a>Eseguire query sui dati

È quindi possibile iniziare a eseguire query sui dati caricati nell'account di archiviazione. Immettere ognuno dei blocchi di codice seguenti in **Cmd 1** e premere **Cmd+INVIO** per eseguire lo script Python.

Per creare frame di dati per le origini dati, eseguire questo script:

* Sostituire il valore segnaposto `<csv-folder-path>` con il percorso del file *CSV*.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferably run this in a separate cmd cell
display(flightDF)
```

Immettere questo script per eseguire alcune query di analisi di base sui dati.

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse per l'account di archiviazione e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Estrarre, trasformare e caricare dati usando Apache Hive in Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
