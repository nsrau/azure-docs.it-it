---
title: Database SQL di Azure - Leggere le query nelle repliche | Microsoft Docs
description: Il database SQL di Azure offre la possibilità di bilanciare il carico dei carichi di lavoro di sola lettura usando la capacità delle repliche di sola lettura, denominate scalabilità in lettura.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 06/03/2019
ms.openlocfilehash: 3d9da312f86128dc738b367371016d03da2c1629
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228240"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Usare le repliche di sola lettura per bilanciare il carico dei carichi di lavoro di query di sola lettura

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Come parte dell' [architettura a disponibilità elevata](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), ogni database nel livello di servizio Premium, business critical o iperscalabile viene sottoposta automaticamente a provisioning con una replica primaria e diverse repliche secondarie. Le repliche secondarie vengono sottoposte a provisioning con le stesse dimensioni di calcolo della replica primaria. La funzionalità di scalabilità in **lettura** consente di bilanciare il carico dei carichi di lavoro di sola lettura del database SQL usando la capacità di una delle repliche di sola lettura anziché condividere la replica di lettura/scrittura. In questo modo i carichi di lavoro di sola lettura verranno isolati dal carico di lavoro principale di lettura/scrittura e non ne comprometteranno le prestazioni. Questa funzionalità è destinata alle applicazioni che includono carichi di lavoro di sola lettura separati logicamente, ad esempio l'analisi. Potrebbero ottenere vantaggi in termini di prestazioni grazie a questa capacità aggiuntiva senza costi aggiuntivi.

Il diagramma seguente illustra l'uso di un database business critical.

