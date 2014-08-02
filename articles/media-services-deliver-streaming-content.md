<properties linkid="develop-media-services-how-to-guides-deliver-streaming-content" urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="How to Deliver Streaming Content from Media Services – Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="" />

Procedura: Distribuire contenuti in streaming
=============================================

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Distribuire un asset mediante download](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409).

Oltre al download di contenuti multimediali da Servizi multimediali, è possibile utilizzare flussi a velocità in bit adattiva per distribuire contenuti. Ad esempio, è possibile creare un URL diretto, denominato localizzatore, per i contenuti in streaming su un server di origine di Servizi multimediali. Le applicazioni client come Microsoft Silverlight possono riprodurre i contenuti in streaming direttamente dal localizzatore.

Nell'esempio seguente viene illustrato come creare un localizzatore di origine per un asset di output generato da un processo. Nell'esempio si suppone che sia già stato ottenuto un riferimento a un asset contenente file Smooth Streaming e nel codice viene fatto riferimento alla variabile denominata **assetToStream**.

Per creare un localizzatore di origine per contenuti in streaming:

1.  Ottenere un riferimento al file manifesto di streaming (file con estensione ism) nell'asset
2.  Definire un criterio di accesso
3.  Creare il localizzatore di origine mediante una chiamata al metodo CreateLocator
4.  Creare un URL del file manifesto

Nel codice seguente viene illustrato come implementare i passaggi:

``` {}
private static ILocator GetStreamingOriginLocator( string targetAssetID)?{?    // Get a reference to the asset you want to stream.
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
    Console.WriteLine("Streaming asset base path on origin: ");?    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");?    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Return the locator. 
    return originLocator;?}?
```

Per ulteriori informazioni sulla distribuzione di asset, vedere:

-   [Distribuzione di asset con Media Services SDK per .NET](http://msdn.microsoft.com/en-us/library/jj129575.aspx)
-   [Distribuzione di asset con l'API REST di Servizi multimediali](http://msdn.microsoft.com/en-us/library/jj129578.aspx)

Passaggi successivi
-------------------

Finora è stata illustrata la distribuzione di contenuti multimediali eseguendo il download da Archiviazione di Azure e utilizzando Smooth Streaming. Nell'argomento successivo dedicato alla [distribuzione di contenuti HLS](http://go.microsoft.com/fwlink/?LinkId=301817) viene illustrata la distribuzione di contenuti in streaming mediante Apple HTTP Live Streaming (HLS).

