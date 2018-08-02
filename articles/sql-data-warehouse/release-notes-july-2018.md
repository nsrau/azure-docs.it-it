---
title: Note sulla versione di Azure SQL Data Warehouse - Giugno 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216709"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Novità di Azure SQL Data Warehouse Luglio 2018
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di luglio 2018.


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Maggiore granularità per i ripristini tra aree e server
Ora è possibile eseguire il ripristino tra aree e server usando qualsiasi punto di ripristino, invece di selezionare i backup con ridondanza geografica che vengono eseguiti ogni 24 ore. I ripristini tra aree e server sono supportati sia per i punti di ripristino automatici che per quelli definiti dall'utente, consentendo una maggiore granularità per una protezione dei dati aggiuntiva. Con la disponibilità di più punti di ripristino, è possibile avere la certezza che il data warehouse sarà logicamente coerente durante il ripristino tra aree.

![Comando di ripristino - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![Opzioni di ripristino - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Per altre informazioni, vedere il post di blog [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Punti di ripristino accelerati e flessibili).

## <a name="20-minute-restorations"></a>Ripristini in 20 minuti
SQL Data Warehouse offre ora il ripristino di qualsiasi data warehouse **in meno di 20 minuti** nella stessa area, indipendentemente dalla dimensione del database. Il tempo di ripristino è applicabile sia in caso di ripristino nello stesso server logico che in un diverso server logico nella stessa area. Inoltre, il processo di snapshot è stato migliorato per ridurre il tempo necessario per creare un punto di ripristino. Ai livelli di prestazioni più bassi (impostazioni DWU inferiori), il miglioramento consente di *dimezzare* il tempo per la creazione degli snapshot.

Per altre informazioni, vedere il post di blog [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Punti di ripristino accelerati e flessibili).

## <a name="custom-restoration-configurations"></a>Configurazioni di ripristino personalizzate
Ora è possibile modificare il livello di prestazioni (DWU) durante il ripristino nel portale di Azure. È anche possibile eseguire il ripristino in un data warehouse di seconda generazione aggiornato. Tramite il ripristino in un'istanza di seconda generazione, è ora possibile valutare l'impatto del sistema di seconda generazione prima dell'[aggiornamento del data warehouse di prima generazione](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Configurazione di ripristino personalizzata - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
La stored procedure [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) viene spesso usata dagli strumenti per ottenere i metadati sui parametri in batch Transact-SQL. La procedura restituisce una riga per ogni parametro nel batch con le informazioni sul tipo dedotto per tale parametro. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Il set di risultati include i metadati sul parametro `@id` (i risultati indicati sono parziali)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```