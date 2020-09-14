---
title: Informazioni su Video Indexer di Servizi multimediali di Azure
titleSuffix: Azure Media Services
description: Questo articolo offre una panoramica del servizio Video Indexer di Servizi multimediali di Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/11/2020
ms.author: juliako
ms.openlocfilehash: b327facc2ac9ad64cf5237ecddc3e4024f75daa6
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052138"
---
# <a name="what-is-azure-media-services-video-indexer"></a>Informazioni su Video Indexer di Servizi multimediali di Azure

[!INCLUDE [regulation](./includes/regulation.md)]

Video Indexer (VI) è la soluzione di intelligenza artificiale di Servizi multimediali di Azure e fa parte di Servizi cognitivi di Azure. Video Indexer offre la possibilità di estrarre informazioni approfondite (senza la necessità di competenze in analisi di dati o scrittura di codice) usando modelli di Machine Learning basati su più canali (vocali e visivi). È possibile personalizzare ulteriormente i modelli ed eseguirne il training. Il servizio consente di eseguire ricerche avanzate, ridurre i costi operativi, sfruttare nuove opportunità di monetizzazione e creare nuove esperienze utente con grandi archivi di video (con barriere di ingresso minime).

Per iniziare a estrarre informazioni dettagliate con Video Indexer, è necessario creare un account e caricare i video. Quando si caricano i video in Video Indexer, vengono analizzati sia gli oggetti visivi che l'audio eseguendo modelli di intelligenza artificiale diversi. Mentre Video Indexer analizza il video, le informazioni dettagliate vengono estratte dai modelli di intelligenza artificiale.

Quando si crea un account di Video Indexer e lo si connette a servizi multimediali, i file multimediali e di metadati vengono archiviati nell'account di archiviazione di Azure associato all'account di servizi multimediali. Per altre informazioni, vedere [creare un account video Indexer connesso ad Azure](connect-to-azure.md).

Il diagramma seguente è un'illustrazione e non una spiegazione tecnica del funzionamento di Video Indexer nel back-end.

![Diagramma di flusso di Video Indexer di Servizi multimediali di Azure](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Conformità, privacy e sicurezza

È importante tenere presente che è necessario usare Video Indexer in conformità a tutte le leggi applicabili e che non è possibile usare Video Indexer né qualsiasi altro servizio di Azure in un modo che violi i diritti o possa arrecare danni ad altre persone.

Prima di caricare un video o un'immagine in Video Indexer, è necessario avere tutti i diritti appropriati per il relativo uso, tra cui, se richiesto per legge, tutti i consensi necessari delle persone (se presenti) incluse nel video o nell'immagine, per l'uso, l'elaborazione e l'archiviazione dei loro dati in Video Indexer e in Azure. Alcune giurisdizioni possono imporre specifici requisiti legali per la raccolta, l'elaborazione online e l'archiviazione di determinate categorie di dati, ad esempio i dati biometrici. Prima di usare Video Indexer e Azure per l'elaborazione e l'archiviazione di dati soggetti a requisiti legali speciali, è necessario garantire la conformità a tali requisiti legali che possono essere applicabili al caso specifico.

Per informazioni su conformità, privacy e sicurezza in Video Indexer, visitare il [Centro protezione](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) Microsoft. Per informazioni sugli obblighi relativi alla privacy e sulle procedure di Microsoft per il trattamento e la conservazione dei dati, tra cui come eliminare i propri dati, vedere l'[informativa sulla privacy](https://privacy.microsoft.com/PrivacyStatement), le [condizioni per l'utilizzo dei servizi online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) e l'[addendum sull'elaborazione dati](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) di Microsoft. Usando Video Indexer, si accetta di essere vincolati dalle condizioni per l'utilizzo dei servizi online, dall'addendum sull'elaborazione dati e dall'informativa sulla privacy.

## <a name="what-can-i-do-with-video-indexer"></a>Quali operazioni si possono eseguire con Video Indexer?

Le informazioni dettagliate di Video Indexer possono essere applicate a molti scenari, tra cui:

