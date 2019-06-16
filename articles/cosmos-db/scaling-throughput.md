---
title: Ridimensionare la velocità effettiva per i contenitori di Azure Cosmos DB
description: In questo articolo viene descritto come Azure Cosmos DB ridimensiona la velocità effettiva in modo elastico
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: f930b5c478cc880952b4559be4c6647b260efcf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243502"
---
# <a name="globally-scale-provisioned-throughput"></a>Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning 

In Azure Cosmos DB, velocità effettiva con provisioning viene rappresentata come richiesta di unità al secondo (UR/s o la forma plurale UR). Le UR misurano il costo delle operazioni sia lettura che di scrittura sul contenitore Cosmos, come illustrato nell'immagine seguente:

![Unità richiesta](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

È possibile effettuare il provisioning delle unità richiesta in un contenitore Cosmos o in un database Cosmos. Unità richiesta con provisioning in un contenitore è disponibili esclusivamente per le operazioni eseguite in tale contenitore. Le unità richiesta con provisioning in un database sono condivise tra tutti i contenitori all'interno del database (ad eccezione di eventuali contenitori esclusivamente assegnati a unità di richiesta).

Per la scalabilità in modo elastico velocità effettiva con provisioning, è possibile aumentare o ridurre le UR/sec con provisioning in qualsiasi momento. Per altre informazioni, vedere [velocità effettiva di provisioning alle procedure](set-throughput.md) e ridimensionare i database e dei contenitori Cosmos. Per la scalabilità a livello globale della velocità effettiva, è possibile aggiungere o rimuovere aree dall'account Cosmos in qualsiasi momento. Per altre informazioni, vedere [Aggiungere o rimuovere aree dall'account di database](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Associazione di più aree con un account Cosmos è importante in molti scenari, per ottenere una latenza bassa e [disponibilità elevata](high-availability.md) tutto il mondo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Come viene distribuita la velocità effettiva di cui è stato effettuato il provisioning tra le diverse aree

Se esegue il provisioning *'R'* RUs in un contenitore Cosmos (o database), Cosmos DB garantisce che *'R'* unità riservate sono disponibili nelle *ogni* aree associate all'account Cosmos. Ogni volta che si aggiunge una nuova area all'account, Cosmos DB esegue automaticamente il provisioning *"R"* unità riservate nell'area appena aggiunto. Le operazioni eseguite a fronte del contenitore Cosmos ricevano sempre *"R"* UR in ogni area. Non è possibile assegnare in modo selettivo le UR a un'area specifica. Vengono effettuato il provisioning di unità richiesta con provisioning in un contenitore Cosmos (o database) in tutte le aree associate all'account Cosmos.

Supponendo che sia configurato con un contenitore Cosmos *'R'* UR e non esistono *"n"* aree associate all'account Cosmos, quindi:

- Se l'account Cosmos è configurato con un'area singola operazione di scrittura, totale delle unità richiesta disponibili a livello globale per il contenitore = *R* x *N*.

- Se l'account Cosmos è configurata con più aree di scrittura, totale delle unità richiesta disponibili a livello globale per il contenitore = *R* x (*N*+ 1). Le ulteriori *R* UR effettuato il provisioning automatico di conflitti di aggiornamento processo e il traffico di Microsoft anti-entropia tra le aree.

Di che preferisci [modello di coerenza](consistency-levels.md) influisce anche sulla velocità effettiva. È possibile ottenere circa 2x velocità effettiva di lettura per i livelli di coerenza più ampi (ad esempio, *sessione*, *prefisso coerente* e *finale* coerenza) rispetto a livelli di coerenza più avanzati (ad esempio, *decadimento ristretto* oppure *sicuro* coerenza).

## <a name="next-steps"></a>Passaggi successivi

Successivamente è possibile imparare a configurare la velocità effettiva in un contenitore o del database:

* [Ottenere e impostare la velocità effettiva per i contenitori e i database](set-throughput.md) 

