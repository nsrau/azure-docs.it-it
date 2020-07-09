---
title: Leggere query sulle repliche
description: Azure SQL offre la possibilità di usare la capacità delle repliche di sola lettura per i carichi di lavoro di lettura, detti scalabilità in lettura.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/26/2020
ms.openlocfilehash: cf9f48b0907d3bfe1d07dcffcc0d0b9534f74c83
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135896"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>Usare le repliche di sola lettura per l'offload dei carichi di lavoro di query di sola lettura
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Come parte dell' [architettura a disponibilità elevata](high-availability-sla.md#premium-and-business-critical-service-tier-availability), per ogni database e istanza gestita nel livello di servizio Premium e business critical viene effettuato automaticamente il provisioning con una replica di lettura/scrittura primaria e diverse repliche secondarie di sola lettura. Le repliche secondarie vengono sottoposte a provisioning con le stesse dimensioni di calcolo della replica primaria. La funzionalità di *scalabilità in lettura* consente di eseguire l'offload dei carichi di lavoro di sola lettura usando la capacità di calcolo di una delle repliche di sola lettura, anziché eseguirli nella replica di lettura/scrittura. In questo modo, alcuni carichi di lavoro di sola lettura possono essere isolati dai carichi di lavoro di lettura/scrittura e non influiscono sulle prestazioni. Questa funzionalità è destinata alle applicazioni che includono carichi di lavoro di sola lettura separati logicamente, ad esempio l'analisi. Nei livelli di servizio Premium e business critical, le applicazioni possono ottenere vantaggi in termini di prestazioni grazie a questa capacità aggiuntiva senza costi aggiuntivi.

La funzionalità di *scalabilità in lettura* è disponibile anche nel livello di servizio di iperscalabilità quando viene creata almeno una replica secondaria. È possibile usare più repliche secondarie per il bilanciamento del carico dei carichi di lavoro di sola lettura che richiedono più risorse rispetto a quelle disponibili in una replica secondaria.

L'architettura a disponibilità elevata dei livelli di servizio Basic, standard e per utilizzo generico non include alcuna replica. La funzionalità di *scalabilità in lettura* non è disponibile in questi livelli di servizio.

Nel diagramma seguente viene illustrata la funzionalità.

