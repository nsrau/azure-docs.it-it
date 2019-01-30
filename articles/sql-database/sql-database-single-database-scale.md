---
title: Ridimensionare le risorse di database singoli - Database SQL di Azure | Microsoft Docs
description: Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per un database singolo nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: a3a4e2c109541effdac01e0c9c03ee91cfdb30bf
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812251"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Ridimensionare le risorse di database singoli nel database SQL di Azure

Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per un database singolo nel database SQL di Azure.

## <a name="vcore-based-purchasing-model-change-storage-size"></a>Modello di acquisto in base ai vCore: modifica delle dimensioni di archiviazione

- Il provisioning dell'archiviazione può essere effettuato fino al limite massimo delle dimensioni tramite incrementi di 1 GB. Lo spazio di archiviazione dei dati minimo configurabile è 5 GB
- Il provisioning dell'archiviazione per un database singolo può essere effettato aumentandone o diminuendone le dimensioni massime tramite il [portale di Azure](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az-sql-db-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Il database SQL alloca automaticamente il 30% di archiviazione aggiuntiva per i file di log e 32 GB per ogni vCore per TempDB, ma senza superare 384 GB. TempDB è disponibile in un'unità SSD collegata in tutti i livelli di servizio.
- Il prezzo dell'archiviazione per un database singolo corrisponde alla somma delle dimensioni di archiviazione dei dati e delle dimensioni di archiviazione dei log moltiplicata per il prezzo unitario dell'archiviazione del livello di servizio. Il costo di TempDB è compreso nel prezzo dei vCore. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>Modello di acquisto in base ai vCore: modifica delle risorse di calcolo

Dopo avere inizialmente selezionato il numero di vCore, è possibile aumentare o diminuire dinamicamente le dimensioni di un database singolo in base all'effettiva esperienza tramite il [portale di Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az-sql-db-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

La modifica del livello di servizio e/o della dimensione di calcolo crea una replica del database originale alla nuova dimensione di calcolo, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Il tempo impiegato per il passaggio è variabile, ma è in genere inferiore a 30 secondi nel 99% dei casi. Se quando le connessioni vengono disabilitate è in elaborazione un elevato numero di transazioni, il tempo impiegato potrebbe essere superiore.

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del database prima e dopo la modifica. Ad esempio, la modifica di un database di 250 GB al, dal o all'interno del livello di servizio per utilizzo generico deve essere completata in sei ore. Per un database delle stesse dimensioni che cambia dimensioni di calcolo all'interno del livello di servizio business critical, l'aumento delle dimensioni dovrebbe essere completato in tre ore.

