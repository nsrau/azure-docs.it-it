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
ms.date: 01/10/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 855cb5b9cda873a2966465062d0164b2b054b1cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Usare attività personalizzate in una pipeline di Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-use-custom-activities.md)
> * [Versione 2 - Anteprima](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere le informazioni sulle [attività personalizzate nella versione 2](../transform-data-using-dotnet-custom-activity.md).

In una pipeline di Azure Data Factory è possibile usare due tipi di attività.

- [Attività di spostamento dei dati](data-factory-data-movement-activities.md) per spostare i dati fra [archivi dati supportati di origine e sink](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md) per trasformare i dati usando servizi di calcolo come Azure HDInsight, Azure Batch e Azure Machine Learning. 

Per spostare dati da e verso un archivio dati non supportato da Azure Data Factory, creare un'**attività personalizzata** contenente la logica di spostamento dei dati richiesta e usarla in una pipeline. Analogamente, per trasformare o elaborare dati in un modo non supportato dalla Data Factory, creare un'attività personalizzata contenente la logica di trasformazione dei dati richiesta e usarla in una pipeline. 

È possibile configurare un'attività personalizzata per l'esecuzione in un pool di macchine virtuali di **Azure Batch**. Quando si usa Azure Batch, è possibile usare solo un pool esistente di Azure Batch.

La procedura dettagliata seguente riporta le istruzioni complete per creare un'attività .NET personalizzata e usarla in una pipeline. La procedura dettagliata usa il servizio collegato **Azure Batch**. 

> [!IMPORTANT]
> - Non è possibile usare il gateway di gestione dati da un'attività personalizzata per accedere alle origini dati locali. Attualmente il [Gateway di gestione dati](data-factory-data-management-gateway.md) supporta solo le attività di copia e di stored procedure in Data Factory.   

## <a name="walkthrough-create-a-custom-activity"></a>Procedura dettagliata: creare un'attività personalizzata
### <a name="prerequisites"></a>prerequisiti
* Visual Studio 2012/2013/2015
* Scaricare e installare [.NET SDK di Azure](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Prerequisiti di Azure Batch
In questa procedura dettagliata vengono eseguite attività .NET personalizzate usando Azure Batch come risorsa di calcolo. **Azure Batch** è un servizio di piattaforma per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. Azure Batch pianifica l'esecuzione del lavoro a elevato utilizzo di calcolo su una **raccolta di macchine virtuali** gestita e può ridimensionare automaticamente le risorse di calcolo in base alle esigenze dei processi. Vedere l'articolo [Nozioni di base di Azure Batch][batch-technical-overview] per una panoramica dettagliata del servizio Azure Batch.

Per l'esercitazione creare un account Batch di Azure con un pool di VM. Di seguito sono riportati i passaggi necessari:

1. Creare un **account di Azure Batch** tramite il [portale di Azure](http://portal.azure.com). Per istruzioni, vedere l'articolo su come [creare e gestire un account Azure Batch][batch-create-account].
2. Annotare il nome dell'account Azure Batch, la chiave account, l'URI e il nome del pool. È necessario creare un servizio collegato Azure Batch.
    1. Nella home page per l'account Azure Batch, l'**URL** avrà il formato seguente: `https://myaccount.westus.batch.azure.com`. In questo esempio, **myaccount** è il nome dell'account Azure Batch. L'URI usato nella definizione del servizio collegato è l'URL senza il nome dell'account. Ad esempio: `https://<region>.batch.azure.com`.
    2. Fare clic su **Chiavi** nel menu a sinistra e copiare la **CHIAVE DI ACCESSO PRIMARIA**.
    3. Per usare un pool esistente, fare clic su **Pool** nel menu e annotare l'**ID** del pool. Se non è disponibile un pool esistente, andare al passaggio successivo.     
2. Creare un **pool di Azure Batch**.

   1. Nel menu a sinistra del [portale di Azure](https://portal.azure.com) fare clic su **Esplora** e quindi su **Account Batch**.
   2. Selezionare il proprio account Azure Batch per aprire il pannello **Account Batch** .
   3. Fare clic sul riquadro **Pool** .
   4. Nel riquadro **pool** fare clic sul pulsante Aggiungi nella barra degli strumenti per aggiungere un pool.
      1. Immettere un ID per il pool (ID pool). Prendere nota dell' **ID del pool**perché sarà necessario durante la creazione della soluzione Data Factory.
      2. Specificare **Windows Server 2012 R2** per l'impostazione Famiglia di sistemi operativi.
      3. Selezionare un **piano tariffario per il nodo**.
      4. Immettere **2** come valore per l'impostazione **Pool dedicati di destinazione**.
      5. Immettere **2** come valore per l'impostazione **Numero massimo attività per nodo**.
   5. Fare clic su **OK** per creare il pool.
   6. Annotare l'**ID** del pool. 



### <a name="high-level-steps"></a>Procedure generali
Eseguire i seguenti due passaggi di alto livello come parte di questa procedura dettagliata: 

1. Creare un'attività personalizzata contenente una semplice logica di trasformazione/elaborazione dei dati.
2. Creare una data factory di Azure con una pipeline che usa l'attività personalizzata.

### <a name="create-a-custom-activity"></a>Creare un'attività personalizzata
Per creare un'attività personalizzata .NET, è necessario creare un progetto **Libreria di classi .NET** con una classe che implementa l'**interfaccia IDotNetActivity**. Questa interfaccia ha un solo metodo, [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) , e la relativa firma è:

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


Il metodo accetta quattro parametri:

- **linkedServices**. Questa proprietà è un elenco enumerabile di servizi collegati di archiviazione dati a cui fanno riferimento i set di dati di input/output per l'attività.   
- **datasets**. Questa proprietà è un elenco enumerabile di set di dati di input/output per l'attività. È possibile usare questo parametro per ottenere le posizioni e gli schemi definiti da set di dati di input e di output.
- **activity**. Questa proprietà rappresenta l'attività corrente. Può essere usata per accedere alle proprietà estese associate all'attività personalizzata. Per i dettagli, vedere [Accedere a tutte le proprietà estese](#access-extended-properties).
- **logger**. Questo oggetto consente di scrivere commenti di debug che verranno visualizzati nel log dell'utente per la pipeline.

Il metodo restituisce un dizionario che può essere usato per concatenare le attività personalizzate in un secondo momento. Questa funzionalità non è ancora implementata, quindi il metodo restituisce un dizionario vuoto.  

### <a name="procedure"></a>Procedura
1. Creare un progetto **Libreria di classi .NET** .
   <ol type="a">
     <li>Avviare <b>Visual Studio 2017</b>, <b>Visual Studio 2015</b>, <b>Visual Studio 2013</b> oppure <b>Visual Studio 2012</b>.</li>
     <li>Fare clic su <b>File</b>, scegliere <b>Nuovo</b> e quindi fare clic su <b>Progetto</b>.</li>
     <li>Espandere <b>Modelli</b> e quindi selezionare <b>Visual C#</b>. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET per sviluppare l'attività personalizzata.</li>
     <li>Selezionare la <b>libreria di classi</b> dall'elenco relativo ai tipi di progetto visualizzato a destra. In Visual Studio 2017, scegliere <b>Libreria di classi (.NET Framework)</b> </li>
     <li>Immettere <b>MyDotNetActivity</b> for the <b>Nome</b>.</li>
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

    > [!IMPORTANT]
    > L'utilità di avvio del servizio Data Factory richiede la versione 4.3 di WindowsAzure.Storage. Se si aggiunge un riferimento a una versione successiva dell'assembly di Archiviazione di Azure nel progetto di attività personalizzato, viene visualizzato un errore durante l'esecuzione dell'attività. Per risolvere l'errore, vedere la sezione [Isolamento di AppDomain](#appdomain-isolation). 
5. Aggiungere le seguenti istruzioni **using** al file di origine nel progetto.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

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

    Nell'esempio seguente si conta il numero di occorrenze del termine di ricerca (“Microsoft”) in ogni BLOB associato con una sezione dei dati.

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
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];
    
        // to log information, use the logger object
        // log all extended properties            
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }
    
        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;
        
        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset 
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);
    
        // get the first Azure Storate linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;
    
        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;
    
        // get the folder path from the input dataset definition
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

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path   
        logger.Write("Writing blob to the folder: {0}", folderPath);
    
        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
        // log the output file name
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
9. Aggiungere i seguenti metodi helper: 

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

        // get type properties of the dataset   
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the folder path found in the type properties
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
    
        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the blob/file name in the type properties
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

    Il metodo GetFolderPath restituisce il percorso della cartella a cui punta il set di dati e il metodo GetFileName restituisce il nome del BLOB o del file a cui punta il set di dati. Se sono presenti definizioni folderPath che usano variabili come {Year}, {Month}, {Day} e così via, il metodo restituisce la stringa così com'è, senza sostituirla con i valori di runtime. Per i dettagli sull'accesso a SliceStart, SliceEnd e così via, vedere [Accedere a tutte le proprietà estese](#access-extended-properties) .    

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
12. Creare un file ZIP **MyDotNetActivity.zip** che contiene tutti i file binari disponibili nella cartella <project folder>\bin\Debug. Includere il file **MyDotNetActivity.pdb** per ottenere altri dettagli, ad esempio il numero della riga nel codice sorgente che ha causato il problema in caso di errore. 

    > [!IMPORTANT]
    > Tutti i file contenuti nel file con estensione zip dell'attività personalizzata devono trovarsi nel **livello principale** senza sottocartelle.

    ![File di output binari](./media/data-factory-use-custom-activities/Binaries.png)
14. Se non è già presente, creare un contenitore BLOB denominato **customactivitycontainer**. 
15. Caricare MyDotNetActivity.zip come BLOB in customactivitycontainer in un'Archiviazione BLOB di Azure **di uso generico**, non ad accesso frequente/sporadico, a cui si fa riferimento da AzureStorageLinkedService.  

> [!IMPORTANT]
> Se si aggiunge questo progetto di attività .NET a una soluzione in Visual Studio che contiene un progetto Data Factory e si aggiunge un riferimento al progetto dell'attività .NET dal progetto dell'applicazione Data Factory, non è necessario eseguire gli ultimi due passaggi, ovvero creare manualmente il file ZIP e caricarlo nell'Archiviazione BLOB di Azure di uso generico. Quando si pubblicano entità Data Factory utilizzando Visual Studio, questi passaggi vengono eseguiti automaticamente dal processo di pubblicazione. Per altre informazioni, vedere la sezione [Data Factory project in Visual Studio](#data-factory-project-in-visual-studio) (Progetto Data Factory in Visual Studio).

## <a name="create-a-pipeline-with-custom-activity"></a>Creare una pipeline con attività personalizzate
È stata creata un'attività personalizzata ed è stato caricato il file zip con file binari in un contenitore BLOB in un account di Archiviazione di Azure **di uso generico**. Questa sezione illustra come creare una data factory di Azure con una pipeline che usa l'attività personalizzata.

Il set di dati di input per l'attività personalizzata rappresenta i BLOB (file) nella cartella customactivityinput del contenitore adftutorial nell'archiviazione BLOB. Il set di dati di output per l'attività rappresenta i BLOB di output nella cartella customactivityoutput del contenitore adftutorial nell'archiviazione BLOB.

Creare il file **file.txt** con il contenuto seguente e caricarlo nella cartella **customactivityinput** del contenitore **adftutorial**. Se non esiste ancora, creare il contenitore adftutorial. 

```
test custom activity Microsoft test custom activity Microsoft
```

La cartella di input corrisponde a una sezione in Azure Data Factory, anche se la cartella contiene due o più file. Quando ogni sezione viene elaborata dalla pipeline, l'attività personalizzata esegue l'iterazione di tutti i BLOB nella cartella di input per tale sezione.

Verrà visualizzato un file di output nella cartella adftutorial\customactivityoutput con una o più righe, pari al numero di BLOB nella cartella di input:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Di seguito sono elencati i passaggi da eseguire in questa sezione:

1. Creare una **data factory**.
2. Creare **servizi collegati** per il pool di VM di Azure Batch in cui viene eseguita l'attività personalizzata e l'Archiviazione di Azure che contiene i BLOB di input e di output.
3. Creare i **set di dati** di input e di output che rappresentano l'input e l'output dell'attività personalizzata.
4. Creare una **pipeline** che usa l'attività personalizzata.

> [!NOTE]
> Creare il file **file.txt** e caricarlo in un contenitore BLOB, se l'operazione non è ancora stata eseguita. Vedere le istruzioni nella sezione precedente.   

### <a name="step-1-create-the-data-factory"></a>Passaggio 1: Creare la data factory
1. Dopo l'accesso al portale di Azure, seguire questa procedura:
   1. Fare clic su **Crea una risorsa** sul menu di sinistra.
   2. Fare clic su **Dati e analisi** nel pannello **Nuovo**.
   3. Fare clic su **Data factory** nel pannello **Analisi dei dati**.
   
    ![Menu Nuova Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Nel pannello **Nuova data factory** immettere **CustomActivityFactory** come Nome. È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore **Il nome "CustomActivityFactory" per la data factory non è disponibile**, cambiare il nome della data factory, ad esempio, **nomeutenteCustomActivityFactory**, e provare di nuovo a crearla.

    ![Pannello Nuova Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Fare clic su **NOME DEL GRUPPO DI RISORSE**e selezionare un gruppo di risorse esistente o crearne uno.
4. Verificare se la **sottoscrizione** e l'**area** in cui si vuole creare la data factory sono quelle corrette.
5. Fare clic su **Crea** nel pannello **Nuova data factory**.
6. Nel **Dashboard** del portale di Azure verrà visualizzata la data factory in fase di creazione.
7. Dopo aver creato la data factory, verrà visualizzato il pannello corrispondente con elencato il contenuto della data factory.
    
    ![Pannello Data factory](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Passaggio 2: Creare servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. In questo passaggio si collegheranno l'account di archiviazione di Azure e l'account Azure Batch alla data factory.

#### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure
1. Fare clic sul riquadro **Creare e distribuire** nel pannello **DATA FACTORY** per **CustomActivityFactory**. Viene visualizzato l'editor di Data Factory.
2. Fare clic su **Nuovo archivio dati** sulla barra dei comandi e scegliere **Archiviazione di Azure**. Nell'editor verrà visualizzato lo script JSON per la creazione di un servizio collegato Archiviazione di Azure.
    
    ![Nuovo archivio dati - Archiviazione di Azure](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Sostituire `<accountname>` con il nome dell'account di archiviazione di Azure e `<accountkey>` con la chiave di accesso dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere la sezione [Visualizzare, copiare e rigenerare le chiavi di accesso nelle risorse di archiviazione](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

    ![Servizio collegato Archiviazione di Azure](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

#### <a name="create-azure-batch-linked-service"></a>Creare il servizio collegato Azure Batch
1. Nell'editor di Data Factory fare clic su **... Altro** sulla barra dei comandi, fare clic su **Nuovo calcolo** e quindi selezionare **Azure Batch** dal menu.

    ![Nuovo calcolo - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Apportare le modifiche seguenti allo script JSON:

   1. Specificare il nome dell'account Azure Batch per la proprietà **accountName** . L'**URL** nel **pannello dell'account Azure Batch** è nel formato seguente: `http://accountname.region.batch.azure.com`. Per la proprietà **batchUri** nel JSON è necessario rimuovere `accountname.` dall'URL e usare il `accountname` per la `accountName` proprietà JSON.
   2. Specificare la chiave dell'account Azure Batch per la proprietà **accessKey** .
   3. Specificare il nome del pool che è stato creato come parte dei prerequisiti per la proprietà **poolName** . È anche possibile specificare l'ID del pool anziché il nome del pool.
   4. Specificare l'URI di Azure Batch per la proprietà **batchUri** . Esempio: `https://westus.batch.azure.com`.  
   5. Per la proprietà **AzureStorageLinkedService** for the **linkedServiceName** .

        ```json
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

    

### <a name="step-3-create-datasets"></a>Passaggio 3: Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di input e di output.

#### <a name="create-input-dataset"></a>Creare set di dati di input
1. Nell'**editor** della data factory fare clic su **... Altro** sulla barra dei comandi, fare clic su **Nuovo set di dati** e quindi selezionare **Archivio BLOB di Azure**.
2. Sostituire il codice JSON nel riquadro a destra con il frammento JSON seguente.

    ```json
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

   Più avanti in questa procedura dettagliata viene creata una pipeline con ora di inizio: 2016-11-16T00:00:00Z e ora di fine: 2016-11-16T05:00:00Z. Viene pianificata per produrre dati ogni ora, in modo da ottenere cinque sezioni di input/output tra **00**:00:00 -> **05**:00:00.

   La **frequenza** e l'**intervallo** per il set di dati di input sono impostati su **Ora** e **1**. Ciò significa che la sezione di input è disponibile ogni ora. In questo esempio si tratta dello stesso file (file.txt) che si trova nella cartella di input.

   Sono indicate le ore di inizio per ogni sezione, rappresentate dalla variabile di sistema SliceStart nel precedente frammento JSON.
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire **InputDataset**. Controllare che sulla barra del titolo dell'editor sia visualizzato un messaggio simile a **LA CREAZIONE DELLA TABELLA È STATA COMPLETATA** .

#### <a name="create-an-output-dataset"></a>Creare un set di dati di output
1. Nell'**editor di Data Factory** fare clic su **... Altro** sulla barra dei comandi, fare clic su **Nuovo set di dati** e quindi selezionare **Archivio BLOB di Azure**.
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
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Tenere presente che tutti i file in una cartella di input fanno parte di una sezione con le ore di inizio indicate in precedenza. Quando la sezione viene elaborata, l'attività personalizzata esamina ogni file e produce una riga nel file di output con il numero di occorrenze del termine di ricerca ("Microsoft"). Se sono presenti tre file nella cartella di input, ci saranno tre righe nel file di output per ogni sezione oraria: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt e così via.
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire **OutputDataset**.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Creare ed eseguire una pipeline che usi l'attività personalizzata
1. Nell'editor di Data Factory fare clic su **... Altro** e quindi selezionare **Nuova pipeline** sulla barra dei comandi. 
2. Sostituire lo script JSON nel riquadro a destra con lo script JSON seguente:

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
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
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
3. Le cinque sezioni di output si trovano nello stato Ready. Se non sono nello stato Ready, non sono state ancora generate. 

   ![Sezioni di output](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Verificare che i file di output vengano generati nell'archiviazione BLOB nel contenitore **adftutorial** .

   ![output dell'attività personalizzata][image-data-factory-ouput-from-custom-activity]
5. Se si apre il file di output, l'output visualizzato dovrebbe essere simile al seguente:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Usare il [portale di Azure][azure-preview-portal] o i cmdlet di Azure PowerShell per monitorare l'istanza di Data Factory, le pipeline e i set di dati. I messaggi possono essere visualizzati da **ActivityLogger** nel codice per l'attività personalizzata nei log (specifically user-0.log) scaricabili dal portale o con i cmdlet.

   ![scaricare i log dall'attività personalizzata][image-data-factory-download-logs-from-custom-activity]

Vedere [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md) per i passaggi dettagliati per il monitoraggio di set di dati e pipeline.      

## <a name="data-factory-project-in-visual-studio"></a>Progetto Data Factory in Visual Studio  
È possibile creare e pubblicare le entità di Data Factory usando Visual Studio anziché il portale di Azure. Vedere gli articoli [Creare la prima pipeline con Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) e [Copiare dati da BLOB di Azure a SQL Azure](data-factory-copy-activity-tutorial-using-visual-studio.md) per altre informazioni sulla creazione e pubblicazione di entità di Data factory con Visual Studio.

Se si sta creando il progetto Data Factory in Visual Studio, eseguire i passaggi aggiuntivi seguenti:
 
1. Aggiungere il progetto Data Factory alla soluzione di Visual Studio che contiene il progetto dell'attività personalizzata. 
2. Aggiungere un riferimento al progetto di attività .NET dal progetto Data Factory. Fare clic con il pulsante destro del mouse sul progetto Data Factory, scegliere **Aggiungi**, quindi fare clic su **Riferimento**. 
3. Nella finestra di dialogo **Aggiungi riferimento** selezionare il progetto **MyDotNetActivity** e fare clic su **OK**.
4. Creare e pubblicare la soluzione.

    > [!IMPORTANT]
    > Quando si pubblica l'entità Data Factory, viene creato automaticamente un file zip e caricato nel contenitore BLOB: customactivitycontainer. Se il contenitore BLOB non esiste, anche questo viene automaticamente creato.  


## <a name="data-factory-and-batch-integration"></a>Integrazione di Data Factory e Batch
Il servizio Data Factory crea un processo in Azure Batch denominato **adf-poolname: job-xxx**. Fare clic su **Processi** nel menu di sinistra. 

![Azure Data Factory: processi di Batch](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Per ogni esecuzione attività di una sezione viene creata un'attività. Se cinque sezioni sono pronte per l'elaborazione, in questo processo vengono create cinque attività. Se sono presenti più nodi di calcolo nel pool di batch, è possibile eseguire due o più sezioni in parallelo. È anche possibile eseguire più sezioni nello stesso nodo di calcolo se l'impostazione per il numero massimo di attività per nodo di calcolo è > 1.

![Azure Data Factory: attività processi di Batch](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Il diagramma seguente illustra il rapporto tra attività di Azure Data Factory e Batch.

![Data Factory e Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Errori in fase di risoluzione dei problemi
La risoluzione dei problemi implica alcune tecniche di base:

1. Se viene visualizzato l'errore seguente, è possibile che si stia usando un'archiviazione BLOB ad accesso frequente/sporadico anziché usare un'Archiviazione BLOB di Azure di uso generico. Caricare il file zip in un **account di archiviazione di Azure di uso generico**. 
 
    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ``` 
2. Se viene visualizzato l'errore seguente, verificare che il nome della classe nel file CS corrisponda al nome specificato per la proprietà **EntryPoint** nella pipeline JSON. Nella procedura dettagliata il nome della classe è MyDotNetActivity e la proprietà EntryPoint nella pipeline JSON è MyDotNetActivityNS.**MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Se i nomi corrispondono, verificare che tutti i file binari si trovino nella **cartella radice** del file ZIP. Quando si apre il file ZIP, in altre parole, tutti i file devono trovarsi nella cartella radice, non nelle sottocartelle.   
3. Se la sezione di input non è impostata su **Ready**, verificare che la struttura di cartelle di input sia corretta e che **file.txt** sia presente nelle cartelle di input.
3. Nel metodo **Execute** dell'attività personalizzata usare l'oggetto **IActivityLogger** per registrare informazioni utili per la risoluzione dei problemi. I messaggi registrati vengono visualizzati nei file di log dell'utente, ovvero uno o più file denominati user-0.log, user-1.log, user-2.log e così via.

   Nel pannello **OutputDataset** fare clic sulla sezione per visualizzare il relativo pannello **SEZIONE DATI**. Verranno visualizzate le **esecuzioni di attività** per quella sezione. Dovrebbe essere visualizzata una esecuzione attività per questa sezione. Facendo clic su Esegui sulla barra dei comandi è possibile avviare un'altra esecuzione attività per la stessa sezione.

   Quando si fa clic sull'esecuzione attività viene visualizzato il pannello **DETTAGLI ESECUZIONE ATTIVITÀ** con un elenco di file di log. I messaggi registrati verranno visualizzati nel file user_0.log. Quando si verifica un errore vengono visualizzate tre esecuzioni attività perché il numero di tentativi è impostato su 3 nel codice JSON della pipeline/attività. Quando si fa clic sull'esecuzione attività vengono visualizzati i file di log che è possibile esaminare per risolvere l'errore.

   Nell'elenco dei file di log fare clic su **user-0.log**. Nel riquadro destro sono riportati i risultati dell'uso del metodo **IActivityLogger.Write** . Se non si vedono tutti i messaggi, controllare se ci sono altri file di log denominati: user_1.log, user_2.log e così via. Altrimenti il codice potrebbe essersi bloccato dopo l'ultimo messaggio registrato.

   Cercare anche eventuali messaggi di errore di sistema ed eccezioni nel file **system-0.log**.
4. Includere il file **PDB** nel file ZIP in modo che i dettagli dell'errore contengano informazioni come lo **stack di chiamate** quando si verifica un errore.
5. Tutti i file contenuti nel file con estensione zip dell'attività personalizzata devono trovarsi nel **livello principale** senza sottocartelle.
6. Assicurarsi che **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e **packageLinkedService** (deve fare riferimento all'Archiviazione BLOB di Azure **di uso generico** che contiene il file ZIP) siano impostati su valori corretti.
7. Se è stato risolto un errore e si vuole rielaborare la sezione, fare clic con il pulsante destro del mouse sulla sezione nel pannello **OutputDataset**, quindi scegliere **Esegui**.
8. Se viene visualizzato l'errore seguente, si usa il pacchetto di Archiviazione di Azure con una versione > 4.3.0. L'utilità di avvio del servizio Data Factory richiede la versione 4.3 di WindowsAzure.Storage. Per informazioni su una soluzione alternativa nel caso in cui sia necessario usare una versione più recente dell'assembly di Archiviazione di Azure, vedere la sezione [Isolamento di AppDomain](#appdomain-isolation). 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    Se è possibile usare la versione 4.3.0 del pacchetto di Archiviazione di Azure, rimuovere il riferimento esistente al pacchetto di Archiviazione di Azure con versione > 4.3.0 ed eseguire il comando seguente dalla console di Gestione pacchetti NuGet. 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Compilare il progetto. Eliminare l'assembly Azure.Storage con versione > 4.3.0 dalla cartella bin\Debug. Creare un file ZIP con i file binari e il file PDB. Sostituire il file ZIP precedente con questo nel contenitore BLOB (customactivitycontainer). Eseguire di nuovo le sezioni non riuscite, facendo clic sul pulsante destro del mouse sulla sezione e scegliendo Esegui.   
8. L'attività personalizzata non usa il file **app** dal pacchetto. Pertanto, se il codice legge tutte le stringhe di connessione dal file di configurazione, l'attività non funzionerà in fase di esecuzione. La procedura consigliata quando si usa Azure Batch consiste nell'inserire tutti i segreti in un **insieme di credenziali delle chiavi di Azure**, usare un'entità servizio basata su certificato per proteggere l'**insieme di credenziali** e distribuire il certificato nel pool di Azure Batch. L'attività personalizzata .NET può quindi accedere ai segreti dall'insieme di credenziali delle chiavi in fase di esecuzione. Questa è una soluzione generica e può essere ridimensionata per qualsiasi tipo di segreto, non solo per una stringa di connessione.

   Esiste una soluzione più semplice, ma non consigliata: è possibile creare un **servizio collegato di Azure SQL** con le impostazioni della stringa di connessione, creare un set di dati che usa il servizio collegato e concatenare tale set di dati come set di dati di input fittizio all'attività .NET personalizzata. È quindi possibile accedere alla stringa di connessione del servizio collegato nel codice dell'attività personalizzata.  

## <a name="update-custom-activity"></a>Aggiornare l'attività personalizzata
Per aggiornare il codice dell'attività personalizzata, compilarlo e caricare il file ZIP contenente i nuovi file binari nell'archiviazione BLOB.

## <a name="appdomain-isolation"></a>Isolamento di AppDomain
Vedere l' [esempio sul passaggio fra AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) che illustra come creare un'attività personalizzata che non sia vincolata alle versioni assembly usate dal servizio di avvio di Azure Data Factory, ad esempio WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x e così via.

## <a name="access-extended-properties"></a>Accedere a tutte le proprietà estese
È possibile dichiarare estese le proprietà presenti nel codice JSON dell'attività come nell'esempio seguente:

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


Nell'esempio sono presenti due proprietà estese: **SliceStart** e **DataFactoryName**. Il valore di SliceStart si basa sulla variabile di sistema SliceStart. Per un elenco delle variabili di sistema supportate, vedere [Pianificazione ed esecuzione con Data Factory](data-factory-functions-variables.md) . Il valore di DataFactoryName è hardcoded su CustomActivityFactory.

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

## <a name="auto-scaling-of-azure-batch"></a>Scalabilità automatica di Azure Batch
È anche possibile creare un pool di Azure Batch con la funzionalità **Scalabilità automatica** . Ad esempio, è possibile creare un pool di Azure Batch con 0 VM dedicate e una formula di scalabilità basata sul numero di attività in sospeso. 

La formula di esempio seguente consente di ottenere il comportamento seguente: quando il pool viene creato inizialmente, inizia con 1 macchina virtuale. La metrica $PendingTasks definisce il numero di attività in esecuzione e quelle in coda.  La formula trova il numero medio di attività in sospeso negli ultimi 180 secondi e imposta TargetDedicated di conseguenza. Assicura che TargetDedicated non vada mai oltre 25 macchine virtuali. Pertanto, quando vengono inviate nuove attività, il pool si espande automaticamente e al completamento delle attività le macchine virtuali diventano disponibili una alla volta e la scalabilità automatica le riduce. È possibile regolare startingNumberOfVMs e maxNumberofVMs in base alle esigenze.

Formula di scalabilità automatica:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Per i dettagli, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](../../batch/batch-automatic-scaling.md) .

Se il pool usa il valore predefinito [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), possono essere necessari 15-30 minuti perché il servizio Batch prepari la VM prima di eseguire l'attività personalizzata.  Se il pool usa un valore autoScaleEvaluationInterval diverso, il servizio Batch può richiedere un valore autoScaleEvaluationInterval + 10 minuti.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Creazione di un'attività personalizzata tramite .NET SDK
Nella procedura dettagliata di questo articolo, si crea una data factory con una pipeline che usa l'attività personalizzata tramite il portale di Azure. Il codice seguente illustra invece come creare la data factory con .NET SDK. È possibile trovare altre informazioni sull'uso di SDK per creare pipeline a livello di codice nell'articolo sulla [creazione di una pipeline con attività di copia tramite API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md). 

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Eseguire il debug di attività personalizzate in Visual Studio
L'esempio relativo all'[ambiente locale di Azure Data Factory](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) su GitHub include uno strumento che consente di eseguire il debug di attività .NET personalizzate all'interno di Visual Studio.  


## <a name="sample-custom-activities-on-github"></a>Attività personalizzata di esempio su GitHub
| Esempio | Funzioni delle attività personalizzate |
| --- | --- |
| [HttpDataDownloaderSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample)(Esempio relativo al downloader dati HTTP). |Scarica i dati da un endpoint HTTP per l'archivio BLOB di Azure usando l'attività personalizzata C# in Data Factory. |
| [Esempio di analisi del sentimento su Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Chiama un modello ML di Azure ed esegue l'analisi del sentimento, l'assegnazione dei punteggi, la stima e così via. |
| [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)(Esempio relativo all'esecuzione di script R con ADF). |Chiama lo script R eseguendo RScript.exe sul cluster HDInsight in cui è già installato R. |
| [Attività .NET per dominio app](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Usa versioni di assembly diverse da quelle usate dal servizio di avvio di Data Factory |
| [Rielaborare un modello in Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Rielabora un modello in Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
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
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
