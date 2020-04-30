---
title: Partizionamento e scalabilità orizzontale in Azure Cosmos DB
description: Informazioni sul funzionamento del partizionamento in Azure Cosmos DB, su come configurare il partizionamento e le chiavi di partizione e su come scegliere la chiave di partizione corretta per l'applicazione.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 19e4c61ba930bb9b127e2401174bcea3fd240dce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234215"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partizionamento e scalabilità orizzontale in Azure Cosmos DB

In questo articolo viene illustrata la relazione tra partizioni logiche e fisiche. Vengono inoltre illustrate le procedure consigliate per il partizionamento e viene fornita una visualizzazione approfondita del funzionamento del ridimensionamento orizzontale in Azure Cosmos DB. Non è necessario comprendere questi dettagli interni per [selezionare la chiave di partizione](partitioning-overview.md#choose-partitionkey) , ma è stata analizzata in modo da ottenere chiarezza sul funzionamento di Azure Cosmos DB.

## <a name="logical-partitions"></a>Partizioni logiche

Una partizione logica è costituita da un set di elementi con la stessa chiave di partizione. Ad esempio, in un contenitore che contiene i dati relativi al cibo dietetico, tutti `foodGroup` gli elementi contengono una proprietà. È possibile usare `foodGroup` come chiave di partizione per il contenitore. Gruppi di elementi con `foodGroup`valori specifici per, ad esempio `Beef Products`,`Baked Products`e `Sausages and Luncheon Meats`, formano partizioni logiche distinte. Non è necessario preoccuparsi di eliminare una partizione logica quando vengono eliminati i dati sottostanti.

Una partizione logica definisce anche l'ambito delle transazioni del database. È possibile aggiornare gli elementi all'interno di una partizione logica utilizzando una [transazione con isolamento dello snapshot](database-transactions-optimistic-concurrency.md). Quando vengono aggiunti nuovi elementi a un contenitore, le nuove partizioni logiche vengono create in modo trasparente dal sistema.

Non esiste alcun limite al numero di partizioni logiche nel contenitore. Ogni partizione logica può archiviare fino a 20 GB di dati. Le scelte valide per le chiavi di partizione hanno un'ampia gamma di valori possibili. Ad esempio, in un contenitore in cui tutti gli elementi `foodGroup`contengono una proprietà, i dati `Beef Products` all'interno della partizione logica possono aumentare fino a 20 GB. La [selezione di una chiave di partizione](partitioning-overview.md#choose-partitionkey) con una vasta gamma di valori possibili garantisce che il contenitore possa essere ridimensionato.

## <a name="physical-partitions"></a>Partizioni fisiche

Un contenitore Azure Cosmos viene ridimensionato distribuendo i dati e la velocità effettiva tra le partizioni fisiche. Internamente, viene eseguito il mapping di una o più partizioni logiche a una singola partizione fisica. Per la maggior parte dei contenitori Cosmos piccoli sono disponibili molte partizioni logiche, ma è necessaria solo una singola partizione fisica. Diversamente dalle partizioni logiche, le partizioni fisiche sono un'implementazione interna del sistema e sono completamente gestite da Azure Cosmos DB.

Il numero di partizioni fisiche nel contenitore Cosmos dipende da quanto segue:

- Quantità di velocità effettiva con provisioning (ogni singola partizione fisica può fornire una velocità effettiva massima di 10.000 unità richiesta al secondo)
- Archiviazione dati totale (ogni singola partizione fisica può archiviare fino a 50 GB)

Non esiste alcun limite al numero totale di partizioni fisiche nel contenitore. Man mano che la velocità effettiva con provisioning o la dimensione dei dati aumenta, Azure Cosmos DB creerà automaticamente nuove partizioni fisiche suddividendo quelle esistenti. Le divisioni di partizioni fisiche non influiscano sulla disponibilità dell'applicazione. Dopo la suddivisione della partizione fisica, tutti i dati all'interno di una singola partizione logica verranno comunque archiviati nella stessa partizione fisica. Una suddivisione della partizione fisica crea semplicemente un nuovo mapping delle partizioni logiche alle partizioni fisiche.

La velocità effettiva con provisioning per un contenitore è divisa uniformemente tra le partizioni fisiche. Una progettazione di chiavi di partizione che non distribuisce in modo uniforme le richieste di velocità effettiva potrebbe creare partizioni "Hot". Le partizioni a caldo possono comportare la limitazione della frequenza e l'utilizzo inefficiente della velocità effettiva con provisioning e costi più elevati.

È possibile visualizzare le partizioni fisiche del contenitore nella sezione **archiviazione** del pannello **metriche** del portale di Azure:

[![Visualizzazione del numero di partizioni](./media/partition-data/view-partitions-zoomed-out.png) fisiche](./media/partition-data/view-partitions-zoomed-in.png#lightbox)

In questo contenitore di esempio, in cui `/foodGroup` è stata scelta come chiave di partizione, ognuno dei tre rettangoli rappresenta una partizione fisica. Nell'immagine l' **intervallo di chiavi di partizione** è identico a quello di una partizione fisica. La partizione fisica selezionata contiene tre partizioni logiche: `Beef Products`, `Vegetable and Vegetable Products`e `Soups, Sauces, and Gravies`.

Se si effettua il provisioning di una velocità effettiva di 18.000 unità richiesta al secondo (UR/sec), ciascuna delle tre partizioni fisiche potrà usare 1/3 della velocità effettiva totale con provisioning. All'interno della partizione fisica selezionata, le chiavi `Beef Products`6.000 di `Vegetable and Vegetable Products`partizione logiche `Soups, Sauces, and Gravies` , e possono, collettivamente, utilizzare le UR/sec con provisioning della partizione fisica. Poiché la velocità effettiva con provisioning è divisa uniformemente tra le partizioni fisiche del contenitore, è importante scegliere una chiave di partizione che distribuisca equamente l'utilizzo della velocità effettiva [scegliendo la chiave di partizione logica corretta](partitioning-overview.md#choose-partitionkey). Se si sceglie una chiave di partizione che distribuisce in modo uniforme l'utilizzo della velocità effettiva tra partizioni logiche, si garantisce che l'utilizzo della velocità effettiva tra le partizioni fisiche sia bilanciato.

## <a name="replica-sets"></a>Set di repliche

Ogni partizione fisica è costituita da un set di repliche, denominato anche [*set di repliche*](global-dist-under-the-hood.md). Ogni set di repliche ospita un'istanza del motore di database di Azure Cosmos. Un set di repliche rende i dati archiviati all'interno della partizione fisica durevole, a disponibilità elevata e coerente. Ogni replica che costituisce la partizione fisica eredita la quota di archiviazione della partizione. Tutte le repliche di una partizione fisica supportano collettivamente la velocità effettiva allocata alla partizione fisica. Azure Cosmos DB gestisce automaticamente i set di repliche.

La maggior parte dei contenitori Cosmos di piccole dimensioni richiede solo una singola partizione fisica, ma avrà comunque almeno 4 repliche.

La figura seguente mostra come le partizioni logiche vengono mappate alle partizioni fisiche distribuite a livello globale:

![Immagine che illustra il partizionamento Azure Cosmos DB](./media/partition-data/logical-partitions.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [scelta di una chiave di partizione](partitioning-overview.md#choose-partitionkey).
* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