> [!TIP]
> Per monitorare le operazioni in corso, vedere: [Gestire le operazioni tramite l'API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Gestire le operazioni tramite l'interfaccia della riga di comando](/cli/azure/sql/db/op), [Monitorare le operazioni tramite T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e i due comandi PowerShell seguenti: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- Se si esegue l'aggiornamento a un livello di servizio o una dimensione di calcolo superiore, le dimensioni massime del database non aumentano a meno che non si specifichino esplicitamente dimensioni più elevate (massime).
- Per effettuare il downgrade di un database, la relativa quantità di spazio usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio e la dimensione di calcolo di destinazione.
- Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, aggiornare i database secondari al livello di servizio e alla dimensione di calcolo desiderati prima di aggiornare il database primario (indicazione generale per ottenere prestazioni ottimali). Durante l'aggiornamento a un'edizione diversa è necessario aggiornare per primo il database secondario.
- Quando si effettua il downgrade di un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, eseguire il downgrade dei database primari al livello di servizio e alla dimensione di calcolo desiderati prima del downgrade del database secondario (indicazione generale per ottenere prestazioni ottimali). Al momento del downgrade a un'edizione diversa, è necessario eseguire questa operazione iniziando dal database primario.
- Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>Modello di acquisto basato su DTU: modifica delle dimensioni di archiviazione

- Il prezzo DTU per un singolo database include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per le quantità di risorse di archiviazione incluse e i limiti di dimensioni massime, vedere [Database singolo: dimensioni di archiviazione e dimensioni di calcolo](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Il provisioning delle risorse di archiviazione extra per un database singolo può essere effettuato aumentandone le dimensioni massime tramite il portale di Azure, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az-sql-db-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Il prezzo delle risorse di archiviazione extra per un singolo database corrisponde alla quantità di risorse di archiviazione extra moltiplicata per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>Modello di acquisto basato su DTU: modifica delle risorse di calcolo (DTU)

Dopo aver inizialmente selezionato un livello di servizio, una dimensione di calcolo e una quantità di spazio di archiviazione, è possibile eseguire l'aumento o la riduzione per un database singolo in modo dinamico in base all'esperienza effettiva tramite il portale di Azure, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az-sql-db-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

Il video seguente mostra come modificare in modo dinamico il livello di servizio e la dimensione di calcolo per aumentare le DTU disponibili per un singolo database.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modifica del livello di servizio e/o della dimensione di calcolo crea una replica del database originale alla nuova dimensione di calcolo, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Il tempo impiegato per il passaggio è variabile, ma è inferiore a 30 secondi nel 99% dei casi. Se quando le connessioni vengono disabilitate è in elaborazione un elevato numero di transazioni, il tempo impiegato potrebbe essere superiore.

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del database prima e dopo la modifica. Ad esempio, il passaggio di un database di 250 GB al livello di servizio Standard o dal livello di servizio Standard a un altro livello o nell'ambito dello stesso livello di servizio Standard viene completato entro 6 ore. Per un database delle stesse dimensioni in fase di modifica delle dimensioni di calcolo all'interno del livello di servizio Premium, il completamento dovrebbe avvenire entro 3 ore.

> [!TIP]
> Per monitorare le operazioni in corso, vedere: [Gestire le operazioni tramite l'API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Gestire le operazioni tramite l'interfaccia della riga di comando](/cli/azure/sql/db/op), [Monitorare le operazioni tramite T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e i due comandi PowerShell seguenti: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- Se si esegue l'aggiornamento a un livello di servizio o una dimensione di calcolo superiore, le dimensioni massime del database non aumentano a meno che non si specifichino esplicitamente dimensioni più elevate (massime).
- Per effettuare il downgrade di un database, la relativa quantità di spazio usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio e la dimensione di calcolo di destinazione.
- Quando si effettua il downgrade dal livello **Premium** al livello **Standard**, viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime del database sono supportate nelle dimensione di calcolo di destinazione e (2) le dimensioni massime superano la quantità inclusa di risorse di archiviazione della dimensione di calcolo di destinazione. Se ad esempio un database P1 con una dimensione massima di 500 GB viene ridotto a S3, viene applicato un costo per le risorse di archiviazione extra, poiché S3 supporta una dimensione massima di 500 GB e la quantità di risorse di archiviazione inclusa è solo di 250 GB. La quantità di risorse di archiviazione extra è quindi 500 GB - 250 GB = 250 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore alla quantità inclusa di risorse di archiviazione, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino alla quantità inclusa.
- Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, aggiornare i database secondari al livello di servizio e alla dimensione di calcolo desiderati prima di aggiornare il database primario (indicazione generale per ottenere prestazioni ottimali). Durante l'aggiornamento a un'edizione diversa è necessario aggiornare per primo il database secondario.
- Quando si effettua il downgrade di un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, eseguire il downgrade dei database primari al livello di servizio e alla dimensione di calcolo desiderati prima del downgrade del database secondario (indicazione generale per ottenere prestazioni ottimali). Al momento del downgrade a un'edizione diversa, è necessario eseguire questa operazione iniziando dal database primario.
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. In caso di downgrade al livello **Basic**, il periodo di conservazione dei backup sarà inferiore. Vedere [Informazioni sui backup automatici del database SQL](sql-database-automated-backups.md).
- Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Modello di acquisto basato su DTU: limitazioni di P11 e P15 quando la dimensione massima è maggiore di 1 TB

Nelle aree seguenti è supportata una dimensione massima maggiore di 1 TB per database P11 e P15: Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Stati Uniti centrali, Francia centrale, Germania centrale, Giappone orientale, Giappone occidentale, Corea del Sud centrale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Regno Unito meridionale, Regno Unito occidentale, Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia ed Europa occidentale. Ai database P11 e P15 con dimensioni massime maggiori di 1 TB vengono applicate le considerazioni e le limitazioni seguenti:

- Se durante la creazione di un database si sceglie una dimensione massima di 1 TB (usando un valore di 4 TB o 4.096 GB) e si effettua il provisioning del database in un'area non supportata, il comando di creazione avrà esito negativo e restituirà un errore.
- Per i database P11 e P15 esistenti che si trovano in una delle aree supportate, è possibile aumentare la quantità massima di risorse di archiviazione oltre 1 TB, in incrementi di 256 GB fino a 4 TB. Per verificare se nella propria area è supportata una dimensione maggiore, usare la funzione [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) o controllare le dimensioni del database nel portale di Azure. È possibile eseguire l'aggiornamento di un database P11 o P15 esistente solo tramite un accesso entità a livello del server o come membri del ruolo del database dbmanager.
- Se un'operazione di aggiornamento viene eseguita in un'area supportata, la configurazione viene aggiornata immediatamente. Il database rimane online durante il processo di aggiornamento. Tuttavia, non è possibile usare la quantità totale di risorse di archiviazione oltre 1 TB fino al completamento dell'aggiornamento dei file del database alle nuove dimensioni massime. Il tempo necessario dipende dalle dimensioni del database in corso di aggiornamento.
- Durante la creazione o l'aggiornamento di un database P11 o P15, è possibile scegliere solamente 1 TB o 4 TB di dimensioni massime in incrementi di 256 GB. Quando si crea un database P11 o P15, l'opzione di archiviazione predefinita di 1 TB è preselezionata. Per i database che si trovano in una delle aree supportate, la quantità di risorse di archiviazione può essere aumentata fino a un massimo di 4 TB per un database singolo nuovo o esistente. In tutte le altre aree, le dimensioni massime non possono essere aumentate oltre 1 TB. Quando si seleziona l'opzione da 4 TB di spazio di archiviazione incluso, il prezzo non cambia.
- Se le dimensioni massime di un database sono maggiori di 1 TB, non possono essere modificate in 1 TB anche se le risorse di archiviazione effettivamente usate sono inferiori a 1 TB. Di conseguenza, non è possibile effettuare il downgrade di un database P11 o P15 con una dimensione massima maggiore di 1 TB a un database P11 da 1 TB o un database P15 da 1 TB o una dimensione di calcolo inferiore, ad esempio P1-P6. Questa restrizione si applica anche agli scenari di ripristino e copia, inclusi gli scenari temporizzati, il ripristino geografico, la conservazione backup a lungo termine e la copia del database. Dopo avere configurato un database con una dimensione massima maggiore di 1 TB, tutte le operazioni di ripristino di tale database devono essere eseguite in un database P11/P15 con dimensioni massime superiori a 1 TB.
- Per gli scenari di replica geografica attiva:
  - Impostazione di una relazione di replica geografica: se il database primario è P11 o P15, devono esserlo anche i database secondari. I livelli di prestazioni inferiori non vengono accettati come database secondari in quanto non supportano l'opzione superiore a 1 TB.
  - Aggiornamento del database primario in una relazione di replica geografica: portando a oltre 1 TB le dimensioni massime di un database primario, viene attivata la stessa modifica nel database secondario. Entrambi gli aggiornamenti devono avere esito positivo per applicare la modifica al database primario. Vengono applicate limitazioni per l'opzione da oltre 1 TB. Se il database secondario si trova in un'area che non supporta l'opzione da oltre 1 TB, il database primario non viene aggiornato.
- Il servizio di importazione/esportazione per caricare i database P11 o P15 con oltre 1 TB non è supportato. Usare SqlPackage.exe per [importare](sql-database-import.md) ed [esportare](sql-database-export.md) i dati.

## <a name="next-steps"></a>Passaggi successivi

Per i limiti generali delle risorse, vedere [Limiti delle risorse basate su vCore del database SQL - database singoli](sql-database-vcore-resource-limits-single-databases.md), [Limiti delle risorse basate su DTU del database SQL - pool elastici](sql-database-dtu-resource-limits-single-databases.md).
