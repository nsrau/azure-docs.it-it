---
title: Filtrare i manifesti usando Dynamic PackagerFilter your manifests using Dynamic Packager
titleSuffix: Azure Media Services
description: Scopri come creare filtri usando Dynamic Packager per filtrare e trasmettere in modo selettivo i tuoi manifesti.
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
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186222"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtrare i manifesti usando Dynamic PackagerFilter your manifests using Dynamic Packager

Quando distribuisci contenuti di streaming a bitrate adattivo ai dispositivi, a volte devi pubblicare più versioni di un manifesto per scegliere come destinazione funzionalità specifiche del dispositivo o larghezza di banda di rete disponibile. [Il Dynamic Packager](dynamic-packaging-overview.md) consente di specificare filtri che possono filtrare durante codec, risoluzioni, bitrate e combinazioni di tracce audio in tempo reale. Questo filtro elimina la necessità di creare più copie. È sufficiente pubblicare un nuovo URL con un set specifico di filtri configurati per i dispositivi di destinazione (iOS, Android, SmartTV o browser) e le funzionalità di rete (scenari con larghezza di banda elevata, mobile o a larghezza di banda ridotta). In questo caso, i client possono modificare lo streaming del contenuto tramite la stringa di query (specificando i [filtri Asset disponibili o i filtri Account)](filters-concept.md)e utilizzare i filtri per trasmettere sezioni specifiche di un flusso.

Alcuni scenari di consegna richiedono l'assicurato che un cliente non possa accedere a tracce specifiche. Ad esempio, è possibile che non si desideri pubblicare un manifesto che contiene tracce HD in un livello di sottoscrittore specifico. Oppure, forse si desidera rimuovere specifiche tracce bitrate adattivo (ABR) per ridurre il costo di consegna a un dispositivo specifico che non trarrebbe vantaggio dalle tracce aggiuntive. In questo caso, è possibile associare un elenco di filtri creati in precedenza al [localizzatore](streaming-locators-concept.md) di streaming al creazione. I client non possono quindi modificare la modalità di streaming del contenuto perché è definito da **Streaming Locator**.

