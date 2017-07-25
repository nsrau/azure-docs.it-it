---
title: Livelli di servizio e di prestazioni del database SQL di Azure | Microsoft Docs
description: Confronto dei livelli di servizio e di prestazioni del database SQL per database singoli e presentazione dei pool elastici SQL
keywords: opzioni di database,prestazioni del database
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/30/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 68d55d2dd088ce6350bd65b79206f161f9d3d788
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
<a id="what-performance-options-are-available-for-an-azure-sql-database" class="xliff"></a>

# Opzioni di prestazioni disponibili per un database SQL di Azure

Il [database SQL di Azure](sql-database-technical-overview.md) offre quattro livelli di servizio per i database sia singoli che [in pool](sql-database-elastic-pool.md): **Basic**, **Standard**, **Premium** e **Premium RS**. Ogni livello di servizio include più livelli di prestazioni ([DTU](sql-database-what-is-a-dtu.md)) e opzioni di archiviazione per la gestione di carichi di lavoro e dimensioni dei dati differenti. Livelli di prestazioni più elevati offrono risorse di calcolo e di archiviazione aggiuntive progettate per garantire un aumento della velocità effettiva e della capacità. È possibile cambiare i livelli di servizio e di prestazioni e lo spazio di archiviazione in modo dinamico senza tempi di inattività. 
- I livelli di servizio **Basic**, **Standard** e **Premium** garantiscono un tempo di attività previsto dal contratto di servizio del 99,99%, opzioni di continuità aziendale flessibili, funzionalità di sicurezza e fatturazione su base oraria. 
- Il livello **Premium RS** offre gli stessi livelli di prestazioni di Premium con un contratto di servizio ridotto, perché viene eseguito con un numero inferiore di copie ridondanti rispetto a un database negli altri livelli di servizio. In caso di errore del servizio, quindi, potrebbe essere necessario recuperare il database da un backup con un ritardo massimo di 5 minuti.

> [!IMPORTANT]
> Un database SQL di Azure ottiene un set garantito di risorse e le caratteristiche di prestazioni previste per il database non saranno influenzate da altri database in Azure. 

<a id="choosing-a-service-tier" class="xliff"></a>

## Scelta di un piano di servizio
Nella tabella seguente sono riportati esempi dei livelli ottimali adatti ai carichi di lavoro di diverse applicazioni.

| Livello di servizio | Carichi di lavoro di destinazione |
| :--- | --- |
| **Basic** | Più adatto per un database di piccole dimensioni, che supporta in genere una singola operazione attiva in un determinato momento. Ad esempio, database usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| **Standard** |Opzione per le applicazioni cloud con requisiti di prestazioni I/O bassi o medi, supporto di più query contemporaneamente. Ad esempio, applicazioni web o per gruppi di lavoro. |
| **Premium** | Progettato per volumi di transazioni elevati con alti requisiti di prestazioni I/O che supportano più utenti contemporaneamente. Ad esempio, database che supportano applicazioni mission-critical. |
| **Premium RS** | Progettato per carichi di lavoro con un numero elevato di operazioni di I/O che non richiedono una garanzia di disponibilità massima. Gli esempi includono test di carichi di lavoro ad alte prestazioni e carichi di lavoro di analisi in cui il database non è il sistema di registrazione. |
|||

È possibile creare database singoli con risorse dedicate in un livello di servizio, con un [livello di prestazioni](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) specifico, oppure creare database in un [pool elastico SQL](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus). In un pool elastico SQL, le risorse di calcolo e di archiviazione sono condivise tra più database in un singolo server logico. 

Le risorse disponibili per i database singoli sono espresse in unità di transazione di database (DTU), mentre quelle per i pool elastici SQL sono espresse in unità di transazione di database elastico (eDTU). Per altre informazioni su DTU ed eDTU, vedere [Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)](sql-database-what-is-a-dtu.md).

Per decidere il livello di servizio da usare, stabilire innanzitutto le funzionalità minime necessarie del database:

| **Funzionalità del livello di servizio** | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Dimensioni massime del database singolo | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| Dimensioni massime di un pool elastico | 156 GB | 2,9 TB | 4 TB* | 750 GB |
| Dimensioni massime dei database in un pool elastico | 2 GB | 250 GB | 500 GB | 500 GB |
| Numero massimo di database per pool | 500  | 500 | 100 | 100 |
| DTU massime del database singolo | 5 | 100 | 4000 | 1000 |
| DTU massime per database in un pool elastico | 5 | 3000 | 4000 | 1000 |
| Periodo di conservazione dei backup dei database | 7 giorni | 35 giorni | 35 giorni | 35 giorni |
||||||

