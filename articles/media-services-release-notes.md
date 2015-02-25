<properties pageTitle="Note sulla versione di Servizi multimediali" description="Note sulla versione di Servizi multimediali" services="media-services" documentationCenter="media" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="media" ms.devlang="dotnet" ms.topic="article" ms.date="10/15/2014" ms.author="juliako"/>


# Note sulla versione di Servizi multimediali di Azure

Nelle presenti note sulla versione vengono riepilogati le modifiche rispetto alle versioni precedenti e i problemi noti.

>[AZURE.NOTE] Microsoft invita i clienti a segnalare i problemi che si verificano e si impegna a risolverli. Per segnalare un problema o rivolgere domande, pubblicare un post nel [forum di MSDN sui Servizi multimediali di Azure].

- [Problemi noti correnti](#issues)
- [Cronologia delle versioni dell'API REST](#rest_version_history)
- [Versione di dicembre 2014](#december_changes_14)
- [Versione di novembre 2014](#november_changes_14)
- [Versione di ottobre 2014](#october_changes_14)
- [Versione di settembre 2014](#september_changes_14)
- [Versione di agosto 2014](#august_changes_14)
- [Versione di luglio 2014](#july_changes_14)
- [Versione di maggio 2014](#may_changes_14)
- [Versione di aprile 2014](#april_changes_14) 
- [Versioni di gennaio/febbraio 2014](#jan_feb_changes_14) 
- [Versione di dicembre 2013](#december_changes_13)
- [Versione di novembre 2013](#november_changes_13)
- [Versione di agosto 2013](#august_changes_13)
- [Versione di giugno 2013](#june_changes_13)
- [Versione di dicembre 2012](#december_changes_12)
- [Versione di novembre 2012](#november_changes_12)
- [Versione di anteprima di giugno 2012](#june_changes_12)


## <a id="issues"></a>Problemi noti correnti

### <a id="general_issues"></a>Problemi generali di Servizi multimediali

<table border="1">
<tr><th>Problema</th><th>Descrizione</yt></tr>
<tr><td>Nell'API REST non sono fornite alcune intestazioni HTTP comuni.</td><td>Se si sviluppano applicazioni di Servizi multimediali tramite l'API REST, alcuni campi di intestazione HTTP comuni, ad esempio CLIENT-REQUEST-ID, REQUEST-ID e RETURN-CLIENT-REQUEST-ID, non sono supportati. Le intestazioni verranno aggiunte in un futuro aggiornamento.</td></tr>
<tr><td>La codifica di un asset con un nome di file contenente caratteri di escape, ad esempio %20, genera un errore simile al seguente "MediaProcessor: file non trovato."</td><td>I nomi di file da aggiungere a un asset e da codificare devono contenere solo caratteri alfanumerici e spazi. Il problema verrà risolto in un futuro aggiornamento.</td></tr>
<tr><td>Il metodo ListBlobs di Azure Storage SDK versione 3.x non riesce.</td><td>Servizi multimediali genera URL SAS basate sulla versione <a href="http://msdn.microsoft.com/it-it/library/azure/dn592123.aspx">12/02/2012</a> . Per elencare tutti i BLOB in un contenitore BLOB con Azure Storage SDK, usare il metodo <a href="http://msdn.microsoft.com/it-it/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a> contenuto in Azure Storage SDK versione 2.x. Il metodo ListBlobs disponibile in Azure Storage SDK versione 3.x non riuscirà.</td></tr>
<tr><td>Il meccanismo di limitazione delle richieste di Servizi multimediali limita l'uso delle risorse per le applicazioni che inviano un numero elevato di richieste al servizio. Il servizio può restituire il codice di stato HTTP di servizio non disponibile (503).</td><td>Per altre informazioni, vedere la descrizione del codice di stato HTTP 503 nell'argomento <a href="http://msdn.microsoft.com/it-it/library/azure/dn168949.aspx">Codici di errore di Servizi multimediali di Azure</a> .</td></tr>
</table><br/>

### <a id="dotnet_issues"></a>Problemi relativi Media Services SDK per .NET

<table border="1">
<tr><th>Problema</th><th>Descrizione</yt></tr>
<tr><td>Gli oggetti di Servizi multimediali nel modulo SDK non possono essere serializzati e di conseguenza non funzionano con Caching di Azure.</td><td>Se si prova a serializzare l'oggetto AssetCollection del modulo SDK per aggiungerlo a Caching di Azure, viene generata un'eccezione.</td></tr>
</table><br/>

## <a id="rest_version_history"></a>Cronologia delle versioni dell'API REST

Per informazioni sulla cronologia versioni dell'API REST di Servizi multimediali, vedere [Informazioni di riferimento sull'API REST di Servizi multimediali di Azure].

## <a id="december_changes_14"></a>Versione di dicembre 2014

###Aggiornamenti generali di Servizi multimediali

- Sono stati aggiunti alcuni aggiornamenti e nuove funzionalità al processore multimediale di indicizzazione di Azure. Per altre informazioni, vedere le [note sulla versione del processore multimediale di indicizzazione di Azure versione 1.1.6.7](http://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- È stata aggiunta una nuova API REST che consente di aggiornare le unità riservate di codifica: [EncodingReservedUnitType con REST](http://msdn.microsoft.com/it-it/library/azure/dn859236.aspx).
- È stato aggiunto il supporto CORS per il servizio di distribuzione delle chiavi.
- Sono stati apportati miglioramenti alle prestazioni delle opzioni per i criteri di autorizzazioni delle query.
- Nel data center cinese l'[URL di distribuzione delle chiavi](http://msdn.microsoft.com/it-it/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) viene ora fornito per ogni cliente, come in tutti gli altri data center.
- È stata aggiunta una durata con destinazione automatica HLS. Quando si esegue lo streaming live, la creazione di pacchetti in HLS avviene sempre in modo dinamico. Per impostazione predefinita, Servizi multimediali calcola automaticamente il rapporto per la creazione di pacchetti dei segmenti HLS (FragmentsPerSegment) in base all'intervallo tra fotogrammi chiave (KeyFrameInterval), noto anche come Group of Pictures (GOP), ricevuti dal codificatore Live. Per altre informazioni, vedere [Uso di Live Streaming di Servizi multimediali di Azure].
 
###Aggiornamenti di Media Services .NET SDK

- [Azure Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) è ora disponibile nella versione 3.1.0.0.
- La dipendenza .Net SDK è stata aggiornata a .NET Framework 4.5.
- È stata aggiunta una nuova API che consente di aggiornare le unità riservate di codifica. Per altre informazioni, vedere [Set di impostazioni di sistema per il codificatore di Servizi multimediali](http://msdn.microsoft.com/it-it/library/azure/jj129582.aspx).
- È stato aggiunto il supporto JWT (JSON Web Token) per l'autenticazione dei token. Per altre informazioni, vedere l'articolo relativo all'[autenticazione di token JWT in Servizi multimediali di Azure e alla crittografia dinamica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Nel modello di licenza PlayReady sono stati aggiunti offset relativi per BeginDate e ExpirationDate.


## <a id="november_changes_14"></a>Versione di novembre 2014

- Servizi multimediali ora consente di inserire contenuti live di Smooth Streaming (FMP4) tramite una connessione SSL. Per l'inserimento tramite SSL, assicurarsi di aggiornare l'URL di inserimento a HTTPS.  Per altre informazioni sullo streaming live, vedere [Uso di Live Streaming di Servizi multimediali di Azure].
- Attualmente non è possibile inserire lo streaming live RTMP tramite una connessione SSL.
- Lo streaming dei contenuti può essere eseguito anche tramite una connessione SSL. A questo scopo, verificare che gli URL di streaming inizino con HTTPS.
- Lo streaming tramite SSL è possibile solo se l'endpoint di streaming da cui si inviano i contenuti è stato creato dopo il 10 settembre 2014. Se gli URL di streaming si basano sugli endpoint di streaming creati dopo il 10 settembre, l'URL contiene "streaming.mediaservices.windows.net" (il nuovo formato). Gli URL di streaming contenenti "origin.mediaservices.windows.net" (il vecchio formato) non supportano SSL. Se l'URL è nel vecchio formato e si vuole poter eseguire lo streaming tramite SSL, [creare un nuovo endpoint di streaming](http://azure.microsoft.com/it-it/documentation/articles/media-services-manage-origins/). Usare gli URL creati con il nuovo endpoint di streaming per lo streaming dei contenuti tramite SSL.
   
## <a id="october_changes_14"></a>Versione di ottobre 2014

### <a id="new_encoder_release"></a>Versione del Codificatore di Servizi multimediali

È stata annunciata una nuova versione del Codificatore multimediale di Azure di Servizi Multimediali. Con la versione più recente del Codificatore multimediale di Azure, all'utente vengono addebitati solo i GB di output, ma per il resto il nuovo codificatore è una funzionalità compatibile con il Codificatore multimediale di Microsoft Azure. Per altre informazioni, vedere [Dettagli prezzi di Servizi multimediali]).

### <a id="oct_sdk"></a>Media Services .NET SDK 

Media Services .NET SDK Extensions è ora alla versione 2.0.0.3.

Media Services SDK per .NET è ora alla versione 3.0.0.8.

Sono state introdotte le seguenti modifiche:

- Refactoring nelle classi di criteri per l'esecuzione di nuovi tentativi.
- Aggiunta della stringa agente utente alle intestazioni delle richieste HTTP.
- Aggiunta del passaggio di compilazione nuget.
- Correzione dei test di scenario per usare il certificato x509 dal repository.
- Convalida delle impostazioni durante l'aggiornamento di un endpoint di canale e di streaming.
 

### Nuovo repository GitHub per l'hosting di esempi di Servizi multimediali

Gli esempi si trovano nel [repository GitHub degli esempi di Servizi multimediali di Azure](https://github.com/Azure/Azure-Media-Services-Samples).


## <a id="september_changes_14"></a>Versione di settembre 2014

È ora disponibile la versione 2.7 dei metadati di REST di Servizi multimediali. Per altre informazioni sugli ultimi aggiornamenti di REST, vedere [Informazioni di riferimento sull'API REST di Servizi multimediali di Azure].

Media Services SDK per .NET è ora alla versione 3.0.0.7.
 
### <a id="sept_14_breaking_changes"></a>Modifiche di rilievo

* **Origin** è stato rinominato in [StreamingEndpoint].
* È stato modificato il comportamento predefinito quando si usa il **portale di gestione di Azure** per codificare e pubblicare i file MP4. 

	Mentre, in precedenza, quando si usava il portale di gestione per pubblicare un asset video costituito da un unico file MP4, veniva creato un URL SAS (che consentiva di scaricare il video da un'archiviazione BLOB), adesso viene generato un URL che punta a un endpoint di flusso di origine di Servizi multimediali di Azure.  Questa modifica, tuttavia, non riguarda i video MP4 che vengono caricati direttamente in Servizi multimediali e pubblicati senza essere codificati da Servizi multimediali di Azure.
	
	Attualmente, per risolvere il problema sono disponibili le due opzioni seguenti. 
	
	* Abilitare le unità di streaming e usare il packaging dinamico per trasmettere l'asset mp4 come presentazione Smooth Streaming.
	
	* Creare un URL SAS per scaricare (o riprodurre in modo progressivo) il file MP4. Per altre informazioni sulla creazione di un localizzatore SAS, vedere [Distribuzione di contenuto]. 


### <a id="sept_14_GA_changes"></a>Nuove funzionalità o scenari nella versione di disponibilità generale

* **Processore multimediale di indicizzazione**. Per altre informazioni vedere [Indicizzazione di file multimediali con Azure Media Indexer].

* L'entità [StreamingEndpoint] permette ora di aggiungere nomi (host) di dominio personalizzati.

	Per poter usare un nome di dominio personalizzato come nome dell'endpoint di streaming di Servizi multimediali, è necessario aggiungere nomi host personalizzati all'endpoint di streaming. Per aggiungere nomi host personalizzati, usare le API REST di Servizi multimediali o l'SDK .NET.
	
	Si applicano le considerazioni seguenti:
	
	* È necessario avere la proprietà del nome di dominio personalizzato.
	
	* La proprietà del nome di dominio deve essere convalidata da Servizi multimediali di Azure. Per convalidare il dominio, creare un CName che mappi <MediaServicesAccountId>.<parent domain> a verifydns.<mediaservices-dns-zone>. 
	
	* È necessario creare un altro CName  che mappi il nome host personalizzato (ad esempio,  sports.contoso.com) al nome host StreamingEndpont di Servizi multimediali (ad esempio,  amstest.streaming.mediaservices.windows.net).


	Per altre informazioni, vedere la proprietà **CustomHostNames** nell'argomento [StreamingEndpoint].

### <a id="sept_14_preview_changes"></a>Nuove funzionalità o scenari nella versione di anteprima pubblica

* Live Streaming Preview. Per altre informazioni, vedere [Uso di Live Streaming di Servizi multimediali di Azure].

* Servizio di distribuzione di chiavi. Per altre informazioni, vedere [Uso della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi].

* Crittografia dinamica AES. Per altre informazioni, vedere [Uso della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi].

* Servizio di distribuzione di licenze per PlayReady. Per altre informazioni, vedere [Uso della crittografia dinamica PlayReady e del server di distribuzione di licenze PlayReady].

* Crittografia dinamica PlayReady. Per altre informazioni, vedere [Uso della crittografia dinamica PlayReady e del server di distribuzione di licenze PlayReady].

* Modello di licenza PlayReady di Servizi multimediali. Per altre informazioni, vedere [Panoramica del modello di licenza PlayReady di Servizi multimediali].

* Trasmissione in flusso di asset di archiviazione crittografati. Per altre informazioni, vedere [Streaming di contenuto crittografato di archiviazione].

## <a id="august_changes_14"></a>Versione di agosto 2014

Quando si esegue la codifica di un asset, al termine del processo viene restituito un asset di output. Fino a questa versione, il codificatore di Servizi multimediali di Azure ha restituito metadati relativi ad asset di output. A partire da questa versione, il codificatore restituisce anche metadati relativi ad asset di input. Per altre informazioni, vedere gli argomenti [Metadati di input] e [Metadati di output].


## <a id="july_changes_14"></a>Versione di luglio 2014

Le seguenti correzioni di bug sono state introdotte per lo strumento per la creazione di pacchetti e il componente di crittografia di Servizi multimediali di Azure:

* Solo l'audio viene riprodotto quando si applica il formato TRANSMUX a un asset di archivio in tempo reale in HTTP Live Streaming. Questo problema è stato risolto e attualmente vengono riprodotti sia l'audio che il video.

* Quando si creano pacchetti di un asset per HTTP Live Streaming e si definisce una crittografia della busta AES a 128 bit, i flussi in pacchetti non vengono riprodotti in dispositivi Android. Questo bug è stato risolto e i flussi in pacchetti vengono riprodotti in dispositivi Android che supportano HTTP Live Streaming.

## <a id="may_changes_14"></a>Versione di maggio 2014

### <a id="may_14_changes"></a>Aggiornamenti generali di Servizi multimediali

È ora possibile usare la funzionalità [Creazione dinamica dei pacchetti] per riprodurre in streaming elementi di HTTP Live Streaming (HLS) v3. A tale scopo, aggiungere il seguente formato al percorso del localizzatore di origine: *.ism/manifest(format=m3u8-aapl-v3). Per altre informazioni, vedere il [blog di Nick Drouin].

La funzionalità Dynamic Packaging supporta attualmente la distribuzione di HTTP Live Streaming (v3 e v4) con crittografia PlayReady basata sulla funzionalità Smooth Streaming crittografata staticamente con PlayReady. Per informazioni su come crittografare Smooth Streaming con PlayReady, vedere [Protezione di Smooth Streaming e MPEG DASH con PlayReady].

### <a name="may_14_donnet_changes"></a>Aggiornamenti di Media Services .NET SDK

In Media Services .NET SDK versione 3.0.0.5 sono stati introdotti i seguenti aggiornamenti:

* Velocità e resilienza maggiori per il caricamento e il download di asset di file multimediali.

* Miglioramenti nella gestione della logica di retry e delle eccezioni temporanee: 

	* Il rilevamento degli errori temporanei e la gestione della logica di retry sono stati migliorati per le eccezioni provocate dall'esecuzione di query, dal salvataggio delle modifiche, dal caricamento o dal download di file. 
	
	* Quando vengono generate eccezioni Web (ad esempio durante una richiesta di token ACS), si nota che ora gli errori irreversibili vengono gestiti più rapidamente.

Per altre informazioni, vedere [Logica di retry in Media Services SDK per .NET].

## <a id="april_changes_14"></a>Versione del codificatore di aprile 2014

### <a name="april_14_enocer_changes"></a>Aggiornamenti del Codificatore di Servizi multimediali

* Supporto aggiunto per gestire i file AVI creati tramite l'editor non lineare Grass Valley EDIUS, in cui il video viene leggermente compresso tramite codec Grass Valley HQ/HQX. Per altre informazioni, vedere la pagina relativa agli [annunci di Grass Valley sullo streaming EDIUS 7 tramite cloud].

* È stato aggiunto supporto per specificare la convenzione di denominazione per i file creati da Media Encoder. Per altre informazioni, vedere [Controllo dei nomi file di output del codificatore di Servizi multimediali].

* Supporto aggiunto per sovrimpressioni video e/o audio. Per altre informazioni, vedere [Creazione di sovrimpressioni].

* Supporto aggiunto per collegare tra loro più segmenti video. Per altre informazioni, vedere [Unione dei segmenti video].

* Bug correlato alla transcodifica di file MP4 in cui l'audio è stato codificato con MPEG-1 Audio layer 3 (noto anche come formato MP3) risolto.


## <a id="jan_feb_changes_14"></a>Versioni di gennaio/febbraio 2014

### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1, 3.0.0.2 e 3.0.0.3

Le modifiche apportate alle versioni 3.0.0.1 e 3.0.0.2 includono:

* Risoluzione dei problemi correlati all'uso di query LINQ con istruzioni OrderBy.

* Soluzioni di test in [Github] divise in test basati su unità e test basati su scenario.

Per altre informazioni su queste modifiche, vedere: [Azure Media Services .NET SDK versioni 3.0.0.1 e 3.0.0.2].

Nella versione 3.0.0.3 sono state introdotte le seguenti modifiche:

* Dipendenze dell'archiviazione di Azure aggiornate per usare la versione 3.0.3.0. 

* È stato risolto il problema relativo alla compatibilità con le versioni precedenti per le versioni 3.0.*.*. 


## <a id="december_changes_13"></a>Versione di dicembre 2013

### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0

>[AZURE.NOTE] Le versioni 3.0.x.x non sono compatibili con le versioni precedenti 2.4.x.x.

La versione più recente di Media Services SDK è ora 3.0.0.0. È possibile scaricare il pacchetto più recente dal sito Web di NuGet oppure ottenere i bit da [Github].

A partire da Media Services SDK versione 3.0.0.0, è possibile usare nuovamente i token di [Servizio di controllo di accesso (ACS) di Azure Active Directory]. Per altre informazioni, vedere la sezione "Riutilizzo di token del Servizio di controllo di accesso" nell'argomento [Connessione a Servizi multimediali con Media Services SDK per .NET].

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK Extensions 2.0.0.0

Azure Media Services .NET SDK Extensions è il nome di un set di metodi di estensione e funzioni di supporto che semplificano il codice e lo sviluppo con Servizi multimediali di Azure. È possibile ottenere i bit più recenti dal sito Web relativo a [Azure Media Services .NET SDK Extensions].

## <a id="november_changes_13"></a>Versione di novembre 2013

### <a name="nov_13_donnet_changes"></a>Modifiche apportate ad Azure Media Services .NET SDK

A partire da questa versione, Media Services SDK per .NET gestisce gli errori temporanei che possono verificarsi quando si effettuano chiamate a livello API REST di Servizi multimediali.

## <a id="august_changes_13"></a>Versione di agosto 2013

### <a name="aug_13_powershell_changes"></a>Cmdlet di PowerShell per Media Services inclusi negli strumenti SDK di Azure

I seguenti cmdlet di PowerShell per Servizi multimediali sono stati inclusi in [azure-sdk-tools].

* Get-AzureMediaServices 

	Ad esempio, `Get-AzureMediaServicesAccount`.

* New-AzureMediaServicesAccount 

	Ad esempio, `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`.

* New-AzureMediaServicesKey 

	Ad esempio, `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount 

	Ad esempio, `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`.

## <a id="june_changes_13"></a>Versione di giugno 2013

### <a name="june_13_general_changes"></a>Modifiche di Servizi multimediali di Azure

Le modifiche citate in questa sezione sono aggiornamenti inclusi nelle versioni di Servizi multimediali di giugno 2013.

* Possibilità di collegare più account di archiviazione a un account di Servizi multimediali. 

	StorageAccount
	
	Asset.StorageAccountName e Asset.StorageAccount

* Possibilità di aggiornare Job.Priority. 

* Entità e proprietà correlate alle notifiche: 

	JobNotificationSubscription
	
	NotificationEndPoint
	
	Processo

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Modifiche apportate ad Azure Media Services .NET SDK

Le seguenti modifiche sono state apportate alle versioni di Media Services SDK di giugno 2013. Il modulo Media Services SDK più recente è disponibile in GitHub.

* A partire dalla versione 2.3.0.0, Media Services SDK consente il collegamento di più account di archiviazione a un unico account di Servizi multimediali. Questa funzionalità è supportata dalle seguenti API:
	
	Tipo IStorageAccount.
	
	Proprietà Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
	
	Proprietà StorageAccount.
	
	Proprietà StorageAccountName.
	
	Per altre informazioni, vedere [Gestione di asset di Servizi multimediali su più account di archiviazione].

* API correlate alle notifiche. A partire dalla versione 2.2.0.0 è possibile ascoltare le notifiche dal servizio di archiviazione delle code di Azure. Per altre informazioni, vedere [Gestione delle notifiche dei processi di Media Services].
	
	Proprietà Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dipendenza da Azure Storage Client SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dipendenza da OData 5.5 (Microsoft.Data.OData.dll).


## <a id="december_changes_12"></a>Versione di dicembre 2012

### <a name="dec_12_dotnet_changes"></a>Modifiche apportate ad Azure Media Services .NET SDK

* Intellisense: è stata aggiunta documentazione di Intellisense mancante per molti tipi.

* Microsoft.Practices.TransientFaultHandling.Core: è stato risolto un problema quando per il modulo SDK era ancora presente una dipendenza da una versione precedente di questo assembly. Il modulo SDK ora fa riferimento alla versione 5.1.1209.1 dell'assembly.

Risoluzioni di problemi rilevati nell'SDK del novembre 2012:

* IAsset.Locators.Count: questo valore è ora riportato in modo corretto nelle nuove interfacce IAsset dopo l'eliminazione di tutti i localizzatori.

* IAssetFile.ContentFileSize: questo valore viene ora impostato correttamente dopo un caricamento da IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize: questa proprietà ora può essere impostata quando si crea un file di asset, mentre in precedenza era di sola lettura.

* IAssetFile.Upload(filepath): è stato risolto un problema quando il metodo di caricamento sincrono generava il seguente errore durante il caricamento di più file nell'asset. L'errore era: "Autenticazione della richiesta non riuscita nel server. Verificare che il formato dell'intestazione Authorization, firma inclusa, sia corretto".

* IAssetFile.UploadAsync: è stato risolto un problema quando non era possibile caricare più di 5 file contemporaneamente.

* IAssetFile.UploadProgressChanged: questo evento è ora fornito dal modulo SDK.

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): questo overload del metodo è ora disponibile.

* IAssetFile.DownloadAsync: è stato risolto un problema quando non era possibile scaricare più di 5 file contemporaneamente.

* IAssetFile.Delete(): è stato risolto un problema quando la chiamata al metodo delete genera un'eccezione se per IAssetFile non è stato caricato alcun file.

* Jobs: è stato risolto un problema quando il collegamento di un'attività di passaggio da MP4 a flussi digitali uniformi con un'attività di protezione PlayReady tramite un modello di processo non crea alcuna attività.

* EncryptionUtils.GetCertificateFromStore(): questo metodo non genera più un'eccezione con riferimento null a causa di errori nella ricerca del certificato in base a problemi di configurazione del certificato stesso.

## <a id="november_changes_12"></a>Versione di novembre 2012

Le modifiche citate in questa sezione sono aggiornamenti inclusi nel modulo SDK versione 2.0.0.0 di novembre 2012. Tali modifiche possono richiedere la riscrittura o la modifica di un codice scritto per la versione di anteprima di SDK di giugno 2012.

* Asset
	
	IAsset.Create(assetName) è la SOLA funzione di creazione degli asset. IAsset.Create non carica più file come parte della chiamata al metodo. Usare IAssetFile per il caricamento.
	
	Il metodo IAsset.Publish e il valore di enumerazione AssetState.Publish sono stati rimossi da Media Services SDK. Qualsiasi codice che si basa su questo valore deve essere riscritto.

* FileInfo

	Questa classe è stata rimossa e sostituita da IAssetFile.

	IAssetFiles

	IAssetFile sostituisce FileInfo e ha un comportamento diverso. Per usarla, creare un'istanza dell'oggetto IAssetFiles e caricare un file tramite Media Services SDK oppure tramite Azure Storage SDK. È possibile usare uno dei seguenti metodi di overload IAssetFile.Upload:

	* IAssetFile.Upload(filePath): metodo sincrono che blocca il thread ed è consigliato solo quando si carica un unico file.
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): metodo asincrono che rappresenta il meccanismo di caricamento preferito. 

	Bug noto: l'uso dell'elemento cancellationToken determina l'annullamento del caricamento, tuttavia lo stato di annullamento delle attività può essere uno qualsiasi di diversi stati. È necessario rilevare e gestire le eccezioni in modo appropriato.

* Localizzatori
	
	Le versioni specifiche dell'origine sono state rimosse. SAS-specific context.Locators.CreateSasLocator(asset, accessPolicy) verrà contrassegnato come deprecato o rimosso dalla fase GA. Vedere la sezione Localizzatori in Nuova funzionalità per il comportamento aggiornato.


## <a id="june_changes_12"></a>Versione di anteprima di giugno 2012

Le funzionalità riportate di seguito sono state introdotte nella versione dell'SDK rilasciata a novembre.

* Eliminazione di entità

	Gli oggetti IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey vengono attualmente eliminati a livello di oggetto, ovvero si usa IObject.Delete(), anziché richiedere un'eliminazione nella raccolta, ovvero cloudMediaContext.ObjCollection.Delete(objInstance).

* Localizzatori

	I localizzatori devono ora essere creati tramite il metodo CreateLocator e usano i valori di enumerazione LocatorType.SAS o LocatorType.OnDemandOrigin come argomento per il tipo specifico di localizzatore da creare.

	Nuove proprietà sono state aggiunte ai localizzatori per semplificare il recupero di URI usabili per il contenuto dell'utente. Questa riprogettazione dei localizzatori è stata pensata per fornire maggiore flessibilità per un'estendibilità futura a terze parti e per aumentare la semplicità di uso per applicazioni client multimediali.

* Supporto di metodi asincroni

	A tutti i metodi è stato aggiunto il supporto asincrono.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Forum di MSDN sui Servizi multimediali di Azure]: http://social.msdn.microsoft.com/forums/azure/it-it/home?forum=MediaServices
[Informazioni di riferimento sull'API REST di Servizi multimediali di Azure]: http://msdn.microsoft.com/it-it/library/azure/hh973617.aspx 
[Dettagli prezzi di Servizi multimediali]: http://azure.microsoft.com/it-it/pricing/details/media-services/
[Metadati di input]: http://msdn.microsoft.com/it-it/library/azure/dn783120.aspx
[Metadati di output]: http://msdn.microsoft.com/it-it/library/azure/dn783217.aspx
[Distribuzione di contenuto]: http://msdn.microsoft.com/it-it/library/azure/hh973618.aspx
[Indicizzazione di file multimediali con Azure Media Indexer]: http://msdn.microsoft.com/it-it/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/it-it/library/azure/dn783468.aspx
[Uso di Live Streaming di Servizi multimediali di Azure]: http://msdn.microsoft.com/it-it/library/azure/dn783466.aspx
[Uso della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi]: http://msdn.microsoft.com/it-it/library/azure/dn783457.aspx
[Uso della crittografia dinamica PlayReady e del server di distribuzione di licenze PlayReady]: http://msdn.microsoft.com/it-it/library/azure/dn783467.aspx
[Funzionalità di anteprima]: http://azure.microsoft.com/it-it/services/preview/
[Panoramica del modello di licenza PlayReady di Servizi multimediali]: http://msdn.microsoft.com/it-it/library/azure/dn783459.aspx
[Streaming di contenuto crittografato di archiviazione]: http://msdn.microsoft.com/it-it/library/azure/dn783451.aspx
[Portale di gestione di Azure]: https://manage.windowsazure.com
[Creazione dinamica dei pacchetti]: http://msdn.microsoft.com/it-it/library/azure/jj889436.aspx
[Blog di Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protezione di Smooth Streaming e MPEG DASH con PlayReady]: http://msdn.microsoft.com/it-it/library/azure/dn189154.aspx
[Logica di retry in Media Services SDK per .NET]: http://msdn.microsoft.com/it-it/library/azure/dn745650.aspx
[Annunci di Grass Valley sullo streaming EDIUS 7 tramite cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Controllo dei nomi file di output del codificatore di Servizi multimediali]: http://msdn.microsoft.com/it-it/library/azure/dn303341.aspx
[Creazione di sovrimpressioni]: http://msdn.microsoft.com/it-it/library/azure/dn640496.aspx
[Unione dei segmenti video]: http://msdn.microsoft.com/it-it/library/azure/dn640504.aspx
[Azure Media Services .NET SDK versioni 3.0.0.1 e 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Servizio di controllo di accesso (ACS) di Azure Active Directory]: http://msdn.microsoft.com/it-it/library/hh147631.aspx
[Connessione a Servizi multimediali con Media Services SDK per .NET]: http://msdn.microsoft.com/it-it/library/azure/jj129571.aspx
[Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[Github]: https://github.com/Azure/azure-sdk-for-media-services
[Gestione di asset di Servizi multimediali su più account di archiviazione]: http://msdn.microsoft.com/it-it/library/azure/dn271889.aspx
[Gestione delle notifiche dei processi di Media Services]: http://msdn.microsoft.com/it-it/library/azure/dn261241.aspx


<!--HONumber=42-->
