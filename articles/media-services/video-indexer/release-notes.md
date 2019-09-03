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
ms.date: 08/29/2019
ms.author: juliako
ms.openlocfilehash: a7d178972a8c8b413e3c6336c403348f1eb78abb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232606"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Note sulla versione di servizi multimediali di Azure Video Indexer

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="august-2019"></a>Agosto 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer distribuita in Regno Unito meridionale

È ora possibile creare un account a pagamento Video Indexer nell'area Regno Unito meridionale.

### <a name="new-editorial-shot-type-insights-available"></a>Sono disponibili nuove informazioni dettagliate sul tipo di articolo

I nuovi tag aggiunti a video Shots forniscono i "tipi di colpo" editoriali per identificarli con frasi editoriali comuni usate nel flusso di lavoro di creazione del contenuto, ad esempio Extreme closeup, closeup, Wide, medium, two shot, outdoor, indoor, Left Face e Right Face (disponibile nella JSON).

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

## <a name="july-2019"></a>2019 luglio

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
