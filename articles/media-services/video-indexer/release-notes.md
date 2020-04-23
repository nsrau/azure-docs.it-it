---
title: Note sulla versione di Azure Media Services Video Indexer Documenti Microsoft
description: Per rimanere aggiornati con gli sviluppi più recenti, in questo articolo vengono forniti gli aggiornamenti più recenti nell'indicizzatore video di Servizi multimediali di Azure.To stay up to-date with the most recent developments, this article provides you with the latest updates on Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: f7ce5f5086bcf2d577fb998df307ee684d536c15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870093"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Note sulla versione di Azure Media Services Video IndexerAzure Media Video Indexer release notes

>Ricevere una notifica su quando rivedere questa pagina per gli `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` aggiornamenti copiando e incollando questo URL: nel lettore di feed RSS.

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="april-2020"></a>Aprile 2020

### <a name="new-widget-parameters-capabilities"></a>Nuove funzionalità dei parametri dei widget

Il widget **Insights** include `language` `control`nuovi parametri: e .

Il widget **Player** `locale` ha un nuovo parametro. Entrambi `locale` `language` i parametri controllano la lingua del giocatore.

Per ulteriori informazioni, consultate la sezione [dei tipi di widget.](video-indexer-embed-widgets.md#widget-types) 

### <a name="new-player-skin"></a>Nuova skin del giocatore

Una nuova skin giocatore lanciato con design aggiornato.

## <a name="january-2020"></a>Gennaio 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Supporto linguistico personalizzato per altre lingue

L'indicizzatore video supporta `ar-SY` ora `en-UK`modelli `en-AU` di linguaggio personalizzati per , e (solo API).
 
### <a name="delete-account-timeframe-action-update"></a>Eliminare l'aggiornamento dell'azione di tempo dell'account

L'azione Elimina account ora elimina l'account entro 90 giorni anziché 48 ore.
 
### <a name="new-video-indexer-github-repository"></a>Nuovo repository GitHub dell'indicizzatore video

È ora disponibile un nuovo Video Indexer GitHub con diversi progetti, guide introduttive ed esempi di codice:A new Video Indexer GitHub with different projects, getting started guides and code samples is now available:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Aggiornamento Swagger

Autenticazioni e **operazioni** unificate dell'indicizzatore video in una singola [specifica OpenAPI dell'indicizzatore video (swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). **authentications** Gli sviluppatori possono trovare le API nel [portale per sviluppatori dell'indicizzatore video.](https://api-portal.videoindexer.ai/)

## <a name="december-2019"></a>Dicembre 2019

### <a name="update-transcript-with-the-new-api"></a>Aggiornare la trascrizione con la nuova APIUpdate transcript with the new API

Aggiornare una sezione specifica nella trascrizione usando l'API [Update-Video-Index.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Correggere la configurazione dell'account dal portale dell'indicizzatore videoFix account configuration from the Video Indexer portal

È ora possibile aggiornare la configurazione della connessione di Servizi multimediali per auto-aiutare con problemi come: 

* risorsa di Servizi multimediali di Azure non corretta
* modifiche della password
* Le risorse di Servizi multimediali sono state spostate tra le sottoscrizioni  

Per correggere la configurazione dell'account, nel portale dell'indicizzatore video passare alla scheda Impostazioni > Account (come proprietario).

### <a name="configure-the-custom-vision-account"></a>Configurare l'account di visione personalizzato

Configurare l'account di visione personalizzato negli account a pagamento usando il portale dell'indicizzatore video (in precedenza, questo era supportato solo dall'API). To do that, sign in to the Video Indexer portal, choose Model Customization > Animated characters > Configure. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scene, scatti e fotogrammi chiave – ora in un unico riquadro di informazioni

Le scene, le riprese e i fotogrammi chiave vengono ora uniti in un'unica intuizione per facilitare il consumo e la navigazione. Quando si seleziona la scena desiderata, è possibile vedere da quali scatti e fotogrammi chiave è costituito. 

### <a name="notification-about-a-long-video-name"></a>Notifica di un nome video lungo

Quando un nome video è più lungo di 80 caratteri, Video Indexer mostra un errore descrittivo durante il caricamento.

### <a name="streaming-endpoint-is-disabled-notification"></a>L'endpoint di streaming è disabilitato notifica

Quando l'endpoint di streaming è disabilitato, Video Indexer mostrerà un errore descrittivo nella pagina del lettore.

### <a name="error-handling-improvement"></a>Miglioramento della gestione degli errori

Il codice di stato 409 verrà ora restituito da [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) e [Update Video Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) API nel caso in cui un video venga indicizzato attivamente, per evitare di sovrascrivere accidentalmente le modifiche di reindicizzazione correnti.

## <a name="november-2019"></a>Novembre 2019
 
* Supporto per i modelli linguistici personalizzati coreani

    L'indicizzatore video supporta ora`ko-KR`i modelli di linguaggio personalizzati in coreano ( ) sia nell'API che nel portale. 
* Nuove lingue supportate per la sintesi vocale (STT)

    Le API dell'indicizzatore video ora supportano STT in arabo Levantine (ar-SY), dialetto inglese del Regno Unito (en-GB) e dialetto australiano inglese (en-AU).
    
    Per il caricamento video, abbiamo sostituito zh-HANS a zh-CN, entrambi sono supportati, ma zh-CN è consigliato e più preciso.
    
## <a name="october-2019"></a>Ottobre 2019
 
* Cercare personaggi animati nella galleria

    Quando indicizzate i personaggi animati, ora potete cercarli nella video cambusa dell'account. Per ulteriori informazioni, consultate [Riconoscimento dei caratteri animati.](animated-characters-recognition.md)

## <a name="september-2019"></a>Settembre 2019
 
Più progressi annunciati all'IBC 2019:
 
* Riconoscimento animato dei caratteri (anteprima pubblica)

    Possibilità di rilevare gli annunci di gruppo riconoscere i caratteri nei contenuti animati, tramite l'integrazione con la visione personalizzata. Per ulteriori informazioni, consultate [Rilevamento di caratteri animati.](animated-characters-recognition.md)
* Identificazione multilingue (anteprima pubblica)

    Rileva i segmenti in più lingue nella traccia audio e crea una trascrizione multilingue basata su di essi. Supporto iniziale: inglese, spagnolo, tedesco e francese. Per ulteriori informazioni, consultate [Identificare e trascrivere automaticamente il contenuto multilingue.](multi-language-identification-transcription.md)
* Estrazione di entità denominate per utenti e posizioni

    Estrae marchi, sedi e persone dal testo vocale e visivo tramite l'elaborazione del linguaggio naturale (NLP).
* Classificazione del tipo di scatto editoriale

    Tagging di scatti con tipi editoriali come primo piano, tiro medio, due scatto, interno, esterno ecc. Per ulteriori informazioni, consultate [Rilevamento del tipo di ripresa editoriale](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Miglioramento dell'inferenza dell'argomento - ora copre il livello 2
    
    Il modello di inferenza degli argomenti supporta ora una granularità più profonda della tassonomia IPTC. Per informazioni dettagliate, vedere [La nuova innovazione basata sull'intelligenza](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)dei dati di Servizi multimediali di Azure .

## <a name="august-2019"></a>Agosto 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Indicizzatore video distribuito nel Regno Unito

Ora puoi creare un account a pagamento dell'Indicizzatore Video nella regione sud del Regno Unito.

### <a name="new-editorial-shot-type-insights-available"></a>Nuove informazioni sul tipo di scatto editoriale disponibili

I nuovi tag aggiunti alle riprese video forniscono "tipi di scatto" editoriali per identificarli con frasi editoriali comuni utilizzate nel flusso di lavoro di creazione dei contenuti, ad esempio: primo piano estremo, primo piano, ampio, medio, due scatto, esterno, interno, faccia sinistra e faccia destra (disponibile nel codice JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Estrazione di nuove entità di persone e località disponibile

L'indicizzatore video identifica le posizioni e le persone nominate tramite l'elaborazione del linguaggio naturale (NLP) dall'OCR e dalla trascrizione del video. L'indicizzatore video usa l'algoritmo di apprendimento automatico per riconoscere quando posizioni specifiche (ad esempio, la Torre Eiffel) o persone (ad esempio, John Doe) vengono chiamate in un video.

### <a name="keyframes-extraction-in-native-resolution"></a>Estrazione di fotogrammi chiave nella risoluzione nativa

I fotogrammi chiave estratti dall'indicizzatore video sono disponibili nella risoluzione originale del video.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA per il training di modelli di volti personalizzati dalle immagini

I volti di formazione dalle immagini sono stati spostati dalla modalità anteprima a GA (disponibile tramite API e nel portale).

> [!NOTE]
> Non vi è alcun impatto sui prezzi relativo alla transizione "Anteprima a GA".

### <a name="hide-gallery-toggle-option"></a>Opzione Nascondi commutazione galleria

L'utente può scegliere di nascondere la scheda della raccolta dal portale (simile a nascondere la scheda campioni).
 
### <a name="maximum-url-size-increased"></a>Dimensione massima dell'URL aumentata

Supporto per la stringa di query URL 4096 (invece di 2048) per l'indicizzazione di un video.
 
### <a name="support-for-multi-lingual-projects"></a>Sostegno a progetti multilingue

I progetti possono ora essere creati in base a video indicizzati in lingue diverse (solo API).

## <a name="july-2019"></a>Luglio 2019

### <a name="editor-as-a-widget"></a>Editor come widget

L'editor AI dell'indicizzatore video è ora disponibile come widget da incorporare nelle applicazioni dei clienti.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aggiornare il modello linguistico personalizzato dal file di sottotitoli codificati dal portale

I clienti possono fornire formati di file VTT, SRT e TTML come input per i modelli di lingua nella pagina di personalizzazione del portale.

## <a name="june-2019"></a>Giugno 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Indicizzatore video distribuito in Giappone orientale

Ora puoi creare un account a pagamento dell'Indicizzatore Video nell'area Japan East.

### <a name="create-and-repair-account-api-preview"></a>Creare e ripristinare l'API dell'account (anteprima)Create and repair account API (Preview)

Aggiunta una nuova API che consente di [aggiornare l'endpoint o la chiave dell'endpoint o](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)della chiave del servizio multimediale di Azure.

### <a name="improve-error-handling-on-upload"></a>Migliorare la gestione degli errori durante il caricamento 

Viene restituito un messaggio descrittivo in caso di configurazione errata dell'account di Servizi multimediali di Azure sottostante.

### <a name="player-timeline-keyframes-preview"></a>Anteprima dei fotogrammi chiave della timeline del giocatore 

Ora puoi vedere un'anteprima dell'immagine ogni volta sul diario del giocatore.

### <a name="editor-semi-select"></a>Editor semi-selezionare

È ora possibile visualizzare un'anteprima di tutte le informazioni dettagliate selezionate come risultato della scelta di un intervallo di tempo specifico nell'editor.

## <a name="may-2019"></a>Maggio 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aggiornare il modello linguistico personalizzato dal file dei sottotitoli codificati

Creare un [modello linguistico personalizzato](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e [aggiornare](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) i modelli linguistici personalizzati ora supportano i formati di file VTT, SRT e TTML come input per i modelli linguistici.

Quando si chiama [l'API Aggiorna trascrizione video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), la trascrizione viene aggiunta automaticamente. Anche il modello di training associato al video viene aggiornato automaticamente. Per informazioni su come personalizzare ed eseguire il training dei modelli linguistici, consultate Personalizzare un modello di [lingua con l'indicizzatore video.](customize-language-model-overview.md)

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nuovi formati di trascrizione di download - TXT e CSV

Oltre al formato di sottotitoli codificati già supportato (SRT, VTT e TTML), Video Indexer ora supporta il download della trascrizione nei formati TXT e CSV.

## <a name="next-steps"></a>Passaggi successivi

[Overview](video-indexer-overview.md)
