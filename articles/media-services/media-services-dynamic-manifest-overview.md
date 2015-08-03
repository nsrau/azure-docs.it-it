<properties 
	pageTitle="Filtri e manifesti dinamici" 
	description="Questo argomento descrive come creare filtri che il client può usare per trasmettere in streaming sezioni specifiche di un flusso. Servizi multimediali crea manifesti dinamici per consentire questo streaming selettivo." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#Filtri e manifesti dinamici

A partire dalla versione 2.11, Servizi multimediali consente di definire filtri per i propri asset. I filtri sono costituiti da regole lato server che consentono ai clienti di eseguire operazioni particolari, come riprodurre solo una sezione di un video (anziché il video intero) oppure specificare solo un sottoinsieme di rendering audio e video, in modo che possa essere gestito dal dispositivo del cliente (anziché tutti i rendering associati all'asset). Il filtro degli asset viene eseguito attraverso **manifesti dinamici** creati su richiesta del cliente per trasmettere un video in streaming in base ai filtri specificati.

Questo argomento illustra alcuni scenari comuni in cui l'uso dei filtri può essere particolarmente vantaggioso e contiene collegamenti ad altri argomenti che illustrano come creare filtri a livello di programmazione (attualmente, è possibile creare filtri solo con le API REST).

##Panoramica

Quando si distribuiscono contenuti ai clienti (eventi in live streaming o video on demand), l'obiettivo è riuscire a trasmettere video di alta qualità a vari tipi di dispositivi in diverse condizioni di rete. Per raggiungere questo obiettivo, eseguire queste operazioni:

- Codificare il flusso video a più velocità in bit ([velocità in bit adattiva](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)), garantendo in questo modo la qualità e le condizioni di rete 
- Usare la funzione di [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md) di Servizi multimediali per riorganizzare dinamicamente il flusso in nuovi pacchetti creati con protocolli diversi, garantendo in questo modo la trasmissione a diversi tipi di dispositivi. Servizi multimediali supporta operazioni di trasmissione nelle seguenti tecnologie di streaming a velocità in bit adattiva: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (solo per i titolari di licenza Adobe PrimeTime/Access). 

###File manifesto 

Quando si codifica un asset per lo streaming a velocità in bit adattiva, viene creato un file (playlist) **manifesto**, ovvero un file basato su testo o XML. Il file **manifesto** include alcuni metadati di streaming, tra cui il tipo di traccia (audio, video o testo), il nome della traccia, l'ora di inizio e di fine, la velocità in bit (qualità), le lingue della traccia, la finestra di presentazione (finestra scorrevole di durata fissa) e un codec video (FourCC). Indica inoltre al lettore come recuperare il frammento successivo fornendo informazioni sui successivi frammenti video riproducibili disponibili e il relativo percorso. I frammenti (o segmenti) sono i "blocchi" effettivi di un contenuto video.


Di seguito è riportato un esempio di file manifesto:

	
	<?xml version="1.0" encoding="UTF-8"?>	
	<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">
	
	<StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
	<QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
	<QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
	<QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
	<QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
	<QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
	<QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
	<QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
	<QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />
	
	<c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
	</StreamIndex>
	
	
	<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
	<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
	
	<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
	
	
	<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
	<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
	
	<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
	
	</SmoothStreamingMedia>
	
###Manifesti dinamici

Esistono [scenari](media-services-dynamic-manifest-overview.md#scenarios) in cui il client deve poter disporre di una flessibilità maggiore rispetto a quanto descritto nel file manifesto predefinito dell'asset. Ad esempio:

- Per il dispositivo: distribuire solo i rendering specificati e/o le tracce di lingua specificate, se supportate dal dispositivo usato per la riproduzione dei contenuti ("filtro di rendering"). 
- Ridurre il manifesto in modo da mostrare solo una sottoclip di un evento live ("filtro di sottoclip").
- Tagliare l'inizio di un video ("trimming di un video").
- Regolare la finestra di presentazione (DVR) in modo da ottenere una lunghezza limitata della finestra nel lettore ("regolazione finestra presentazione").
 
Per ottenere questa flessibilità, Servizi multimediali offre **manifesti dinamici** basati su [filtri](media-services-dynamic-manifest-overview.md#filters) predefiniti. Dopo aver definito i filtri, è possibile usarli per trasmettere solo un determinato rendering o specifici sottoclip di un video. I filtri desiderati dovranno essere specificati nell'URL di streaming. È possibile applicare i filtri ai protocolli di streaming a velocità in bit adattiva supportati dalla funzione di [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH, Smooth Streaming e HDS. Ad esempio:

URL MPEG DASH con filtro

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL Smooth Streaming con filtro

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Per altre informazioni su come distribuire i contenuti e gli URL di streaming di compilazione, vedere [Informazioni generali sulla distribuzione di contenuti](media-services-deliver-content-overview/).


>[AZURE.NOTE]Tenere presente che i manifesti dinamici non modificano l'asset e il relativo manifesto predefinito. È possibile decidere di richiedere un flusso con o senza filtri.


###<a id="filters"></a>Filtri 

Sono disponibili due tipi di filtri di asset:

- Filtri globali (possono essere applicati a qualsiasi asset nell'account di Servizi multimediali di Azure e hanno una durata equivalente a quella dell'account). 
- Filtri locali (possono essere applicati a uno degli asset a cui è stato associato il filtro in fase di creazione e hanno una durata equivalente a quella dell'asset). 

I filtri globali e i filtri locali presentano esattamente le stesse proprietà e si differenziano solo per il tipo di scenario in cui sono più appropriati. I filtri globali, in genere, sono più adatti per i profili di dispositivo (filtro di rendering), mentre i filtri locali possono essere usati per tagliare un asset specifico.


##<a id="scenarios"></a>Scenari comuni 

Come accennato in precedenza, quando si distribuiscono contenuti ai clienti (eventi in live streaming o video on demand), l'obiettivo è riuscire a trasmettere video di alta qualità a vari tipi di dispositivi in diverse condizioni di rete. Possono tuttavia verificarsi anche altre situazioni in cui è opportuno applicare filtri agli asset e usare **manifesti dinamici**. Le sezioni seguenti forniscono una breve panoramica di diversi scenari di filtro.

- Definizione di un solo sottoinsieme di rendering audio e video che possa essere gestito da determinati dispositivi (anziché tutti i rendering associati all'asset). 
- Riproduzione di una sola sezione di un video (anziché del video intero).
- Regolazione della finestra di presentazione DVR

###Filtro di rendering 

È possibile scegliere di codificare un asset con più profili di codifica (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) e più velocità in bit di qualità. Non tutti i dispositivi, tuttavia, supportano tutti i profili e le velocità in bit dell'asset. Solo i dispositivi Android di precedente generazione, ad esempio, supportano il profilo H.264 Baseline+AACL. La trasmissione di contenuti a un dispositivo con una velocità in bit superiore di cui non può trarre vantaggio, inoltre, comporta uno spreco di larghezza di banda e di capacità di elaborazione. Per poter visualizzare i contenuti trasmessi, infatti, il dispositivo deve decodificare e ridimensionare tutte le informazioni trasmesse.

Con il manifesto dinamico, è possibile creare profili di dispositivo, ad esempio mobile, console, HD/SD e così via, e includere le tracce e la qualità desiderate per ogni profilo.

 
![Esempio di filtro di rendering][renditions2]

Nell'esempio seguente si usa Azure Media Encoder per codificare un asset in formato intermedio in sette rendering video ISO MP4 (da 180p a 1080p). L'asset così codificato può essere quindi riorganizzato dinamicamente in pacchetti creati con uno dei seguenti protocolli di streaming: HLS, Smooth, MPEG DASH e HDS. Nella parte superiore del diagramma è riportato il manifesto HLS per l'asset senza filtri (contiene tutti i sette rendering), mentre in basso a sinistra è riportato il manifesto HLS con applicato un filtro denominato "ott". Il filtro "ott" indica che devono essere rimosse tutte le velocità in bit inferiori a 1 Mbps. L'applicazione di questo filtro ha generato i due livelli di qualità inferiori rimossi nella risposta. In basso a destra è riportato invece il manifesto HLS con applicato un filtro denominato "mobile". Il filtro "mobile" indica che devono essere rimossi tutti i rendering con risoluzione superiore a 720p. L'applicazione di questo filtro ha generato i due rendering 1080p rimossi.

![Filtro di rendering][renditions1]

###Rimozione delle tracce di lingua

Gli asset possono includere più lingue audio, ad esempio inglese, spagnolo, francese e così via. In genere, è l'SDK del lettore a gestire la selezione della traccia audio predefinita e delle tracce audio disponibili per ciascuna selezione utente. Sviluppare questo tipo di SDK, tuttavia, è particolarmente difficile poiché richiede diverse implementazioni nel Player Framework specifico di ogni dispositivo. In alcune piattaforme, inoltre, le API del lettore offrono funzionalità limitate (ad esempio non includono funzioni di selezione audio) e non consentono quindi agli utenti di selezionare o modificare la traccia audio predefinita. Con i filtri di asset, è possibile controllare il comportamento mediante la creazione di filtri che includono solo le lingue audio desiderate.

![Filtro delle tracce di lingua][language_filter]


###Trimming della parte iniziale di un asset 

Nella maggior parte degli eventi in live streaming, gli operatori eseguono alcuni test prima dell'evento effettivo. Prima dell'inizio dell'evento, ad esempio, possono includere uno slate di questo tipo: "Il programma sta per iniziare". Se il programma prevede l'archiviazione, vengono archiviati e inclusi nella presentazione anche i dati del test e dello slate. Queste informazioni, tuttavia, non risultano visibili ai client. Con il manifesto dinamico, è possibile creare un filtro di ora di inizio e rimuovere dal manifesto tutti i dati non desiderati.

![Trimming della parte iniziale][trim_filter]

###Creazione di sottoclip (visualizzazioni) da un archivio live

Molti eventi live hanno una durata molto lunga ed è possibile quindi che un archivio live includa più eventi. Al termine dell'evento live, ad esempio, gli emittenti possono decidere di suddividere l'archivio live in sequenze logiche di avvio e arresto del programma. Possono quindi pubblicare separatamente questi programmi virtuali, senza dover post-elaborare l'archivio live e creare asset distinti (che non trarrebbero vantaggio dei frammenti presenti nella cache in caso di reti CDN). Esempi di programmi virtuali (sottoclip) possono essere, ad esempio, i tempi di una partita di calcio o di basket, gli inning del baseball o eventi individuali di un pomeriggio di programma olimpico.

Con il manifesto dinamico, è possibile creare filtri basati sulle ore di inizio/fine e creare visualizzazioni virtuali all'inizio dell'archivio live.

![Filtro di sottoclip][subclip_filter]

Asset filtrato:

![Sci][skiing]

###Regolazione della finestra di presentazione (DVR)

Attualmente, Servizi multimediali di Azure offre un archivio circolare la cui durata può essere configurata tra 5 minuti e 25 ore. Usando i filtri del file manifesto, è possibile creare una finestra DVR in sequenza all'inizio dell'archivio, senza dover eliminare alcun contenuto. Sono molti i casi in cui per un emittente può essere utile creare una finestra DVR limitata, in grado di spostarsi con il margine live, e al tempo stesso mantenere una finestra di archiviazione più grande. Un emittente, ad esempio, può decidere di usare i dati esterni alla finestra DVR per evidenziare clip oppure fornire finestre DVR diverse per dispositivi diversi. La maggior parte dei dispositivi mobili, inoltre, non è in grado di gestire finestre DVR di grandi dimensioni (è possibile usufruire di una finestra DVR di due minuti per i dispositivi mobile e di un'ora per i client desktop).

![Finestra DVR][dvr_filter]

###Regolazione LiveBackoff (posizione live)

È possibile usare i filtri del file manifesto anche per rimuovere alcuni secondi dal margine live di un programma live. In questo modo, gli emittenti possono guardare la presentazione nel punto di pubblicazione di anteprima e creare punti di inserimento di annunci prima che i destinatari ricevano il flusso (in genere ritardato di 30 secondi). Gli emittenti possono quindi inserire questi annunci nel proprio Framework Client in tempo per poter ricevere ed elaborare le informazioni prima dell'opportunità di annuncio.

Oltre a fornire il supporto per gli annunci pubblicitari, LiveBackoff consente anche di regolare la posizione di download live del client in modo che, nel momento in cui i client deviano e raggiungono il margine live, possono comunque ottenere dal server i frammenti desiderati, anziché gli errori HTTP 404 o 412.



![livebackoff_filter][livebackoff_filter]


###Combinazione di più regole in un unico filtro

È possibile combinare più regole di filtro in un unico filtro. Ad esempio, è possibile definire una regola di intervallo per rimuovere lo slate da un archivio live e applicare un filtro alle velocità in bit disponibili. In caso di più regole di filtro, il risultato finale è la composizione (solo intersezione) di queste regole.

![multiple-rules][multiple-rules]

##Creare filtri a livello di codice

L'argomento seguente descrive le entità di Servizi multimediali correlati ai filtri. Illustra inoltre la procedura per creare filtri a livello di codice.

[Creare filtri con le API REST](media-services-rest-dynamic-manifest.md).

##Problemi noti e limitazioni

- Il manifesto dinamico opera nei limiti dell'intervallo GOP (fotogrammi chiave) e, pertanto, il trimming eredita la precisione del GOP. 
- È possibile usare lo stesso nome di filtro per i filtri globali e locali. I filtri locali, tuttavia, hanno la precedenza e sovrascrivono quindi i filtri globali.
- Se si aggiorna un filtro, l'endpoint di streaming può impiegare fino a due minuti per aggiornare le regole. Se il contenuto è stato trasmesso usando dei filtri (e memorizzato nelle cache dei proxy e delle reti CDN), l'aggiornamento dei filtri può determinare un errore del lettore. È consigliabile quindi cancellare la cache dopo aver aggiornato il filtro. Se questa operazione non è consentita, prendere in considerazione la possibilità di usare un filtro diverso.


##Vedere anche



[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]: ./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
 

<!---HONumber=July15_HO4-->