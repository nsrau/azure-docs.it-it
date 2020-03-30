---
title: Partizionamento e scalabilità orizzontale in Azure Cosmos DB
description: Informazioni sul funzionamento del partizionamento in Azure Cosmos DB, su come configurare il partizionamento e le chiavi di partizione e su come scegliere la chiave di partizione più adatta per l'applicazione.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246617"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partizionamento e scalabilità orizzontale in Azure Cosmos DB

Questo articolo illustra le partizioni fisiche e logiche in Azure Cosmos DB. Discute inoltre le procedure consigliate per il ridimensionamento e il partizionamento. 

## <a name="logical-partitions"></a>Partizioni logiche

Una partizione logica è costituita da un set di elementi che hanno la stessa chiave di partizione. Ad esempio, in un contenitore `City` in cui `City` tutti gli elementi contengono una proprietà, è possibile usare come chiave di partizione per il contenitore. Gruppi di elementi con `City`valori specifici `London` `Paris`per `NYC`, ad esempio , e , formano partizioni logiche distinte. Non devi preoccuparti di eliminare una partizione quando vengono eliminati i dati sottostanti.

In Azure Cosmos DB un contenitore è l'unità fondamentale di scalabilità. I dati aggiunti al contenitore e la velocità effettiva di cui si esegue il provisioning nel contenitore vengono partizionati automaticamente (orizzontalmente) in un set di partizioni logiche. I dati e la velocità effettiva vengono partizionati in base alla chiave di partizione specificata per il contenitore Cosmos di Azure.Data and throughput are partitioned based on the partition key you specify for the Azure Cosmos container. Per altre informazioni, vedere [Creare un contenitore Cosmos](how-to-create-container.md)di Azure.For more information, see Create an Azure Cosmos container .

Una partizione logica definisce anche l'ambito delle transazioni di database. È possibile aggiornare gli elementi all'interno di una partizione logica utilizzando una [transazione con isolamento dello snapshot](database-transactions-optimistic-concurrency.md). Quando vengono aggiunti nuovi elementi a un contenitore, le nuove partizioni logiche vengono create in modo trasparente dal sistema.

## <a name="physical-partitions"></a>Partizioni fisiche

Un contenitore Cosmos di Azure viene ridimensionato distribuendo dati e velocità effettiva in un numero elevato di partizioni logiche. Internamente, una o più partizioni logiche vengono mappate a una partizione fisica costituita da un set di repliche, denominato anche set di [*repliche*](global-dist-under-the-hood.md). Ogni set di repliche ospita un'istanza del motore di database Cosmos di Azure.Each replica set hosts an instance of the Azure Cosmos database engine. Un set di repliche rende i dati archiviati all'interno della partizione fisica durevoli, altamente disponibili e coerenti. Una partizione fisica supporta la quantità massima di unità di archiviazione e richiesta (RU). Ogni replica che costituisce la partizione fisica eredita la quota di archiviazione della partizione. Tutte le repliche di una partizione fisica supportano collettivamente la velocità effettiva allocata alla partizione fisica. 

La figura seguente mostra come le partizioni logiche vengono mappate alle partizioni fisiche distribuite a livello globale:

![Immagine che illustra il partizionamento di Azure Cosmos DB](./media/partition-data/logical-partitions.png)

La velocità effettiva di cui è stato eseguito il provisioning per un contenitore viene suddivisa in modo uniforme tra le partizioni fisiche. Una progettazione di chiavi di partizione che non distribuisce le richieste di velocità effettiva in modo uniforme potrebbe creare partizioni "calde". Le partizioni a caldo potrebbero comportare la limitazione della frequenza e un utilizzo inefficiente della velocità effettiva di cui è stato eseguito il provisioning e costi più elevati.

A differenza delle partizioni logiche, le partizioni fisiche sono un'implementazione interna del sistema. Non è possibile controllare le dimensioni, la posizione o il conteggio delle partizioni fisiche e non è possibile controllare il mapping tra partizioni logiche e partizioni fisiche. Tuttavia, è possibile controllare il numero di partizioni logiche e la distribuzione dei dati, del carico di lavoro e della velocità effettiva scegliendo la chiave di [partizione logica corretta.](partitioning-overview.md#choose-partitionkey)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla scelta di una chiave di [partizione](partitioning-overview.md#choose-partitionkey).
* Informazioni sulla velocità effettiva di cui è stato eseguito il [provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
