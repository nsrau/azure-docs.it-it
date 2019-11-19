---
title: Analisi di flusso live con Video Indexer
titleSuffix: Azure Media Services
description: Questo articolo illustra come eseguire un'analisi di flusso live con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 4766d48a748fedd82532f2ac49223ab0460b9ef7
ms.sourcegitcommit: fa41a56357de487b10e54711fbbdbd4a373fa67c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74153551"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Analisi di flusso live con Video Indexer

Servizi multimediali di Azure Video Indexer è un servizio di Azure progettato per estrarre informazioni approfondite dai file video e audio offline. Ciò consente di analizzare un determinato file multimediale già creato in anticipo. Tuttavia, per alcuni casi d'uso è importante ottenere i media Insights da un feed live il più rapidamente possibile per sbloccare i casi di utilizzo operativi e gli altri casi di utilizzo premuti nel tempo. Ad esempio, i metadati avanzati in un flusso Live potrebbero essere usati dai produttori di contenuti per automatizzare la produzione TV.

Una soluzione descritta in questo articolo consente ai clienti di usare Video Indexer in risoluzioni quasi in tempo reale sui feed live. Il ritardo nell'indicizzazione può essere minimo di quattro minuti usando questa soluzione, a seconda dei blocchi di dati indicizzati, della risoluzione di input, del tipo di contenuto e del calcolo usato per questo processo.

![Metadati del Video Indexer nel flusso Live](./media/live-stream-analysis/live-stream-analysis01.png)

*Figura 1: lettore di esempio che Visualizza i metadati del Video Indexer nel flusso Live*

La [soluzione di analisi di flusso](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/blob/master/media-functions-for-logic-app/LiveStreamAnalysis.md) a disposizione usa funzioni di Azure e due app per la logica per elaborare un programma Live da un canale Live in servizi multimediali di azure con video Indexer e visualizza il risultato con Azure Media Player mostrando il flusso risultante quasi in tempo reale.

A livello generale, è costituito da due passaggi principali. Il primo passaggio viene eseguito ogni 60 secondi e accetta una clip secondaria degli ultimi 60 secondi riprodotti, crea un asset da esso e lo indicizza tramite Video Indexer. Il secondo passaggio viene quindi chiamato una volta completata l'indicizzazione. Le informazioni acquisite vengono elaborate, inviate a Azure Cosmos DB e il sottoclip indicizzato viene eliminato.

Il lettore di esempio riproduce il flusso live e ottiene le informazioni da Azure Cosmos DB, usando una funzione di Azure dedicata. Visualizza i metadati e le anteprime sincronizzati con il video in tempo reale.

![Due app per la logica che elaborano il flusso live ogni minuto nel cloud](./media/live-stream-analysis/live-stream-analysis02.png)

*Figura 2: le due app per la logica che elaborano il flusso live ogni minuto nel cloud.*

## <a name="step-by-step-guide"></a>Guida dettagliata 

Il codice completo e una guida dettagliata per la distribuzione dei risultati sono disponibili nel [progetto GitHub per analisi servizi multimediali live con video Indexer](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)
