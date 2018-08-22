---
title: Concetti relativi ad Video Indexer di Azure | Microsoft Docs
description: Questo argomento descrive alcuni concetti relativi al servizio Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 224c8b05027f51fb99c8d58be34c3604032c0f77
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399496"
---
# <a name="video-indexer-concepts"></a>Concetti di Video Indexer
 
Questo argomento descrive alcuni concetti relativi al servizio Video Indexer.
    
## <a name="summarized-insights"></a>Informazioni dettagliate riepilogate

Le informazioni dettagliate riepilogate contengono una visualizzazione aggregata dei dati: visi, parole chiave e sentiment. Ad esempio, invece di esaminare singolarmente le migliaia di intervalli di tempo e controllare quali visi contengono, le informazioni dettagliate riepilogate mostrano tutti i visi e, per ognuno di essi, gli intervalli di tempo in cui viene visualizzato e la percentuale di tempo per cui viene visualizzato.

## <a name="topicskeywords"></a>Argomenti/parole chiave

Gli argomenti/parole chiave sono nell'elenco delle frasi chiave che Video Indexer estrae dal testo. Ad esempio, un video di Scott Guthrie potrebbe contenere gli argomenti/parole chiave seguenti: sicurezza, Azure, Microsoft Cloud, ricavi.

## <a name="sentiments"></a>Sentiment

Quando Video Indexer analizza le trascrizioni, rileva anche il sentiment. Ad esempio, "questo è un evento molto interessante" è un sentiment positivo.

## <a name="time-range-vs-adjusted-time-range"></a>Intervallo di tempo e intervallo di tempo rettificato

TimeRange è l'intervallo di tempo del video originale. AdjustedTimeRange è l'intervallo di tempo relativo alla playlist corrente. Poiché è possibile creare una playlist da righe diverse di video diversi, è possibile prendere un video di 1 ora e usarne solo una riga, ad esempio, 10:00-10:15. In questo caso si avrà una playlist con 1 riga, in cui l'intervallo di tempo è 10:00-10:15 ma l'intervallo di tempo rettificato è 00:00-00:15.
 
## <a name="blocks"></a>Blocchi

I blocchi sono concepiti per semplificare l'esame dei dati. Ad esempio, i blocchi potrebbero essere suddivisi in base a quando cambia il parlante o alla presenza di una lunga pausa.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare, vedere [Come iscriversi e caricare il primo video](video-indexer-get-started.md).

## <a name="see-also"></a>Vedere anche 

[Panoramica di Video Indexer](video-indexer-overview.md)
