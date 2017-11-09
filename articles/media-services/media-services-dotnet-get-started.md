---
title: Introduzione alla distribuzione di contenuto su richiesta con .NET | Documentazione di Microsoft
description: Questa esercitazione illustra il processo di implementazione di un'applicazione di distribuzione di contenuti su richiesta con Servizi multimediali di Azure tramite .NET.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: f0be787ba1ccee067fb1d7e6a6554be32f886089
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introduzione alla distribuzione di contenuti su richiesta utilizzando .NET SDK
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Questa esercitazione illustra il processo di implementazione di un servizio per la distribuzione di contenuto video on demand (VoD) di base con l'applicazione Servizi multimediali di Azure (AMS) usando Azure Media Services .NET SDK.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue:

* Un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* Account di Servizi multimediali. Per creare un account Servizi multimediali, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).
* .NET Framework 4.0 o versione successiva.
* Visual Studio.

Questa esercitazione include le attività seguenti:

1. Avviare l'endpoint di streaming usando il portale di Azure.
2. Creare e configurare un progetto di Visual Studio.
3. Connettersi all'account di Servizi multimediali.
2. Caricare un file video.
3. Codificare il file di origine in un set di file MP4 a velocità in bit adattiva.
4. Pubblicare l'asset e ottenere gli URL di streaming e di download progressivo.  
5. Riprodurre i contenuti.

## <a name="overview"></a>Panoramica
Questa esercitazione illustra il processo di implementazione di un'applicazione di distribuzione di contenuti Video on Demand (VoD) usando l'SDK di Servizi multimediali di Azure per .NET.

L'esercitazione descrive il flusso di lavoro di base di Servizi multimediali nonché gli oggetti e le attività di programmazione usati più di frequente per lo sviluppo basato su Servizi multimediali. Al termine dell'esercitazione sarà possibile eseguire lo streaming o il download progressivo di un file multimediale di esempio caricato, codificato e scaricato.

### <a name="ams-model"></a>Modello AMS

L'immagine seguente illustra alcuni degli oggetti più comuni usati durante lo sviluppo di applicazioni VoD rispetto al modello OData di Servizi multimediali.

Fare clic sull'immagine per visualizzarla a schermo intero.  

<a href="./media/media-services-dotnet-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png"></a> 

È possibile visualizzare il modello completo [qui](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Avviare endpoint di streaming usando il portale di Azure

Uno degli scenari più frequenti dell'uso di Servizi multimediali di Azure riguarda la distribuzione di contenuto video in streaming a bitrate adattivo. Servizi multimediali include la funzionalità per la creazione dinamica dei pacchetti, che consente di distribuire contenuto con codifica MP4 a bitrate adattivo nei formati supportati da Servizi multimediali, come MPEG DASH, HLS e Smooth Streaming in modalità JIT, senza dover archiviare le versioni predefinite di ognuno di questi formati di streaming.

>[!NOTE]
>Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**.

Per avviare l'endpoint di streaming, eseguire queste operazioni:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella finestra Impostazioni fare clic su Endpoint di streaming.
3. Fare clic sull'endpoint di streaming predefinito.

    Verrà visualizzata la finestra DETTAGLI ENDPOINT DI STREAMING PREDEFINITO.

4. Fare clic sull'icona di avvio.
5. Fare clic sul pulsante Salva per salvare le modifiche apportate.

## <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

1. Configurare l'ambiente di sviluppo e popolare il file app.config con le informazioni di connessione, come descritto in [Sviluppo di applicazioni di Servizi multimediali con .NET](media-services-dotnet-how-to-use.md). 
2. Creare una nuova cartella, in un punto qualsiasi nell'unità locale, e copiare un file con estensione mp4 di cui eseguire codifica e streaming o il download progressivo. In questo esempio viene usato il percorso "C:\VideoFiles".

## <a name="connect-to-the-media-services-account"></a>Connettersi all'account di Servizi multimediali

Quando si usa Servizi multimediali con .NET, è necessario usare la classe **CloudMediaContext** per la maggior parte delle attività di programmazione di Servizi multimediali: connessione all'account di Servizi multimediali, creazione, aggiornamento, accesso ed eliminazione dei seguenti oggetti: asset, file di asset, processi, criteri di accesso, localizzatori e così via.

Sovrascrivere la classe predefinita Program con il codice seguente. Il codice mostra come leggere i valori di connessione dal file App.config e come creare l'oggetto **CloudMediaContext** per connettersi a Servizi multimediali. Per altre informazioni, vedere la [connessione all'API Servizi multimediali](media-services-use-aad-auth-to-access-ams-api.md).

Assicurarsi di aggiornare il nome file e il percorso in cui salvare il file multimediale.

La funzione **Main** chiama metodi che verranno definiti più in dettaglio in questa sezione.

> [!NOTE]
> Verranno visualizzati errori di compilazione finché si aggiungono definizioni per tutte le funzioni.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
    }

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Creare un nuovo asset e caricare un file video

In Servizi multimediali i file digitali vengono caricati (o inseriti) in un asset. L'entità **Asset** può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, oltre ai metadati relativi a questi file.  Dopo aver caricato i file, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming. I file nell'asset sono denominati **File di asset**.

Il metodo **UploadFile** definito di seguito chiama **CreateFromFile**, definito in .NET SDK Extensions. **CreateFromFile** crea un nuovo asset in cui viene caricato il file di origine specificato.

Il metodo **CreateFromFile** acquisisce **AssetCreationOptions**, che consente di specificare una delle opzioni di creazione seguenti:

