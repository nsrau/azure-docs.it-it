---
title: Limiti delle risorse basati su DTU del database SQL di Azure per i singoli database| Microsoft Docs
description: Questa pagina descrive alcuni limiti di risorse comuni basati su DTU per i database singoli nel database SQL di Azure.
services: sql-database
author: sachinpMSFT
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: effb09cfc68961065ad0b4e4be52255bcd1fe4e0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414168"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limiti di risorse per i database singoli usando il modello di acquisto basato su DTU 

Questo articolo illustra in modo dettagliato i limiti delle risorse per i pool elastici del database SQL di Azure usando il modello di acquisto basato su DTU.

Per i limiti di risorse basati su DTU per pool elastici, vedere [Limiti di risorse basati su DTU - pool elastici](sql-database-vcore-resource-limits-elastic-pools.md). Per i limiti di risorse in base ai VCore, vedere [Limiti delle risorse basati su vCore per i database singoli](sql-database-vcore-resource-limits-single-databases.md) e [Limiti delle risorse basati su vCore per pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Database singolo: dimensioni di archiviazione e livelli delle prestazioni

Per i database singoli, le tabelle seguenti illustrano le risorse disponibili per un singolo database a ogni livello di servizio e prestazioni. È possibile impostare il livello di servizio, il livello di prestazioni e la quantità di risorse di archiviazione per un singolo database mediante il [portale di Azure](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), l'[interfaccia della riga di comando di Azure](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases) o l'[API REST](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="basic-service-tier"></a>Livello di servizio Basic
| **Livello di prestazioni** | **Basic** |
| :--- | --: |
| Max DTU | 5 |
| Risorse di archiviazione incluse (GB) | 2 |
| Opzioni per lo spazio di archiviazione massimo (GB) | 2 |
| Spazio di archiviazione OLTP in memoria max (GB) |N/D |
| Numero massimo di ruoli di lavoro simultanei (richieste) | 30 |
| Numero massimo di sessioni simultanee | 300 |
|||

### <a name="standard-service-tier"></a>Livello di servizio Standard
| **Livello di prestazioni** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Max DTU | 10 | 20 | 50 | 100 |
| Risorse di archiviazione incluse (GB) | 250 | 250 | 250 | 250 |
| Opzioni per lo spazio di archiviazione massimo (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Spazio di archiviazione OLTP in memoria max (GB) | N/D | N/D | N/D | N/D |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 60 | 90 | 120 | 200 |
| Numero massimo di sessioni simultanee |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Livello di servizio Standard (continua)
| **Livello di prestazioni** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Max DTU | 200 | 400 | 800 | 1600 | 3000 |
| Risorse di archiviazione incluse (GB) | 250 | 250 | 250 | 250 | 250 |
| Opzioni per lo spazio di archiviazione massimo (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Spazio di archiviazione OLTP in memoria max (GB) | N/D | N/D | N/D | N/D |N/D |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 400 | 800 | 1600 | 3200 |6000 |
| Numero massimo di sessioni simultanee |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Livello di servizio Premium 
| **Livello di prestazioni** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Max DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Risorse di archiviazione incluse (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Opzioni per lo spazio di archiviazione massimo (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Spazio di archiviazione OLTP in memoria max (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Numero massimo di sessioni simultanee | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> Nel livello Premium è attualmente disponibile più di 1 TB di archiviazione in tutte le aree tranne le seguenti: Stati Uniti centro-occidentali, Cina orientale, USDoDCentral, Germania centrale, USDoDEast, US Gov Southwest, Germania nord-orientale, USGovIowa, Cina settentrionale. Nelle altre aree la quantità massima di risorse di archiviazione nel livello Premium è limitata a 1 TB. Vedere [Limitazioni correnti per P11-P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Database singolo: modifica delle dimensioni di archiviazione

- Il prezzo DTU per un singolo database include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per le quantità di risorse di archiviazione incluse e i limiti di dimensioni massime, vedere [Database singolo: dimensioni di archiviazione e livelli delle prestazioni](#single-database-storage-sizes-and-performance-levels).
- Le risorse di archiviazione extra per un singolo database possono essere sottoposte a provisioning aumentandone le dimensioni massime mediante il [portale di Azure](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az_sql_db_update) o l'[API REST](/rest/api/sql/databases/update).
- Il prezzo delle risorse di archiviazione extra per un singolo database corrisponde alla quantità di risorse di archiviazione extra moltiplicata per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Database singolo: modifica delle DTU

Dopo aver inizialmente selezionato un livello di servizio, un livello di prestazioni e una quantità di risorse di archiviazione, sarà possibile eseguire l'aumento o la riduzione per un singolo database in modo dinamico in base all'esperienza effettiva mediante il [portale di Azure](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az_sql_db_update) o l'[API REST](/rest/api/sql/databases/update). 

Il video seguente mostra in modo dinamico la modifica del livello di prestazioni per aumentare le DTU disponibili per un singolo database.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modifica del livello di servizio e/o di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Il tempo impiegato per il passaggio è variabile, ma è inferiore a 30 secondi nel 99% dei casi. Se quando le connessioni vengono disabilitate è in elaborazione un elevato numero di transazioni, il tempo impiegato potrebbe essere superiore. 

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del database prima e dopo la modifica. Ad esempio, il passaggio di un database di 250 GB al livello di servizio Standard o dal livello di servizio Standard a un altro livello o nell'ambito dello stesso livello di servizio Standard viene completato entro 6 ore. Per un database delle stesse dimensioni in fase di modifica dei livelli di prestazioni all'interno del livello di servizio Premium, il completamento dovrebbe avvenire entro 3 ore.

> [!TIP]
> Per monitorare le operazioni in corso, vedere: [Gestire le operazioni tramite l'API REST SQL](/rest/api/sql/Operations/List), [Gestire le operazioni tramite l'interfaccia della riga di comando](/cli/azure/sql/db/op), [Monitorare le operazioni tramite T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e i due comandi PowerShell seguenti: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Se si esegue l'aggiornamento a un livello di servizio o di prestazioni superiore, le dimensioni massime del database non aumentano a meno che non si specifichino esplicitamente dimensioni più elevate (massime).
* Per effettuare il downgrade di un database, la relativa quantità di spazio usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio di destinazione e il livello di prestazioni. 
* Quando si effettua il downgrade dal livello **Premium** al livello **Standard**, viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime del database sono supportate nel livello di prestazioni di destinazione e (2) le dimensioni massime superano la quantità inclusa di risorse di archiviazione del livello di prestazioni di destinazione. Se ad esempio un database P1 con una dimensione massima di 500 GB viene ridotto a S3, viene applicato un costo per le risorse di archiviazione extra, poiché S3 supporta una dimensione massima di 500 GB e la quantità di risorse di archiviazione inclusa è solo di 250 GB. La quantità di risorse di archiviazione extra è quindi 500 GB - 250 GB = 250 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore alla quantità inclusa di risorse di archiviazione, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino alla quantità inclusa. 
* Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario (indicazione generale per ottenere prestazioni ottimali). Durante l'aggiornamento a un'edizione diversa è necessario aggiornare per primo il database secondario.
* Quando si effettua il downgrade di un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, eseguire il downgrade dei database primari al livello di prestazioni desiderato prima del downgrade del database secondario (indicazione generale per ottenere prestazioni ottimali). Al momento del downgrade a un'edizione diversa, è necessario eseguire questa operazione iniziando dal database primario.
* Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. In caso di downgrade al livello **Basic**, il periodo di conservazione dei backup sarà inferiore. Vedere la sezione relativa ai [backup del database SQL di Azure](sql-database-automated-backups.md).
* Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Database singolo: limitazioni di P11 e P15 quando la dimensione massima è maggiore di 1 TB

Nelle aree seguenti è supportata una dimensione massima maggiore di 1 TB per database P11 e P15: Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Stati Uniti centrali, Francia centrale, Germania centrale, Giappone orientale, Giappone occidentale, Corea del Sud centrale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Regno Unito meridionale, Regno Unito occidentale, Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia ed Europa occidentale. Ai database P11 e P15 con dimensioni massime maggiori di 1 TB vengono applicate le considerazioni e le limitazioni seguenti:

- Se durante la creazione di un database si sceglie una dimensione massima di 1 TB (usando un valore di 4 TB o 4.096 GB) e si effettua il provisioning del database in un'area non supportata, il comando di creazione avrà esito negativo e restituirà un errore.
- Per i database P11 e P15 esistenti che si trovano in una delle aree supportate, è possibile aumentare la quantità massima di risorse di archiviazione oltre 1 TB, in incrementi di 256 GB fino a 4 TB. Per verificare se nella propria area è supportata una dimensione maggiore, usare la funzione [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) o controllare le dimensioni del database nel portale di Azure. È possibile eseguire l'aggiornamento di un database P11 o P15 esistente solo tramite un accesso entità a livello del server o come membri del ruolo del database dbmanager. 
- Se un'operazione di aggiornamento viene eseguita in un'area supportata, la configurazione viene aggiornata immediatamente. Il database rimane online durante il processo di aggiornamento. Tuttavia, non è possibile usare la quantità totale di risorse di archiviazione oltre 1 TB fino al completamento dell'aggiornamento dei file del database alle nuove dimensioni massime. Il tempo necessario dipende dalle dimensioni del database in corso di aggiornamento. 
- Durante la creazione o l'aggiornamento di un database P11 o P15, è possibile scegliere solamente 1 TB o 4 TB di dimensioni massime in incrementi di 256 GB. Quando si crea un database P11 o P15, l'opzione di archiviazione predefinita di 1 TB è preselezionata. Per i database che si trovano in una delle aree supportate, la quantità di risorse di archiviazione può essere aumentata fino a un massimo di 4 TB per un database singolo nuovo o esistente. In tutte le altre aree, le dimensioni massime non possono essere aumentate oltre 1 TB. Quando si seleziona l'opzione da 4 TB di spazio di archiviazione incluso, il prezzo non cambia.
- Se le dimensioni massime di un database sono maggiori di 1 TB, non possono essere modificate in 1 TB anche se le risorse di archiviazione effettivamente usate sono inferiori a 1 TB. Di conseguenza, non è possibile effettuare il downgrade di un database P11 o P15 con una dimensione massima maggiore di 1 TB a 1 TB per database P11 o 1 TB per database P15 o un livello di prestazioni inferiore, ad esempio P1-P6. Questa restrizione si applica anche agli scenari di ripristino e copia, inclusi gli scenari temporizzati, il ripristino geografico, la conservazione backup a lungo termine e la copia del database. Dopo avere configurato un database con una dimensione massima maggiore di 1 TB, tutte le operazioni di ripristino di tale database devono essere eseguite in un database P11/P15 con dimensioni massime superiori a 1 TB.
- Per gli scenari di replica geografica attiva:
   - Impostazione di una relazione di replica geografica: se il database primario è P11 o P15, devono esserlo anche i database secondari. I livelli di prestazioni inferiori non vengono accettati come database secondari in quanto non supportano l'opzione superiore a 1 TB.
   - Aggiornamento del database primario in una relazione di replica geografica: portando a oltre 1 TB le dimensioni massime di un database primario, viene attivata la stessa modifica nel database secondario. Entrambi gli aggiornamenti devono avere esito positivo per applicare la modifica al database primario. Vengono applicate limitazioni per l'opzione da oltre 1 TB. Se il database secondario si trova in un'area che non supporta l'opzione da oltre 1 TB, il database primario non viene aggiornato.
- Il servizio di importazione/esportazione per caricare i database P11 o P15 con oltre 1 TB non è supportato. Usare SqlPackage.exe per [importare](sql-database-import.md) ed [esportare](sql-database-export.md) i dati.

## <a name="next-steps"></a>Passaggi successivi

- Per le risposte alle domande più frequenti, vedere [Domande frequenti sul database SQL](sql-database-faq.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
- Per informazioni su DTU ed eDTU, vedere [DTU ed eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Per informazioni sui limiti di dimensioni di tempdb, vedere https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
