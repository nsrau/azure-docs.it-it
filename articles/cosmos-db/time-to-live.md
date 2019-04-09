---
title: Impostare la scadenza dei dati in Cosmos DB usando la durata (TTL)
description: Con l'impostazione TTL, Microsoft Azure Cosmos DB offre la possibilità di eliminare automaticamente i documenti dal sistema dopo un periodo di tempo determinato.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 27540c3dfce73788e01f0f8ab0892c733f153fdf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271272"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Durata (TTL) in Azure Cosmos DB 

Con **Time to Live** o durata (TTL), Azure Cosmos DB offre la possibilità di eliminare automaticamente gli elementi da un contenitore dopo un certo periodo di tempo. Per impostazione predefinita, è possibile impostare la durata a livello di contenitore ed eseguire l'override del valore per singolo elemento. Dopo aver impostato la durata a livello di contenitore o di elemento, Azure Cosmos DB rimuove automaticamente questi elementi dopo il periodo di tempo specificato, a partire dall'ora dell'ultima modifica. Il valore TTL viene configurato in secondi. Quando si configura la durata (TTL), il sistema eliminerà automaticamente gli elementi scaduti basati al valore di durata (TTL), senza la necessità di un'operazione di eliminazione viene eseguita in modo esplicito dall'applicazione client.

## <a name="time-to-live-for-containers-and-items"></a>Durata (TTL) per contenitori ed elementi

Il valore time to live è impostato in secondi e viene interpretato come differenziale dal momento in cui un elemento dell'ultima modifica apportata. È possibile impostare la durata per un contenitore o un elemento all'interno del contenitore:

1. **Durata (TTL) per un contenitore** (impostata tramite `DefaultTimeToLive`):

   - Se non è presente o è impostata su Null, gli elementi non scadono automaticamente.

   - Se presente e il valore è impostato su "-1", è uguale all'infinito e gli elementi non scadono per impostazione predefinita.

   - Se presente e il valore è impostato su un numero *"n"* – elementi scadrà *"n"* secondi dopo la loro ultima ora di modifica.

2. **Durata (TTL) per un elemento** (impostata tramite `ttl`):

   - Questa proprietà è applicabile solo se `DefaultTimeToLive` è presente e non è impostato su Null per il contenitore padre.

   - Se presente, esegue l'override del valore `DefaultTimeToLive` del contenitore padre.

## <a name="time-to-live-configurations"></a>Configurazioni della durata (TTL)

* Se durata (TTL) è impostata su *"n"* su un contenitore, quindi gli elementi in tale contenitore scadrà dopo *n* secondi.  Se sono presenti gli elementi nello stesso contenitore con le proprie attività in tempo reale, impostare su -1, che indica che non scadono, o se alcuni elementi hanno sostituito la durata (TTL) impostazione con un numero diverso, questi elementi scadono basato sul proprio valore di durata (TTL) configurato. 

* Se la durata (TTL) non è impostata per un contenitore, la durata impostata per un elemento all'interno del contenitore non ha alcun effetto. 

* Se la durata per un contenitore è impostata su -1, un elemento all'interno del contenitore la cui durata è impostata su n scadrà dopo n secondi, mentre gli elementi rimanenti non scadono. 

L'eliminazione di elementi in base alla durata (TTL) è gratuita. Non vengono applicati costi aggiuntivi (ossia non vengono utilizzate UR aggiuntive) quando un elemento viene eliminato in seguito alla scadenza della durata.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare Time to Live negli articoli seguenti:

* [Jak nakonfigurovat Time to Live](how-to-time-to-live.md)
