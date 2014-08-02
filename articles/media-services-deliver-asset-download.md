<properties linkid="develop-media-services-how-to-guides-deliver-media-assets" urlDisplayName="Delivering Media Assets" pageTitle="How to Deliver Media Assets - Azure" metaKeywords="" description="Learn about options for delivering media assets that have been uploaded to Media Services in Azure. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="migree" solutions="" manager="" editor="" />

Procedura: Distribuire un asset mediante download
=================================================

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Gestire gli asset](http://go.microsoft.com/fwlink/?LinkID=301815&clcid=0x409).

In questo argomento vengono illustrate le opzioni per la distribuzione di asset di file multimediali caricati in Servizi multimediali. È possibile distribuire contenuti di Servizi multimediali in numerosi scenari di applicazione. È possibile scaricare asset di file multimediali oppure accedervi mediante un localizzatore. I contenuti multimediali possono essere inviati a un'altra applicazione o un altro provider di contenuti. Per ottenere livelli più elevati di prestazioni e scalabilità, è anche possibile distribuire contenuti utilizzando una rete per la distribuzione di contenuti (CDN), ad esempio la rete CDN di Azure.

In questo esempio viene illustrato come scaricare asset di file multimediali da Servizi multimediali. Il codice esegue query sui processi associati all'account di Servizi multimediali mediante l'ID processo e accede alla relativa raccolta **OutputMediaAssets**, ovvero il set con uno o più asset di file multimediali di output risultante dall'esecuzione di un processo. In questo esempio di codice viene illustrato come scaricare asset di file multimediali di output da un processo, ma lo stesso approccio può essere utilizzato anche per scaricare altri asset.

<pre><code> 
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

    var downloadTasks = new List&lt;Task&gt;();
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
</code></pre>

Per ulteriori informazioni sulla distribuzione di asset, vedere:

-   [Distribuzione di asset con Media Services SDK per .NET](http://msdn.microsoft.com/en-us/library/jj129575.aspx)
-   [Distribuzione di asset con l'API REST di Servizi multimediali](http://msdn.microsoft.com/en-us/library/jj129578.aspx)

Passaggi successivi
-------------------

In questo argomento è stato illustrato il download di un asset da Archiviazione di Azure. Per informazioni su altri modi per distribuire asset, vedere la [procedura relativa alla distribuzione di contenuti in streaming](http://go.microsoft.com/fwlink/?LinkID=301942).

