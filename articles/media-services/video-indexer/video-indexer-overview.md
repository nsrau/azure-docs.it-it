---
title: Informazioni su Video Indexer di Servizi multimediali di Azure
titleSuffix: Azure Media Services
description: Questo argomento offre una panoramica del servizio Video Indexer di servizi multimediali di Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/17/2019
ms.author: juliako
ms.openlocfilehash: d346f68534a9fdbc286cd5521c00d81c2a5fec78
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203548"
---
# <a name="what-is-video-indexer"></a>Informazioni su Video Indexer

Servizi multimediali di Azure Video Indexer è una soluzione di intelligenza artificiale basata su servizi cognitivi di Azure. Video Indexer offre la possibilità di estrarre informazioni approfondite (senza necessità di analisi dei dati o competenze di codifica) usando modelli di apprendimento automatico basati su più canali (voce, vocals, Visual). È possibile personalizzare ulteriormente ed eseguire il training dei modelli. Il servizio consente la ricerca approfondita, riduce i costi operativi, offre nuove opportunità di monetizzazione, nuove esperienze utente in archivi di video di grandi dimensioni (con barriere di immissione minime). 

Per iniziare a estrarre informazioni dettagliate con Video Indexer, è necessario creare un account e caricare i video. Quando si caricano i video in Video Indexer, analizza sia gli oggetti visivi che l'audio eseguendo diversi modelli di intelligenza artificiale. Come Video Indexer analizza il video, le informazioni dettagliate estratte dai modelli.

