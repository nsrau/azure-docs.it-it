---
title: Linee guida per la progettazione di tabelle di archiviazione di Azure | Microsoft Docs
description: Progettare il servizio tabelle di Azure per supportare le operazioni di lettura in modo efficiente.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61269848"
---
# <a name="guidelines-for-table-design"></a>Linee guida per la progettazione di tabelle

La progettazione di tabelle per l'uso con il servizio tabelle di archiviazione di Azure è molto diversa dalle considerazioni di progettazione per un database relazionale. Questo articolo descrive le linee guida per la progettazione di una soluzione di servizio tabelle in grado di supportare in modo efficiente le operazioni di lettura e scrittura.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Progettare una soluzione di servizio tabelle efficiente per le operazioni di lettura

* ***Progettazione per le query nelle applicazioni con intensa attività di lettura.*** Quando si progettano le tabelle, considerare le query (soprattutto quelle sensibili alla latenza) che si eseguiranno prima di pensare a come si aggiorneranno le entità. Ciò comporta in genere una soluzione efficiente e ad alte prestazioni.  
* ***Specificare PartitionKey e RowKey nelle query.*** *Scegliere query* come queste sono le query più efficienti del servizio tabella.  
* ***Prendere in considerazione l'archiviazione di copie duplicate delle entità.*** Poiché l'archiviazione tabelle è economica, considerare la possibilità di archiviare la stessa entità più volte (con chiavi diverse) per consentire query più efficienti.  
* ***Considerare la denormalizzazione dei dati.*** L’archiviazione delle tabelle è economica, dunque è opportuno considerare la denormalizzazione dei dati. Ad esempio, archiviare le entità di riepilogo in modo che le query per aggregare i dati debbano accedere a una singola entità.  
* ***Usare valori chiave composti.*** Le sole chiavi a disposizione sono **PartitionKey** e **RowKey**. Ad esempio, per abilitare percorsi alternativi per l'accesso con chiave alle entità, ad esempio, utilizzare valori chiave composti.  
* ***Usare la proiezione di query.*** È possibile ridurre la quantità di dati trasferiti tramite la rete usando query che selezionano solo i campi necessari.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Progettare una soluzione di servizio tabelle efficiente per le operazioni di scrittura  

* ***Non creare partizioni critiche.*** Scegliere chiavi che consentono di distribuire le richieste tra più partizioni in qualsiasi momento.  
* ***Evitare picchi di traffico.*** Contenere il traffico in un intervallo di tempo ragionevole ed evitare i picchi di traffico.
* ***Non creare necessariamente una tabella separata per ogni tipo di entità.*** Quando è necessario eseguire transazioni atomiche tra diversi tipi di entità, è possibile archiviare questi tipi di entità nella stessa partizione della stessa tabella.
* ***Considerare la velocità effettiva massima che è necessario raggiungere.*** È necessario tenere presenti gli obiettivi di scalabilità per il servizio tabelle e assicurarsi di non superarli con la progettazione.  

Questa guida contiene esempi in cui vengono messi in pratica tutti questi principi. 

## <a name="next-steps"></a>Passaggi successivi

- [Modelli di progettazione tabella](table-storage-design-patterns.md)
- [Progettazione per le query](table-storage-design-for-query.md)
- [Crittografia dei dati delle tabelle](table-storage-design-encrypt-data.md)
- [Progettazione per la modifica dei dati](table-storage-design-for-modification.md)