![Repliche di sola lettura](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

La funzionalità di *scalabilità in lettura* è abilitata per impostazione predefinita nei nuovi database Premium, business critical e con iperscalabilità. Per l'iperscalabilità, per impostazione predefinita viene creata una replica secondaria per i nuovi database. 

> [!NOTE]
> La scalabilità in lettura è sempre abilitata nel livello di servizio business critical di Istanza gestita.

Se la stringa di connessione SQL è configurata con `ApplicationIntent=ReadOnly` , l'applicazione verrà reindirizzata a una replica di sola lettura di tale database o istanza gestita. Per informazioni sull'uso della `ApplicationIntent` proprietà, vedere Specifica della [finalità dell'applicazione](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se si desidera assicurarsi che l'applicazione si connetta alla replica primaria indipendentemente dall' `ApplicationIntent` impostazione nella stringa di connessione SQL, è necessario disabilitare in modo esplicito la scalabilità in lettura durante la creazione del database o quando si modifica la configurazione. Ad esempio, se si aggiorna il database dal livello standard o per utilizzo generico al livello Premium, business critical o iperscalabile e si vuole assicurarsi che tutte le connessioni continuino a passare alla replica primaria, disabilitare la scalabilità in lettura. Per informazioni dettagliate su come disabilitarlo, vedere [abilitare e disabilitare la scalabilità in lettura](#enable-and-disable-read-scale-out).

> [!NOTE]
> Le funzionalità di Query Store e SQL Profiler non sono supportate nelle repliche di sola lettura. 

## <a name="data-consistency"></a>Coerenza dei dati

Uno dei vantaggi delle repliche è che sono sempre in uno stato coerente a livello di transazione. Per temporizzazioni diverse potrebbe però verificarsi una breve latenza tra le diverse repliche. La scalabilità in lettura supporta la coerenza a livello di sessione. Ciò significa che se la sessione di sola lettura si riconnette dopo un errore di connessione causato dalla mancata disponibilità della replica, può essere reindirizzata a una replica che non è aggiornata al 100% con la replica di lettura/scrittura. Analogamente, se un'applicazione scrive i dati usando una sessione di lettura/scrittura e li legge immediatamente usando una sessione di sola lettura, è possibile che gli aggiornamenti più recenti non siano immediatamente visibili nella replica. La latenza è causata da un'operazione asincrona di rollforward del log delle transazioni.

> [!NOTE]
> Le latenze di replica all'interno dell'area sono basse e questa situazione è rara. Per monitorare la latenza di replica, vedere [monitoraggio e risoluzione dei problemi della replica](#monitoring-and-troubleshooting-read-only-replicas)di sola lettura.

## <a name="connect-to-a-read-only-replica"></a>Connettersi a una replica di sola lettura

Quando si Abilita la scalabilità in lettura per un database, l' `ApplicationIntent` opzione nella stringa di connessione fornita dal client stabilisce se la connessione viene instradata alla replica di scrittura o a una replica di sola lettura. In particolare, se il `ApplicationIntent` valore è `ReadWrite` (valore predefinito), la connessione verrà indirizzata alla replica di lettura/scrittura. Questo comportamento è identico a quello quando `ApplicationIntent` non è incluso nella stringa di connessione. Se il valore di `ApplicationIntent` è `ReadOnly`, la connessione viene instradata a una replica di sola lettura.

La stringa di connessione seguente, ad esempio, connette il client a una replica di sola lettura (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Entrambe la stringhe di connessione seguenti connettono il client a una replica di lettura/scrittura (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Verificare di essere connessi a una replica di sola lettura

È possibile verificare se si è connessi a una replica di sola lettura eseguendo la query seguente nel contesto del database. Verrà restituito READ_ONLY quando si è connessi a una replica di sola lettura.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> Nei livelli di servizio Premium e business critical è possibile accedere solo a una delle repliche di sola lettura in un determinato momento. Iperscalabilità supporta più repliche di sola lettura.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>Monitoraggio e risoluzione dei problemi delle repliche di sola lettura

Quando si è connessi a una replica di sola lettura, le viste a gestione dinamica (DMV) riflettono lo stato della replica e possono essere sottoposte a query a scopo di monitoraggio e risoluzione dei problemi. Il motore di database offre più visualizzazioni per esporre un'ampia gamma di dati di monitoraggio. 

Le visualizzazioni di uso comune sono:

| Nome | Scopo |
|:---|:---|
|[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Fornisce metriche di utilizzo delle risorse per l'ultima ora, tra cui CPU, i/o dati e utilizzo di scrittura log rispetto ai limiti degli obiettivi di servizio.|
|[sys.dm_os_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| Fornisce le statistiche di attesa aggregate per l'istanza del motore di database. |
|[sys. dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| Fornisce le statistiche di sincronizzazione e lo stato di integrità della replica. Le dimensioni della coda di rollforward e la velocità di rollforward vengono utilizzate come indicatori di latenza dei dati nella replica di sola lettura. |
|[sys.dm_os_performance_counters](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| Fornisce contatori delle prestazioni del motore di database.|
|[sys.dm_exec_query_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| Fornisce le statistiche di esecuzione per query, ad esempio il numero di esecuzioni, il tempo di CPU usato e così via.|
|[sys. dm_exec_query_plan ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| Fornisce piani di query memorizzati nella cache. |
|[sys. dm_exec_sql_text ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| Fornisce il testo della query per un piano di query memorizzato nella cache.|
|[sys.dm_exec_query_profiles](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Fornisce lo stato di avanzamento delle query in tempo reale durante l'esecuzione delle query.|
|[sys. dm_exec_query_plan_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Fornisce l'ultimo piano di esecuzione effettivo noto, incluse le statistiche di runtime per una query.|
|[sys. dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| Fornisce le statistiche relative a IOPS, velocità effettiva e latenza di archiviazione per tutti i file di database. |

> [!NOTE]
> `sys.resource_stats`E `sys.elastic_pool_resource_stats` DMV nel database master logico restituiscono i dati di utilizzo delle risorse della replica primaria.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>Monitoraggio delle repliche di sola lettura con eventi estesi

Non è possibile creare una sessione eventi estesi quando si è connessi a una replica di sola lettura. Tuttavia, nel database SQL di Azure, le definizioni delle sessioni di [eventi estesi](xevent-db-diff-from-svr.md) con ambito database create e modificate nella replica primaria vengono replicate in repliche di sola lettura, incluse le repliche geografiche, e acquisiscono eventi in repliche di sola lettura.

Una sessione di eventi estesi in una replica di sola lettura basata su una definizione di sessione della replica primaria può essere avviata e arrestata in modo indipendente dalla replica primaria. Quando una sessione di eventi estesi viene rilasciata nella replica primaria, viene eliminata anche in tutte le repliche di sola lettura.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>Livello di isolamento delle transazioni nelle repliche di sola lettura

Per le query eseguite su repliche di sola lettura viene sempre eseguito il mapping al livello di isolamento della transazione [snapshot](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) . L'isolamento dello snapshot utilizza il controllo delle versioni delle righe per evitare scenari in cui i lettori bloccano i writer.

In rari casi, se una transazione di isolamento dello snapshot accede ai metadati degli oggetti che sono stati modificati in un'altra transazione simultanea, è possibile che venga visualizzato l'errore [3961](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error), "transazione di isolamento dello snapshot non riuscita nel database '%. * ls ' perché l'oggetto a cui si accede dall'istruzione è stato modificato da un'istruzione DDL in un'altra transazione simultanea dopo l'avvio della transazione È stata respinta perché i metadati non sono sottoposti al controllo delle versioni. Un aggiornamento simultaneo dei metadati può compromettere l'incoerenza se è stato combinato con l'isolamento dello snapshot.

### <a name="long-running-queries-on-read-only-replicas"></a>Query con esecuzione prolungata su repliche di sola lettura

Le query in esecuzione su repliche di sola lettura devono accedere ai metadati per gli oggetti a cui viene fatto riferimento nella query (tabelle, indici, statistiche e così via) In rari casi, se un oggetto di metadati viene modificato nella replica primaria mentre una query conserva un blocco sullo stesso oggetto nella replica di sola lettura, la query può [bloccare](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) il processo che applica le modifiche dalla replica primaria alla replica di sola lettura. Se l'esecuzione di una query di questo tipo richiede molto tempo, la replica di sola lettura non sarà più sincronizzata con la replica primaria. 

Se una query con esecuzione prolungata su una replica di sola lettura causa questo tipo di blocco, verrà automaticamente terminato e la sessione riceverà l'errore 1219, "la sessione è stata disconnessa a causa di un'operazione DDL ad alta priorità".

> [!NOTE]
> Se viene visualizzato l'errore 3961 o l'errore 1219 durante l'esecuzione di query su una replica di sola lettura, riprovare a eseguire la query.

> [!TIP]
> Nei livelli di servizio Premium e business critical, quando si è connessi a una replica di sola lettura, le `redo_queue_size` `redo_rate` colonne e nella DMV [sys. dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) possono essere utilizzate per monitorare il processo di sincronizzazione dei dati, fungendo da indicatori della latenza dei dati nella replica di sola lettura.
> 

## <a name="enable-and-disable-read-scale-out"></a>Abilitare e disabilitare la scalabilità in lettura

La scalabilità in lettura è abilitata per impostazione predefinita nei livelli di servizio Premium, business critical e iperscalabile. Non è possibile abilitare la scalabilità in lettura nei livelli di servizio Basic, standard o per utilizzo generico. La scalabilità in lettura viene disabilitata automaticamente nei database con iperscalabilità configurati con zero repliche.

È possibile disabilitare e riabilitare la scalabilità in lettura in database singoli e database di pool elastici nei livelli di servizio Premium o business critical usando i metodi seguenti.

> [!NOTE]
> Per i database singoli e i database del pool elastico, la possibilità di disabilitare la scalabilità in lettura viene fornita per compatibilità con le versioni precedenti. Non è possibile disabilitare la scalabilità in lettura in business critical istanze gestite.

### <a name="azure-portal"></a>Portale di Azure

È possibile gestire l'impostazione di scalabilità in lettura nel pannello **Configura** database.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato, ma tutto lo sviluppo futuro riguarda il modulo AZ. SQL. Il modulo Azure Resource Manager continuerà a ricevere correzioni di bug fino ad almeno il 2020 dicembre.  Gli argomenti per i comandi nel modulo AZ e nei moduli Azure Resource Manager sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione al nuovo Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Per la gestione della scalabilità in lettura in Azure PowerShell è necessario il 2016 dicembre Azure PowerShell o versione successiva. Per la versione più recente di PowerShell, vedere [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

È possibile disabilitare o riabilitare la scalabilità in lettura in Azure PowerShell richiamando il cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) e passando il valore desiderato ( `Enabled` o `Disabled` ) per il `-ReadScale` parametro.

Per disabilitare la scalabilità in lettura in un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Per disabilitare la scalabilità in lettura in un nuovo database (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Per riabilitare la scalabilità in lettura in un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Per creare un database con scalabilità in lettura disabilitato o per modificare l'impostazione di un database esistente, usare il metodo seguente con la `readScale` proprietà impostata su `Enabled` o `Disabled` , come nella richiesta di esempio seguente.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Per ulteriori informazioni, vedere [database-create or Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>Utilizzo del `tempdb` database in una replica di sola lettura

Il `tempdb` database nella replica primaria non viene replicato nelle repliche di sola lettura. Ogni replica dispone di un proprio `tempdb` database creato al momento della creazione della replica. Ciò garantisce che `tempdb` sia aggiornabile e possa essere modificato durante l'esecuzione della query. Se il carico di lavoro di sola lettura dipende dall'uso `tempdb` di oggetti, è necessario creare questi oggetti come parte dello script di query.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Uso della scalabilità in lettura con database con replica geografica

I database secondari con replica geografica presentano la stessa architettura a disponibilità elevata dei database primari. Se ci si connette al database secondario con replica geografica con scalabilità in lettura abilitata, le sessioni con `ApplicationIntent=ReadOnly` verranno indirizzate a una delle repliche a disponibilità elevata nello stesso modo in cui vengono instradate nel database scrivibile primario. Le sessioni senza `ApplicationIntent=ReadOnly` verranno indirizzate alla replica primaria del database secondario con replica geografica, anche questa di sola lettura. 

In questo modo, la creazione di una replica geografica offre altre due repliche di sola lettura per un database primario di lettura/scrittura, per un totale di tre repliche di sola lettura. Ogni replica geografica aggiuntiva fornisce un'altra coppia di repliche di sola lettura. È possibile creare repliche geografiche in qualsiasi area di Azure, inclusa l'area del database primario.

> [!NOTE]
> Non esiste un Round Robin automatico o qualsiasi altro routing con carico bilanciato tra le repliche di un database secondario con replica geografica.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'offerta di scalabilità di database SQL, vedere [livello di servizio con scalabilità](service-tier-hyperscale.md)automatica.
