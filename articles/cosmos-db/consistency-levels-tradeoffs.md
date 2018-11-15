---
title: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB | Microsoft Docs
description: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB.
keywords: coerenza, prestazioni, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 0e4105d6f56a8eb45a83e970c85319cf25041781
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514775"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB

I database distribuiti che si basano sulla replica per gestire la disponibilità elevata, la bassa latenza o entrambe, applicano il compromesso fondamentale tra coerenza di lettura e disponibilità, latenza e velocità effettiva. Azure Cosmos DB affronta la coerenza dei dati offrendo una serie di scelte invece dei due estremi della coerenza assoluta e finale. Cosmos DB offre agli sviluppatori la possibilità di scegliere tra cinque modelli di coerenza ben definiti nello spettro della coerenza (dal più sicuro al più debole): **assoluto**, **decadimento ristretto**, **sessione**, **coerenza del prefisso** e **finale**. Ognuno dei cinque modelli prevede compromessi di disponibilità e prestazioni ed è supportato da un contratto di servizio completo.

## <a name="consistency-levels-and-latency"></a>Livelli di coerenza e latenza

- La **latenza di lettura** per tutti i livelli di coerenza è sempre minore ai 10 millisecondi al 99° percentile ed è supportata dal contratto di servizio. La latenza di lettura media (al 50° percentile) è in genere uguale o inferiore ai 2 millisecondi.

- Ad eccezione degli account Cosmos, che si estendono su più aree geografiche e sono configurati con la coerenza assoluta, la **latenza di scrittura** per gli altri livelli di coerenza è sempre inferiore a 10 millisecondi al 99° percentile. Questa latenza di scrittura è supportata dal contratto di servizio. La latenza di scrittura media (al 50° percentile) è in genere uguale o inferiore ai 5 millisecondi.

- Per gli account Cosmos su più aree configurati con coerenza assoluta (attualmente in anteprima), la **latenza di scrittura** è sempre inferiore a < (2 * Round-trip time/RTT) + 10 millisecondi al 99° percentile. Il tempo RTT è calcolato tra due delle regioni più distanti tra loro associate all'account Cosmos. La latenza RTT esatta è una funzione della velocità della luce e l'esatta topologia di rete di Azure. La rete di Azure non fornisce nessun contratto di servizio di latenza per il tempo RTT tra due aree di Azure. Le latenze di replica di Cosmos DB sono visualizzate nel portale di Azure per l'account Cosmos, consentendo di monitorare le latenze di replica tra le varie aree associate all'account Cosmos.

## <a name="consistency-levels-and-throughput"></a>Livelli di coerenza e velocità effettiva

- Per la stessa quantità di unità di richieste, i livelli di coerenza sessione, prefisso coerente e finale forniscono una velocità effettiva di lettura due volte maggiore in confronto ai livelli assoluto e con decadimento ristretto.

- Per un determinato tipo di operazione di scrittura (come inserimento, sostituzione, upsert, eliminazione ecc.), la velocità effettiva di scrittura per le unità di richieste è identica per tutti i livelli di coerenza.

## <a name="next-steps"></a>Passaggi successivi

Nei seguenti articoli si troveranno altre informazioni relative ai compromessi tra coerenza generale e distribuzione globale nei sistemi distribuiti:

* [Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Disponibilità elevata](high-availability.md)
* [Contratto di servizio di Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
