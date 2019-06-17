---
title: Database SQL di Azure - Leggere le query nelle repliche | Microsoft Docs
description: Il Database SQL di Azure offre la possibilità di bilanciare il carico sola lettura i carichi di lavoro usando la capacità delle repliche di sola lettura, chiamata scalabilità in lettura.
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
ms.openlocfilehash: 1b452fb0bac91429793f8d55e439c36c70784722
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66492728"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Usare le repliche di sola lettura per i carichi di lavoro di bilanciamento del carico query di sola lettura

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Durante la [architettura a disponibilità elevata](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), viene automaticamente eseguito il provisioning di ogni database nel livello di servizio Premium, Business Critical o con scalabilità elevatissima con una replica primaria e più repliche secondarie. Le repliche secondarie vengono effettuato il provisioning con le stesse dimensioni di calcolo come replica primaria. Il **scalabilità in lettura** funzionalità consente di bilanciare il carico del Database SQL sola lettura dei carichi di lavoro usando la capacità di una delle repliche di sola lettura invece di condividere la replica di lettura / scrittura. In questo modo i carichi di lavoro di sola lettura verranno isolati dal carico di lavoro principale di lettura/scrittura e non ne comprometteranno le prestazioni. La funzionalità è previsto per le applicazioni che includono logicamente separati da sola lettura i carichi di lavoro, ad esempio analitica. È possibile migliorare le prestazioni usando questa ulteriore capacità senza costi aggiuntivi.

Il diagramma seguente illustra usando un database Business Critical.

![Repliche di sola lettura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

La funzionalità di scalabilità orizzontale in lettura è abilitata per impostazione predefinita nei nuovi Premium, Business Critical e i database su scala molto vasta. Se la stringa di connessione SQL è configurata con `ApplicationIntent=ReadOnly`, verrà reindirizzata all'applicazione da parte del gateway a una replica di sola lettura di tale database. Per informazioni su come usare il `ApplicationIntent` proprietà, vedere [finalità dell'applicazione specificando](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se si vuole assicurare che l'applicazione si connette alla replica primaria indipendentemente il `ApplicationIntent` impostazione nella stringa di connessione SQL, è necessario disabilitare in modo esplicito lettura scalabilità orizzontale durante la creazione del database o quando si modifica la configurazione. Ad esempio, se si aggiorna il database dal livello Standard o generico al livello Premium, Business Critical o con Iperscalabilità e si desidera assicurarsi che tutte le connessioni continuano passare alla replica primaria, disabilitare la scalabilità in lettura. Per informazioni dettagliate sul modo in cui disabilitarli, vedere [abilitare e disabilitare la scalabilità in lettura](#enable-and-disable-read-scale-out).

> [!NOTE]
> Query Data Store, gli eventi estesi, Profiler SQL e le funzionalità di controllo non sono supportate nelle repliche di sola lettura. 

## <a name="data-consistency"></a>Coerenza dei dati

Uno dei vantaggi delle repliche è che sono sempre in uno stato coerente a livello di transazione. Per temporizzazioni diverse potrebbe però verificarsi una breve latenza tra le diverse repliche. La scalabilità in lettura supporta la coerenza a livello di sessione. Significa che, se la sessione di sola lettura si riconnette dopo un errore di connessione causato dalla mancata disponibilità della replica, potrebbe essere reindirizzato a una replica che non è aggiornata con la replica di lettura / scrittura al 100%. Analogamente, se un'applicazione scrive i dati usando una sessione di lettura / scrittura e li legge immediatamente usando una sessione di sola lettura, è possibile che gli aggiornamenti più recenti non sono immediatamente visibili nella replica. La latenza è causata da un'operazione di ripristino di log delle transazioni asincrona.

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

Lettura Scale-Out è abilitata per impostazione predefinita nei livelli di servizio Premium, Business Critical e su scala molto vasta. Scalabilità orizzontale in lettura non è possibile abilitare nei livelli di servizio Basic, Standard o generico. Scalabilità orizzontale in lettura viene disabilitato automaticamente nei database con scalabilità elevatissima configurati con 0 repliche. 

È possibile disabilitare e riabilitare Scale-Out di lettura sul database singoli e pool di database elastici nel livello di servizio Premium o Business Critical usando i metodi seguenti.

> [!NOTE]
> La possibilità di disabilitare la scalabilità in lettura viene fornita per compatibilità con le versioni precedenti.

### <a name="azure-portal"></a>Portale di Azure

È possibile gestire l'impostazione di lettura Sacle orizzontale nella **configura** pannello del database. 

### <a name="powershell"></a>PowerShell

Per gestire la scalabilità in lettura in Azure PowerShell, è necessaria la versione di Azure PowerShell di dicembre 2016 o una successiva. Per la versione più recente di PowerShell, vedere [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

È possibile disabilitare o riabilitare lettura Scale-Out in Azure PowerShell richiamando il [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet e passando il valore desiderato – `Enabled` o `Disabled` -per il `-ReadScale` parametro. 

Per disabilitare la scalabilità di lettura su un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Per disabilitare la lettura di scalabilità orizzontale in un nuovo database (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Per riabilitare la lettura di scalabilità orizzontale in un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Per creare un database con la lettura scalabilità disabilitata, o per modificare l'impostazione per un database esistente, usare il metodo seguente con il `readScale` impostata su `Enabled` o `Disabled` come nella richiesta di esempio seguente.

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

## <a name="using-tempdb-on-read-only-replica"></a>Uso di TempDB nella replica di sola lettura

Il database TempDB non viene replicato per le repliche di sola lettura. Ogni replica ha la propria versione del database TempDB che viene creato durante la creazione della replica. Assicura che TempDB è aggiornabile e possono essere modificati durante l'esecuzione della query. Se il carico di lavoro di sola lettura si basa sull'utilizzo di TempDB oggetti, è necessario creare questi oggetti nell'ambito dello script di query. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Uso della scalabilità in lettura con database con replica geografica

Se si usa Scale-Out di lettura per il bilanciamento del carico ReadOnly carichi di lavoro in un database con replica geografica (ad esempio un membro di un gruppo di failover), assicurarsi che tale tipo lettura scale-out è abilitata sia il database primario sia per i database secondari con replica geografica. Questa configurazione assicura che la stessa esperienza di bilanciamento del carico continua quando l'applicazione si connette al nuovo database primario dopo il failover. Se ci si connette al database secondario con replica geografica con scalabilità in lettura abilitata, le sessioni con `ApplicationIntent=ReadOnly` saranno indirizzate a una delle repliche nello stesso modo in cui le connessioni sono state indirizzate al database primario.  Le sessioni senza `ApplicationIntent=ReadOnly` verranno indirizzate alla replica primaria del database secondario con replica geografica, anche questa di sola lettura. Poiché i database secondari con replica geografica ha un endpoint diverso da quello del database primario, in passato per accedere al database secondario non è stata deve impostare `ApplicationIntent=ReadOnly`. Per garantire la compatibilità con le versioni precedenti, DMV `sys.geo_replication_links` mostra `secondary_allow_connections=2`, che indica che qualsiasi connessione client è consentita.

> [!NOTE]
> Round robin o qualsiasi altro carico bilanciato routing tra le repliche del database secondario locale non è supportato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'offerta con Iperscalabilità di Database SQL, vedere [livello di servizio su scala molto vasta](./sql-database-service-tier-hyperscale.md).