![Diagramma di flusso](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Quali operazioni si possono eseguire con Video Indexer?

Le informazioni dettagliate di Video Indexer possono essere applicate a molti scenari, tra cui:

* *Ricerca completa* : usare le informazioni dettagliate estratte dal video per migliorare l'esperienza di ricerca in una raccolta video. Ad esempio, l'indicizzazione di parole e visi è in grado di abilitare l'esperienza di ricerca di individuare i momenti in un video in cui una persona ha parlato di determinate parole o quando due persone sono state visualizzate insieme. La ricerca basata su tali informazioni dettagliate estratte dai video è utile per agenzie di stampa, istituti di istruzione, operatori che trasmettono eventi live, proprietari di contenuti per l'intrattenimento, app line-of-business aziendali e in genere per qualsiasi settore che ha un catalogo video in cui gli utenti devono eseguire ricerche.
* *Creazione di contenuto*: creare trailer, evidenziare i rulli, il contenuto dei social media o le clip di notizie in base alle informazioni video Indexer estratti dal contenuto. I fotogrammi chiave, i marcatori di scene e i timestamp per le persone e l'aspetto delle etichette rendono il processo di creazione molto più semplice e veloce e consente di ottenere direttamente le parti interessanti del video necessarie per il contenuto che si sta creando.
* *Accessibilità*: se si vuole rendere disponibili i contenuti per gli utenti con particolari esigenze o se si vuole che il contenuto venga distribuito in aree diverse usando lingue diverse, è possibile usare la trascrizione e la traduzione fornite dal video indicizzatore in più lingue.
* *Monetizzazione*: Video Indexer possono contribuire ad aumentare il valore dei video. Ad esempio, i settori che si basano sui ricavi ad (ad esempio, le notizie, i social media e così via) possono fornire annunci rilevanti usando le informazioni dettagliate estratte come segnali aggiuntivi al server ad.
* *Moderazione del contenuto*: usare i modelli di moderazione del contenuto visivo e testuale per proteggere gli utenti dal contenuto inappropriato e verificare che i contenuti pubblicati corrispondano ai valori dell'organizzazione. È possibile bloccare automaticamente determinati video o avvisare gli utenti in merito al contenuto. 
* *Raccomandazioni*: Il video Insights può essere usato per migliorare l'engagement degli utenti evidenziando i momenti video rilevanti per gli utenti. Contrassegnando ogni video con metadati aggiuntivi, è possibile consigliare agli utenti i video più rilevanti ed evidenziare la parte del video che soddisfa le proprie esigenze. 

## <a name="features"></a>Funzionalità

Di seguito è riportato l'elenco di informazioni dettagliate che è possibile recuperare dai video usando Video Indexer modelli video e audio:

### <a name="video-insights"></a>Informazioni dettagliate sui contenuti video

* **Rilevamento volto**: rileva e raggruppa i visi visualizzati nel video.
* **Identificazione di celebrità**: Video Indexer identifica automaticamente più di 1 milione celebrità, ad esempio leader mondiali, attori, attrici, atleti, ricercatori, aziende e leader della tecnologia in tutto il mondo. I dati relativi a queste celebrità possono anche essere trovati su diversi siti Web noti, ad esempio IMDB e Wikipedia.
* **Identificazione viso basata sull'account**: Video Indexer esegue il training di un modello per un account specifico. Riconosce quindi i visi nel video in base al modello sottoposto a training. Per altre informazioni, vedere [personalizzare un modello di persona dal sito web video Indexer](customize-person-model-with-website.md) e [personalizzare un modello di persona con l'API video Indexer](customize-person-model-with-api.md).
* **Estrazione dell'anteprima per i visi** ("viso migliore"): identifica automaticamente il viso migliore acquisito in ogni gruppo di visi (in base a qualità, dimensioni e posizione frontale) e lo estrae come risorsa immagine.
* **Riconoscimento del testo visivo** (OCR): estrae il testo visualizzato nel video.
* **Moderazione del contenuto visivo**: rileva contenuto visivo per adulti e/o razzista.
* **Identificazione delle etichette**: identifica gli oggetti visivi e le azioni visualizzate.
* **Segmentazione della scena**: determina quando una scena cambia in video in base a segnali visivi. Una scena raffigura un singolo evento ed è costituita da una serie di scatti consecutivi, che sono semanticamente correlati.
* **Rilevamento shot**: determina quando viene modificato il video in base ai segnali visivi. Un'immagine è una serie di frame ricavati dalla stessa fotocamera di movimento. Per altre informazioni, vedere scene, scatti e fotogrammi chiave.
* **Rilevamento fotogramma nero**: identifica i fotogrammi neri presentati nel video.
* **Estrazione di fotogrammi chiave**: rileva i fotogrammi chiave stabili in un video.
* **Crediti in sequenza**: identificare l'inizio e la fine dei crediti in corso alla fine della TV e dei film.
* **Rilevamento di caratteri animati** (anteprima): rilevamento, raggruppamento e riconoscimento dei caratteri nel contenuto animato tramite l'integrazione con la [visione personalizzata di servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Per ulteriori informazioni, vedere [rilevamento dei caratteri animati](animated-characters-recognition.md).
* **Rilevamento del tipo di ripresa editoriale**: assegnazione di tag in base al tipo (ad esempio, grandangolo, medio colpo, chiusura, estrema chiusura, due colpi, più persone, esterno e interno e così via). Per ulteriori informazioni, vedere il [rilevamento del tipo di ripresa editoriale](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Informazioni dettagliate sui contenuti audio

* **Rilevamento automatico della lingua**: identifica automaticamente la lingua parlata dominante. Le lingue supportate sono inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), giapponese, russo e portoghese brasiliano. Se la lingua non può essere identificata in tutta sicurezza, Video Indexer presuppone che la lingua parlata sia l'inglese. Per altre informazioni, vedere [modello di identificazione della lingua](language-identification-model.md).
* **Identificazione e trascrizione di riconoscimento vocale multilingue** (anteprima): Identifica automaticamente la lingua parlata in segmenti diversi dall'audio, inviando ogni segmento del file multimediale da trascrivere e combinando di nuovo la trascrizione con una trascrizione unificata. Per altre informazioni, vedere [identificare e trascrivere automaticamente contenuti multilingue](multi-language-identification-transcription.md).
* **Trascrizione audio**: converte la voce in testo in 12 lingue diverse e consente le estensioni. Le lingue supportate sono inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), giapponese, arabo, russo, Portoghese brasiliano, Hindi e coreano.
* **Sottotitoli**: consente di creare sottotitoli in tre formati: VTT, TTML e SRT.
* **Elaborazione a due canali**: rileva automaticamente trascrizioni separate e le unisce in una singola sequenza temporale.
* **Riduzione del rumore**: ripulisce le registrazioni audio di telefonia o le registrazioni con rumori (in base ai filtri Skype).
* **Personalizzazione della trascrizione** (CRIS): Consente di eseguire il training del riconoscimento vocale personalizzato nei modelli di testo per creare trascrizioni specifiche del settore. Per altre informazioni, vedere [personalizzare un modello di linguaggio dal sito Web di video Indexer](customize-language-model-with-website.md) e [personalizzare un modello di linguaggio con le API video Indexer](customize-language-model-with-api.md).
* **Enumerazione dei relatori**: identifica e riconosce quale relatore ha pronunciato determinate parole e quando.
* **Statistiche dei relatori**: Fornisce le statistiche relative ai rapporti vocali degli altoparlanti.
* **Moderazione del contenuto testuale**: rileva testo esplicito nella trascrizione audio.
* **Effetti audio**: identifica effetti audio, come applausi, parlato e silenzio.
* **Rilevamento delle emozioni**: Identifica le emozioni in base al parlato (cosa viene detto) e alla tonalità vocale (come viene detto). L'emozione può essere gioia, tristezza, rabbia o timore.
* **Traduzione**: crea traduzioni della trascrizione audio in 54 lingue diverse.

