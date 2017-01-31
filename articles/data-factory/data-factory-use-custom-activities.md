---
title: "Usare attività personalizzate in una pipeline di Azure Data Factory"
description: "Informazioni su come creare attività personalizzate e usarle in una pipeline di Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 691fd286fa41f26f192354574c460bd88d9c2bce
ms.openlocfilehash: 1222f736a71f38611ae72a2591bfdc893d91eb6e


---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Usare attività personalizzate in una pipeline di Azure Data Factory

> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md) 
> * [Pig](data-factory-pig-activity.md)
> * [MapReduce](data-factory-map-reduce.md)
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata .NET](data-factory-use-custom-activities.md)
>


In una pipeline di Azure Data Factory è possibile usare due tipi di attività.

* [Attività di spostamento dei dati](data-factory-data-movement-activities.md) per spostare i dati fra [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
* [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md) per trasformare/elaborare i dati usando calcoli come Azure HDInsight, Azure Batch e Azure Machine Learning. Ad esempio: HDInsight Hive e Machine Learning Batch Execution.  

Se è necessario spostare dati da e verso un archivio dati non supportato da Azure Data Factory, è possibile creare un'attività .NET personalizzata contenente la logica di spostamento dei dati richiesta e usarla nella pipeline.

Analogamente, se è necessario trasformare o elaborare dati in un modo non supportato da Data Factory, è possibile creare un’attività personalizzata contenente la logica di elaborazione dei dati richiesta e usarla nella pipeline.

> [!NOTE]
> Attualmente, il Gateway di gestione dati supporta solo le attività di copia e attività di stored procedure in Data Factory. Non è possibile usare il gateway da un'attività personalizzata per accedere alle origini dati locali.

È possibile configurare l'attività .NET personalizzata da eseguire usando il servizio **Azure Batch** o un cluster **Azure HDInsight**.   

La procedura dettagliata seguente riporta le istruzioni complete per creare un'attività .NET personalizzata e usarla in una pipeline. La procedura usa il servizio collegato **Azure Batch** . Per usare invece il servizio collegato Azure HDInsight, si crea un servizio collegato di tipo **HDInsight** (se si usa un proprio cluster HDInsight) o **HDInsightOnDemand** (se si desidera che Data Factory crei un cluster HDInsight su richiesta) e lo si usa nella sezione attività della pipeline JSON (**linkedServiceName**). Per i dettagli sull’uso di Azure HDInsight per eseguire l’attività personalizzata vedere [Usare i servizi collegati Azure HDInsight](#use-azure-hdinsight-linked-services) .

> [!IMPORTANT]
> Impostare la versione 4.5.2 di .NET Framework come framework di destinazione per il progetto di attività personalizzata .NET in Visual Studio. Data factory non supporta le attività personalizzate compilate in versioni di .NET Framework successive alla 4.5.2.   

## <a name="walkthrough"></a>Procedura dettagliata
### <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2012/2013/2015
* Scaricare e installare [Azure .NET SDK][azure-developer-center]

### <a name="azure-batch-prerequisites"></a>Prerequisiti di Azure Batch
In questa procedura dettagliata vengono eseguite attività .NET personalizzate usando Azure Batch come risorsa di calcolo. Per una panoramica del servizio Azure Batch, vedere [Nozioni di base su Azure Batch][batch-technical-overview]. Per iniziare rapidamente a usare il servizio, vedere [Introduzione alla libreria di Azure Batch per .NET][batch-get-started].

Per l'esercitazione è necessario creare un account Azure Batch con un pool di macchine virtuali. Di seguito sono riportati i passaggi necessari:

1. Creare un **account di Azure Batch** tramite il [portale di Azure](http://manage.windowsazure.com). Per istruzioni, vedere l'articolo su come [creare e gestire un account Azure Batch][batch-create-account]. Annotare il nome e la chiave dell'account Azure Batch.

    È anche possibile usare il cmdlet [New-AzureBatchAccount][new-azure-batch-account] per creare un account Azure Batch. Per istruzioni dettagliate sull'uso del cmdlet, vedere [Using Azure PowerShell to Manage Azure Batch Account][azure-batch-blog] (Uso di Azure PowerShell per gestire un account Azure Batch).
2. Creare un **pool di Azure Batch**.

   1. Nel menu a sinistra del [portale di Azure](https://portal.azure.com) fare clic su **Esplora** e quindi su **Account Batch**.
   2. Selezionare il proprio account Azure Batch per aprire il pannello **Account Batch** .
   3. Fare clic sul riquadro **Pool** .
   4. Nel riquadro **pool** fare clic sul pulsante Aggiungi nella barra degli strumenti per aggiungere un pool.
      1. Immettere un ID per il pool (**ID pool**). Prendere nota dell' **ID del pool**perché sarà necessario durante la creazione della soluzione Data Factory.
      2. Specificare **Windows Server 2012 R2** per l'impostazione Famiglia di sistemi operativi.
      3. Selezionare un **piano tariffario per il nodo**.
      4. Immettere **2** come valore per l'impostazione **Pool dedicati di destinazione**.
      5. Immettere **2** come valore per l'impostazione **Numero massimo attività per nodo**.
   5. Fare clic su **OK** per creare il pool.

      Inoltre, è possibile utilizzare [New-AzureBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) per creare un pool di Azure Batch.     

### <a name="high-level-steps"></a>Procedure generali
1. **Creare un'attività personalizzata** da usare in una pipeline di Data Factory. L'attività personalizzata di questo esempio contiene la logica di trasformazione/elaborazione dei dati.
   1. In Visual Studio creare un progetto Libreria di classi .NET, aggiungere il codice per l'elaborazione dei dati di input e compilare il progetto.    
   2. Comprimere tutti i file binari e il file PDB (facoltativo) nella cartella di output.    
   3. Caricare il file ZIP nell'archiviazione BLOB di Azure. La procedura dettagliata è illustrata nella sezione Creare l'attività personalizzata.
2. **Creare una data factory di Azure che usa l'attività personalizzata**:
   1. Creare un'istanza di Azure Data Factory.
   2. Creare servizi collegati.
      1. AzureStorageLinkedService: fornisce le credenziali di archiviazione per accedere ai BLOB.
      2. AzureBatchLinkedService: specifica Azure Batch come risorsa di calcolo.
   3. Creare set di dati.
      1. InputDataset: specifica il contenitore di archiviazione e la cartella per i BLOB di input.
      2. OuputDataset: specifica il contenitore di archiviazione e la cartella per i BLOB di output.
   4. Creare una pipeline che usi l'attività personalizzata.
   5. Eseguire e testare la pipeline.
   6. Eseguire il debug della pipeline.

## <a name="create-the-custom-activity"></a>Creare l'attività personalizzata
Per creare un'attività personalizzata .NET, è necessario creare un progetto **Libreria di classi .NET** con una classe che implementa l'**interfaccia IDotNetActivity**. Questa interfaccia ha un solo metodo, [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) , e la relativa firma è:

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


Il metodo accetta quattro parametri:

* **linkedServices**. Questa proprietà rappresenta un elenco enumerabile di servizi collegati che collegano origini dati di input/output (ad esempio, l'archiviazione BLOB di Azure) alla data factory. In questo esempio c'è un solo servizio collegato di tipo Archiviazione di Azure usato sia per l'input che per l'output.
* **datasets**. Questa proprietà rappresenta un elenco enumerabile di set di dati. È possibile usare questo parametro per ottenere le posizioni e gli schemi definiti da set di dati di input e di output.
* **activity**. Questa proprietà rappresenta l'entità di calcolo corrente, in questo caso un Azure Batch.
* **logger**. Questo oggetto consente di scrivere commenti di debug che verranno visualizzati come log dell'utente per la pipeline.

Il metodo restituisce un dizionario che può essere usato per concatenare le attività personalizzate in un secondo momento. Questa funzionalità non è ancora implementata, quindi il metodo restituisce un dizionario vuoto.  

### <a name="procedure"></a>Procedura
1. Creare un progetto **Libreria di classi .NET** .
   <ol type="a">
     <li>Avviare <b>Visual Studio 2015</b> o <b>Visual Studio 2013</b> o <b>Visual Studio 2012</b>.</li>
     <li>Fare clic su <b>File</b>, scegliere <b>Nuovo</b> e quindi fare clic su <b>Progetto</b>.</li>
     <li>Espandere <b>Modelli</b> e quindi selezionare <b>Visual C#</b>. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET per sviluppare l'attività personalizzata.</li>
     <li>Selezionare la <b>libreria di classi</b> dall'elenco relativo ai tipi di progetto visualizzato a destra.</li>
     <li>Immettere <b>MyDotNetActivity</b> per <b>Nome</b>.</li>
     <li>Selezionare <b>C:\ADFGetStarted</b> per <b>Percorso</b>.</li>
     <li>Fare clic su <b>OK</b> per creare il progetto.</li>
   </ol>
2. Fare clic su **Strumenti**, scegliere **Gestione pacchetti NuGet** e quindi fare clic su **Console di Gestione pacchetti**.
3. In Console di Gestione pacchetti eseguire il comando seguente per importare **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importare nel progetto il pacchetto NuGet **Azure Storage** .

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!NOTE]
    > L'utilità di avvio del servizio Data Factory richiede la versione 4.3 di WindowsAzure.Storage. Se si aggiunge un riferimento a una versione successiva dell'assembly di Archiviazione di Azure nel progetto di attività personalizzato, viene visualizzato un errore durante l'esecuzione dell'attività. Per risolvere l'errore, vedere la sezione [Isolamento di AppDomain](#appdomain-isolation). 
5. Aggiungere le seguenti istruzioni **using** al file di origine nel progetto.

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Modificare il nome dello **spazio dei nomi** in **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Modificare il nome della classe in **MyDotNetActivity** e derivarlo dall'interfaccia **IDotNetActivity** come illustrato nel frammento di codice seguente:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementare (aggiungere) il metodo **Execute** dell'interfaccia **IDotNetActivity** nella classe **MyDotNetActivity** e copiare il seguente codice di esempio nel metodo.

    Nell’esempio seguente si conta il numero di occorrenze del termine di ricerca (“Microsoft”) in ogni BLOB associato con una sezione dei dati.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    
    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // to get extended properties (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];
    
        // to log all extended properties            
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }
    
        // declare types for input and output data stores
        AzureStorageLinkedService inputLinkedService;
    
        // declare dataset types
        CustomDataset inputLocation;
        AzureBlobDataset outputLocation;
    
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
        inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;
    
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);
    
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;
    
        string connectionString = inputLinkedService.ConnectionString;
    
        // To create an input storage client.
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.
    
        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);
    
            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
               // with the data slice. definition of the method is shown in the next step.
    
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
        } while (continuationToken != null);
    
        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
        // convert to blob location object.
        outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        folderPath = GetFolderPath(outputDataset);
    
        logger.Write("Writing blob to the folder: {0}", folderPath);
    
        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());
        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);
    
        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.  
    
        return new Dictionary<string, string>();
    }
    ```
9. Aggiungere i seguenti metodi helper. Il metodo **Execute** richiama questi metodi helper. Il metodo **GetConnectionString** consente di recuperare la stringa di connessione di Archiviazione di Azure, mentre il metodo **GetFolderPath** consente di recuperare il percorso BLOB. In particolare, il metodo **Calculate** isola il codice che esegue l'iterazione di ogni BLOB.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    
    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }
    
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        return blobDataset.FolderPath;
    }
    
    /// <summary>
    /// Gets the fileName value from the input/output dataset.   
    /// </summary>
    
    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }
    
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>
    
    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    Il metodo GetFolderPath restituisce il percorso della cartella a cui punta il set di dati e il metodo GetFileName restituisce il nome del BLOB o del file a cui punta il set di dati. Se sono presenti definizioni folderPath che usano variabili come {Year}, {Month}, {Day} e così via, il metodo restituisce la stringa così com’è, senza sostituirla con i valori di runtime. Per i dettagli sull'accesso a SliceStart, SliceEnd e così via, vedere [Accedere a tutte le proprietà estese](#access-extended-properties) .    

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Il metodo Calculate calcola il numero di istanze della parola chiave "Microsoft" nei file di input (BLOB nella cartella). Il termine di ricerca ("Microsoft") è hardcoded nel codice.
10. Compilare il progetto. Fare clic su **Compila** dal menu e scegliere **Compila soluzione**.

    > [!IMPORTANT]
    > Impostare la versione 4.5.2 di .NET Framework come framework di destinazione per il progetto: fare clic con il pulsante destre sul progetto e quindi su **Proprietà** per impostare il framework di destinazione. Data factory non supporta le attività personalizzate compilate in versioni di .NET Framework successive alla 4.5.2.

11. Avviare **Esplora risorse** e passare alla cartella **bin\debug** o **bin\release**, a seconda del tipo di compilazione.
12. Creare un file ZIP **MyDotNetActivity.zip** che contiene tutti i file binari disponibili nella cartella <project folder>\bin\Debug. È possibile includere il file **MyDotNetActivity.pdb** per ottenere altri dettagli, ad esempio il numero della riga nel codice sorgente che ha causato il problema in caso di errore. Tutti i file contenuti nel file con estensione zip dell'attività personalizzata devono trovarsi nel **livello principale** senza sottocartelle.

    ![File di output binari](./media/data-factory-use-custom-activities/Binaries.png)
13. Caricare **MyDotNetActivity.zip** come BLOB nel contenitore BLOB **customactivitycontainer** dell'archivio BLOB di Azure usato dal servizio collegato **AzureStorageLinkedService** in **ADFTutorialDataFactory**.  Se non è già presente, creare il contenitore BLOB **customactivitycontainer** .

> [!NOTE]
> Se si aggiunge questo progetto di attività .NET a una soluzione di Visual Studio che contiene un progetto data factory e si aggiunge un riferimento al progetto dell’attività .NET dal progetto dell’applicazione data factory, non è necessario eseguire gli ultimi due passaggi, ovvero creare manualmente il file ZIP e caricarlo nell'archiviazione BLOB di Azure. Quando si pubblicano entità Data Factory utilizzando Visual Studio, questi passaggi vengono eseguiti automaticamente dal processo di pubblicazione. Vedere gli articoli [Creare la prima pipeline con Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) e [Copiare dati da BLOB di Azure a SQL Azure](data-factory-copy-activity-tutorial-using-visual-studio.md) per informazioni sulla creazione e pubblicazione di entità di Data factory con Visual Studio.  
>
>

### <a name="execute-method"></a>Metodo Execute
Questa sezione fornisce informazioni dettagliate e note sul codice del metodo **Execute** .

1. I membri per l'iterazione della raccolta di input si trovano nello spazio dei nomi [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) . L'iterazione della raccolta di BLOB richiede l'uso della classe **BlobContinuationToken** . In sostanza, è necessario usare un ciclo do-while con il token come meccanismo di uscita dal ciclo. Per altre informazioni, vedere [Come usare l'archivio BLOB da .NET](../storage/storage-dotnet-how-to-use-blobs.md). Di seguito è illustrato un ciclo di base:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {   
        // Get the list of input blobs from the input storage client object.
        BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
            true,
            BlobListingDetails.Metadata,
            null,
            continuationToken,
            null,
            null);
        // Return a string derived from parsing each blob.
        output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    } while (continuationToken != null);
    ```

    Per informazioni dettagliate, vedere la documentazione relativa al metodo [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) .
