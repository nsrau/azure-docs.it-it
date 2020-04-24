---
title: Note sulla versione di servizi multimediali di Azure Video Indexer | Microsoft Docs
description: Per rimanere sempre aggiornati sugli sviluppi più recenti, questo articolo fornisce gli aggiornamenti più recenti su servizi multimediali di Azure Video Indexer.
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
ms.openlocfilehash: 07046d181e8e83e0271cb78cfb182df4e76980b5
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115517"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Note sulla versione di servizi multimediali di Azure Video Indexer

>Consente di ricevere notifiche su quando rivisitare la pagina per gli aggiornamenti copiando e incollando questo `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` URL: nel lettore di feed RSS.

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="april-2020"></a>Aprile 2020

### <a name="new-widget-parameters-capabilities"></a>Nuove funzionalità dei parametri widget

Il widget **Insights** include nuovi parametri: `language` e. `control`

Il widget **Player** ha un nuovo `locale` parametro. Entrambi `locale` i `language` parametri e controllano la lingua del lettore.

Per ulteriori informazioni, vedere la sezione [tipi di widget](video-indexer-embed-widgets.md#widget-types) . 

### <a name="new-player-skin"></a>Nuova interfaccia del lettore

Nuova interfaccia del lettore avviata con una progettazione aggiornata.

### <a name="prepare-for-upcoming-changes"></a>Preparare le modifiche future

* Attualmente, le API seguenti restituiscono un oggetto account:

    * [Crea account a pagamento](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account)
    * [Get-account](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account)
    * [Get-accounts-Authorization](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-Authorization)
    * [Get-accounts-with-token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-With-Token)
 
    L'oggetto account dispone di `Url` un campo che punta al percorso del [sito web video Indexer](https://www.videoindexer.ai/).
Per gli account a `Url` pagamento, il campo punta attualmente a un URL interno anziché al sito Web pubblico.
Nelle prossime settimane verrà modificato e restituito l'URL del [sito web video Indexer](https://www.videoindexer.ai/) per tutti gli account (versione di valutazione e a pagamento).

    Non usare gli URL interni, ma è necessario usare le [api video Indexer pubbliche](https://api-portal.videoindexer.ai/).
* Se si incorporano Video Indexer URL nelle applicazioni e gli URL non puntano al [sito Web di video Indexer](https://www.videoindexer.ai/) o all' [endpoint API video Indexer](https://api.videoindexer.ai) , ma piuttosto a un endpoint a livello di area ( `https://wus2.videoindexer.ai`ad esempio,), rigenerare gli URL.

   È possibile eseguire questa operazione in uno dei seguenti casi:

    * Sostituzione dell'URL con un URL che punta alle API widget Video Indexer (ad esempio, il [widget Insights](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Insights-Widget))
    * Uso del sito Web Video Indexer per generare un nuovo URL incorporato:
         
         Premere **Riproduci** per accedere alla pagina del video > fare clic su ** &lt;incorpora&gt; ** -> copiare l'URL nell'applicazione:
   
    Gli URL locali non sono supportati e verranno bloccati nelle prossime settimane.

## <a name="january-2020"></a>Gennaio 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Supporto del linguaggio personalizzato per lingue aggiuntive

Video Indexer supporta ora modelli di lingua personalizzati `ar-SY` per `en-UK`, e `en-AU` (solo API).
 
### <a name="delete-account-timeframe-action-update"></a>Elimina l'aggiornamento dell'azione dell'intervallo di tempo dell'account

Elimina l'azione dell'account ora elimina l'account entro 90 giorni anziché 48 ore.
 
### <a name="new-video-indexer-github-repository"></a>Nuovo repository GitHub Video Indexer

Un nuovo Video Indexer GitHub con progetti diversi, guide introduttive ed esempi di codice sono ora disponibili:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Aggiornamento di spavalderia

Video Indexer le **autenticazioni** e **le operazioni** unificate in una singola [specifica video Indexer openapi (spavalderia)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). Gli sviluppatori possono trovare le API nel [portale per sviluppatori video Indexer](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Dicembre 2019

### <a name="update-transcript-with-the-new-api"></a>Aggiornare la trascrizione con la nuova API

Aggiornare una sezione specifica della trascrizione usando l'API [Update-video-index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) .

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Correzione della configurazione dell'account dal portale di Video Indexer

È ora possibile aggiornare la configurazione della connessione di servizi multimediali per aiutare a risolvere i problemi in modo autonomo, ad esempio: 

* risorsa di servizi multimediali di Azure non corretta
* modifiche della password
* Le risorse di servizi multimediali sono state spostate tra sottoscrizioni  

Per correggere la configurazione dell'account, nel portale di Video Indexer passare a impostazioni > scheda account (proprietario).

### <a name="configure-the-custom-vision-account"></a>Configurare l'account di visione artificiale personalizzato

Configurare l'account per la visione personalizzata per gli account a pagamento usando il portale di Video Indexer (in precedenza era supportato solo dall'API). A tale scopo, accedere al portale di Video Indexer, scegliere Personalizzazione del modello > caratteri animati > Configura. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scene, scatti e fotogrammi chiave-ora in un unico riquadro informazioni

Le scene, gli scatti e i fotogrammi chiave sono ora Uniti in un'unica intuizione per semplificare il consumo e la navigazione. Quando si seleziona la scena desiderata, è possibile vedere quali scatti e fotogrammi chiave sono costituiti da. 

### <a name="notification-about-a-long-video-name"></a>Notifica relativa a un nome di video lungo

Quando un nome di video è più lungo di 80 caratteri, Video Indexer Visualizza un errore descrittivo al caricamento.

### <a name="streaming-endpoint-is-disabled-notification"></a>La notifica dell'endpoint di streaming è disabilitata

Quando endpoint di streaming è disabilitato, Video Indexer visualizzerà un errore descrittivo nella pagina del lettore.

### <a name="error-handling-improvement"></a>Miglioramento della gestione degli errori

Il codice di stato 409 verrà ora restituito da [reindicizzare video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) e aggiornare le API dell' [Indice video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) nel caso in cui un video venga indicizzato attivamente, per evitare di sovrascrivere le modifiche correnti di REINDEX per errore.

## <a name="november-2019"></a>Novembre 2019
 
* Supporto per modelli di linguaggio personalizzati coreano

    Video Indexer supporta ora modelli di lingua personalizzati in coreano`ko-KR`() sia nell'API che nel portale. 
* Nuove lingue supportate per la sintesi vocale (STT)

    Le API di Video Indexer supportano ora STT in arabo Levantina (ar-SY), dialetto inglese (en-GB) e dialetto australiano inglese (en-AU).
    
    Per il caricamento video, è stata sostituita da ZH-HANS a zh-CN, entrambi sono supportati, ma è consigliabile usare zh-CN e una maggiore precisione.
    
## <a name="october-2019"></a>Ottobre 2019
 
* Cercare i caratteri animati nella raccolta

    Quando si indicizzano i caratteri animati, è ora possibile cercarli nella cambusa video dell'account. Per ulteriori informazioni, vedere [riconoscimento dei caratteri animati](animated-characters-recognition.md).

## <a name="september-2019"></a>Settembre 2019
 
Sono stati annunciati più progressi in IBC 2019:
 
* Riconoscimento di caratteri animati (anteprima pubblica)

    Possibilità di rilevare il gruppo ad Recognize characters nel contenuto animato tramite l'integrazione con la visione personalizzata. Per ulteriori informazioni, vedere [rilevamento dei caratteri animati](animated-characters-recognition.md).
* Identificazione a più lingue (anteprima pubblica)

    Rilevare i segmenti in più lingue nella traccia audio e creare una trascrizione multilingue basata su di essi. Supporto iniziale: inglese, spagnolo, tedesco e francese. Per altre informazioni, vedere [identificare e trascrivere automaticamente contenuti multilingue](multi-language-identification-transcription.md).
* Estrazione di entità denominate per utenti e località

    Estrae le marche, le località e gli utenti da testo vocale e visivo tramite la PNL (Natural Language Processing).
* Classificazione del tipo di ripresa editoriale

    Assegnazione di tag a scatti con tipi editoriali come Close up, medium, two shot, indoor, outdoor e così via. Per ulteriori informazioni, vedere il [rilevamento del tipo di ripresa editoriale](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Miglioramento dell'inferenza degli argomenti-ora copertura del livello 2
    
    Il modello di inferenza degli argomenti supporta ora la granularità più approfondita della tassonomia IPTC. Leggi i dettagli completi su [servizi multimediali di Azure nuova innovazione basata sull'intelligenza artificiale](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Agosto 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer distribuita in Regno Unito meridionale

È ora possibile creare un account a pagamento Video Indexer nell'area Regno Unito meridionale.

### <a name="new-editorial-shot-type-insights-available"></a>Sono disponibili nuove informazioni dettagliate sul tipo di articolo

I nuovi tag aggiunti a video Shots forniscono i "tipi di colpo" editoriali per identificarli con frasi editoriali comuni usate nel flusso di lavoro di creazione del contenuto, ad esempio Extreme closeup, closeup, Wide, medium, two shot, outdoor, indoor, Left Face e Right Face (disponibile in JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Estrazione delle entità nuove persone e posizioni disponibili

Video Indexer identifica le località e le persone denominate tramite l'elaborazione del linguaggio naturale (PNL) dall'OCR e dalla trascrizione del video. Video Indexer usa l'algoritmo di machine learning per riconoscere quando vengono richiamate posizioni specifiche, ad esempio la Torre Eiffel, o persone, ad esempio John Doe, in un video.

### <a name="keyframes-extraction-in-native-resolution"></a>Estrazione fotogrammi chiave nella risoluzione nativa

I fotogrammi chiave estratti da Video Indexer sono disponibili nella risoluzione originale del video.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA per il training dei modelli viso personalizzati dalle immagini

I visi di training dalle immagini sono passati dalla modalità di anteprima alla versione GA (disponibile tramite l'API e nel portale).

> [!NOTE]
> Non vi è alcun effetto sui prezzi correlato alla transizione "da anteprima a GA".

### <a name="hide-gallery-toggle-option"></a>Opzione Nascondi raccolta

L'utente può scegliere di nascondere la scheda della raccolta dal portale (simile alla scheda esempi).
 
### <a name="maximum-url-size-increased"></a>Dimensioni massime URL aumentate

Supporto per la stringa di query dell'URL 4096 (anziché 2048) nell'indicizzazione di un video.
 
### <a name="support-for-multi-lingual-projects"></a>Supporto per progetti multilingue

I progetti possono ora essere creati in base ai video indicizzati in lingue diverse (solo API).

## <a name="july-2019"></a>Luglio 2019

### <a name="editor-as-a-widget"></a>Editor come widget

Il Video Indexer l'editor di intelligenza artificiale è ora disponibile come widget da incorporare nelle applicazioni dei clienti.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aggiornare il modello di lingua personalizzato dal file di sottotitoli chiuso dal portale

I clienti possono fornire formati di file VTT, SRT e TTML come input per i modelli di linguaggio nella pagina Personalizzazione del portale.

## <a name="june-2019"></a>Giugno 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer distribuito in Giappone orientale

È ora possibile creare un account a pagamento Video Indexer nell'area Giappone orientale.

### <a name="create-and-repair-account-api-preview"></a>Creare e ripristinare l'API dell'account (anteprima)

È stata aggiunta una nuova API che consente di [aggiornare la chiave o l'endpoint di connessione del servizio multimediale di Azure](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Migliorare la gestione degli errori al caricamento 

Viene restituito un messaggio descrittivo in caso di errata configurazione dell'account di servizi multimediali di Azure sottostante.

### <a name="player-timeline-keyframes-preview"></a>Anteprima fotogrammi chiave della sequenza temporale del lettore 

È ora possibile visualizzare un'anteprima dell'immagine per ogni volta nella sequenza temporale del lettore.

### <a name="editor-semi-select"></a>Selezione semi-editor

È ora possibile visualizzare un'anteprima di tutte le informazioni selezionate in seguito alla scelta di un intervallo di tempo specifico nell'editor.

## <a name="may-2019"></a>Maggio 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aggiornare il modello di lingua personalizzato da un file di sottotitoli chiuso

[Creare un modello di linguaggio personalizzato](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e aggiornare le API dei [modelli di linguaggio personalizzati](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) ora supportano i formati di file VTT, SRT e TTML come input per i modelli di linguaggio.

Quando si chiama l' [API della trascrizione video di aggiornamento](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), la trascrizione viene aggiunta automaticamente. Anche il modello di training associato al video viene aggiornato automaticamente. Per informazioni su come personalizzare ed eseguire il training dei modelli di linguaggio, vedere [personalizzare un modello di linguaggio con video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nuovi formati di trascrizione per il download-TXT e CSV

Oltre al formato di didascalia chiuso già supportato (SRT, VTT e TTML), Video Indexer ora supporta il download della trascrizione nei formati TXT e CSV.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](video-indexer-overview.md)