* *Ricerca avanzata*: usare le informazioni dettagliate estratte dal video per ottimizzare l'esperienza di ricerca in un catalogo video. L'indicizzazione del testo parlato e dei visi può ad esempio consentire di usare l'esperienza di ricerca per trovare i momenti in un video in cui una persona specifica ha pronunciato determinate parole o in cui due persone vengono viste insieme. La ricerca basata su tali informazioni dettagliate estratte dai video è utile per agenzie di stampa, istituti di istruzione, operatori che trasmettono eventi live, proprietari di contenuti per l'intrattenimento, app line-of-business aziendali e in genere per qualsiasi settore in cui gli utenti abbiano l'esigenza di eseguire ricerche in un catalogo video.
* *Creazione di contenuto*: con le informazioni dettagliate che Video Indexer estrae dal contenuto è possibile creare trailer, momenti salienti, contenuti per social media o clip di notizie. I fotogrammi chiave, i marcatori di scene e i timestamp per gli aspetti di persone ed etichette rendono il processo di creazione molto più semplice e veloce e consentono di recuperare le parti del video necessarie per il contenuto da creare.
* *Accessibilità*: sia per rendere disponibili i contenuti per persone con disabilità o per distribuire il contenuto in aree diverse con lingue diverse, è possibile usare le funzionalità di trascrizione e traduzione fornite da Video Indexer in più lingue.
* *Monetizzazione*: Video Indexer può contribuire a incrementare il valore dei video. Ad esempio, i settori che si basano sui ricavi della pubblicità (mezzi di informazione, social media e così via) possono pubblicare annunci pertinenti usando le informazioni dettagliate estratte come segnali aggiuntivi inviati al server di annunci.
* *Moderazione dei contenuti*: usare i modelli di moderazione dei contenuti testuali e visivi per proteggere gli utenti da contenuti inappropriati e verificare che quelli pubblicati rispettino i valori dell'organizzazione. È possibile bloccare automaticamente determinati video o avvisare gli utenti sul contenuto.
* *Raccomandazioni*: le informazioni dettagliate dei video possono essere usate per migliorare l'engagement degli utenti evidenziando i momenti più pertinenti per loro. Contrassegnando ogni video con metadati aggiuntivi, è possibile consigliare agli utenti i video più pertinenti ed evidenziare le parti che soddisfano le loro esigenze.

## <a name="features"></a>Funzionalità

L'elenco seguente contiene le informazioni dettagliate che è possibile recuperare dai video usando i modelli di video e audio di Video Indexer:

### <a name="video-insights"></a>Informazioni dettagliate sui contenuti video

