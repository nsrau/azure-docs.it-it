---
title: Eliminare i dati da Esplora dati di Azure
description: Questo articolo descrive gli scenari di eliminazione in Azure Esplora dati, inclusi ripulitura, eliminazione di extent e eliminazioni basate sulla conservazione.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 681cfd71d2666630b192935d66ba32eaf16c92de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204614"
---
# <a name="delete-data-from-azure-data-explorer"></a>Eliminare i dati da Esplora dati di Azure

Azure Esplora dati supporta diversi scenari di eliminazione descritti in questo articolo. 

## <a name="delete-data-using-the-retention-policy"></a>Eliminare i dati usando i criteri di conservazione

Azure Esplora dati Elimina automaticamente i dati in base ai [criteri di conservazione](/azure/kusto/management/retentionpolicy). Questo metodo è il modo più efficiente e senza problemi di eliminare i dati. Impostare i criteri di conservazione a livello di database o di tabella.

Si consideri un database o una tabella che viene impostata su 90 giorni di conservazione. Se sono necessari solo 60 giorni di dati, eliminare i dati meno recenti come indicato di seguito:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Eliminazione di dati tramite l'eliminazione di extent

[Extent (partizione di dati)](/azure/kusto/management/extents-overview) è la struttura interna in cui vengono archiviati i dati. Ogni extent può ospitare fino a milioni di record. Gli extent possono essere eliminati singolarmente o come un gruppo usando [i comandi drop extent (s)](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Esempi

È possibile eliminare tutte le righe di una tabella o solo un extent specifico.

* Eliminare tutte le righe di una tabella:

    ```kusto
    .drop extents from TestTable
    ```

* Eliminare un extent specifico:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Elimina singole righe mediante ripulitura

L'eliminazione [dei dati](/azure/kusto/management/data-purge) può essere usata per eliminare le righe di singoli utenti. L'eliminazione non è immediata e richiede risorse di sistema significative. Di conseguenza, è consigliabile solo per gli scenari di conformità.  

