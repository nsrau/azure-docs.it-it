---
title: Concetti di Video Indexer
titleSuffix: Azure Media Services
description: Questo articolo descrive alcuni concetti del servizio Indicizzatore Video di Servizi multimediali di Azure.This article describes some concepts of the Azure Media Services Video Indexer service.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900683"
---
# <a name="video-indexer-concepts"></a>Concetti di Video Indexer
 
Questo articolo descrive alcuni concetti relativi al servizio Video Indexer.
    
## <a name="summarized-insights"></a>Informazioni dettagliate riepilogate

Le informazioni dettagliate riepilogate contengono una visualizzazione aggregata dei dati: visi, argomenti ed emozioni. Ad esempio, invece di esaminare singolarmente le migliaia di intervalli di tempo e controllare quali visi contengono, le informazioni dettagliate riepilogate mostrano tutti i visi e, per ognuno di essi, gli intervalli di tempo in cui viene visualizzato e la percentuale di tempo per cui viene visualizzato.

## <a name="time-range-vs-adjusted-time-range"></a>Intervallo di tempo e intervallo di tempo rettificato

TimeRange è l'intervallo di tempo del video originale. Intervallo di tempo rispetto alla playlist corrente. Poiché è possibile creare una playlist da righe diverse di video diversi, è possibile prendere un video di 1 ora e usarne solo una riga, ad esempio, 10:00-10:15. In questo caso si avrà una playlist con 1 riga, in cui l'intervallo di tempo è 10:00-10:15 ma l'intervallo di tempo rettificato è 00:00-00:15.
 
## <a name="blocks"></a>Blocchi

I blocchi sono concepiti per semplificare l'esame dei dati. Ad esempio, i blocchi potrebbero essere suddivisi in base a quando cambia il parlante o alla presenza di una lunga pausa.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare, vedere [Come iscriversi e caricare il primo video](video-indexer-get-started.md).

## <a name="see-also"></a>Vedere anche

[Panoramica di Video Indexer](video-indexer-overview.md)
