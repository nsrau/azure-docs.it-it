---
title: Eliminare i dati da Esplora dati di Azure
description: Questo articolo descrive gli scenari di eliminazione in blocco in Esplora dati di Azure, inclusa la ripulitura e l'eliminazione basata sulla conservazione.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 571a005dd3f50690f291a7ffa3c1174ea15cb0ed
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046284"
---
# <a name="delete-data-from-azure-data-explorer"></a>Eliminare i dati da Esplora dati di Azure

Esplora dati di Azure supporta diversi approcci di eliminazione in blocco, trattati in questo articolo. Non supporta l'eliminazione per record in tempo reale, perché è ottimizzato per l'accesso in lettura veloce.

* Se il database non è più necessario, eliminarlo usando il comando di drop database.

    ```Kusto
    .drop database <DatabaseName>
    ```

* Se una o più tabelle non è più necessaria, eliminarla utilizzando la drop table o il comando drop table.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* Se i dati meno recenti non sono più necessari, eliminarli modificando il periodo di conservazione a livello di database o tabella.

    Si consideri un database o una tabella che viene impostata su 90 giorni di conservazione. È necessario modificare il business, perciò ora sono necessari solo 60 giorni di dati. In questo caso, eliminare i dati meno recenti in uno dei modi seguenti.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    Per altre informazioni, vedere [Criteri di conservazione](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy).

* È possibile eliminare singoli record usando l'operazione *ripulisci*, in base a un predicato come `where CustomerName == 'contoso'`. Ciò premesso, una ripulitura è un'eliminazione in blocco che non è progettata per l'eliminazione in tempo reale. L'esempio seguente mostra una ripulitura.

    ```Kusto
    .purge table Customer records
    | where CustomerName =='contoso'
    ```

Se occorre assistenza per problemi con il l'eliminazione di dati, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com).