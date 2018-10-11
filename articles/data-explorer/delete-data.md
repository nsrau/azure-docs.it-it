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
ms.openlocfilehash: 07ee05128333df963c2d8ff3dd3f6442a3843d07
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48866891"
---
# <a name="delete-data-from-azure-data-explorer"></a>Eliminare i dati da Esplora dati di Azure

Esplora dati di Azure supporta diversi approcci di eliminazione in blocco, trattati in questo articolo. Non supporta l'eliminazione per record in tempo reale, perché è ottimizzato per l'accesso in lettura veloce.

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

Se occorre assistenza per problemi con il l'eliminazione di dati, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com).
