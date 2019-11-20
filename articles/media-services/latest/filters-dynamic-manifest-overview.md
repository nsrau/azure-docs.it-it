---
title: Filtrare i manifesti usando Dynamic Packager
titleSuffix: Azure Media Services
description: Informazioni su come creare filtri usando Dynamic Packager per filtrare e trasmettere in modo selettivo i manifesti.
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
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186222"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtrare i manifesti usando Dynamic Packager

Quando si distribuisce contenuto di streaming a bitrate adattivo ai dispositivi, a volte è necessario pubblicare più versioni di un manifesto per individuare funzionalità specifiche del dispositivo o larghezza di banda di rete disponibile. [Dynamic Packager](dynamic-packaging-overview.md) consente di specificare filtri che consentono di filtrare i codec, le soluzioni, le velocità in bit e le combinazioni di tracce audio specifiche in tempo reale. Questo filtro elimina la necessità di creare più copie. È sufficiente pubblicare un nuovo URL con un set specifico di filtri configurati per i dispositivi di destinazione (iOS, Android, SmartTV o browser) e le funzionalità di rete (scenari a larghezza di banda elevata, per dispositivi mobili o a larghezza di banda ridotta). In questo caso, i client possono modificare lo streaming del contenuto tramite la stringa di query (specificando i filtri di [asset o i filtri](filters-concept.md)per gli account disponibili) e usare i filtri per trasmettere sezioni specifiche di un flusso.

Per alcuni scenari di recapito è necessario assicurarsi che un cliente non possa accedere a tracce specifiche. Ad esempio, è possibile che non si desideri pubblicare un manifesto che contiene tracce HD a un livello specifico del Sottoscrittore. In alternativa, è possibile rimuovere tracce specifiche di velocità in bit adattiva (ABR) per ridurre i costi di consegna a un dispositivo specifico che non trarrebbe vantaggio dalle tracce aggiuntive. In questo caso, è possibile associare un elenco di filtri creati in precedenza al [localizzatore di streaming](streaming-locators-concept.md) al momento della creazione. I client non possono quindi modificare il modo in cui il contenuto viene trasmesso perché è definito dal **localizzatore di streaming**.

