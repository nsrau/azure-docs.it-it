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
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: fd67e867b5eac58838551ac29e1fca5e860b15ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414222"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Note sulla versione di servizi multimediali di Azure Video Indexer

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

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
