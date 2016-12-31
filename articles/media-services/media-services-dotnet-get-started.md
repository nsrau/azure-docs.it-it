---
title: Introduzione alla distribuzione di contenuto su richiesta con .NET | Documentazione di Microsoft
description: Questa esercitazione illustra il processo di implementazione di un&quot;applicazione di distribuzione di contenuti su richiesta con Servizi multimediali di Azure tramite .NET.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/11/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 24d324a724792051eb6d86026da7b41ee9ff87b1
ms.openlocfilehash: 26720340d72c31016e51cc33589388780a2f4a8a


---

# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introduzione alla distribuzione di contenuti su richiesta utilizzando .NET SDK
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). 
> 
> 

## <a name="overview"></a>Overview
Questa esercitazione illustra il processo di implementazione di un'applicazione di distribuzione di contenuti Video on Demand (VoD) usando l'SDK di Servizi multimediali di Azure per .NET.

L'esercitazione descrive il flusso di lavoro di base di Servizi multimediali nonché gli oggetti e le attività di programmazione usati più di frequente per lo sviluppo basato su Servizi multimediali. Al termine dell'esercitazione sarà possibile eseguire lo streaming o il download progressivo di un file multimediale di esempio caricato, codificato e scaricato.

## <a name="what-youll-learn"></a>Contenuto dell'esercitazione
Nell'esercitazione viene illustrato come eseguire le attività seguenti:

1. Creare un account di Servizi multimediali (usando il portale di Azure).
2. Configurare un endpoint di streaming usando il portale di Azure.
3. Creare e configurare un progetto di Visual Studio.
4. Connettersi all'account di Servizi multimediali.
5. Creare un nuovo asset e caricare un file video.
6. Codificare il file di origine in un set di file MP4 a velocità in bit adattiva.
7. Pubblicare l'asset e ottenere gli URL di streaming e di download progressivo.
8. Testare riproducendo i contenuti.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione è necessario quanto segue.

* Per completare l'esercitazione, è necessario un account Azure. 
  
    Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). sono inclusi crediti da usare per provare i servizi di Azure a pagamento. Una volta esauriti i crediti, è possibile mantenere l'account e usare le funzionalità e i servizi di Azure gratuiti, ad esempio la funzionalità App Web nel servizio app di Azure.
* Sistemi operativi: Windows 8 o versione successiva, Windows 2008 R2, Windows 7.
* .NET Framework 4.0 o versione successiva.
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express) o versioni successive.