2. Il codice per l'uso in modo logico del set di BLOB viene inserito all'interno del ciclo do-while. Nel metodo **Execute** il ciclo do-while passa l'elenco di BLOB a un metodo denominato **Calculate**. Il metodo restituisce una variabile stringa denominata **output** che rappresenta il risultato dell'iterazione di tutti i BLOB nel segmento.

   Restituisce il numero di occorrenze del termine di ricerca, **Microsoft**, nel BLOB passato al metodo **Calculate**.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Una volta completato, il metodo **Calculate** deve essere scritto in un nuovo BLOB. Quindi, per ogni set di BLOB elaborati è possibile scrivere un nuovo BLOB con i risultati. Per scrivere in un nuovo BLOB, trovare prima di tutto il set di dati di output.
    
    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
    // Convert to blob location object.
    outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    ```
4. Il codice chiama anche un metodo helper, **GetFolderPath** , per recuperare il percorso della cartella, ovvero il nome del contenitore di archiviazione.

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```

   **GetFolderPath** esegue il cast dell'oggetto DataSet a un oggetto AzureBlobDataSet, che include una proprietà denominata FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. Il codice chiama il metodo **GetFileName** per recuperare il nome del file (nome del BLOB).  

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Il nome del file viene scritto creando un oggetto URI. Il costruttore URI usa la proprietà **BlobEndpoint** per restituire il nome del contenitore. Il nome del file e il percorso della cartella vengono aggiunti per creare l'URI del BLOB di output.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. Il nome del file è stato scritto e ora è possibile scrivere la stringa di output dal metodo Calculate in un nuovo BLOB:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