![Repliche di sola lettura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

La funzionalità di scalabilità in lettura è abilitata per impostazione predefinita nei nuovi database Premium, business critical e con iperscalabilità. Se la stringa di connessione SQL è configurata con `ApplicationIntent=ReadOnly`, l'applicazione verrà reindirizzata dal gateway a una replica di sola lettura di tale database. Per informazioni sull'uso della proprietà, `ApplicationIntent` vedere Specifica della [finalità dell'applicazione](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se si desidera assicurarsi che l'applicazione si connetta alla replica primaria indipendentemente dall' `ApplicationIntent` impostazione nella stringa di connessione SQL, è necessario disabilitare in modo esplicito la scalabilità in lettura durante la creazione del database o quando si modifica la configurazione. Ad esempio, se si aggiorna il database dal livello standard o per utilizzo generico al livello Premium, business critical o iperscalabile e si vuole assicurarsi che tutte le connessioni continuino a passare alla replica primaria, disabilitare la scalabilità in lettura. Per informazioni dettagliate su come disabilitarlo, vedere [abilitare e disabilitare](#enable-and-disable-read-scale-out)la scalabilità in lettura.

> [!NOTE]
> Query Data Store, gli eventi estesi, SQL Profiler e le funzionalità di controllo non sono supportati nelle repliche di sola lettura. 

## <a name="data-consistency"></a>Coerenza dei dati

Uno dei vantaggi delle repliche è che sono sempre in uno stato coerente a livello di transazione. Per temporizzazioni diverse potrebbe però verificarsi una breve latenza tra le diverse repliche. La scalabilità in lettura supporta la coerenza a livello di sessione. Ciò significa che se la sessione di sola lettura si riconnette dopo un errore di connessione causato dalla mancata disponibilità della replica, può essere reindirizzata a una replica che non è aggiornata al 100% con la replica di lettura/scrittura. Analogamente, se un'applicazione scrive i dati usando una sessione di lettura/scrittura e li legge immediatamente usando una sessione di sola lettura, è possibile che gli aggiornamenti più recenti non siano immediatamente visibili nella replica. La latenza è causata da un'operazione asincrona di rollforward del log delle transazioni.

> [!NOTE]
> Le latenze di replica nell'area sono basse e questa situazione è rara.

## <a name="connect-to-a-read-only-replica"></a>Connettersi a una replica di sola lettura

Quando si abilita la scalabilità in lettura per un database, l'opzione `ApplicationIntent` nella stringa di connessione fornita dal client indica se la connessione viene instradata alla replica in scrittura o a una replica di sola lettura. In particolare, se il valore di `ApplicationIntent` è `ReadWrite` (valore predefinito), la connessione verrà indirizzata alla replica di lettura/scrittura del database. È un comportamento identico a quello esistente. Se il valore di `ApplicationIntent` è `ReadOnly`, la connessione viene instradata a una replica di sola lettura.

La stringa di connessione seguente, ad esempio, connette il client a una replica di sola lettura (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Entrambe la stringhe di connessione seguenti connettono il client a una replica di lettura/scrittura (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Verificare di essere connessi a una replica di sola lettura

È possibile verificare se si è connessi a una replica di sola lettura eseguendo la query seguente. Verrà restituito READ_ONLY quando si è connessi a una replica di sola lettura.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> In un determinato momento solo una delle repliche AlwaysOn è accessibile per le sessioni ReadOnly.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitoraggio e risoluzione dei problemi della replica di sola lettura

Quando si è connessi a una replica di sola lettura, è possibile accedere alle metriche delle prestazioni `sys.dm_db_resource_stats` usando la DMV. Per accedere alle statistiche del piano di query `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` usare `sys.dm_exec_sql_text` i DMV e.

> [!NOTE]
> La DMV `sys.resource_stats` nel database master logico restituisce i dati di archiviazione e di utilizzo della CPU della replica primaria.


## <a name="enable-and-disable-read-scale-out"></a>Abilitare e disabilitare la scalabilità in lettura

La scalabilità in lettura è abilitata per impostazione predefinita nei livelli di servizio Premium, business critical e iperscalabile. Non è possibile abilitare la scalabilità in lettura nei livelli di servizio Basic, standard o per utilizzo generico. La scalabilità in lettura viene disabilitata automaticamente nei database con iperscalabilità configurati con 0 repliche. 

È possibile disabilitare e riabilitare la scalabilità in lettura in database singoli e database del pool elastico nel livello di servizio Premium o business critical usando i metodi seguenti.

> [!NOTE]
> Per la compatibilità con le versioni precedenti, è disponibile la possibilità di disabilitare la scalabilità in lettura.

### <a name="azure-portal"></a>Portale di Azure

È possibile gestire l'impostazione di scalabilità in lettura nel pannello **Configura** database. 

### <a name="powershell"></a>PowerShell

Per gestire la scalabilità in lettura in Azure PowerShell, è necessaria la versione di Azure PowerShell di dicembre 2016 o una successiva. Per la versione più recente di PowerShell, vedere [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

È possibile disabilitare o riabilitare la scalabilità in lettura in Azure PowerShell richiamando il cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) e passando il valore `Enabled` desiderato oppure `Disabled` , per il `-ReadScale` parametro. 

Per disabilitare la scalabilità in lettura in un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Per disabilitare la scalabilità in lettura in un nuovo database (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Per riabilitare la scalabilità in lettura in un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Per creare un database con scalabilità in lettura disabilitato o per modificare l'impostazione di un database esistente, usare il metodo seguente con la `readScale` proprietà impostata su `Enabled` o `Disabled` come nella richiesta di esempio seguente.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Per altre informazioni, vedere [Databases - Create or Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) (Database - Creare o aggiornare).

## <a name="using-tempdb-on-read-only-replica"></a>Utilizzo di TempDB in una replica di sola lettura

Il database TempDB non viene replicato nelle repliche di sola lettura. Ogni replica dispone di una propria versione del database TempDB creato al momento della creazione della replica. Garantisce che TempDB sia aggiornabile e possa essere modificato durante l'esecuzione della query. Se il carico di lavoro di sola lettura dipende dall'uso di oggetti TempDB, è necessario creare questi oggetti come parte dello script di query. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Uso della scalabilità in lettura con database con replica geografica

Se si usa la scalabilità in lettura per bilanciare il carico dei carichi di lavoro di sola lettura in un database con replica geografica, ad esempio come membro di un gruppo di failover, assicurarsi che la scalabilità in lettura sia abilitata sia nei database primari che nei database secondari con replica geografica. Questa configurazione garantisce che la stessa esperienza di bilanciamento del carico continui quando l'applicazione si connette al nuovo database primario dopo il failover. Se ci si connette al database secondario con replica geografica con scalabilità in lettura abilitata, le sessioni con `ApplicationIntent=ReadOnly` saranno indirizzate a una delle repliche nello stesso modo in cui le connessioni sono state indirizzate al database primario.  Le sessioni senza `ApplicationIntent=ReadOnly` verranno indirizzate alla replica primaria del database secondario con replica geografica, anche questa di sola lettura. Poiché il database secondario con replica geografica ha un endpoint diverso da quello del database primario, per accedere al database secondario non era necessario impostare `ApplicationIntent=ReadOnly`. Per garantire la compatibilità con le versioni precedenti, DMV `sys.geo_replication_links` mostra `secondary_allow_connections=2`, che indica che qualsiasi connessione client è consentita.

> [!NOTE]
> Il Round Robin o qualsiasi altro routing con bilanciamento del carico tra le repliche locali del database secondario non è supportato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'offerta di scalabilità di database SQL, vedere [livello di servizio](./sql-database-service-tier-hyperscale.md)con scalabilità automatica.
