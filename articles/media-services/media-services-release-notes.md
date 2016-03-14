<properties 
	pageTitle="Note sulla versione di Servizi multimediali" 
	description="Note sulla versione di Servizi multimediali" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="media" 
	ms.devlang="dotnet" 
	ms.topic="article" 
 	ms.date="03/01/2016"
	ms.author="juliako"/>


# Note sulla versione di Servizi multimediali di Azure

Nelle presenti note sulla versione vengono riepilogati le modifiche rispetto alle versioni precedenti e i problemi noti.

>[AZURE.NOTE] Microsoft invita i clienti a segnalare i problemi che si verificano e si impegna a risolverli. Per segnalare un problema o rivolgere domande, pubblicare un post nel [forum di MSDN sui Servizi multimediali di Azure].

- [Problemi noti correnti](#issues)
- [Cronologia delle versioni dell'API REST](#rest_version_history)
- [Versione di febbraio 2016](#feb_changes16)
- [Versione di gennaio 2016](#jan_changes_16)
- [Versione di dicembre 2015](#dec_changes_15)
- [Versione di novembre 2015](#nov_changes_15)
- [Versione ottobre 2015](#oct_changes_15)
- [Versione di settembre 2015](#september_changes_15)
- [Versione di agosto 2015](#august_changes_15)
- [Versione di luglio 2015](#july_changes_15)
- [Versione di giugno 2015](#june_changes_15)
- [Versione di maggio 2015](#may_changes_15)
- [Versione di aprile 2015](#april_changes_15)
- [Versione di marzo 2015](#march_changes_15)
- [Versione di febbraio 2015](#february_changes_15)
- [Versione di gennaio 2015](#january_changes_15)
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


##<a id="issues"></a>Problemi noti correnti

### <a id="general_issues"></a>Problemi generali di Servizi multimediali

Problema|Descrizione
---|---
Nell'API REST non sono fornite alcune intestazioni HTTP comuni.|Se si sviluppano applicazioni di Servizi multimediali tramite l'API REST, alcuni campi di intestazione HTTP comuni, ad esempio CLIENT-REQUEST-ID, REQUEST-ID e RETURN-CLIENT-REQUEST-ID, non sono supportati. Le intestazioni verranno aggiunte in un futuro aggiornamento.
La codifica di un asset con un nome di file contenente caratteri di escape, (ad esempio %20), genera un errore simile al seguente "MediaProcessor: File non trovato.”|I nomi di file da aggiungere a un asset e da codificare devono contenere solo caratteri alfanumerici e spazi. Il problema verrà risolto in un futuro aggiornamento.
Il metodo ListBlobs di Azure Storage SDK versione 3.x non riesce.|Servizi multimediali genera URL di firma di accesso condiviso basati sulla versione [2012-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx). Se si vuol usare Azure Storage SDK per elencare oggetti BLOB in un contenitore dello stesso tipo, usare il metodo [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) disponibile in Azure Storage SDK versione 2.x. Il metodo ListBlobs disponibile in Azure Storage SDK versione 3.x non riuscirà.
Il meccanismo di limitazione delle richieste di Servizi multimediali limita l'uso delle risorse per le applicazioni che inviano un numero elevato di richieste al servizio. Il servizio può restituire il codice di stato HTTP di servizio non disponibile (503).|Per altre informazioni, vedere la descrizione del codice di stato HTTP 503 nell'argomento [Codici di errore di Servizi multimediali di Azure](http://msdn.microsoft.com/library/azure/dn168949.aspx).
Quando si esegue una query di entità, è previsto un limite di 1000 entità restituite in una sola volta perché la versione 2 pubblica di REST limita i risultati della query a 1000 risultati. | È necessario usare **Skip** e **Take** (.NET)/**top** (REST) come descritto in [questo esempio .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [in questo esempio di API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 


### <a id="dotnet_issues"></a>Problemi relativi all'SDK di Servizi multimediali per .NET

Problema|Descrizione
---|---
Gli oggetti di Servizi multimediali nel modulo SDK non possono essere serializzati e di conseguenza non funzionano con Caching di Azure.|Se si prova a serializzare l'oggetto AssetCollection del modulo SDK per aggiungerlo a Caching di Azure, viene generata un'eccezione.

##<a id="rest_version_history"></a>Cronologia delle versioni dell'API REST

Per informazioni sulla cronologia versioni dell'API REST di Servizi multimediali, vedere [Informazioni di riferimento sull'API REST di Servizi multimediali di Azure].

##<a id="feb_changes16"></a>Versione di febbraio 2016

La versione più recente di Azure Media Services SDK per .NET (3.5.3) contiene una correzione di bug relativa a Widevine. Problema: non è possibile usare di nuovo AssetDeliveryPolicy per più asset crittografati con Widevine. All'interno di questa correzione di bug, è stato aggiunto all'SDK la proprietà seguente: **WidevineBaseLicenseAcquisitionUrl**.
	
	Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
	    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
	{
	    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
	    
	};

##<a id="jan_changes_16"></a>Versione di gennaio 2016

Unità riservate di codifica rinominate per ridurre la confusione con i nomi del codificatore.

Le unità riservate di codifica Basic, Standard e Premium sono state rinominate rispettivamente unità riservate S1, S2 e S3. I clienti che usano unità riservate con codifica Basic visualizzeranno ora l'etichetta S1 nel portale di Azure e nella fattura, mentre i clienti con codifica Standard e Premium visualizzeranno rispettivamente le etichette S2 e S3.

##<a id="dec_changes_15"></a>Versione di dicembre 2015

Il team di Azure SDK ha pubblicato una nuova versione del pacchetto [Azure SDK per PHP](http://github.com/Azure/azure-sdk-for-php) che contiene aggiornamenti e nuove funzionalità di Servizi multimediali di Microsoft Azure. In particolare, Azure Media Services SDK per PHP supporta ora le funzionalità di [protezione del contenuto](media-services-content-protection-overview.md) più recenti: la crittografia dinamica con AES e DRM (PlayReady e Widevine) con e senza restrizione Token. Supporta inoltre la scalabilità delle [unità di codifica](media-services-dotnet-encoding-units.md).

Per altre informazioni, vedere:

- Il blog di [Microsoft Azure Media Services SDK per PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/).
- Gli [esempi di codice](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) seguenti per iniziare rapidamente a svolgere le attività preliminari:
	- **vodworkflow\_aes.php**: si tratta di un file PHP che illustra come usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi. È basato sull'esempio .NET illustrato in [questo](media-services-protect-with-aes128.md) articolo.
	- **vodworkflow\_aes.php**: si tratta di un file PHP che illustra come usare la crittografia dinamica PlayReady e il servizio di distribuzione delle licenze. È basato sull'esempio .NET illustrato in [questo](media-services-protect-with-drm.md) articolo.
	- **scale\_encoding\_units.php**: si tratta di un file PHP che illustra come scalare unità riservate di codifica.


##<a id="nov_changes_15"></a>Versione di novembre 2015

Servizi multimediali di Azure offre ora il servizio di distribuzione della licenza Google Widevine nel cloud. Per altri dettagli, consultare [questo annuncio di blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Vedere anche [questa esercitazione](media-services-protect-with-drm.md) e il [repository GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

Si noti che i servizi di distribuzione delle licenze Widevine forniti da Servizi multimediali di Azure sono in anteprima. Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Versione di ottobre 2015

Servizi multimediali di Azure (AMS) è ora disponibile anche nei data center seguenti: Brasile meridionale, India occidentale, India meridionale e India centrale. È ora possibile usare il portale di Azure classico per [creare account di Servizi multimediali](media-services-create-account.md#create-a-media-services-account-using-quick-create) ed eseguire diverse attività descritte [qui](https://azure.microsoft.com/documentation/services/media-services/). In questi data center la codifica live non è tuttavia abilitata. Inoltre, non sono disponibili tutti i tipi di unità riservate di codifica.

- Brasile meridionale: sono disponibili solo unità riservate di codifica Standard e Basic
- India occidentale, India meridionale e India centrale: sono disponibili solo unità riservate di codifica Basic


##<a id="september_changes_15"></a>Versione di settembre 2015 

- AMS offre ora la possibilità di proteggere le video on Demand (VOD) e i flussi Live con la tecnologia DRM modulare Widevine. È possibile usare i partner dei servizi di recapito seguenti per consentire la distribuzione di licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/). Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

	È possibile usare [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partire dalla versione 3.5.1) o l'API REST per configurare AssetDeliveryConfiguration per l'uso di Widevine.

- AMS ha aggiunto il supporto per i video Apple ProRes. È ora possibile caricare i file video di origine QuickTime che utilizzano Apple ProRes o altri codec. Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- È ora possibile utilizzare il Codificatore multimediale standard per eseguire l'estrazione di archivio secondario di ritaglio e live. Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- Sono stati apportati i seguenti aggiornamenti per il filtro.

	- È ora possibile utilizzare il formato Apple HTTP Live Streaming (HLS) con il filtro solo audio. Questo aggiornamento consente di rimuovere solo la traccia audio specificando (solo audio = false) nell'URL.
	- Quando si definiscono i filtri per gli asset, ora è possibile combinare più filtri (fino a 3) in un singolo URL.

	Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

- AMS supporta ora gli I-Frames in HLS v4. Il supporto I-Frame consente di ottimizzare le operazioni di avanzamento veloce e riavvolgimento. Per impostazione predefinita, tutti gli output v4 HLS includono le playlist I-Frame (EXT-X-I-FRAME-STREAM-INF).
 
	Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

##<a id="august_changes_15"></a>Versione di agosto 2015

- La release Azure Media Services SDK per Java V0.8.0 e nuovi esempi sono ora disponibili. Per altre informazioni, vedere:

	- [Post di blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
	- [Repository di esempi Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Aggiornamento di Azure Media Player con supporto per flussi multi-audio. Per altre informazioni, vedere:
	- [Post di blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Versione di luglio 2015

- È stata annunciata la disponibilità generale del codificatore multimediale standard. Per altre informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

	Il codificatore multimediale standard usa i set di impostazioni descritti in [questa](http://go.microsoft.com/fwlink/?LinkId=618336) sezione. Si noti che, quando si usa un set di impostazioni per 4.000 codifiche, è consigliabile acquistare tipo di unità riservata **Premium**. Per altre informazioni, vedere [Come scalare la codifica](media-services-portal-encoding-units).
- Didascalie live in tempo reale con servizi multimediali e Player di Azure. Per altre informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

###Aggiornamenti dell'SDK di Servizi multimediali per .NET

Azure Media Services .NET SDK è ora disponibile nella versione 3.4.0.0. In questa versione è stata aggiunta la seguente funzionalità:

- Implementazione del supporto per l'archivio live. Si noti che non è possibile scaricare un asset che contiene un archivio live.
- Implementazione del supporto per i filtri dinamici.
- Implementazione della funzionalità che consente agli utenti di conservare il contenitore di archiviazione durante l'eliminazione dell'asset.
- Correzioni di bug relativi a criteri di ripetizione nei canali.
- Abilitazione del **flusso di lavoro Premium del codificatore multimediale**.

##<a id="june_changes_15"></a>Versione di giugno 2015

###Aggiornamenti dell'SDK di Servizi multimediali per .NET

L'SDK di Servizi multimediali di Azure per .NET è ora disponibile nella versione 3.3.0.0. In questa versione è stata aggiunta la seguente funzionalità:

- supporto per la specifica di individuazione OpenId Connect
- supporto per la gestione del rollover delle chiavi sul lato del provider di identità. 

Se si usa un provider di identità che espone il documento di individuazione OpenID Connect (come i seguenti provider: Azure Active Directory, Google, Salesforce), è possibile fare in modo che Servizi multimediali di Azure ottenga le chiavi di firma per la convalida dei token JWT dalla specifica di individuazione OpenID Connect.

Per altre informazioni, vedere l’argomento relativo all’[uso delle chiavi Web Json dalla specifica di individuazione OpenID Connect per l’utilizzo con l’autenticazione del token JWT in Servizi multimediali di Azure](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Versione di maggio 2015

Annuncio delle nuove funzionalità seguenti:

- [Anteprima della codifica live con Servizi multimediali](media-services-manage-live-encoder-enabled-channels.md)
- [Manifesto dinamico](media-services-dynamic-manifest-overview.md)
- [Anteprima del processore di contenuti multimediali Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Versione di aprile 2015

###Aggiornamenti generali di Servizi multimediali

- [Annuncio di Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- A partire da Servizi multimediali REST 2.10, i canali configurati per l'inserimento di un protocollo RTMP vengono creati con URL di inserimento primari e secondari. Per altre informazioni, vedere [Configurazioni di inserimento del canale](media-services-manage-channels-overview.md#channel_input)
- Aggiornamenti di Azure Media Indexer
	- Supporto per la lingua spagnola
	- Nuovo formato xml di configurazione
	
	Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###Aggiornamenti dell'SDK di Servizi multimediali per .NET

L'SDK di Servizi multimediali per .NET è ora disponibile nella versione 3.2.0.0.

Di seguito sono riportati alcuni degli aggiornamenti utilizzati dal cliente:
 
- **Modifica di rilievo**: modificati **TokenRestrictionTemplate.Issuer** e **TokenRestrictionTemplate.Audience** in modo che siano di tipo string. 
- Aggiornamenti relativi alla creazione di criteri di ripetizione personalizzati. 
- Correzioni di bug correlati al caricamento/download di file. 
- La classe **MediaServicesCredentials** accetta ora endpoint di controllo di accesso primari e secondari per l'autenticazione.



##<a id="march_changes_15"></a>Versione di marzo 2015

### Aggiornamenti generali di Servizi multimediali

- Servizi multimediali offre ora l'integrazione con la rete CDN di Azure. Per supportare l'integrazione, la proprietà **CdnEnabled** è stata aggiunta a **StreamingEndpoint**. La proprietà **CdnEnabled** può essere usata con le API REST a partire dalla versione 2.9 (per altre informazioni, vedere [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)). La proprietà **CdnEnabled** può essere inoltre usata con SDK .NET a partire dalla versione 3.1.0.2 (per altre informazioni, vedere [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Annuncio del **flusso di lavoro Premium del codificatore multimediale**. Per altre informazioni, vedere [Introduzione alla codifica Premium in Servizi multimediali di Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Versione di febbraio 2015

### Aggiornamenti generali di Servizi multimediali

È ora disponibile la versione 2.9 dell'API REST di Servizi multimediali. A partire da questa versione, è possibile abilitare l'integrazione della rete CDN di Azure con endpoint di streaming. Per altre informazioni, vedere [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Versione di gennaio 2015

### Aggiornamenti generali di Servizi multimediali

Annuncio della versione GA (General Availability) per la protezione dei contenuti con la crittografia dinamica. Per altre informazioni, vedere la pagina relativa a [Servizi multimediali di Azure per il miglioramento della sicurezza di flusso con la versione GA della tecnologia DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###Aggiornamenti dell'SDK di Servizi multimediali per .NET

L'SDK di Servizi multimediali di Azure per .NET è ora disponibile nella versione 3.1.0.1.

In questa versione il costruttore Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate è contrassegnato come obsoleto. Il nuovo costruttore accetta TokenType come argomento.

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Versione di dicembre 2014

###Aggiornamenti generali di Servizi multimediali

- Sono stati aggiunti alcuni aggiornamenti e nuove funzionalità al processore multimediale di indicizzazione di Azure. Per altre informazioni, vedere le [note sulla versione di Azure Media Indexer versione 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- È stata aggiunta una nuova API REST che consente di aggiornare le unità riservate di codifica: [EncodingReservedUnitType con REST](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- È stato aggiunto il supporto CORS per il servizio di distribuzione delle chiavi.
- Sono stati apportati miglioramenti alle prestazioni delle opzioni per i criteri di autorizzazioni delle query.
- Nel data center cinese l'[URL di distribuzione delle chiavi](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) viene ora fornito per ogni cliente, come in tutti gli altri data center.
- È stata aggiunta una durata con destinazione automatica HLS. Quando si esegue lo streaming live, la creazione di pacchetti in HLS avviene sempre in modo dinamico. Per impostazione predefinita, Servizi multimediali calcola automaticamente il rapporto per la creazione di pacchetti dei segmenti HLS (FragmentsPerSegment) in base all'intervallo tra fotogrammi chiave (KeyFrameInterval), noto anche come Group of Pictures (GOP), ricevuti dal codificatore Live. Per altre informazioni, vedere [Uso di Live Streaming di Servizi multimediali di Azure].
 
###Aggiornamenti dell'SDK di Servizi multimediali per .NET

- L'[SDK di Servizi multimediali di Azure per .NET](http://www.nuget.org/packages/windowsazure.mediaservices/) è ora disponibile nella versione 3.1.0.0.
- La dipendenza dell'SDK per .NET è stata aggiornata a .NET Framework 4.5.
- È stata aggiunta una nuova API che consente di aggiornare le unità riservate di codifica. Per altre informazioni, vedere [Aggiornamento del tipo di unità riservata e aumento delle unità riservate di codifica](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- È stato aggiunto il supporto JWT (JSON Web Token) per l'autenticazione dei token. Per altre informazioni, vedere l'articolo relativo all'[autenticazione di token JWT in Servizi multimediali di Azure e alla crittografia dinamica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Nel modello di licenza PlayReady sono stati aggiunti offset relativi per BeginDate e ExpirationDate.


##<a id="november_changes_14"></a>Versione di novembre 2014

- Servizi multimediali ora consente di inserire contenuti live di Smooth Streaming (FMP4) tramite una connessione SSL. Per l'inserimento tramite SSL, assicurarsi di aggiornare l'URL di inserimento a HTTPS. Per altre informazioni sullo streaming live, vedere [Uso di Live Streaming di Servizi multimediali di Azure].
- Attualmente non è possibile inserire lo streaming live RTMP tramite una connessione SSL.
- Lo streaming dei contenuti può essere eseguito anche tramite una connessione SSL. A questo scopo, verificare che gli URL di streaming inizino con HTTPS.
- Lo streaming tramite SSL è possibile solo se l'endpoint di streaming da cui si inviano i contenuti è stato creato dopo il 10 settembre 2014. Se gli URL di streaming si basano sugli endpoint di streaming creati dopo il 10 settembre, l'URL contiene "streaming.mediaservices.windows.net" (il nuovo formato). Gli URL di streaming contenenti "origin.mediaservices.windows.net" (il vecchio formato) non supportano SSL. Se l'URL è nel vecchio formato e si desidera poter eseguire lo streaming tramite SSL, [creare un nuovo endpoint di streaming](media-services-manage-origins.md). Usare gli URL creati con il nuovo endpoint di streaming per lo streaming dei contenuti tramite SSL.
   
##<a id="october_changes_14"></a>Versione di ottobre 2014

### <a id="new_encoder_release"></a>Versione del Codificatore di Servizi multimediali

È stata annunciata una nuova versione di Azure Media Encoder di Servizi Multimediali. Con la versione più recente di Azure Media Encoder, all'utente vengono addebitati solo i GB di output, ma per il resto il nuovo codificatore è una funzionalità compatibile con il codificatore precedente. Per altre informazioni, vedere [Dettagli prezzi dei servizi multimediali].

### <a id="oct_sdk"></a>SDK di Servizi multimediali per .NET 

Le estensioni dell'SDK di Servizi multimediali per .NET sono ora disponibili nella versione 2.0.0.3.

L'SDK di Servizi multimediali per .NET è ora disponibile nella versione 3.0.0.8.

Sono state introdotte le seguenti modifiche:

- Refactoring nelle classi di criteri per l'esecuzione di nuovi tentativi.
- Aggiunta della stringa agente utente alle intestazioni delle richieste HTTP.
- Aggiunta del passaggio di compilazione nuget.
- Correzione dei test di scenario per usare il certificato x509 dal repository.
- Convalida delle impostazioni durante l'aggiornamento di un endpoint di canale e di streaming.
 

### Nuovo repository GitHub per l'hosting di esempi di Servizi multimediali

Gli esempi si trovano nel [repository GitHub degli esempi di Servizi multimediali di Azure](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Versione di settembre 2014

È ora disponibile la versione 2.7 dei metadati di REST di Servizi multimediali. Per altre informazioni sugli ultimi aggiornamenti di REST, vedere [Informazioni di riferimento sull'API REST di Servizi multimediali di Azure].

L'SDK di Servizi multimediali per .NET è ora disponibile nella versione 3.0.0.7.
 
### <a id="sept_14_breaking_changes"></a>Modifiche di rilievo

* L'entità **Origin** è stata rinominata in [StreamingEndpoint].
* È stato modificato il comportamento predefinito quando si usa il **portale di Azure classico** per codificare e pubblicare file MP4.

Mentre, in precedenza, quando si usava il portale di Azure classico per pubblicare un asset video costituito da un unico file MP4, veniva creato un URL della firma di accesso condiviso (che consentiva di scaricare il video da un'archiviazione BLOB), adesso quando si utilizza il portale di Azure classico per codificare e pubblicare un asset video costituito da un unico file MP4 viene generato un URL che punta a un endpoint di flusso di origine di Servizi multimediali di Azure. Questa modifica, tuttavia, non riguarda i video MP4 che vengono caricati direttamente in Servizi multimediali e pubblicati senza essere codificati da Servizi multimediali di Azure.

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
	
	* È necessario creare un altro oggetto CName che mappa il nome host personalizzato (ad esempio, sports.contoso.com) al nome host StreamingEndpont di Servizi multimediali (ad esempio, amstest.streaming.mediaservices.windows.net).


	Per altre informazioni, vedere la proprietà **CustomHostNames** nell'argomento [StreamingEndpoint].

### <a id="sept_14_preview_changes"></a>Nuove funzionalità o scenari nella versione di anteprima pubblica

* Live Streaming Preview. Per altre informazioni, vedere [Uso di Live Streaming di Servizi multimediali di Azure].

* Servizio di distribuzione di chiavi. Per altre informazioni, vedere [Uso della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi].

* Crittografia dinamica AES. Per altre informazioni, vedere [Uso della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi].

* Servizio di distribuzione di licenze per PlayReady. Per altre informazioni, vedere [Uso della crittografia dinamica PlayReady e del server di distribuzione di licenze PlayReady].

* Crittografia dinamica PlayReady. Per altre informazioni, vedere [Uso della crittografia dinamica PlayReady e del server di distribuzione di licenze PlayReady].

* Modello di licenza PlayReady di Servizi multimediali. Per altre informazioni, vedere [Panoramica del modello di licenza PlayReady di Servizi multimediali].

* Trasmissione in flusso di asset di archiviazione crittografati. Per altre informazioni, vedere [Streaming di contenuto crittografato di archiviazione].

##<a id="august_changes_14"></a>Versione di agosto 2014

Quando si esegue la codifica di un asset, al termine del processo viene restituito un asset di output. Fino a questa versione, il codificatore di Servizi multimediali di Azure ha restituito metadati relativi ad asset di output. A partire da questa versione, il codificatore restituisce anche metadati relativi ad asset di input. Per altre informazioni, vedere gli argomenti [Metadati di input] e [Metadati di output].


##<a id="july_changes_14"></a>Versione di luglio 2014

Le seguenti correzioni di bug sono state introdotte per lo strumento per la creazione di pacchetti e il componente di crittografia di Servizi multimediali di Azure:

* Solo l'audio viene riprodotto quando si applica il formato TRANSMUX a un asset di archivio in tempo reale in HTTP Live Streaming. Questo problema è stato risolto e attualmente vengono riprodotti sia l'audio che il video.

* Quando si creano pacchetti di un asset per HTTP Live Streaming e si definisce una crittografia della busta AES a 128 bit, i flussi in pacchetti non vengono riprodotti in dispositivi Android. Questo bug è stato risolto e i flussi in pacchetti vengono riprodotti in dispositivi Android che supportano HTTP Live Streaming.

##<a id="may_changes_14"></a>Versione di maggio 2014

### <a id="may_14_changes"></a>Aggiornamenti generali di Servizi multimediali

È ora possibile usare la funzionalità [Dynamic Packaging] per riprodurre in streaming elementi di HTTP Live Streaming (HLS) v3. A tale scopo, aggiungere il seguente formato al percorso del localizzatore di origine: * .ism/manifest(format=m3u8-aapl-v3). Per altre informazioni, vedere il [blog di Nick Drouin].

La funzionalità Dynamic Packaging supporta attualmente la distribuzione di HTTP Live Streaming (v3 e v4) con crittografia PlayReady basata sulla funzionalità Smooth Streaming crittografata staticamente con PlayReady. Per informazioni su come crittografare Smooth Streaming con PlayReady, vedere l'argomento [Protezione di Smooth Streaming e MPEG DASH con PlayReady].

### <a name="may_14_donnet_changes"></a>Aggiornamenti dell'SDK di Servizi multimediali per .NET

Nell'SDK di Servizi multimediali per .NET versione 3.0.0.5 sono stati introdotti i seguenti aggiornamenti:

* Velocità e resilienza maggiori per il caricamento e il download di asset di file multimediali.

* Miglioramenti nella gestione della logica di retry e delle eccezioni temporanee:

	* Il rilevamento degli errori temporanei e la gestione della logica di retry sono stati migliorati per le eccezioni provocate dall'esecuzione di query, dal salvataggio delle modifiche, dal caricamento o dal download di file. 
	
	* Quando vengono generate eccezioni Web (ad esempio durante una richiesta di token ACS), si nota che ora gli errori irreversibili vengono gestiti più rapidamente.

Per altre informazioni, vedere [Logica di retry in Media Services SDK for .NET].

##<a id="april_changes_14"></a>Versione del codificatore di aprile 2014

### <a name="april_14_enocer_changes"></a>Aggiornamenti del Codificatore di Servizi multimediali

* Supporto aggiunto per gestire i file AVI creati tramite l'editor non lineare Grass Valley EDIUS, in cui il video viene leggermente compresso tramite codec Grass Valley HQ/HQX. Per altre informazioni, vedere la pagina relativa agli [annunci di Grass Valley sulla trasmissione in flusso EDIUS 7 nel cloud].

* È stato aggiunto supporto per specificare la convenzione di denominazione per i file creati da Media Encoder. Per altre informazioni, vedere [Controllo dei nomi file di output del codificatore di Servizi multimediali].

* Supporto aggiunto per sovrimpressioni video e/o audio. Per altre informazioni, vedere [Creazione di sovrimpressioni].

* Supporto aggiunto per collegare tra loro più segmenti video. Per altre informazioni, vedere [Unione dei segmenti video].

* Bug correlato alla transcodifica di file MP4 in cui l'audio è stato codificato con MPEG-1 Audio layer 3 (noto anche come formato MP3) risolto.


##<a id="jan_feb_changes_14"></a>Versioni di gennaio/febbraio 2014

### <a name="jan_fab_14_donnet_changes"></a>SDK di Servizi multimediali di Azure per .NET versioni 3.0.0.1, 3.0.0.2 e 3.0.0.3

Le modifiche apportate alle versioni 3.0.0.1 e 3.0.0.2 includono:

* Risoluzione dei problemi correlati all'uso di query LINQ con istruzioni OrderBy.

* Soluzioni di test in [GitHub] divise in test basati su unità e test basati su scenario.

Per altre informazioni su queste modifiche, vedere: [SDK di Servizi multimediali di Azure per .NET versioni 3.0.0.1 e 3.0.0.2].

Nella versione 3.0.0.3 sono state introdotte le seguenti modifiche:

* Dipendenze dell'archiviazione di Azure aggiornate per usare la versione 3.0.3.0. 

* Problema relativo alla compatibilità con le versioni precedenti per le versioni 3.0.*.* risolto.


##<a id="december_changes_13"></a>Versione di dicembre 2013

### <a name="dec_13_donnet_changes"></a>SDK di Servizi multimediali di Azure per .NET versione 3.0.0.0

>[AZURE.NOTE] Le versioni 3.0.x.x non sono compatibili con le versioni precedenti 2.4.x.x.

L'SDK di Servizi multimediali è ora disponibile nella versione 3.0.0.0. È possibile scaricare il pacchetto più recente dal sito Web di NuGet oppure ottenere i bit da [GitHub].

A partire da Media Services SDK versione 3.0.0.0, è possibile usare nuovamente i token di [Azure Active Directory Access Control (ACS)]. Per altre informazioni, vedere la sezione "Riutilizzo di token del Servizio di controllo di accesso" nell'argomento [Connessione a Servizi multimediali con Media Services SDK per .NET].

### <a name="dec_13_donnet_ext_changes"></a>Estensioni dell'SDK di Servizi multimediali di Azure per .NET versione 2.0.0.0

Le estensioni dell'SDK di Servizi multimediali di Azure per .NET sono un set di metodi di estensione e funzioni di supporto che semplificano il codice e lo sviluppo con Servizi multimediali di Azure. È possibile ottenere i bit più recenti sul sito Web relativo a [Azure Media Services .NET SDK Extensions].

##<a id="november_changes_13"></a>Versione di novembre 2013

### <a name="nov_13_donnet_changes"></a>Modifiche apportate all'SDK di Servizi multimediali di Azure per .NET

A partire da questa versione, l'SDK di Servizi multimediali per .NET gestisce gli errori temporanei che possono verificarsi quando si effettuano chiamate a livello API REST di Servizi multimediali.

##<a id="august_changes_13"></a>Versione di agosto 2013

### <a name="aug_13_powershell_changes"></a>Cmdlet di PowerShell per Servizi multimediali inclusi negli strumenti SDK di Azure

I seguenti cmdlet di PowerShell per Servizi multimediali sono stati inclusi in [azure-sdk-tools].

* Get-AzureMediaServices 

	ad esempio `Get-AzureMediaServicesAccount`.

* New-AzureMediaServicesAccount

	ad esempio `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* New-AzureMediaServicesKey

	ad esempio `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount

	ad esempio `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Versione di giugno 2013

### <a name="june_13_general_changes"></a>Modifiche di Servizi multimediali di Azure

Le modifiche citate in questa sezione sono aggiornamenti inclusi nelle versioni di Servizi multimediali di giugno 2013.

* Possibilità di collegare più account di archiviazione a un account di Servizi multimediali. 

	StorageAccount
	
	Asset.StorageAccountName e Asset.StorageAccount

* Possibilità di aggiornare Job.Priority.

* Entità e proprietà correlate alle notifiche:

	JobNotificationSubscription
	
	NotificationEndPoint
	
	Job

* Asset.Uri

* Locator.Name

### <a name="june_13_dotnet_changes"></a>Modifiche apportate all'SDK di Servizi multimediali di Azure per .NET

Le seguenti modifiche sono state apportate alle versioni dell'SDK di Servizi multimediali di giugno 2013. Il modulo Media Services SDK più recente è disponibile in GitHub.

* A partire dalla versione 2.3.0.0, Media Services SDK consente il collegamento di più account di archiviazione a un unico account di Servizi multimediali. Questa funzionalità è supportata dalle seguenti API:
	
	Tipo IStorageAccount.
	
	Proprietà Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
	
	Proprietà StorageAccount.
	
	Proprietà StorageAccountName.
	
	Per altre informazioni, vedere [Gestione di asset di Servizi multimediali su più account di archiviazione].

* API correlate alle notifiche. A partire dalla versione 2.2.0.0 è possibile ascoltare le notifiche dal servizio di archiviazione delle code di Azure. Per altre informazioni vedere [Gestione delle notifiche dei processi di Media Services].
	
	Proprietà Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
	
	Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dipendenza da Azure Storage Client SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dipendenza da OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Versione di dicembre 2012

### <a name="dec_12_dotnet_changes"></a>Modifiche apportate all'SDK di Servizi multimediali di Azure per .NET

* Intellisense: è stata aggiunta documentazione di Intellisense mancante per molti tipi.

* Microsoft.Practices.TransientFaultHandling.Core: è stato risolto un problema quando per l'SDK era ancora presente una dipendenza da una versione precedente di questo assembly. L'SDK ora fa riferimento alla versione 5.1.1209.1 dell'assembly.

Risoluzioni di problemi rilevati nell'SDK del novembre 2012:

* IAsset.Locators.Count: questo valore è ora riportato in modo corretto nelle nuove interfacce IAsset dopo l'eliminazione di tutti i localizzatori.

* IAssetFile.ContentFileSize: questo valore viene ora impostato correttamente dopo un caricamento da IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize: questa proprietà ora può essere impostata quando si crea un file di asset, mentre in precedenza era di sola lettura.

* IAssetFile.Upload(filepath): è stato risolto un problema quando il metodo di caricamento sincrono generava il seguente errore durante il caricamento di più file nell'asset. L'errore era: "Autenticazione della richiesta non riuscita nel server. Verificare che il formato dell'intestazione Authorization, firma inclusa, sia corretto".

* IAssetFile.UploadAsync: è stato risolto un problema quando non era possibile caricare più di 5 file contemporaneamente.

* IAssetFile.UploadProgressChanged: questo evento è ora fornito dall’SDK.

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): questo overload del metodo è ora disponibile.

* IAssetFile.DownloadAsync: è stato risolto un problema quando non era possibile scaricare più di 5 file contemporaneamente.

* IAssetFile.Delete(): è stato risolto un problema quando la chiamata al metodo delete genera un'eccezione se per IAssetFile non è stato caricato alcun file.

* Jobs: è stato risolto un problema quando il concatenamento di un'attività di passaggio da MP4 a flussi digitali uniformi con un'attività di protezione PlayReady tramite un modello di processo non crea alcuna attività.

* EncryptionUtils.GetCertificateFromStore(): questo metodo non genera più un'eccezione con riferimento null a causa di errori nella ricerca del certificato in base a problemi di configurazione del certificato stesso.

##<a id="november_changes_12"></a>Versione di novembre 2012

Le modifiche citate in questa sezione sono aggiornamenti inclusi nell'SDK versione 2.0.0.0 di novembre 2012. Tali modifiche possono richiedere la riscrittura o la modifica di un codice scritto per la versione di anteprima di SDK di giugno 2012.

* Asset
	
	IAsset.Create(assetName) è la SOLA funzione di creazione degli asset. IAsset.Create non carica più file come parte della chiamata al metodo. Usare IAssetFile per il caricamento.
	
	Il metodo IAsset.Publish e il valore di enumerazione AssetState.Publish sono stati rimossi da Media Services SDK. Qualsiasi codice che si basa su questo valore deve essere riscritto.

* FileInfo

	Questa classe è stata rimossa e sostituita da IAssetFile.

	IAssetFiles

	IAssetFile sostituisce FileInfo e ha un comportamento diverso. Per usarla, creare un'istanza dell'oggetto IAssetFiles e caricare un file tramite Media Services SDK oppure tramite Azure Storage SDK. È possibile usare uno dei seguenti metodi di overload IAssetFile.Upload:

	* IAssetFile.Upload(filePath): metodo sincrono che blocca il thread ed è consigliato solo quando si carica un unico file.
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): un metodo asincrono. che rappresenta il meccanismo di caricamento preferito.

	Bug noto: l'uso dell'elemento cancellationToken determina l'annullamento del caricamento, tuttavia lo stato di annullamento delle attività può essere uno qualsiasi di diversi stati. È necessario rilevare e gestire le eccezioni in modo appropriato.

* Localizzatori
	
	Le versioni specifiche dell'origine sono state rimosse. SAS-specific context.Locators.CreateSasLocator(asset, accessPolicy) verrà contrassegnato come deprecato o rimosso dalla fase GA. Vedere la sezione Localizzatori in Nuova funzionalità per il comportamento aggiornato.


##<a id="june_changes_12"></a>Versione di anteprima di giugno 2012

Le funzionalità riportate di seguito sono state introdotte nella versione dell'SDK rilasciata a novembre.

* Eliminazione di entità

	Gli oggetti IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey vengono attualmente eliminati a livello di oggetto, ovvero si usa IObject.Delete(), anziché richiedere un'eliminazione nella raccolta, ovvero cloudMediaContext.ObjCollection.Delete(objInstance).

* Localizzatori

	I localizzatori devono ora essere creati tramite il metodo CreateLocator e usano i valori di enumerazione LocatorType.SAS o LocatorType.OnDemandOrigin come argomento per il tipo specifico di localizzatore da creare.

	Nuove proprietà sono state aggiunte ai localizzatori per semplificare il recupero di URI usabili per il contenuto dell'utente. Questa riprogettazione dei localizzatori è stata pensata per fornire maggiore flessibilità per un'estendibilità futura a terze parti e per aumentare la semplicità di uso per applicazioni client multimediali.

* Supporto di metodi asincroni

	A tutti i metodi è stato aggiunto il supporto asincrono.


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[forum di MSDN sui Servizi multimediali di Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Informazioni di riferimento sull'API REST di Servizi multimediali di Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx
[Dettagli prezzi dei servizi multimediali]: http://azure.microsoft.com/pricing/details/media-services/
[Metadati di input]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadati di output]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Distribuzione di contenuto]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indicizzazione di file multimediali con Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Uso di Live Streaming di Servizi multimediali di Azure]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Uso della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Uso della crittografia dinamica PlayReady e del server di distribuzione di licenze PlayReady]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Panoramica del modello di licenza PlayReady di Servizi multimediali]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming di contenuto crittografato di archiviazione]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[Dynamic Packaging]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[blog di Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protezione di Smooth Streaming e MPEG DASH con PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Logica di retry in Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[annunci di Grass Valley sulla trasmissione in flusso EDIUS 7 nel cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Controllo dei nomi file di output del codificatore di Servizi multimediali]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Creazione di sovrimpressioni]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Unione dei segmenti video]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[SDK di Servizi multimediali di Azure per .NET versioni 3.0.0.1 e 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Connessione a Servizi multimediali con Media Services SDK per .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Gestione di asset di Servizi multimediali su più account di archiviazione]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Gestione delle notifiche dei processi di Media Services]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 

<!---HONumber=AcomDC_0302_2016-->