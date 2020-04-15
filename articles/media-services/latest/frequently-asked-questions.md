---
title: Domande frequenti su Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su Servizi multimediali di Azure 3.This article gives answers to frequently asked questions about Azure Media Services v3.
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
ms.openlocfilehash: 713acbd098255af2869d7a462c9990f3d7e10bf1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309194"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Domande frequenti su Media Services v3

Questo articolo fornisce le risposte alle domande frequenti su Servizi multimediali di Azure 3.This article gives answers to frequently asked questions about Azure Media Services v3.

## <a name="general"></a>Generale

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quali ruoli di Azure possono eseguire azioni sulle risorse di Servizi multimediali di Azure? 

Vedere Controllo degli accessi in base al [ruolo per gli account di Servizi multimediali.](rbac-overview.md)

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Come faccio a trasmettere in streaming sui dispositivi Apple iOS?

Alla fine del percorso (dopo la parte **/manifest** dell'URL) assicurarsi di avere **(format) (formato :m3u8-aapl)** per indicare al server di origine del flusso di restituire il contenuto HTTP Live Streaming (HLS) per l'utilizzo nei dispositivi nativi Apple iOS. Per informazioni dettagliate, consultate [Consegna del contenuto.](dynamic-packaging-overview.md)

### <a name="how-do-i-configure-media-reserved-units"></a>Come si esegue la configurazione di Media Reserved Units?

Per i processi di analisi audio e analisi video attivati da Servizi multimediali v3 o Registrato Indexer, è consigliabile eseguire il provisioning dell'account con 10 Unità riservate (MrU) multimediali S3. Se sono necessarie più di 10 procedure MRU S3, aprire un ticket di supporto tramite il portale di [Azure.](https://portal.azure.com/)

Per informazioni dettagliate, consultate [Ridimensionare l'elaborazione multimediale.](media-reserved-units-cli-how-to.md)

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual è il metodo consigliato per l'elaborazione dei processi?

Usare [Trasformazioni](https://docs.microsoft.com/rest/api/media/transforms) per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni trasformazione descrive un recipe o un flusso di lavoro di attività per l'elaborazione dei file video o audio. Un [processo](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva a Servizi multimediali di applicare la trasformazione a un contenuto video o audio di input. Dopo aver creato la trasformazione, è possibile inviare processi utilizzando le API di Servizi multimediali o uno qualsiasi degli SDK pubblicati. Per altre informazioni, vedere [Trasformazioni e processi](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Ho caricato, codificato e pubblicato un video. Perché il video non viene riprodotto quando provo a riprodurlo in streaming?

Uno dei motivi più comuni è che non si dispone dell'endpoint di streaming da cui si sta tentando di riprodurre nello stato In esecuzione.

### <a name="how-does-pagination-work"></a>Come funziona la paginazione?

Quando si usa l'impaginazione, è consigliabile usare sempre il collegamento successivo per enumerare la raccolta e non dipendere da una determinata dimensione di pagina. Per informazioni dettagliate ed esempi, vedere [Filtro, ordinamento, restituzione di più pagine](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quali funzionalità non sono ancora disponibili in Servizi multimediali di Azure v3?

Per informazioni dettagliate, consultate [Spazi delle funzionalità rispetto alle API v2.](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual è il processo di spostamento di un account di Servizi multimediali tra sottoscrizioni?  

Per informazioni dettagliate, vedere [Spostamento di un account di Servizi multimediali tra sottoscrizioni.](media-services-account-concept.md)

## <a name="live-streaming"></a>Streaming live 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Come faccio a interrompere il live streaming dopo la trasmissione è stata completata?

È possibile affrontarlo dal lato client o dal lato server.

#### <a name="client-side"></a>Lato client

L'applicazione Web deve chiedere all'utente se desidera terminare la trasmissione mentre sta chiudendo il browser. Si tratta di un evento del browser che l'applicazione Web è in grado di gestire.

#### <a name="server-side"></a>Sul lato server

È possibile monitorare gli eventi live sottoscrivendo eventi Griglia di eventi di Azure.You can monitor live events by subscribing to Azure Event Grid events. Per ulteriori informazioni, vedere lo [schema degli eventi EventGrid](media-services-event-schemas.md#live-event-types).

È possibile:

* [Sottoscrivi](reacting-to-media-services-events.md) gli eventi [Microsoft.Media.LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) a livello di flusso e monitora che non sono disponibili riconnessioni per un po' di tempo per interrompere ed eliminare l'evento live.
* [Sottoscrivere](reacting-to-media-services-events.md) gli eventi [heartbeat](media-services-event-schemas.md#liveeventingestheartbeat) a livello di traccia. Se tutte le tracce hanno una velocità in bit in ingresso che scende a 0 o l'ultimo timestamp non è più in aumento, è possibile arrestare in modo sicuro l'evento live. Gli eventi heartbeat arrivano ogni 20 secondi per ogni traccia, quindi potrebbe essere un po' dettagliato.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Come inserisco interruzioni/video e lavagne di immagini durante un live streaming?

La codifica live di Servizi multimediali v3 non supporta ancora l'inserimento di slate immagine o video durante lo streaming live. 

È possibile usare un [codificatore locale live](recommended-on-premises-live-encoders.md) per commutare il video di origine. Molte app offrono la possibilità di passare da una fonte all'altra, tra cui Telestream Wirecast, Switcher Studio (su iOS) e OBS Studio (app gratuita).

## <a name="content-protection"></a>Protezione del contenuto

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usare la crittografia a chiave non crittografata AES-128 o un sistema DRM?

I clienti spesso si chiedono se devono usare la crittografia AES o un sistema DRM. La differenza principale tra i due sistemi è che con la crittografia AES, la chiave simmetrica viene trasmessa al client tramite TLS in modo che la chiave venga crittografata in transito ma senza alcuna crittografia aggiuntiva ("in chiaro"). Di conseguenza, la chiave utilizzata per decrittografare il contenuto è accessibile al lettore client e può essere visualizzata in una traccia di rete sul client in testo normale. La crittografia con chiave non crittografata AES-128 è adatta nei casi in cui il visualizzatore è attendibile (ad esempio, la crittografia di video aziendali distribuiti all'interno di un'azienda per essere visualizzati dai dipendenti).

I sistemi DRM come PlayReady, Widevine e FairPlay offrono tutti un ulteriore livello di crittografia sulla chiave utilizzata per decrittografare il contenuto, rispetto a una chiave non crittografata AES-128. La chiave simmetrica viene crittografata in una chiave protetta dal runtime DRM oltre a qualsiasi crittografia a livello di trasporto fornita da TLS. Inoltre, la decrittografia viene gestita in un ambiente sicuro a livello di sistema operativo, più difficilmente attaccabile da un utente malintenzionato. È consigliabile DRM per i casi d'uso in cui il visualizzatore potrebbe non essere un'entità attendibile ed è necessario il massimo livello di sicurezza.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Come si mostra un video solo agli utenti che dispongono di un'autorizzazione specifica, senza usare Azure AD?

Non è necessario usare alcun provider di token specifico, ad esempio Azure Active Directory (Azure AD). È possibile creare il proprio provider [JWT](https://jwt.io/) (il cosiddetto Secure Token Service o servizio token di sicurezza) utilizzando la crittografia a chiave asimmetrica. Nel servizio token di sicurezza personalizzato è possibile aggiungere attestazioni in base alla logica di business.

Assicurarsi che l'autorità emittente, il pubblico e le attestazioni corrispondano esattamente tra il contenuto di JWT e il `ContentKeyPolicyRestriction` valore utilizzato in `ContentKeyPolicy`.

Per ulteriori informazioni, consultate Proteggere il contenuto utilizzando la crittografia dinamica di [Servizi multimediali.](content-protection-overview.md)

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Come e dove ho ottenuto un token JWT prima di utilizzarlo per richiedere una licenza o una chiave?

Per la produzione, è necessario disporre di Secure Token Service (ovvero, un servizio Web), che emette un token JWT su una richiesta HTTPS. Per il test, è possibile `GetTokenAsync` utilizzare il codice illustrato nel metodo definito in [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

Il giocatore effettua una richiesta, dopo l'autenticazione di un utente, al servizio token di sicurezza per tale token e la assegna come valore del token. È possibile usare l'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

Per un esempio di esecuzione del servizio token di sicurezza con una chiave simmetrica o una chiave asimmetrica, vedere lo [strumento JWT](https://aka.ms/jwt). Per un esempio di un lettore basato su Azure Media Player che usa un token JWT di questo tipo, vedere lo strumento di [test multimediale](https://aka.ms/amtest)di Azure.For an example of a player based on Azure Media Player using such a JWT token, see the Azure media test tool . Espandere il collegamento **player_settings** per visualizzare l'input del token.

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Come posso autorizzare le richieste di streaming video con crittografia AES?

L'approccio corretto consiste nell'utilizzare il servizio token di sicurezza. Nel servizio token di sicurezza, a seconda del profilo utente, aggiungere attestazioni diverse (ad esempio "Utente Premium", "Utente di base", "Utente di prova gratuito"). Con diverse attestazioni in un token JWT, l'utente può visualizzare diversi tipi di contenuto. Per contenuti o `ContentKeyPolicyRestriction` attività diversi, `RequiredClaims` avrà il valore corrispondente.

Usare le API di Servizi multimediali di Azure per configurare il recapito di licenze/chiavi e crittografare gli asset (come illustrato in [questo esempio).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Per altre informazioni, vedere:

- [Panoramica della protezione del contenuto](content-protection-overview.md)
- [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Devo usare HTTP o HTTPS?
L'applicazione lettore MVC ASP.NET deve supportare quanto segue:

* Autenticazione utente con Azure AD, che deve usare il protocollo HTTPS.
* Scambio di token JWT tra il client e Azure AD, che deve usare il protocollo HTTPS.
* Acquisizione della licenza DRM dal client, che deve essere gestita tramite protocollo HTTPS se la distribuzione delle licenze avviene tramite Servizi multimediali. La famiglia di prodotti PlayReady non obbliga a usare il protocollo HTTPS per la distribuzione delle licenze. Se il server delle licenze PlayReady non rientra nei Servizi multimediali, usare il protocollo HTTP o HTTPS.

L'applicazione lettore ASP.NET usa il protocollo HTTPS come procedura consigliata, quindi Media Player si trova in una pagina in HTTPS. Tuttavia, HTTP è preferibile per lo streaming, pertanto è necessario considerare questi problemi con il contenuto misto:However, HTTP is preferred for streaming, so you need to consider these issues with mixed content:

* Il browser non consente il contenuto misto, Ma i plug-in come Silverlight e il plug-in OSMF per Smooth e DASH lo consentono. Il contenuto misto è un problema di sicurezza a causa della minaccia della possibilità di iniettare JavaScript dannoso, che può mettere a rischio i dati dei clienti. Per impostazione predefinita, il browser blocca questa funzionalità. Finora il solo modo per aggirare il problema è intervenire sul lato server (origine), per consentire tutti i domini (sia HTTPS che HTTP), ma probabilmente nemmeno questa è una buona idea.
* Evitare il contenuto misto. Sia l'applicazione lettore che Media Player devono usare il protocollo HTTP o HTTPS. Quando si riproduce contenuto misto, il tecnico SilverlightSS richiede la cancellazione di un avviso di contenuto misto. La tecnologia FlashSS gestisce il contenuto misto senza un avviso di contenuto misto.
* Se l'endpoint di streaming è stato creato prima di agosto 2014, non supporterà il protocollo HTTPS. In questo caso, creare e usare un nuovo endpoint di streaming per il protocollo HTTPS.

### <a name="what-about-live-streaming"></a>Come funziona lo streaming live?

Puoi usare esattamente la stessa progettazione e la stessa implementazione per proteggere lo streaming live in Servizi multimediali trattando l'asset associato a un programma come un asset VOD. Per fornire una protezione multi-DRM del contenuto live, applica la stessa configurazione/elaborazione all'asset come se fosse una risorsa VOD prima di associare l'asset all'output live.

### <a name="what-about-license-servers-outside-media-services"></a>Come funzionano i server licenze all'esterno di Servizi multimediali?

Spesso, i clienti hanno investito in una server farm di licenze nel proprio data center o in uno ospitato da provider di servizi DRM. Con la protezione del contenuto di Servizi multimediali, è possibile operare in modalità ibrida. Il contenuto può essere ospitato e protetto dinamicamente in Servizi multimediali, mentre le licenze DRM vengono fornite da server esterni a Servizi multimediali. In questo caso, tenere presenti le modifiche seguenti:

* Il servizio token di sicurezza deve rilasciare token che siano accettabili e verificabili dalla farm di server licenze. Ad esempio, i server licenze Widevine forniti da Axinom richiedono uno specifico token JWT contenente un elemento "entitlement message". È necessario disporre di un servizio token di sicurezza per emettere un token JWT di questo tipo. 
* Non è più necessario configurare il servizio di distribuzione delle licenze in Servizi multimediali. È necessario fornire gli URL di acquisizione delle licenze (per PlayReady, `ContentKeyPolicy`Widevine e FairPlay) quando si configura .

> [!NOTE]
> Widevine è un servizio fornito da Google e soggetto ai termini di servizio e alla politica sulla privacy di Google.

## <a name="media-services-v2-vs-v3"></a>Servizi multimediali v2 e v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>È possibile usare il portale di Azure per gestire le risorse della versione v3?

Attualmente, è possibile usare il portale di Azure per:Currently, you can use the [Azure portal](https://portal.azure.com/) to:

* Gestire gli [eventi live](live-events-outputs-concept.md) in Servizi multimediali v3. 
* Visualizzare (non gestire) [le risorse](assets-concept.md)v3. 
* [Ottenere informazioni sull'accesso alle API.](access-api-portal.md) 

Per tutte le altre attività di gestione, ad esempio [Trasformazioni e processi](transforms-jobs-concept.md) e [protezione del contenuto,](content-protection-overview.md)usare l'API [REST](https://docs.microsoft.com/rest/api/media/), l'interfaccia della riga di comando di [Azure](https://aka.ms/ams-v3-cli-ref)o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.

### <a name="is-there-an-assetfile-concept-in-v3"></a>È presente un concetto AssetFile nella versione v3?

Il `AssetFile` concetto è stato rimosso dall'API di Servizi multimediali per separare Servizi multimediali dalla dipendenza dell'SDK di archiviazione. Ora Archiviazione di Azure, non Servizi multimediali, mantiene le informazioni appartenenti all'SDK di archiviazione. 

Per altre informazioni, vedere [Eseguire la migrazione a Servizi multimediali v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Dove è finita la crittografia di archiviazione lato client?

È ora consigliabile utilizzare la crittografia di archiviazione lato server (attivata per impostazione predefinita). Per altre informazioni, vedere Crittografia del servizio di archiviazione di Azure per i [dati inattivi.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

## <a name="offline-streaming"></a>Streaming offline

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming per iOS

Le domande frequenti seguenti forniscono assistenza per la risoluzione dei problemi relativi allo streaming Online FairPlay per iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Perché in modalità offline viene riprodotto solo l'audio senza video?

Questo comportamento sembra essere predefinito nell'app di esempio. Quando è presente una traccia audio alternativa (come nel caso di HLS) durante la modalità offline, sia iOS 10 che iOS 11 vengono per impostazione predefinita sulla traccia audio alternativa. Per compensare questo comportamento per la modalità offline FPS, rimuovete la traccia audio alternativa dal flusso. Per eseguire questa operazione in Servizi multimediali, aggiungere il filtro del manifesto dinamico **audio-only : false**. In altre parole, un URL HLS termina con **.ism/manifest(format : m3u8-aapl, audio-only )false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Perché, anche dopo aver aggiunto audio-only=false, in modalità offline continua a essere riprodotto solo l'audio senza il video?

A seconda della progettazione della chiave di cache per la rete per la distribuzione del contenuto, il contenuto potrebbe essere memorizzato nella cache. Ripulire la cache.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>La modalità offline di FPS è supportata su iOS 11 oltre a iOS 10?

Sì. La modalità offline di FPS è supportata per iOS 10 e iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Il documento "Offline Playback with FairPlay Streaming and HTTP Live Streaming" non è incluso in FPS Server SDK. Perché?

A partire dalla versione 4 di FPS Server SDK, questo documento è stato incluso in "FairPlay Streaming Programming Guide" (Guida alla programmazione di FairPlay Streaming).

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Qual è la struttura del file scaricato/offline nei dispositivi iOS?

La struttura del file scaricato in un dispositivo iOS è simile a quella illustrata nello screenshot seguente. La cartella `_keys` archivia le licenze FPS scaricate, con un singolo file di archivio per ogni host del servizio licenze. La cartella `.movpkg` archivia il contenuto audio e video. 

La prima cartella con un nome che termina con un trattino seguito da un numero contiene contenuto video. Il valore numerico è la larghezza di banda massima delle copie trasformate video. La seconda cartella con il nome che termina con un trattino seguito da 0 include contenuto audio. La terza `Data` cartella denominata contiene la playlist principale del contenuto FPS. Infine, boot.xml fornisce una descrizione completa del contenuto della cartella `.movpkg`. 

![Struttura di file offline per l'app di esempio FairPlay per iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Di seguito è riportato un file boot.xml di esempio:Here's a sample boot.xml file:

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

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Come posso liberare licenze permanenti (abilitate offline) per alcuni client/utenti e licenze non persistenti (offline disabilitato) per altri? È necessario duplicare il contenuto e utilizzare chiavi di contenuto separate?

Poiché Servizi multimediali v3 consente `StreamingLocator` a una risorsa di avere più istanze, è possibile avere:Because Media Services v3 allows an asset to have multiple instances, you can have:

* `ContentKeyPolicy` Un'istanza `license_type = "persistent"` `ContentKeyPolicyRestriction` con , `"persistent"`con `StreamingLocator`attestazione su , e la relativa .
* `ContentKeyPolicy` Un'altra `license_type="nonpersistent"` `ContentKeyPolicyRestriction` istanza con `"nonpersistent`, con `StreamingLocator`attestazione su ", e la sua .
* Due `StreamingLocator` istanze `ContentKey` con valori diversi.

In base alla logica di business del servizio token di sicurezza personalizzato, nel token JWT vengono rilasciate diverse attestazioni. Con il token, può essere ottenuta solo la licenza corrispondente e può essere usato solo il relativo URL.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Qual è la mappatura tra i livelli di sicurezza DRM di Widevine e Servizi multimediali?

"Widevine DRM Architecture Overview" di Google definisce tre livelli di sicurezza. Tuttavia, la documentazione di Servizi multimediali di Azure nel modello di [licenza Widevine](widevine-license-template-overview.md) descrive cinque livelli di sicurezza (requisiti di robustezza del client per la riproduzione). In questa sezione viene illustrato il mapping dei livelli di sicurezza.

Entrambi i set di livelli di sicurezza sono definiti da Google Widevine. La differenza è nel livello di utilizzo: architettura o API. I cinque livelli di sicurezza vengono usati nell'API Widevine. L'oggetto, `content_key_specs` `security_level`che contiene , viene deserializzato e passato al servizio di recapito globale Widevine dal servizio di licenza Azure Media Services Widevine. Nella tabella seguente viene illustrato il mapping tra i due set di livelli di sicurezza.

| **Livelli di sicurezza definiti nell'architettura Widevine** |**Livelli di sicurezza utilizzati nell'API WidevineSecurity levels used in Widevine API**|
|---|---| 
| **Livello di sicurezza 1:** tutta l'elaborazione, la crittografia e il controllo del contenuto vengono eseguiti all'interno di Trusted Execution Environment (TEE). In alcuni modelli di implementazione, l'elaborazione della sicurezza potrebbe essere eseguita in chip diversi.|**security_level5**: La crittografia, la decodifica e tutta la gestione dei supporti (compressi e non compressi) devono essere gestiti all'interno di un TEE supportato dall'hardware.<br/><br/>**security_level:** la crittografia e la decodifica del contenuto devono essere eseguite all'interno di un TEE supportato dall'hardware.|
**Livello di sicurezza 2**: La crittografia (ma non l'elaborazione video) viene eseguita all'interno del TEE. I buffer decrittografati vengono restituiti al dominio applicazione ed elaborati tramite hardware o software video separato. Al livello 2, tuttavia, le informazioni crittografiche vengono comunque elaborate solo all'interno del TEE.| **security_level3**: Il materiale della chiave e le operazioni crittografiche devono essere eseguite all'interno di un TEE supportato dall'hardware. |
| **Livello di sicurezza 3:** non è presente alcun TEE nel dispositivo. È possibile adottare misure appropriate per proteggere le informazioni crittografiche e decrittografare il contenuto nel sistema operativo host. Un'implementazione di livello 3 può anche includere un motore di crittografia hardware, ma che migliora solo le prestazioni, non la sicurezza. | **security_level2**: Sono necessari software crittografici e un decodificatore offuscato.<br/><br/>**security_level:** è richiesto il crypto white-box basato su software.|

#### <a name="why-does-content-download-take-so-long"></a>Perché il download di contenuto richiede molto tempo?

Per migliorare la velocità di download sono disponibili due diverse strategie:

* Abilitare una rete per la distribuzione di contenuti in modo che gli utenti siano più propensi a raggiungere tale endpoint anziché l'endpoint di origine/streaming per il download del contenuto. Se un utente raggiunge un endpoint di streaming, ogni segmento HLS o dash viene incluso in modo dinamico nel pacchetto e crittografato. Anche se questa latenza è in scala di millisecondi per ogni segmento o frammento, quando si dispone di un video di un'ora, la latenza accumulata può essere grande e causare un download più lungo.
* Offri agli utenti la possibilità di scaricare selettivamente livelli di qualità video e tracce audio invece di tutti i contenuti. Per la modalità offline, non ha senso scaricare tutti i livelli di qualità. A questo scopo è possibile procedere in due modi:

  * Controllo client: l'app lettore seleziona automaticamente, o l'utente seleziona, il livello di qualità video e le tracce audio da scaricare.
  * Controllato dal servizio: è possibile usare la funzionalità Manifesto dinamico in Servizi multimediali di Azure per creare un filtro (globale) che limita la playlist HLS o DASH MPD a un singolo livello di qualità video e alle tracce audio selezionate. Quindi l'URL di download presentato agli utenti includerà questo filtro.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Servizi multimediali v3](media-services-overview.md)
