---
title: Partizionamento e scalabilità orizzontale in Azure Cosmos DB
description: Informazioni sul funzionamento del partizionamento in Azure Cosmos DB, su come configurare il partizionamento e le chiavi di partizione e su come scegliere la chiave di partizione corretta per l'applicazione.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754917"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partizionamento e scalabilità orizzontale in Azure Cosmos DB

Questo articolo illustra le partizioni fisiche e logiche in Azure Cosmos DB. Vengono inoltre descritte le procedure consigliate per la scalabilità e il partizionamento. 

## <a name="logical-partitions"></a>Partizioni logiche

Una partizione logica è costituita da un set di elementi con la stessa chiave di partizione. Ad esempio, in un contenitore in cui tutti gli elementi contengono una proprietà `City`, è possibile usare `City` come chiave di partizione per il contenitore. Gruppi di elementi con valori specifici per `City`, ad esempio `London`, `Paris` e `NYC`, formano partizioni logiche distinte. Non è necessario preoccuparsi di eliminare una partizione quando vengono eliminati i dati sottostanti.

In Azure Cosmos DB un contenitore è l'unità fondamentale di scalabilità. I dati aggiunti al contenitore e la velocità effettiva di cui viene effettuato il provisioning sul contenitore vengono partizionati automaticamente (orizzontalmente) in un set di partizioni logiche. I dati e la velocità effettiva sono partizionati in base alla chiave di partizione specificata per il contenitore Azure Cosmos. Per altre informazioni, vedere [creare un contenitore di Azure Cosmos](how-to-create-container.md).

Una partizione logica definisce anche l'ambito delle transazioni del database. È possibile aggiornare gli elementi all'interno di una partizione logica utilizzando una [transazione con isolamento dello snapshot](database-transactions-optimistic-concurrency.md). Quando vengono aggiunti nuovi elementi a un contenitore, le nuove partizioni logiche vengono create in modo trasparente dal sistema.

## <a name="physical-partitions"></a>Partizioni fisiche

Un contenitore Azure Cosmos viene ridimensionato distribuendo i dati e la velocità effettiva in un numero elevato di partizioni logiche. Internamente, viene eseguito il mapping di una o più partizioni logiche a una partizione fisica costituita da un set di repliche, noto anche come [*set di repliche*](global-dist-under-the-hood.md). Ogni set di repliche ospita un'istanza del motore di database di Azure Cosmos. Un set di repliche rende i dati archiviati all'interno della partizione fisica durevole, a disponibilità elevata e coerente. Una partizione fisica supporta la quantità massima di unità di archiviazione e di unità richiesta (UR). Ogni replica che costituisce la partizione fisica eredita la quota di archiviazione della partizione. Tutte le repliche di una partizione fisica supportano collettivamente la velocità effettiva allocata alla partizione fisica. 

La figura seguente mostra come le partizioni logiche vengono mappate alle partizioni fisiche distribuite a livello globale:

![Immagine che illustra il partizionamento Azure Cosmos DB](./media/partition-data/logical-partitions.png)

La velocità effettiva con provisioning per un contenitore è divisa uniformemente tra le partizioni fisiche. Una progettazione di chiavi di partizione che non distribuisce in modo uniforme le richieste di velocità effettiva potrebbe creare partizioni "Hot". Le partizioni a caldo possono comportare la limitazione della frequenza e l'utilizzo inefficiente della velocità effettiva con provisioning e costi più elevati.

A differenza delle partizioni logiche, le partizioni fisiche sono un'implementazione interna del sistema. Non è possibile controllare le dimensioni, la posizione o il numero di partizioni fisiche e non è possibile controllare il mapping tra partizioni logiche e partizioni fisiche. Tuttavia, è possibile controllare il numero di partizioni logiche e la distribuzione dei dati, del carico di lavoro e della velocità effettiva [scegliendo la chiave di partizione logica corretta](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [scelta di una chiave di partizione](partitioning-overview.md#choose-partitionkey).
* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
