---
title: Filtri e manifesti dinamici di Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento descrive come creare filtri che il client può usare per trasmettere in streaming sezioni specifiche di un flusso. Servizi multimediali crea manifesti dinamici per consentire questo streaming selettivo.
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
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: ee0200f7c007b437a27b8e9d0f36becc13b8f611
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835810"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>Pre-filtro dei manifesti con creazione dinamica dei pacchetti

Quando si distribuiscono contenuti ai dispositivi di streaming a bitrate adattivo, è spesso necessario pubblicare più versioni del manifesto di funzionalità specifiche del dispositivo di destinazione o larghezza di banda disponibile. Il [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) consente di specificare filtri che è possono escludere specifici codec, risoluzione e velocità in bit audio tenere traccia delle combinazioni su immediatamente eliminando la necessità di creare più copie. È sufficiente pubblicare un nuovo URL con un set specifico di filtri configurati per i dispositivi di destinazione (iOS, Android, SmartTV o browser) e le funzionalità di rete (scenari di larghezza di banda elevata, per dispositivi mobili o larghezza di banda limitata). In questo caso, i client possono modificare lo streaming di contenuti tramite la stringa di query (specificando disponibili [Account filtri o filtri di Asset](filters-concept.md)) e usare i filtri a sezioni specifiche del flusso di un flusso.

Alcuni scenari di distribuzione è necessario che un cliente non è in grado di accedere alle tracce specifiche. Ad esempio, si potrebbe non si desidera pubblicare un manifesto che contiene le tracce HD a un livello determinato sottoscrittore. In alternativa, è possibile rimuovere le tracce specifiche a bitrate adattivo (ABR) per ridurre i costi di consegna a un dispositivo specifico che indicate non traggono vantaggio da altre tracce. In questo caso è possibile associare un elenco dei filtri creati in precedenza con il [localizzatore di Streaming](streaming-locators-concept.md) al momento della creazione. In questo caso, i client non è possibile modificare la modalità con cui viene inviato nel flusso di contenuto, viene definito per il **localizzatore di Streaming**.

