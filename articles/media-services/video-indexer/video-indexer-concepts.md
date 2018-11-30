---
title: Concetti di Video Indexer
titlesuffix: Azure Media Services
description: Questo argomento descrive alcuni concetti relativi al servizio Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: fde98dac45eeda9ec2c842b9d8e0f93134ab8092
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291948"
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