> [!IMPORTANT]
> Lo spazio di archiviazione fino a 4 TB è attualmente disponibile nelle aree seguenti: Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia, Europa occidentale, Germania centrale, Asia sud-orientale, Giappone orientale, Australia orientale, Canada centrale e Canada orientale. Vedere le [limitazioni correnti per l'opzione 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

Dopo aver individuato il livello di servizio appropriato, è possibile determinare il livello di prestazioni (ossia il numero di DTU) e lo spazio di archiviazione del database. 

- I livelli di prestazioni S2 e S3 del livello **Standard** sono in molti casi un valido punto di partenza. 
- Per i database con requisiti elevati in termini di CPU o I/O, i livelli di prestazioni del livello **Premium** sono il punto di partenza ottimale. 
- Il livello **Premium** offre più CPU e I/O iniziale 10 volte superiore rispetto al livello di prestazioni più elevato del livello **Standard**.
- Il livello **Premium RS** offre le prestazioni del livello **Premium** a un costo inferiore, ma con un contratto di servizio ridotto.

> [!IMPORTANT]
> Per informazioni sul raggruppamento di database in pool elastici SQL per la condivisione delle risorse di calcolo e di archiviazione, vedere l'argomento relativo ai [pool elastici SQL](sql-database-elastic-pool.md). Il resto di questo argomento è incentrato sui livelli di servizio e di prestazioni per i database singoli.
>

<a id="single-database-service-tiers-and-performance-levels" class="xliff"></a>

## Livelli di servizio e di prestazioni per database singoli
Per i database singoli, all'interno di ogni livello di servizio sono disponibili più livelli di prestazioni e spazi di archiviazione. 

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

<a id="scaling-up-or-scaling-down-a-single-database" class="xliff"></a>

## Ridimensionamento di un singolo database

Dopo aver selezionato inizialmente un livello di servizio e di prestazioni, è possibile ridimensionare un singolo database in modo dinamico in base all'esperienza effettiva.  

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modifica del livello di servizio e/o di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Il tempo impiegato per il passaggio è variabile, ma è in genere inferiore a 4 secondi e nel 99% dei casi è inferiore a 30 secondi. Se quando le connessioni vengono disabilitate è in elaborazione un elevato numero di transazioni, il tempo impiegato potrebbe essere superiore.  

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del database prima e dopo la modifica. Ad esempio, il passaggio di un database di 250 GB al livello di servizio Standard o dal livello di servizio Standard a un altro livello o nell'ambito dello stesso livello di servizio Standard viene completato entro 6 ore. Per un database delle stesse dimensioni in fase di modifica dei livelli di prestazioni all'interno del livello di servizio Premium, il completamento dovrebbe avvenire entro 3 ore.

* Se si esegue l'aggiornamento a un livello di servizio o di prestazioni superiore, le dimensioni massime del database non aumentano a meno che non si specifichino esplicitamente dimensioni massime più elevate.
* Per effettuare il downgrade di un database, le relative dimensioni devono essere inferiori alle dimensioni massime consentite per il livello di servizio di destinazione. 
* Quando si aggiorna un database con [replica geografica](sql-database-geo-replication-portal.md) abilitata, l'indicazione generale è aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario.
* Quando si effettua il downgrade dal livello di servizio **Premium** a uno inferiore, è prima necessario terminare tutte le relazioni di replica geografica. Per arrestare il processo di replica tra il database primario e i database secondari, seguire la procedura descritta nell'argomento relativo al [ripristino dopo un'interruzione del servizio](sql-database-disaster-recovery.md).
* Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. In caso di downgrade al livello **Basic**, il periodo di conservazione dei backup sarà inferiore. Vedere l'articolo relativo ai [backup del database SQL di Azure](sql-database-automated-backups.md).
* Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.


<a id="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize" class="xliff"></a>

## Limitazioni correnti dei database P11 e P15 con dimensioni massime di 4 TB

Come illustrato in precedenza, le dimensioni massime di 4 TB per i database P11 e P15 sono supportate in alcune aree. Ai database P11 e P15 con dimensioni massime di 4 TB si applicano le considerazioni e le limitazioni seguenti:

