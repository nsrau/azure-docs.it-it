---
title: Process large-scale datasets by using Data Factory and Batch (Elaborare set di dati su larga scala con Data Factory e Batch) | Microsoft Docs
description: Descrive come elaborare elevate quantità di dati in una pipeline di Azure Data Factory usando la funzionalità di elaborazione parallela di Azure Batch.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8f0cd8aad2d5c5142fc66c78393b57ff210a7b83
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Elaborare set di dati su larga scala con Data Factory e Batch
> [!NOTE]
> Questo articolo fa riferimento alla versione 1 di Azure Data Factory, disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere le [Attività personalizzate in Data Factory versione 2](../transform-data-using-dotnet-custom-activity.md).

Questo articolo descrive l'architettura di una soluzione di esempio che sposta ed elabora set di dati su larga scala in modo automatico e pianificato. Viene descritta anche una procedura dettagliata end-to-end per implementare la soluzione tramite Data Factory e Azure Batch.

Questo articolo è più lungo dei nostri soliti articoli perché contiene la procedura dettagliata per un'intera soluzione di esempio. Se non si ha familiarità con Batch e Data Factory, sarà possibile ottenere altre informazioni su questi servizi e su come interagiscono. Se invece si conoscono già questi servizi e si sta progettando o creando una soluzione, è possibile concentrarsi sulla [sezione architettura](#architecture-of-sample-solution) dell'articolo. Se si sta sviluppando un prototipo o una soluzione, è possibile provare a seguire le istruzioni della [procedura dettagliata](#implementation-of-sample-solution). Microsoft invita gli utenti a inviare i loro commenti su questo contenuto e sulle relative modalità di impiego.

In primo luogo si vedrà in che modo i servizi Data Factory e Batch possono essere utili per elaborare grandi set di dati nel cloud.     

## <a name="why-azure-batch"></a>Perché Azure Batch?
 Si può usare Batch per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. Batch è un servizio di piattaforma che consente di pianificare il lavoro a elevato uso di calcolo da eseguire su una raccolta gestita di macchine virtuali. È possibile ridimensionare automaticamente le risorse di calcolo per soddisfare le esigenze dei processi.

Il servizio Batch consente di definire le risorse di calcolo di Azure per eseguire le applicazioni in parallelo e su larga scala. È possibile eseguire processi su richiesta o pianificati. Non è necessario creare, configurare e gestire manualmente un cluster HPC, singole macchine virtuali, reti virtuali o un'infrastruttura complessa di pianificazione di processi e attività.

 Se non si ha familiarità con Batch, vedere gli articoli seguenti che aiutano a comprendere l'architettura e l'implementazione della soluzione descritta in questo articolo:   

* [Nozioni di base di Batch](../../batch/batch-technical-overview.md)
* [Panoramica delle funzionalità Batch](../../batch/batch-api-basics.md)

Per altre informazioni su Batch è possibile consultare il [Percorso di apprendimento di Batch](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Perché Azure Data Factory?
Data factory è un servizio di integrazione delle informazioni basato sul cloud che permette di automatizzare lo spostamento e la trasformazione dei dati. È possibile usare Data Factory per creare pipeline di dati gestiti che spostino i dati da archivi locali e su cloud a un archivio dati centralizzato. Un esempio è l'archivio BLOB di Azure. È possibile usare Data Factory per elaborare o trasformare dati tramite servizi come Azure HDInsight e Azure Machine Learning. È anche possibile pianificare le pipeline di dati per eseguire operazioni in modo pianificato (ad esempio con intervalli orari, giornalieri e settimanali). È possibile monitorare e gestire le pipeline in modo immediato per identificare i problemi e definire gli interventi necessari.

  Se non si ha familiarità con Data Factory, vedere gli articoli seguenti che aiutano a comprendere l'architettura e l'implementazione della soluzione descritta in questo articolo:  

* [Introduzione al servizio Data factory](data-factory-introduction.md)
* [Creare la prima pipeline di dati](data-factory-build-your-first-pipeline.md)   

Per altre informazioni su Data Factory, è possibile anche vedere il [Percorso di apprendimento per Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Data Factory e Batch insieme
Data Factory include attività predefinite. Ad esempio, è disponibile l'attività Copia per copiare e spostare dati da un archivio dati di origine a uno di destinazione. L'attività Hive viene usata per elaborare dati mediante i cluster Hadoop (HDInsight) in Azure. Per un elenco delle attività di trasformazione supportate, vedere le informazioni sulle [Attività di trasformazione dati](data-factory-data-transformation-activities.md).

È inoltre possibile creare attività personalizzate di .NET per spostare o elaborare dati con una logica personalizzata. È possibile eseguire queste attività in un cluster HDInsight o in un pool di Batch di macchine virtuali. Quando si usa Batch, è possibile configurare il pool per la scalabilità automatica (aggiungere o rimuovere macchine virtuali in base al carico di lavoro) secondo la formula scelta dall'utente.     

## <a name="architecture-of-a-sample-solution"></a>Architettura di una soluzione di esempio
  L'architettura descritta in questo articolo è quella di una soluzione semplice. È adatta anche per scenari complessi, ad esempio la modellazione di rischio per servizi finanziari, l'elaborazione di immagini e il rendering, così come le analisi di tipo genomico.

Il diagramma illustra come Data Factory orchestra l'elaborazione e lo spostamento dei dati. Viene inoltre illustrato come Batch elabora i dati in modo parallelo. Scaricare e stampare il diagramma per riferimento (297 x 420 mm, formato A3). Per accedere al diagramma in modo da poterlo stampare, vedere [HPC and data orchestration by using Batch and Data Factory (Orchestrazione di HPC e dati mediante Batch e Data Factory)](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagramma di elaborazione dei dati su larga scala](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

Nell'elenco seguente vengono presentati i passaggi di base del processo. La soluzione include il codice e le spiegazioni per compilare la soluzione end-to-end.

* **Configurare Batch con un pool di nodi di calcolo (macchine virtuali).** È possibile specificare il numero di nodi e le dimensioni di ogni nodo.

* **Creare un'istanza di Data Factory** configurata con le entità che rappresentano l'archivio BLOB, il servizio di calcolo di Batch, i dati di input/output e una pipeline o un flusso di lavoro con attività per lo spostamento e la trasformazione dei dati.

* **Creare un'attività personalizzata di .NET nella pipeline di Data Factory.** L'attività è il codice utente che viene eseguito nel pool di Batch.

* **Archiviare grandi quantità di dati di input come BLOB in Archiviazione di Azure.** I dati vengono divisi in sezioni logiche, in genere in base al tempo.

* **Data Factory copia i dati che vengono elaborati in parallelo** nel percorso secondario.

* **Data Factory esegue l'attività personalizzata usando il pool allocato da Batch.** Data Factory può eseguire attività contemporaneamente. Ogni attività elabora una sezione dei dati. I risultati vengono archiviati nella risorsa di archiviazione.

* **Data Factory sposta i risultati finali in un terzo percorso,**per la distribuzione tramite un'app o per una successiva elaborazione con altri strumenti.

## <a name="implementation-of-the-sample-solution"></a>Implementazione della soluzione di esempio
La soluzione di esempio è volutamente semplice. È progettata per illustrare come usare Data Factory e Batch insieme ai set di dati di processo. La soluzione conta semplicemente il numero di occorrenze del termine di ricerca ("Microsoft") nei file di input organizzati in una serie temporale. Restituisce quindi il numero in file di output.

**Tempo**: se si ha familiarità con le nozioni di base di Azure, di Data Factory e di Batch e sono stati completati i prerequisiti elencati di seguito, si stima che il completamento di questa soluzione richieda 1-2 ore.

### <a name="prerequisites"></a>prerequisiti
#### <a name="azure-subscription"></a>Sottoscrizione di Azure
Se non si dispone di una sottoscrizione di Azure, è possibile creare un account di valutazione gratuita. Per altre informazioni, vedere [Valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Account di archiviazione di Azure
Usare un account di archiviazione per archiviare i dati di questa esercitazione. Se non si dispone di un account di archiviazione, vedere [Create a storage account (Creare un account di archiviazione)](../../storage/common/storage-create-storage-account.md#create-a-storage-account). La soluzione di esempio usa l'archivio BLOB.

#### <a name="azure-batch-account"></a>Account Azure Batch
Creare un account di Batch usando il [portale di Azure](http://portal.azure.com/). Per altre informazioni, vedere [Creare e gestire un account di Batch](../../batch/batch-account-create-portal.md). Annotare il nome dell'account di Batch e della relativa chiave. È possibile anche usare il cmdlet [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) per creare un account di Batch. Per istruzioni dettagliate sull'uso del cmdlet, vedere [Guida introduttiva ai cmdlet PowerShell di Batch](../../batch/batch-powershell-cmdlets-get-started.md).

Per elaborare i dati in modalità parallela in un pool di nodi di calcolo, ovvero una raccolta gestita di macchine virtuali, la soluzione di esempio usa Batch indirettamente tramite una pipeline di data factory.

#### <a name="azure-batch-pool-of-virtual-machines"></a>Pool di Azure Batch di macchine virtuali
Creare un pool di Batch con almeno 2 nodi di calcolo.

1. Nel menu a sinistra del [portale di Azure](https://portal.azure.com) fare clic su **Altri servizi** e quindi su **Batch Accounts**.

2. Selezionare il proprio account di Batch per aprire il pannello **Batch Account**.

3. Selezionare il riquadro **Pools**.

4. Nel riquadro **Pools**, fare clic sul pulsante **Aggiungi** nella barra degli strumenti per aggiungere un pool.

   a. Immettere un ID per il pool (**ID pool**). Annotare l'ID del pool. È necessario quando si crea la soluzione di data factory.

   b. Specificare **Windows Server 2012 R2** per l'impostazione **Famiglia di Sistemi Operativi**.

   c. Selezionare un **piano tariffario per il nodo**.

   d. Immettere **2** come valore per l'impostazione **Pool dedicati di destinazione**.

   e. Immettere **2** come valore per l'impostazione **Numero massimo di attività per nodo**.

   f. Fare clic su **OK** per creare il pool.

#### <a name="azure-storage-explorer"></a>Esplora archivi Azure
Usare [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) o [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (di ClumsyLeaf Software) per controllare e modificare i dati nei progetti di Archiviazione. È anche possibile esaminare e modificare i dati nei log delle applicazioni ospitate nel cloud.

1. Creare un contenitore denominato **mycontainer** con accesso privato (nessun accesso anonimo).

2. Se si usa CloudXplorer, creare cartelle e sottocartelle con la struttura seguente:

   ![Struttura delle cartelle e sottocartelle](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`e `outputfolder` sono le cartelle di livello superiore in `mycontainer`. La cartella `inputfolder` contiene sottocartelle con indicatori data e ora (AAAA-MM-GG-HH).

   Se si usa Storage Explorer, nel passaggio successivo si dovranno caricare file denominati `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` e così via. Le cartelle verranno create automaticamente.

3. Creare un file di testo **file.txt** nel computer con contenuto che include la parola chiave **Microsoft**. Ad esempio: "test custom activity Microsoft test custom activity Microsoft".

4. Caricare il file nelle cartelle di input seguenti nell'archivio BLOB:

   ![Cartelle di input](./media/data-factory-data-processing-using-batch/image4.png)

   Se si usa Storage Explorer, caricare il file **file.txt** in **mycontainer**. Fare clic su **Copia** sulla barra degli strumenti per creare una copia del BLOB. Nella finestra di dialogo **Copia BLOB** modificare il **nome del BLOB di destinazione** in `inputfolder/2015-11-16-00/file.txt`. Ripetere questo passaggio per creare `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` e così via. Le cartelle verranno create automaticamente.

5. Creare un altro contenitore denominato `customactivitycontainer`. Caricare il file ZIP dell'attività personalizzata in questo contenitore.

#### <a name="visual-studio"></a>Visual Studio
Installare Visual Studio 2012 o versione successiva per creare l'attività Batch personalizzata da usare nella soluzione data factory.

### <a name="high-level-steps-to-create-the-solution"></a>Passaggi generali per creare la soluzione
1. Creare un'attività personalizzata che contenga la logica di elaborazione dei dati.

2. Creare una data factory che usi l'attività personalizzata.

### <a name="create-the-custom-activity"></a>Creare l'attività personalizzata
L'attività personalizzata di data factory è il fulcro della soluzione di esempio. La soluzione di esempio usa Batch per eseguire l'attività personalizzata. Per informazioni su come sviluppare attività personalizzate e come usarle in una pipeline di data factory, vedere [Usare attività personalizzate in una pipeline di data factory](data-factory-use-custom-activities.md).

Per creare un'attività personalizzata di .NET da usare in una pipeline di data factory, è necessario creare un progetto della libreria di classi .NET con una classe che implementi l'interfaccia IDotNetActivity. Questa interfaccia ha un solo metodo: Execute. Questa è la firma del metodo:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

È necessario conoscere alcuni dei componenti chiave del metodo:

* Il metodo accetta quattro parametri:

  * **linkedServices**. Un elenco enumerabile di servizi collegati che collegano origini dati di input/output (ad esempio, l'archivio BLOB) alla data factory. In questo esempio c'è un solo servizio collegato di tipo Archiviazione di Azure usato sia per l'input che per l'output.
  * **datasets**. Questo parametro rappresenta un elenco enumerabile di set di dati. È possibile usare questo parametro per ottenere le posizioni e gli schemi definiti da set di dati di input e di output.
  * **activity**. Questo parametro rappresenta l'entità di calcolo corrente. In questo caso si tratta di un servizio Batch.
  * **logger**. Il logger consente di scrivere commenti di debug che verranno visualizzati come log "Utente" per la pipeline.
* Il metodo restituisce un dizionario che può essere usato per concatenare le attività personalizzate in un secondo momento. Questa funzionalità non è ancora implementata, quindi il metodo restituisce un dizionario vuoto.

#### <a name="procedure-create-the-custom-activity"></a>Procedura: Creare l'attività personalizzata
1. Creare un progetto libreria di classi .NET in Visual Studio.

   a. Avviare Visual Studio versione 2012, 2013 o 2015.

   b. Selezionare **File** > **Nuovo** > **Progetto**.

   c. Espandere **Modelli** e quindi selezionare **Visual C\#**. In questa procedura dettagliata viene usato C\#, ma è possibile usare qualsiasi linguaggio .NET per sviluppare l'attività personalizzata.

   d. Selezionare la **libreria di classi** dall'elenco relativo ai tipi di progetto visualizzato a destra.

   e. Immettere **MyDotNetActivity** for the **Nome**.

   f. Selezionare **C:\\ADF** per **Percorso**. Creare la cartella **ADF** se non esiste già.

   g. Selezionare **OK** per creare il progetto.

2. Fare clic su **Strumenti** > **Gestione Pacchetti NuGet** > **Console di Gestione pacchetti**.

3. In Console di Gestione pacchetti eseguire il comando seguente per importare Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importare nel progetto il pacchetto NuGet di **Archiviazione di Azure**. Questo pacchetto è necessario perché in questo esempio si usa l'API dell'archivio BLOB:

    ```powershell
    Install-Package Azure.Storage
    ```
5. Aggiungere le direttive seguenti al file di origine nel progetto:

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
6. Modificare il nome dello spazio dei nomi in **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Modificare il nome della classe in **MyDotNetActivity** e derivarlo dall'interfaccia **IDotNetActivity** come illustrato di seguito:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementare (aggiungere) il metodo **Execute** dell'interfaccia **IDotNetActivity** alla classe **MyDotNetActivity**. Copiare il codice di esempio seguente nel metodo. Per una descrizione della logica usata in questo metodo, vedere la sezione [Metodo Execute](#execute-method).

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // Initialize the continuation token before using it in the do-while loop.
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
    
           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
9. Aggiungere alla classe i metodi helper riportati di seguito. Questi metodi vengono richiamati dal metodo **Execute** . In particolare, il metodo **Calculate** isola il codice che esegue l'iterazione di ogni BLOB.

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
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
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
    Il metodo GetFolderPath restituisce il percorso della cartella a cui punta il set di dati e il metodo GetFileName restituisce il nome del BLOB o del file a cui punta il set di dati.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Il metodo Calculate calcola il numero di istanze della parola chiave "Microsoft" nei file di input (BLOB nella cartella). Il termine di ricerca ("Microsoft") è hardcoded nel codice.

10. Compilare il progetto. Fare clic su **Compila** dal menu e scegliere **Compila soluzione**.

11. Avviare Esplora risorse e passare alla cartella **bin\\debug** o **bin\\release**. La scelta della cartella dipende dal tipo di compilazione.

12. Creare un file ZIP **MyDotNetActivity.zip** che contiene tutti i file binari disponibili nella cartella **\\bin\\Debug**. È possibile includere il file MyDotNetActivity.**pdb** per ottenere altri dettagli, ad esempio il numero della riga nel codice sorgente che ha causato il problema in caso di errore.

   ![L'elenco della cartella bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

13. Caricare **MyDotNetActivity.zip** come BLOB nel contenitore BLOB `customactivitycontainer` dell'archivio BLOB usato dal servizio collegato StorageLinkedService in ADFTutorialDataFactory. Creare il contenitore BLOB `customactivitycontainer` se non esiste già.

#### <a name="execute-method"></a>Metodo Execute
Questa sezione offre informazioni dettagliate sul codice nel metodo Execute.

1. I membri per l'iterazione della raccolta di input si trovano nello spazio dei nomi [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) . L'iterazione della raccolta di BLOB richiede l'uso della classe **BlobContinuationToken**. In sostanza, è necessario usare un ciclo do-while con il token come meccanismo di uscita dal ciclo. Per altre informazioni, vedere [Come usare l'archivio BLOB da .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Di seguito è illustrato un ciclo di base:

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
   Per altre informazioni, vedere la documentazione relativa al metodo [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx).

2. Il codice per l'uso in modo logico del set di BLOB viene inserito all'interno del ciclo do-while. Nel metodo **Execute** il ciclo do-while passa l'elenco di BLOB a un metodo denominato **Calculate**. Il metodo restituisce una variabile stringa denominata **output** che rappresenta il risultato dell'iterazione di tutti i BLOB nel segmento.

   Restituisce il numero di occorrenze del termine di ricerca "Microsoft" nel BLOB passato al metodo **Calculate**.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Una volta completato, il metodo **Calculate** deve essere scritto in un nuovo BLOB. Quindi, per ogni set di BLOB elaborati è possibile scrivere un nuovo BLOB con i risultati. Per scrivere in un nuovo BLOB, trovare prima di tutto il set di dati di output.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. Il codice chiama anche il metodo helper **GetFolderPath** per recuperare il percorso della cartella, ovvero il nome del contenitore di archiviazione.

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Il metodo GetFolderPath esegue il cast dell'oggetto DataSet a un oggetto AzureBlobDataSet, che include una proprietà denominata FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. Il codice chiama il metodo **GetFileName** per recuperare il nome del file (nome del BLOB). Il codice è simile a quello visto in precedenza per ottenere il percorso della cartella.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Il nome del file viene scritto creando un oggetto URI. Il costruttore URI usa la proprietà **BlobEndpoint** per restituire il nome del contenitore. Il nome del file e il percorso della cartella vengono aggiunti per creare l'URI del BLOB di output.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. Dopo aver scritto il nome del file è possibile scrivere la stringa di output del metodo **Calculate** in un nuovo BLOB:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Creare la data factory
La sezione [Creare l'attività personalizzata](#create-the-custom-activity) ha illustrato come creare un'attività personalizzata e caricare il file ZIP con i file binari e il file PDB in un contenitore BLOB. Questa sezione illustra come creare una data factory con una pipeline che usa l'attività personalizzata.

Il set di dati di input per l'attività personalizzata rappresenta i BLOB (file) nella cartella di input (`mycontainer\\inputfolder`) nell'archiviazione BLOB. Il set di dati di output per l'attività rappresenta i BLOB di output nella cartella di output (`mycontainer\\outputfolder`) nell'archiviazione BLOB.

Lasciare uno o più file nelle cartelle di input:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Ad esempio, rilasciare un file (file.txt) con il contenuto seguente in ognuna delle cartelle:

```
test custom activity Microsoft test custom activity Microsoft
```

Ogni cartella di input corrisponde a una sezione in data factory, anche se la cartella contiene due o più file. Quando ogni sezione viene elaborata dalla pipeline, l'attività personalizzata esegue l'iterazione di tutti i BLOB nella cartella di input per tale sezione.

Verranno visualizzati cinque file di output con lo stesso contenuto. Ad esempio, il file di output generato dall'elaborazione del file nella cartella 2015-11-16-00 ha il contenuto seguente:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Se si rilasciano più file, ovvero file.txt, file2.txt, file3.txt, con lo stesso contenuto nella cartella di input, nel file di output viene visualizzato il contenuto seguente. Ogni cartella (2015-11-16-00 e così via) corrisponde a una sezione in questo esempio, anche se la cartella contiene più file di input.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Il file di output include tre righe, una per ogni file di input (BLOB) presente nella cartella associata alla sezione (2015-11-16-00).

Per ogni esecuzione di attività viene creata un'attività. In questo esempio è presente una sola attività nella pipeline. Quando una sezione viene elaborata dalla pipeline, l'attività personalizzata viene eseguita in Batch per elaborare la sezione. Poiché ci sono cinque sezioni, e ogni sezione può contenere più BLOB o file, in Batch vengono create cinque attività. Quando un'attività viene eseguita in Batch, si tratta in effetti dell'attività personalizzata.

La procedura dettagliata seguente fornisce dettagli aggiuntivi.

#### <a name="step-1-create-the-data-factory"></a>Passaggio 1: Creare la data factory
1. Dopo l'accesso al [portale di Azure](https://portal.azure.com/), seguire questa procedura:

   a. Fare clic su **NUOVO** nel menu a sinistra.

   b. Fare clic su **Data + Analytics** nel pannello **Nuovo**.

   c. Fare clic su **Data factory** nel pannello **Dati e analisi**.

2. Nel pannello **Nuova data factory** immettere **CustomActivityFactory** come nome. Il nome della data factory deve essere univoco a livello globale. Se viene visualizzato l'errore "Data factory name CustomActivityFactory is not available", modificare il nome della data factory. Usare ad esempio yournameCustomActivityFactory e provare di nuovo a crearla.

3. Fare clic su **NOME DEL GRUPPO DI RISORSE**e selezionare un gruppo di risorse esistente o crearne uno.

4. Verificare che la sottoscrizione e l'area in cui si vuole creare la data factory siano quelle corrette.

5. Fare clic su **Crea** nel pannello **Nuova data factory**.

6. Nel dashboard del portale verrà creata la data factory.

7. Dopo la creazione della data factory, viene visualizzata la corrispondente pagina **Data factory** con elencato il contenuto della data factory.

   ![Pagina Data factory](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Passaggio 2: Creare servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory. In questo passaggio verranno collegati l'account di archiviazione e l'account di Batch alla data factory.

#### <a name="create-an-azure-storage-linked-service"></a>Creare un servizio collegato Archiviazione di Azure
1. Fare clic sul riquadro **Crea e distribuisci** nel pannello **Data factory** per **CustomActivityFactory**. Viene visualizzato l'editor di Data Factory.

2. Fare clic su **Nuovo archivio dati** sulla barra dei comandi e scegliere **Archiviazione di Azure.** Lo script JSON usato per creare un servizio collegato di Archiviazione viene visualizzato nell'editor.

   ![Nuovo archivio dati](./media/data-factory-data-processing-using-batch/image7.png)

3. Sostituire **nome account** con il nome del proprio account di archiviazione. Sostituire **chiave account** con la chiave di accesso dell'account di archiviazione. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso nelle risorse di archiviazione](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

   ![Distribuire](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Creare un servizio collegato Azure Batch
In questo passaggio si crea un servizio collegato per l'account di Batch, che verrà usato per eseguire l'attività personalizzata di data factory.

1. Fare clic su **Nuovo calcolo** sulla barra dei comandi e scegliere **Azure Batch.** Lo script JSON usato per creare un servizio collegato di Batch viene visualizzato nell'editor.

2. Nello script JSON:

   a. Sostituire **nome account** con il nome del proprio account di Batch.

   b. Sostituire **chiave di accesso** con la chiave di accesso dell'account di Batch.

   c. Immettere l'ID del pool per la proprietà **poolName**. Per questa proprietà è possibile specificare il nome del pool o l'ID del pool.

   d. Immettere l'URI del batch per la proprietà JSON **batchUri** .

      > [!IMPORTANT]
      > L'URL del pannello **Account di Batch** è nel formato seguente: \<nomeaccount\>.\<area\>.batch.azure.com. Per la proprietà **batchUri** nello script JSON è necessario rimuovere a88"accountname."** dall'URL. Un esempio è `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Pannello Account di Batch](./media/data-factory-data-processing-using-batch/image9.png)

      Per la proprietà **poolName** è anche possibile specificare l'ID del pool anziché il nome del pool.

      > [!NOTE]
      > Il servizio Data Factory non supporta un'opzione su richiesta per Batch, come accade invece per HDInsight. È possibile usare solo il proprio pool di Batch in una data factory.
      >
      >
   
   e. Specificare **StorageLinkedService** for the **StorageLinkedService** . Questo servizio collegato è stato creato nel passaggio precedente. Questo servizio di archiviazione viene usato come area di staging per file e log.

3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

#### <a name="step-3-create-datasets"></a>Passaggio 3: Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di input e di output.

#### <a name="create-the-input-dataset"></a>Creare il set di dati di input
1. Nell'Editor di Data Factory, selezionare il pulsante **Nuovo set di dati** sulla barra degli strumenti. Selezionare **Archivio Blob di Azure** dall'elenco a discesa.

2. Sostituire lo script JSON nel riquadro a destra con il frammento seguente di codice JSON:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
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

    Più avanti in questa procedura dettagliata viene creata una pipeline con ora di inizio: 2015-11-16T00:00:00Z e ora di fine: 2015-11-16T05:00:00Z. Viene pianificata per produrre dati ogni ora, in modo da ottenere cinque sezioni di input/output tra **00**:00:00 e \> **05**:00:00.

    La **frequenza** e l'**intervallo** per il set di dati di input sono impostati su **Ora** e **1**. Ciò significa che la sezione di input è disponibile ogni ora.

    Le ore di inizio per ogni sezione sono rappresentate dalla variabile di sistema **SliceStart** nel precedente frammento di codice JSON. Ecco le ore di inizio per ogni sezione.

    | **Sezione** | **Ora di inizio**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    La proprietà **folderPath** viene calcolata usando la parte di anno, mese, giorno e ora dell'ora di inizio sezione (**SliceStart**). Ecco come viene eseguito il mapping di una cartella di input a una sezione.

    | **Sezione** | **Ora di inizio**          | **Cartella di input**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire la tabella **InputDataset**.

#### <a name="create-the-output-dataset"></a>Creare il set di dati di output
In questo passaggio si crea un altro set di dati di tipo AzureBlob per rappresentare i dati di output.

1. Nell'Editor di Data Factory, selezionare il pulsante **Nuovo set di dati** sulla barra degli strumenti. Selezionare **Archivio Blob di Azure** dall'elenco a discesa.

2. Sostituire lo script JSON nel riquadro a destra con il frammento seguente di codice JSON:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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

    Un BLOB o file di output viene generato per ogni sezione di input. Ecco come viene denominato il file di output per ogni sezione. Tutti i file di output vengono generati in una cartella di output, `mycontainer\\outputfolder`.

    | **Sezione** | **Ora di inizio**          | **File di output**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Tenere presente che tutti i file in una cartella di input, ad esempio 2015-11-16-00, fanno parte di una sezione con l'ora di inizio 2015-11-16-00. Quando la sezione viene elaborata, l'attività personalizzata esamina ogni file e produce una riga nel file di output con il numero di occorrenze del termine di ricerca "Microsoft." Se nella cartella 2015-11-16-00 sono presenti tre file, ci saranno tre righe nel file di output 2015-11-16-00.txt.

3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire l'**OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Passaggio 4: Creare ed eseguire la pipeline con un'attività personalizzata
In questo passaggio si crea una pipeline con un'attività, ovvero l'attività personalizzata creata in precedenza.

> [!IMPORTANT]
> Prima di creare la pipeline, caricare il **file.txt** nelle cartelle di input nel contenitore BLOB, se ancora non è stato fatto. La proprietà **isPaused** è impostata su false nello script JSON della pipeline, quindi la pipeline verrà eseguita immediatamente poiché la data di **inizio** è trascorsa.
>
>

1. Nell'Editor di Data factory fare clic su **Nuova pipeline** sulla barra dei comandi. Se non viene visualizzato il comando, selezionare il simbolo di puntini di sospensione per visualizzarlo.

2. Sostituire lo script JSON nel riquadro a destra con il frammento seguente di codice JSON:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Tenere presente quanto segue:

   * Nella pipeline esiste una sola attività ed è di tipo **DotNetActivity**.
   * **AssemblyName** è impostato sul nome della DLL **MyDotNetActivity.dll**.
   * **EntryPoint** è impostato su **MyDotNetActivityNS.MyDotNetActivity**. Si tratta in sostanza di \<spazio dei nomi\>.\<nome classe\> nel codice.
   * **PackageLinkedService** è impostato su **StorageLinkedService** che punta all'archivio BLOB contenente il file ZIP dell'attività personalizzata. Se vengono usati account di archiviazione diversi per i file di input/output e per il file ZIP dell'attività personalizzata, è necessario creare un altro servizio collegato di Archiviazione. Questo articolo presuppone che venga usato lo stesso account di archiviazione.
   * **PackageFile** è impostato su **customactivitycontainer/MyDotNetActivity.zip**. Ha il formato: \<containerforthezip\>/\<nameofthezip.zip\>.
   * L'attività personalizzata accetta **InputDataset** come input e **OutputDataset** come output.
   * La proprietà **linkedServiceName** dell'attività personalizzata punta ad **AzureBatchLinkedService** per indicare a Data Factory che l'attività personalizzata deve essere eseguita in Batch.
   * L'impostazione **concurrency** è importante. Se si usa il valore predefinito 1, le sezioni vengono elaborate una dopo l'altra, anche se sono disponibili 2 o più nodi di calcolo nel pool di Batch. In questo modo non si sfrutta la funzionalità di elaborazione parallela di Batch. Se si imposta **concurrency** su un valore superiore, ad esempio 2, potranno essere elaborate contemporaneamente due sezioni (corrispondenti a due attività in Batch). In questo caso vengono usate entrambe le macchine virtuali nel pool di Batch. Impostare quindi correttamente la proprietà concurrency.
   * Per impostazione predefinita, viene eseguita solo un'attività (sezione) in una VM in qualsiasi momento. Per impostazione predefinita, **Numero massimo attività per ogni macchina virtuale** è impostato su 1 per un pool di Batch. Come parte dei prerequisiti, è stato creato un pool con questa proprietà impostata su 2. Di conseguenza, sono eseguibili le sezioni di due data factory in una macchina virtuale nello stesso momento.
    - La proprietà **isPaused** è impostata su false per impostazione predefinita. In questo esempio la pipeline viene eseguita immediatamente perché le sezioni hanno inizio nel passato. È possibile impostare questa proprietà su **true** per sospendere la pipeline e reimpostarla su **false** per riavviarla.
    -   I tempi di **inizio** e **fine** sono distanti cinque ore. Le sezioni vengono generate ogni ora, in modo che la pipeline generi cinque sezioni.

3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.

#### <a name="step-5-test-the-pipeline"></a>Passaggio 5: Testare la pipeline
In questo passaggio si testerà la pipeline rilasciando i file nelle cartelle di input. Iniziare testando la pipeline con un file per ogni cartella di input.

1. Nel pannello **Data factory** del portale di Azure fare clic su **Diagramma**.

   ![Diagramma](./media/data-factory-data-processing-using-batch/image10.png)

2. Nella vista **Diagramma** fare doppio clic sul set di dati di input **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

3. Verrà visualizzato il pannello **InputDataset** con tutte e cinque le sezioni pronte. Notare i valori di **ORA DI INIZIO SEZIONE** e **ORA DI FINE SEZIONE** per ogni sezione.

   ![Ora di inizio e ora di fine della sezione di input](./media/data-factory-data-processing-using-batch/image12.png)

4. Nella vista **Diagramma** fare clic su **OutputDataset**.

5. Le cinque sezioni di output vengono visualizzate in stato di **Pronto** se sono state prodotte.

   ![Ora di inizio e ora di fine della sezione di output](./media/data-factory-data-processing-using-batch/image13.png)

6. Usare il portale per visualizzare le attività associate alle sezioni e vedere in quale macchina virtuale viene eseguita ogni sezione. Per altre informazioni, vedere la sezione [Integrazione di Data Factory e Batch](#data-factory-and-batch-integration).

7. I file di output vengono visualizzati in `mycontainer` in `outputfolder` nell'archivio BLOB.

   ![File di output nella risorsa di archiviazione](./media/data-factory-data-processing-using-batch/image15.png)

   Saranno presenti cinque file di output, uno per ogni sezione di input. Il contenuto di ogni file di output sarà simile al seguente:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Il diagramma seguente illustra il mapping tra le sezioni di data factory e le attività in Batch. In questo esempio a una sezione è associata una sola esecuzione.

   ![Diagramma di mapping della sezione](./media/data-factory-data-processing-using-batch/image16.png)

8. Si proverà ora con più file in una cartella. Creare i file **file2.txt**, **file3.txt**, **file4.txt** e **file5.txt** con lo stesso contenuto del file.txt nella cartella **2015-11-06-01**.

9. Nella cartella di output eliminare il file di output **2015-11-16-01.txt**.

10. Nel pannello **OutputDataset** fare clic con il pulsante destro del mouse sulla sezione con **ORA DI INIZIO SEZIONE** impostata su **11/16/2015 01:00:00 AM**. Scegliere quindi **Esegui** per rieseguire/rielaborare la sezione. A questo punto, la sezione ha cinque file anziché un file.

    ![Esegui](./media/data-factory-data-processing-using-batch/image17.png)

11. Dopo che la sezione è stata eseguita e lo stato è diventato **Pronto**, verificare il contenuto nel file di output per questa sezione (**2015-11-16-01.txt**). Il file di output viene visualizzato in `mycontainer` in `outputfolder` nell'archivio BLOB. Deve essere presente una riga per ogni file della sezione.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Se il file di output 2015-11-16-01.txt non è stato eliminato prima di provare con cinque file di input, si vedrà una riga della precedente esecuzione della sezione e cinque righe dell'esecuzione attuale della sezione. Per impostazione predefinita, il contenuto viene aggiunto ai file di output, se esiste già.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integrazione di Data Factory e Batch
Il servizio Data Factory crea un processo in Batch denominato `adf-poolname:job-xxx`.

![Processi Batch](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Per ogni esecuzione attività di una sezione viene creata un'attività nel processo. Se sono presenti 10 sezioni pronte per l'elaborazione, nel processo vengono create 10 attività. È possibile eseguire più sezioni in parallelo se sono disponibili più nodi di calcolo nel pool. È anche possibile eseguire più sezioni nello stesso nodo di calcolo se l'impostazione per il numero massimo di attività per nodo di calcolo è > 1.

In questo esempio ci sono cinque sezioni, quindi cinque attività in Batch. Con la proprietà **concurrency** impostata su **5** nello script JSON della pipeline in data factory e il **Numero massimo di attività per ogni macchina virtuale** impostato su **2** nel pool di Batch con **2** macchine virtuali, le attività vengono eseguite molto velocemente. Controllare l'ora di inizio e fine delle attività.

Usare il portale per visualizzare il processo di Batch e le relative attività associate alle sezioni e vedere in quale macchina virtuale viene eseguita ogni sezione.

![Attività di processo Batch](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Eseguire il debug della pipeline
Il debug è costituito da alcune tecniche di base.

1. Se la sezione di input non è impostata su **Pronto**, verificare che la struttura di cartelle di input sia corretta e che il file.txt sia presente nelle cartelle di input.

   ![Struttura della cartella di input](./media/data-factory-data-processing-using-batch/image3.png)

2. Nel metodo **Execute** dell'attività personalizzata usare l'oggetto **IActivityLogger** per registrare informazioni utili per la risoluzione dei problemi. I messaggi registrati verranno visualizzati nel file user\_0.log.

   Nel pannello **OutputDataset** fare clic sulla sezione per visualizzare il relativo pannello **Sezione Dati**. Dovrebbe essere visualizzata una **Esecuzione attività** per questa sezione. Facendo clic su **Esegui** sulla barra dei comandi è possibile avviare un'altra esecuzione attività per la stessa sezione.

   Quando si fa clic sull'esecuzione attività viene visualizzato il pannello **Dettagli esecuzione attività** con un elenco di file di log. I messaggi registrati verranno visualizzati nel file user\_0.log. Quando si verifica un errore vengono visualizzate tre esecuzioni attività perché il numero di tentativi è impostato su 3 nel codice JSON della pipeline/attività. Quando si fa clic sull'esecuzione attività vengono visualizzati i file di log che è possibile esaminare per risolvere l'errore.

   ![Pannelli OutputDataset e Sezione dati](./media/data-factory-data-processing-using-batch/image18.png)

   Nell'elenco dei file di log fare clic su **user-0.log**. Nel riquadro destro sono riportati i risultati dell'uso del metodo **IActivityLogger.Write**.

   ![Pannello Dettagli esecuzione attività](./media/data-factory-data-processing-using-batch/image19.png)

   È consigliabile cercare anche nel file system-0.log eventuali messaggi di errore di sistema ed eccezioni.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Includere il file **PDB** nel file ZIP in modo che i dettagli dell'errore contengano informazioni come lo stack di chiamate quando si verifica un errore.

4. Tutti i file nel file ZIP dell'attività personalizzata devono trovarsi nel primo livello, senza sottocartelle.

   ![Elenco di file zip di attività personalizzata](./media/data-factory-data-processing-using-batch/image20.png)

5. Assicurarsi che **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e **packageLinkedService** (deve fare riferimento all'archivio BLOB che contiene il file ZIP) siano impostati su valori corretti.

6. Se è stato risolto un errore e si vuole rielaborare la sezione, fare clic con il pulsante destro del mouse sulla sezione nel pannello **OutputDataset**, quindi scegliere **Esegui**.

   ![Pannello OutputDataset opzione Esegui](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > È un contenitore nell'archivio BLOB denominato `adfjobs`. Questo contenitore non viene eliminato automaticamente, ma è possibile farlo senza problemi dopo aver completato il test della soluzione. Analogamente, la soluzione data factory crea un processo di Batch denominato `adf-\<pool ID/name\>:job-0000000001`. Dopo aver eseguito il test della soluzione, è possibile eliminare questo processo se necessario.
   >
   >
7. L'attività personalizzata non usa il file **app.config** dal pacchetto. Pertanto, se il codice legge tutte le stringhe di connessione dal file di configurazione, l'attività non funzionerà in fase di esecuzione. La procedura consigliata quando si usa Batch è contenere tutti i segreti nell'insieme di Azure Key Vault. Quindi usare un'entità servizio basata su certificati per proteggere la chiave dell'insieme di credenziali e distribuire il certificato al pool di Batch. L'attività personalizzata di .NET può quindi accedere ai segreti dall'insieme di credenziali delle chiavi in fase di esecuzione. Questa è una soluzione generica e può essere ridimensionata per qualsiasi tipo di segreto, non solo per una stringa di connessione.

    È una soluzione più semplice, ma non è consigliata. È possibile creare un servizio collegato database SQL con le impostazioni della stringa di connessione. È quindi possibile creare un set di dati che usi il servizio collegato e concatenare il set di dati come set di dati input fittizio per l'attività personalizzata di .NET. È quindi possibile accedere alla stringa di connessione del servizio collegato nel codice dell'attività personalizzata. Dovrebbe funzionare correttamente in fase di esecuzione.  

#### <a name="extend-the-sample"></a>Estendere l'esempio
È possibile estendere questo esempio per ottenere altre informazioni sulle funzionalità di Data Factory e di Batch. Ad esempio, per elaborare le sezioni in un intervallo di tempo diverso, seguire questa procedura:

1. Aggiungere le sottocartelle seguenti in `inputfolder`: 2015-11-16-05, 2015-11-16-06, 07 201-11-16, 2011-11-16-08 e 2015-11-16-09. Posizionare i file di input in queste cartelle. Modificare l'ora di fine per la pipeline da `2015-11-16T05:00:00Z` a `2015-11-16T10:00:00Z`. Nella vista **Diagramma** fare doppio clic su **InputDataset** e verificare che le sezioni di input siano pronte. Fare doppio clic su **OuptutDataset** per visualizzare lo stato delle sezioni di output. Se lo stato è **Pronto**, controllare i file di output nella cartella di output.

2. Aumentare o diminuire l'impostazione **concurrency** per comprenderne gli effetti sulle prestazioni della soluzione, in particolare l'elaborazione che si verifica in Batch. Per altre informazioni sull'impostazione **concurrency**, vedere "Passaggio 4: creare ed eseguire la pipeline con un'attività personalizzata."

3. Creare un pool con un **numero massimo di attività per ogni VM**più alto o più basso. Aggiornare il servizio collegato di Batch nella soluzione data factory per usare il nuovo pool creato. Per altre informazioni sull'impostazione del **Numero massimo di attività per ogni macchina virtuale**, vedere "Passaggio 4: creare ed eseguire la pipeline con un'attività personalizzata."

4. Creare un pool di Batch con la funzionalità **scalabilità automatica**. Il ridimensionamento automatico dei nodi di calcolo in un pool di Batch è una regolazione dinamica della potenza di elaborazione usata dall'applicazione. 

    La formula di esempio di seguito consente di ottenere il comportamento seguente. Quando viene creato inizialmente il pool, viene avviato con una macchina virtuale. La metrica $PendingTasks definisce il numero di attività in esecuzione e quelle in coda. La formula trova il numero medio di attività in sospeso negli ultimi 180 secondi e imposta TargetDedicated di conseguenza. Assicura che TargetDedicated non vada mai oltre 25 macchine virtuali. Man mano che vengono inviate nuove attività, il pool si espande automaticamente. Al termine delle attività le macchine virtuali diventano libere una ad una e la formula di scalabilità automatica riduce il pool. È possibile regolare startingNumberOfVMs e maxNumberofVMs in base alle esigenze.
 
    Formula di scalabilità automatica:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Per altre informazioni, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](../../batch/batch-automatic-scaling.md).

   Se il pool usa il valore predefinito [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), possono essere necessari da 15 a 30 minuti perché il servizio Batch prepari la macchina virtuale prima di eseguire l'attività personalizzata. Se il pool usa un valore autoScaleEvaluationInterval diverso, il servizio Batch può richiedere un valore autoScaleEvaluationInterval + 10 minuti.

5. Nella soluzione di esempio il metodo**Execute** richiama il metodo **Calculate** che elabora una sezione di dati di input per generare una sezione di dati di output. È possibile scrivere un metodo personalizzato per elaborare i dati di input e sostituire la chiamata al metodo **Calculate** nel metodo **Execute** con una chiamata al metodo personalizzato.

### <a name="next-steps-consume-the-data"></a>Passaggi successivi: Utilizzare i dati
Dopo l'elaborazione dei dati, è possibile usarli con strumenti online come Power BI. Ecco alcuni collegamenti che illustrano Power BI e come è possibile usarlo in Azure:

* [Esplorare un set di dati in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Introduzione a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Aggiornamento dei dati in Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure e Power BI - Panoramica di base](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Riferimenti
* [Data factory di Azure](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introduzione al servizio Data Factory](data-factory-introduction.md)
  * [Introduzione a Data Factory](data-factory-build-your-first-pipeline.md)
  * [Usare attività personalizzate in una pipeline di Data Factory](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Nozioni di base di Batch](../../batch/batch-technical-overview.md)
  * [Cenni preliminari sulle funzionalità di Batch](../../batch/batch-api-basics.md)
  * [Creare e gestire un account di Batch nel portale di Azure](../../batch/batch-account-create-portal.md)
  * [Introduzione alla libreria client di Batch per .NET](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
