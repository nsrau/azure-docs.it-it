---
title: Esercitazioni avanzate del flusso di lavoro Premium del codificatore multimediale
description: "Questo documento contiene procedure dettagliate che illustrano come eseguire attività avanzate con il flusso di lavoro Premium del codificatore multimediale e come creare flussi di lavoro complessi con Progettazione flussi di lavoro."
services: media-services
documentationcenter: 
author: xstof
manager: erikre
editor: 
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: xstof;xpouyat;juliako
translationtype: Human Translation
ms.sourcegitcommit: 602f86f17baffe706f27963e8d9963f082971f54
ms.openlocfilehash: 5cb610b4b2387af48ef29acdcc5e40e154f515a7


---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Esercitazioni avanzate del flusso di lavoro Premium del codificatore multimediale
## <a name="overview"></a>Panoramica
Questo documento contiene procedure dettagliate che illustrano come personalizzare i flussi di lavoro con **Progettazione flussi di lavoro**. I file dei flussi di lavoro effettivi sono disponibili [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>Sommario
Vengono trattati gli argomenti seguenti:

* [Codifica di un file MXF in un MP4 a velocità in bit singola](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Avvio di un nuovo flusso di lavoro](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Uso di Media File Input](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Analisi di flussi multimediali](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Aggiunta di un codificatore video per la generazione di file MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Codifica del flusso audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexing di flussi audio e video in un contenitore MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Scrittura del file MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Creazione di un asset di Servizi multimediali dal file di output](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testare il flusso di lavoro completato in locale](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Codifica di un file MXF in MP4 a velocità in bit multipla - Creazione dinamica dei pacchetti abilitata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Aggiunta di uno o più output MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Configurazione dei nomi di output dei file](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Aggiunta di una traccia audio separata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Aggiunta del file SMIL ISM](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Codifica di un file MXF in un MP4 a velocità in bit multipla - Progetto avanzato](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [Panoramica del flusso di lavoro da migliorare](#workflow-overview-to-enhance)
  * [Convenzioni di denominazione dei file](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Pubblicazione delle proprietà dei componenti nella radice del flusso di lavoro](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Fare in modo che i nomi file di output generati si basino sui valori delle proprietà pubblicati](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Aggiunta di anteprime all'output MP4 a velocità in bit multipla](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Panoramica del flusso di lavoro a cui aggiungere le anteprime](#workflow-overview-to-add-thumbnails-to)
  * [Aggiunta della codifica JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Gestione della conversione dello spazio colore](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Scrittura delle anteprime](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Rilevamento di errori in un flusso di lavoro](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Flusso di lavoro completato](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Taglio basato sull'ora dell'output MP4 a velocità in bit multipla](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Panoramica del flusso di lavoro a cui iniziare ad aggiungere il taglio](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Uso di Stream Trimmer](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Flusso di lavoro completato](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introduzione al componente con script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Scripting in un flusso di lavoro: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Taglio basato sul fotogramma dell'output MP4 a velocità in bit multipla](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Panoramica del progetto a cui iniziare ad aggiungere il taglio](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Uso di Clip List XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modifica dell'elenco di clip da un componente con script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Aggiunta di una pratica proprietà ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="a-idmxftomp4aencoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>Codifica di un file MXF in un MP4 a velocità in bit singola
In questa procedura dettagliata verrà creato un file MP4 a velocità in bit singola con audio con codifica AAC-HE da un file di input MXF.

### <a name="a-idmxftomp4startnewastarting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Avvio di un nuovo flusso di lavoro
Aprire Progettazione flussi di lavoro e selezionare "File" - "New Workspace" - "Transcode Blueprint"

Il nuovo flusso di lavoro mostrerà 3 elementi:

* Primary Source File
* Clip List XML
* Output File/Asset  

![Nuovo flusso di lavoro della codifica](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nuovo flusso di lavoro della codifica*

### <a name="a-idmxftomp4withfileinputausing-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>Uso di Media File Input
Per accettare il file multimediale di input, si inizia aggiungendo un componente Media File Input. Per aggiungere un componente al flusso di lavoro, cercarlo nella casella di ricerca del repository e trascinare la voce desiderata sul riquadro della finestra di progettazione. Eseguire questa operazione per Media File Input e connettere il componente Primary Source File al pin di input Filename da Media File Input.

![Media File Input connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Media File Input connesso*

Prima di eseguire altre operazioni, sarà necessario indicare a Progettazione flussi di lavoro quale file di esempio usare per progettare il flusso di lavoro. A questo scopo, fare clic sullo sfondo del riquadro della finestra di progettazione e cercare la proprietà Primary Source File nel riquadro delle proprietà sulla destra. Fare clic sull'icona della cartella e selezionare il file desiderato con cui testare il flusso di lavoro. Al termine dell'operazione, il componente Media File Input esaminerà il file e popolerà i pin di output per poter effettuare la reflection del file esaminato.

![Media File Input popolato](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Media File Input popolato*

Questa operazione specifica l'input da usare, ma non indica ancora dove salvare l'output codificato. Come è stato configurato Primary Source File, configurare ora la proprietà Output Folder Variable, appena sotto.

![Proprietà di input e output configurate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Proprietà di input e output configurate*

### <a name="a-idmxftomp4streamsainspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Analisi di flussi multimediali
Spesso si vuole sapere come appare il flusso che scorre nel flusso di lavoro. Per esaminare un flusso in qualsiasi punto del flusso di lavoro, è sufficiente fare clic su un pin di output o di input in uno dei componenti. In questo caso, provare a fare clic sul pin di output Uncompressed Video da Media File Input. Verrà aperta una finestra di dialogo che consente di esaminare il video in uscita.

![Analisi del pin di output Uncompressed Video](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Analisi del pin di output Uncompressed Video*

In questo caso, ad esempio, indica che si ha a che fare con un input 1920x1080 a 24 fotogrammi al secondo nel campionamento 4:2:2 per un video di almeno 2 minuti.

### <a name="a-idmxftomp4filegenerationaadding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Aggiunta di un codificatore video per la generazione di file MP4
Si noti che ora in Media File Input sono disponibili per l'uso un pin Uncompressed Video e più pin di output Uncompressed Audio. Per codificare il video in entrata, è necessario un componente di codifica, in questo caso per generare i file MP4.

Per codificare il flusso video in H.264, aggiungere il componente AVC Video Encoder all'area di progettazione. Questo componente accetta un flusso video non compresso e genera un flusso video compresso AVC nel pin di output.

![Codificatore AVC non connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Codificatore AVC non connesso*

Le proprietà determinano come avviene esattamente la codifica. Ecco alcune delle impostazioni più importanti:

* Output width e Output height: determinano la risoluzione del video codificato. In questo caso, si usa 640x360.
* Frame Rate: se viene impostata su passthrough, adotterà la frequenza dei fotogrammi di origine, anche se è possibile eseguirne l'override. Si noti che tale conversione della frequenza dei fotogrammi non è compensata dal movimento.
* Profile e Level: determinano il profilo e il livello AVC. Per ottenere facilmente altre informazioni sui diversi livelli e profili, fare clic sull'icona del punto di domanda nel componente AVC Video Encoder. La pagina della Guida mostrerà altri dettagli su ogni livello. Per questo esempio, si userà il profilo principale al livello 3.2 (impostazione predefinita).
* Rate Control Mode e Bitrate (kbps): in questo scenario viene usato un output con velocità in bit costante (CBR) a 1200 kbps
* Video Format: riguarda le informazioni sull'usabilità video che vengono scritte nel flusso H.264 (informazioni secondarie che possono essere usate da un decodificatore per migliorare la visualizzazione, ma non indispensabili per una corretta decodifica):
* NTSC (tipico per Stati Uniti o Giappone, usa 30 fps)
* PAL (tipico per l'Europa, usa 25 fps)
* GOP Size Mode: in questo caso, verrà configurata una dimensione GOP fissa con un intervallo chiave di 2 secondi con GOP chiusi. Questo assicura la compatibilità con la creazione dinamica di pacchetti fornita da Servizi multimediali di Azure.

Per alimentare il codificatore AVC, connettere il pin di output Uncompressed Video del componente Media File Input al pin di input Uncompressed Video del codificatore AVC.

![Codificatore AVC connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Codificatore principale AVC connesso*

### <a name="a-idmxftomp4audioaencoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Codifica del flusso audio
A questo punto, il video è stato codificato, ma il flusso audio non compresso originale deve ancora essere compresso. A questo scopo verrà applicata la codifica AAC usando il componente AAC Encoder (Dolby). Aggiungerlo al flusso di lavoro.

![Codificatore AVC non connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codificatore AAC non connesso*

Ora esiste un'incompatibilità: è presente un solo pin di input audio non compresso dal codificatore AAC, ma molto probabilmente per Media File Input saranno disponibili due diversi flussi audio non compressi: uno per il canale audio sinistro e uno per quello destro. Se si usa un sistema audio di tipo surround, i canali saranno addirittura 6. Quindi non è possibile connettere direttamente l'audio dall'origine Media File Input al codificatore audio AAC. Per il componente AAC è previsto un flusso audio cosiddetto "con interleave": un solo flusso con entrambi i canali sinistro e destro con interleave reciproco. Una volta conosciuta dal file multimediale di origine la posizione nell'origine di ogni traccia audio, è possibile generare tale flusso audio con interleave con le posizioni degli altoparlanti assegnate correttamente per la sinistra e la destra.

Prima si genererà un flusso con interleave dai canali audio di origine necessari. Il componente Audio Stream Interleaver lo gestirà automaticamente. Aggiungerlo al flusso di lavoro e connettere gli output audio da Media File Input a esso.

![Audio Stream Interleaver connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Audio Stream Interleaver connesso*

Ora che è disponibile un flusso audio con interleave, non si è tuttavia specificato a quali posizioni assegnare l'altoparlante sinistro o destro. Per specificarlo, è possibile sfruttare Speaker Position Assigner.

![Aggiunta di Speaker Position Assigner](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Aggiunta di Speaker Position Assigner*

Configurare Speaker Position Assigner per l'uso con un flusso di input stereo usando un filtro del set di impostazioni del codificatore "Custom" e il set di impostazioni del canale denominato "2.0 (L,R)". La posizione dell'altoparlante sinistro verrà assegnata al canale 1 e la posizione dell'altoparlante destro al canale 2.

Connettere l'output di Speaker Position Assigner all'input del codificatore AAC. Indicare quindi al codificatore AAC di usare un set di impostazioni del canale "2.0 (L,R)", in modo che usi l'audio stereo come input.

### <a name="a-idmxftomp4audioandfideoamultiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexing di flussi audio e video in un contenitore MP4
È possibile acquisire sia il flusso video con codifica AVC che il flusso audio con codifica AAC in un contenitore MP4. Il processo di missaggio di flussi diversi in uno solo è chiamato "multiplexing" (o "muxing"). In questo caso verrà eseguito l'interleave dei flussi audio e video in un solo pacchetto MP4 coerente. Il componente che coordina questa operazione per un contenitore MP4 è chiamato ISO MPEG-4 Multiplexer. Aggiungerne uno all'area di progettazione e connettere sia AVC Video Encoder che il codificatore AAC agli input.

![MPEG4 Multiplexer connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*MPEG4 Multiplexer connesso*

### <a name="a-idmxftomp4writingmp4awriting-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Scrittura del file MP4
Quando si scrive un file di output, viene usato il componente File Output. È possibile connetterlo all'output di ISO MPEG-4 Multiplexer in modo che l'output venga scritto sul disco. A questo scopo, connettere il pin di output Container (MPEG-4) al pin di input Write di File Output.

![File Output connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*File Output connesso*

Il nome file che verrà usato viene determinato dalla proprietà File. Anche se tale proprietà può essere hardcoded in un determinato valore, molto probabilmente si preferirà invece impostarla usando un'espressione.

Per fare in modo che il flusso di lavoro determini automaticamente la proprietà File name di output da un'espressione, fare clic sul pulsante accanto al nome file (accanto all'icona della cartella). Quindi scegliere "Expression" dal menu a discesa. Verrà visualizzato l'editor espressioni. Prima di tutto cancellare i contenuti dell'editor.

![Expression Editor vuoto](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Expression Editor vuoto*

L'editor espressioni consente di immettere qualsiasi valore letterale, combinato con una o più variabili. Le variabili iniziano con un simbolo di dollaro. Quando si preme il tasto $, l'editor visualizzerà una casella a discesa con le variabili disponibili. In questo caso si userà una combinazione tra la variabile della directory di output e la variabile del nome file di input di base:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Expression Editor compilato](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Expression Editor compilato*

> [!NOTE]
> Per visualizzare un file di output del processo di codifica in Azure, è necessario specificare un valore nell'editor espressioni.
>
>

Quando si conferma l'espressione facendo clic su OK, la finestra delle proprietà visualizzerà in anteprima il valore in cui viene attualmente risolta la proprietà File.

![L'espressione File risolve la directory di output](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*L'espressione File risolve la directory di output*

### <a name="a-idmxftomp4assetfromoutputacreating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Creazione di un asset di Servizi multimediali dal file di output
Anche se è stato scritto un file di output MP4, tuttavia è necessario indicare che questo file appartiene all'asset di output che Servizi multimediali genererà come risultato dell'esecuzione del flusso di lavoro. A questo scopo, viene usato il nodo Output File/Asset nel canvas del flusso di lavoro. Tutti i file in arrivo in questo nodo faranno parte dell'asset di Servizi multimediali di Azure risultante.

Connettere il componente File Output al componente Output File/Asset per completare il flusso di lavoro.

![Flusso di lavoro completato](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Flusso di lavoro completato*

### <a name="a-idmxftomp4testatest-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>Testare il flusso di lavoro completato in locale
Per testare il flusso di lavoro in locale, fare clic sul pulsante play nella barra degli strumenti in alto. Al termine dell'esecuzione del flusso di lavoro, esaminare l'output generato nella cartella di output configurata. Verrà visualizzato il file di output MP4 completato codificato dal file di origine di input MXF.

## <a name="a-idmxftomp4withdynpackagingaencoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Codifica di un file MXF in MP4 - Creazione dinamica dei pacchetti a velocità in bit multipla abilitata
In questa procedura dettagliata verrà creato un set di file MP4 a velocità in bit multipla con audio con codifica AAC da un solo file di input MXF.

Quando si vuole usare un output di asset a velocità in bit multipla in combinazione con le funzionalità di Creazione dinamica dei pacchetti offerte da Servizi multimediali di Azure, dovranno essere generati più file MP4 allineati con GOP per ogni diversa velocità in bit e risoluzione. A questo scopo, la procedura dettagliata [Codifica di un file MXF in un MP4 a velocità in bit singola](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) costituisce un valido punto di partenza.

![Flusso di lavoro iniziale](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Flusso di lavoro iniziale*

### <a name="a-idmxftomp4withdynpackagingmoreoutputsaadding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Aggiunta di uno o più output MP4
Ogni file MP4 nell'asset di Servizi multimediali di Azure risultante supporterà una velocità in bit e una risoluzione diversa. Ora verrà aggiunto uno o più file di output MP4 al flusso di lavoro.

Per verificare che tutti i codificatori video creati abbiano le stesse impostazioni, la soluzione più comoda consiste nel duplicare il componente AVC Video Encoder esistente e configurare un'altra combinazione di risoluzione e velocità in bit (ne verrà aggiunta una pari a 960 x 540 a 25 fotogrammi al secondo a 2,5 Mbps). Per duplicare il codificatore esistente, copiarlo e incollarlo nell'area di progettazione.

Connettere il pin di output Uncompressed Video di Media File Input nel nuovo componente AVC.

![Secondo codificatore AVC connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Secondo codificatore AVC connesso*

Adattare ora la configurazione del nuovo codificatore AVC all'output 960x540 a 2,5 Mbps usando le proprietà "Output width", "Output height" e "Bitrate (kbps)".

Poiché si vuole usare l'asset risultante con la creazione dinamica dei pacchetti di Servizi multimediali di Azure, l'endpoint di streaming deve poter generare da questi file MP4 frammenti DASH/MP4 frammentati/HLS esattamente allineati tra loro in modo che i client che passano da una velocità in bit a un'altra ottengano un'esperienza video e audio continua e uniforme. A questo scopo, è necessario assicurarsi che nelle proprietà di entrambi i codificatori AVC la dimensione GOP ("Group of Pictures") per entrambi i file MP4 sia impostata su 2 secondi, come indicato di seguito:

* impostare GOP Size Mode sulla dimensione Fixed GOP e
* Key Frame Interval su due secondi
* impostare anche GOP IDR Control su Closed GOP per assicurarsi che tutti i GOP siano autonomi senza dipendenze

Per rendere comprensibile il flusso di lavoro, rinominare il primo codificatore AVC "AVC Video Encoder 640x360 1200kbps" e il secondo codificatore AVC "AVC Video Encoder 960x540 2500 kbps".

Ora aggiungere un secondo componente ISO MPEG-4 Multiplexer e un secondo componente File Output. Connettere il multiplexer al nuovo codificatore AVC e verificare che l'output sia diretto a File Output. Quindi connettere anche l'output del codificatore audio AAC all'input del nuovo multiplexer. File Output a sua volta può essere connesso al nodo Output File/Asset per aggiungerlo all'asset di Servizi multimediali che verrà creato.

![Secondo muxer e File Output connessi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Secondo muxer e File Output connessi*

Per assicurare la compatibilità con la creazione dinamica dei pacchetti di Servizi multimediali di Azure, impostare Chunk Mode del multiplexer su GOP count o su Duration e impostare i GOP per ogni blocco su 1. Questa dovrebbe essere l'impostazione predefinita.

![Modalità blocco del muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modalità blocco del muxer*

Nota: è possibile ripetere questo processo per tutte le altre combinazioni di velocità in bit e risoluzione che si vuole aggiungere all'output dell'asset.

### <a name="a-idmxftomp4withdynpackagingconfoutputnamesaconfiguring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurazione dei nomi di output dei file
È stato aggiunto più di un file all'asset di output. Per questo è necessario verificare che i nomi di ogni file di output siano tutti diversi ed eventualmente applicare anche una convenzione di denominazione file in modo che dal nome file risulti chiaro di che cosa si tratta.

La denominazione dell'output dei file può essere controllata con le espressioni nella finestra di progettazione. Aprire il riquadro delle proprietà per uno dei componenti File Output e aprire l'editor espressioni per la proprietà File. Il primo file di output è stato configurato con l'espressione seguente (vedere l'esercitazione per la codifica da un [file MXF in un MP4 a velocità in bit singola](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Il nome file viene quindi determinato da due variabili: la directory di output in cui scrivere e il nome di base del file di origine. La prima viene esposta come proprietà nella radice del flusso di lavoro e il secondo viene determinato dal file in arrivo. Si noti che la directory di output è quella usata per il test locale. Il motore del flusso di lavoro eseguirà l'override di questa proprietà quando il flusso di lavoro verrà eseguito dal processore multimediale basato sul cloud in Servizi multimediali di Azure.
Per assegnare a entrambi i file di output una denominazione di output coerente, modificare l'espressione di denominazione del primo file in:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

e la seconda in:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Effettuare un'esecuzione di test intermedia per verificare che entrambi i file di output MP4 vengano generati correttamente.

### <a name="a-idmxftomp4withdynpackagingaudiotracksaadding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Aggiunta di una traccia audio separata
Come sarà illustrato in seguito, quando si genera un file ism da usare con i file di output MP4, sarà necessario anche un file MP4 solo audio come traccia audio per il flusso adattivo. Per creare questo file, aggiungere un altro muxer al flusso di lavoro (ISO-MPEG-4 Multiplexer) e connettere il pin di output del codificatore AAC al pin di input per Track 1.

![Muxer audio aggiunto](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Muxer audio aggiunto*

Creare un terzo componente File Output per l'output del flusso in uscita dal muxer e configurare l'espressione di denominazione file come:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Creazione di File Output con il muxer audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Creazione di File Output con il muxer audio*

### <a name="a-idmxftomp4withdynpackagingismfileaadding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Aggiunta del file SMIL ISM
Per il funzionamento della creazione dinamica dei pacchetti con entrambi i file MP4 (e l'MP4 solo audio) nell'asset di Servizi multimediali, è necessario anche un file manifesto (chiamato anche file "SMIL": Synchronized Multimedia Integration Language). Questo file indica a Servizi multimediali di Azure quali file MP4 sono disponibili per la creazione dinamica dei pacchetti e quali tenere in considerazione per il flusso audio. Un file manifesto tipico per un set di MP4 con un solo flusso audio sarà simile al seguente:

    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

Il file ism contiene in un'istruzione switch un riferimento a ogni singolo file video MP4 e, oltre a questi, anche uno o più file audio fanno riferimento a un MP4 contenente solo l'audio.

La generazione del file manifesto per il set di MP4 può essere eseguita con un componente denominato "AMS Manifest Writer". Per usarlo, trascinarlo sulla superficie e connettere i pin di output "Write Complete" dai tre componenti File Output all'input di AMS Manifest Writer. Verificare quindi di connettere l'output di AMS Manifest Writer ad Output File/Asset.

Come per gli altri componenti di output dei file, configurare il nome di output del file ism con un'espressione:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Il flusso di lavoro completato è simile al seguente:

![File MXF completato nel flusso di lavoro MP4 a velocità in bit multipla](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*File MXF completato nel flusso di lavoro MP4 a velocità in bit multipla*

## <a name="a-idmxftomultibitratemp4aencoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>Codifica di un file MXF in un MP4 a velocità in bit multipla - Progetto avanzato
Nella [procedura dettagliata sul flusso di lavoro precedente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) è stato illustrato come un solo asset di input MXF possa essere convertito in un asset di output con file MP4 a velocità in bit multipla, un file MP4 solo audio e un file manifesto da usare con la creazione dinamica dei pacchetti di Servizi multimediali di Azure.

Questa procedura dettagliata illustra come alcuni aspetti possano essere migliorati e diventare più pratici.

### <a name="a-idmxftomultibitratemp4overviewaworkflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>Panoramica del flusso di lavoro da migliorare
![Flusso di lavoro MP4 a velocità in bit multipla da migliorare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Flusso di lavoro MP4 a velocità in bit multipla da migliorare*

### <a name="a-idmxftomultibitratemp4filenamingafile-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Convenzioni di denominazione dei file
Nel flusso di lavoro precedente è stata specificata una semplice espressione come base per la generazione dei nomi file di output, ma ci sono state alcune duplicazioni: tutti i singoli componenti dei file di output hanno specificato tale espressione.

Ad esempio, il componente File Output per il primo file video è configurato con questa espressione:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Invece per il secondo video di output, l'espressione è:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Non sarebbe più lineare, meno soggetto a errori e più pratico, se fosse possibile rimuovere alcune duplicazioni e rendere il tutto più facilmente configurabile? Fortunatamente è possibile: le funzionalità delle espressioni della finestra di progettazione unite alla possibilità di creare proprietà personalizzate nella radice del flusso di lavoro offrono un livello di praticità aggiuntivo.

Si supponga che la configurazione dei nomi file si basi sulle velocità in bit dei singoli file MP4. Queste velocità in bit dovranno essere configurate in una posizione centrale (nella radice del grafico), da dove saranno accessibili per configurare e determinare la generazione dei nomi file. A questo scopo, iniziare pubblicando la proprietà della velocità in bit da entrambi i codificatori AVC nella radice del flusso di lavoro, in modo che sia accessibile da entrambe le radici oltre che dai codificatori AVC. Anche se visualizzata in due punti diversi, esiste un solo valore sottostante.

### <a name="a-idmxftomultibitratemp4publishingapublishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Pubblicazione delle proprietà dei componenti nella radice del flusso di lavoro
Aprire il primo codificatore AVC, andare alla proprietà Bitrate (kbps) e nel menu a discesa scegliere Publish.

![Pubblicazione della proprietà della velocità in bit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Pubblicazione della proprietà della velocità in bit*

Configurare la finestra di dialogo di pubblicazione per la pubblicazione nella radice del grafico del flusso di lavoro, con il nome pubblicato "video1bitrate" e il nome visualizzato leggibile "Video 1 Bitrate". Configurare un nome gruppo denominato "Streaming Bitrates" e fare clic su Publish.

![Pubblicazione della proprietà della velocità in bit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Finestra di dialogo di pubblicazione per la proprietà della velocità in bit*

Ripetere le stesse operazioni per la proprietà della velocità in bit del secondo codificatore AVC e denominarla "video2bitrate" con il nome visualizzato "Video 2 Bitrate", nello stesso gruppo personalizzato "Streaming Bitrates".

Se ora si esaminano le proprietà della radice del flusso di lavoro, si noterà il gruppo personalizzato con le due proprietà pubblicate visualizzate. Entrambe mostrano il valore della velocità in bit del rispettivo codificatore AVC.

![Proprietà della velocità in bit pubblicate nella radice del flusso di lavoro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Quando si vuole accedere a queste proprietà dal codice o da un'espressione, è possibile eseguire questa operazione:

* dal codice inline da un componente direttamente sotto la radice: node.getPropertyAsString('../video1bitrate',null)
* in un'espressione: ${ROOT_video1bitrate}

Completare ora il gruppo "Streaming Bitrates" pubblicando anche la velocità in bit della traccia audio. Nelle proprietà del codificatore AAC cercare l'impostazione Bitrate e scegliere Publish dal menu a discesa accanto a essa. Eseguire la pubblicazione nella radice del grafico con il nome "audio1bitrate" e il nome visualizzato "Audio 1 Bitrate" nel gruppo personalizzato "Streaming Bitrates".

![Finestra di dialogo di pubblicazione per la velocità in bit audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Finestra di dialogo di pubblicazione per la velocità in bit audio*

![Proprietà video e audio risultanti nella radice](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Proprietà video e audio risultanti nella radice*

Si noti che, modificando uno dei tre valori, vengono riconfigurati e modificati anche i valori nei rispettivi componenti a cui sono collegati (e da cui sono stati pubblicati).

### <a name="a-idmxftomultibitratemp4outputfilesahave-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>Fare in modo che i nomi file di output generati si basino sui valori delle proprietà pubblicati
Invece di impostare come hardcoded i nomi file generati, ora è possibile modificare l'espressione del nome file in ogni componente File Output in modo che si basi sulle proprietà delle velocità in bit pubblicate nella radice del grafico. Iniziando dal primo componente File Output, trovare la proprietà File e modificare l'espressione in questo modo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

I diversi parametri di questa espressione sono accessibili e possono essere immessi premendo il simbolo di dollaro sulla tastiera mentre è attiva la finestra dell'espressione. Uno dei parametri disponibili è la proprietà video1bitrate pubblicata prima.

![Accesso ai parametri in un'espressione](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Accesso ai parametri in un'espressione*

Eseguire le stesse operazioni per il componente File Output per il secondo video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

e per il componente File Output solo audio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Se ora si modifica la velocità in bit per uno dei file video o audio, il rispettivo codificatore verrà riconfigurato e la convenzione dei nomi file basata sulla velocità in bit verrà rispettata automaticamente.

## <a name="a-idthumbnailstomultibitratemp4aadding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Aggiunta di anteprime all'output MP4 a velocità in bit multipla
Partendo da un flusso di lavoro che genera [un output MP4 a velocità in bit multipla da un input MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), verrà ora esaminata l'aggiunta di anteprime all'output.

### <a name="a-idthumbnailstomultibitratemp4overviewaworkflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>Panoramica del flusso di lavoro a cui aggiungere le anteprime
![Flusso di lavoro MP4 a velocità in bit multipla da cui iniziare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Flusso di lavoro MP4 a velocità in bit multipla da cui iniziare*

### <a name="a-idthumbnailstomultibitratemp4withjpgaadding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Aggiunta della codifica JPG
La base della generazione delle anteprime sarà il componente JPG Encoder, usato per l'output di file JPG.

![JPG Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

Non è tuttavia possibile connettere direttamente il flusso Uncompressed Video dal componente Media File Input al codificatore JPG. È invece previsto che gli vengano passati i singoli fotogrammi. Questa operazione può essere eseguita con il componente Video Frame Gate.

![Connessione di un'attività di controllo dei fotogrammi al codificatore JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Connessione di un'attività di controllo dei fotogrammi al codificatore JPG*

L'attività di controllo dei fotogrammi consente a un fotogramma video di passare dopo un intervallo stabilito di secondi o di fotogrammi. L'intervallo e l'offset temporale con cui questa operazione viene eseguita sono configurabili nelle proprietà.

![Proprietà di Video Frame Gate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Proprietà di Video Frame Gate*

Verrà ora creata un'anteprima ogni minuto impostando la modalità su Time (seconds) e l'intervallo su 60.

### <a name="a-idthumbnailstomultibitratemp4colorspaceadealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>Gestione della conversione dello spazio colore
Anche se a questo punto può sembrare logico connettere entrambi i pin Uncompressed Video dell'attività di controllo dei fotogrammi e di Media File Input, se si esegue l'operazione viene visualizzato un avviso.

![Errore relativo allo spazio color di input](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Errore relativo allo spazio color di input*

Infatti il modo in cui le informazioni sui colori sono rappresentate nel flusso video non compresso e non elaborato originale, proveniente dal file MXF, è diverso da quello previsto per il codificatore JPG. In particolare, è previsto il flusso di un cosiddetto "spazio colore" "RGB" o "gradazioni di grigio". Al flusso video in entrata di Video Frame Gate dovrà quindi essere prima applicata una conversione relativa allo spazio colore.

Trascinare nel flusso di lavoro Color Space Converter - Intel e connetterlo all'attività di controllo dei fotogrammi.

![Connessione di un convertitore dello spazio colore](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Connessione di un convertitore dello spazio colore*

Nella finestra delle proprietà selezionare la voce BGR 24 nell'elenco Preset.

### <a name="a-idthumbnailstomultibitratemp4writingthumbnailsawriting-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Scrittura delle anteprime
Diversamente dai video MP4, il componente JPG Encoder genererà più di un file. Per gestire i file, è possibile usare un componente Scene Search JPG File Writer, che accetterà le anteprime JPG in ingresso e le trascriverà, con un numero diverso come suffisso per ogni nome file. Il numero indica in genere il numero di secondi/unità nel flusso da cui l'anteprima è stata disegnata.

![Introduzione di Scene Search JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introduzione di Scene Search JPG File Writer*

Configurare la proprietà Output Folder Path con l'espressione: ${ROOT_outputWriteDirectory}

e la proprietà Filename Prefix con:

    ${ROOT_sourceFileBaseName}_thumb_

Il prefisso determinerà come vengono denominati i file delle anteprime. Come suffisso verrà usato un numero indicante la posizione dell'anteprima nel flusso.

![Proprietà di Scene Search JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Proprietà di Scene Search JPG File Writer*

Connettere Scene Search JPG File Writer al nodo Output File/Asset.

### <a name="a-idthumbnailstomultibitratemp4errorsadetecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Rilevamento di errori in un flusso di lavoro
Connettere l'input di Color Space Converter nell'output video non compresso non elaborato. Ora effettuare un'esecuzione di test locale per il flusso di lavoro. È probabile che l'esecuzione del flusso di lavoro venga arrestata improvvisamente e il componente in cui si è verificato un errore venga indicato con un bordo rosso:

![Errore di Color Space Converter](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Errore di Color Space Converter*

Fare clic sull'icona con la "E" rossa nell'angolo in alto a destra del componente Color Space Converter per visualizzare il motivo per cui il tentativo di codifica non è riuscito.

![Finestra di dialogo dell'errore di Color Space Converter](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Finestra di dialogo dell'errore di Color Space Converter*

Come si può notare, lo standard dello spazio colore in entrata per Color Space Converter deve essere rec601 per la conversione richiesta di YUV in RGB. Sembra che il flusso non indichi rec601. Rec 601 è uno standard per la codifica dei segnali video analogici interlacciati nel formato video digitale. Specifica un'area attiva che copre 720 campioni di luminanza e 360 campioni di crominanza per linea. Il sistema di codifica dei colori è noto come YCbCr 4:2:2.

Per risolvere il problema, si indicherà nei metadati del flusso che si sta usando contenuto rec601. A questo scopo, verrà usato un componente Video Data Type Updater, che verrà inserito tra l'origine non elaborata e il componente di conversione dello spazio colore. Questo strumento di aggiornamento del tipo di dati consente l'aggiornamento manuale di alcune proprietà del tipo di dati video. Configurarlo in modo che Color Space Standard indichi "Rec 601". In questo modo Video Data Type Updater contrassegnerà il flusso con lo spazio colore "Rec 601" se non è ancora stato definito alcuno spazio colore. Eseguirà l'override dei metadati esistenti, solo se è stata selezionata la casella di controllo Override.

![Aggiornamento di Color Space Standard in Data Type Updater](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aggiornamento di Color Space Standard in Data Type Updater*

### <a name="a-idthumbnailstomultibitratemp4finishafinished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Flusso di lavoro completato
Ora che il flusso di lavoro è stato completato, effettuare un'altra esecuzione di test per assicurarsi che vengano superati.

![Flusso di lavoro completato per l'output di più MP4 con anteprime](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Flusso di lavoro completato per l'output di più MP4 con anteprime*

## <a name="a-idtimebasedtrimatime-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Taglio basato sull'ora dell'output MP4 a velocità in bit multipla
Partendo da un flusso di lavoro che genera [un output MP4 a velocità in bit multipla da un input MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), verrà ora esaminato il taglio del video di origine basato su timestamp.

### <a name="a-idtimebasedtrimstartaworkflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Panoramica del flusso di lavoro a cui iniziare ad aggiungere il taglio
![Flusso di lavoro iniziale a cui aggiungere il taglio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Flusso di lavoro iniziale a cui aggiungere il taglio*

### <a name="a-idtimebasedtrimusestreamtrimmerausing-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>Uso di Stream Trimmer
Il componente Stream Trimmer consente anche di tagliare l'inizio e la fine di un flusso di input in base alle informazioni sugli intervalli (secondi, minuti...). Il trimmer non supporta il taglio basato su fotogramma.

![Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream Trimmer*

Invece di collegare direttamente i codificatori AVC e Speaker Position Assigner a Media File Input, tra di essi verrà inserito Stream Trimmer (uno per il segnale video e uno per il segnale audio con interleave).

![Inserire Stream Trimmer in mezzo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Inserire Stream Trimmer in mezzo*

Ora il trimmer verrà configurato per poter elaborare solo il video e l'audio tra 15 secondi e 60 secondi nel video.

Andare alle proprietà di Video Stream Trimmer e configurare entrambe le proprietà Start Time (15 secondi) ed End Time (60 secondi). Entrambi i trimmer audio e video verranno pubblicati nella radice del flusso di lavoro per verificare che siano sempre configurati sugli stessi valori di inizio e di fine.

![Pubblicare la proprietà Start Time da Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Pubblicare la proprietà Start Time da Stream Trimmer*

![Finestra di dialogo Publish Property per Start Time](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Finestra di dialogo Publish Property per Start Time*

![Finestra di dialogo Publish Property per End Time](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Finestra di dialogo Publish Property per End Time*

Se ora si osserva la radice del flusso di lavoro, si noterà che entrambe le proprietà sono ben visualizzate e configurabili direttamente.

![Proprietà pubblicate disponibili nella radice](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Proprietà pubblicate disponibili nella radice*

Aprire ora le proprietà di taglio dal trimmer audio e configurare sia l'ora di inizio che quella di fine con un'espressione che faccia riferimento alle proprietà pubblicate nella radice del flusso di lavoro.

Per l'ora di inizio del taglio dell'audio:

    ${ROOT_TrimmingStartTime}

e per l'ora di fine:

    ${ROOT_TrimmingEndTime}

### <a name="a-idtimebasedtrimfinishafinished-workflow"></a><a id="time_based_trim_finish"></a>Flusso di lavoro completato
![Flusso di lavoro completato](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Flusso di lavoro completato*

## <a name="a-idscriptingaintroducing-the-scripted-component"></a><a id="scripting"></a>Introduzione al componente con script
I componenti con script possono eseguire script arbitrari durante le fasi di esecuzione del flusso di lavoro. Possono essere eseguiti quattro diversi script, tutti con caratteristiche specifiche e con la propria posizione nel ciclo di vita del flusso di lavoro:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

La documentazione del componente con script li illustra più dettagliatamente. Nella [sezione seguente](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), il componente di scripting **realizeScript** viene usato per costruire un file xml con un elenco di clip non appena il flusso di lavoro viene avviato. Questo script viene chiamato durante la configurazione del componente, che viene eseguita solo una volta nel ciclo di vita.

### <a name="a-idscriptinghelloworldascripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Scripting in un flusso di lavoro: hello world
Trascinare un componente con script sull'area di progettazione e rinominarlo (ad esempio, "SetClipListXML").

![Aggiunta di un componente con script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Aggiunta di un componente con script*

Quando si esaminano le proprietà del componente con script, vengono visualizzati i quattro diversi tipi di script, tutti configurabili in uno script diverso.

![Proprietà del componente con script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Proprietà del componente con script*

Cancellare il contenuto di processInputScript e aprire l'editor per realizeScript. La configurazione è stata eseguita ed è possibile avviare lo script.

Gli script vengono scritti in Groovy, un linguaggio di scripting compilato in modo dinamico per la piattaforma Java, che mantiene la compatibilità con Java. La maggior parte del codice Java è effettivamente codice Groovy valido.

Verrà ora scritto un semplice script di Groovy hello world nel contesto di realizeScript. Immettere nell'editor quanto segue:

    node.log("hello world");

Ora effettuare un'esecuzione di test locale. Dopo questa esecuzione, esaminare (nella scheda System del componente con script) la proprietà Logs.

![Output di log hello world](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Output di log hello world*

L'oggetto nodo su cui viene chiamato il metodo log, fa riferimento al "nodo" corrente, ovvero al componente in cui si sta creando lo script. Ogni componente ha di per sé la possibilità di creare l'output dei dati di registrazione, disponibili nella scheda System. In questo caso, viene creato l'output del valore letterale stringa "hello world". È importante comprendere che questo si rivela un prezioso strumento di debug perché offre informazioni dettagliate sulle operazioni effettivamente eseguite dallo script.

Dall'ambiente di scripting è anche possibile accedere alle proprietà degli altri componenti. Provare a eseguire quanto segue:

    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

La finestra dei log visualizzerà quanto segue:

![Output dei log per l'accesso ai percorsi dei nodi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Output dei log per l'accesso ai percorsi dei nodi*

## <a name="a-idframebasedtrimaframe-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Taglio basato sul fotogramma dell'output MP4 a velocità in bit multipla
Partendo da un flusso di lavoro che genera [un output MP4 a velocità in bit multipla da un input MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), verrà ora esaminato il taglio del video di origine basato sui conteggi dei fotogrammi.

### <a name="a-idframebasedtrimstartablueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>Panoramica del progetto a cui iniziare ad aggiungere il taglio
![Flusso di lavoro a cui iniziare ad aggiungere il taglio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Flusso di lavoro a cui iniziare ad aggiungere il taglio*

### <a name="a-idframebasedtrimcliplistausing-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>Uso di Clip List XML
In tutte le esercitazioni precedenti sul flusso di lavoro è stato usato il componente Media File Input come origine di input video. Per questo specifico scenario, si userà invece il componente Clip List Source. Si noti che questo non è il modo migliore di procedere. Usare Clip List Source solo quando è effettivamente necessario (come nel caso seguente, in cui si usano le funzionalità di taglio dell'elenco di clip).

Per passare da Media File Input a Clip List Source, trascinare il componente Clip List Source sull'area di progettazione e connettere il pin Clip List XML al nodo Clip List XML di Progettazione flussi di lavoro. Clip List Source verrà popolato con i pin di output, in base al video di input. Connettere ora i pin Uncompressed Video e Uncompressed Audio da Clip List Source ai rispettivi codificatori AVC e ad Audio Stream Interleaver. Ora rimuovere Media File Input.

![Sostituire Media File Input con Clip List Source](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Sostituire Media File Input con Clip List Source*

Il componente Clip List Source accetta "Clip List XML" come input. Quando si seleziona il file di origine con cui eseguire il test in locale, questo file XML dell'elenco di clip viene automaticamente popolato.

![Proprietà Clip List XML popolata automaticamente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Proprietà Clip List XML popolata automaticamente*

Se si osserva meglio il file XML, ecco come appare:

![Finestra di dialogo di modifica dell'elenco di clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Finestra di dialogo di modifica dell'elenco di clip*

Questo, tuttavia, non rispecchia le funzionalità del file XML dell'elenco di clip. Una possibilità è quella di aggiungere un elemento "Trim" sia sotto l'origine video che sotto quella audio, in questo modo:

![Aggiunta di un elemento trim all'elenco di clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Aggiunta di un elemento trim all'elenco di clip*

Se si modifica il file XML dell'elenco di clip come indicato sopra e si esegue un test locale, si noterà che il video è stato tagliato correttamente tra i 10 e i 20 secondi.

Contrariamente a quanto accade quando si effettua un'esecuzione locale, questo stesso file XML dell'elenco di clip non avrà lo stesso effetto se applicato a un flusso di lavoro eseguito in Servizi multimediali di Azure. Quando Codificatore premium di Azure viene avviato, il file XML dell'elenco di clip viene generato di nuovo ogni volta, in base al file di input assegnato al processo di codifica. Quindi verrà purtroppo eseguito l'override delle eventuali modifiche apportate al file XML.

Per evitare che il file XML dell'elenco di clip venga cancellato quando viene avviato un processo di codifica, è possibile rigenerarlo subito dopo l'avvio del flusso di lavoro. Tali azioni personalizzate possono essere eseguite con un cosiddetto "componente con script". Per altre informazioni, vedere [Introduzione al componente con script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Trascinare un componente con script sull'area di progettazione e rinominarlo SetClipListXML.

![Aggiunta di un componente con script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Aggiunta di un componente con script*

Quando si esaminano le proprietà del componente con script, vengono visualizzati i quattro diversi tipi di script, tutti configurabili in uno script diverso.

![Proprietà del componente con script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Proprietà del componente con script*

### <a name="a-idframebasedtrimmodifycliplistamodifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>Modifica dell'elenco di clip da un componente con script
Prima di poter riscrivere il file XML dell'elenco di clip generato durante l'avvio del flusso di lavoro, sarà necessario l'accesso alla proprietà e ai contenuti del file XML. A questo scopo, è possibile usare il codice seguente:

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Elenco di clip in entrata da registrare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Elenco di clip in entrata da registrare*

Prima di tutto è necessario determinare da quale punto a quale punto si vuole tagliare il video. Per facilitare l'operazione agli utenti del flusso di lavoro meno esperti, pubblicare due proprietà nella radice del grafico. A questo scopo, fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere "Add Property":

* Prima proprietà: "ClippingTimeStart" di tipo: "TIMECODE"
* Seconda proprietà: "ClippingTimeEnd" di tipo: "TIMECODE"

![Finestra di dialogo Add Property per l'ora di inizio del ritaglio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Finestra di dialogo Add Property per l'ora di inizio del ritaglio*

![Proprietà degli orari di ritaglio pubblicate nella radice del flusso di lavoro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Proprietà degli orari di ritaglio pubblicate nella radice del flusso di lavoro*

Configurare entrambe le proprietà su un valore appropriato:

![Configurare le proprietà di inizio e di fine del ritaglio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurare le proprietà di inizio e di fine del ritaglio*

Ora dallo script è possibile accedere a entrambe le proprietà, in questo modo:

    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Finestra dei log che mostra l'inizio e la fine del ritaglio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Finestra dei log che mostra l'inizio e la fine del ritaglio*

Verranno ora analizzate le stringhe del time code in un formato più facile da usare, con una semplice espressione regolare:

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);

![Finestra dei log con l'output del time code analizzato](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Finestra dei log con l'output del time code analizzato*

Con queste informazioni a disposizione, ora è possibile modificare il file XML dell'elenco di clip per poter rispecchiare le ore di inizio e di fine per il ritaglio accurato dei fotogrammi desiderati del filmato.

![Codice di script per aggiungere elementi trim](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Codice di script per aggiungere elementi trim*

Queste modifiche sono state apportate con normali operazioni di manipolazione delle stringhe. Il file XML dell'elenco di clip modificato risultante è stato scritto di nuovo nella proprietà clipListXML nella radice del flusso di lavoro con il metodo "setProperty". La finestra dei log dopo un'altra esecuzione di test visualizzerà quanto segue:

![Registrazione dell'elenco di clip risultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registrazione dell'elenco di clip risultante*

Effettuare un'esecuzione di test per visualizzare come i flussi video e audio sono stati tagliati. Quando si effettueranno altre esecuzioni di test con valori diversi per i punti di taglio, si noterà che tali valori non verranno tenuti in considerazione perché la finestra di progettazione, diversamente dal runtime di Azure, NON esegue l'override del file XML dell'elenco di clip a ogni esecuzione. Per questo motivo solo la prima volta che si impostano i punti di entrata e di uscita il file XML viene trasformato, invece tutte le altre volte la clausola guard (if(clipListXML.indexOf("<trim>") == -1)) impedirà al flusso di lavoro di aggiungere un altro elemento trim se ne è già presente uno.

Per semplificare il test locale del flusso di lavoro, è consigliabile aggiungere un codice di manutenzione che controlla se è già presente un elemento trim. In questo caso, è possibile rimuoverlo prima di continuare a modificare il file XML con i nuovi valori. Invece di usare le normali manipolazioni di stringa, è probabilmente più sicuro eseguire questa operazione con l'analisi del modello a oggetti XML effettivo.

Prima di poter aggiungere tale codice, è tuttavia necessario aggiungere alcune istruzioni import all'inizio dello script:

    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

Ora è possibile aggiungere il codice di pulizia necessario:

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

Questo codice viene inserito esattamente sopra il punto in cui si aggiungono gli elementi trim nel file XML dell'elenco di clip.

A questo punto, è possibile eseguire e modificare il flusso di lavoro come si preferisce, applicando ogni volta le modifiche.    

### <a name="a-idframebasedtrimclippingenabledpropaadding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>Aggiunta di una pratica proprietà ClippingEnabled
Poiché non sempre è necessario usare la funzionalità di taglio, il flusso di lavoro verrà completato aggiungendo un pratico flag booleano che indica se si vuole abilitare o meno il taglio/ritaglio.

Come prima, pubblicare nella radice del flusso di lavoro una nuova proprietà denominata "ClippingEnabled" di tipo "BOOLEAN".

![Pubblicare una proprietà per l'abilitazione del ritaglio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Pubblicare una proprietà per l'abilitazione del ritaglio*

Con la semplice clausola guard seguente, è possibile controllare se il taglio è necessario e decidere se l'elenco di clip deve essere modificato o meno.

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }


### <a name="a-idcodeacomplete-code"></a><a id="code"></a>Codice completo
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }


## <a name="also-see"></a>Vedere anche
[Introduzione alla codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Come usare la codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codifica di contenuti su richiesta con Servizi multimediali di Azure](media-services-encode-asset.md#media-encoder-premium-workflow)

[Codec e formati del flusso di lavoro Premium del codificatore multimediale](media-services-premium-workflow-encoder-formats.md)

[File del flusso di lavoro di esempio](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Strumento di esplorazione di Servizi multimediali di Azure](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Nov16_HO3-->


