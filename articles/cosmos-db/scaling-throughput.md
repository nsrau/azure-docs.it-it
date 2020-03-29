---
title: Ridimensionare la velocità effettiva per i contenitori di Azure Cosmos DB
description: Questo articolo descrive come Azure Cosmos DB ridimensiona la velocità effettiva tra aree diverse in cui viene eseguito il provisioning dell'account Cosmos di Azure.This article describes how Azure Cosmos DB scales throughput across different regions where the Azure Cosmos account is provisioned.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873506"
---
# <a name="globally-scale-provisioned-throughput"></a>Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning 

In Azure Cosmos DB, la velocità effettiva di cui è stato eseguito il provisioning è rappresentata come unità di richiesta/secondo (RU/s o il formato plurale RUs). Le UR misurano il costo delle operazioni sia lettura che di scrittura sul contenitore Cosmos, come illustrato nell'immagine seguente:

![Unità richiesta](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

È possibile effettuare il provisioning delle unità richiesta in un contenitore Cosmos o in un database Cosmos. Le RU di cui è stato eseguito il provisioning in un contenitore sono disponibili esclusivamente per le operazioni eseguite su tale contenitore. Le unità richiesta con provisioning in un database sono condivise tra tutti i contenitori all'interno del database (ad eccezione di eventuali contenitori esclusivamente assegnati a unità di richiesta).

Per la velocità effettiva con provisioning con scalabilità elastica, è possibile aumentare o ridurre le RU/s di cui è stato eseguito il provisioning in qualsiasi momento. Per altre informazioni, vedere Procedura di provisioning della velocità effettiva e scala elastica di contenitori e database Cosmos.For more information, see [How-to provision throughput](set-throughput.md) and to elastically scale Cosmos containers and databases. Per la velocità effettiva con scalabilità globale, è possibile aggiungere o rimuovere aree dall'account Cosmos in qualsiasi momento. Per altre informazioni, vedere [Aggiungere o rimuovere aree dall'account di database](how-to-manage-database-account.md#addremove-regions-from-your-database-account). L'associazione di più aree a un account Cosmos è importante in molti scenari: per ottenere bassa latenza e [disponibilità elevata](high-availability.md) in tutto il mondo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Come viene distribuita la velocità effettiva di cui è stato effettuato il provisioning tra le diverse aree

Se si esegue il provisioning di *R* Su su un contenitore Cosmos (o database), Cosmos DB assicura che le RU *'R'* siano disponibili in *ogni* area associata all'account Cosmos. Ogni volta che aggiungi una nuova regione al tuo account, Cosmos DB esegue automaticamente il provisioning delle RU *'R'* nella regione appena aggiunta. Le operazioni eseguite sul contenitore Cosmos sono garantite per ottenere *'R'* RU in ogni area. Non è possibile assegnare in modo selettivo le UR a un'area specifica. Le RU di cui è stato eseguito il provisioning in un contenitore (o database) Cosmos vengono sottoposte a provisioning in tutte le aree associate all'account Cosmos.

Supponendo che un contenitore Cosmos sia configurato con *R'* e che vi siano *regioni 'N'* associate all'account Cosmos, quindi:

- Se l'account Cosmos è configurato con una singola area di scrittura, il totale delle RU disponibili a livello globale nel contenitore, ovvero *R* x *N*.

- Se l'account Cosmos è configurato con più aree di scrittura, le RU totali disponibili a livello globale nel contenitore: *R* x *(N*n . 1). Viene eseguito automaticamente il provisioning delle RU *aggiuntive* per elaborare i conflitti di aggiornamento e il traffico anti-entropia tra le aree.

Il modello di [coerenza](consistency-levels.md) scelto influisce anche sulla velocità effettiva. È possibile ottenere una velocità effettiva di lettura di circa 2 x per i livelli di coerenza più rilassati (ad esempio, *sessione,* *prefisso coerente* ed *eventuale* coerenza) rispetto a livelli di coerenza più elevati (ad esempio, *stantio limitato* o coerenza *forte).*

## <a name="next-steps"></a>Passaggi successivi

Successivamente è possibile imparare a configurare la velocità effettiva in un contenitore o un database:Next you can learn how to configure throughput on a container or database:

* [Ottenere e impostare la velocità effettiva per i contenitori e i database](set-throughput.md) 