* **None** : non viene usata alcuna crittografia. Si tratta del valore predefinito. Quando si usa questa opzione, il contenuto non è protetto durante il transito, né nell'archiviazione locale.
  Se si pianifica la distribuzione di un file MP4 con il download progressivo, usare questa opzione.
* **StorageEncrypted** : usare questa opzione per crittografare localmente il contenuto non crittografato applicando la crittografia AES (Advanced Encryption Standard) a 256 bit e quindi caricarlo nel servizio Archiviazione di Azure, in cui viene archiviato in forma crittografata. Gli asset protetti con la crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica, quindi ricrittografati facoltativamente prima di essere ricaricati di nuovo come nuovo asset di output. La crittografia di archiviazione viene usata principalmente quando si vogliono proteggere i file multimediali con input di alta qualità con una crittografia avanzata sul disco locale.
* **CommonEncryptionProtected** : usare questa opzione per caricare contenuti già crittografati e protetti con Common Encryption o PlayReady DRM (ad esempio, Smooth Streaming protetto con PlayReady DRM).
* **EnvelopeEncryptionProtected** : usare questa opzione se si stanno caricando contenuti HLS crittografati con AES. I file devono essere stati codificati e crittografati da Transform Manager.

Il metodo **CreateFromFile** consente anche di specificare un callback per visualizzare l'avanzamento del caricamento del file.

Nell'esempio seguente è specificato **None** per le opzioni dell'asset.

Aggiungere il seguente metodo alla classe Program.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificare il file di origine in un set di file MP4 a velocità in bit adattiva
Dopo aver inserito gli asset in Servizi multimediali, i file multimediali possono essere codificati, sottoposti a transmux e all'applicazione di filigrana e così via prima di essere distribuiti ai client. Queste attività vengono pianificate ed eseguite in più istanze del ruolo in background per assicurare prestazioni e disponibilità elevate. Queste attività vengono chiamate processi. Ogni processo è formato da attività atomiche che svolgono le procedure effettive nel file di asset.

Come indicato prima, quando si usa Servizi multimediali di Azure, uno degli scenari più frequenti consiste nella distribuzione di contenuti in streaming a velocità in bit adattiva ai client. Servizi multimediali può creare dinamicamente un pacchetto di un set di file MP4 a velocità in bit adattiva in uno dei formati seguenti: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH.

Per sfruttare la creazione dinamica dei pacchetti è necessario codificare o transcodificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a bitrate adattivo.  

Il seguente codice mostra come inviare un processo di codifica. Il processo contiene un'attività che indica di transcodificare il file in formato intermedio in un set di file MP4 a velocità in bit adattiva con **Media Encoder Standard**. Il codice invia il processo e ne attende il completamento.

Al termine, è possibile eseguire lo streaming dell'asset o il download progressivo dei file MP4 creati con la transcodifica.

Aggiungere il seguente metodo alla classe Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Pubblicare l'asset e ottenere gli URL di streaming e di download progressivo

Per eseguire lo streaming o il download di un asset è necessario prima "pubblicarlo" creando un localizzatore. I localizzatori forniscono l'accesso ai file contenuti nell'asset. Servizi multimediali supporta due tipi di localizzatori: localizzatori OnDemandOrigin, usati per lo streaming dei file multimediali (ad esempio, MPEG DASH, HLS o Smooth Streaming) e localizzatori di firma di accesso condiviso, usati per scaricare i file multimediali. Per altre informazioni sui localizzatori di firma di accesso condiviso, vedere [questo](http://southworks.com/blog/2015/05/27/reusing-azure-media-services-locators-to-avoid-facing-the-5-shared-access-policy-limitation/) blog.

### <a name="some-details-about-url-formats"></a>Alcuni dettagli sui formati di URL

Dopo aver creato i localizzatori, è possibile compilare gli URL usati per eseguire lo streaming o il download dei file. L'esempio in questa esercitazione restituirà URL che possono essere incollati nei browser appropriati. Questa sezione fornisce brevi esempi dei diversi formati.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Un URL di streaming per MPEG DASH ha il seguente formato:

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest**(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Un URL di streaming per HLS ha il seguente formato:

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest**(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Un URL di streaming per Smooth Streaming ha il seguente formato:

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>Un URL di firma di accesso condiviso usato per scaricare i file ha il seguente formato:

{nome contenitore BLOB}/{nome asset}/{nome file}/{firma di accesso condiviso}

Le estensioni dell'SDK di Servizi multimediali per .NET forniscono pratici metodi di supporto che restituiscono URL formattati per l'asset pubblicato.

Il codice seguente usa le estensioni dell'SDK per .NET per creare i localizzatori e ottenere URL per lo streaming e il download progressivo. Il codice mostra anche come scaricare i file in una cartella locale.

Aggiungere il seguente metodo alla classe Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## <a name="test-by-playing-your-content"></a>Testare riproducendo i contenuti.

Dopo aver eseguito il programma definito nella sezione precedente, nella finestra della console vengono visualizzati URL simili al seguente.

URL per streaming adattivo:

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URL per download progressivo (audio e video).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Per eseguire lo streaming del video, incollare l'URL nella casella di testo URL in [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Per testare il download progressivo, incollare un URL in un browser (ad esempio, IE, Chrome, Safari).

Per ulteriori informazioni, vedere gli argomenti seguenti:

- [Riproduzione di contenuti con i lettori esistenti](media-services-playback-content-with-existing-players.md)
- [Sviluppo di applicazioni di lettore video](media-services-develop-video-players.md)
- [Integrazione di uno streaming video adattivo MPEG-DASH in un'applicazione HTML5 con DASH.js](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Scaricare un esempio
L'esempio di codice seguente contiene il codice creato in questa esercitazione: [esempio](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/
