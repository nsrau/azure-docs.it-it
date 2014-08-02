<properties linkid="develop-media-services-how-to-guides-deliver-apple-live-streaming" urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="How to Deliver Apple HTTP Live Streaming (HLS) - Azure" metaKeywords="" description="Learn how to create a locator to Apple HTTP Live Stream (HLS) content on Media Services origin server. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver Apple HLS streaming content" authors="migree" solutions="" manager="" editor="" />

Procedura: Distribuire contenuti in streaming Apple HLS
=======================================================

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Distribuire contenuti in streaming](http://go.microsoft.com/fwlink/?LinkID=301942&clcid=0x409).

In questo argomento viene illustrato come creare un localizzatore per contenuti basati sul protocollo Apple HTTP Live Streaming (HLS) in un server di origine di Servizi multimediali. Questo approccio consente di creare un URL per il contenuto Apple HLS e fornirlo ai dispositivi Apple iOS. L'approccio di base alla creazione dell'URL del localizzatore è lo stesso. Creare un localizzatore del percorso dell'asset Apple Live Streaming (HLS) in un server di origine e quindi l'URL completo che punta al manifesto per i contenuti in streaming.

Nell'esempio di codice seguente si suppone che sia già stato ottenuto un riferimento a un asset in streaming HLS e che nel codice si faccia riferimento alla variabile denominata **assetToStream**. Dopo l'esecuzione del codice per la generazione di un localizzatore di origine sull'asset, è possibile utilizzare l'URL risultante per riprodurre i contenuti in streaming in un dispositivo iOS, ad esempio un iPad o un iPhone.

Per creare un localizzatore per contenuti in streaming Apple HLS:

1.  Ottenere un riferimento al file manifesto nell'asset
2.  Definire un criterio di accesso
3.  Creare il localizzatore di origine mediante una chiamata al metodo CreateLocator
4.  Creare un URL del file manifesto

Nel codice seguente viene illustrato come implementare i passaggi:

<pre><code>
static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the HLS streaming manifest file from the  
    // collection of files in the asset. 
    var theManifest =
                        from f in assetToStream.AssetFiles
                        where f.Name.EndsWith(".ism")
                        select f;

    // Cast the reference to a true IAssetFile type. 
    IAssetFile manifestFile = theManifest.First();
    IAccessPolicy policy = null;
    ILocator originLocator = null;

    // Create a 30-day readonly access policy. 
    policy = _context.AccessPolicies.Create("Streaming HLS Policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

    // Create a URL to the HLS streaming manifest file. Use this for playback
    // in Apple iOS streaming clients.
    string urlForClientStreaming = originLocator.Path
        + manifestFile.Name + "/manifest(format=m3u8-aapl)";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();

    // Return the locator. 
    return originLocator;
}
</code></pre>

Per ulteriori informazioni sulla distribuzione di asset, vedere:

-   [Distribuzione di asset con Media Services SDK per .NET](http://msdn.microsoft.com/en-us/library/jj129575.aspx)
-   [Distribuzione di asset con l'API REST di Servizi multimediali](http://msdn.microsoft.com/en-us/library/jj129578.aspx)

Passaggi successivi
-------------------

Questo argomento conclude la serie dedicata all'utilizzo di Servizi multimediali di Azure. È stato illustrato come configurare il computer per lo sviluppo di Servizi multimediali e per l'esecuzione di attività di programmazione comuni. Per ulteriori informazioni sulla programmazione di Servizi multimediali, vedere le risorse seguenti:

-   [Documentazione di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?linkid=245437)
-   [Introduzione a Media Services SDK per .NET](http://go.microsoft.com/fwlink/?linkid=252966)
-   [Creazione di applicazioni con Media Services SDK per .NET](http://go.microsoft.com/fwlink/?linkid=247821)
-   [Creazione di applicazioni con l'API REST di Servizi multimediali](http://go.microsoft.com/fwlink/?linkid=252967)
-   [Forum su Servizi multimediali](http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads)
-   [Come monitorare un account di Servizi multimediali](http://www.windowsazure.com/en-us/manage/services/media-services/how-to-monitor-a-media-services-account/)
-   [Come gestire il contenuto in Servizi multimediali](http://www.windowsazure.com/en-us/manage/services/media-services/how-to-manage-content-in-media-services/)

