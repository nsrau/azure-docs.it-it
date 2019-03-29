---
title: Database SQL di Azure - Leggere le query nelle repliche | Microsoft Docs
description: Il database SQL di Azure consente di bilanciare il carico dei carichi di lavoro di sola lettura usando la funzionalità delle repliche di sola lettura, chiamata scalabilità in lettura.
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
ms.date: 03/28/2019
ms.openlocfilehash: d9ad859ef24b51dc337dc23281d2fe4e1eada1e6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619892"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Usare le repliche di sola lettura per caricare i carichi di lavoro di saldo query di sola lettura

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

La **scalabilità in lettura** consente di bilanciare il carico dei carichi di lavoro di sola lettura del database SQL di Azure usando la funzionalità di un'unica replica di sola lettura.

Di ogni database nel livello Premium ([modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md)) o nel livello Business Critical ([modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md)) viene effettuato il provisioning automatico con diverse repliche AlwaysOn per supportare il contratto di servizio per la disponibilità. Tale condizione è illustrata dal diagramma seguente.

![Repliche di sola lettura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Le repliche secondarie vengono effettuato il provisioning con le stesse dimensioni di calcolo come replica primaria. La funzionalità di **scalabilità in lettura** consente di bilanciare il carico dei carichi di lavoro di sola lettura del database SQL usando la capacità di una delle repliche di sola lettura invece di condividere la replica di lettura/scrittura. In questo modo i carichi di lavoro di sola lettura verranno isolati dal carico di lavoro principale di lettura/scrittura e non ne comprometteranno le prestazioni. La funzionalità è destinata alle applicazioni che includono carichi di lavoro di sola lettura separati in modo logico, ad esempio dati di analisi, che quindi potrebbero ottenere vantaggi di prestazioni usando tale capacità senza costi aggiuntivi.

Per usare la funzionalità di scalabilità orizzontale in lettura con un determinato database, è necessario abilitare esplicitamente, durante la creazione del database o in un secondo momento modificando la configurazione con PowerShell richiamando il [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) o il [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet o tramite il REST API di Azure Resource Manager usando i [database - creare o aggiornare](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) (metodo).

Dopo aver abilitato la scalabilità in lettura per un database, applicazioni che si connettono a tale database verranno indirizzate da parte del gateway per la replica in lettura / scrittura o a una replica di sola lettura del database in base al `ApplicationIntent` proprietà configurate nel stringa di connessione dell'applicazione. Per informazioni sulla proprietà `ApplicationIntent`, vedere [Specifying Application Intent](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent) (Specifica della finalità dell'applicazione).

Se la scalabilità in lettura è disabilitata o è stata impostata la proprietà ReadScale in un livello di servizio non supportato, tutte le connessioni vengono indirizzate alla replica in lettura-scrittura, indipendentemente dalla proprietà `ApplicationIntent`.

> [!NOTE]
> Query Data Store, gli eventi estesi, Profiler SQL e le funzionalità di controllo non sono supportate nelle repliche di sola lettura. 
## <a name="data-consistency"></a>Coerenza dei dati

Uno dei vantaggi delle repliche è che sono sempre in uno stato coerente a livello di transazione. Per temporizzazioni diverse potrebbe però verificarsi una breve latenza tra le diverse repliche. La scalabilità in lettura supporta la coerenza a livello di sessione. Se quindi la sessione di sola lettura si riconnette dopo un errore di connessione causato dalla mancata disponibilità della replica, può essere reindirizzata a una replica non aggiornata al 100% con la replica di lettura/scrittura. Analogamente, se un'applicazione scrive i dati usando una sessione di lettura / scrittura e li legge immediatamente usando una sessione di sola lettura, è possibile che gli aggiornamenti più recenti non sono immediatamente visibili nella replica. La latenza è causata da un'operazione di ripristino di log delle transazioni asincrona.

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

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitoraggio e risoluzione dei problemi di replica di sola lettura

Quando si è connessi a una replica di sola lettura, è possibile accedere a metriche di prestazioni usando il `sys.dm_db_resource_stats` DMV. Per accedere alle statistiche sul piano di query, usare il `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` e `sys.dm_exec_sql_text` viste a gestione dinamica.

> [!NOTE]
> La vista DMV `sys.resource_stats` nel database master logico restituisce i dati di archiviazione e di utilizzo della CPU della replica primaria.


## <a name="enable-and-disable-read-scale-out"></a>Abilitare e disabilitare la scalabilità in lettura

La scalabilità in lettura è abilitata per impostazione predefinita nel livello Business Critical di [Istanza gestita](sql-database-managed-instance.md). Deve essere abilitata in modo esplicito nei livelli Premium e Business Critical del [database disponibile nel server di database SQL](sql-database-servers.md). I metodi per l'abilitazione e la disabilitazione della scalabilità in lettura sono illustrati di seguito.

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell: Abilitare e disabilitare la scalabilità in lettura

Per gestire la scalabilità in lettura in Azure PowerShell, è necessaria la versione di Azure PowerShell di dicembre 2016 o una successiva. Per la versione più recente di PowerShell, vedere [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Abilitare o disabilitare la lettura di scalabilità in Azure PowerShell richiamando il [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet e passando il valore desiderato – `Enabled` o `Disabled` -per il `-ReadScale` parametro. In alternativa, è possibile usare la [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet per creare un nuovo database con scalabilità in lettura abilitata.

Ad esempio, per abilitare la scalabilità in lettura per un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Per disabilitare la scalabilità in lettura per un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Per creare un nuovo database con la scalabilità in lettura abilitata (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>API REST: Abilitare e disabilitare la scalabilità in lettura

Per creare un database con la scalabilità in lettura abilitata oppure per abilitare o disabilitare la scalabilità in lettura per un database esistente, creare o aggiornare l'entità del database corrispondente con la proprietà `readScale` impostata su `Enabled` o `Disabled`, come nella richiesta di esempio seguente.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

Per altre informazioni, vedere [Databases - Create or Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) (Database - Creare o aggiornare).

## <a name="using-tempdb-on-read-only-replica"></a>Uso di TempDB nella replica di sola lettura

Il database TempDB non viene replicato per le repliche di sola lettura. Ogni replica ha la propria versione del database TempDB che viene creato durante la creazione della replica. Assicura che TempDB è aggiornabile e possono essere modificati durante l'esecuzione della query. Se il carico di lavoro di sola lettura si basa sull'utilizzo di TempDB oggetti, è necessario creare questi oggetti nell'ambito dello script di query. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Uso della scalabilità in lettura con database con replica geografica

Se si usa lettura scalabilità orizzontale per caricare il saldo ReadOnly carichi di lavoro in un database con replica geografica (ad esempio un membro di un gruppo di failover), assicurarsi che leggono scale-out è abilitata sia il database primario sia per i database secondari con replica geografica. Questa configurazione assicura che la stessa esperienza di bilanciamento del carico continua quando l'applicazione si connette al nuovo database primario dopo il failover. Se ci si connette al database secondario con replica geografica con scalabilità in lettura abilitata, le sessioni con `ApplicationIntent=ReadOnly` saranno indirizzate a una delle repliche nello stesso modo in cui le connessioni sono state indirizzate al database primario.  Le sessioni senza `ApplicationIntent=ReadOnly` verranno indirizzate alla replica primaria del database secondario con replica geografica, anche questa di sola lettura. Poiché il database secondario con replica geografica dispone di un endpoint diverso da quello del database primario, in passato per accedere al database secondario non era necessario impostare `ApplicationIntent=ReadOnly`. Per garantire la compatibilità con le versioni precedenti, DMV `sys.geo_replication_links` mostra `secondary_allow_connections=2`, che indica che qualsiasi connessione client è consentita.

> [!NOTE]
> Round robin o qualsiasi altro con carico bilanciato il routing tra le repliche del database secondario locale non è supportato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'uso di PowerShell per impostare la lettura di scalabilità, vedere la [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) o il [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet.
- Per informazioni sull'uso dell'API REST per impostare la scalabilità in lettura, vedere [Databases - Create or Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) (Database - Creare o aggiornare).
