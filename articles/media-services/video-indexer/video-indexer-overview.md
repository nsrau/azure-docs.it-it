---
title: Informazioni su Video Indexer
titleSuffix: Azure Media Services
description: Questo articolo offre una panoramica del servizio Indicizzatore Video di Servizi multimediali di Azure.This article gives an overview of the Azure Media Services Video Indexer service.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: juliako
ms.openlocfilehash: 1c7156da171a101f04db9f83df587f5b8bc71e8f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687105"
---
# <a name="what-is-video-indexer"></a>Informazioni su Video Indexer

L'indicizzatore video (VI) è la soluzione di applicazione di Servizi multimediali di Azure e fa parte del marchio Servizi cognitivi di Azure.Video Indexer (VI) is the Azure Media Services AI solution and part of the Azure Cognitive Services brand. L'indicizzatore video offre la possibilità di estrarre informazioni approfondite (senza necessità di analisi dei dati o abilità di codifica) utilizzando modelli di apprendimento automatico basati su più canali (voce, voce, visuale). È possibile personalizzare e addestrare ulteriormente i modelli. Il servizio consente una ricerca approfondita, riduce i costi operativi, consente nuove opportunità di monetizzazione e crea nuove esperienze utente su grandi archivi di video (con basse barriere di ingresso).

Per iniziare a estrarre informazioni dettagliate con Video Indexer, devi creare un account e caricare video. Quando carichi i tuoi video su Video Indexer, analizza sia gli oggetti visivi che l'audio eseguendo diversi modelli di IA. Quando L'indicizzatore video analizza il video, le informazioni dettagliate estratte dai modelli di ia-a.

Il diagramma seguente è un'illustrazione e non una spiegazione tecnica del funzionamento dell'indicizzatore video nel back-end.