- Se durante la creazione di un database si sceglie l'opzione da 4 TB (usando un valore di 4 TB o 4096 GB) e si effettua il provisioning del database in un'area non supportata, il comando di creazione avrà esito negativo e restituirà un errore.
- Per i database P11 e P15 che si trovano in una delle aree supportate, è possibile aumentare lo spazio di archiviazione massimo fino a 4 TB. È possibile verificarlo usando [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) o esaminando le dimensioni del database nel portale di Azure. È possibile eseguire l'aggiornamento di un database P11 o P15 esistente solo tramite un accesso entità a livello del server o come membri del ruolo del database dbmanager. 
- Se un'operazione di aggiornamento viene eseguita in un'area supportata, la configurazione viene aggiornata immediatamente. Il database rimane online durante il processo di aggiornamento. Tuttavia, non è possibile usare tutti i 4 TB di memoria fino al completamento dell'aggiornamento dei file del database alle nuove dimensioni massime. Il tempo necessario dipende dalle dimensioni del database in corso di aggiornamento.  
- Durante la creazione o l'aggiornamento di un database P11 o P15, è possibile scegliere solamente 1 TB o 4 TB di dimensioni massime. Gli account di archiviazione intermedi non sono attualmente supportati. Quando si crea un database P11 o P15, l'opzione di archiviazione predefinita di 1 TB è preselezionata. Per i database che si trovano in una delle aree supportate, lo spazio di archiviazione massimo di un database singolo nuovo o esistente può essere aumentato fino a 4 TB. In tutte le altre aree, le dimensioni massime non possono essere aumentate oltre 1 TB. Quando si seleziona l'opzione da 4 TB di spazio di archiviazione incluso, il prezzo non cambia.
- Un database dalle dimensioni massime di 4 TB non potrà essere modificato a 1 TB, anche se la memoria usata non supera quest'ultimo valore. Pertanto, non è possibile eseguire il downgrade da P11 o P15 da 4 TB a P11 o P15 da 1 TB o a un livello di prestazioni minore, ad esempio P1-P6, fino a quando non verranno fornite opzioni di archiviazione aggiuntive per tutti gli altri livelli di prestazioni. Questa restrizione si applica anche agli scenari di ripristino e copia, inclusi gli scenari temporizzati, il ripristino geografico, la conservazione backup a lungo termine e la copia del database. Dopo avere configurato un database con l'opzione da 4 TB, tutte le operazioni di ripristino di tale database devono essere eseguite in un database P11/P15 con dimensioni massime di 4 TB.
- La creazione e l'aggiornamento di un database P11/P15 in un'area non supportata avranno esito negativo, con il seguente messaggio di errore: **P11 and P15 database with up to 4TB of storage are available in US East2, West US, US Gov Virginia, West Europe, Germany Central, South East Asia, Japan East, Australia East, Canada Central, and Canada East.** (I database P11 e P15 con memoria massima di 4 TB sono disponibili nelle aree Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia, Europa occidentale, Germania centrale, Asia sud-orientale, Giappone orientale, Australia orientale, Canada centrale e Canada orientale).
- Per gli scenari di replica geografica attiva:
   - Impostazione di una relazione di replica geografica: se il database primario è P11 o P15, devono esserlo anche i database secondari. I livelli di prestazioni inferiori non vengono accettati come database secondari in quanto non supportano l'opzione da 4 TB.
   - Aggiornamento del database primario in una relazione di replica geografica: portando a 4 TB le dimensioni massime di un database primario, viene attivata la stessa modifica nel database secondario. Entrambi gli aggiornamenti devono avere esito positivo per applicare la modifica al database primario. Si applicano limitazioni per l'opzione da 4 TB (vedere sopra). Se il database secondario si trova in un'area che non supporta l'opzione da 4 TB, il database primario non viene aggiornato.
- Il servizio di importazione/esportazione per caricare i database P11 o P15 da 4 TB non è supportato. Usare SqlPackage.exe per [importare](sql-database-import.md) ed [esportare](sql-database-export.md) i dati.

<a id="manage-single-database-service-tiers-and-performance-levels-using-the-azure-portal" class="xliff"></a>

## Gestire i livelli di servizio e di prestazioni per database singoli con il portale di Azure

Per impostare o modificare il livello di servizio, il livello di prestazioni o lo spazio di archiviazione per un database SQL di Azure nuovo o esistente con il portale di Azure, aprire la finestra **Configura prestazioni** relativa al database facendo clic su **Piano tariffario (piano DTU)**, come illustrato nello screenshot seguente. 

