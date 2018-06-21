---
title: Limiti di risorse in base ai vCore per il database SQL di Azure | Microsoft Docs
description: Questa pagina descrive alcuni limiti di risorse comuni in base ai vCore per il database SQL di Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: carlrab
ms.openlocfilehash: 324f9f35de37c717d57e46413cd881dce785f4fd
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737664"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Limiti del modello di acquisto in base ai vCore per il database SQL di Azure (anteprima)

> [!IMPORTANT]
> Per informazioni sui limiti del modello di acquisto in base alle DTU, vedere [SQL Database DTU-based resource limits](sql-database-dtu-resource-limits.md) (Limiti di risorse in base alle DTU per il database SQL).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Database singolo: dimensioni di archiviazione e livelli delle prestazioni

Per i database singoli, le tabelle seguenti illustrano le risorse disponibili per un singolo database a ogni livello di servizio e prestazioni. È possibile impostare il livello di servizio, il livello di prestazioni e la quantità di risorse di archiviazione per un singolo database mediante il [portale di Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), l'[interfaccia della riga di comando di Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) o l'[API REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

### <a name="general-purpose-service-tier"></a>Livello di servizio Utilizzo generico

#### <a name="generation-4-compute-platform"></a>Piattaforma di calcolo Generazione 4
|Livello di prestazioni|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Dimensioni massime dei dati (GB)|1024|1024|1536|3072|4096|4096|
|Dimensioni massime dei log|307|307|461|922|1229|1229|
|Dimensioni di TempDB (database)|32|64|128|256|384|384|
|Target IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|7000
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

#### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5
|Livello di prestazioni|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|11|22|44|88|132|176|220|440|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Dimensioni massime dei dati (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Dimensioni massime dei log|307|307|461|614|1229|1229|1229|1229|
|Dimensioni di TempDB (database)|64|128|256|384|384|384|384|384|
|Target IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|1|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

### <a name="business-critical-service-tier"></a>Livello di servizio business critical

#### <a name="generation-4-compute-platform"></a>Piattaforma di calcolo Generazione 4
|Livello di prestazioni|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|1|2|4|8|20|36|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime dei log|307|307|307|307|307|307|
|Dimensioni di TempDB (database)|32|64|128|256|384|384|
|Target IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|3200|4800|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Numero di repliche|3|3|3|3|3|3|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

#### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5
|Livello di prestazioni|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|11|22|44|88|132|176|220|440|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|1.571|3.142|6.284|15.768|25.252|37.936|52.22|131.64|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Dimensioni massime dei log|307|307|307|307|614|1229|1229|1229|
|Dimensioni di TempDB (database)|64|128|256|384|384|384|384|384|
|Target IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Numero massimo di ruoli di lavoro simultanei (richieste)|200|400|800|1600|2400|3200|4000|8000|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Numero di repliche|3|3|3|3|3|3|3|3|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

## <a name="single-database-change-storage-size"></a>Database singolo: modifica delle dimensioni di archiviazione

- Il provisioning dell'archiviazione può essere effettuato fino al limite massimo delle dimensioni tramite incrementi di 1 GB. L'archiviazione dei dati minima configurabile è 5 GB 
- Il provisioning dell'archiviazione per un database singolo può essere effettato aumentandone o diminuendone le dimensioni massime tramite il [portale di Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az_sql_db_update) o l'[API REST](/rest/api/sql/databases/update).
- Il database SQL alloca automaticamente il 30% di archiviazione aggiuntiva per i file di log e 32 GB per ogni vCore per TempDB, ma senza superare 384 GB. TempDB è disponibile in un'unità SSD collegata in tutti i livelli di servizio.
- Il prezzo dell'archiviazione per un database singolo corrisponde alla somma delle dimensioni di archiviazione dei dati e delle dimensioni di archiviazione dei log moltiplicata per il prezzo unitario dell'archiviazione del livello di servizio. Il costo di TempDB è compreso nel prezzo dei vCore. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-vcores"></a>Database singolo: modifica dei vCore

Dopo avere inizialmente selezionato il numero di vCore, è possibile aumentare o diminuire dinamicamente le dimensioni di un database singolo in base all'effettiva esperienza tramite il [portale di Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az_sql_db_update) o l'[API REST](/rest/api/sql/databases/update). 

La modifica del livello di servizio e/o di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Il tempo impiegato per il passaggio è variabile, ma è in genere inferiore a 4 secondi e nel 99% dei casi è inferiore a 30 secondi. Se quando le connessioni vengono disabilitate è in elaborazione un elevato numero di transazioni, il tempo impiegato potrebbe essere superiore. 

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del database prima e dopo la modifica. Ad esempio, la modifica di un database di 250 GB al, dal o all'interno del livello di servizio per utilizzo generico deve essere completata in sei ore. Per un database delle stesse dimensioni che cambia livelli delle prestazioni all'interno del livello di servizio business critical, l'aumento delle dimensioni deve essere completato in tre ore.

> [!TIP]
> Per monitorare le operazioni in corso, vedere: [Gestire le operazioni tramite l'API REST SQL](/rest/api/sql/Operations/List), [Gestire le operazioni tramite l'interfaccia della riga di comando](/cli/azure/sql/db/op), [Monitorare le operazioni tramite T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e i due comandi PowerShell seguenti: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Se si esegue l'aggiornamento a un livello di servizio o di prestazioni superiore, le dimensioni massime del database non aumentano a meno che non si specifichino esplicitamente dimensioni più elevate (massime).
* Per effettuare il downgrade di un database, la relativa quantità di spazio usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio di destinazione e il livello di prestazioni. 
* Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario (indicazione generale per ottenere prestazioni ottimali). Durante l'aggiornamento a un'edizione diversa è necessario aggiornare per primo il database secondario.
* Quando si effettua il downgrade di un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, eseguire il downgrade dei database primari al livello di prestazioni desiderato prima del downgrade del database secondario (indicazione generale per ottenere prestazioni ottimali). Al momento del downgrade a un'edizione diversa, è necessario eseguire questa operazione iniziando dal database primario.
* Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pool elastico: dimensioni di archiviazione e livelli delle prestazioni

Per i pool elastici del database SQL le tabelle seguenti illustrano le risorse disponibili a ogni livello di servizio e prestazioni. È possibile impostare il livello di servizio, il livello di prestazioni e la quantità di risorse di archiviazione mediante il [portale di Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), l'[interfaccia della riga di comando di Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) o l'[API REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> I limiti di risorse di singoli database nei pool elastici sono in genere gli stessi di quelli per i database singoli al di fuori dei pool che hanno lo stesso livello di prestazioni. Ad esempio, il numero massimo di ruoli di lavoro simultanei per un database GP_Gen4_1 è 200. Di conseguenza, anche il numero massimo di ruoli di lavoro per un database in un pool GP_Gen4_1 è 200. Si noti che il numero totale di ruoli di lavoro simultanei in un pool GP_Gen4_1 è 210.

### <a name="general-purpose-service-tier"></a>Livello di servizio Utilizzo generico

#### <a name="generation-4-compute-platform"></a>Piattaforma di calcolo Generazione 4
|Livello di prestazioni|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Dimensioni massime dei dati (GB)|512|756|1536|2048|3584|4096|
|Dimensioni massime dei log|154|227|461|614|1075|1229|
|Dimensioni di TempDB (database)|32|64|128|256|384|384|
|Target IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|210|420|840|1680|3360|5040|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Massima densità dei pool|100|200|500|500|500|500|
|Numero minimo/massimo di arresti del pool elastico|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Numero di repliche|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

#### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5
|Livello di prestazioni|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|11|22|44|88|132|176|220|440|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo di archiviazione|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|Archiviazione (remota) Premium|
|Dimensioni massime dei dati (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Dimensioni massime dei log|154|227|461|614|922|1229|1229|1229|
|Dimensioni di TempDB (database)|64|128|256|384|384|384|384|384|
|Target IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Latenza di I/O (approssimativa)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|5-7 ms (scrittura)<br>5-10 ms (lettura)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|210|420|840|1680|2520|3360|4200|8400
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Massima densità dei pool|100|200|500|500|500|500|500|500|
|Numero minimo/massimo di arresti del pool elastico|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Numero di repliche|1|1|1|1|1|1|1|1|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

### <a name="business-critical-service-tier"></a>Livello di servizio business critical

#### <a name="generation-4-compute-platform"></a>Piattaforma di calcolo Generazione 4
|Livello di prestazioni|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generazione hardware|4|4|4|4|4|4|
|vCore|1|2|4|8|16|24|
|Memoria (GB)|7|14|28|56|112|168|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|1|2|4|8|20|36|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|1024|1024|
|Dimensioni massime dei log|307|307|307|307|307|307|
|Dimensioni di TempDB (database)|32|64|128|256|384|384|
|Target IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Numero massimo di ruoli di lavoro simultanei (richieste)|210|420|840|1680|3360|5040|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|
|Massima densità dei pool|N/D|50|100|100|100|100|
|Numero minimo/massimo di arresti del pool elastico|N/D|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Numero di repliche|3|3|3|3|3|3|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

#### <a name="generation-5-compute-platform"></a>Piattaforma di calcolo Generazione 5
|Livello di prestazioni|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generazione hardware|5|5|5|5|5|5|5|5|
|vCore|2|4|8|16|24|32|40|80|
|Memoria (GB)|11|22|44|88|132|176|220|440|
|Supporto per columnstore|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archiviazione OLTP in memoria (GB)|1.571|3.142|6.284|15.768|25.252|37.936|52.22|131.64|
|Tipo di archiviazione|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|SSD locale|
|Latenza di I/O (approssimativa)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|1-2 ms (scrittura)<br>1-2 ms (lettura)|
|Dimensioni massime dei dati (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Dimensioni massime dei log|307|307|307|307|614|1229|1229|1229|
|Dimensioni di TempDB (database)|64|128|256|384|384|384|384|384|
|Target IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Numero massimo di ruoli di lavoro simultanei (richieste)|210|420|840|1680|2520|3360|5040|8400|
|Numero massimo di sessioni consentite|30000|30000|30000|30000|30000|30000|30000|30000|
|Massima densità dei pool|N/D|50|100|100|100|100|100|100|
|Numero minimo/massimo di arresti del pool elastico|N/D|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Numero di repliche|3|3|3|3|3|3|3|3|
|Zone di disponibilità multiple|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Scalabilità orizzontale in lettura|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Archivio di backup incluso|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|Dimensioni del database 1X|
|||

Se tutti i vCore in un pool elastico sono occupati, ogni database nel pool riceve una quantità identica di risorse di calcolo per elaborare le query. Il servizio di database SQL suddivide equamente le risorse fra i database con intervalli equivalenti per i tempi di calcolo. L'equa condivisione delle risorse del pool elastico avviene in aggiunta a qualsiasi quantità di risorse altrimenti garantita per ogni database quando il numero minimo di vCore per database è impostato su un valore diverso da zero.

### <a name="database-properties-for-pooled-databases"></a>Proprietà del database per i database in pool

La tabella seguente descrive le proprietà per i database in pool.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Numero massimo di vCore per database |Numero massimo di vCore che qualsiasi database nel pool può usare, se disponibile in base all'utilizzo da parte di altri database nel pool. Il numero massimo di vCore per database non è una garanzia di risorse per un database. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Impostare il numero massimo di vCore per database su un valore sufficientemente elevato per gestire i picchi di utilizzo dei database. È previsto un certo grado di overcommit perché il pool in genere presuppone modelli di utilizzo dei database a freddo e a caldo in cui i database non raggiungono il picco contemporaneamente.|
| Numero minimo di vCore per database |Numero minimo di vCore garantito per ogni database nel pool. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Il numero minimo di vCore per database può essere impostato su 0, che è anche il valore predefinito. Questa proprietà è impostata su qualsiasi valore compreso tra 0 e l'utilizzo medio di vCore per ogni database. Il prodotto tra il numero di database nel pool e il numero minimo di vCore per database non può superare il numero di vCore per pool.|
| Quantità massima di risorse di archiviazione per database |Dimensioni massime del database impostate dall'utente per un database in un pool. I database in pool condividono l'archiviazione allocata del pool e di conseguenza le dimensioni che un database può raggiungere sono limitate al valore più basso tra le restanti dimensioni del database e quelle di archiviazione del pool. Le dimensioni massime del database indicano le dimensioni massime dei file di dati e non includono lo spazio usato dai file di log. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Pool elastico: modifica delle dimensioni di archiviazione

- Il provisioning dell'archiviazione può essere effettuato fino al limite massimo di dimensioni: 
 - Per l'archiviazione Standard, aumentare o diminuire le dimensioni in incrementi di 10 GB
 - Per l'archiviazione Premium, aumentare o diminuire le dimensioni in incrementi di 250 GB
- Il provisioning dell'archiviazione per un pool elastico può essere effettuato aumentandone o diminuendone le dimensioni massime tramite il [portale di Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o l'[API REST](/rest/api/sql/elasticpools/update).
- Il prezzo dell'archiviazione per un pool elastico corrisponde alle dimensioni di archiviazione moltiplicate per il prezzo unitario dell'archiviazione del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-vcores"></a>Pool elastico: modifica dei vCore

È possibile aumentare o ridurre il livello di prestazioni per un pool elastico in base alle risorse necessarie tramite il [portale di Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o l'[API REST](/rest/api/sql/elasticpools/update).

- Durante il ridimensionamento dei vCore del pool, le connessioni di database vengono brevemente interrotte. Si tratta dello stesso comportamento che si verifica durante il ridimensionamento delle DTU per un singolo database (non in un pool). Per informazioni dettagliate sulla durata e sull'impatto delle connessioni interrotte per un database durante le operazioni di ridimensionamento, vedere la sezione relativa al [ridimensionamento delle DTU per un singolo database](#single-database-change-storage-size). 
- La durata per ridimensionare i vCore del pool può dipendere dalle dimensioni totali di archiviazione usate da tutti i database nel pool. In generale, la latenza media di ridimensionamento è di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale usato da tutti i database nel pool è pari a 200 GB, la latenza prevista per il ridimensionamento del pool è di 3 ore o meno. In alcuni casi, all'interno del livello Standard o Basic, la latenza di ridimensionamento può essere inferiore a cinque minuti, indipendentemente dalla quantità di spazio usata.
- In generale, la durata per modificare il numero minimo di vCore per database o il numero massimo di vCore per database è cinque minuti o meno.
- Quando si riducono le dimensioni dei vCore del pool, lo spazio utilizzato del pool deve essere inferiore alle dimensioni massime consentite del livello di servizio di destinazione e dei vCore del pool.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Che cos'è il numero massimo di server e database?

| Massima | Valore |
| :--- | :--- |
| Database per server | 5000 |
| Numero di server per sottoscrizione per area | 20 |
|||

> [!IMPORTANT]
> Poiché il numero di database si avvicina al limite per ogni server, può verificarsi quanto segue:
> <br> •    Latenza in aumento nelle query in esecuzione nel database master.  Ciò include le visualizzazioni delle statistiche di utilizzo delle risorse, ad esempio sys.resource_stats.
> <br> •    Latenza in aumento nelle operazioni di gestione e nel portale di esecuzione del rendering dei punti di visualizzazione che coinvolgono l'enumerazione dei database nel server.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Cosa accade quando vengono raggiunti i limiti delle risorse del pool elastico e del database?

### <a name="compute-vcores"></a>Calcolo (vCore)

Quando l'utilizzo delle risorse di calcolo del database (misurate in base all'utilizzo dei vCore) diventa elevato, la latenza delle query si riduce e può essere addirittura raggiunto il timeout. In queste condizioni le query possono essere messe in coda dal servizio e vengono rese disponibili alcune risorse per l'esecuzione.
In caso di uso elevato di risorse di elaborazione, le opzioni di mitigazione includono:

- Aumento del livello di prestazioni del database o del pool elastico per garantire un numero maggiore di vCore al database. Vedere [Database singolo: modifica dei vCore](#single-database-change-vcores) e [Pool elastico: modifica dei vCore](#elastic-pool-change-vcores).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Archiviazione

Quando la quantità di spazio del database usato raggiunge il limite di dimensioni massime, gli inserimenti e gli aggiornamenti del database che aumentano la dimensione dei dati hanno esito negativo e i clienti ricevono un [messaggio di errore](sql-database-develop-error-messages.md). Le operazioni SELECT e DELETE del database continuano ad avere esito positivo.

In caso di uso elevato di spazio, le opzioni di mitigazione includono:

- Aumento delle dimensioni massime del database o del pool elastico oppure modifica del livello di prestazioni per aumentare le dimensioni di archiviazione massima. Vedere [Limiti di risorse in base ai vCore per il database SQL](sql-database-vcore-resource-limits.md).
- Se il database è in un pool elastico, in alternativa può essere spostato all'esterno del pool in modo che lo spazio di archiviazione non venga condiviso con altri database.

### <a name="sessions-and-workers-requests"></a>Sessioni e ruoli di lavoro (richieste) 

Il numero massimo di sessioni e ruoli di lavoro simultanei è determinato dal livello di servizio e dal livello di prestazioni. Le nuove richieste vengono rifiutate quando vengono raggiunti i limiti delle sessioni o dei ruoli di lavoro e i clienti ricevono un messaggio di errore. Mentre il numero di connessioni disponibili può essere controllato dall'applicazione, il numero di ruoli di lavoro simultanei è spesso più difficile da stimare e da controllare. Ciò vale soprattutto durante i picchi di periodi di carico quando vengono raggiunti i limiti di risorse del database e i ruoli di lavoro si accumulano per via di query a esecuzione prolungata. 

In caso di uso elevato di sessioni o ruoli di lavoro, le opzioni di mitigazione includono:
- Aumento del livello di servizio o prestazioni del database o del pool elastico. Vedere [Database singolo: modifica delle dimensioni di archiviazione](#single-database-change-storage-size), [Database singolo: modifica dei vCore](#single-database-change-vcores), [Pool elastico: modifica delle dimensioni di archiviazione](#elastic-pool-change-storage-size) e [Pool elastico: modifica dei vCore](#elastic-pool-change-vcores).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query, se l'aumento dell'uso di ruoli di lavoro è dovuto a un conflitto delle risorse di elaborazione. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Passaggi successivi

- Per le risposte alle domande più frequenti, vedere [Domande frequenti sul database SQL](sql-database-faq.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
