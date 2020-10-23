---
title: Domande frequenti su Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su servizi multimediali di Azure V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 50a0fe0fa5dece41ac9e343d5a8939e8d9dc634e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426887"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Domande frequenti su servizi multimediali V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo fornisce le risposte alle domande frequenti su servizi multimediali di Azure V3.

## <a name="general"></a>Generale

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Quali sono le limitazioni portale di Azure per Media Services V3?

È possibile usare la [portale di Azure](https://portal.azure.com/) per gestire gli eventi live V3, visualizzare asset e processi V3, ottenere informazioni sull'accesso alle API, crittografare il contenuto. <br/>Per tutte le altre attività di gestione (ad esempio, la gestione di trasformazioni e processi o l'analisi del contenuto V3), usare l' [API REST](/rest/api/media/accountfilters), l' [interfaccia](/cli/azure/ams)della riga di comando o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.

Se il video è stato caricato in precedenza nell'account di Servizi multimediali tramite l'API di Servizi multimediali v3 oppure il contenuto è stato generato in base a un output live, non sarà possibile visualizzare i pulsanti **Codifica**, **Analizza** o **Crittografa** nel portale di Azure. Usare le API di Servizi multimediali v3 per eseguire queste attività.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quali ruoli di Azure possono eseguire azioni sulle risorse di servizi multimediali di Azure? 

Vedere [controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gli account di servizi multimediali](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Ricerca per categorie trasmettere ai dispositivi Apple iOS?

Assicurarsi di avere **(format = m3u8-aapl)** alla fine del percorso (dopo la parte **/manifest** dell'URL) per indicare al server di origine del flusso di restituire http live streaming contenuto (HLS) per l'utilizzo nei dispositivi nativi Apple iOS. Per informazioni dettagliate, vedere [distribuzione di contenuto](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Come si esegue la configurazione di Media Reserved Units?

Per i processi di analisi audio e analisi dei video attivati da Media Services V3 o Video Indexer, è consigliabile effettuare il provisioning dell'account con 10 media reserved Unit S3 (MRU). Se sono necessari più di 10 MRU S3, aprire un ticket di supporto usando il [portale di Azure](https://portal.azure.com/).

Per informazioni dettagliate, vedere [ridimensionare l'elaborazione di contenuti multimediali](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual è il metodo consigliato per l'elaborazione dei processi?

Usare [Trasformazioni](/rest/api/media/transforms) per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni trasformazione descrive un recipe o un flusso di lavoro di attività per l'elaborazione dei file video o audio. Un [processo](/rest/api/media/jobs) è la richiesta effettiva a servizi multimediali di applicare la trasformazione a un video di input o a un contenuto audio. Dopo aver creato la trasformazione, è possibile inviare i processi usando le API di servizi multimediali o uno degli SDK pubblicati. Per altre informazioni, vedere [Trasformazioni e processi](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Ho caricato, codificato e pubblicato un video. Perché il video non viene riprodotto quando si tenta di eseguire lo streaming?

Uno dei motivi più comuni è che non si ha l'endpoint di streaming da cui si sta provando a riprodurlo nello stato Running.

### <a name="how-does-pagination-work"></a>Come funziona la paginazione?

Quando si usa la paginazione, è necessario usare sempre il collegamento successivo per enumerare la raccolta e non dipendere da una particolare dimensione della pagina. Per informazioni dettagliate ed esempi, vedere [Filtro, ordinamento, restituzione di più pagine](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quali funzionalità non sono ancora disponibili in servizi multimediali di Azure V3?

Per informazioni dettagliate, vedere [gap delle funzionalità rispetto alle API v2](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual è il processo di trasferimento di un account di servizi multimediali tra le sottoscrizioni?  

Per informazioni dettagliate, vedere [trasferimento di un account di servizi multimediali tra sottoscrizioni](media-services-account-concept.md).

## <a name="live-streaming"></a>Streaming live 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Ricerca per categorie arrestare il flusso live al termine della trasmissione?

È possibile accedervi dal lato client o dal lato server.

#### <a name="client-side"></a>Lato client

L'applicazione Web deve richiedere all'utente se desidera terminare la trasmissione mentre sta chiudendo il browser. Si tratta di un evento del browser che l'applicazione Web è in grado di gestire.

#### <a name="server-side"></a>Sul lato server

È possibile monitorare gli eventi live sottoscrivendo gli eventi di griglia di eventi di Azure. Per ulteriori informazioni, vedere lo [schema dell'evento EventGrid](media-services-event-schemas.md#live-event-types).

È possibile:

* [Sottoscrivere](reacting-to-media-services-events.md) gli eventi [Microsoft. Media. LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) a livello di flusso e monitorare che non sono disponibili riconnessioni per un periodo di tempo per arrestare ed eliminare l'evento Live.
* [Sottoscrivere](reacting-to-media-services-events.md) gli eventi di [heartbeat](media-services-event-schemas.md#liveeventingestheartbeat) a livello di traccia. Se tutte le tracce hanno una velocità in bit in ingresso che diminuisce a 0 o l'ultimo timestamp non è più in aumento, è possibile arrestare tranquillamente l'evento Live. Gli eventi di heartbeat arrivano ogni 20 secondi per ogni traccia, quindi potrebbe essere un po' dettagliato.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Ricerca per categorie inserire interruzioni/video e Slate di immagini durante un flusso Live?

La codifica live di Servizi multimediali v3 non supporta ancora l'inserimento di slate immagine o video durante lo streaming live. 

È possibile usare un [codificatore locale live](recommended-on-premises-live-encoders.md) per commutare il video di origine. Molte app offrono la possibilità di cambiare origine, tra cui Telestream Wirecast, Switcher Studio (in iOS) ed OBS Studio (app gratuita).

## <a name="content-protection"></a>Protezione del contenuto

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>È consigliabile usare la crittografia con chiave non crittografata AES-128 o un sistema DRM?

I clienti spesso si chiedono se devono usare la crittografia AES o un sistema DRM. La differenza principale tra i due sistemi è che con la crittografia AES la chiave simmetrica viene trasmessa al client su TLS, in modo che la chiave sia crittografata in transito, ma senza alcuna crittografia aggiuntiva ("in chiaro"). Di conseguenza, la chiave usata per decrittografare il contenuto è accessibile al lettore client e può essere visualizzata in una traccia di rete sul client in testo normale. La crittografia con chiave non crittografata AES-128 è adatta nei casi in cui il visualizzatore è attendibile (ad esempio, la crittografia di video aziendali distribuiti all'interno di un'azienda per essere visualizzati dai dipendenti).

I sistemi DRM come PlayReady, Widevine e FairPlay forniscono tutti un livello aggiuntivo di crittografia sulla chiave usata per decrittografare il contenuto, rispetto a una chiave non crittografata AES-128. La chiave simmetrica viene crittografata con una chiave protetta dal runtime di DRM, oltre a qualsiasi crittografia a livello di trasporto fornita da TLS. Inoltre, la decrittografia viene gestita in un ambiente sicuro a livello di sistema operativo, più difficilmente attaccabile da un utente malintenzionato. Si consiglia DRM per i casi d'uso in cui il visualizzatore potrebbe non essere una parte attendibile ed è necessario il massimo livello di sicurezza.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Ricerca per categorie mostrare un video solo agli utenti che dispongono di un'autorizzazione specifica, senza usare Azure AD?

Non è necessario usare un provider di token specifico, ad esempio Azure Active Directory (Azure AD). È possibile creare un provider [JWT](https://jwt.io/) personalizzato, denominato servizio token di sicurezza o STS, usando la crittografia a chiave asimmetrica. Nel servizio token di protezione personalizzato è possibile aggiungere attestazioni in base alla logica di business.

Verificare che l'autorità emittente, il gruppo di destinatari e tutte le attestazioni corrispondano esattamente tra gli elementi di JWT e il `ContentKeyPolicyRestriction` valore utilizzato in `ContentKeyPolicy` .

Per altre informazioni, vedere [proteggere il contenuto usando la crittografia dinamica di servizi multimediali](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Come e dove è stato ottenuto un token JWT prima di usarlo per richiedere una licenza o una chiave?

Per la produzione, è necessario disporre di un servizio token di sicurezza, ovvero un servizio Web, che rilascia un token JWT su una richiesta HTTPS. Per il test, è possibile usare il codice illustrato nel `GetTokenAsync` metodo definito in [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

Il lettore esegue una richiesta, dopo che un utente è stato autenticato, a STS per un token di questo tipo e lo assegna come valore del token. È possibile usare l'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

Per un esempio di esecuzione di STS con una chiave simmetrica o asimmetrica, vedere lo [strumento JWT](https://aka.ms/jwt). Per un esempio di un lettore basato su Azure Media Player usando un token JWT di questo tipo, vedere lo [strumento Azure Media test](https://aka.ms/amtest). (Espandere il collegamento **player_settings** per visualizzare l'input del token).

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Ricerca per categorie autorizzare le richieste per lo streaming di video con la crittografia AES?

L'approccio corretto consiste nell'usare il servizio token di sicurezza. In STS, a seconda del profilo utente, aggiungere attestazioni diverse (ad esempio "utente Premium", "utente di base", "utente della versione di valutazione gratuita"). Con diverse attestazioni in un token JWT, l'utente può visualizzare diversi tipi di contenuto. Per contenuti o asset diversi, `ContentKeyPolicyRestriction` avrà il valore corrispondente `RequiredClaims` .

Usare le API di servizi multimediali di Azure per la configurazione del recapito di licenze/chiavi e la crittografia degli asset (come illustrato in [questo esempio](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Per altre informazioni, vedere:

- [Panoramica della protezione del contenuto](content-protection-overview.md)
- [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>È consigliabile usare HTTP o HTTPS?
L'applicazione lettore MVC ASP.NET deve supportare quanto segue:

* Autenticazione utente con Azure AD, che deve usare il protocollo HTTPS.
* Scambio di token JWT tra il client e Azure AD, che deve usare il protocollo HTTPS.
* Acquisizione della licenza DRM dal client, che deve essere gestita tramite protocollo HTTPS se la distribuzione delle licenze avviene tramite Servizi multimediali. La famiglia di prodotti PlayReady non obbliga a usare il protocollo HTTPS per la distribuzione delle licenze. Se il server delle licenze PlayReady non rientra nei Servizi multimediali, usare il protocollo HTTP o HTTPS.

L'applicazione lettore ASP.NET usa il protocollo HTTPS come procedura consigliata, quindi Media Player si trova in una pagina in HTTPS. Tuttavia, per lo streaming è preferibile usare HTTP, quindi è necessario considerare questi problemi con contenuto misto:

* Il browser non consente il contenuto misto, I plug-in come Silverlight e il plug-in OSMF per Smooth e DASH lo consentono. Il contenuto misto è un problema di sicurezza a causa della minaccia della possibilità di inserire codice JavaScript dannoso, che può mettere a rischio i dati dei clienti. Per impostazione predefinita, il browser blocca questa funzionalità. Finora il solo modo per aggirare il problema è intervenire sul lato server (origine), per consentire tutti i domini (sia HTTPS che HTTP), ma probabilmente nemmeno questa è una buona idea.
* Evitare il contenuto misto. Sia l'applicazione lettore che Media Player devono usare il protocollo HTTP o HTTPS. Quando si esegue contenuto misto, la tecnologia Silverlights richiede la cancellazione di un avviso di contenuto misto. Il Tech Flash gestisce il contenuto misto senza avvisi di contenuto misto.
* Se l'endpoint di streaming è stato creato prima di agosto 2014, non supporterà il protocollo HTTPS. In questo caso, creare e usare un nuovo endpoint di streaming per il protocollo HTTPS.

### <a name="what-about-live-streaming"></a>Come funziona lo streaming live?

È possibile usare esattamente la stessa progettazione e la stessa implementazione per proteggere lo streaming live in servizi multimediali trattando l'asset associato a un programma come asset VOD. Per fornire una protezione DRM multipla del contenuto Live, applicare la stessa configurazione/elaborazione all'asset come se si trattasse di un asset VOD prima di associare l'asset all'output Live.

### <a name="what-about-license-servers-outside-media-services"></a>Come funzionano i server licenze all'esterno di Servizi multimediali?

Spesso i clienti hanno investito in una licenza server farm nel proprio Data Center o in uno ospitato da provider di servizi DRM. Con la protezione del contenuto di Servizi multimediali, è possibile operare in modalità ibrida. I contenuti possono essere ospitati e protetti in modo dinamico in servizi multimediali, mentre le licenze DRM vengono distribuite dai server all'esterno di servizi multimediali. In questo caso, tenere presenti le modifiche seguenti:

* Il servizio token di sicurezza deve rilasciare token che siano accettabili e verificabili dalla farm di server licenze. Ad esempio, i server licenze Widevine forniti da Axinom richiedono uno specifico token JWT contenente un elemento "entitlement message". È necessario disporre di un STS per emettere un JWT. 
* Non è più necessario configurare il servizio di distribuzione delle licenze in Servizi multimediali. Quando si configura, è necessario fornire gli URL di acquisizione delle licenze (per PlayReady, Widevine e FairPlay) `ContentKeyPolicy` .

> [!NOTE]
> Widevine è un servizio fornito da Google e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google.

## <a name="media-services-v2-vs-v3"></a>Media Services V2 e V3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>È possibile usare il portale di Azure per gestire le risorse della versione v3?

Attualmente, è possibile utilizzare il [portale di Azure](https://portal.azure.com/) per:

* Gestire [eventi live](live-events-outputs-concept.md) in servizi multimediali V3. 
* Visualizzare (non gestire) gli [Asset](assets-concept.md)V3. 
* [Ottenere informazioni sull'accesso alle API](./access-api-howto.md). 

Per tutte le altre attività di gestione (ad esempio, [trasformazioni e processi](transforms-jobs-concept.md) e [protezione del contenuto](content-protection-overview.md)), usare l' [API REST](/rest/api/media/), l'interfaccia della riga di comando di [Azure](/cli/azure/ams)o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.

### <a name="is-there-an-assetfile-concept-in-v3"></a>È presente un concetto AssetFile nella versione v3?

Il `AssetFile` concetto è stato rimosso dall'API di servizi multimediali per separare servizi multimediali dalla dipendenza di storage SDK. Ora archiviazione di Azure, non servizi multimediali, mantiene le informazioni appartenenti all'SDK di archiviazione. 

Per altre informazioni, vedere [Eseguire la migrazione a Servizi multimediali v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Dove è finita la crittografia di archiviazione lato client?

È ora consigliabile usare la crittografia di archiviazione sul lato server, che è abilitata per impostazione predefinita. Per altre informazioni, vedere [crittografia del servizio di archiviazione di Azure per i dati](../../storage/common/storage-service-encryption.md)inattivi.

## <a name="offline-streaming"></a>Streaming offline

### <a name="fairplay-streaming-for-ios"></a>Streaming FairPlay per iOS

Le seguenti domande frequenti forniscono assistenza per la risoluzione dei problemi relativi allo streaming FairPlay offline per iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Perché in modalità offline viene riprodotto solo l'audio senza video?

Questo comportamento sembra essere predefinito nell'app di esempio. Quando è presente una traccia audio alternativa (come nel caso di HLS) durante la modalità offline, l'impostazione predefinita di iOS 10 e iOS 11 è la traccia audio alternativa. Per compensare questo comportamento per la modalità offline di FPS, rimuovere la traccia audio alternativa dal flusso. Per eseguire questa operazione in servizi multimediali, aggiungere il filtro manifesto dinamico **solo audio-only = false**. In altre parole, un URL HLS termina con **. ISM/manifest (format = m3u8-aapl, audio-only = false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Perché, anche dopo aver aggiunto audio-only=false, in modalità offline continua a essere riprodotto solo l'audio senza il video?

A seconda della progettazione della chiave della cache per la rete per la distribuzione di contenuti, il contenuto potrebbe essere memorizzato nella cache. Ripulire la cache.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>La modalità offline di FPS è supportata in iOS 11 oltre che in iOS 10?

Sì. La modalità offline di FPS è supportata per iOS 10 e iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Il documento "Offline Playback with FairPlay Streaming and HTTP Live Streaming" non è incluso in FPS Server SDK. Perché?

A partire dalla versione 4 di FPS Server SDK, questo documento è stato incluso in "FairPlay Streaming Programming Guide" (Guida alla programmazione di FairPlay Streaming).

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Qual è la struttura del file scaricato/offline nei dispositivi iOS?

La struttura del file scaricato in un dispositivo iOS è simile a quella illustrata nello screenshot seguente. La cartella `_keys` archivia le licenze FPS scaricate, con un singolo file di archivio per ogni host del servizio licenze. La cartella `.movpkg` archivia il contenuto audio e video. 

La prima cartella con un nome che termina con un trattino seguito da un numero contiene contenuto video. Il valore numerico corrisponde alla larghezza di banda massima dei rendering video. La seconda cartella con il nome che termina con un trattino seguito da 0 include contenuto audio. La terza cartella denominata `Data` contiene la playlist master del contenuto fps. Infine, boot.xml fornisce una descrizione completa del contenuto della cartella `.movpkg`. 

![Struttura di file offline per l'app di esempio FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Ecco un file di boot.xml di esempio:

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

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Come è possibile distribuire licenze permanenti (non in linea abilitate) per alcuni client/utenti e licenze non permanenti (offline disabilitate) per gli altri? È necessario duplicare il contenuto e usare chiavi simmetriche separate?

Poiché Media Services v3 consente a un asset di avere più `StreamingLocator` istanze, è possibile avere:

* Un' `ContentKeyPolicy` istanza con `license_type = "persistent"` , `ContentKeyPolicyRestriction` con attestazione su `"persistent"` e il relativo oggetto `StreamingLocator` .
* Un'altra `ContentKeyPolicy` istanza con `license_type="nonpersistent"` , `ContentKeyPolicyRestriction` con attestazione su `"nonpersistent` "e la relativa `StreamingLocator` .
* Due `StreamingLocator` istanze con valori diversi `ContentKey` .

In base alla logica di business del servizio token di sicurezza personalizzato, nel token JWT vengono rilasciate diverse attestazioni. Con il token, può essere ottenuta solo la licenza corrispondente e può essere usato solo il relativo URL.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Qual è il mapping tra i livelli di sicurezza DRM Widevine e servizi multimediali?

"Panoramica dell'architettura DRM Widevine" di Google definisce tre livelli di sicurezza. Tuttavia, la [documentazione di servizi multimediali di Azure nel modello di licenza Widevine](widevine-license-template-overview.md) delinea cinque livelli di sicurezza (requisiti di affidabilità client per la riproduzione). In questa sezione viene illustrato il mapping dei livelli di sicurezza.

Entrambi i set di livelli di sicurezza sono definiti da Google Widevine. La differenza si trova nel livello di utilizzo: architettura o API. I cinque livelli di sicurezza vengono usati nell'API Widevine. L' `content_key_specs` oggetto, che contiene `security_level` , viene deserializzato e passato al servizio di recapito globale Widevine dal servizio di licenza Widevine di servizi multimediali di Azure. Nella tabella seguente viene illustrato il mapping tra i due set di livelli di sicurezza.

| **Livelli di sicurezza definiti nell'architettura Widevine** |**Livelli di sicurezza usati nell'API Widevine**|
|---|---| 
| **Livello di sicurezza 1**: tutte le operazioni di elaborazione, crittografia e controllo del contenuto vengono eseguite all'interno dell'ambiente di esecuzione attendibile. In alcuni modelli di implementazione, l'elaborazione della sicurezza può essere eseguita in chip diversi.|**security_level = 5**: la crittografia, la decodifica e tutte le gestioni dei supporti (compressi e non compressi) devono essere gestite in un tee con supporto hardware.<br/><br/>**security_level = 4**: la crittografia e la decodifica del contenuto devono essere eseguite all'interno di un tee con supporto hardware.|
**Livello di sicurezza 2**: la crittografia (ma non l'elaborazione video) viene eseguita all'interno del tee. I buffer decrittografati vengono restituiti al dominio applicazione ed elaborati tramite software o hardware video separato. Al livello 2, tuttavia, le informazioni crittografiche vengono ancora elaborate solo all'interno del TEE.| **security_level = 3**: il materiale della chiave e le operazioni di crittografia devono essere eseguiti all'interno di un tee con supporto hardware. |
| **Livello di sicurezza 3**: non è presente alcun Tee sul dispositivo. È possibile adottare misure appropriate per proteggere le informazioni crittografiche e il contenuto decrittografato nel sistema operativo host. Un'implementazione di livello 3 può includere anche un motore di crittografia hardware, che migliora solo le prestazioni e non la sicurezza. | **security_level = 2**: sono necessari la crittografia software e un decodificatore offuscato.<br/><br/>**security_level = 1**: è richiesta la crittografia a casella bianca basata su software.|

#### <a name="why-does-content-download-take-so-long"></a>Perché il download di contenuto richiede molto tempo?

Per migliorare la velocità di download sono disponibili due diverse strategie:

* Abilitare una rete per la distribuzione di contenuti in modo che gli utenti abbiano più probabilità di raggiungere tale rete anziché l'endpoint di origine/streaming per il download del contenuto. Se un utente raggiunge un endpoint di streaming, ogni segmento HLS o frammento DASH viene impacchettato e crittografato dinamicamente. Anche se questa latenza è in millisecondi per ogni segmento o frammento, quando si dispone di un video di ora, la latenza accumulata può essere di grandi dimensioni e causare un download più lungo.
* Offrire agli utenti la possibilità di scaricare in modo selettivo i livelli di qualità video e le tracce audio anziché tutto il contenuto. Per la modalità offline, non è possibile scaricare tutti i livelli qualitativi. A questo scopo è possibile procedere in due modi:

  * Controllato dal client: l'App Player seleziona automaticamente o l'utente seleziona, il livello di qualità video e le tracce audio da scaricare.
  * Controllo dei servizi: è possibile usare la funzionalità manifesto dinamico in servizi multimediali di Azure per creare un filtro (globale), che limita la playlist HLS o il DASH MPD a un singolo livello di qualità video e le tracce audio selezionate. Quindi, l'URL di download presentato agli utenti includerà il filtro.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Servizi multimediali v3](media-services-overview.md)