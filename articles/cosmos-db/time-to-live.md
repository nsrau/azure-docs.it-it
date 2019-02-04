---
title: Impostare la scadenza dei dati in Cosmos DB usando la durata (TTL)
description: Con l'impostazione TTL, Microsoft Azure Cosmos DB offre la possibilità di eliminare automaticamente i documenti dal sistema dopo un periodo di tempo determinato.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 9b5054da56bdc6a88642ea7860702b280a76dbb1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461169"
---
# <a name="time-to-live-in-azure-cosmos-db"></a>Durata (TTL) in Azure Cosmos DB 

Impostando un valore di durata, o TTL, Azure Cosmos DB consente di eliminare automaticamente elementi da un contenitore dopo un determinato periodo di tempo. Per impostazione predefinita, è possibile impostare la durata a livello di contenitore ed eseguire l'override del valore per singolo elemento. Dopo aver impostato la durata a livello di contenitore o di elemento, Azure Cosmos DB rimuove automaticamente questi elementi dopo il periodo di tempo specificato, a partire dall'ora dell'ultima modifica. Il valore TTL viene configurato in secondi. Quando si configura il valore TTL, il sistema elimina automaticamente gli elementi scaduti in base a questo valore, a differenza di un'operazione di eliminazione che viene eseguita in modo esplicito dall'applicazione client.

## <a name="time-to-live-for-containers-and-items"></a>Durata (TTL) per contenitori ed elementi

Il valore TTL viene impostato in secondi e interpretato come differenziale dall'ora dell'ultima modifica di un elemento. È possibile impostare la durata per un contenitore o un elemento all'interno del contenitore:

1. **Durata (TTL) per un contenitore** (impostata tramite `DefaultTimeToLive`):

   - Se non è presente o è impostata su Null, gli elementi non scadono automaticamente.

   - Se è presente e il valore è impostato su "-1", che è uguale a infinito, gli elementi non scadono per impostazione predefinita.

   - Se è presente e il valore è impostato su un numero ("n"), gli elementi scadono "n" secondi dopo l'ultima modifica.

2. **Durata (TTL) per un elemento** (impostata tramite `TimeToLive`):

   - Questa proprietà è applicabile solo se `DefaultTimeToLive` è presente e non è impostato su Null per il contenitore padre.

   - Se presente, esegue l'override del valore `DefaultTimeToLive` del contenitore padre.

## <a name="time-to-live-configurations"></a>Configurazioni della durata (TTL)

* Se la durata è impostata su "n" per un contenitore, gli elementi all'interno di quel contenitore scadono dopo n secondi.  Se nello stesso contenitore sono presenti elementi la cui durata è impostata su -1 (a indicare che non scadono mai) o se alcuni elementi hanno eseguito l'override dell'impostazione TTL con un altro numero, questi elementi scadono in base al valore TTL configurato. 

* Se la durata (TTL) non è impostata per un contenitore, la durata impostata per un elemento all'interno del contenitore non ha alcun effetto. 

* Se la durata per un contenitore è impostata su -1, un elemento all'interno del contenitore la cui durata è impostata su n scadrà dopo n secondi, mentre gli elementi rimanenti non scadono. 

L'eliminazione di elementi in base alla durata (TTL) è gratuita. Non vengono applicati costi aggiuntivi (ossia non vengono utilizzate UR aggiuntive) quando un elemento viene eliminato in seguito alla scadenza della durata.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare la durata (TTL) sono disponibili nell'articolo seguente:

* [Come configurare la durata (TTL)](how-to-time-to-live.md)
