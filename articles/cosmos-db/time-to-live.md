---
title: Impostare la scadenza dei dati in Cosmos DB usando la durata (TTL)
description: Con l'impostazione TTL, Microsoft Azure Cosmos DB offre la possibilità di eliminare automaticamente i documenti dal sistema dopo un periodo di tempo determinato.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188713"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Durata (TTL) in Azure Cosmos DB 

Con **Time to Live** o TTL, Azure Cosmos DB offre la possibilità di eliminare automaticamente gli elementi da un contenitore dopo un determinato periodo di tempo. Per impostazione predefinita, è possibile impostare la durata a livello di contenitore ed eseguire l'override del valore per singolo elemento. Dopo aver impostato la durata a livello di contenitore o di elemento, Azure Cosmos DB rimuove automaticamente questi elementi dopo il periodo di tempo specificato, a partire dall'ora dell'ultima modifica. Il valore TTL viene configurato in secondi. Quando si configura TTL, il sistema eliminerà automaticamente gli elementi scaduti in base al valore TTL, senza che sia necessaria un'operazione di eliminazione emessa in modo esplicito dall'applicazione client.

L'eliminazione degli elementi scaduti è un'attività in background che utilizza unità di [richiesta](request-units.md)rimanenti, ovvero unità richiesta che non sono state utilizzate dalle richieste dell'utente. Anche dopo che il valore TTL è scaduto, se il contenitore è sovraccarico di richieste e se non sono disponibili abbastanza RU, l'eliminazione dei dati viene ritardata. I dati vengono eliminati una volta che sono disponibili abbastanza RU per eseguire l'operazione di eliminazione. Anche se l'eliminazione dei dati viene ritardata, i dati non vengono restituiti da alcuna query (da qualsiasi API) dopo la scadenza del TTL.

## <a name="time-to-live-for-containers-and-items"></a>Durata (TTL) per contenitori ed elementi

Il valore di durata viene impostato in secondi e viene interpretato come un delta dal momento dell'ultima modifica di un elemento. È possibile impostare la durata per un contenitore o un elemento all'interno del contenitore:

1. **Durata (TTL) per un contenitore** (impostata tramite `DefaultTimeToLive`):

   - Se non è presente o è impostata su Null, gli elementi non scadono automaticamente.

   - Se presente e il valore è impostato su "-1", è uguale a infinito e gli elementi non scadono per impostazione predefinita.

   - Se presente e il valore è impostato su un certo numero *"n",* gli elementi scadranno *"n"* secondi dopo l'ora dell'ultima modifica.

2. **Durata (TTL) per un elemento** (impostata tramite `ttl`):

   - Questa proprietà è applicabile solo se `DefaultTimeToLive` è presente e non è impostato su Null per il contenitore padre.

   - Se presente, esegue l'override del valore `DefaultTimeToLive` del contenitore padre.

## <a name="time-to-live-configurations"></a>Configurazioni della durata (TTL)

* Se TTL è impostato su *"n"* in un contenitore, gli elementi in tale contenitore scadranno dopo *n* secondi.  Se sono presenti elementi nello stesso contenitore che hanno il proprio tempo di vita, impostato su -1 (che indica che non scadono) o se alcuni elementi hanno eseguito l'override dell'impostazione di tempo per vivere con un numero diverso, questi elementi scadono in base al proprio valore TTL configurato. 

* Se la durata (TTL) non è impostata per un contenitore, la durata impostata per un elemento all'interno del contenitore non ha alcun effetto. 

* Se la durata per un contenitore è impostata su -1, un elemento all'interno del contenitore la cui durata è impostata su n scadrà dopo n secondi, mentre gli elementi rimanenti non scadono.

## <a name="examples"></a>Esempi

Questa sezione mostra alcuni esempi con diversi valori di tempo di vita assegnati al contenitore e agli elementi:This section shows some examples with different time to live values assigned to container and items:

### <a name="example-1"></a>Esempio 1

TTL sul contenitore è impostato su null (DefaultTimeToLive - null)

|TTL sull'articolo| Risultato|
|---|---|
|ttl - null|    TTL è disabilitato. L'elemento non scadrà mai (impostazione predefinita).|
|ttl - -1   |TTL è disabilitato. L'elemento non scadrà mai.|
|ttl - 2000 |TTL è disabilitato. L'elemento non scadrà mai.|


### <a name="example-2"></a>Esempio 2

TTL nel contenitore è impostato su -1 (DefaultTimeToLive - -1)

|TTL sull'articolo| Risultato|
|---|---|
|ttl - null |TTL è abilitato. L'elemento non scadrà mai (impostazione predefinita).|
|ttl - -1   |TTL è abilitato. L'elemento non scadrà mai.|
|ttl - 2000 |TTL è abilitato. L'elemento scadrà dopo 2000 secondi.|


### <a name="example-3"></a>Esempio 3

TTL nel contenitore è impostato su 1000 (DefaultTimeToLive - 1000)

|TTL sull'articolo| Risultato|
|---|---|
|ttl - null|    TTL è abilitato. L'elemento scadrà dopo 1000 secondi (impostazione predefinita).|
|ttl - -1   |TTL è abilitato. L'elemento non scadrà mai.|
|ttl - 2000 |TTL è abilitato. L'elemento scadrà dopo 2000 secondi.|

## <a name="next-steps"></a>Passaggi successivi

Scopri come configurare Time to Live nei seguenti articoli:

* [Come configurare la durata (TTL)](how-to-time-to-live.md)