* **Rilevamento volto**: rileva e raggruppa i visi visualizzati nel video.
* **Identificazione di celebrità**: Video Indexer identifica automaticamente oltre 1 milione di celebrità, ad esempio leader mondiali, attori e attrici, atleti, ricercatori, imprenditori e leader tecnologici in tutto il mondo. I dati relativi a queste celebrità possono anche essere trovati su diversi siti Web noti, ad esempio IMDB, Wikipedia e così via.
* **Identificazione viso basata sull'account**: Video Indexer esegue il training di un modello per un account specifico. Riconosce quindi i visi nel video in base al modello sottoposto a training. Per altre informazioni, vedere [Personalizzare un modello di persona dal sito Web di Video Indexer](customize-person-model-with-website.md) e [Personalizzare un modello di persona con l'API Video Indexer](customize-person-model-with-api.md).
* **Estrazione dell'anteprima per i visi** ("viso migliore"): identifica automaticamente il viso migliore acquisito in ogni gruppo (in base a qualità, dimensioni e posizione frontale) e lo estrae come asset immagine.
* **Riconoscimento del testo visivo** (OCR): estrae il testo visualizzato nel video.
* **Moderazione del contenuto visivo**: rileva contenuto visivo per adulti e/o razzista.
* **Identificazione delle etichette**: identifica gli oggetti visivi e le azioni visualizzate.
* **Segmentazione scena**: determina quando cambia una scena del video in base a segnali visivi. Una scena raffigura un singolo evento ed è costituita da una serie di scatti consecutivi, che sono semanticamente correlati.
* **Rilevamento inquadratura**: determina quando cambia uno scatto del video in base a segnali visivi. Uno scatto è una serie di fotogrammi acquisiti dalla videocamera. Per altre informazioni, vedere [Scene, scatti e fotogrammi chiave](scenes-shots-keyframes.md).
* **Rilevamento fotogramma nero**: identifica i fotogrammi neri presentati nel video.
* **Estrazione di fotogrammi chiave**: rileva i fotogrammi chiave stabili in un video.
* **Titoli di coda a scorrimento**: identifica l'inizio e la fine dei titoli di coda a scorrimento al termine di programmi TV e film.
* **Rilevamento di caratteri animati** (anteprima): rilevamento, raggruppamento e riconoscimento di caratteri in contenuti animati tramite l'integrazione con [Visione personalizzata di Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Per altre informazioni, vedere [Rilevamento di caratteri animati](animated-characters-recognition.md).
* **Rilevamento del tipo di scatto editoriale**: assegnazione di tag agli scatti in base al tipo (ad esempio, scatto ampio o medio, primo piano, primo piano estremo, doppio scatto, più persone, esterni e interni e così via). Per altre informazioni, vedere [Rilevamento del tipo di scatto editoriale](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Informazioni dettagliate sui contenuti audio

* **Trascrizione audio**: converte la voce in testo in 12 lingue diverse e consente le estensioni. Le lingue supportate sono inglese, spagnolo, francese, tedesco, italiano, cinese mandarino, giapponese, arabo, russo, portoghese, hindi e coreano.
* **Rilevamento automatico della lingua**: identifica automaticamente la lingua parlata dominante. Le lingue supportate sono inglese, spagnolo, francese, tedesco, italiano, cinese mandarino, giapponese, russo e portoghese. Se la lingua non può essere identificata in modo affidabile, Video Indexer presuppone che la lingua parlata sia l'inglese. Per altre informazioni, vedere [Modello di identificazione della lingua](language-identification-model.md).
* **Identificazione e trascrizione del parlato multilingue** (anteprima): identifica automaticamente la lingua parlata in segmenti diversi dell'audio. Invia ogni segmento del file multimediale da trascrivere, quindi combina nuovamente la trascrizione in un'unica trascrizione unificata. Per altre informazioni, vedere [Identificare e trascrivere automaticamente contenuto multilingue](multi-language-identification-transcription.md).
* **Sottotitoli**: consente di creare sottotitoli in tre formati: VTT, TTML e SRT.
* **Elaborazione a due canali**: rileva automaticamente trascrizioni separate e le unisce in una singola sequenza temporale.
* **Riduzione del rumore**: ripulisce le registrazioni audio di telefonia o le registrazioni con rumori (in base ai filtri Skype).
* **Personalizzazione della trascrizione** (CRIS): esegue il training di modelli di riconoscimento vocale personalizzati per creare trascrizioni specifiche del settore. Per altre informazioni, vedere [Personalizzare un modello di lingua dal sito Web di Video Indexer](customize-language-model-with-website.md) e [Personalizzare un modello di lingua con l'API Video Indexer](customize-language-model-with-api.md).
* **Enumerazione dei relatori**: identifica e riconosce quale relatore ha pronunciato determinate parole e quando. È possibile rilevare sedici oratori in un unico file audio.
* **Statistiche dei relatori**: fornisce statistiche sulla percentuale di parlato dei parlanti.
* **Moderazione del contenuto testuale**: rileva testo esplicito nella trascrizione audio.
* **Effetti audio**: identifica effetti audio, come applausi, parlato e silenzio.
* **Rilevamento delle emozioni**: identifica le emozioni in base al parlato (cosa viene detto) e alla tonalità vocale (come viene detto). L'emozione potrebbe essere gioia, tristezza, rabbia o paura.
* **Traduzione**: crea traduzioni della trascrizione audio in 54 lingue diverse.

### <a name="audio-and-video-insights-multi-channels"></a>Informazioni dettagliate sui contenuti audio e video (più canali)

Quando si esegue l'indicizzazione in base a un canale, sarà disponibile il risultato parziale per tali modelli.

* **Estrazione di parole chiave**: estrae parole chiave dal parlato e dal testo visivo.
* **Estrazione di entità denominate**: estrae marchi, località e persone da parlato e testo visivo tramite elaborazione del linguaggio naturale (NLP).
* **Inferenza dell'argomento**: deduce gli argomenti principali dalle trascrizioni. È inclusa la tassonomia IPTC di secondo livello.
* **Artefatti**: estrae un ampio set di artefatti con "livello di dettaglio successivo" per ognuno dei modelli.
* **Analisi del sentiment**: identifica sentiment positivi, negativi e neutri dal parlato e dal testo visivo.

## <a name="how-can-i-get-started-with-video-indexer"></a>Come si può iniziare a usare Video Indexer?

È possibile accedere alle funzionalità di Video Indexer in tre modi:

* Portale di Video Indexer: una soluzione intuitiva che consente di valutare il prodotto, gestire l'account e personalizzare i modelli.

    Per altre informazioni sul portale, vedere [Introduzione al sito Web di Video Indexer](video-indexer-get-started.md).  

* Integrazione dell'API: tutte le funzionalità di Video Indexer sono disponibili tramite un'API REST, che consente di integrare la soluzione nelle app e nell'infrastruttura.

    Per iniziare come sviluppatore, vedere  [Usare l'API REST di Video Indexer](video-indexer-use-apis.md).

* Widget incorporabile: consente di incorporare le informazioni dettagliate, il lettore e le esperienze di editor di Video Indexer nell'app.

    Per altre informazioni, vedere  [Incorporare widget visivi nell'applicazione](video-indexer-embed-widgets.md).

Se si usa il sito Web, le informazioni dettagliate vengono aggiunte come metadati e sono visibili nel portale. Se si usano le API, le informazioni dettagliate sono disponibili come file JSON.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile iniziare a usare Video Indexer. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione al sito Web di Video Indexer](video-indexer-get-started.md).
- [Elaborare contenuti con l'API REST di Video Indexer](video-indexer-use-apis.md).
- [Incorporare widget visivi nell'applicazione](video-indexer-embed-widgets.md).