## <a name="create-the-data-factory-using-azure-portal"></a>Creare la data factory usando il portale di Azure
La sezione **Creare l'attività personalizzata** ha illustrato come creare un'attività personalizzata e caricare il file ZIP con i file binari e il file PDB in un contenitore BLOB di Azure. Questa sezione illustra come creare una **data factory** di Azure con una **pipeline** che usa l'**attività personalizzata**.

Il set di dati di input per l'attività personalizzata rappresenta i BLOB (file) nella cartella di input (adftutorial\inputfolder) nell'archivio BLOB. Il set di dati di output per l'attività rappresenta i BLOB di output nella cartella di output (adftutorial\outputfolder) nell'archivio BLOB.

Creare un file denominato **file.txt** con il contenuto seguente e caricarlo in **adftutorial\inputfolder**. adftutorial è il nome del contenitore BLOB di Azure, mentre inputfolder è il nome della cartella nel contenitore.

```
test custom activity Microsoft test custom activity Microsoft
```

La cartella di input corrisponde a una sezione in Azure Data Factory, anche se la cartella contiene due o più file. Quando ogni sezione viene elaborata dalla pipeline, l'attività personalizzata esegue l'iterazione di tutti i BLOB nella cartella di input per tale sezione.

Verrà visualizzato un file di output nella cartella adftutorial\output con una o più righe, pari al numero di BLOB nella cartella di input:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```


Di seguito sono elencati i passaggi da eseguire in questa sezione:

1. Creare una **data factory**.
2. **Servizi collegati** per il pool di macchine virtuali di Azure Batch in cui viene eseguita l'attività personalizzata e l'archivio di Azure che contiene i BLOB di input e di output.
3. **Set di dati** di input e di output che rappresentano l'input e l'output dell'attività personalizzata.
4. **Pipeline** che usa l'attività personalizzata.
5. **Data factory**. Ne verrà creata una durante la pubblicazione di queste entità in Azure.

> [!NOTE]
> Creare il file **file.txt** e caricarlo in un contenitore BLOB, se l'operazione non è ancora stata eseguita. Le istruzioni sono riportate sopra.  
>
>

### <a name="step-1-create-the-data-factory"></a>Passaggio 1: Creare la data factory
1. Dopo l'accesso al portale di Azure, seguire questa procedura:
   1. Fare clic su **Nuovo** nel menu a sinistra.
   2. Fare clic su **Dati e analisi** nel pannello **Nuovo**.
   3. Fare clic su **Data factory** nel pannello **Analisi dei dati**.
2. Nel pannello **Nuova data factory** immettere **CustomActivityFactory** come Nome. È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore **Il nome "CustomActivityFactory" per la data factory non è disponibile**, cambiare il nome della data factory, ad esempio, **nomeutenteCustomActivityFactory**, e provare di nuovo a crearla.
3. Fare clic su **NOME DEL GRUPPO DI RISORSE**e selezionare un gruppo di risorse esistente o crearne uno.
4. Verificare se la **sottoscrizione** e l'**area** in cui si vuole creare la data factory sono quelle corrette.
5. Fare clic su **Crea** nel pannello **Nuova data factory**.
6. Nel **Dashboard** del portale di Azure verrà visualizzata la data factory in fase di creazione.
7. Dopo aver creato la data factory, verrà visualizzato il pannello corrispondente con elencato il contenuto della data factory.

### <a name="step-2-create-linked-services"></a>Passaggio 2: Creare servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. In questo passaggio si collegheranno l'account di archiviazione di Azure e l'account Azure Batch alla data factory.

#### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure
1. Fare clic sul riquadro **Creare e distribuire** nel pannello **DATA FACTORY** per **CustomActivityFactory**. Viene visualizzato l'editor di Data Factory.
2. Fare clic su **Nuovo archivio dati** sulla barra dei comandi e scegliere **Archiviazione di Azure**. Nell'editor verrà visualizzato lo script JSON per la creazione di un servizio collegato Archiviazione di Azure.

3. Sostituire **account name** con il nome dell'account di archiviazione di Azure e **account key** con la chiave di accesso dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere la sezione [Visualizzare, copiare e rigenerare le chiavi di accesso nelle risorse di archiviazione](../storage/storage-create-storage-account.md#manage-your-storage-account).

4. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

#### <a name="create-azure-batch-linked-service"></a>Creare il servizio collegato Azure Batch
1. Nell'editor di Data Factory fare clic su **Nuovo calcolo** sulla barra dei comandi, quindi scegliere **Azure Batch** dal menu.
2. Apportare le modifiche seguenti allo script JSON:

   1. Specificare il nome dell'account Azure Batch per la proprietà **accountName** . L'**URL** nel **pannello dell'account Azure Batch** è nel formato seguente: http://**<nomeaccount>**.<area>.batch.azure.com. Per la proprietà **batchUri** nello script JSON è necessario **rimuovere "accountname."** dall'URL e usare **accountname** per la proprietà JSON **accountName**.
   2. Specificare la chiave dell'account Azure Batch per la proprietà **accessKey** .
   3. Specificare il nome del pool che è stato creato come parte dei prerequisiti per la proprietà **poolName** . È anche possibile specificare l'ID del pool anziché il nome del pool.
   4. Specificare l'URI di Azure Batch per la proprietà **batchUri** . Vedere le note precedenti per la proprietà **accountName** . Esempio: https://westus.batch.azure.com.  
   5. Per la proprietà **AzureStorageLinkedService** for the **linkedServiceName** .

        ```JSON
        {
         "name": "AzureBatchLinkedService",
         "properties": {
           "type": "AzureBatch",
           "typeProperties": {
             "accountName": "myazurebatchaccount",
             "batchUri": "https://westus.batch.azure.com",
             "accessKey": "<yourbatchaccountkey>",
             "poolName": "myazurebatchpool",
             "linkedServiceName": "AzureStorageLinkedService"
           }
         }
        }
        ```

       Per la proprietà **poolName** è anche possibile specificare l'ID del pool anziché il nome del pool.

      > [!NOTE]
      > Il servizio Data Factory non supporta un'opzione su richiesta per il Batch di Azure come accade per HDInsight. È possibile usare solo il proprio pool di Batch di Azure in una data factory di Azure.
      >
      >

### <a name="step-3-create-datasets"></a>Passaggio 3: Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di input e di output.

#### <a name="create-input-dataset"></a>Creare set di dati di input
1. Nell'**Editor** per l'istanza di Data factory fare clic sul pulsante **Nuovo set di dati** sulla barra degli strumenti, quindi scegliere **Archiviazione BLOB di Azure** dal menu a discesa.
2. Sostituire il codice JSON nel riquadro a destra con il frammento JSON seguente.

    ```JSON
    {
     "name": "InputDataset",
     "properties": {
         "type": "AzureBlob",
         "linkedServiceName": "AzureStorageLinkedService",
         "typeProperties": {
             "folderPath": "adftutorial/customactivityinput/",
             "format": {
                 "type": "TextFormat"
             }
         },
         "availability": {
             "frequency": "Hour",
             "interval": 1
         },
         "external": true,
         "policy": {}
     }
    }
    ```

   Più avanti in questa procedura dettagliata viene creata una pipeline con ora di inizio: 2015-11-16T00:00:00Z e ora di fine: 2015-11-16T05:00:00Z. Viene pianificata per produrre dati ogni ora, in modo da ottenere 5 sezioni di input/output tra **00**:00:00 -> **05**:00:00

   La **frequenza** e l'**intervallo** per il set di dati di input sono impostati su **Ora** e **1**. Ciò significa che la sezione di input è disponibile ogni ora. In questo esempio si tratta dello stesso file (file.txt) che si trova nella cartella di input.

   Sono indicate le ore di inizio per ogni sezione, rappresentate dalla variabile di sistema SliceStart nel precedente frammento JSON.
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire **InputDataset**. Controllare che sulla barra del titolo dell'editor sia visualizzato un messaggio simile a **LA CREAZIONE DELLA TABELLA È STATA COMPLETATA** .

#### <a name="create-an-output-dataset"></a>Creare un set di dati di output
1. Nell'**Editor di Data factory** fare clic su **Nuovo set di dati**, quindi fare clic su **Archiviazione BLOB di Azure** nella barra dei comandi.
2. Sostituire lo script JSON nel riquadro a destra con il seguente script JSON:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     Il percorso di output è **adftutorial/customactivityoutput/** e il nome del file di output è yyyy-MM-dd-HH.txt, dove yyyy-MM-dd-HH indica anno, mese, giorno e ora della sezione che viene generata. Per informazioni dettagliate, vedere la [guida di riferimento per gli sviluppatori][adf-developer-reference].

    Un BLOB o file di output viene generato per ogni sezione di input. Ecco come viene denominato il file di output per ogni sezione. Tutti i file di output vengono generati in una cartella di output: **adftutorial\customactivityoutput**.

   | Sezione | Ora di inizio | File di output |
   |:--- |:--- |:--- |
   | 1 |2015-11-16T00:00:00 |2015-11-16-00.txt |
   | 2 |2015-11-16T01:00:00 |2015-11-16-01.txt |
   | 3 |2015-11-16T02:00:00 |2015-11-16-02.txt |
   | 4 |2015-11-16T03:00:00 |2015-11-16-03.txt |
   | 5 |2015-11-16T04:00:00 |2015-11-16-04.txt |

    Tenere presente che tutti i file in una cartella di input fanno parte di una sezione con le ore di inizio indicate in precedenza. Quando la sezione viene elaborata, l'attività personalizzata esamina ogni file e produce una riga nel file di output con il numero di occorrenze del termine di ricerca ("Microsoft"). Se sono presenti tre file nella cartella di input, ci saranno tre righe nel file di output per ogni sezione oraria: 2015-11-16-00.txt, 2015-11-16:01:00:00.txt e così via.
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire **OutputDataset**.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Creare ed eseguire una pipeline che usi l'attività personalizzata
1. Nell'Editor di Data factory fare clic su **Nuova pipeline** sulla barra dei comandi. Se non viene visualizzato il comando, fare clic su **... (puntini di sospensione)** per visualizzarlo.
2. Sostituire lo script JSON nel riquadro a destra con lo script JSON seguente.

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2015-11-16T00:00:00Z",
        "end": "2015-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Tenere presente quanto segue:

   * **Concorrenza** è impostata su **2** in modo che due sezioni siano elaborate in parallelo da 2 VM nel pool di Azure Batch.
   * Nella sezione delle attività esiste una sola attività, di tipo **DotNetActivity**.
   * **AssemblyName** è impostato sul nome della DLL **MyDotNetActivity.dll**.
   * **EntryPoint** è impostato su **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** è impostato su **AzureStorageLinkedService** che punta all'archiviazione BLOB contenente il file ZIP dell'attività personalizzata. Se vengono usati account di archiviazione di Azure diversi per i file di input/output e per il file ZIP dell'attività personalizzata, è necessario creare un altro servizio collegato Archiviazione di Azure. Questo articolo presuppone che venga usato lo stesso account di archiviazione di Azure.
   * **PackageFile** è impostato su **customactivitycontainer/MyDotNetActivity.zip**. Ha il formato: contenitoreperlozip/nomedellozip.zip.
   * L'attività personalizzata accetta **InputDataset** come input e **OutputDataset** come output.
   * La proprietà linkedServiceName dell'attività personalizzata punta ad **AzureBatchLinkedService**per indicare ad Azure Data Factory che l'attività personalizzata deve essere eseguita nelle VM di Azure Batch.
   * La proprietà **isPaused** è impostata su **false** per impostazione predefinita. In questo esempio la pipeline viene eseguita immediatamente perché le sezioni hanno inizio nel passato. È possibile impostare questa proprietà su true per sospendere la pipeline e reimpostarla su false per riavviare la pipeline.
   * L'ora di **inizio** e l'ora di **fine** hanno **cinque** ore di differenza e le sezioni vengono prodotte ogni ora, quindi la pipeline genera cinque sezioni.
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline
1. Nel pannello Data factory del portale di Azure fare clic su **Diagramma**.

    ![Riquadro Diagramma](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Nella vista Diagramma fare clic su OutputDataset.

    ![Vista diagramma](./media/data-factory-use-custom-activities/diagram.png)
3. Le cinque sezioni di output si trovano nello stato Ready se sono già state generate.

   ![Sezioni di output](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Verificare che i file di output vengano generati nell'archiviazione BLOB nel contenitore **adftutorial** .

   ![output dell'attività personalizzata][image-data-factory-ouput-from-custom-activity]
5. Se si apre il file di output, l'output visualizzato dovrebbe essere simile al seguente:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
6. Usare il [portale di Azure][azure-preview-portal] o i cmdlet di Azure PowerShell per monitorare l'istanza di Data Factory, le pipeline e i set di dati. I messaggi possono essere visualizzati da **ActivityLogger** nel codice per l'attività personalizzata nei log (specifically user-0.log) scaricabili dal portale o con i cmdlet.

   ![scaricare i log dall'attività personalizzata][image-data-factory-download-logs-from-custom-activity]

Vedere [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md) per i passaggi dettagliati per il monitoraggio di set di dati e pipeline.      

### <a name="data-factory-and-batch-integration"></a>Integrazione di Data Factory e Batch
Il servizio Data Factory crea un processo in Azure Batch denominato **adf-poolname:job-xxx**.

![Azure Data Factory: processi di Batch](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Per ogni esecuzione attività di una sezione viene creata un'attività. Se sono presenti 10 sezioni pronte per l'elaborazione, vengono create 10 attività nel processo. È possibile eseguire più sezioni in parallelo se sono disponibili più nodi di calcolo nel pool. È anche possibile eseguire più sezioni nello stesso nodo di calcolo se l'impostazione per il numero massimo di attività per nodo di calcolo è > 1.

![Azure Data Factory: attività processi di Batch](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Il diagramma seguente illustra il rapporto tra attività di Azure Data Factory e Batch.

![Data Factory e Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="debug-the-pipeline"></a>Eseguire il debug della pipeline
Il debug è costituito da alcune tecniche di base:

1. Se viene visualizzato il messaggio di errore seguente, verificare che il nome della classe nel file CS corrisponda al nome specificato nella proprietà **EntryPoint** nella pipeline JSON. Nella procedura dettagliata precedente, il nome della classe è MyDotNetActivity e la proprietà EntryPoint nella pipeline JSON è MyDotNetActivityNS.**MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Se i nomi corrispondono, verificare che tutti i file binari si trovino nella **cartella radice** del file ZIP. Quando si apre il file ZIP, in altre parole, tutti i file devono trovarsi nella cartella radice, non nelle sottocartelle.   
2. Se la sezione di input non è impostata su **Ready**, verificare che la struttura di cartelle di input sia corretta e che **file.txt** sia presente nelle cartelle di input.
3. Nel metodo **Execute** dell'attività personalizzata usare l'oggetto **IActivityLogger** per registrare informazioni utili per la risoluzione dei problemi. I messaggi registrati vengono visualizzati nei file di log dell'utente, ovvero uno o più file denominati user-0.log, user-1.log, user-2.log e così via.

   Nel pannello **OutputDataset** fare clic sulla sezione per visualizzare il relativo pannello **SEZIONE DATI**. Verranno visualizzate le **esecuzioni di attività** per quella sezione. Dovrebbe essere visualizzata una esecuzione attività per questa sezione. Facendo clic su Esegui sulla barra dei comandi è possibile avviare un'altra esecuzione attività per la stessa sezione.

   Quando si fa clic sull'esecuzione attività viene visualizzato il pannello **DETTAGLI ESECUZIONE ATTIVITÀ** con un elenco di file di log. I messaggi registrati verranno visualizzati nel file user_0.log. Quando si verifica un errore vengono visualizzate tre esecuzioni attività perché il numero di tentativi è impostato su 3 nel codice JSON della pipeline/attività. Quando si fa clic sull'esecuzione attività vengono visualizzati i file di log che è possibile esaminare per risolvere l'errore.

   Nell'elenco dei file di log fare clic su **user-0.log**. Nel riquadro destro sono riportati i risultati dell'uso del metodo **IActivityLogger.Write** . Se non si vedono tutti i messaggi, controllare se ci sono altri file di log denominati: user_1.log, user_2.log e così via. Altrimenti il codice potrebbe essersi bloccato dopo l’ultimo messaggio registrato.

   È consigliabile cercare anche nel file **system-0.log** eventuali messaggi di errore di sistema ed eccezioni.
4. Includere il file **PDB** nel file ZIP in modo che i dettagli dell'errore contengano informazioni come lo **stack di chiamate** quando si verifica un errore.
5. Tutti i file contenuti nel file con estensione zip dell'attività personalizzata devono trovarsi nel **livello principale** senza sottocartelle.
6. Assicurarsi che **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e **packageLinkedService** (deve fare riferimento all'archivio BLOB di Azure che contiene il file ZIP) siano impostati su valori corretti.
7. Se è stato risolto un errore e si vuole rielaborare la sezione, fare clic con il pulsante destro del mouse sulla sezione nel pannello **OutputDataset**, quindi scegliere **Esegui**.
8. Se viene visualizzato l'errore seguente, si usa il pacchetto di Archiviazione di Azure con una versione > 4.3.0. L'utilità di avvio del servizio Data Factory richiede la versione 4.3 di WindowsAzure.Storage. Per informazioni su una soluzione alternativa nel caso in cui sia necessario usare una versione più recente dell'assembly di Archiviazione di Azure, vedere la sezione [Isolamento di AppDomain](#appdomain-isolation). 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    Se è possibile usare la versione 4.3.0 del pacchetto di Archiviazione di Azure, rimuovere il riferimento esistente al pacchetto di Archiviazione di Azure con versione > 4.3.0 ed eseguire il comando seguente dalla console di Gestione pacchetti NuGet. 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Compilare il progetto. Eliminare l'assembly Azure.Storage con versione > 4.3.0 dalla cartella bin\Debug. Creare un nuovo file ZIP con i file binari e il file PDB. Sostituire il file ZIP precedente con questo nel contenitore BLOB (customactivitycontainer). Eseguire di nuovo le sezioni non riuscite, facendo clic sul pulsante destro del mouse sulla sezione e scegliendo Esegui.   
8. L'attività personalizzata non usa il file **app.config** dal pacchetto, quindi se il codice legge eventuali stringhe di connessione dal file di configurazione, non funzionerà in fase di esecuzione. La procedura consigliata quando si usa Azure Batch consiste nell'inserire tutti i segreti in un **insieme di credenziali delle chiavi di Azure**, usare un'entità servizio basata su certificato per proteggere l'**insieme di credenziali** e distribuire il certificato nel pool di Azure Batch. L'attività personalizzata .NET può quindi accedere ai segreti dall'insieme di credenziali delle chiavi in fase di esecuzione. Questa è una soluzione generica e può essere ridimensionata per qualsiasi tipo di segreto, non solo per una stringa di connessione.

   Esiste una soluzione più semplice, ma non consigliata: è possibile creare un **servizio collegato di Azure SQL** con le impostazioni della stringa di connessione, creare un set di dati che usa il servizio collegato e concatenare tale set di dati come set di dati di input fittizio all'attività .NET personalizzata. È quindi possibile accedere alla stringa di connessione del servizio collegato nel codice dell'attività personalizzata, che dovrebbe funzionare correttamente in fase di esecuzione.  

## <a name="update-the-custom-activity"></a>Aggiornare l'attività personalizzata
Per aggiornare il codice dell'attività personalizzata, compilarlo e caricare il file ZIP contenente i nuovi file binari nell'archiviazione BLOB.

## <a name="copymove-data"></a>Copiare o spostare dati

L'attività di copia esegue la copia dei dati da un archivio dati di **origine** a un archivio dati **sink**. Per l'elenco degli archivi dati supportati come origini e sink per l'attività di copia, vedere [Archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

Per spostare i dati da e verso un archivio dati che non è supportato dall'**attività di copia** è possibile usare l'**attività personalizzata** in Data Factory con la logica richiesta per copiare o spostare i dati. Vedere l' [esempio relativo all'unità di download dei dati HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) su GitHub.  

## <a name="appdomain-isolation"></a>Isolamento di AppDomain
Vedere l' [esempio sul passaggio fra AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) che illustra come creare un'attività .NET personalizzata per Azure Data Factory che non sia vincolata alle versioni assembly usate dal servizio di avvio di Azure Data Factory, ad esempio WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x e così via.

## <a name="access-extended-properties"></a>Accedere a tutte le proprietà estese
È possibile dichiarare estese le proprietà presenti nel codice JSON dell'attività come illustrato di seguito:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```


