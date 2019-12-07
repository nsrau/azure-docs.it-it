---
title: Che cos'è Video Indexer?
titleSuffix: Azure Media Services
description: Questo articolo offre una panoramica del servizio Video Indexer di servizi multimediali di Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/23/2019
ms.author: juliako
ms.openlocfilehash: a942277b5616f3e8220e7fc0fd00f7d660d76d3e
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892703"
---
# <a name="what-is-video-indexer"></a>Che cos'è Video Indexer?

Video Indexer (VI) è la soluzione di intelligenza artificiale di servizi multimediali di Azure e fa parte del marchio Servizi cognitivi di Azure. Video Indexer offre la possibilità di estrarre informazioni approfondite (senza necessità di analisi dei dati o competenze di codifica) usando modelli di apprendimento automatico basati su più canali (voce, vocals, Visual). È possibile personalizzare ulteriormente ed eseguire il training dei modelli. Il servizio consente la ricerca approfondita, riduce i costi operativi, Abilita nuove opportunità di monetizzazione e crea nuove esperienze utente su archivi di video di grandi dimensioni (con barriere di immissione minime).

Per iniziare a estrarre informazioni dettagliate con Video Indexer, è necessario creare un account e caricare i video. Quando si caricano i video in Video Indexer, analizza sia gli oggetti visivi che l'audio eseguendo diversi modelli di intelligenza artificiale. Video Indexer analizza il video, le informazioni dettagliate estratte dai modelli di intelligenza artificiale.

Il diagramma seguente è un'illustrazione e non una spiegazione tecnica del funzionamento di Video Indexer nel back-end.

