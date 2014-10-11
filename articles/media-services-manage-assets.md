<properties linkid="develop-media-services-how-to-guides-manage-assets" urlDisplayName="Manage Assets in Media Services" pageTitle="How to Manage Assets in Media Services - Azure" metaKeywords="" description="Learn how to manage assets on Media Services. You can also manage jobs, tasks, access policies, locators, and more. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Procedura: Gestire gli asset in archivio

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Proteggere un asset][].

Dopo avere creato gli asset multimediali e averli caricati in Servizi multimediali, è possibile accedervi e gestirli sul server. Sul server è possibile gestire anche altri oggetti che fanno parte di Servizi multimediali, tra cui processi, attività, criteri di accesso, localizzatori e altro ancora.

Nell'esempio seguente viene mostrato come eseguire una query per ottenere un asset in base all'ID asset.

<pre><code>
static IAsset GetAsset(string assetId)
{
    // Use a LINQ Select query to get an asset.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Reference the asset as an IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
</code></pre>

Per elencare tutti gli asset disponibili sul server, è possibile usare il metodo riportato di seguito, che scorre la raccolta di asset e visualizza i dettagli su ogni asset.

<pre><code> 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());
}
</code></pre>

Il frammento di codice seguente elimina tutti gli asset dall'account di Servizi multimediali.

<pre><code>
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
</code></pre>

Per altre informazioni sulla gestione di asset, vedere:

-   [Gestione di asset con Media Services SDK for .NET][]
-   [Gestione di asset con l'API REST di Servizi multimediali][]


## Passaggi successivi

Dopo avere acquisito le nozioni necessarie per gestire gli asset, è possibile passare all'argomento [Distribuire un asset mediante download][].

  [Procedura: Proteggere un asset]: http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409
  [Gestione di asset con Media Services SDK for .NET]: http://msdn.microsoft.com/en-us/library/jj129589.aspx
  [Gestione di asset con l'API REST di Servizi multimediali]: http://msdn.microsoft.com/en-us/library/jj129583.aspx
  [Distribuire un asset mediante download]: http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409
