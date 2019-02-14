---
title: Concetti di Video Indexer
titlesuffix: Azure Media Services
description: Questo argomento descrive alcuni concetti relativi al servizio Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c7bbe8c6b2ad51ed7272cd215552807c7cea3aee
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991508"
---
# <a name="video-indexer-concepts"></a>Concetti di Video Indexer
 
Questo articolo descrive alcuni concetti relativi al servizio Video Indexer.
    
## <a name="summarized-insights"></a>Informazioni dettagliate riepilogate

Le informazioni dettagliate riepilogate contengono una visualizzazione aggregata dei dati: visi, argomenti ed emozioni. Ad esempio, invece di esaminare singolarmente le migliaia di intervalli di tempo e controllare quali visi contengono, le informazioni dettagliate riepilogate mostrano tutti i visi e, per ognuno di essi, gli intervalli di tempo in cui viene visualizzato e la percentuale di tempo per cui viene visualizzato.

## <a name="time-range-vs-adjusted-time-range"></a>Intervallo di tempo e intervallo di tempo rettificato

TimeRange è l'intervallo di tempo del video originale. AdjustedTimeRange è l'intervallo di tempo relativo alla playlist corrente. Poiché è possibile creare una playlist da righe diverse di video diversi, è possibile prendere un video di 1 ora e usarne solo una riga, ad esempio, 10:00-10:15. In questo caso si avrà una playlist con 1 riga, in cui l'intervallo di tempo è 10:00-10:15 ma l'intervallo di tempo rettificato è 00:00-00:15.
 
## <a name="blocks"></a>Blocchi

I blocchi sono concepiti per semplificare l'esame dei dati. Ad esempio, i blocchi potrebbero essere suddivisi in base a quando cambia il parlante o alla presenza di una lunga pausa.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare, vedere [Come iscriversi e caricare il primo video](video-indexer-get-started.md).

## <a name="see-also"></a>Vedere anche 

[Panoramica di Video Indexer](video-indexer-overview.md)
