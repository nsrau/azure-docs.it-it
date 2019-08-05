---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a24159a482c55496bb1e6031be951196258a6796
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565486"
---
È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database a grafo. 

1. Fare clic su **Esplora dati** > **New Graph** (Nuovo grafo).

    Viene visualizzata l'area **Add Graph** (Aggiungi grafo) all'estrema destra. Per vederla potrebbe essere necessario scorrere la schermata.

    ![Pagina Esplora dati, Add Graph (Aggiungi grafico) nel portale di Azure](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. Nella pagina **Add Graph** (Aggiungi grafo) immettere le impostazioni per il nuovo grafo.

    Impostazione|Valore consigliato|DESCRIZIONE
    ---|---|---
    ID database|sample-database|Immettere *sample-database* come nome del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali.
    Velocità effettiva|400 UR/s|Modificare la velocità effettiva in 400 unità richiesta al secondo (UR/sec). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento.
    ID grafo|sample-graph|Immettere *sample-graph* come nome della nuova raccolta. I nomi dei grafi presentano gli stessi requisiti relativi ai caratteri degli ID di database.
    Chiave di partizione| /pk |Per la scalabilità orizzontale di tutti gli account Cosmos DB, è necessaria una chiave di partizione. Per informazioni su come selezionare una chiave di partizione appropriata, vedere l'articolo [Partizionamento dei dati dei grafici](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

3. Dopo aver compilato il modulo, fare clic su **OK**.
