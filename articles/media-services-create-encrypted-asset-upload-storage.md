<properties linkid="develop-media-services-how-to-guides-create-assets" urlDisplayName="Create Encrypted Asset and Upload to Storage" pageTitle="Create Encrypted Asset and Upload to Storage Azure" metaKeywords="" description="Learn how to get media content into Media Services by creating and uploading an encrypted asset." metaCanonical="" services="media-services" documentationCenter="" title="How to: Create an encrypted Asset and upload to storage" authors="migree" solutions="" manager="" editor="" />

Procedura: Creare e caricare un asset crittografato nell'archiviazione
======================================================================

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Configurazione del computer per Servizi multimediali](http://go.microsoft.com/fwlink/?LinkID=301751&clcid=0x409).

Per caricare contenuto multimediale in Servizi multimediali, è innanzitutto necessario creare un asset, aggiungervi i file e quindi caricarlo. Questo processo è denominato inserimento di contenuto.

Quando si creano asset, e\` possibile specificare tre diverse opzioni per la crittografia.

-   **AssetCreationOptions.None**: non applica alcuna crittografia. Impostare questa opzione se si desidera creare un asset non crittografato.
-   **AssetCreationOptions.CommonEncryptionProtected**: consente di creare file CENC (Common Encryption Protected). Un esempio è un set di file già crittografati con PlayReady.
-   **AssetCreationOptions.StorageEncrypted**: applica la crittografia di archiviazione. Crittografa un file di input non crittografato prima che venga caricato nei servizi di archiviazione di Azure.

**NOTA**: Si noti che Servizi multimediali offre una crittografia di archiviazione su disco, non in rete come Digital Rights Management (DRM).

Nel codice di esempio seguente vengono eseguite le operazioni seguenti:

-   Creare un asset vuoto.
-   Creare un'istanza di AssetFile da associare all'asset.
-   Creare un'istanza di AccessPolicy che definisce le autorizzazioni e la durata dell'accesso all'asset.
-   Creare un'istanza di Locator che consente l'accesso all'asset.
-   Caricare in Servizi multimediali un singolo file multimediale.

``` {}
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)?{?    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;?}??static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)?{?    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);
    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    return asset;?}?
```

Nel codice seguente viene illustrato come creare un asset e caricare più file.

``` {}
static public IAsset CreateAssetAndUploadMultipleFiles( AssetCreationOptions assetCreationOptions, string folderPath)?{?    var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                        AccessPermissions.Write | AccessPermissions.List);
    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    var blobTransferClient = new BlobTransferClient();
    blobTransferClient.NumberOfConcurrentTransfers = 20;
    blobTransferClient.ParallelTransferThreadCount = 20;

    blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

    var filePaths = Directory.EnumerateFiles(folderPath);

    Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

    if (!filePaths.Any())?    {?        throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));?    }??    var uploadTasks = new List<Task>();
    foreach (var filePath in filePaths)?    {?        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        Console.WriteLine("Created assetFile {0}", assetFile.Name);
                
        // It is recommended to validate AccestFiles before upload. 
        Console.WriteLine("Start uploading of {0}", assetFile.Name);
        uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));?    }??    Task.WaitAll(uploadTasks.ToArray());
    Console.WriteLine("Done uploading the files");

    blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

    locator.Delete();
    accessPolicy.Delete();

    return asset;?}??static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)?{?    if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.?    {?        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);?    }?}??
```

Passaggi successivi
-------------------

Dopo avere caricato un asset in Servizi multimediali, è possibile passare all'argomento [Procedura: Ottenere un'istanza del processore di contenuti multimediali](http://go.microsoft.com/fwlink/?LinkID=301732&clcid=0x409).

