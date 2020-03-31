---
title: Analisi del flusso in tempo reale con Video Indexer
titleSuffix: Azure Media Services
description: Questo articolo illustra come eseguire un'analisi del flusso live utilizzando Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185990"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Analisi del flusso live con Video Indexer

Azure Media Services Video Indexer è un servizio di Azure progettato per estrarre informazioni approfondite dai file video e audio offline. Questo per analizzare un dato file multimediale già creato in anticipo. Tuttavia, per alcuni casi d'uso è importante ottenere le informazioni multimediali da un live feed il più velocemente possibile per sbloccare casi operativi e altri casi d'uso premuti nel tempo. Ad esempio, tali metadati avanzati su un live streaming potrebbero essere utilizzati dai produttori di contenuti per automatizzare la produzione tv.

Una soluzione descritta in questo articolo consente ai clienti di utilizzare l'indicizzatore video con risoluzioni quasi in tempo reale nei feed live. Il ritardo nell'indicizzazione può essere basso fino a quattro minuti utilizzando questa soluzione, a seconda dei blocchi di dati indicizzati, della risoluzione di input, del tipo di contenuto e del calcolo utilizzato per questo processo.

![Metadati dell'indicizzatore video nel live streaming](./media/live-stream-analysis/live-stream-analysis01.png)

*Figura 1 – Lettore di esempio che visualizza i metadati dell'indicizzatore video nel live streaming*

La soluzione di analisi del [flusso](https://aka.ms/livestreamanalysis) a portata di mano usa Funzioni di Azure e due app per la logica per elaborare un programma live da un canale live in Servizi multimediali di Azure con indicizzatore video e visualizza il risultato con Azure Media Player che mostra il flusso a risultati quasi in tempo reale.

In alto livello, è composto da due fasi principali. Il primo passaggio viene eseguito ogni 60 secondi e prende un clip secondario degli ultimi 60 secondi riprodotti, crea una risorsa da esso e la indicizza tramite Video Indexer. Quindi il secondo passaggio viene chiamato una volta completata l'indicizzazione. Le informazioni dettagliate acquisite vengono elaborate, inviate a Azure Cosmos DB e la clip secondaria indicizzata viene eliminata.

Il lettore di esempio riproduce il flusso live e ottiene le informazioni dettagliate da Azure Cosmos DB, usando una funzione di Azure dedicata. Visualizza i metadati e le miniature in sincronia con il video in diretta.

![Le due app per la logica che elaborano il flusso live ogni minuto nel cloud](./media/live-stream-analysis/live-stream-analysis02.png)

*Figura 2 – Le due app per la logica che elaborano il flusso live ogni minuto nel cloud.*

## <a name="step-by-step-guide"></a>Guida passo-passo 

Il codice completo e una guida dettagliata per distribuire i risultati sono disponibili nel [progetto GitHub per l'analisi multimediale in tempo](https://aka.ms/livestreamanalysis)reale con Video Indexer . 

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)
