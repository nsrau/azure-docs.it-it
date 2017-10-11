---
title: Pubblicare contenuti di Servizi multimediali di Azure mediante .NET | Microsoft Docs
description: Informazioni su come creare un localizzatore da usare per un URL di streaming. Negli esempi di codice, scritti in C#, viene usato Media Services SDK per .NET.
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 2bcb012eef84faa7c1e13ed22e88e45e4300ed54
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="publish-azure-media-services-content-using-net"></a>Pubblicare contenuti di Servizi multimediali di Azure mediante .NET
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portale](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Overview
È possibile trasmettere in streaming un set MP4 a velocità in bit adattiva creando un localizzatore di streaming OnDemand e un URL di streaming. L'argomento relativo alla [codifica di un asset](media-services-encode-asset.md) illustra come codificare un asset in un set MP4 a bitrate adattivo. 

> [!NOTE]
> Se il contenuto è crittografato, configurare i criteri di distribuzione degli asset (come descritto in [questo](media-services-dotnet-configure-asset-delivery-policy.md) argomento) prima di creare un localizzatore. 
> 
> 

È inoltre possibile usare un localizzatore di streaming OnDemand per creare URL che puntano a file MP4 scaricabili in modo progressivo.  

Questo argomento illustra come creare un localizzatore di streaming OnDemand, per pubblicare l'asset e creare URL di streaming Smooth, MPEG DASH e HLS, e come creare URL di download progressivo. 

## <a name="create-an-ondemand-streaming-locator"></a>Creare un localizzatore di streaming OnDemand
Per creare un localizzatore di streaming OnDemand e ottenere gli URL, è necessario effettuare le operazioni seguenti:

1. Se il contenuto viene crittografato, definire i criteri di accesso.
2. Creare un localizzatore di streaming OnDemand.
3. Se si pianifica lo streaming, ottenere il file manifesto di streaming (.ism) nell'asset. 
   
   Se si pianifica il download progressivo, ottenere i nomi dei file MP4 nell'asset.  
4. Creare URL che puntano al file manifesto o ai file MP4. 


>[!NOTE]
>È previsto un limite di 1.000.000 di criteri per i diversi criteri AMS (ad esempio per i criteri Locator o ContentKeyAuthorizationPolicy). Se si usano sempre gli stessi giorni/autorizzazioni di accesso, usare lo stesso ID criterio. Ad esempio, i criteri dei localizzatori che devono rimanere sul posto per molto tempo (criteri di non-caricamento). Per altre informazioni, vedere [questo](media-services-dotnet-manage-entities.md#limit-access-policies) argomento.

### <a name="use-media-services-net-sdk"></a>Usare l'SDK di Servizi multimediali per .NET
Creare URL di streaming 

    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }

Gli output:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> Lo streaming dei contenuti può essere eseguito anche tramite una connessione SSL. A questo scopo, verificare che gli URL di streaming inizino con HTTPS. Attualmente AMS non supporta SSL con domini personalizzati.
> 
> 

Creare URL di download progressivo 

    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }

Gli output:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Usare le estensioni dell'SDK di Servizi multimediali per .NET
Il codice seguente chiama i metodi delle estensioni dell'SDK per .NET che creano un localizzatore e generano URL Smooth Streaming, HLS e MPEG-DASH per lo streaming adattivo.

    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Scaricare gli asset](media-services-deliver-asset-download.md)
* [Configurare i criteri di distribuzione dell'asset](media-services-dotnet-configure-asset-delivery-policy.md)