È possibile combinare il filtro specificando [i filtri in Streaming Locator,](filters-concept.md#associating-filters-with-streaming-locator) ovvero filtri aggiuntivi specifici del dispositivo specificati dal client nell'URL. Questa combinazione è utile per limitare tracce aggiuntive come metadati o flussi di eventi, lingue audio o tracce audio descrittive.

Questa possibilità di specificare filtri diversi nel flusso fornisce una potente soluzione di manipolazione **del manifesto dinamico** per indirizzare più scenari di casi d'uso per i dispositivi di destinazione. In questo argomento vengono illustrati i concetti relativi **ai manifesti dinamici** e vengono forniti esempi di scenari in cui è possibile utilizzare questa funzionalità.

> [!NOTE]
> I manifesti dinamici non modificano l'asset e il manifesto predefinito per tale asset.

## <a name="overview-of-manifests"></a>Panoramica dei manifesti

Servizi multimediali di Azure supporta i protocolli HLS, MPEG DASH e Smooth Streaming. Come parte del pacchetto [dinamico](dynamic-packaging-overview.md), i manifesti del client di streaming (HLS Master Playlist, DASH Media Presentation Description [MPD] e Smooth Streaming) vengono generati dinamicamente in base al selettore di formato nell'URL. Per ulteriori informazioni, vedere i protocolli di recapito in Flusso di lavoro [su richiesta comune](dynamic-packaging-overview.md#delivery-protocols).

### <a name="get-and-examine-manifest-files"></a>Ottenere ed esaminare i file manifesto

Devi specificare un elenco di condizioni delle proprietà delle tracce di filtro in base alle tracce dello streaming (live o video on-demand [VOD]) da includere in un manifesto creato dinamicamente. Per ottenere ed esaminare le proprietà dei brani, è necessario caricare prima di tutto il manifesto Smooth Streaming.

L'esercitazione [Caricare, codificare e trasmettere file con .NET](stream-files-tutorial-with-api.md#get-streaming-urls) illustra come compilare gli URL di streaming con .NET. Se si esegue l'app, uno degli URL punta al manifesto Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Copiare e incollare l'URL nella barra degli indirizzi del browser. Il file verrà scaricato. È possibile aprirlo in qualsiasi editor di testo.

Per un esempio REST, vedere [Caricare, codificare e trasmettere file con REST.](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorare la velocità in bit di un flusso video

È possibile usare la [pagina di prova di Azure Media Player](https://aka.ms/azuremediaplayer) per monitorare la velocità in bit di un flusso video. La pagina demo visualizza le informazioni di diagnostica nella scheda **Diagnostica:The** demo page displays diagnostics info on the Diagnostics tab:

![Diagnostica di Azure Media Player][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Esempi: URL con filtri nella stringa di queryExamples: URLs with filters in query string

È possibile applicare filtri ai protocolli di streaming ABR: HLS, MPEG-DASH e Smooth Streaming. Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocollo|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtro di rendering

È possibile scegliere di codificare l'asset in più profili di codifica (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) e più velocità in bit di qualità. Non tutti i dispositivi, tuttavia, supportano tutti i profili e le velocità in bit dell'asset. Ad esempio, i dispositivi Android meno recenti supportano solo H.264 Baseline-AACL. L'invio di bitrate più elevate a un dispositivo che non può ottenere i vantaggi spreca larghezza di banda e calcolo del dispositivo. Tale dispositivo deve decodificare tutte le informazioni fornite, solo per scalare verso il basso per la visualizzazione.

Con Dynamic Manifest, è possibile creare profili di dispositivo (ad esempio mobile, console o HD/SD) e includere le tracce e le qualità che si desidera far parte di ogni profilo. Questo si chiama filtro delle consegne. Il diagramma seguente mostra un esempio di esso.

![Esempio di filtro delle rendering con manifesto dinamico][renditions2]

Nell'esempio seguente si usa un codificatore per codificare un asset in formato intermedio in sette rendering video ISO MP4 (da 180p a 1080p). L'asset codificato può essere [incluso dinamicamente](dynamic-packaging-overview.md) in uno dei seguenti protocolli di streaming: HLS, MPEG DASH e Smooth.

Nella parte superiore del diagramma seguente viene illustrato il manifesto HLS per l'asset senza filtri. (Contiene tutte e sette le copie trasformate.)  In basso a sinistra, il diagramma mostra un manifesto HLS a cui è stato applicato un filtro denominato "ott". Il filtro "ott" specifica la rimozione di tutti i bitrate al di sotto di 1 Mbps, quindi i due livelli di qualità inferiori sono stati rimossi nella risposta. In basso a destra, il diagramma mostra il manifesto HLS a cui è stato applicato un filtro denominato "mobile". Il filtro "mobile" specifica la rimozione delle copie trasformate in cui la risoluzione è maggiore di 720p, pertanto le due copie 1080p sono state rimosse.

![Filtro delle copie trasformate con manifesto dinamico][renditions1]

## <a name="removing-language-tracks"></a>Rimozione delle tracce di lingua
Le risorse potrebbero includere più lingue audio, ad esempio inglese, spagnolo, francese e così via. In genere, player SDK gestisce la selezione predefinita delle tracce audio e le tracce audio disponibili per ogni selezione utente.

Lo sviluppo di tali SDK per giocatori è impegnativo perché richiede implementazioni diverse tra i framework dei giocatori specifici del dispositivo. Inoltre, su alcune piattaforme, le API del lettore sono limitate e non includono la funzione di selezione audio in cui gli utenti non possono selezionare o modificare la traccia audio predefinita. Con i filtri delle risorse, puoi controllare il comportamento creando filtri che includono solo le lingue audio desiderate.

![Filtraggio delle tracce linguistiche con Dynamic Manifest][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Taglio dell'inizio di un cespite

Nella maggior parte degli eventi in live streaming, gli operatori eseguono alcuni test prima dell'evento effettivo. Ad esempio, potrebbero includere una lavagna come questa prima dell'inizio dell'evento: "Il programma inizierà momentaneamente".

Se il programma prevede l'archiviazione, vengono archiviati e inclusi nella presentazione anche i dati del test e dello slate. Tuttavia, queste informazioni non devono essere visualizzate ai client. Con il manifesto dinamico, è possibile creare un filtro di ora di inizio e rimuovere dal manifesto tutti i dati non desiderati.

![Taglio dell'inizio di un asset con Manifesto dinamico][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Creazione di sottoclip (visualizzazioni) da un archivio live

Molti eventi live hanno una durata molto lunga ed è possibile quindi che un archivio live includa più eventi. Al termine dell'evento live, ad esempio, gli emittenti possono decidere di suddividere l'archivio live in sequenze logiche di avvio e arresto del programma.

È possibile pubblicare questi programmi virtuali separatamente senza post-elaborare l'archivio live e non creare risorse separate (che non ottiene il vantaggio dei frammenti memorizzati nella cache esistenti nelle reti CDN). Esempi di programmi virtuali di questo tipo sono, ad esempio, i tempi di una partita di calcio o di basket, gli inning del baseball o eventi singoli di un qualsiasi programma sportivo.

Con Il manifesto dinamico, puoi creare filtri usando l'ora di inizio/fine e creare viste virtuali sopra l'archivio live.

![Filtro clip secondaria con manifesto dinamicoSubclip filter with Dynamic Manifest][subclip_filter]

Ecco la risorsa filtrata:

![Risorsa filtrata con manifesto dinamicoFiltered asset with Dynamic Manifest][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Regolazione della finestra di presentazione (DVR)

Attualmente, Servizi multimediali di Azure offre un archivio circolare in cui la durata può essere configurata tra 1 minuto e 25 ore. Usando i filtri del file manifesto, è possibile creare una finestra DVR in sequenza all'inizio dell'archivio, senza dover eliminare alcun contenuto. Sono molti i casi in cui per un emittente può essere utile creare una finestra DVR limitata, in grado di spostarsi con il margine live, e al tempo stesso mantenere una finestra di archiviazione più grande. Un'emittente potrebbe voler utilizzare i dati esterni alla finestra DVR per evidenziare le clip o fornire finestre DVR diverse per dispositivi diversi. La maggior parte dei dispositivi mobili, ad esempio, non è in grado di gestire finestre DVR di grandi dimensioni (è possibile usufruire di una finestra DVR di 2 minuti per i dispositivi mobili e di un'ora per i client desktop).

![Finestra DVR con manifesto dinamico][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Regolazione LiveBackoff (posizione live)

È possibile usare i filtri del file manifesto anche per rimuovere alcuni secondi dal margine live di un programma live. Il filtro consente alle emittenti di guardare la presentazione nel punto di pubblicazione dell'anteprima e di creare punti di inserimento pubblicitari prima che gli spettatori ricevano lo streaming (sostenuto di 30 secondi). Le emittenti possono quindi inviare questi annunci ai propri framework client in tempo per ricevere ed elaborare le informazioni prima dell'opportunità di annuncio.

Oltre al supporto pubblicitario, l'impostazione di back-off dal vivo può essere utilizzata per regolare la posizione degli spettatori in modo che quando i clienti si spostano e colpiscono il bordo dal vivo, possano ancora ottenere frammenti dal server. In questo modo, i client non otterranno un errore HTTP 404 o 412.

![Filtrare per il back-off attivo con manifesto dinamicoFilter for live back-off with Dynamic Manifest][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinazione di più regole in un unico filtro

È possibile combinare più regole di filtro in un unico filtro. Ad esempio, è possibile definire una "regola di intervallo" per rimuovere le lavagne da un archivio live e anche filtrare i bitrate disponibili. Quando si applicano più regole di filtro, il risultato finale è l'intersezione di tutte le regole.

![Più regole di filtro con manifesto dinamicoMultiple filtering rules with Dynamic Manifest][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinazione di più filtri (filtro composizione)

È inoltre possibile combinare più filtri in un singolo URL. Lo scenario seguente illustra il motivo per cui è possibile combinare filtri:

1. Devi filtrare le qualità video per i dispositivi mobili, come Android o iPad (al fine di limitare le qualità video). Per rimuovere le qualità indesiderate, si creerà un filtro account adatto per i profili del dispositivo. È possibile utilizzare i filtri dell'account per tutte le risorse con lo stesso account di Servizi multimediali senza ulteriori associazioni.
1. Inoltre si desidera tagliare l'ora di inizio e fine di un asset. Per eseguire la riduzione, devi creare un filtro risorse e impostare l'ora di inizio/fine.
1. Si desidera combinare entrambi questi filtri. Senza combinazione, è necessario aggiungere un filtro qualità al filtro di taglio, il che renderebbe più difficile l'utilizzo del filtro.

Per combinare i filtri, impostare i nomi dei filtri sull'URL del manifesto/elenco di riproduzione in formato delimitato da punti e virgola. Si supponga di disporre di un filtro denominato *MyMobileDevice* che filtra le qualità e un altro denominato *MyStartTime* per impostare un'ora di inizio specifica. È possibile combinare fino a tre filtri.

Per ulteriori informazioni, consultate questo post di [blog.](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)

## <a name="considerations-and-limitations"></a>Considerazioni e limiti

- I valori per **forceEndTimestamp**, **presentationWindowDuration**e **liveBackoffDuration** non devono essere impostati per un filtro VOD. Vengono utilizzati solo per scenari di filtro in tempo reale.
- Un manifesto dinamico opera nei limiti GOP (fotogrammi chiave), pertanto il taglio ha una precisione GOP.
- È possibile utilizzare lo stesso nome di filtro per i filtri account e asset. I filtri asset hanno una precedenza più alta e sostituiscono i filtri account.
- Se si aggiorna un filtro, l'aggiornamento delle regole da parte dell'endpoint di streaming può richiedere fino a 2 minuti. Se hai usato i filtri per servire il contenuto (e il contenuto è stato memorizzato nella cache in proxy e cache CDN), l'aggiornamento di questi filtri può causare errori del lettore. È consigliabile cancellare la cache dopo l'aggiornamento del filtro. Se questa opzione non è possibile, prendere in considerazione l'utilizzo di un filtro diverso.
- I clienti devono scaricare manualmente il manifesto e analizzare l'indicatore esatto dell'ora di inizio e la scala temporale.

    - Per determinare le proprietà delle tracce in una risorsa, [ottenere ed esaminare il file manifesto.](#get-and-examine-manifest-files)
    - La formula per impostare le proprietà del timestamp del filtro asset è: <br/>startTimestamp: &lt;ora di&gt; +  &lt;inizio nel manifesto&gt; prevista per l'avvio previsto del filtro in secondi, scala cronologica

## <a name="next-steps"></a>Passaggi successivi

Negli articoli seguenti viene illustrato come creare filtri a livello di codice:The following articles show how to create filters programmatically:  

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
