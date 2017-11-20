---
title: Importare i dati da usare con l'API Tabelle di Azure Cosmos DB | Microsoft Docs
description: Informazioni su come importare dati da usare con l'API Tabelle di Azure Cosmos DB.
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 984510b2dae99849507953163f94ad2f925651cd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importare dati da usare con l'API Tabelle di Azure Cosmos DB

Questa esercitazione fornisce le istruzioni per importare i dati da usare con l'[API Tabelle](table-introduction.md) di Azure Cosmos DB. Se sono stati archiviati dati nell'archiviazione tabelle di Azure, è possibile usare sia l'Utilità di migrazione dati che il comando AzCopy per importare i dati in Azure Cosmos DB. Dopo l'importazione dei dati, sarà possibile usufruire delle funzionalità Premium offerte da Azure Cosmos DB, tra cui distribuzione globale chiavi in mano, velocità effettiva dedicata, latenze pari a singole unità di millisecondi al 99° percentile, disponibilità elevata garantita e indicizzazione secondaria automatica.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Importazione dei dati con l'Utilità di migrazione dati
> * Importazione dei dati con AzCopy

## <a name="data-migration-tool"></a>Utilità di migrazione dati

L'Utilità di migrazione dati di Azure Cosmos DB è una delle opzioni disponibili per importare dati dell'archiviazione tabelle di Azure. Per importare dati con questa utilità, selezionare l'archiviazione tabelle di Azure come origine e l'API Tabelle di Azure Cosmos DB come destinazione, fornendo le rimanenti informazioni come richiesto nell'utilità. È possibile importare i dati in blocco oppure usando l'importazione di record sequenziali. 

Per informazioni su come definire l'archiviazione tabelle di Azure come origine e l'API Tabelle come destinazione, vedere le sezioni seguenti:
- [Uso dell'archiviazione tabelle di Azure come origine della migrazione dei dati](import-data.md#AzureTableSource). 
- [Esportare nell'API Tabelle (importazione in blocco)](import-data.md#tableapibulkexport).
- [Esportare nell'API Tabelle (importazione di record sequenziali)](import-data.md#tableapiseqtarget).

## <a name="azcopy-command"></a>Comando AzCopy

L'utilità da riga di comando AzCopy è l'altra opzione per eseguire la migrazione dei dati dall'archiviazione tabelle di Azure all'API Tabelle di Azure Cosmos DB. Per usare AzCopy, è prima necessario esportare i dati in un file manifesto come descritto in [Esportare dati da Archiviazione tabelle](../storage/common/storage-use-azcopy.md#export-data-from-table-storage) e quindi importare i dati dal file manifesto nell'[API Tabelle di Azure Cosmos DB](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Importare dati con l'Utilità di migrazione dati
> * Importare dati con AzCopy

È ora possibile passare all'esercitazione successiva per ottenere informazioni su come eseguire query sui dati usando l'API Tabelle di Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Come eseguire query sui dati](../cosmos-db/tutorial-query-table.md)



