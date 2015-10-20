<properties
	pageTitle="Introduzione alla distribuzione di contenuti su richiesta utilizzando .NET SDK"
	description="Questa esercitazione illustra il processo di implementazione di un'applicazione di distribuzione di contenuti su richiesta con Servizi multimediali di Azure tramite .NET."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="10/05/2015"
	ms.author="juliako"/>


# Introduzione alla distribuzione di contenuti su richiesta utilizzando .NET SDK

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">versione di valutazione gratuita di Azure</a>.

Questa esercitazione illustra il processo di implementazione di un'applicazione di distribuzione di contenuti Video on Demand (VoD) usando l'SDK di Servizi multimediali di Azure per .NET.


L'esercitazione descrive il flusso di lavoro di base di Servizi multimediali nonché gli oggetti e le attività di programmazione usati più di frequente per lo sviluppo basato su Servizi multimediali. Al termine dell'esercitazione sarà possibile eseguire lo streaming o il download progressivo di un file multimediale di esempio caricato, codificato e scaricato.

## Prerequisiti
Per iniziare l'attività di sviluppo con l'SDK di Servizi multimediali per .NET sono previsti i seguenti prerequisiti.

- Sistemi operativi: Windows 8 o versione successiva, Windows 2008 R2, Windows 7.
- .NET Framework 4.5 o .NET Framework 4.0
- Visual Studio 2010, SP1 (Professional, Premium, Ultimate, o Express) o versioni successive.


Questa guida rapida illustra come effettuare le seguenti attività.

1.  Creare un account di Servizi multimediali (usando il portale di Azure).
2.  Configurare l'endpoint di streaming (usando il portale).
3.  Creare e configurare un progetto di Visual Studio.
5.  Connettersi all'account di Servizi multimediali.
6.  Creare un nuovo asset e caricare un file video.
7.  Codificare il file di origine in un set di file MP4 a velocità in bit adattiva.
8.  Pubblicare l'asset e ottenere gli URL di streaming e di download progressivo.  
9.  Riprodurre i contenuti.


##Creare un account di Servizi multimediali usando il portale

