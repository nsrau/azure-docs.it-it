---
title: Partizionamento e scalabilità orizzontale in Azure Cosmos DB
description: Informazioni su come funziona il partizionamento in Azure Cosmos DB, come configurare il partizionamento e chiavi di partizione e come scegliere la chiave di partizione corretta per l'applicazione.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797824"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partizionamento e scalabilità orizzontale in Azure Cosmos DB

Questo articolo illustra le partizioni fisiche e logiche in Azure Cosmos DB. Illustra anche le procedure consigliate per la scalabilità e partizionamento. 

## <a name="logical-partitions"></a>Partizioni logiche

Una partizione logica è costituito da un set di elementi che presentano la stessa chiave di partizione. Ad esempio, in un contenitore in cui tutti gli elementi contengono una `City` proprietà, è possibile usare `City` come chiave di partizione per il contenitore. Gruppi di elementi con valori specifici per `City`, ad esempio `London`, `Paris`, e `NYC`, costituiscono le partizioni logiche distinte. Non devi preoccuparti di eliminazione di una partizione quando viene eliminati i dati sottostanti.

In Azure Cosmos DB un contenitore è l'unità fondamentale di scalabilità. I dati che viene aggiunto al contenitore e la velocità effettiva che si esegue il provisioning del contenitore vengono automaticamente partizionati (orizzontalmente) in un set di partizioni logiche. Velocità effettiva e i dati vengono partizionati in base alla chiave di partizione specificata per il contenitore di Azure Cosmos. Per altre informazioni, vedere [creare un contenitore di Azure Cosmos](how-to-create-container.md).

Una partizione logica definisce anche l'ambito delle transazioni di database. È possibile aggiornare gli elementi all'interno di una partizione logica usando un [delle transazioni con isolamento dello snapshot](database-transactions-optimistic-concurrency.md). Quando vengono aggiunti nuovi elementi a un contenitore, le nuove partizioni logiche vengono create in modo trasparente dal sistema.

## <a name="physical-partitions"></a>Partizioni fisiche

Un contenitore di Azure Cosmos viene ridimensionato distribuendo i dati e la velocità effettiva tra un numero elevato di partizioni logiche. Internamente, uno o più partizioni logiche vengono mappate a una partizione fisica che è costituito da un set di repliche, noto anche come un [ *set di repliche*](global-dist-under-the-hood.md). Ogni set di repliche ospita un'istanza del motore di database Azure Cosmos DB. Un set di repliche rende i dati archiviati all'interno della partizione fisica durevole, altamente disponibile e coerente. Una partizione fisica supporta la quantità massima di unità di archiviazione e richiesta (UR). Ogni replica che costituisce la partizione fisica eredita la quota di archiviazione della partizione. Tutte le repliche di una partizione fisica supportano la velocità effettiva allocata per la partizione fisica. 

La figura seguente mostra come le partizioni logiche vengono mappate alle partizioni fisiche distribuite a livello globale:

![Un'immagine che illustra il partizionamento di Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Velocità effettiva di provisioning per un contenitore viene suddiviso equamente tra le partizioni fisiche. Una progettazione di chiave di partizione che non distribuisce uniformemente le richieste di velocità effettiva potrebbe creare partizioni "critiche". Partizioni critiche potrebbe nella limitazione della frequenza e nell'uso inefficiente della velocità effettiva con provisioning e costi più elevati.

A differenza delle partizioni logiche, le partizioni fisiche sono un'implementazione interna del sistema. Non è possibile controllare le dimensioni, posizione o conteggio delle partizioni fisiche e non è possibile controllare il mapping tra le partizioni logiche e le partizioni fisiche. Tuttavia, è possibile controllare il numero di partizioni logiche e la distribuzione dei dati, il carico di lavoro e velocità effettiva [scelta la chiave di partizione logica corretta](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Passaggi successivi

* Scopri [scelta di una chiave di partizione](partitioning-overview.md#choose-partitionkey).
* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
