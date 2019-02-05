---
title: Ridimensionare la velocità effettiva per i contenitori di Azure Cosmos DB
description: In questo articolo viene descritto come Azure Cosmos DB ridimensiona la velocità effettiva in modo elastico
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: dd17b08a16dedf474b2a1eca8fa8034672610c1f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454437"
---
# <a name="globally-scale-provisioned-throughput"></a>Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning 

In Azure Cosmos DB la velocità effettiva con provisioning viene rappresentata come unità di richieste al secondo (UR/s, plurale: unità richiesta). Le UR misurano il costo delle operazioni sia lettura che di scrittura sul contenitore Cosmos, come illustrato nell'immagine seguente:

![Unità richiesta](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

È possibile effettuare il provisioning delle unità richiesta in un contenitore Cosmos o in un database Cosmos. Le unità richiesta con provisioning in un contenitore sono disponibili esclusivamente per le operazioni eseguite in tale contenitore. Le unità richiesta con provisioning in un database sono condivise tra tutti i contenitori all'interno del database (ad eccezione di eventuali contenitori esclusivamente assegnati a unità di richiesta).

Per ridimensionare in modo elastico la velocità effettiva, aumentare o ridurre le UR/sec con provisioning in qualsiasi momento. Per altre informazioni, vedere [come effettuare il provisioning della velocità effettiva](set-throughput.md) e ridimensionare in modo elastico i database e i contenitori Cosmos. Per la scalabilità a livello globale della velocità effettiva, è possibile aggiungere o rimuovere aree nell'account Cosmos in qualsiasi momento. Per altre informazioni, vedere [Aggiungere o rimuovere aree dall'account di database](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Associare più aree con un account Cosmos è importante in molti scenari per ottenere una latenza bassa e una [disponibilità elevata](high-availability.md) in tutto il mondo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Come viene distribuita la velocità effettiva di cui è stato effettuato il provisioning tra le diverse aree

Se si esegue il provisioning di UR di "R" in un contenitore Cosmos (o database), Cosmos DB garantisce che le UR di "R" siano disponibili in *ogni* area associata all'account Cosmos. Ogni volta che si aggiunge una nuova area nell'account, Cosmos DB esegue automaticamente il provisioning UR di "R" nell'area appena aggiunta. Le operazioni eseguite a fronte del contenitore Cosmos garantiscono di ottenere UR di "R" in ognuna delle aree. Non è possibile assegnare in modo selettivo le UR a un'area specifica. Le UR con provisioning per un contenitore (o database) Cosmos vengono sottoposte a provisioning per tutte le aree associate all'account Cosmos.

Supponendo che un contenitore Cosmos sia configurato con UR di "R" e sono presenti aree di "N" associate all'account Cosmos, quindi:

- Se l'account Cosmos è configurato con un'area singola di scrittura, il totale delle unità di richiesta disponibili a livello globale per il contenitore = R x N.

- Se l'account Cosmos è configurato con più aree di scrittura, il totale delle unità di richiesta disponibili a livello globale per il contenitore = R x (N+1). Le unità di richiesta aggiuntive di R sono sottoposte automaticamente a provisioning per elaborare conflitti di aggiornamento e il traffico anti-entropia tra le aree.

Anche la scelta del [modello di coerenza](consistency-levels.md) influisce sulla velocità effettiva. È possibile ottenere una velocità effettiva di lettura di circa 2x per sessione, un prefisso coerente e la coerenza finale rispetto al decadimento ristretto o coerenza assoluta.

## <a name="next-steps"></a>Passaggi successivi

Successivamente si apprenderà come configurare la velocità effettiva con l'aiuto dell'articolo seguente:

* [Ottenere e impostare la velocità effettiva per i contenitori e i database](set-throughput.md) 