## <a name="download-sample"></a>Scaricare un esempio
È possibile ottenere ed eseguire un esempio [qui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Creare un account Servizi multimediali di Azure con il portale di Azure
I passaggi descritti in questa sezione illustrano come creare un account Servizi multimediali di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+Nuovo** > **Contenuti multimediali e rete CDN** > **Servizi multimediali**.
   
    ![Creare Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. In **CREARE UN ACCOUNT DEL SERVIZIO MULTIMEDIALE** immettere i valori richiesti.
   
    ![Creare Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. In **Nome account**immettere il nome del nuovo account di AMS. Un nome di account di Servizi multimediali deve essere composto da tutte lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.
   2. In Sottoscrizione selezionare una delle diverse sottoscrizioni di Azure per le quali è disponibile l'accesso.
   3. In **Gruppo di risorse**selezionare la risorsa nuova o esistente.  Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Fare clic [qui](../azure-resource-manager/resource-group-overview.md#resource-groups) per altre informazioni.
   4. In **Località** selezionare l'area geografica usata per archiviare i contenuti multimediali e i record di metadati per l'account Servizi multimediali. Questa area viene usata per elaborare e riprodurre in streaming il contenuto multimediale. Nella casella dell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili. 
   5. In **Account di archiviazione**selezionare un account di archiviazione per l'archivio BLOB del contenuto multimediale dell'account Servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account Servizi multimediali oppure è possibile crearne uno. Un nuovo account di archiviazione viene creato nella stessa area geografica. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.
      
       Altre informazioni sull'archiviazione sono disponibili [qui](../storage/storage-introduction.md).
   6. Selezionare **Aggiungi al dashboard** per visualizzare lo stato della distribuzione di account.
4. Fare clic su **Crea** nella parte inferiore del form.
   
    Dopo aver creato l'account, lo stato diventa **In esecuzione**. 
   
    ![Impostazioni di Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Per gestire l'account AMS, ad esempio per caricare video, codificare asset, monitorare lo stato dei processi, usare la finestra **Impostazioni** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurare endpoint di streaming usando il portale di Azure
Uno degli scenari più frequenti dell'utilizzo di Servizi multimediali di Azure riguarda la distribuzione ai client di contenuto video in streaming a bitrate adattivo. Servizi multimediali supporta le tecnologie di streaming a bitrate adattivo seguenti: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH.

Servizi multimediali include la funzionalità per la creazione dinamica dei pacchetti, che consente di distribuire contenuto con codifica MP4 a bitrate adattivo nei formati supportati da Servizi multimediali, come MPEG DASH, HLS e Smooth Streaming in modalità JIT, senza dover archiviare le versioni predefinite di ognuno di questi formati di streaming.

Per sfruttare i vantaggi del servizio di creazione dinamica dei pacchetti, è necessario seguire questa procedura:

* Codificare il file (di origine) in formato intermedio in un set di file MP4 a bitrate adattivo. La procedura per la codifica è descritti più avanti in questa esercitazione.  
* Creare almeno un'unità di streaming per l' *endpoint di streaming* da cui si prevede di distribuire il contenuto. I passaggi seguenti illustrano come modificare il numero di unità di streaming.

Con la creazione dinamica dei pacchetti si archiviano e si pagano solo i file in un singolo formato di archiviazione e Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Per creare e modificare il numero di unità riservate di streaming, seguire questa procedura:

1. Nella finestra **Impostazioni** fare clic su **Endpoint di streaming**. 
2. Fare clic sull'endpoint di streaming predefinito. 
   
    Verrà visualizzata la finestra **DETTAGLI ENDPOINT DI STREAMING PREDEFINITO** .
3. Per specificare il numero di unità di streaming, usare il dispositivo di scorrimento di **Unità di streaming** .
   
    ![Unità di streaming](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Fare clic sul pulsante **Salva** per salvare le modifiche apportate.
   
   > [!NOTE]
   > Il completamento dell'allocazione di nuove unità può richiedere fino a 20 minuti.
   > 
   > 

## <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

1. Creare una nuova applicazione console C# in Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Immettere un valore nei campi **Nome**, **Percorso** e **Nome soluzione** e fare clic su **OK**.
2. Usare il pacchetto NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) per installare **Azure Media Services .NET SDK Extensions**.  Media Services .NET SDK Extensions è un set di metodi di estensione e funzioni di supporto che semplificano il codice e lo sviluppo con Servizi multimediali. Insieme al pacchetto viene installato anche **Media Services .NET SDK** e vengono aggiunte tutte le altre dipendenze necessarie.

    Per aggiungere riferimenti usando NuGet, seguire questa procedura: in Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Gestisci pacchetti NuGet**. Cercare quindi **windowsazure.mediaservices.extensions** e fare clic su **Installa**.

3. Aggiungere un riferimento all'assembly System.Configuration, che contiene la classe **System.Configuration.ConfigurationManager** usata per accedere ai file di configurazione, ad esempio App.config.

    Per aggiungere un riferimento, seguire questa procedura: in Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi** > **Riferimento** e digitare la configurazione nella casella di ricerca. 

4. Aprire il file App.config (aggiungere il file al progetto, se non è stato aggiunto per impostazione predefinita) e aggiungere una sezione *appSettings* al file. Impostare i valori per il nome e la chiave dell'account di Servizi multimediali di Azure, come visualizzato nel seguente esempio. Per ottenere il nome dell'account e la chiave, accedere al [portale di Azure](https://portal.azure.com/) e selezionare l'account AMS. Selezionare poi **Impostazioni** > **Chiavi**. Nella finestra Gestisci chiavi sono visualizzati il nome dell'account e le chiavi primaria e secondaria. Copiare i valori del nome dell'account e della chiave primaria.
   
        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
   
        </configuration>
5. Sovrascrivere le istruzioni **using** esistenti all'inizio del file Program.cs con il codice seguente.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
6. Creare una nuova cartella nella directory dei progetti e copiare un file MP4 o WMV di cui eseguire codifica e streaming o il download progressivo. In questo esempio viene usato il percorso "C:\VideoFiles".

## <a name="connect-to-the-media-services-account"></a>Connettersi all'account di Servizi multimediali

Quando si usa Servizi multimediali con .NET, è necessario usare la classe **CloudMediaContext** per la maggior parte delle attività di programmazione di Servizi multimediali: connessione all'account di Servizi multimediali, creazione, aggiornamento, accesso ed eliminazione dei seguenti oggetti: asset, file di asset, processi, criteri di accesso, localizzatori e così via.

Sovrascrivere la classe predefinita Program con il codice seguente. Il codice mostra come leggere i valori di connessione dal file App.config e come creare l'oggetto **CloudMediaContext** per connettersi a Servizi multimediali. Per altre informazioni sulla connessione a Servizi multimediali, vedere [Connessione a Servizi multimediali con Media Services SDK per .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).


La funzione **Main** chiama metodi che verranno definiti più in dettaglio in questa sezione.

> [!NOTE]
> Verranno visualizzati errori di compilazione finché si aggiungono definizioni per tutte le funzioni.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

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

Per sfruttare i vantaggi del servizio di creazione dinamica dei pacchetti, è necessario seguire questa procedura:

* Codificare o transcodificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a velocità in bit adattiva,  
* Ottenere almeno un'unità di streaming per l'endpoint di streaming da cui si pianifica la distribuzione dei contenuti.

Il seguente codice mostra come inviare un processo di codifica. Il processo contiene un'attività che indica di transcodificare il file in formato intermedio in un set di file MP4 a velocità in bit adattiva con **Media Encoder Standard**. Il codice invia il processo e ne attende il completamento.

Al termine, è possibile eseguire lo streaming dell'asset o il download progressivo dei file MP4 creati con la transcodifica.
Non è necessario disporre di più di 0 unità di streaming per poter eseguire il download progressivo dei file MP4.

Aggiungere il seguente metodo alla classe Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
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

Dopo aver creato i localizzatori è possibile compilare gli URL usati per eseguire lo streaming o il download dei file.

Un URL di streaming per Smooth Streaming ha il seguente formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Un URL di streaming per HLS ha il seguente formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Un URL di streaming per MPEG DASH ha il seguente formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Un URL di firma di accesso condiviso usato per scaricare i file ha il seguente formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

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


Per riprodurre il video, utilizzare [Lettore di Servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Per testare il download progressivo, incollare un URL in un browser (ad esempio, IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Passaggi successivi: Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Se si è alla ricerca di qualcos’altro.
Se questo argomento non è di aiuto, non contiene ciò che si cerca o in altro modo non soddisfa le esigenze, è possibile inviare commenti e suggerimenti tramite il thread di Disqus riportato di seguito.

<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/



<!--HONumber=Dec16_HO2-->