1. Nel portale di Azure fare clic su **New**, quindi su **Media Service** e infine su **Quick Create**.

	![Creazione rapida di Servizi multimediali](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. In **NAME** immettere il nome per il nuovo account. Un nome di account Servizi multimediali deve essere composto da tutte lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.

3. In **REGION** selezionare l'area geografica che verrà utilizzata per archiviare i record dei metadati per l'account di Servizi multimediali. Nell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili.

4. In **STORAGE ACCOUNT** selezionare un account di archiviazione per l'archiviazione BLOB del contenuto multimediale dell'account di Servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account di Servizi multimediali oppure è possibile crearne uno nuovo. Un nuovo account di archiviazione viene creato nella stessa area geografica.

5. Se è stato creato un nuovo account di archiviazione, in **NEW STORAGE ACCOUNT NAME** specificare un nome per l'account di archiviazione. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.

6. Nella parte inferiore del modulo fare clic su **Quick Create**.

	È possibile monitorare lo stato di elaborazione nell'area dei messaggi, nella parte inferiore della finestra.

	Dopo aver creato l'account, lo stato passa su **Attivo**.

	Nella parte inferiore della pagina viene visualizzato il pulsante **GESTISCI CHIAVI**. Quando si fa clic su questo pulsante, viene visualizzata una finestra di dialogo con il nome dell'account di Servizi multimediali e le chiavi primaria e secondaria. Per accedere a livello di codice all'account di Servizi multimediali è richiesto il nome dell'account e la chiave primaria.

	![Pagina Servizi multimediali](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

	Quando si fa doppio clic sul nome dell'account, per impostazione predefinita viene visualizzata la pagina **Avvio rapido**. Questa pagina consente di eseguire alcune attività di gestione disponibili anche in altre pagine del portale. È ad esempio possibile caricare un file video sia da questa pagina sia dalla pagina CONTENUTO.

##Configurare l'endpoint di streaming usando il portale

Quando si usa Servizi multimediali di Azure, uno degli scenari più frequenti consiste nella distribuzione di contenuti in streaming a velocità in bit adattiva ai client. Con lo streaming a velocità in bit adattiva, il client può passare a un flusso con velocità in bit maggiore o minore durante la visualizzazione del video, in base alla larghezza di banda attuale della rete, all'utilizzo della CPU e ad altri fattori. Servizi multimediali supporta le seguenti tecnologie di streaming a velocità in bit adattiva: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (solo per i titolari di licenza Adobe PrimeTime/Access).

Servizi multimediali fornisce il servizio di creazione dinamica dei pacchetti, che consente di distribuire i contenuti codificati in formato MP4 o Smooth Streaming con velocità in bit adattiva in formati di streaming supportati da Servizi multimediali (MPEG DASH, HLS, Smooth Streaming, HDS), senza dover ricreare i pacchetti con questi formati di streaming.

Per sfruttare i vantaggi del servizio di creazione dinamica dei pacchetti, è necessario seguire questa procedura:

- Codificare o transcodificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a velocità in bit adattiva (i passaggi per la codifica sono descritti più avanti in questa esercitazione).  
- Ottenere almeno un'unità di streaming per l'**endpoint di streaming** da cui si pianifica la distribuzione dei contenuti.

Con la creazione dinamica dei pacchetti si archiviano e si pagano solo i file in un unico formato di archiviazione e Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Per modificare il numero di unità riservate di streaming, effettuare le seguenti operazioni:

1. Nel [portale](https://manage.windowsazure.com/) fare clic su **Media Services**. Fare quindi clic sul nome del servizio multimediale.

2. Selezionare la pagina ENDPOINT DI STREAMING. Quindi, fare clic sull'endpoint di streaming da modificare.

3. Per specificare il numero di unità di streaming, selezionare la scheda RIDIMENSIONA e spostare il dispositivo di scorrimento **capacità riservata**.

	![Pagina Scale](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. Premere **SALVA** per salvare le modifiche.

	L'allocazione di nuove unità richiede circa 20 minuti.

	>[AZURE.NOTE]Attualmente, se si riporta a zero qualsiasi valore positivo delle unità di streaming, è possibile che lo streaming venga disabilitato per un periodo che può durare fino a un'ora.
	>
	> Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo. Per informazioni sui prezzi, vedere [Dettagli prezzi di Servizi multimediali](http://go.microsoft.com/fwlink/?LinkId=275107).



##Creare e configurare un progetto di Visual Studio

1. Creare una nuova applicazione console C# in Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Immettere un valore nei campi **Nome**, **Percorso** e **Nome soluzione**, quindi fare clic su **OK**.

2. Usare il pacchetto Nuget [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) per installare **Azure Media Services .NET SDK Extensions**. Media Services .NET SDK Extensions è un set di metodi di estensione e funzioni di supporto che semplificano il codice e lo sviluppo con Servizi multimediali. Insieme al pacchetto viene installato anche **Media Services .NET SDK** e vengono aggiunte tutte le altre dipendenze necessarie.

3. Aggiungere un riferimento all'assembly System.Configuration, che contiene la classe **System.Configuration.ConfigurationManager** usata per accedere ai file di configurazione, ad esempio App.config.

4. Aprire il file App.config (aggiungere il file al progetto, se non è stato aggiunto per impostazione predefinita) e aggiungere una sezione *appSettings* al file. Impostare i valori per il nome e la chiave dell'account di Servizi multimediali di Azure, come visualizzato nel seguente esempio. Per ottenere le informazioni sul nome e la chiave dell'account, aprire il portale di Azure, selezionare l'account di Servizi multimediali e fare clic sul pulsante **GESTISCI CHIAVI**.

	<configuration> ... <appSettings> <add key="MediaServicesAccountName" value="Media-Services-Account-Name" /> <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /> </appSettings>
	  
	</configuration>ion&gt; </code></pre>

5. Sovrascrivere le istruzioni **using** esistenti all'inizio del file Program.cs con il seguente codice.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

6. Creare una nuova cartella nella directory dei progetti e copiare un file MP4 o WMV di cui eseguire codifica e streaming o il download progressivo. In questo esempio viene usato il percorso "C:\\VideoFiles".

##Connettersi all'account di Servizi multimediali

Quando si usa Servizi multimediali con .NET, è necessario usare la classe **CloudMediaContext** per la maggior parte delle attività di programmazione di Servizi multimediali: connessione all'account di Servizi multimediali, creazione, aggiornamento, accesso ed eliminazione dei seguenti oggetti: asset, file di asset, processi, criteri di accesso, localizzatori e così via.

Sovrascrivere la classe predefinita Program con il codice seguente. Il codice mostra come leggere i valori di connessone dal file App.config e come creare l'oggetto **CloudMediaContext** per connettersi a Servizi multimediali. Per altre informazioni sulla connessione a Servizi multimediali, vedere [Connessione a Servizi multimediali con Media Services SDK for .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

La funzione **Main** chiama metodi che verranno definiti più in dettaglio in questa sezione.

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

##Creare un nuovo asset e caricare un file video

In Servizi multimediali i file digitali vengono caricati (o inseriti) in un asset. L'entità **Asset** può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati (e anche i metadati relativi a questi file). Dopo aver caricato i file, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming. I file nell'asset sono denominati **File di asset**.

Il metodo **UploadFile** definito di seguito chiama **CreateFromFile** (definito nelle estensioni dell'SDK per .NET). **CreateFromFile** crea un nuovo asset in cui viene caricato il file di origine specificato.

Il metodo **CreateFromFile** acquisisce **AssetCreationOptions** che consente di specificare una delle seguenti opzioni di creazione:

- **None**: non viene usata alcuna crittografia. Si tratta del valore predefinito. Quando si usa questa opzione, il contenuto non è protetto durante il transito, né nell'archiviazione locale. Se si pianifica la distribuzione di un file MP4 con il download progressivo, usare questa opzione.
- **StorageEncrypted**: usare questa opzione per crittografare localmente il contenuto non crittografato applicando la crittografia AES a 256 bit e quindi caricarlo nel servizio Archiviazione di Azure, dove viene archiviato in forma crittografata. Gli asset protetti con la crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica, quindi ricrittografati facoltativamente prima di essere ricaricati di nuovo come nuovo asset di output. La crittografia di archiviazione viene usata principalmente quando si vogliono proteggere i file multimediali con input di alta qualità con una crittografia avanzata sul disco locale.
- **CommonEncryptionProtected**: usare questa opzione per caricare contenuti già crittografati e protetti con Common Encryption o PlayReady DRM (ad esempio, Smooth Streaming protetto con PlayReady DRM).
- **EnvelopeEncryptionProtected**: usare questa opzione se si stanno caricando contenuti HLS crittografati con AES. I file devono essere stati codificati e crittografati da Transform Manager.

Il metodo **CreateFromFile** consente anche di specificare un callback per visualizzare l'avanzamento del caricamento del file.

Nel seguente esempio è specificato **None** per le opzioni dell'asset.

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


##Codificare il file di origine in un set di file MP4 a velocità in bit adattiva

Dopo aver inserito gli asset in Servizi multimediali, i file multimediali possono essere codificati, sottoposti a transmux e all'applicazione di filigrana e così via prima di essere distribuiti ai client. Queste attività vengono pianificate ed eseguite in più istanze del ruolo in background per assicurare prestazioni e disponibilità elevate. Queste attività vengono chiamate Processi e ogni processo è formato da attività atomiche che svolgono le procedure effettive nel file di asset.

Come indicato prima, quando si usa Servizi multimediali di Azure, uno degli scenari più frequenti consiste nella distribuzione di contenuti in streaming a velocità in bit adattiva ai client. Servizi multimediali può creare dinamicamente un pacchetto di un set di file MP4 a velocità in bit adattiva in uno dei seguenti formati: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (solo per i titolari di licenza Adobe PrimeTime/Access).

Per sfruttare i vantaggi del servizio di creazione dinamica dei pacchetti, è necessario seguire questa procedura:

- Codificare o transcodificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a velocità in bit adattiva,  
- Ottenere almeno un'unità di streaming per l'endpoint di streaming da cui si pianifica la distribuzione dei contenuti.

Il seguente codice mostra come inviare un processo di codifica. Il processo contiene un'attività che indica di transcodificare il file in formato intermedio in un set di file MP4 a velocità in bit adattiva con il **Codificatore multimediale di Azure**. Il codice invia il processo e ne attende il completamento.

Al termine, è possibile eseguire lo streaming dell'asset o il download progressivo dei file MP4 creati con la transcodifica. Non è necessario disporre di più di 0 unità di streaming per poter eseguire il download progressivo dei file MP4.

Aggiungere il seguente metodo alla classe Program.

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
		// Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

	    IJob job = _context.Jobs.CreateWithSingleTask(
	        MediaProcessorNames.AzureMediaEncoder,
	        MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
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

##Pubblicare l'asset e ottenere gli URL di streaming e di download progressivo

Per eseguire lo streaming o il download di un asset è necessario prima "pubblicarlo" creando un localizzatore. I localizzatori forniscono l'accesso ai file contenuti nell'asset. Servizi multimediali supporta due tipi di localizzatori: localizzatori OnDemandOrigin, usati per lo streaming dei file multimediali (ad esempio, MPEG DASH, HLS o Smooth Streaming) e localizzatori di firma di accesso condiviso, usati per scaricare i file multimediali.

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

##Riprodurre i contenuti  

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


##Passaggi successivi

###Percorsi di apprendimento di Media Services

È possibile visualizzare i percorsi di apprendimento AMS qui:

- [Flusso di lavoro AMS Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flusso di lavoro AMS Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

### Se si è alla ricerca di qualcos’altro.

Se questo argomento non contiene i risultati desiderati, manca un elemento o in altro modo non soddisfa le esigenze, si prega di inviarci dei suggerimenti tramite il thread di Disqus riportato di seguito.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/

<!---HONumber=Oct15_HO3-->