![Diagramma di flusso Video Indexer di servizi multimediali di Azure](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Quali operazioni si possono eseguire con Video Indexer?

Le informazioni dettagliate di Video Indexer possono essere applicate a molti scenari, tra cui:

* *Ricerca completa*: usare le informazioni dettagliate estratte dal video per migliorare l'esperienza di ricerca in una raccolta video. Ad esempio, l'indicizzazione di parole e visi è in grado di abilitare l'esperienza di ricerca di individuare i momenti in un video in cui una persona ha parlato di determinate parole o quando due persone sono state visualizzate insieme. La ricerca basata su tali informazioni dai video è applicabile a agenzie di notizie, istituti didattici, broadcaster, proprietari di contenuti di intrattenimento, app LOB aziendali e in generale a qualsiasi settore che disponga di una videoteca in cui gli utenti devono eseguire ricerche.
* *Creazione di contenuto*: creare trailer, evidenziare Reel, contenuto di social media o clip di notizie in base alle informazioni dettagliate video Indexer estratti dal contenuto. I fotogrammi chiave, i marcatori di scene e i timestamp per le persone e l'aspetto delle etichette rendono il processo di creazione molto più semplice e veloce e consente di ottenere le parti del video necessarie per il contenuto che si sta creando.
* *Accessibilità*: se si vuole rendere disponibili i contenuti per gli utenti con particolari esigenze o se si vuole che il contenuto venga distribuito in aree diverse usando lingue diverse, è possibile usare la trascrizione e la traduzione fornite da video indexer in più lingue.
* *Monetizzazione*: video Indexer possono contribuire ad aumentare il valore dei video. Ad esempio, le industrie che si basano sui ricavi ad (notizie, media e così via) possono fornire annunci rilevanti usando le informazioni dettagliate estratte come segnali aggiuntivi al server ad.
* *Moderazione del contenuto*: usare i modelli di moderazione del contenuto visivo e testuale per proteggere gli utenti dal contenuto inappropriato e verificare che i contenuti pubblicati corrispondano ai valori dell'organizzazione. È possibile bloccare automaticamente determinati video o avvisare gli utenti sul contenuto.
* *Suggerimenti*: le informazioni dettagliate sul video possono essere usate per migliorare il coinvolgimento degli utenti evidenziando i momenti video pertinenti agli utenti. Contrassegnando ogni video con metadati aggiuntivi, è possibile consigliare agli utenti i video più rilevanti ed evidenziare le parti del video che soddisfano le proprie esigenze.

## <a name="features"></a>database elastico

L'elenco seguente mostra le informazioni che è possibile recuperare dai video usando Video Indexer modelli video e audio:

### <a name="video-insights"></a>Informazioni dettagliate sui contenuti video

* **Rilevamento volto**: rileva e raggruppa i visi visualizzati nel video.
* **Identificazione delle celebrità**: video Indexer identifica automaticamente più di 1 milione celebrità, ad esempio leader mondiali, attori, attrici, atleti, ricercatori, aziende e leader della tecnologia in tutto il mondo. I dati relativi a queste celebrità sono disponibili anche in vari siti Web (IMDB, Wikipedia e così via).
* **Identificazione viso basata sull'account**: Video Indexer esegue il training di un modello per un account specifico. Riconosce quindi i visi nel video in base al modello sottoposto a training. Per altre informazioni, vedere [personalizzare un modello di persona dal sito web video Indexer](customize-person-model-with-website.md) e [personalizzare un modello di persona con l'API video Indexer](customize-person-model-with-api.md).
* **Estrazione di anteprime per visi** ("Best Face"): identifica automaticamente il volto acquisito migliore in ogni gruppo di visi (in base alla qualità, alle dimensioni e alla posizione frontale) e lo estrae come asset di immagine.
* **Riconoscimento del testo visivo** (OCR): estrae il testo visualizzato visivamente nel video.
* **Moderazione del contenuto visivo**: rileva contenuto visivo per adulti e/o razzista.
* **Identificazione delle etichette**: identifica gli oggetti visivi e le azioni visualizzate.
* **Segmentazione della scena**: determina quando una scena cambia in video in base a segnali visivi. Una scena raffigura un singolo evento ed è costituita da una serie di scatti consecutivi, che sono semanticamente correlati.
* **Rilevamento shot**: determina quando viene modificato il video in base ai segnali visivi. Un'immagine è una serie di frame ricavati dalla stessa fotocamera di movimento. Per altre informazioni, vedere [scene, scatti e fotogrammi chiave](scenes-shots-keyframes.md).
* **Rilevamento fotogramma nero**: identifica i fotogrammi neri presentati nel video.
* **Estrazione di fotogrammi chiave**: rileva i fotogrammi chiave stabili in un video.
* **Crediti in sequenza**: identifica l'inizio e la fine dei crediti in sequenza alla fine della TV e dei film.
* **Rilevamento dei caratteri animati** (anteprima): rilevamento, raggruppamento e riconoscimento di caratteri nel contenuto animato tramite l'integrazione con la [visione personalizzata di servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Per ulteriori informazioni, vedere [rilevamento dei caratteri animati](animated-characters-recognition.md).
* **Rilevamento del tipo di ripresa editoriale**: assegnazione di tag in base al tipo (ad esempio, wide shot, medium, close up, Extreme close up, two shot, multiple people, outdoor e indoor e così via). Per ulteriori informazioni, vedere il [rilevamento del tipo di ripresa editoriale](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Informazioni dettagliate sui contenuti audio

* **Rilevamento automatico della lingua**: identifica automaticamente la lingua parlata dominante. Le lingue supportate sono inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), giapponese, russo e portoghese (Brasile). Se la lingua non può essere identificata in tutta sicurezza, Video Indexer presuppone che la lingua parlata sia l'inglese. Per altre informazioni, vedere [modello di identificazione della lingua](language-identification-model.md).
* **Riconoscimento vocale e trascrizione** in più lingue (anteprima): identifica automaticamente la lingua parlata in segmenti diversi dall'audio. Invia ogni segmento del file multimediale da trascrivere, quindi combina nuovamente la trascrizione a una trascrizione unificata. Per altre informazioni, vedere [identificare e trascrivere automaticamente contenuti multilingue](multi-language-identification-transcription.md).
* **Trascrizione audio**: converte la voce in testo in 12 lingue e consente le estensioni. Le lingue supportate sono inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), giapponese, arabo, russo, Portoghese brasiliano, Hindi e coreano.
* **Sottotitoli codificati**: consente di creare sottotitoli in tre formati: VTT, TTML, SRT.
* **Elaborazione di due canali**: rileva automaticamente la trascrizione e le unioni separate a una singola sequenza temporale.
* **Riduzione del rumore**: Cancella la telefonia audio o le registrazioni rumorose (basate sui filtri Skype).
* **Trascrizione** (Cris): consente di eseguire il training di un riconoscimento vocale personalizzato nei modelli di testo per creare trascrizioni specifiche del settore. Per altre informazioni, vedere [personalizzare un modello di linguaggio dal sito Web di video Indexer](customize-language-model-with-website.md) e [personalizzare un modello di linguaggio con le API video Indexer](customize-language-model-with-api.md).
* **Speaker Enumeration (enumerazione altoparlante**): esegue il mapping e riconosce quale altoparlante ha parlato di quali parole e quando.
* **Statistiche del relatore**: fornisce statistiche relative ai rapporti vocali degli altoparlanti.
* **Moderazione del contenuto testuale**: rileva testo esplicito nella trascrizione audio.
* **Effetti audio**: identifica gli effetti audio come gli applausi a mano, il riconoscimento vocale e il silenzio.
* **Rilevamento**emozioni: identifica le emozioni in base al parlato (cosa viene detto) e alla tonalità vocale (come viene detto). L'emozione può essere gioia, tristezza, rabbia o timore.
* **Traduzione**: crea traduzioni della trascrizione audio in 54 lingue diverse.