Ne codice sono presenti due proprietà estese: **SliceStart** e **DataFactoryName**. Il valore di SliceStart si basa sulla variabile di sistema SliceStart. Per un elenco delle variabili di sistema supportate, vedere [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md#data-factory-functions-and-system-variables) . Il valore di DataFactoryName è hardcoded su "CustomActivityFactory".

Per accedere alle proprietà estese nel metodo **Execute**, usare codice simile al seguente:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties                               
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-feature-of-azure-batch"></a>Funzionalità di scalabilità automatica di Azure Batch
È anche possibile creare un pool di Azure Batch con la funzionalità **Scalabilità automatica** . Ad esempio, è possibile creare un pool di Azure Batch con 0 VM dedicate e una formula di scalabilità basata sul numero di attività in sospeso:

Una macchina virtuale per ogni attività in sospeso al momento. Ad esempio, cinque attività in sospeso -> cinque macchine virtuali:

```
pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
$TargetDedicated = max(pendingTaskSampleVector);
```

Massimo una macchina virtuale alla volta indipendentemente dal numero di attività in sospeso:

```
pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
$TargetDedicated = (max(pendingTaskSampleVector)>0)?1:0;
```

Per i dettagli, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](../batch/batch-automatic-scaling.md) .

Se il pool usa il valore predefinito [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), possono essere necessari 15-30 minuti perché il servizio Batch prepari la VM prima di eseguire l'attività personalizzata.  Se il pool usa un valore autoScaleEvaluationInterval diverso, il servizio Batch può richiedere un valore autoScaleEvaluationInterval + 10 minuti.