![Diagramma di flusso dell'indicizzatore video di Servizi multimediali di AzureAzure Media Services Video Indexer flow diagram](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Conformità, privacy e sicurezza

Come promemoria importante, è necessario rispettare tutte le leggi applicabili nell'uso dell'indicizzatore video e non è possibile usare l'indicizzatore video o qualsiasi servizio di Azure in modo da violare i diritti di altri utenti o che potrebbe essere dannoso per altri utenti.

Prima di caricare qualsiasi video/immagine nell'indicizzatore video, è necessario disporre di tutti i diritti appropriati per utilizzare il video/immagine, inclusi, se richiesto dalla legge, tutti i consensi necessari da parte di individui (se presenti) nel video/immagine, per l'uso, l'elaborazione e l'archiviazione dei dati in Video Indexer e Azure. Alcune giurisdizioni possono imporre particolari requisiti legali per la raccolta, l'elaborazione online e l'archiviazione di determinate categorie di dati, come i dati biometrici. Prima di usare Video Indexer e Azure per l'elaborazione e l'archiviazione di tutti i dati soggetti a requisiti legali speciali, è necessario garantire la conformità con tali requisiti legali applicabili all'utente.

Per informazioni su conformità, privacy e sicurezza in Video Indexer, visitare il [Centro protezione](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)Microsoft . Per gli obblighi di Microsoft in materia di privacy, la gestione e le procedure di conservazione dei dati, incluse le modalità di eliminazione dei dati, consultare [l'Informativa sulla privacy](https://privacy.microsoft.com/PrivacyStatement)di Microsoft, le [Condizioni](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) per i servizi online ("OST") e [l'Aggiunta del trattamento dei dati](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Utilizzando Video Indexer, l'utente accetta di essere vincolato dall'OST, dal DPA e dall'Informativa sulla privacy.

## <a name="what-can-i-do-with-video-indexer"></a>Quali operazioni si possono eseguire con Video Indexer?

Le informazioni dettagliate dell'indicizzatore video possono essere applicate a molti scenari, tra cui:Video Indexer's insights to many scenarios, among them are:

* *Ricerca approfondita*: Utilizza le informazioni dettagliate estratte dal video per migliorare l'esperienza di ricerca in una libreria video. Ad esempio, l'indicizzazione di parole pronunciate e volti può consentire l'esperienza di ricerca di trovare momenti in un video in cui una persona ha pronunciato determinate parole o quando due persone sono state viste insieme. La ricerca basata su tali approfondimenti dai video è applicabile alle agenzie di stampa, agli istituti di istruzione, alle emittenti, ai proprietari di contenuti di intrattenimento, alle app business-SO aziendali e in generale a qualsiasi settore che disponga di una libreria video in base alla quali gli utenti devono eseguire ricerche.
* *Creazione di contenuti:* crea trailer, evidenzia clip, contenuti di social media o clip di notizie in base alle informazioni estratte video dell'indicizzatore dai tuoi contenuti. I fotogrammi chiave, i marcatori delle scene e i timestamp per le persone e le immagini delle etichette rendono il processo di creazione molto più semplice e più semplice e consentono di accedere alle parti del video necessarie per il contenuto che si sta creando.
* *Accessibilità*: Se si desidera rendere i contenuti disponibili per le persone con disabilità o se si desidera che i contenuti vengano distribuiti in diverse aree geografiche utilizzando lingue diverse, è possibile utilizzare la trascrizione e la traduzione fornite dall'indicizzatore video in più lingue.
* *Monetizzazione*: Video Indexer può contribuire ad aumentare il valore dei video. Ad esempio, i settori che si basano sulle entrate pubblicitarie (media di notizie, social media e così via) possono fornire annunci pertinenti utilizzando le informazioni dettagliate estratte come segnali aggiuntivi all'ad server.
* *Moderazione dei contenuti*: utilizzare modelli di moderazione dei contenuti testuali e visivi per proteggere gli utenti da contenuti inappropriati e verificare che il contenuto pubblicato corrisponda ai valori dell'organizzazione. Puoi bloccare automaticamente determinati video o avvisare gli utenti del contenuto.
* *Raccomandazioni*: Le informazioni video possono essere utilizzate per migliorare il coinvolgimento degli utenti evidenziando i momenti video rilevanti per gli utenti. Taggando ogni video con metadati aggiuntivi, puoi consigliare agli utenti i video più pertinenti ed evidenziare le parti del video che corrisponderanno alle loro esigenze.

## <a name="features"></a>Funzionalità

L'elenco seguente mostra le informazioni dettagliate che puoi recuperare dai tuoi video utilizzando i modelli video e audio dell'indicizzatore video:

### <a name="video-insights"></a>Informazioni dettagliate sui contenuti video

* **Rilevamento volto**: rileva e raggruppa i visi visualizzati nel video.
* **Identificazione delle celebrità**: Video Indexer identifica automaticamente oltre 1 milione di celebrità, come leader mondiali, attori, attrici, atleti, ricercatori, aziende e leader tecnologici in tutto il mondo. I dati su queste celebrità possono anche essere trovati su vari siti web (IMDB, Wikipedia, e così via).
* **Identificazione viso basata sull'account**: Video Indexer esegue il training di un modello per un account specifico. Riconosce quindi i volti nel video in base al modello sottoposto a training. Per ulteriori informazioni, consultate [Personalizzare un modello Person dal sito Web Dell'indicizzatore video](customize-person-model-with-website.md) e Personalizzare un modello Person con [l'API Video Indexer.](customize-person-model-with-api.md)
* **Estrazione delle miniature per le facce** ("faccia migliore"): identifica automaticamente il volto acquisito migliore in ogni gruppo di facce (in base alla qualità, alle dimensioni e alla posizione frontale) e lo estrae come risorsa immagine.
* **Riconoscimento del testo visivo** (OCR): estrae il testo visualizzato visivamente nel video.
* **Moderazione del contenuto visivo**: rileva contenuto visivo per adulti e/o razzista.
* **Identificazione delle etichette**: identifica gli oggetti visivi e le azioni visualizzate.
* **Segmentazione scena**: Determina quando una scena cambia nel video in base a segnali visivi. Una scena descrive un singolo evento ed è composta da una serie di scatti consecutivi, che sono semanticamente correlati.
* **Rilevamento ripresa**: Determina quando una ripresa cambia nel video in base a segnali visivi. Uno scatto è una serie di fotogrammi presi dalla stessa macchina fotografica. Per ulteriori informazioni, consultate [Scene, inquadrature e fotogrammi chiave.](scenes-shots-keyframes.md)
* **Rilevamento fotogramma nero**: identifica i fotogrammi neri presentati nel video.
* **Estrazione di fotogrammi chiave**: rileva i fotogrammi chiave stabili in un video.
* Crediti di rotolamento : Identifica l'inizio e la fine dei crediti di rotolamento alla fine di spettacoli **televisivi**e film.
* **Rilevamento di caratteri animati** (anteprima): rilevamento, raggruppamento e riconoscimento dei caratteri nei contenuti animati tramite l'integrazione con la visione [personalizzata di Servizi cognitivi.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Per ulteriori informazioni, consultate [Rilevamento di caratteri animati.](animated-characters-recognition.md)
* **Rilevamento del tipo di scatto editoriale**: Tagging scatti in base al loro tipo (come wide shot, media shot, close up, estremo close up, due scatto, più persone, all'aperto e indoor, e così via). Per ulteriori informazioni, consultate [Rilevamento del tipo di ripresa editoriale](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Informazioni dettagliate sui contenuti audio

* **Trascrizione audio**: Converte il parlato in testo in 12 lingue e consente le estensioni. Le lingue supportate includono inglese, spagnolo, francese, tedesco, italiano, cinese mandarino, giapponese, arabo, russo, portoghese brasiliano, hindi e coreano.
* **Rilevamento automatico della lingua**: identifica automaticamente la lingua parlata dominante. Le lingue supportate includono inglese, spagnolo, francese, tedesco, italiano, cinese mandarino, giapponese, russo e portoghese brasiliano. Se la lingua non può essere identificata con fiducia, Video Indexer presuppone che la lingua parlata sia l'inglese. Per ulteriori informazioni, vedere Modello di [identificazione del linguaggio](language-identification-model.md).
* **Identificazione e trascrizione vocale multilingue** (anteprima): identifica automaticamente la lingua parlata in segmenti diversi dall'audio. Invia ogni segmento del file multimediale da trascrivere e quindi combina la trascrizione a una trascrizione unificata. Per ulteriori informazioni, consultate [Identificare e trascrivere automaticamente il contenuto multilingue.](multi-language-identification-transcription.md)
* **Sottotitoli codificati**: consente di creare sottotitoli in tre formati: VTT, TTML, SRT.
* **Elaborazione a due canali**: Rileva automaticamente la trascrizione separata e si unisce a una singola sequenza temporale.
* **Riduzione del rumore**: Cancella l'audio della telefonia o le registrazioni rumorose (in base ai filtri Skype).
* **Personalizzazione delle trascrizioni** (CRIS): consente di addestrare il riconoscimento vocale personalizzato ai modelli di testo per creare trascrizioni specifiche del settore. Per ulteriori informazioni, consultate [Personalizzare un modello linguistico dal sito Web Dell'indicizzatore video](customize-language-model-with-website.md) e Personalizzare un modello di [linguaggio con le API dell'indicizzatore video.](customize-language-model-with-api.md)
* **Enumerazione degli altoparlanti**: Mappe e capisce quale oratore ha pronunciato quali parole e quando.
* **Statistiche degli oratori**: Fornisce statistiche sui rapporti vocali degli oratori.
* **Moderazione del contenuto testuale**: rileva testo esplicito nella trascrizione audio.
* **Effetti audio**: Identifica gli effetti audio come applausi delle mani, voce e silenzio.
* **Rilevamento emozioni**: Identifica le emozioni in base al discorso (ciò che viene detto) e tonalità vocale (come viene detto). L'emozione potrebbe essere gioia, tristezza, rabbia o paura.
* **Traduzione**: crea traduzioni della trascrizione audio in 54 lingue diverse.

### <a name="audio-and-video-insights-multi-channels"></a>Informazioni dettagliate su audio e video (multicanali)

Quando si esegue l'indicizzazione in base a un canale, sarà disponibile un risultato parziale per tali modelli.

* **Estrazione parole chiave**: Estrae le parole chiave dal parlato e dal testo visivo.
* **Estrazione di entità denominate**: Estrae marchi, posizioni e persone dal parlato e testo visivo tramite l'elaborazione del linguaggio naturale (NLP).
* **Inferenza degli argomenti**: deriva gli argomenti principali dalle trascrizioni. È inclusa la tassonomia IPTC di secondo livello.
* **Artefatti**: estrae un ampio set di artefatti con "livello di dettaglio successivo" per ognuno dei modelli.
* **Analisi del sentiment**: identifica sentiment positivi, negativi e neutri dal parlato e dal testo visivo.

## <a name="how-can-i-get-started-with-video-indexer"></a>Come posso iniziare a usare Video Indexer?

È possibile accedere alle funzionalità dell'indicizzatore video in tre modi:

* Portale dell'indicizzatore video: una soluzione facile da usare che consente di valutare il prodotto, gestire l'account e personalizzare i modelli.

    Per ulteriori informazioni sul portale, consultate [Introduzione al sito Web Indicizzatore video.](video-indexer-get-started.md)  

* Integrazione API: tutte le funzionalità dell'indicizzatore video sono disponibili tramite un'API REST, che consente di integrare la soluzione nelle app e nell'infrastruttura.

    Per iniziare come sviluppatore, vedere [Usare l'API REST dell'indicizzatore video.](video-indexer-use-apis.md)

* Widget incorporabile: consente di incorporare le informazioni dettagliate sull'indicizzatore video, le esperienze del lettore e dell'editor nella tua app.

    Per ulteriori informazioni, consultate [Incorporare widget visivi nell'applicazione.](video-indexer-embed-widgets.md)

Se si utilizza il sito Web, le informazioni dettagliate vengono aggiunte come metadati e sono visibili nel portale. Se si usano le API, le informazioni dettagliate sono disponibili come file JSON.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile iniziare a usare Video Indexer. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione al sito Web Indicizzatore video](video-indexer-get-started.md).
- [Elaborare il contenuto con l'API REST dell'indicizzatore video.](video-indexer-use-apis.md)
- [Incorporare widget visivi nell'applicazione](video-indexer-embed-widgets.md).
