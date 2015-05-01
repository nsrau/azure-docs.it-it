<properties 
	pageTitle="Come distribuire contenuti Apple HTTP Live Streaming (HLS) - Azure" 
	description="Informazioni su come creare un localizzatore per contenuti basati sul protocollo Apple HTTP Live Streaming (HLS) in un server di origine di Servizi multimediali. Negli esempi di codice, scritti in C#, viene usato l'SDK di Servizi multimediali per .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





<h1>Procedura: Distribuire contenuti in streaming Apple HLS</h1>

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Distribuire contenuti in streaming](media-services-deliver-streaming-content.md).

Questo argomento descrive come creare un localizzatore per contenuti basati sul protocollo Apple HTTP Live Streaming (HLS) in un server di origine di Servizi multimediali. Questo approccio consente di creare un URL per il contenuto Apple HLS e fornirlo ai dispositivi Apple iOS. L'approccio di base alla creazione dell'URL del localizzatore è lo stesso. Creare un localizzatore del percorso dell'asset Apple Live Streaming (HLS) in un server di origine e quindi l'URL completo che punta al manifesto per i contenuti in streaming.

Il seguente esempio di codice suppone che sia già stato ottenuto un riferimento a un asset in streaming HLS e che nel codice si faccia riferimento alla variabile denominata **assetToStream**. Dopo l'esecuzione del codice per la generazione di un localizzatore di origine sull'asset, è possibile usare l'URL risultante per riprodurre i contenuti in streaming in un dispositivo iOS, ad esempio un iPad o un iPhone.

Per creare un localizzatore per contenuti in streaming Apple HLS:

   1. Ottenere un riferimento al file manifesto nell'asset
   2. Definire un criterio di accesso
   3. Creare il localizzatore di origine mediante una chiamata al metodo CreateLocator
   4. Creare un URL del file manifesto

Il seguente codice illustra come implementare i passaggi:

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

Per altre informazioni sulla distribuzione di asset, vedere:
<ul>
<li><a href="http://msdn.microsoft.com/library/jj129575.aspx">Recapito di asset con l'SDK di Servizi multimediali per .NET</a></li>
<li><a href="http://msdn.microsoft.com/library/jj129578.aspx">Distribuzione di asset con l'API REST di Servizi multimediali</a></li>
</ul>

<h2>Passaggi successivi</h2>

Questo argomento conclude la serie dedicata all'uso di Servizi multimediali di Azure. È stato illustrato come configurare il computer per lo sviluppo di Servizi multimediali e per l'esecuzione di attività di programmazione comuni. Per altre informazioni sulla programmazione di Servizi multimediali, vedere le seguenti risorse:

-   [Documentazione di Servizi multimediali di Azure][]
-   [Introduzione all'SDK di Servizi multimediali per .NET][]
-   [Creazione di applicazioni con l'SDK di Servizi multimediali per .NET][]
-   [Creazione di applicazioni con l'API REST di Servizi multimediali][]
-   [Forum su Servizi multimediali][]
-	[Come monitorare un account di Servizi multimediali](media-services-monitor-services-account.md)
-	[Come gestire il contenuto in Servizi multimediali](media-services-manage-content.md)

[Documentazione di Servizi multimediali di Azure]: http://go.microsoft.com/fwlink/?linkid=245437
[Introduzione all'SDK di Servizi multimediali per .NET]: http://go.microsoft.com/fwlink/?linkid=252966
[Creazione di applicazioni con l'API REST di Servizi multimediali]: http://go.microsoft.com/fwlink/?linkid=252967
[Creazione di applicazioni con l'SDK di Servizi multimediali per .NET]: http://go.microsoft.com/fwlink/?linkid=247821
[Forum su Servizi multimediali]: http://social.msdn.microsoft.com/Forums/MediaServices/threads


<!--HONumber=52-->