---
title: Eliminare i dati da Esplora dati di Azure
description: Questo articolo descrive gli scenari di eliminazione in Azure Data Explorer, incluse le eliminazioni, gli extent elimini e le eliminazioni basate sulla conservazione.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501424"
---
# <a name="delete-data-from-azure-data-explorer"></a>Eliminare i dati da Esplora dati di Azure

Azure Data Explorer supporta vari scenari di eliminazione descritti in questo articolo. 

## <a name="delete-data-using-the-retention-policy"></a>Eliminare i dati utilizzando i criteri di conservazioneDelete data using the retention policy

Azure Data Explorer elimina automaticamente i dati in base ai criteri di [conservazione.](/azure/kusto/management/retentionpolicy) Questo metodo è il modo più efficiente e senza problemi di eliminazione dei dati. Impostare i criteri di conservazione a livello di database o di tabella.

Si consideri un database o una tabella che viene impostata su 90 giorni di conservazione. Se sono necessari solo 60 giorni di dati, eliminare i dati meno recenti come segue:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Eliminare i dati eliminando le estensioni

[Extent (data shard)](/azure/kusto/management/extents-overview) è la struttura interna in cui vengono archiviati i dati. Ogni estensione può contenere milioni di record. Le estensioni possono essere eliminate singolarmente o come gruppo utilizzando [i comandi di estensioni](/azure/kusto/management/extents-commands#drop-extents)di rilascio. 

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

## <a name="delete-individual-rows-using-purge"></a>Eliminare singole righe utilizzando l'eliminazione

[L'eliminazione dei dati](/azure/kusto/concepts/data-purge) può essere utilizzata per eliminare le righe di singoli utenti. L'eliminazione non è immediata e richiede risorse di sistema significative. Di conseguenza, è consigliato solo per gli scenari di conformità.  