## <a name="use-azure-hdinsight-linked-services"></a>Usare i servizi collegati Azure HDInsight
Nella procedura dettagliata è stato usato il calcolo Azure Batch per eseguire l'attività personalizzata. È anche possibile usare il proprio cluster HDInsight o fare in modo che Data Factory crei un cluster HDInsight su richiesta ed esegua l'attività personalizzata sul cluster HDInsight. Di seguito sono riportati i passaggi generali per usare un cluster HDInsight.  

1. Creare un servizio collegato Azure HDInsight.   
2. Usare il servizio collegato HDInsight al posto di **AzureBatchLinkedService** nella pipeline JSON.

È consigliabile modificare l'ora di **inizio** e **fine** per la pipeline per poter testare lo scenario con il servizio Azure HDInsight.

#### <a name="create-azure-hdinsight-linked-service"></a>Creare un servizio collegato Azure HDInsight
Il servizio Azure Data Factory supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, se si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster.

> [!NOTE]
> Al runtime, un'istanza di un'attività .NET viene eseguita solo in un nodo di lavoro nel cluster HDInsight, ma non può essere scalata al fine di essere eseguita in più nodi. È possibile eseguire più istanze di attività .NET contemporaneamente su diversi nodi del cluster HDInsight.
>
>

##### <a name="to-use-an-on-demand-hdinsight-cluster"></a>Per usare un cluster HDInsight su richiesta
1. Nel menu a sinistra del **portale di Azure**fare clic su **Creare e distribuire** nella home page di Data factory.
2. Nell'editor di Data Factory fare clic su **Nuovo calcolo** sulla barra dei comandi, quindi scegliere **Cluster HDInsight su richiesta** dal menu.
3. Apportare le modifiche seguenti allo script JSON:

   1. Per la proprietà **clusterSize** , specificare le dimensioni del cluster HDInsight.
   2. Per la proprietà **timeToLive** specificare il tempo di inattività consentito per il cliente prima dell'eliminazione.
   3. Per la proprietà **version** , specificare la versione di HDInsight da utilizzare. Se si esclude questa proprietà, verrà usata la versione più recente.  
   4. Per **linkedServiceName**, specificare l'oggetto **AzureStorageLinkedService** creato nell'esercitazione introduttiva.

        ```JSON
        {
           "name": "HDInsightOnDemandLinkedService",
           "properties": {
               "type": "HDInsightOnDemand",
               "typeProperties": {
                   "clusterSize": 4,
                   "timeToLive": "00:05:00",
                   "osType": "Windows",
                   "linkedServiceName": "AzureStorageLinkedService",
               }
           }
        }
        ```
4. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

##### <a name="to-use-your-own-hdinsight-cluster"></a>Per usare il proprio cluster HDInsight:
1. Nel menu a sinistra del **portale di Azure**fare clic su **Creare e distribuire** nella home page di Data factory.
2. Nell**editor di Data Factory** fare clic su **Nuovo calcolo** sulla barra dei comandi, quindi scegliere **Cluster HDInsight** dal menu.
3. Apportare le modifiche seguenti allo script JSON:

   1. Per la proprietà **clusterUri** , immettere l'URL di HDInsight. Ad esempio: https://<clustername>.azurehdinsight.net/     
   2. Per la proprietà **UserName** , immettere il nome dell'utente che ha accesso al cluster HDInsight.
   3. Per la proprietà **Password** , immettere la password dell'utente.
   4. Per la proprietà **LinkedServiceName**, immettere **AzureStorageLinkedService**, ovvero il servizio collegato creato nell'esercitazione introduttiva.
4. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

Per informazioni dettagliate, vedere [Servizi collegati di calcolo](data-factory-compute-linked-services.md) .

Nella **pipeline JSON**usare il servizio collegato HDInsight, proprio o su richiesta:

```JSON
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "HDInsightOnDemandLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2015-11-16T00:00:00Z",
    "end": "2015-11-16T05:00:00Z",
    "isPaused": false
  }
}
```

## <a name="examples"></a>esempi
| Esempio | Funzioni delle attività personalizzate |
| --- | --- |
| [HttpDataDownloaderSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample)(Esempio relativo al downloader dati HTTP). |Scarica i dati da un endpoint HTTP per l'archivio BLOB di Azure usando l'attività personalizzata C# in Data Factory. |
| [Esempio di analisi del sentimento su Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Chiama un modello ML di Azure ed esegue l'analisi del sentimento, l'assegnazione dei punteggi, la stima e così via. |
| [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)(Esempio relativo all'esecuzione di script R con ADF). |Chiama lo script R eseguendo RScript.exe sul cluster HDInsight in cui è già installato R. |
| [Attività .NET per dominio app](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Usa versioni di assembly diverse da quelle usate dal servizio di avvio di Data Factory |

## <a name="see-also"></a>Vedere anche
[Aggiornamenti della Data factory di Azure: eseguire le attività .NET personalizzate per ADF usando Azure Batch](https://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png



<!--HONumber=Dec16_HO3-->


