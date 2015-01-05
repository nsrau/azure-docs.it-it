<properties urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="Come distribuire contenuti in streaming da Servizi multimediali - Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="juliako" manager="dwrede" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#Procedura: Distribuire contenuti in streaming

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Distribuire un asset mediante download](../media-services-deliver-asset-download/).

Oltre al download di contenuti multimediali da Servizi multimediali, è possibile usare lo streaming a velocità in bit adattiva per distribuire contenuti. Ad esempio, è possibile creare un URL diretto, denominato localizzatore, per i contenuti in streaming su un server di origine di Servizi multimediali. Le applicazioni client come Microsoft Silverlight possono riprodurre i contenuti in streaming direttamente dal localizzatore.

Nell'esempio seguente viene illustrato come creare un localizzatore di origine per un asset di output generato da un processo. Nell'esempio si suppone che sia già stato ottenuto un riferimento a un asset contenente file Smooth Streaming e nel codice viene fatto riferimento alla variabile denominata **assetToStream**. 

Per creare un localizzatore di origine per contenuti in streaming:

   1. Ottenere un riferimento al file manifesto di streaming (file con estensione ism) nell'asset 
   2. Definire un criterio di accesso
   3. Creare il localizzatore di origine mediante una chiamata al metodo CreateLocator 
   4. Creare un URL del file manifesto 

Il codice seguente illustra come implementare i passaggi:
<pre><code>
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the streaming manifest file from the  
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
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Create an OnDemandOrigin locator to the asset. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Display some useful values based on the locator.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Return the locator. 
    return originLocator;
}
</code></pre>

Per altre informazioni sulla distribuzione di asset, vedere:
<ul>
<li><a href="http://msdn.microsoft.com/it-it/library/jj129575.aspx">Distribuzione di asset con Media Services SDK per .NET</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/jj129578.aspx">Distribuzione di asset con l'API REST di Servizi multimediali</a></li>
</ul>

<h2>Passaggi successivi</h2>
Finora è stata illustrata la distribuzione di contenuti multimediali eseguendo il download da Archiviazione di Azure e usando Smooth Streaming. L'argomento successivo dedicato alla [distribuzione di contenuti HLS](../media-services-deliver-http-live-streaming-content/) illustra la distribuzione di contenuti in streaming mediante Apple HTTP Live Streaming (HLS)

<!--HONumber=35.1-->