È possibile combinare i filtri tramite la specifica di [filtri nel localizzatore di streaming](filters-concept.md#associating-filters-with-streaming-locator) e altri filtri specifici del dispositivo specificati dal client nell'URL. Questa combinazione è utile per limitare tracce aggiuntive, come metadati, flussi di eventi, lingue audio o tracce audio descrittive.

Questa possibilità di specificare filtri diversi nel flusso fornisce una potente soluzione di manipolazione dei **manifesti dinamici** per la destinazione di più casi d'uso per i dispositivi di destinazione. In questo argomento vengono illustrati i concetti relativi ai **manifesti dinamici** e vengono forniti esempi di scenari in cui è possibile utilizzare questa funzionalità.

> [!NOTE]
> I manifesti dinamici non modificano l'asset e il manifesto predefinito per tale asset.

## <a name="overview-of-manifests"></a>Panoramica dei manifesti

Servizi multimediali di Azure supporta i protocolli HLS, MPEG DASH e Smooth Streaming. Nell'ambito della creazione [dinamica dei pacchetti](dynamic-packaging-overview.md), i manifesti del client di streaming (playlist Master HLS, la descrizione della presentazione dei supporti Dash [MPD] e Smooth Streaming) vengono generati in modo dinamico in base al selettore di formato nell'URL. Per ulteriori informazioni, vedere la pagina relativa ai protocolli di recapito in un [flusso di lavoro comune su richiesta](dynamic-packaging-overview.md#delivery-protocols).

### <a name="get-and-examine-manifest-files"></a>Ottenere ed esaminare i file manifesto

È possibile specificare un elenco di condizioni di proprietà della traccia di filtro in base alle tracce del flusso (in diretta o video su richiesta [VOD]) da includere in un manifesto creato dinamicamente. Per ottenere ed esaminare le proprietà dei brani, è necessario caricare prima di tutto il manifesto Smooth Streaming.

L'esercitazione [caricare, codificare e trasmettere file con .NET](stream-files-tutorial-with-api.md#get-streaming-urls) illustra come creare gli URL di streaming con .NET. Se si esegue l'app, uno degli URL punta al manifesto Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Copiare e incollare l'URL nella barra degli indirizzi del browser. Il file verrà scaricato. È possibile aprirlo in qualsiasi editor di testo.

Per un esempio REST, vedere [caricare, codificare e trasmettere file con REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorare la velocità in bit di un flusso video

È possibile usare la [pagina di prova di Azure Media Player](https://aka.ms/azuremediaplayer) per monitorare la velocità in bit di un flusso video. La pagina demo Visualizza informazioni di diagnostica nella scheda **diagnostica** :

![Diagnostica di Azure Media Player][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Esempi: URL con filtri nella stringa di query

È possibile applicare filtri ai protocolli di streaming ABR: HLS, MPEG-DASH e Smooth Streaming. Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocol|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtro di rendering

È possibile scegliere di codificare l'asset in più profili di codifica (H. 264 Baseline, H. 264 High, AACL, AACH, Dolby Digital Plus) e più velocità in bit di qualità. Non tutti i dispositivi, tuttavia, supportano tutti i profili e le velocità in bit dell'asset. Ad esempio, i dispositivi Android meno recenti supportano solo H. 264 Baseline + AACL. L'invio di velocità in bit più elevate a un dispositivo che non è in grado di sfruttare i vantaggi della larghezza di banda e dei dispositivi Un dispositivo di questo tipo deve decodificare tutte le informazioni fornite, solo per ridurle per la visualizzazione.

Con il manifesto dinamico, è possibile creare profili di dispositivo, ad esempio dispositivi mobili, console o HD/SD, e includere le tracce e le qualità che si desidera far parte di ogni profilo. Questo metodo è denominato filtro di rendering. Nel diagramma seguente viene illustrato un esempio.

![Esempio di filtro di rendering con manifesto dinamico][renditions2]

Nell'esempio seguente si usa un codificatore per codificare un asset in formato intermedio in sette rendering video ISO MP4 (da 180p a 1080p). L'asset codificato può essere inserito [dinamicamente](dynamic-packaging-overview.md) in un pacchetto in uno dei protocolli di streaming seguenti: HLS, MPEG Dash e Smooth.

Nella parte superiore del diagramma seguente viene illustrato il manifesto HLS per l'asset senza filtri. Contiene tutti e sette i rendering.  In basso a sinistra il diagramma mostra un manifesto HLS a cui è stato applicato un filtro denominato "ott". Il filtro "ott" specifica la rimozione di tutte le velocità in bit inferiori a 1 Mbps, quindi i due livelli di qualità inferiori sono stati rimossi nella risposta. In basso a destra il diagramma mostra il manifesto HLS a cui è stato applicato un filtro denominato "mobile". Il filtro "mobile" specifica la rimozione dei rendering in cui la risoluzione è maggiore di 720p, quindi i due rendering 1080p sono stati rimossi.

![Filtraggio del rendering con manifesto dinamico][renditions1]

## <a name="removing-language-tracks"></a>Rimozione delle tracce di lingua
Gli asset possono includere più lingue audio, ad esempio inglese, spagnolo, francese e così via. In genere, Player SDK gestisce la selezione della traccia audio predefinita e le tracce audio disponibili per ogni selezione utente.

Lo sviluppo di tali SDK è impegnativo perché richiede implementazioni diverse tra i Framework dei lettori specifici del dispositivo. In alcune piattaforme, inoltre, le API dei giocatori sono limitate e non includono la funzionalità di selezione audio, in cui gli utenti non possono selezionare o modificare la traccia audio predefinita. Con i filtri di asset è possibile controllare il comportamento creando filtri che includono solo lingue audio desiderate.

![Filtro delle tracce della lingua con manifesto dinamico][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Taglio dell'inizio di un asset

Nella maggior parte degli eventi in live streaming, gli operatori eseguono alcuni test prima dell'evento effettivo. Ad esempio, potrebbe includere uno Slate come questo prima dell'inizio dell'evento: "il programma inizierà momentaneamente".

Se il programma prevede l'archiviazione, vengono archiviati e inclusi nella presentazione anche i dati del test e dello slate. Tuttavia, queste informazioni non devono essere visualizzate ai client. Con il manifesto dinamico, è possibile creare un filtro di ora di inizio e rimuovere dal manifesto tutti i dati non desiderati.

![Troncamento dell'inizio di un asset con manifesto dinamico][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Creazione di sottoclip (visualizzazioni) da un archivio live

Molti eventi live hanno una durata molto lunga ed è possibile quindi che un archivio live includa più eventi. Al termine dell'evento live, ad esempio, gli emittenti possono decidere di suddividere l'archivio live in sequenze logiche di avvio e arresto del programma.

È possibile pubblicare separatamente questi programmi virtuali senza pubblicare l'Archivio Live e creare asset distinti, il che non sfrutta i vantaggi dei frammenti memorizzati nella cache presenti in CDNs. Esempi di programmi virtuali di questo tipo sono, ad esempio, i tempi di una partita di calcio o di basket, gli inning del baseball o eventi singoli di un qualsiasi programma sportivo.

Con il manifesto dinamico, è possibile creare filtri usando gli orari di inizio/fine e creare visualizzazioni virtuali all'inizio dell'archivio live.

![Filtro di sottoclip con manifesto dinamico][subclip_filter]

Di seguito è riportato l'asset filtrato:

![Asset filtrato con manifesto dinamico][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Regolazione della finestra di presentazione (DVR)

Attualmente, servizi multimediali di Azure offre un archivio circolare in cui la durata può essere configurata da 1 minuto a 25 ore. Usando i filtri del file manifesto, è possibile creare una finestra DVR in sequenza all'inizio dell'archivio, senza dover eliminare alcun contenuto. Sono molti i casi in cui per un emittente può essere utile creare una finestra DVR limitata, in grado di spostarsi con il margine live, e al tempo stesso mantenere una finestra di archiviazione più grande. Un broadcaster potrebbe voler usare i dati fuori dalla finestra DVR per evidenziare le clip o potrebbe voler fornire finestre DVR diverse per dispositivi diversi. La maggior parte dei dispositivi mobili, ad esempio, non è in grado di gestire finestre DVR di grandi dimensioni (è possibile usufruire di una finestra DVR di 2 minuti per i dispositivi mobili e di un'ora per i client desktop).

![Finestra DVR con manifesto dinamico][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Regolazione LiveBackoff (posizione live)

È possibile usare i filtri del file manifesto anche per rimuovere alcuni secondi dal margine live di un programma live. Il filtro consente ai broadcaster di guardare la presentazione nel punto di pubblicazione di anteprima e creare punti di inserimento di annunci prima che i visualizzatori ricevano il flusso (con un backup di 30 secondi). Gli emittenti possono quindi effettuare il push di questi annunci ai propri framework client nel tempo per ricevere ed elaborare le informazioni prima dell'opportunità dell'annuncio.

Oltre al supporto per gli annunci, è possibile usare l'impostazione Live back-off per modificare la posizione dei visualizzatori in modo che, quando i client si spostano e accedano al perimetro attivo, possano comunque ottenere frammenti dal server. In questo modo, i client non riceveranno un errore HTTP 404 o 412.

![Filtrare per il back-off Live con manifesto dinamico][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinazione di più regole in un unico filtro

È possibile combinare più regole di filtro in un unico filtro. Ad esempio, è possibile definire una "regola di intervallo" per rimuovere gli slate da un archivio live e filtrare anche le velocità in bit disponibili. Quando si applicano più regole di filtro, il risultato finale è l'intersezione di tutte le regole.

![Più regole di filtro con manifesto dinamico][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinazione di più filtri (filtro composizione)

È inoltre possibile combinare più filtri in un singolo URL. Lo scenario seguente illustra il motivo per cui è possibile combinare filtri:

1. È necessario filtrare le qualità video per i dispositivi mobili, ad esempio Android o iPad (per limitare le qualità dei video). Per rimuovere le qualità indesiderate, creare un filtro account adatto per i profili di dispositivo. È possibile usare i filtri account per tutti gli asset con lo stesso account di servizi multimediali senza ulteriori associazioni.
1. Inoltre si desidera tagliare l'ora di inizio e fine di un asset. Per eseguire l'operazione di taglio, è necessario creare un filtro asset e impostare l'ora di inizio/fine.
1. Si desidera combinare entrambi i filtri. Senza combinazione, è necessario aggiungere un filtro qualità al filtro di taglio, rendendo più difficile l'utilizzo del filtro.

Per combinare i filtri, impostare i nomi dei filtri sull'URL del manifesto o della playlist in formato delimitato da punto e virgola. Si supponga di avere un filtro denominato *MyMobileDevice* che filtra le qualità e di avere un altro denominato *MyStartTime* per impostare un'ora di avvio specifica. È possibile combinare fino a tre filtri.

Per altre informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Considerazioni e limiti

- I valori per **forceEndTimestamp**, **presentationWindowDuration**e **liveBackoffDuration** non devono essere impostati per un filtro VOD. Sono usati solo per gli scenari di filtro Live.
- Un manifesto dinamico opera nei limiti del GOP (fotogrammi chiave), quindi l'operazione di taglio ha una precisione GOP.
- È possibile usare lo stesso nome di filtro per gli account e i filtri di asset. I filtri asset hanno precedenza più elevata e sostituiranno i filtri account.
- Se si aggiorna un filtro, l'endpoint di streaming può richiedere fino a 2 minuti per aggiornare le regole. Se sono stati usati filtri per gestire il contenuto (e il contenuto è stato memorizzato nella cache in proxy e cache della rete CDN), l'aggiornamento di questi filtri può comportare errori del lettore. Si consiglia di cancellare la cache dopo aver aggiornato il filtro. Se questa opzione non è possibile, provare a usare un filtro diverso.
- I clienti devono scaricare manualmente il manifesto e analizzare lo Stamp dell'ora di inizio esatto e la scala temporale.

    - Per determinare le proprietà delle tracce in un asset, [ottenere ed esaminare il file manifesto](#get-and-examine-manifest-files).
    - La formula per impostare le proprietà relative al timestamp del filtro asset è la seguente: <br/>startTimestamp = &lt;ora di inizio nel manifesto&gt; +  &lt;ora di inizio filtro in secondi&gt; * scala cronologica

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
