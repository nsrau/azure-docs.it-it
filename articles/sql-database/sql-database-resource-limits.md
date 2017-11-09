---
title: Limiti delle risorse di Database SQL Azure | Microsoft Docs
description: In questa pagina vengono descritti alcuni limiti di risorse comuni per il Database SQL Azure.
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 2e0acc3cc09de4293dcc049c37bee6b899e6101a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-resource-limits"></a>Limiti delle risorse del database SQL di Azure

## <a name="single-database-storage-sizes-and-performance-levels"></a>Database singolo: dimensioni di archiviazione e livelli delle prestazioni

Per i database singoli, le tabelle seguenti illustrano le risorse disponibili per un singolo database a ogni livello di servizio e prestazioni. È possibile impostare il livello di servizio, il livello di prestazioni e la quantità di risorse di archiviazione per un singolo database mediante il [portale di Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), l'[interfaccia della riga di comando di Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) o l'[API REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>Database singolo: modifica delle dimensioni di archiviazione

- Il prezzo DTU per un singolo database include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per le quantità di risorse di archiviazione incluse e i limiti di dimensioni massime, vedere [Database singolo: dimensioni di archiviazione e livelli delle prestazioni](#single-database-storage-sizes-and-performance-levels).
- Le risorse di archiviazione extra per un singolo database possono essere sottoposte a provisioning aumentandone le dimensioni massime mediante il [portale di Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az_sql_db_update) o l'[API REST](/rest/api/sql/databases/update).
- Il prezzo delle risorse di archiviazione extra per un singolo database corrisponde alla quantità di risorse di archiviazione extra moltiplicata per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Database singolo: modifica delle DTU

Dopo aver inizialmente selezionato un livello di servizio, un livello di prestazioni e una quantità di risorse di archiviazione, sarà possibile eseguire l'aumento o la riduzione per un singolo database in modo dinamico in base all'esperienza effettiva mediante il [portale di Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az_sql_db_update) o l'[API REST](/rest/api/sql/databases/update). 

Il video seguente mostra in modo dinamico la modifica del livello di prestazioni per aumentare le DTU disponibili per un singolo database.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modifica del livello di servizio e/o di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Il tempo impiegato per il passaggio è variabile, ma è in genere inferiore a 4 secondi e nel 99% dei casi è inferiore a 30 secondi. Se quando le connessioni vengono disabilitate è in elaborazione un elevato numero di transazioni, il tempo impiegato potrebbe essere superiore. 

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del database prima e dopo la modifica. Ad esempio, il passaggio di un database di 250 GB al livello di servizio Standard o dal livello di servizio Standard a un altro livello o nell'ambito dello stesso livello di servizio Standard viene completato entro 6 ore. Per un database delle stesse dimensioni in fase di modifica dei livelli di prestazioni all'interno del livello di servizio Premium, il completamento dovrebbe avvenire entro 3 ore.

> [!TIP]
> Per controllare lo stato di un'operazione di ridimensionamento di un database SQL in corso, è possibile usare la query seguente: ```select * from sys.dm_operation_status```.
>

* Se si esegue l'aggiornamento a un livello di servizio o di prestazioni superiore, le dimensioni massime del database non aumentano a meno che non si specifichino esplicitamente dimensioni più elevate (massime).
* Per effettuare il downgrade di un database, la relativa quantità di spazio usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio di destinazione e il livello di prestazioni. 
* Quando si effettua il downgrade dal livello **Premium** o **Premium RS** al livello **Standard**, viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime del database sono supportate nel livello di prestazioni di destinazione e (2) le dimensioni massime superano la quantità inclusa di risorse di archiviazione del livello di prestazioni di destinazione. Se ad esempio un database P1 con una dimensione massima di 500 GB viene ridotto a S3, viene applicato un costo per le risorse di archiviazione extra, poiché S3 supporta una dimensione massima di 500 GB e la quantità di risorse di archiviazione inclusa è solo di 250 GB. La quantità di risorse di archiviazione extra è quindi 500 GB - 250 GB = 250 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore alla quantità inclusa di risorse di archiviazione, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino alla quantità inclusa. 
* Quando si aggiorna un database con [replica geografica](sql-database-geo-replication-portal.md) abilitata, l'indicazione generale è aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario. Durante l'aggiornamento a un'edizione diversa è necessario aggiornare per primo il database secondario.
* Quando si effettua il downgrade di un database con [replica geografica](sql-database-geo-replication-portal.md) abilitata, l'indicazione generale è di eseguire il downgrade dei database primari al livello di prestazioni desiderato prima di eseguire questa operazione per il database secondario. Al momento del downgrade a un'edizione diversa, è necessario eseguire questa operazione iniziando dal database primario.
* Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. In caso di downgrade al livello **Basic**, il periodo di conservazione dei backup sarà inferiore. Vedere la sezione relativa ai [backup del database SQL di Azure](sql-database-automated-backups.md).
* Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Database singolo: limitazioni di P11 e P15 quando la dimensione massima è maggiore di 1 TB

Nelle aree seguenti è supportata una dimensione massima maggiore di 1 TB per database P11 e P15: Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia, Europa occidentale, Germania centrale, Asia sud-orientale, Giappone orientale, Australia orientale, Canada centrale e Canada orientale. Ai database P11 e P15 con dimensioni massime maggiori di 1 TB vengono applicate le considerazioni e le limitazioni seguenti:

- Se durante la creazione di un database si sceglie una dimensione massima di 1 TB (usando un valore di 4 TB o 4.096 GB) e si effettua il provisioning del database in un'area non supportata, il comando di creazione avrà esito negativo e restituirà un errore.
- Per i database P11 e P15 esistenti che si trovano in una delle aree supportate, è possibile aumentare la quantità massima di risorse di archiviazione oltre 1 TB, in incrementi di 256 GB fino a 4 TB. Per verificare se nella propria area è supportata una dimensione maggiore, usare la funzione [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) o controllare le dimensioni del database nel portale di Azure. È possibile eseguire l'aggiornamento di un database P11 o P15 esistente solo tramite un accesso entità a livello del server o come membri del ruolo del database dbmanager. 
- Se un'operazione di aggiornamento viene eseguita in un'area supportata, la configurazione viene aggiornata immediatamente. Il database rimane online durante il processo di aggiornamento. Tuttavia, non è possibile usare la quantità totale di risorse di archiviazione oltre 1 TB fino al completamento dell'aggiornamento dei file del database alle nuove dimensioni massime. Il tempo necessario dipende dalle dimensioni del database in corso di aggiornamento. 
- Durante la creazione o l'aggiornamento di un database P11 o P15, è possibile scegliere solamente 1 TB o 4 TB di dimensioni massime in incrementi di 256 GB. Quando si crea un database P11 o P15, l'opzione di archiviazione predefinita di 1 TB è preselezionata. Per i database che si trovano in una delle aree supportate, la quantità di risorse di archiviazione può essere aumentata fino a un massimo di 4 TB per un database singolo nuovo o esistente. In tutte le altre aree, le dimensioni massime non possono essere aumentate oltre 1 TB. Quando si seleziona l'opzione da 4 TB di spazio di archiviazione incluso, il prezzo non cambia.
- Se le dimensioni massime di un database sono maggiori di 1 TB, non possono essere modificate in 1 TB anche se le risorse di archiviazione effettivamente usate sono inferiori a 1 TB. Di conseguenza, non è possibile effettuare il downgrade di un database P11 o P15 con una dimensione massima maggiore di 1 TB a 1 TB per database P11 o 1 TB per database P15 o un livello di prestazioni inferiore, ad esempio P1-P6. Questa restrizione si applica anche agli scenari di ripristino e copia, inclusi gli scenari temporizzati, il ripristino geografico, la conservazione backup a lungo termine e la copia del database. Dopo avere configurato un database con una dimensione massima maggiore di 1 TB, tutte le operazioni di ripristino di tale database devono essere eseguite in un database P11/P15 con dimensioni massime superiori a 1 TB.
- Per gli scenari di replica geografica attiva:
   - Impostazione di una relazione di replica geografica: se il database primario è P11 o P15, devono esserlo anche i database secondari. I livelli di prestazioni inferiori non vengono accettati come database secondari in quanto non supportano l'opzione superiore a 1 TB.
   - Aggiornamento del database primario in una relazione di replica geografica: portando a oltre 1 TB le dimensioni massime di un database primario, viene attivata la stessa modifica nel database secondario. Entrambi gli aggiornamenti devono avere esito positivo per applicare la modifica al database primario. Vengono applicate limitazioni per l'opzione da oltre 1 TB. Se il database secondario si trova in un'area che non supporta l'opzione da oltre 1 TB, il database primario non viene aggiornato.
- Il servizio di importazione/esportazione per caricare i database P11 o P15 con oltre 1 TB non è supportato. Usare SqlPackage.exe per [importare](sql-database-import.md) ed [esportare](sql-database-export.md) i dati.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pool elastico: dimensioni di archiviazione e livelli delle prestazioni

Per i pool elastici del database SQL le tabelle seguenti illustrano le risorse disponibili a ogni livello di servizio e prestazioni. È possibile impostare il livello di servizio, il livello di prestazioni e la quantità di risorse di archiviazione mediante il [portale di Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), l'[interfaccia della riga di comando di Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) o l'[API REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> I limiti delle risorse di database singoli nei pool elastici sono in genere identici a quelli di database singoli all'esterno dei pool in base alle DTU e al livello di servizio. Ad esempio, il numero massimo di thread di lavoro simultanei per un database S2 è 120. Pertanto, anche il numero massimo di ruoli di lavoro simultanei per un database in un pool Standard è 120 se il numero massimo di DTU per ogni database nel pool è 50 (che è equivalente a S2).
>

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Se vengono utilizzate tutte le DTU di un pool elastico, ogni database del pool riceve un'uguale quantità di risorse per l'elaborazione di query. Il servizio di database SQL suddivide equamente le risorse fra i database con intervalli equivalenti per i tempi di calcolo. La condivisione equa delle risorse del pool elastico accompagna ogni quantità di risorse altrimenti garantita per ogni database quando il numero minimo di DTU per ogni database è impostato su un valore diverso da zero.

### <a name="database-properties-for-pooled-databases"></a>Proprietà del database per i database in pool

La tabella seguente descrive le proprietà per i database in pool.

| Proprietà | Descrizione |
|:--- |:--- |
| Numero massimo di eDTU per database |Il numero massimo di eDTU di cui un database può usufruire nel pool se disponibili sulla base dell'uso da parte di altri database nel pool. Il numero massimo di eDTU per database non è una garanzia di risorse per un database. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Impostare il numero massimo di eDTU per database sufficiente per gestire i picchi di utilizzo dei database. È previsto un certo grado di overcommit perché il pool in genere presuppone modelli di utilizzo dei database a freddo e a caldo in cui i database non raggiungono il picco contemporaneamente. Si pensi al caso in cui il picco di utilizzo per ogni database sia di 20 eDTU e solo il 20% dei 100 database nel pool raggiunga il picco nello stesso momento. Se il numero massimo di eDTU per ogni database è impostato su 20 eDTU, è ragionevole eseguire l'overcommit del pool moltiplicando per 5 e impostare il numero di eDTU su 400. |
| Numero minimo di eDTU per database |Il numero minimo di eDTU garantito a ogni database nel pool. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Il numero minimo di eDTU per database può essere impostato su 0, che corrisponde anche al valore predefinito. Questa proprietà è impostata su un valore compreso tra 0 e l'utilizzo medio di eDTU per ogni database. Il prodotto tra il numero di database nel pool e il numero minimo di eDTU per database non può superare il numero di eDTU per pool. Ad esempio, se un pool dispone di 20 database e di un numero minimo di eDTU per database impostato su 10 eDTU, il numero di eDTU per pool deve essere almeno pari a 200. |
| Quantità massima di risorse di archiviazione per database |Lo spazio di archiviazione massimo per un database in un pool. I database in pool condividono lo spazio di archiviazione del pool, quindi lo spazio di archiviazione del database è limitato allo spazio di archiviazione del pool rimanente e allo spazio massimo per database. Lo spazio di archiviazione massimo per database indica le dimensioni massime dei file di dati e non include lo spazio usato dai file di log. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Pool elastico: modifica delle dimensioni di archiviazione

- Il prezzo eDTU per un pool elastico include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per le quantità di risorse di archiviazione incluse e i limiti di dimensioni massime, vedere [Pool elastico: dimensioni di archiviazione e livelli delle prestazioni](#elastic-pool-storage-sizes-and-performance-levels).
- Le risorse di archiviazione extra per un pool elastico possono essere sottoposte a provisioning aumentandone le dimensioni massime mediante il [portale di Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o l'[API REST](/rest/api/sql/elasticpools/update).
- Il prezzo delle risorse di archiviazione extra per un pool elastico corrisponde alla quantità di risorse di archiviazione extra moltiplicata per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>Pool elastico: modifica delle eDTU

È possibile aumentare o ridurre le risorse disponibili per un pool elastico in base alle esigenze mediante il [portale di Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o l'[API REST](/rest/api/sql/elasticpools/update).

- Durante il ridimensionamento delle eDTU del pool, le connessioni di database vengono interrotte per un breve periodo. Si tratta dello stesso comportamento che si verifica durante il ridimensionamento delle DTU per un singolo database (non in un pool). Per informazioni dettagliate sulla durata e sull'impatto delle connessioni interrotte per un database durante le operazioni di ridimensionamento, vedere la sezione relativa al [ridimensionamento delle DTU per un singolo database](#single-database-change-storage-size). 
- La durata per ridimensionare le eDTU del pool può dipendere dalla quantità totale di risorse di archiviazione usate da tutti i database nel pool. In generale, la latenza media di ridimensionamento è di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale usato da tutti i database nel pool è pari a 200 GB, la latenza prevista per il ridimensionamento del pool è di 3 ore o meno. In alcuni casi, all'interno del livello Standard o Basic, la latenza di ridimensionamento può essere inferiore a cinque minuti, indipendentemente dalla quantità di spazio usata.
- In generale, la durata per modificare il numero minimo di eDTU per database o il numero massimo di eDTU per database è di cinque minuti o meno.
- Quando si riducono le dimensioni delle eDTU del pool, lo spazio del pool usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio e il livello di prestazioni di destinazione.
- Durante il ridimensionamento delle eDTU del pool viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime delle risorse di archiviazione del pool sono supportate dal pool di destinazione e (2) la dimensione massima delle risorse di archiviazione supera la quantità inclusa del pool di destinazione. Se ad esempio un pool standard da 100 eDTU con una dimensione massima di 100 GB viene ridotto a un pool standard da 50 eDTU, viene applicato un costo per le risorse di archiviazione extra, poiché il pool di destinazione supporta una dimensione massima di 100 GB e la quantità di risorse di archiviazione inclusa è solo di 50 GB. La quantità di risorse di archiviazione extra è quindi 100 GB - 50 GB = 50 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore alla quantità inclusa di risorse di archiviazione, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino alla quantità inclusa. 

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Cosa accade quando vengono raggiunti i limiti delle risorse del pool elastico e del database?

### <a name="compute-dtus-and-edtus"></a>Elaborazione (DTU ed eDTU)

Quando l'uso delle risorse di elaborazione del database (misurato in base a DTU ed eDTU) diventa elevato, la latenza delle query aumenta e può anche verificarsi un timeout. In queste condizioni le query possono essere messe in coda dal servizio e vengono rese disponibili alcune risorse per l'esecuzione.
In caso di uso elevato di risorse di elaborazione, le opzioni di mitigazione includono:

- Aumento del livello di prestazioni del database o del pool elastico per garantire un numero superiore di DTU ed eDTU al database. Vedere [Database singolo: modifica delle DTU](#single-database-change-dtus) e [Pool elastico: modifica delle eDTU](#elastic-pool-change-edtus).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Archiviazione

Quando la quantità di spazio del database usato raggiunge il limite di dimensioni massime, gli inserimenti e gli aggiornamenti del database che aumentano la dimensione dei dati hanno esito negativo e i clienti ricevono un [messaggio di errore](sql-database-develop-error-messages.md). Le operazioni SELECT e DELETE del database continuano ad avere esito positivo.

In caso di uso elevato di spazio, le opzioni di mitigazione includono:

- Aumento delle dimensioni massime del database o del pool elastico oppure modifica del livello di prestazioni per ottenere altre risorse di archiviazione incluse. Vedere [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md).
- Se il database è in un pool elastico, in alternativa può essere spostato all'esterno del pool in modo che lo spazio di archiviazione non venga condiviso con altri database.

### <a name="sessions-and-workers-requests"></a>Sessioni e ruoli di lavoro (richieste) 

Il numero massimo di sessioni e ruoli di lavoro simultanei è determinato dal livello di servizio e prestazioni (DTU ed eDTU).  Le nuove richieste vengono rifiutate quando vengono raggiunti i limiti delle sessioni o dei ruoli di lavoro e i clienti ricevono un messaggio di errore. Mentre il numero di connessioni disponibili può essere controllato dall'applicazione, il numero di ruoli di lavoro simultanei è spesso più difficile da stimare e da controllare. Ciò vale soprattutto durante i picchi di periodi di carico quando vengono raggiunti i limiti di risorse del database e i ruoli di lavoro si accumulano per via di query a esecuzione prolungata. 

In caso di uso elevato di sessioni o ruoli di lavoro, le opzioni di mitigazione includono:
- Aumento del livello di servizio o prestazioni del database o del pool elastico. Vedere [Database singolo: modifica delle dimensioni di archiviazione](#single-database-change-storage-size), [Database singolo: modifica delle DTU](#single-database-change-dtus), [Pool elastico: modifica delle dimensioni di archiviazione](#elastic-pool-change-storage-size) e [Pool elastico: modifica delle eDTU](#elastic-pool-change-edtus).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query, se l'aumento dell'uso di ruoli di lavoro è dovuto a un conflitto delle risorse di elaborazione. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui livelli di servizio, vedere [Livelli di servizio](sql-database-service-tiers.md).
- Per informazioni sui database singoli, vedere la sezione relativa alle [risorse dei singoli database](sql-database-resource-limits.md).
- Per informazioni sui pool elastici, vedere [Pool elastici](sql-database-elastic-pool.md).
- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
- Per informazioni su DTU ed eDTU, vedere [DTU ed eDTU](sql-database-what-is-a-dtu.md).
