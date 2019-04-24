---
title: Eliminare i dati da Esplora dati di Azure
description: Questo articolo descrive gli scenari di eliminazione in blocco in Esplora dati di Azure, inclusa la ripulitura e l'eliminazione basata sulla conservazione.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9c1b21e119a38c6d306b9c564ab7958ba21a1c41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445669"
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

Se occorre assistenza per problemi con il l'eliminazione di dati, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