È possibile combinare filtri tramite la specifica [filtri sul localizzatore di Streaming](filters-concept.md#associating-filters-with-streaming-locator) + filtri specifici di dispositivo aggiuntivi che il client specifica l'URL. Ciò può essere utile per limitare le tracce aggiuntive come flussi di metadati o un evento, lingue audio o brani audio descrittivi. 

Questa possibilità di specificare filtri diversi sul flusso, fornisce una potente **manifesto dinamico** soluzione manipolazione come destinazione più casi d'uso per i dispositivi di destinazione. Questo argomento illustra i concetti relativi ai **manifesti dinamici** e fornisce alcuni esempi di scenari in cui è possibile usare questa funzionalità.

> [!NOTE]
> I manifesti dinamici non modificano l'asset e il relativo manifesto predefinito. 
> 

##  <a name="overview-of-manifests"></a>Panoramica di manifesti

Servizi multimediali di Azure supporta i protocolli HLS, MPEG DASH e Smooth Streaming. Come parte della [creazione dinamica dei pacchetti](dynamic-packaging-overview.md), i manifesti client streaming (Master HLS Playlist, DASH Media Presentation Description [MPD] e Smooth Streaming) vengono generati dinamicamente in base il selettore di formato nell'URL. Vedere i protocolli di recapito [flusso di lavoro on demand comune](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Ottenere ed esaminare i file manifesto

Si specifica un elenco di condizioni di filtro proprietà track che tiene traccia del flusso (in tempo reale o video su richiesta [VOD]) deve essere inclusi in un manifesto creato dinamicamente in base. Per ottenere ed esaminare le proprietà dei brani, è necessario caricare prima di tutto il manifesto Smooth Streaming.

Il [caricare, codificare e trasmettere i file con .NET](stream-files-tutorial-with-api.md#get-streaming-urls) esercitazione illustra come compilare gli URL di streaming con .NET. Se si esegue l'app, uno degli URL punta al manifesto Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Copiare e incollare l'URL nella barra degli indirizzi del browser. Il file verrà scaricato. Può essere aperto in un editor di testo di propria scelta.

Per un esempio REST, vedere [caricare, codificare e trasmettere i file con REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorare la velocità in bit di un flusso video

È possibile usare la [pagina di prova di Azure Media Player](http://aka.ms/azuremediaplayer) per monitorare la velocità in bit di un flusso video. La pagina di prova consente di visualizzare informazioni di diagnostica sul **diagnostica** scheda:

![Diagnostica di Azure Media Player][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Esempi: URL con i filtri nella stringa di query

È possibile applicare filtri a ABR protocolli di streaming: HLS, MPEG-DASH e Smooth Streaming. Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocol|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtro di rendering

È possibile scegliere di codificare un asset con più profili di codifica (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) e più velocità in bit di qualità. Non tutti i dispositivi, tuttavia, supportano tutti i profili e le velocità in bit dell'asset. Dispositivi Android meno recenti, ad esempio, supportano solo H.264 Baseline + AACL. L'invio di velocità in bit superiore a un dispositivo che non è possibile ottenere i vantaggi comporta uno spreco di larghezza di banda e calcolo. Tale dispositivo deve decodificare tutte le informazioni, solo per la scalabilità verso il basso per la visualizzazione.

Con il manifesto dinamico, è possibile creare i profili di dispositivo (ad esempio dispositivi mobili, console o HD/SD) e includere le tracce e la qualità che si desidera far parte di ogni profilo. Che viene chiamato filtro di rendering. Il diagramma seguente mostra un esempio di esso.

![Esempio di filtro di rendering][renditions2]

Nell'esempio seguente si usa un codificatore per codificare un asset in formato intermedio in sette rendering video ISO MP4 (da 180p a 1080p). L'asset così codificato può essere [in modo dinamico nel pacchetto](dynamic-packaging-overview.md) in nessuno dei protocolli di streaming seguenti: HLS, MPEG DASH e Smooth. 

La parte superiore del diagramma seguente mostra che il manifesto HLS per l'asset senza filtri. (Contiene tutti i sette rendering).  In basso a sinistra, il diagramma illustra un manifesto HLS per cui è stato applicato un filtro denominato "ott". Il filtro "ott" Specifica la rimozione di tutte le velocità in bit inferiore a 1 Mbps, in modo che i due livelli di qualità sono stati rimossi nella risposta. In basso a destra, il diagramma mostra il manifesto HLS per cui è stato applicato un filtro denominato "mobile". Il filtro "mobile" Specifica la rimozione del rendering in cui la risoluzione è superiore a 720p, in modo che i due 1080 trasformate p sono stati rimossi.

![Filtro di rendering][renditions1]

## <a name="removing-language-tracks"></a>Rimozione delle tracce di lingua
Gli asset possono includere più lingue audio, ad esempio inglese, spagnolo, francese e così via. In genere, è l'SDK del lettore a gestire la selezione della traccia audio predefinita e delle tracce audio disponibili per ciascuna selezione utente.

È difficile lo sviluppo di questo tipo di SDK, poiché richiede diverse implementazioni su Framework di lettori di specifiche del dispositivo. Inoltre, in alcune piattaforme, le API del lettore sono limitate e non includono la funzionalità di selezione audio in cui gli utenti non è possibile selezionare o modificare la traccia audio predefinita. Con i filtri di asset, è possibile controllare il comportamento mediante la creazione di filtri che includono solo le lingue audio desiderate.

![Il filtro delle tracce di lingua][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Trimming della parte iniziale di un asset

Nella maggior parte degli eventi in live streaming, gli operatori eseguono alcuni test prima dell'evento effettivo. Prima dell'inizio dell'evento, ad esempio, possono includere uno slate di questo tipo: "Programma sta per iniziare." 

Se il programma prevede l'archiviazione, vengono archiviati e inclusi nella presentazione anche i dati del test e dello slate. Queste informazioni, tuttavia, non risultano visibili ai client. Con il manifesto dinamico, è possibile creare un filtro di ora di inizio e rimuovere dal manifesto tutti i dati non desiderati.

![Trimming della parte iniziale][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Creazione di sottoclip (visualizzazioni) da un archivio live

Molti eventi live hanno una durata molto lunga ed è possibile quindi che un archivio live includa più eventi. Al termine dell'evento live, ad esempio, gli emittenti possono decidere di suddividere l'archivio live in sequenze logiche di avvio e arresto del programma. 

È possibile pubblicare separatamente questi programmi virtuali senza dover post-elaborazione dell'archivio live e creare asset distinti (che non traggono il vantaggio dei frammenti memorizzati nella cache esistenti nel caso di reti CDN). Esempi di programmi virtuali di questo tipo sono, ad esempio, i tempi di una partita di calcio o di basket, gli inning del baseball o eventi singoli di un qualsiasi programma sportivo.

Con il manifesto dinamico, è possibile creare filtri con ora di inizio/fine e creare visualizzazioni virtuali nella parte superiore dell'archivio live. 

![Filtro di sottoclip][subclip_filter]

Ecco l'asset filtrato:

![Sci][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Regolazione finestra presentazione (DVR)

Attualmente, servizi multimediali di Azure offre un archivio circolare in cui la durata può essere configurata tra 5 minuti e 25 ore. Usando i filtri del file manifesto, è possibile creare una finestra DVR in sequenza all'inizio dell'archivio, senza dover eliminare alcun contenuto. Sono molti i casi in cui per un emittente può essere utile creare una finestra DVR limitata, in grado di spostarsi con il margine live, e al tempo stesso mantenere una finestra di archiviazione più grande. Un emittente, ad esempio, può decidere di usare i dati esterni alla finestra DVR per evidenziare clip oppure fornire finestre DVR diverse per dispositivi diversi. Ad esempio, la maggior parte dei dispositivi mobili non gestire finestre DVR di grandi dimensioni (è possibile avere una finestra DVR di 2 minuti per i dispositivi mobili e 1 ora per i client desktop).

![Finestra DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Regolazione LiveBackoff (posizione live)

È possibile usare i filtri del file manifesto anche per rimuovere alcuni secondi dal margine live di un programma live. Filtri gli emittenti possono guardare la presentazione nel punto di pubblicazione di anteprima e creare punti di inserimento di annunci prima che i destinatari ricevano il flusso (supportato da 30 secondi). Gli emittenti possono quindi inserire questi annunci nel proprio framework client in tempo per poter ricevere ed elaborare le informazioni prima dell'opportunità di annuncio.

Oltre al supporto degli annunci, l'impostazione di Backoff in tempo reale è utilizzabile per regolare la posizione dei visualizzatori in modo che quando i client deviano e raggiungono il margine live, frammenti, possono comunque ottenere dal server. In questo modo, i client non otterranno un errore HTTP 404 o errore 412.

![Filtro per eseguire il backoff in tempo reale][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinazione di più regole in un unico filtro

È possibile combinare più regole di filtro in un unico filtro. È ad esempio possibile definire una "regola di intervallo" per rimuovere slate da un archivio live e applicare un filtro alle velocità in bit disponibili. Quando si applica più regole di filtro, il risultato finale è l'intersezione di tutte le regole.

![Più regole di filtro][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinazione di più filtri (filtro composizione)

È inoltre possibile combinare più filtri in un singolo URL. Lo scenario seguente illustra il motivo per cui è possibile combinare filtri:

1. È necessario filtrare le qualità video per i dispositivi mobili, ad esempio Android o iPad (per limitare le qualità video). Per rimuovere le qualità indesiderate, si creerà un filtro di account adatto per i profili di dispositivo. È possibile usare i filtri di account per tutti gli asset con lo stesso account di servizi multimediali senza altre associazioni.
1. Inoltre si desidera tagliare l'ora di inizio e fine di un asset. A tale scopo, si verrà creato un filtro di asset e impostare l'ora di inizio/fine. 
1. Si desidera combinare entrambi questi filtri. Senza combinazione, devi aggiungere un filtro qualità al filtro di taglio, che renderebbe più difficile l'utilizzo del filtro.


Per combinare i filtri, è necessario impostare i nomi dei filtri per il manifesto/playlist URL nel formato delimitato da punto e virgola. Si supponga che si dispone di un filtro denominato *MyMobileDevice* che filtra le qualità e si dispone di un altro denominato *MyStartTime* per impostare una specifica ora di inizio. È possibile combinare fino a tre filtri. 

Per altre informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Considerazioni e limiti

- I valori per **forceEndTimestamp**, **presentationWindowDuration**, e **liveBackoffDuration** non deve essere impostato per un filtro VOD. E vengono usati solo per gli scenari di filtro in tempo reale. 
-  Un manifesto dinamico opera nei limiti GOP (fotogrammi chiave), quindi, trimming ha la precisione del GOP.
-  È possibile usare lo stesso nome di filtro per i filtri di asset e account. Filtri di asset hanno la precedenza e sostituiranno i filtri di account.
- Se si aggiorna un filtro, possono volerci fino a 2 minuti per l'endpoint di streaming aggiornare le regole. Se sono stati usati i filtri per rendere disponibile il contenuto ed è memorizzato nella cache il contenuto di proxy e memorizza nella cache della rete CDN, l'aggiornamento di questi filtri può causare errori di Windows Media player. Si consiglia di cancellare la cache dopo aver aggiornato il filtro. Se questa opzione non è possibile, è consigliabile usare un filtro diverso.
- I clienti devono scaricare il manifesto e analizzare il timestamp di inizio e scala cronologica manualmente.
    
    - Per determinare le proprietà delle tracce in un asset [ottenere ed esaminare il file manifesto](#get-and-examine-manifest-files).
    - La formula per impostare le proprietà di data e ora filtro di asset è: <br/>startTimestamp = &lt;ora di inizio nel manifesto&gt; +  &lt;previsto ora di inizio filtro in pochi secondi&gt; * scala cronologica

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti illustrano come creare filtri a livello di codice:  

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