- Impostare o modificare il livello di servizio selezionando quello per il proprio carico di lavoro. 
- Impostare o modificare il livello di prestazioni (**DTU**) all'interno di un livello di servizio usando il dispositivo di scorrimento **DTU**.
- Impostare o modificare lo spazio di archiviazione per il livello di prestazioni usando il dispositivo di scorrimento **Archiviazione**. 

  ![Configurare il livello di servizio e di prestazioni](./media/sql-database-service-tiers/service-tier-performance-level.png)

> [!IMPORTANT]
> Se si seleziona un livello di servizio P11 o P15, vedere [Limitazioni correnti dei database P11 e P15 con dimensioni massime di 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize).
>

<a id="manage-single-database-service-tiers-and-performance-levels-using-powershell" class="xliff"></a>

## Gestire i livelli di servizio e di prestazioni per database singoli con PowerShell

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere l'articolo su come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Cmdlet | Descrizione |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crea un database |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Recupera uno o più database|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Imposta le proprietà di un database oppure sposta un database esistente in un pool elastico|


> [!TIP]
> Per uno script di esempio di PowerShell che monitora le metriche delle prestazioni di un database, lo ridimensiona passando a un livello di prestazioni superiore e crea una regola di avviso per una delle metriche delle prestazioni, vedere [Monitorare e ridimensionare un database SQL singolo usando PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

<a id="manage-single-database-service-tiers-and-performance-levels-using-the-azure-cli" class="xliff"></a>

## Gestire i livelli di servizio e di prestazioni per database singoli con l'interfaccia della riga di comando di Azure

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con l'interfaccia della riga di comando di Azure, usare i [comandi per database SQL](/cli/azure/sql/db) seguenti. Usare [Cloud Shell](/azure/cloud-shell/overview) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows. Per creare e gestire pool elastici SQL, vedere l'articolo relativo ai [pool elastici](sql-database-elastic-pool.md).

| Cmdlet | Descrizione |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |Crea un database|
|[az sql db list](/cli/azure/sql/db#list)|Elenca tutti i database e i data warehouse in un server o tutti i database in un pool elastico|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|Elenca gli obiettivi di servizio e i limiti di archiviazione disponibili|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|Restituisce gli utilizzi del database|
|[az sql db show](/cli/azure/sql/db#show)|Recupera un database o un data warehouse|
|[az sql db update](/cli/azure/sql/db#update)|Aggiorna un database|

> [!TIP]
> Per uno script di esempio dell'interfaccia della riga di comando di Azure che ridimensiona un database SQL di Azure singolo passando a un livello di prestazioni diverso dopo l'esecuzione di query sulle dimensioni del database, vedere l'articolo su come [usare l'interfaccia della riga di comando per monitorare e ridimensionare un database SQL singolo](scripts/sql-database-monitor-and-scale-database-cli.md).
>

<a id="manage-single-database-service-tiers-and-performance-levels-using-transact-sql" class="xliff"></a>

## Gestire i livelli di servizio e di prestazioni per database singoli con Transact-SQL

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con Transact-SQL, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando il portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o qualsiasi altro programma che può connettersi a un server di database SQL di Azure e passare comandi Transact-SQL. 

| Comando | Descrizione |
| --- | --- |
|[CREATE DATABASE (database SQL di Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Crea un nuovo database. Per creare un nuovo database è necessario essere connessi al database master.|
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica un database SQL di Azure. |
|[sys.database_service_objectives (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Restituisce l'edizione (livello di servizio), l'obiettivo di servizio (piano tariffario) e il nome del pool elastico, se presente, di un database SQL di Azure o un'istanza di Azure SQL Data Warehouse. Se si è connessi al database master in un server di database SQL di Azure, restituisce informazioni su tutti i database. Per Azure SQL Data Warehouse, è necessario essere connessi al database master.|
|[sys.database_usage (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Elenca il numero, il tipo e la durata dei database in un server di database SQL di Azure.|

L'esempio seguente illustra la modifica delle dimensioni massime con il comando ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

<a id="manage-single-databases-using-the-rest-api" class="xliff"></a>

## Gestire database singoli con l'API REST

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con l'API REST, vedere [Azure SQL Database REST API](/rest/api/sql/) (API REST per database SQL di Azure).

<a id="next-steps" class="xliff"></a>

## Passaggi successivi

* Altre informazioni sulle [DTU](sql-database-what-is-a-dtu.md).
* Per informazioni sul monitoraggio dell'utilizzo di DTU, vedere l'articolo relativo a [monitoraggio e ottimizzazione delle prestazioni](sql-database-troubleshoot-performance.md).