### <a name="audio-and-video-insights-multi-channels"></a>Informazioni dettagliate sui contenuti audio e video (più canali)

Quando si esegue l'indicizzazione in base a un canale, sarà disponibile il risultato parziale per tali modelli

* **Estrazione di parole chiave**: estrae parole chiave dal parlato e dal testo visivo.
* **Estrazione entità denominate**: Estrae le marche, le località e gli utenti da testo vocale e visivo tramite la PNL (Natural Language Processing).
* **Inferenza dell'argomento**: deduce gli argomenti principali dalle trascrizioni. È inclusa la tassonomia IPTC di primo livello.
* **Artefatti**: estrae un ampio set di artefatti con "livello di dettaglio successivo" per ognuno dei modelli.
* **Analisi del sentiment**: identifica sentiment positivi, negativi e neutri dal parlato e dal testo visivo.

## <a name="how-can-i-get-started-with-video-indexer"></a>Come è possibile iniziare a usare Video Indexer?

È possibile accedere alle funzionalità di Video Indexer usando tre modi diversi: 

* Portale di Video Indexer: soluzione facile da usare che consente di valutare il prodotto, gestire l'account e personalizzare i modelli. 

    Per ulteriori informazioni sul portale, vedere [la pagina relativa all'introduzione al sito web video Indexer](video-indexer-get-started.md).  
* Integrazione dell'API: tutte le funzionalità di Video Indexer sono disponibili tramite un'API REST per consentire l'integrazione della soluzione nelle applicazioni e nell'infrastruttura. 

    Per iniziare a usare gli sviluppatori, vedere [usare video INDEXER API REST](video-indexer-use-apis.md). 
* Widget Emendable: consente di incorporare video Indexer Insights, Player e l'esperienza dell'editor nell'applicazione. 

    Per altre informazioni, vedere [incorporare i widget visivi nell'applicazione](video-indexer-embed-widgets.md). 

Se si usa il sito Web, le informazioni dettagliate vengono aggiunte come metadati e visibili nel portale. Se si usano le API, le informazioni dettagliate sono disponibili come file JSON. 

## <a name="next-steps"></a>Passaggi successivi

È ora possibile iniziare a usare Video Indexer. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione al sito Web di Video Indexer](video-indexer-get-started.md)
- [Elaborare contenuti con l'API REST Video Indexer](video-indexer-use-apis.md)
- [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione)
