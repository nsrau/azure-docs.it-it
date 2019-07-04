---
title: Note sulla versione di Azure Media Services Video Indexer | Microsoft Docs
description: Per rimanere aggiornati con gli sviluppi più recenti, in questo articolo offre gli aggiornamenti più recenti su indicizzatore Video di servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454022"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Note sulla versione di Azure Media Services Video Indexer

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="june-2019"></a>Giugno 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer distribuita in Giappone orientale

È ora possibile creare un indicizzatore Video a pagamento di account nell'area Giappone orientale.

### <a name="create-and-repair-account-api-preview"></a>Creare e ripristinare l'account API (anteprima)

Aggiunta una nuova API che ti permette [aggiornare l'endpoint di connessione di servizi multimediali di Azure o una chiave](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Migliorare la gestione degli errori sul caricamento 

Viene restituito un messaggio descrittivo in caso di errore di configurazione dell'account servizi multimediali di Azure sottostante.

### <a name="player-timeline-keyframes-preview"></a>Anteprima di Windows Media Player della sequenza temporale fotogrammi chiave 

È ora possibile visualizzare un'anteprima dell'immagine per ogni periodo di tempo nella sequenza temporale del lettore.

### <a name="editor-semi-select"></a>Editor semi-selezione

È ora possibile visualizzare un'anteprima di tutte le informazioni dettagliate che sono selezionati come risultato la scelta di un intervallo di tempo di approfondimento specifico nell'editor.

## <a name="may-2019"></a>Maggio 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aggiornare il modello di lingua personalizzato dal file di sottotitoli

[Crea modello di lingua personalizzato](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e [aggiornare modelli di lingua personalizzati](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API ora supportano VTT, SRT, e formati di file TTML come input per i modelli di linguaggio.

Quando si chiama il [trascrizione Video aggiornamento API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), la trascrizione viene aggiunto automaticamente. Il training del modello associato con il video viene aggiornato automaticamente. Per informazioni su come personalizzare ed eseguire il training di modelli di lingua, vedere [personalizzare un modello di lingua con l'indicizzatore Video](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nuovi formati di trascrizione di download: CSV e TXT

Oltre al chiusi i sottotitoli codificato formato già supportato (SRT, VTT e TTML), Video Indexer supporta ora il download la trascrizione nei formati CSV e TXT.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](video-indexer-overview.md)
