<properties urlDisplayName="Delivering Media Assets" pageTitle="Come distribuire asset di file multimediali - Azure" metaKeywords="" description="Informazioni sulle opzioni per la distribuzione di asset di file multimediali caricati in Servizi multimediali in Azure. Negli esempi di codice, scritti in C#, viene usato Media Services SDK per .NET." metaCanonical="" services="media-services" documentationCenter="" title="Procedura: Distribuire un asset mediante download" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Procedura: Distribuire un asset mediante download

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Gestire gli asset][Procedura: Gestire gli asset].

In questo argomento vengono illustrate le opzioni per la distribuzione di asset di file multimediali caricati in Servizi multimediali. È possibile distribuire contenuti di Servizi multimediali in numerosi scenari di applicazione. È possibile scaricare asset di file multimediali oppure accedervi mediante un localizzatore. I contenuti multimediali possono essere inviati a un'altra applicazione o un altro provider di contenuti. Per ottenere livelli più elevati di prestazioni e scalabilità, è anche possibile distribuire contenuti usando una rete per la distribuzione di contenuti (CDN), ad esempio la rete CDN di Azure.

In questo esempio viene illustrato come scaricare asset di file multimediali da Servizi multimediali. Il codice esegue query sui processi associati all'account di Servizi multimediali mediante l'ID processo e accede alla relativa raccolta **OutputMediaAssets**, ovvero il set con uno o più asset di file multimediali di output risultante dall'esecuzione di un processo. In questo esempio di codice viene illustrato come scaricare asset di file multimediali di output da un processo, ma lo stesso approccio può essere usato anche per scaricare altri asset.

     
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }

Per altre informazioni sulla distribuzione di asset, vedere:

-   [Distribuzione di asset con Media Services SDK per .NET][Distribuzione di asset con Media Services SDK per .NET]
-   [Distribuzione di asset con l'API REST di Servizi multimediali][Distribuzione di asset con l'API REST di Servizi multimediali]

</p>
## Passaggi successivi

In questo argomento è stato illustrato il download di un asset da Archiviazione di Azure. Per informazioni su altri modi per distribuire asset, vedere la [procedura relativa alla distribuzione di contenuti in streaming][procedura relativa alla distribuzione di contenuti in streaming].

  [Procedura: Gestire gli asset]: ../media-services-manage-assets/
  [Distribuzione di asset con Media Services SDK per .NET]: http://msdn.microsoft.com/it-it/library/jj129575.aspx
  [Distribuzione di asset con l'API REST di Servizi multimediali]: http://msdn.microsoft.com/it-it/library/jj129578.aspx
  [procedura relativa alla distribuzione di contenuti in streaming]: ../media-services-deliver-streaming-content/
