---
title: Domande frequenti su Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: a4f4bd6eaa07907dd672abe068b515b5127adac9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886824"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Domande frequenti su Media Services v3

Questo articolo contiene le risposte alle domande frequenti su Servizi multimediali di Azure (AMS) v3.

## <a name="general"></a>Generale

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quali ruoli di Azure possono eseguire azioni sulle risorse di Servizi multimediali di Azure? 

Vedere Controllo degli accessi in base al [ruolo per gli account di Servizi multimediali.](rbac-overview.md)

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Come si esegue lo streaming su dispositivi Apple iOS?

Alla fine del percorso (dopo la parte "/manifest" dell'URL) assicurarsi di avere "(format:m3u8-aapl)" (dopo la parte "/manifest" dell'URL) per indicare al server di origine dello streaming di restituire il contenuto HLS per l'utilizzo nei dispositivi nativi Apple iOS (per informazioni dettagliate, vedere distribuzione del [contenuto](dynamic-packaging-overview.md)).

### <a name="how-do-i-configure-media-reserved-units"></a>Come si esegue la configurazione di Media Reserved Units?

Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 MRU S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).

Per informazioni dettagliate, vedere [Ridimensionamento dell'elaborazione di contenuti multimediali con l'interfaccia della riga di comando](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual è il metodo consigliato per l'elaborazione dei processi?

Usare [Trasformazioni](https://docs.microsoft.com/rest/api/media/transforms) per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni **trasformazione** descrive un recipe o un flusso di lavoro di attività per l'elaborazione dei file video o audio. Un [processo](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva a Servizi multimediali di applicare la **trasformazione** a un determinato contenuto video o audio di input. Dopo aver creato la trasformazione, è possibile inviare i processi usando le API di Servizi multimediali o uno degli SDK pubblicati. Per altre informazioni, vedere [Trasformazioni e processi](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>Ho caricato, codificato e pubblicato un video. Quale può essere il motivo per cui il video non viene riprodotto quando provo a trasmetterlo in streaming?

Uno dei motivi più comuni consiste nel fatto che l'endpoint di streaming da cui si sta tentando la riproduzione non si trova nello stato In esecuzione.

### <a name="how-does-pagination-work"></a>Come funziona la paginazione?

Quando si usa la paginazione, usare sempre il collegamento seguente per enumerare la raccolta e non dipendere da una determinata dimensione di pagina. Per informazioni dettagliate ed esempi, vedere [Filtro, ordinamento, restituzione di più pagine](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quali funzionalità non sono ancora disponibili in Servizi multimediali di Azure v3?

Per informazioni dettagliate, vedere [Spazi delle funzionalità rispetto alle API v2.](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual è il processo di spostamento di un account di Servizi multimediali tra sottoscrizioni?  

Per informazioni dettagliate, vedere [Spostamento di un account di Servizi multimediali tra sottoscrizioni.](media-services-account-concept.md)

## <a name="live-streaming"></a>Streaming live 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>Come interrompere il live streaming dopo la trasmissione è fatto?

È possibile affrontarlo da un lato client o da un lato server.

#### <a name="client-side"></a>Lato client

L'applicazione Web deve chiedere all'utente se desidera terminare la trasmissione se sta chiudendo il browser. Si tratta di un evento del browser che l'applicazione Web è in grado di gestire.

#### <a name="server-side"></a>Sul lato server

È possibile monitorare gli eventi live sottoscrivendo gli eventi della griglia di eventi. Per ulteriori informazioni, vedere lo [schema dell'evento eventgrid](media-services-event-schemas.md#live-event-types).

* È possibile [sottoscrivere](reacting-to-media-services-events.md) il livello di flusso [Microsoft.Media.LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) e monitorare che non sono disponibili riconnessioni per un po ' per interrompere ed eliminare l'evento live.
* In alternativa, è possibile [sottoscrivere](reacting-to-media-services-events.md) gli eventi [heartbeat](media-services-event-schemas.md#liveeventingestheartbeat) a livello di traccia. Se tutte le tracce hanno velocità di bit in ingresso scendendo a 0; o l'ultimo timestamp non è più in aumento, quindi è anche possibile chiudere in modo sicuro l'evento live. Gli eventi heartbeat arrivano ogni 20 secondi per ogni traccia in modo che possa essere un po 'dettagliato.

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Come è possibile inserire interruzioni/video e slate immagine durante lo streaming live?

La codifica live di Servizi multimediali v3 non supporta ancora l'inserimento di slate immagine o video durante lo streaming live. 

È possibile usare un [codificatore locale live](recommended-on-premises-live-encoders.md) per commutare il video di origine. Molte app offrono la possibilità di commutare le origini, tra cui Telestream Wirecast, Switcher Studio (in iOS), OBS Studio (app gratuita) e molte altre.

## <a name="content-protection"></a>Protezione del contenuto

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usare una crittografia a chiave non crittografata AES-128 o un sistema DRM?

I clienti spesso si chiedono se devono usare la crittografia AES o un sistema DRM. La differenza principale tra i due sistemi è che con la crittografia AES la chiave simmetrica viene trasmessa al client tramite TLS in modo che la chiave venga crittografata in transito ma senza alcuna crittografia aggiuntiva ("in chiaro"). Di conseguenza, la chiave utilizzata per decrittografare il contenuto è accessibile al lettore client e può essere visualizzata in una traccia di rete sul client in testo normale. Una crittografia a chiave non crittografata AES-128 è adatta per i casi d'uso in cui il visualizzatore è una parte attendibile (ad esempio, la crittografia dei video aziendali distribuiti all'interno di un'azienda per essere visualizzati dai dipendenti).

I sistemi DRM come PlayReady, Widevine e FairPlay forniscono tutti un ulteriore livello di crittografia sulla chiave utilizzata per decrittografare il contenuto rispetto a una chiave non crittografata AES-128. La chiave simmetrica viene crittografata in una chiave protetta dal runtime DRM in aggiunta a qualsiasi crittografia a livello di trasporto fornita da TLS. Inoltre, la decrittografia viene gestita in un ambiente sicuro a livello di sistema operativo, più difficilmente attaccabile da un utente malintenzionato. Il DRM è consigliato per i casi d'uso in cui il visualizzatore potrebbe non essere attendibile ed è necessario il massimo livello di sicurezza.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Come mostrare un video solo agli utenti che dispongono di un'autorizzazione specifica, senza usare Azure AD?

Non è necessario usare alcun provider di token specifico (ad esempio Azure AD). È possibile creare il proprio provider [JWT](https://jwt.io/) (cosiddetto STS, Secure Token Service), utilizzando la crittografia a chiave asimmetrica. Nel servizio token di sicurezza personalizzato è possibile aggiungere attestazioni in base alla logica di business.

Assicurarsi che l'autorità emittente, il gruppo di destinatari e le attestazioni corrispondano esattamente tra l'oggetto JWT e ContentKeyPolicyRestriction utilizzato in ContentKeyPolicy.

Per ulteriori informazioni, consultate Proteggere il contenuto utilizzando la crittografia dinamica di [Servizi multimediali.](content-protection-overview.md)

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Come e dove si ottiene il token JWT prima di usarlo per richiedere la licenza o la chiave?

1. Per la produzione, è necessario disporre di un servizio token di sicurezza (servizio Web) (servizio Web) che emette il token JWT su una richiesta HTTPS. Per i test, è possibile usare il codice illustrato nel metodo **GetTokenAsync** definito in [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Dopo l'autenticazione di un utente, il lettore dovrà richiedere al servizio token di sicurezza tale token e assegnarlo come valore del token. È possibile usare l'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

* Per un esempio di esecuzione del servizio token di [https://aka.ms/jwt](https://aka.ms/jwt)sicurezza, con chiave simmetrica e asimmetrica, vedere . 
* Per un esempio di un lettore basato su Azure [https://aka.ms/amtest](https://aka.ms/amtest) Media Player che usa tale token JWT, vedere (espandere il collegamento "player_settings" per visualizzare l'input del token.

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Come si autorizzano le richieste di streaming di video con la crittografia AES?

L'approccio corretto consiste nell'usare il servizio token di sicurezza:

Nel servizio token di sicurezza, a seconda del profilo dell'utente, aggiungere attestazioni diverse (ad esempio "Utente Premium", "Utente Basic", "Utente di prova gratuita"). Con diverse attestazioni in un token JWT, l'utente può visualizzare diversi tipi di contenuto. Naturalmente, per contenuto/risorse diverse, ContentKeyPolicyRestriction avrà il valore RequiredClaims corrispondente.

Usare le API di Servizi multimediali di Azure per configurare il recapito di licenze/chiavi e crittografare gli asset (come illustrato in [questo esempio).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Per altre informazioni, vedere:

- [Panoramica della protezione del contenuto](content-protection-overview.md)
- [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP o HTTPS?
L'applicazione lettore MVC ASP.NET deve supportare quanto segue:

* Autenticazione utente con Azure AD, che deve usare il protocollo HTTPS.
* Scambio di token JWT tra il client e Azure AD, che deve usare il protocollo HTTPS.
* Acquisizione della licenza DRM dal client, che deve essere gestita tramite protocollo HTTPS se la distribuzione delle licenze avviene tramite Servizi multimediali. La famiglia di prodotti PlayReady non obbliga a usare il protocollo HTTPS per la distribuzione delle licenze. Se il server delle licenze PlayReady non rientra nei Servizi multimediali, usare il protocollo HTTP o HTTPS.

L'applicazione lettore ASP.NET usa il protocollo HTTPS come procedura consigliata, quindi Media Player si trova in una pagina in HTTPS. Tuttavia, per lo streaming è preferibile usare il protocollo HTTP. È quindi necessario considerare il problema del contenuto misto.

* Il browser non consente il contenuto misto, ma lo consentono i plug-in come Silverlight e OSMF per Smooth e DASH. Il contenuto misto pone una questione di sicurezza a causa della minaccia derivante dalla possibilità di inserire codice JavaScript dannoso che può mettere a rischio i dati dei clienti. Per impostazione predefinita, il browser blocca questa funzionalità. Finora il solo modo per aggirare il problema è intervenire sul lato server (origine), per consentire tutti i domini (sia HTTPS che HTTP), ma probabilmente nemmeno questa è una buona idea.
* Evitare il contenuto misto. Sia l'applicazione lettore che Media Player devono usare il protocollo HTTP o HTTPS. Quando gestisce il contenuto misto, la tecnologia silverlightSS richiede la cancellazione di un avviso di contenuto misto. La tecnologia flashSS gestisce il contenuto misto senza avvisi di contenuto misto.
* Se l'endpoint di streaming è stato creato prima di agosto 2014, non supporterà il protocollo HTTPS. In questo caso, creare e usare un nuovo endpoint di streaming per il protocollo HTTPS.

### <a name="what-about-live-streaming"></a>Come funziona lo streaming live?

È possibile usare esattamente la stessa progettazione e la stessa implementazione per proteggere lo streaming live in Servizi multimediali, trattando l'asset associato a un programma come asset video on demand (VOD). Per fornire una protezione multi-DRM del contenuto live, applicate la stessa configurazione/elaborazione all'asset come se fosse una risorsa VOD prima di associare l'asset all'Output live.

### <a name="what-about-license-servers-outside-media-services"></a>Come funzionano i server licenze all'esterno di Servizi multimediali?

I clienti spesso investono in una farm di server licenze nel proprio data center oppure ospitata da provider di servizi DRM. Con la protezione del contenuto di Servizi multimediali, è possibile operare in modalità ibrida. È possibile ospitare e proteggere dinamicamente i contenuti in Servizi multimediali, mentre le licenze DRM vengono distribuite dal server all'esterno di Servizi multimediali. In questo caso, tenere presenti le modifiche seguenti:

* Il servizio token di sicurezza deve rilasciare token che siano accettabili e verificabili dalla farm di server licenze. Ad esempio, i server licenze Widevine forniti da Axinom richiedono uno specifico token JWT contenente un elemento "entitlement message". Per rilasciare tale token JWT, è quindi necessario un servizio token di sicurezza. 
* Non è più necessario configurare il servizio di distribuzione delle licenze in Servizi multimediali. È sufficiente fornire gli URL di acquisizione delle licenze (per PlayReady, Widevine e FairPlay) quando si configura ContentKeyPolicies.

> [!NOTE]
> Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="media-services-v2-vs-v3"></a>Servizi multimediali v2 e v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>È possibile usare il portale di Azure per gestire le risorse della versione v3?

Attualmente, è possibile usare il portale di Azure per:Currently, you can use the [Azure portal](https://portal.azure.com/) to:

* gestire Media Services v3 [Live Events](live-events-outputs-concept.md), 
* visualizza (non gestisce) [Risorse](assets-concept.md)v3 , 
* [ottenere informazioni sull'accesso alle API](access-api-portal.md). 

Per tutte le altre attività di gestione, ad esempio [Trasformazioni e processi](transforms-jobs-concept.md) e [Protezione contenuto,](content-protection-overview.md)utilizzare l'API [REST](https://docs.microsoft.com/rest/api/media/), l'interfaccia della riga [di comando](https://aka.ms/ams-v3-cli-ref)o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.

### <a name="is-there-an-assetfile-concept-in-v3"></a>È presente un concetto AssetFile nella versione v3?

Le entità AssetFile sono state rimosse dall'API AMS per separare Servizi multimediali dalla dipendenza da Storage SDK. Le informazioni che appartengono a Storage SDK vengono ora conservate in Storage e non in Servizi multimediali. 

Per altre informazioni, vedere [Eseguire la migrazione a Servizi multimediali v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Dove è finita la crittografia di archiviazione lato client?

È ora consigliabile usare la crittografia di archiviazione lato server (che è attivata per impostazione predefinita). Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="offline-streaming"></a>Streaming offline

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming per iOS

Le domande frequenti seguenti forniscono assistenza per la risoluzione dei problemi relativi allo streaming Online FairPlay per iOS:

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Perché in modalità offline viene riprodotto solo l'audio senza video?

Questo comportamento sembra essere predefinito nell'app di esempio. Quando è presente una traccia audio alternativa (come nel caso di HLS) durante la modalità offline, sia iOS 10 che iOS 11 vengono per impostazione predefinita sulla traccia audio alternativa. Per compensare questo comportamento per la modalità offline FPS, rimuovete la traccia audio alternativa dal flusso. Per eseguire questa operazione in Servizi multimediali, aggiungere il filtro di manifesto dinamico "audio-only=false". In altre parole, un URL HLS termina con .ism/manifest(format=m3u8-aapl,audio-only=false). 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Perché, anche dopo aver aggiunto audio-only=false, in modalità offline continua a essere riprodotto solo l'audio senza il video?

A seconda del modo in cui è progettata la chiave di cache della rete CDN, è possibile che il contenuto sia memorizzato nella cache. Ripulire la cache.

#### <a name="is-fps-offline-mode-also-supported-on-ios-11-in-addition-to-ios-10"></a>La modalità offline di FPS è anche supportata in iOS 11 oltre che in iOS 10?

Sì. La modalità offline di FPS è supportata per iOS 10 e iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Il documento "Offline Playback with FairPlay Streaming and HTTP Live Streaming" non è incluso in FPS Server SDK. Perché?

A partire dalla versione 4 di FPS Server SDK, questo documento è stato incluso in "FairPlay Streaming Programming Guide" (Guida alla programmazione di FairPlay Streaming).

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Qual è la struttura del file scaricato/offline nei dispositivi iOS?

La struttura del file scaricato in un dispositivo iOS è simile a quella illustrata nello screenshot seguente. La cartella `_keys` archivia le licenze FPS scaricate, con un singolo file di archivio per ogni host del servizio licenze. La cartella `.movpkg` archivia il contenuto audio e video. La prima cartella con il nome che termina con un trattino seguito da un valore numerico include contenuto video. Il valore numerico è il valore PeakBandwidth del rendering del video. La seconda cartella con il nome che termina con un trattino seguito da 0 include contenuto audio. La terza cartella denominata "Data" contiene la playlist master del contenuto FPS. Infine, boot.xml fornisce una descrizione completa del contenuto della cartella `.movpkg`. 

![Struttura del file dell'app di esempio iOS di FairPlay offline](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Un file boot.xml di esempio:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Streaming Widevine per Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-key"></a>Come è possibile distribuire licenze permanenti (abilitate per la modalità offline) ad alcuni client o utenti e licenze non permanenti (disabilitate per la modalità offline) ad altri? È necessario duplicare il contenuto e usare chiavi simmetriche distinte?

Poiché Servizi multimediali versione 3 consente a un'entità Asset di avere più entità StreamingLocators, è possibile avere:

* Un'entità ContentKeyPolicy con license_type = "persistent", un'entità ContentKeyPolicyRestriction con attestazione impostata su "persistent" e relativa entità StreamingLocator;
* Un'altra entità ContentKeyPolicy con license_type = "nonpersistent", un'entità ContentKeyPolicyRestriction con attestazione impostata su "nonpersistent" e relativa entità StreamingLocator.
* Le due entità StreamingLocators hanno diverse entità ContentKey.

In base alla logica di business del servizio token di sicurezza personalizzato, nel token JWT vengono rilasciate diverse attestazioni. Con il token, può essere ottenuta solo la licenza corrispondente e può essere usato solo il relativo URL.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Qual è la mappatura tra i livelli di sicurezza DRM di Widevine e Servizi multimediali?

"Widevine DRM Architecture Overview" di Google definisce tre diversi livelli di sicurezza. mentre nella [documentazione di Servizi multimediali di Azure relativa al modello di licenza Widevine](widevine-license-template-overview.md) ne vengono presentati cinque. In questa sezione viene illustrato il mapping dei livelli di sicurezza.

Il documento "Widevine DRM Architecture Review" di Google definisce i seguenti tre livelli di sicurezza:

* Livello di sicurezza 1: tutte le operazioni di elaborazione, crittografia e controllo del contenuto vengono eseguite all'interno dell'ambiente di esecuzione affidabile. In alcuni modelli di implementazione è possibile che l'elaborazione delle informazioni di sicurezza venga eseguita in chip diversi.
* Livello di sicurezza 2: la crittografia (ma non l'elaborazione dei dati video) viene eseguita all'interno dell'ambiente di esecuzione affidabile. I buffer decrittografati vengono restituiti al dominio applicazione ed elaborati tramite software o hardware video separato. Al livello 2, le informazioni di crittografia vengono comunque elaborate solo all'interno dell'ambiente di esecuzione affidabile.
* Livello di sicurezza 3: non è presente un ambiente di esecuzione affidabile sul dispositivo. È possibile adottare misure appropriate per proteggere le informazioni di crittografia e il contenuto decrittografato nel sistema operativo host. Un'implementazione di livello 3 può includere anche un motore di crittografia hardware, ma solo per migliorare le prestazioni, non la sicurezza.

Parallelamente, nella [documentazione di Servizi multimediali di Azure relativa al modello di licenza Widevine](widevine-license-template-overview.md) la proprietà security_level di content_key_specs può avere i cinque valori seguenti, che definiscono i diversi requisiti di affidabilità client per la riproduzione di contenuto:

* È necessario un crypto white-box basato su software.
* Sono necessari una soluzione di crittografia software e un decodificatore offuscato.
* Il materiale delle chiavi e le operazioni di crittografia devono essere gestiti all'interno di un ambiente di esecuzione affidabile basato su hardware.
* Le operazioni di crittografia e decodifica del contenuto devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.
* Le operazioni di crittografia e decodifica e l'intera gestione dei file multimediali (con e senza compressione) devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.

Entrambi i set di livelli di sicurezza sono definiti da Google Widevine. La differenza è data dall'uso che ne viene fatto, rispettivamente a livello di architettura o di API. I cinque livelli di sicurezza vengono usati nell'API Widevine. L'oggetto content_key_specs, che contiene security_level, viene deserializzato e passato al servizio di distribuzione globale di Widevine dal servizio licenze Widevine di Servizi multimediali di Azure. La tabella seguente illustra la corrispondenza tra i due set di livelli di sicurezza.

| **Livelli di sicurezza definiti nell'architettura Widevine** |**Livelli di sicurezza usati nell'API Widevine**|
|---|---| 
| **Livello di sicurezza 1:** tutta l'elaborazione, la crittografia e il controllo del contenuto vengono eseguiti all'interno di Trusted Execution Environment (TEE). In alcuni modelli di implementazione è possibile che l'elaborazione delle informazioni di sicurezza venga eseguita in chip diversi.|**security_level=5**: le operazioni di crittografia e decodifica e l'intera gestione dei file multimediali (con e senza compressione) devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.<br/><br/>**security_level=4**: le operazioni di crittografia e decodifica del contenuto devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.|
**Livello di sicurezza 2**: esegue la crittografia (ma non l'elaborazione dei dati video) all'interno dell'ambiente di esecuzione affidabile. I buffer decrittografati vengono restituiti al dominio applicazione ed elaborati tramite software o hardware video separato. Al livello 2, le informazioni di crittografia vengono comunque elaborate solo all'interno dell'ambiente di esecuzione affidabile.| **security_level=3**: il materiale delle chiavi e le operazioni di crittografia devono essere gestiti all'interno di un ambiente di esecuzione affidabile basato su hardware. |
| **Livello di sicurezza 3**: non è presente un ambiente di esecuzione affidabile sul dispositivo. È possibile adottare misure appropriate per proteggere le informazioni di crittografia e il contenuto decrittografato nel sistema operativo host. Un'implementazione di livello 3 può includere anche un motore di crittografia hardware, ma solo per migliorare le prestazioni, non la sicurezza. | **security_level2**: Sono necessari software crittografici e un decodificatore offuscato.<br/><br/>**security_level:** è richiesto il crypto white-box basato su software.|

#### <a name="why-does-content-download-take-so-long"></a>Perché il download di contenuto richiede molto tempo?

Per migliorare la velocità di download sono disponibili due diverse strategie:

* Abilitare la rete CDN in modo che gli utenti finali raggiungano con maggiore probabilità la rete CDN anziché l'endpoint di origine/streaming per il download di contenuto. Se viene raggiunto l'endpoint di streaming, ogni segmento HLS o frammento DASH viene dinamicamente incluso in un pacchetto e crittografato. Anche se questa latenza è misurabile in millisecondi per singolo segmento o frammento, nel caso di un video della durata di un'ora la latenza accumulata può comportare tempi di download più lunghi.
* Offrire agli utenti finali la possibilità di scegliere i livelli di qualità video e le tracce audio da scaricare anziché tutti i contenuti. Per la modalità offline, è inutile scaricare tutti i livelli di qualità. A questo scopo è possibile procedere in due modi:

   * Modalità controllata dal client: l'app lettore seleziona automaticamente il livello di qualità video e le tracce audio da scaricare oppure è l'utente stesso a farlo.
   * Modalità controllata dal servizio: è possibile usare la funzionalità di manifesto dinamico in Servizi multimediali di Azure per creare un filtro (globale) che limiti la playlist HLS o il file MPD (Media Presentation Description) DASH a un singolo livello di qualità video e a tracce audio selezionate. L'URL di download presentato agli utenti finali includerà pertanto questo filtro.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Servizi multimediali v3](media-services-overview.md)
