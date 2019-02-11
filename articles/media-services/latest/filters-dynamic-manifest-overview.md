---
title: Filtri e manifesti dinamici di Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento descrive come creare filtri che il client può usare per trasmettere in streaming sezioni specifiche di un flusso. Servizi multimediali crea manifesti dinamici per archiviare questo streaming selettivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.openlocfilehash: 9c463740acf6ef464880a43e0e68de683b97f64f
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813420"
---
# <a name="filters-and-dynamic-manifests"></a>Filtri e manifesti dinamici

Quando si distribuiscono contenuti ai clienti (flusso di eventi dal vivo o di video on demand) il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. 

I filtri sono regole lato server che consentono ai clienti di eseguire operazioni come: 

- Riprodurre una sola sezione di un video (anziché il video intero). Ad esempio: 

    - Ridurre il manifesto in modo da mostrare solo una clip secondaria di un evento live ("filtro di clip secondaria”) o
    - Tagliare l'inizio di un video ("trimming di un video").

- Distribuire solo i rendering specificati e/o le tracce di lingua specificate, se supportate dal dispositivo usato per la riproduzione dei contenuti ("filtro di rendering"). 
- Regolare la finestra di presentazione (DVR) in modo da ottenere una lunghezza limitata della finestra nel lettore ("regolazione finestra presentazione").

Questo argomento descrive i [concetti](#concepts) e illustra le definizioni dei filtri. Fornisce inoltre dettagli sugli scenari comuni. Alla fine dell'articolo, sono disponibili collegamenti che spiegano come creare filtri in modo programmatico.  

## <a name="concepts"></a>Concetti

### <a name="dynamic-manifests"></a>Manifesti dinamici

Servizi multimediali offre **manifesti dinamici** basati su filtri predefiniti. Dopo aver definito i filtri, i client possono usarli per trasmettere un determinato rendering o clip secondarie di un video. I filtri desiderati dovranno essere specificati nell'URL di streaming. È possibile applicare filtri ai protocolli di streaming a bitrate adattivo: Apple HLS (HTTP Live Streaming), MPEG-DASH e Smooth Streaming. 

Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocollo|Esempio|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

> [!NOTE]
> I manifesti dinamici non modificano l'asset e il relativo manifesto predefinito. È possibile decidere di richiedere un flusso con o senza filtri. 
> 
> 

### <a name="manifest-files"></a>File manifesto

Quando si codifica un asset per lo streaming a velocità in bit adattiva, viene creato un file (playlist) **manifesto** , ovvero un file basato su testo o XML. Il file **manifesto** include alcuni metadati di streaming, tra cui il tipo di traccia (audio, video o testo), il nome della traccia, l'ora di inizio e di fine, la velocità in bit (qualità), le lingue della traccia, la finestra di presentazione (finestra scorrevole di durata fissa) e un codec video (FourCC). Indica inoltre al lettore come recuperare il frammento successivo fornendo informazioni sui successivi frammenti video riproducibili disponibili e il relativo percorso. I frammenti (o segmenti) sono i "blocchi" effettivi di un contenuto video.

Di seguito è riportato un esempio di file manifesto HLS: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>Ottenere ed esaminare i file manifesto

Specificare un elenco di condizioni delle proprietà dei brani in base alle quali i brani del flusso (live o video on demand) devono essere inclusi nel manifesto creato dinamicamente. Per ottenere ed esaminare le proprietà dei brani, è necessario caricare prima di tutto il manifesto Smooth Streaming.

L’esercitazione [Caricare, codificare e trasmettere i file con .NET](stream-files-tutorial-with-api.md) spiega come compilare gli URL di streaming con .NET (vedere la sezione [Creazione di URL](stream-files-tutorial-with-api.md#get-streaming-urls)). Se si esegue l'app, uno degli URL punta al manifesto Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Copiare e incollare l'URL nella barra degli indirizzi del browser. Il file verrà scaricato. Può essere aperto in un editor di testo di propria scelta.

Per l'esempio REST, vedere [Caricare, codificare ed eseguire lo streaming dei file con REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorare la velocità in bit di un flusso video

È possibile usare la [pagina di prova di Azure Media Player](http://aka.ms/amp) per monitorare la velocità in bit di un flusso video. La pagina di prova mostra le informazioni di diagnostica nella scheda **Diagnostica**:

![Diagnostica di Azure Media Player][amp_diagnostics]

## <a name="defining-filters"></a>Definizione di filtri

Sono disponibili due tipi di filtri di asset: 

* [Filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) (globali): possono essere applicati a qualsiasi asset nell’account di Servizi multimediali di Azure e hanno una durata equivalente a quella dell’account.
* [Filtri asset](https://docs.microsoft.com/rest/api/media/assetfilters) (locali): possono essere applicati solo a uno degli asset a cui è stato associato il filtro in fase di creazione e hanno una durata equivalente a quella dell’asset. 

I [filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) e i [filtri Asset](https://docs.microsoft.com/rest/api/media/assetfilters) hanno esattamente le stesse proprietà per la definizione/descrizione del filtro. Tranne quando si crea il **filtro asset**, è necessario specificare il nome dell'asset a cui si desidera associare il filtro.

A seconda dello scenario, decidere quale tipo di filtro è più adatto (asset o account). I filtri asset sono più adatti per i profili di dispositivo (filtro di rendering), mentre i filtri asset possono essere usati per tagliare un asset specifico.

Utilizzare le seguenti proprietà per descrivere i filtri. 

|NOME|DESCRIZIONE|
|---|---|
|firstQuality|La prima velocità in bit di qualità del filtro.|
|presentationTimeRange|L’intervallo di tempo di presentazione. Questa proprietà viene utilizzata per filtrare i punti di inizio/fine del manifesto, la lunghezza della finestra di presentazione e la posizione iniziale live. <br/>Per altre informazioni, vedere [Intervallo di tempo di presentazione](#PresentationTimeRange).|
|brani|Le condizioni di selezione dei brani. Per ulteriori informazioni, vedere [brani](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Utilizzare questa proprietà con i **filtri asset**. Non è consigliabile impostare la proprietà per i **filtri account**.

|NOME|DESCRIZIONE|
|---|---|
|**endTimestamp**|Il limite di tempo finale assoluto. È adatto per i video on demand (VoD). Per la presentazione live, viene automaticamente ignorato e applicato al termine della presentazione, quando il flusso diventa VoD.<br/><br/>Il valore rappresenta un punto finale assoluto del flusso. Viene arrotondato all'inizio GOP successivo più vicino.<br/><br/>Utilizzare StartTimestamp ed EndTimestamp per tagliare la playlist (manifesto). Ad esempio, se StartTimestamp = 40000000 ed EndTimestamp = 100000000, verrà generata una playlist che contiene elementi multimediali tra StartTimestamp ed EndTimestamp. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.<br/><br/>Vedere anche la seguente definizione di **forceEndTimestamp**.|
|**forceEndTimestamp**|Si applica ai filtri live.<br/><br/>**forceEndTimestamp** è un valore booleano che indica se **endTimestamp** è stato impostato su un valore valido o meno. <br/><br/>Se il valore è **true**, il valore **endTimestamp** deve essere specificato. Se non è specificato, viene restituita una richiesta non valida.<br/><br/>Se, ad esempio, si vuole definire un filtro che inizia in corrispondenza di 5 minuti nel video di input e viene mantenuto fino alla fine del flusso, bisogna impostare **forceEndTimestamp** su false e omettere l'impostazione **endTimestamp**.|
|**liveBackoffDuration**|Si applica solo a contenuti live. La proprietà viene utilizzata per definire la posizione della riproduzione live. Con questa regola, è possibile ritardare la posizione di riproduzione live e creare un buffer lato server per i lettori. LiveBackoffDuration dipende dalla posizione live. La durata massima di backoff live è di 300 secondi.|
|**presentationWindowDuration**|Si applica ai contenuti live. Usare **presentationWindowDuration** per applicare una finestra temporale scorrevole alla playlist. Ad esempio, impostare presentationWindowDuration = 1200000000 per applicare una finestra temporale scorrevole di due minuti. Nella playlist verranno inclusi i contenuti multimediali che rientrano nei 2 minuti dell’arco live. Se un frammento attraversa il limite, l'intero frammento verrà incluso nella playlist. La durata minima della finestra di presentazione è di 60 secondi.|
|**startTimestamp**|Si applica ai flussi live o VoD. Il valore rappresenta un punto iniziale assoluto del flusso. Viene arrotondato all'inizio GOP successivo più vicino.<br/><br/>Utilizzare **startTimestamp** e **endTimestamp** per tagliare la playlist (manifesto). Ad esempio, se startTimestamp = 40000000 ed endTimestamp = 100000000, verrà generata una playlist che contiene elementi multimediali tra StartTimestamp ed EndTimestamp. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.|
|**timescale**|Si applica ai flussi live o VoD. La scala cronologica usata dai timestamp e dalle durate specificate in precedenza. La scala cronologica predefinita è 10000000. È possibile usare una scala cronologica diversa. Il valore predefinito è 10000000 HNS (centinaia di nanosecondi).|

### <a name="tracks"></a>Brani

Specificare un elenco di condizioni delle proprietà dei brani (FilterTrackPropertyConditions) in base alle quali i brani del flusso (live o video on demand) devono essere inclusi nel manifesto creato dinamicamente. I filtri vengono combinati usando gli operatori logici **AND** e **O**.

Le condizioni delle proprietà di filtro indicano i tipi di brano, i valori (descritti nella tabella seguente) e le operazioni (Uguale, DiversoDa). 

|NOME|DESCRIZIONE|
|---|---|
|**Bitrate**|Usare la velocità in bit del brano per il filtro.<br/><br/>Il valore consigliato è un intervallo di velocità in bit, in bit al secondo. Ad esempio, "0-2427000".<br/><br/>Nota: anche se è possibile usare un valore di velocità in bit specifico, ad esempio 250000 (bit al secondo), questo approccio è sconsigliato, perché la velocità in bit esatta può variare da un asset a un altro.|
|**FourCC**|Usare il valore FourCC del brano come filtro.<br/><br/>Il valore è il primo elemento di formato codec, come specificato in [RFC 6381](https://tools.ietf.org/html/rfc6381). Attualmente sono supportati i seguenti codec: <br/>Per i video: "avc1", "hev1", "hvc1"<br/>Per l’audio: "mp4a", "ec-3"<br/><br/>Per determinare i valori di FourCC per i brani in un asset, [ottenere ed esaminare il file manifesto](#get-and-examine-manifest-files).|
|**Lingua**|Usare la lingua del brano come filtro.<br/><br/>Il valore è il tag di una lingua che si desidera includere, come specificato in RFC 5646. Ad esempio, "en".|
|**Nome**|Usare il nome del brano come filtro.|
|**Tipo**|Usare il tipo di brano come filtro.<br/><br/>Sono consentiti i seguenti valori: "video", "audio" o "text".|

### <a name="example"></a>Esempio

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="rendition-filtering"></a>Filtro di rendering

È possibile scegliere di codificare un asset con più profili di codifica (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) e più velocità in bit di qualità. Non tutti i dispositivi, tuttavia, supportano tutti i profili e le velocità in bit dell'asset. Solo i dispositivi Android di precedente generazione, ad esempio, supportano il profilo H.264 Baseline+AACL. La trasmissione di contenuti a un dispositivo con una velocità in bit superiore di cui non può trarre vantaggio, inoltre, comporta uno spreco di larghezza di banda e di capacità di elaborazione. Per poter visualizzare i contenuti trasmessi, infatti, il dispositivo deve decodificare e ridimensionare tutte le informazioni trasmesse.

Con il manifesto dinamico, è possibile creare profili di dispositivo, ad esempio mobile, console, HD/SD e così via, e includere le tracce e la qualità desiderate per ogni profilo.

![Esempio di filtro di rendering][renditions2]

Nell'esempio seguente si usa un codificatore per codificare un asset in formato intermedio in sette rendering video ISO MP4 (da 180p a 1080p). L'asset così codificato può essere quindi riorganizzato dinamicamente in pacchetti creati con uno dei seguenti protocolli di streaming: HLS, MPEG DASH e Smooth.  Nella parte superiore del diagramma è riportato il manifesto HLS per l'asset senza filtri (contiene tutti i sette rendering),  mentre in basso a sinistra è riportato il manifesto HLS con applicato un filtro denominato "ott". Il filtro "ott" indica che devono essere rimosse tutte le velocità in bit inferiori a 1 Mbps. L'applicazione di questo filtro ha generato i due livelli di qualità inferiori rimossi nella risposta. In basso a destra è riportato invece il manifesto HLS con applicato un filtro denominato "mobile". Il filtro "mobile" indica che devono essere rimossi tutti i rendering con risoluzione superiore a 720p. L'applicazione di questo filtro ha generato i due rendering 1080p rimossi.

![Filtro di rendering][renditions1]

## <a name="removing-language-tracks"></a>Rimozione delle tracce di lingua
Gli asset possono includere più lingue audio, ad esempio inglese, spagnolo, francese e così via. In genere, è l'SDK del lettore a gestire la selezione della traccia audio predefinita e delle tracce audio disponibili per ciascuna selezione utente. Sviluppare questo tipo di SDK, tuttavia, è particolarmente difficile poiché richiede diverse implementazioni nel Player Framework specifico di ogni dispositivo. In alcune piattaforme, inoltre, le API del lettore offrono funzionalità limitate (ad esempio non includono funzioni di selezione audio) e non consentono quindi agli utenti di selezionare o modificare la traccia audio predefinita. Con i filtri di asset, è possibile controllare il comportamento mediante la creazione di filtri che includono solo le lingue audio desiderate.

![Filtro delle tracce di lingua][language_filter]

## <a name="trimming-start-of-an-asset"></a>Trimming della parte iniziale di un asset
Nella maggior parte degli eventi in live streaming, gli operatori eseguono alcuni test prima dell'evento effettivo. Prima dell'inizio dell'evento, ad esempio, possono includere uno slate di questo tipo: "Il programma sta per iniziare". Se il programma prevede l'archiviazione, vengono archiviati e inclusi nella presentazione anche i dati del test e dello slate. Queste informazioni, tuttavia, non risultano visibili ai client. Con il manifesto dinamico, è possibile creare un filtro di ora di inizio e rimuovere dal manifesto tutti i dati non desiderati.

![Trimming della parte iniziale][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Creazione di sottoclip (visualizzazioni) da un archivio live
Molti eventi live hanno una durata molto lunga ed è possibile quindi che un archivio live includa più eventi. Al termine dell'evento live, ad esempio, gli emittenti possono decidere di suddividere l'archivio live in sequenze logiche di avvio e arresto del programma. Possono quindi pubblicare separatamente questi programmi virtuali, senza dover eseguire la post-elaborazione dell'archivio live e creare asset distinti, che non traggono vantaggio dai frammenti presenti nella cache in caso di reti per la distribuzione di contenuti. Esempi di programmi virtuali di questo tipo sono, ad esempio, i tempi di una partita di calcio o di basket, gli inning del baseball o eventi singoli di un qualsiasi programma sportivo.

Con il manifesto dinamico, è possibile creare filtri basati sulle ore di inizio/fine e creare visualizzazioni virtuali all'inizio dell'archivio live. 

![Filtro di sottoclip][subclip_filter]

Asset filtrato:

![Sci][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Regolazione della finestra di presentazione (DVR)
Attualmente, Servizi multimediali di Azure offre un archivio circolare la cui durata può essere configurata tra 5 minuti e 25 ore. Usando i filtri del file manifesto, è possibile creare una finestra DVR in sequenza all'inizio dell'archivio, senza dover eliminare alcun contenuto. Sono molti i casi in cui per un emittente può essere utile creare una finestra DVR limitata, in grado di spostarsi con il margine live, e al tempo stesso mantenere una finestra di archiviazione più grande. Un emittente, ad esempio, può decidere di usare i dati esterni alla finestra DVR per evidenziare clip oppure fornire finestre DVR diverse per dispositivi diversi. La maggior parte dei dispositivi mobili, ad esempio, non è in grado di gestire finestre DVR di grandi dimensioni (è possibile usufruire di una finestra DVR di 2 minuti per i dispositivi mobili e di un'ora per i client desktop).

![Finestra DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Regolazione LiveBackoff (posizione live)
È possibile usare i filtri del file manifesto anche per rimuovere alcuni secondi dal margine live di un programma live. Con i filtri gli emittenti possono guardare la presentazione nel punto di pubblicazione di anteprima e creare punti di inserimento di annunci prima che i destinatari ricevano il flusso (ritardato di 30 secondi). Gli emittenti possono quindi inserire questi annunci nel proprio Framework Client in tempo per poter ricevere ed elaborare le informazioni prima dell'opportunità di annuncio.

Oltre a fornire il supporto per gli annunci pubblicitari, l'impostazione LiveBackoff consente anche di regolare la posizione dei visualizzatori in modo che, nel momento in cui i client deviano e raggiungono il margine live, possono comunque ottenere i frammenti dal server, anziché un errore HTTP 404 o 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinazione di più regole in un unico filtro
È possibile combinare più regole di filtro in un unico filtro. È ad esempio possibile definire una "regola di intervallo" per rimuovere slate da un archivio live e applicare un filtro alle velocità in bit disponibili. Se si applicano più regole di filtro, il risultato finale è l'intersezione di tutte le regole.

![multiple-rules][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinazione di più filtri (filtro composizione)

È inoltre possibile combinare più filtri in un singolo URL. 

Lo scenario seguente illustra il motivo per cui è possibile combinare filtri:

1. È necessario filtrare le qualità video per dispositivi mobili, come ad esempio  Android o iPad (per limitare le qualità video). Per rimuovere le qualità indesiderate, creare un filtro account adatto per i profili del dispositivo. I filtri account possono essere utilizzati per tutti gli asset con lo stesso account di servizi multimediali senza altre associazioni. 
2. Inoltre si desidera tagliare l'ora di inizio e fine di un asset. A tale scopo, è necessario creare un filtro asset e impostare l'ora di inizio e di fine. 
3. È necessario combinare entrambi questi filtri, in caso contrario è necessario aggiungere un filtro qualità al filtro di taglio, il che rende più difficile l'utilizzo del filtro.

Per combinare i filtri, è necessario impostare i nomi dei filtri per il manifesto/playlist URL delimitati dal punto e virgola. Si supponga di disporre di un filtro denominato *MyMobileDevice* che filtra le qualità e di un altro filtro denominato *MyStartTime* per impostare una specifica ora di inizio. È possibile combinarli nel seguente modo:

È possibile combinare fino a tre filtri. 

Per altre informazioni, vedere [questo](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="considerations-and-limitations"></a>Considerazioni e limiti

- I valori di **forceEndTimestamp**, **presentationWindowDuration** e **liveBackoffDuration** non devono essere impostati per un filtro VoD. Vengono usati solo per gli scenari di filtro live. 
- Il manifesto dinamico opera nei limiti dell'intervallo GOP (fotogrammi chiave) e, pertanto, il trimming eredita la precisione del GOP. 
- È possibile usare lo stesso nome di filtro per i filtri account e asset. I filtri asset, tuttavia, hanno la precedenza e sovrascrivono quindi i filtri account.
- Se si aggiorna un filtro, l'endpoint di streaming può impiegare fino a due minuti per aggiornare le regole. Se il contenuto è stato trasmesso usando dei filtri (e memorizzato nelle cache dei proxy e delle reti CDN), l'aggiornamento dei filtri può determinare un errore del lettore. È consigliabile cancellare la cache dopo aver aggiornato il filtro. Se questa operazione non è consentita, prendere in considerazione la possibilità di usare un filtro diverso.
- I clienti devono scaricare manualmente il manifesto e analizzare il startTimestamp esatto e la scala cronologica.
    
    - Per determinare le proprietà dei brani in un asset, [ottenere ed esaminare il file manifesto](#get-and-examine-manifest-files).
    - Formula per impostare le proprietà del timestamp di filtro dell’asset: <br/>startTimestamp = &lt;ora di inizio nel manifesto&gt; +  &lt;ora di inizio del filtro prevista in secondi&gt;*scala cronologica


## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti spiegano come creare filtri in modo programmatico.  

- [Creare filtri con le API REST](filters-dynamic-manifest-rest-howto.md)
- [Creare filtri con .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Creare filtri con l’interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