### <a name="audio-and-video-insights-multi-channels"></a>Informazioni dettagliate audio e video (canali multicanale)

Quando si esegue l'indicizzazione in base a un canale, saranno disponibili risultati parziali per tali modelli.

* **Parole chiave estrazione**: estrae parole chiave da sintesi vocale e testo visivo.
* **Estrazione di entità denominate**: estrae marchi, posizioni e persone da testo vocale e visivo tramite l'elaborazione del linguaggio naturale (PNL).
* **Inferenza degli argomenti**: deriva gli argomenti principali dalle trascrizioni. È inclusa la tassonomia IPTC di secondo livello.
* **Artefatti**: estrae un ampio set di artefatti con "livello di dettaglio successivo" per ognuno dei modelli.
* **Analisi del sentiment**: identifica sentiment positivi, negativi e neutri dal parlato e dal testo visivo.

## <a name="how-can-i-get-started-with-video-indexer"></a>Come è possibile iniziare a usare Video Indexer?

È possibile accedere alle funzionalità di Video Indexer in tre modi:

* Portale di Video Indexer: soluzione facile da usare che consente di valutare il prodotto, gestire l'account e personalizzare i modelli.

    Per ulteriori informazioni sul portale, vedere [la pagina relativa all'introduzione al sito web video Indexer](video-indexer-get-started.md).  

* Integrazione dell'API: tutte le funzionalità di Video Indexer sono disponibili tramite un'API REST, che consente di integrare la soluzione nelle app e nell'infrastruttura.

    Per iniziare a usare gli sviluppatori, vedere [usare video INDEXER API REST](video-indexer-use-apis.md).

* Widget incorporabile: consente di incorporare le esperienze Video Indexer Insights, Player ed editor nell'app.

    Per altre informazioni, vedere [incorporare i widget visivi nell'applicazione](video-indexer-embed-widgets.md).

Se si usa il sito Web, le informazioni dettagliate vengono aggiunte come metadati e sono visibili nel portale. Se si usano le API, le informazioni dettagliate sono disponibili come file JSON.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile iniziare a usare Video Indexer. Per altre informazioni, vedere gli articoli seguenti:

- [Inizia a usare il sito web video Indexer](video-indexer-get-started.md).
- [Elaborare il contenuto con video INDEXER API REST](video-indexer-use-apis.md).
- [Incorporare i widget visivi nell'applicazione](video-indexer-embed-widgets.md).
