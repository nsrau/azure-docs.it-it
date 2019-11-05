---
title: Impostare la scadenza dei dati in Cosmos DB usando la durata (TTL)
description: Con l'impostazione TTL, Microsoft Azure Cosmos DB offre la possibilità di eliminare automaticamente i documenti dal sistema dopo un periodo di tempo determinato.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: c504e2f574970142942945de5a0a9fb409bb166b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498304"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Durata (TTL) in Azure Cosmos DB 

Con **time to Live** o TTL, Azure Cosmos DB consente di eliminare automaticamente gli elementi da un contenitore dopo un determinato periodo di tempo. Per impostazione predefinita, è possibile impostare la durata a livello di contenitore ed eseguire l'override del valore per singolo elemento. Dopo aver impostato la durata a livello di contenitore o di elemento, Azure Cosmos DB rimuove automaticamente questi elementi dopo il periodo di tempo specificato, a partire dall'ora dell'ultima modifica. Il valore TTL viene configurato in secondi. Quando si configura la durata (TTL), il sistema eliminerà automaticamente gli elementi scaduti in base al valore TTL, senza che sia necessaria un'operazione di eliminazione rilasciata in modo esplicito dall'applicazione client.

L'eliminazione degli elementi scaduti è un'attività in background che utilizza le [unità richiesta](request-units.md)rimaste, ovvero le unità richiesta che non sono state utilizzate dalle richieste dell'utente. Anche dopo la scadenza della durata (TTL), se il contenitore viene sottoposta a overload con le richieste e se non è disponibile un numero sufficiente di UR, l'eliminazione dei dati viene posticipata. I dati vengono eliminati quando sono disponibili ur sufficienti per eseguire l'operazione di eliminazione. Anche se l'eliminazione dei dati viene posticipata, i dati non vengono restituiti da alcuna query (da alcuna API) dopo la scadenza della durata (TTL).

## <a name="time-to-live-for-containers-and-items"></a>Durata (TTL) per contenitori ed elementi

Il valore di durata (TTL) viene impostato in secondi e viene interpretato come un delta rispetto al momento dell'Ultima modifica di un elemento. È possibile impostare la durata per un contenitore o un elemento all'interno del contenitore:

1. **Durata (TTL) per un contenitore** (impostata tramite `DefaultTimeToLive`):

   - Se non è presente o è impostata su Null, gli elementi non scadono automaticamente.

   - Se è presente e il valore è impostato su "-1", è uguale a infinito e gli elementi non scadono per impostazione predefinita.

   - Se è presente e il valore è impostato su un numero *"n"* – gli elementi scadranno *"n"* secondi dopo l'ora dell'Ultima modifica.

2. **Durata (TTL) per un elemento** (impostata tramite `ttl`):

   - Questa proprietà è applicabile solo se `DefaultTimeToLive` è presente e non è impostato su Null per il contenitore padre.

   - Se presente, esegue l'override del valore `DefaultTimeToLive` del contenitore padre.

## <a name="time-to-live-configurations"></a>Configurazioni della durata (TTL)

* Se la funzione TTL è impostata su *"n"* in un contenitore, gli elementi del contenitore scadranno dopo *n* secondi.  Se sono presenti elementi nello stesso contenitore che hanno una propria durata, impostare su-1 (indicando che non scadono) o se alcuni elementi hanno eseguito l'override dell'impostazione Time to Live con un numero diverso, questi elementi scadono in base al relativo valore TTL configurato. 

* Se la durata (TTL) non è impostata per un contenitore, la durata impostata per un elemento all'interno del contenitore non ha alcun effetto. 

* Se la durata per un contenitore è impostata su -1, un elemento all'interno del contenitore la cui durata è impostata su n scadrà dopo n secondi, mentre gli elementi rimanenti non scadono. 

L'eliminazione di elementi in base alla durata (TTL) è gratuita. Non vengono applicati costi aggiuntivi (ossia non vengono utilizzate UR aggiuntive) quando un elemento viene eliminato in seguito alla scadenza della durata.

## <a name="examples"></a>Esempi

Questa sezione illustra alcuni esempi con valori di durata (TTL) diversi assegnati a contenitori ed elementi:

### <a name="example-1"></a>Esempio 1

TTL sul contenitore è impostato su null (DefaultTimeToLive = null)

|TTL sull'elemento| Risultato|
|---|---|
|TTL = null|    TTL è disabilitato. L'elemento non scadrà mai (impostazione predefinita).|
|TTL =-1   |TTL è disabilitato. L'elemento non scadrà mai.|
|TTL = 2000 |TTL è disabilitato. L'elemento non scadrà mai.|


### <a name="example-2"></a>Esempio 2

TTL sul contenitore è impostato su-1 (DefaultTimeToLive =-1)

|TTL sull'elemento| Risultato|
|---|---|
|TTL = null |TTL è abilitato. L'elemento non scadrà mai (impostazione predefinita).|
|TTL =-1   |TTL è abilitato. L'elemento non scadrà mai.|
|TTL = 2000 |TTL è abilitato. L'elemento scadrà dopo 2000 secondi.|


### <a name="example-3"></a>Esempio 3

TTL sul contenitore è impostato su 1000 (DefaultTimeToLive = 1000)

|TTL sull'elemento| Risultato|
|---|---|
|TTL = null|    TTL è abilitato. L'elemento scadrà dopo 1000 secondi (impostazione predefinita).|
|TTL =-1   |TTL è abilitato. L'elemento non scadrà mai.|
|TTL = 2000 |TTL è abilitato. L'elemento scadrà dopo 2000 secondi.|

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare il time to Live negli articoli seguenti:

* [Come configurare la durata (TTL)](how-to-time-to-live.md)
