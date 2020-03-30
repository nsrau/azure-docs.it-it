---
title: Leggere query sulle replicheRead queries on replicas
description: Il database SQL di Azure offre la possibilità di bilanciare il carico dei carichi di lavoro di sola lettura usando la capacità delle repliche di sola lettura, denominate scalabilità orizzontale in lettura.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206946"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Usare le repliche di sola lettura per bilanciare il carico dei carichi di lavoro di query di sola lettura

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Come parte [dell'architettura a disponibilità elevata](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), viene eseguito automaticamente il provisioning di ogni database nel livello servizio Premium e Business Critical con una replica primaria e diverse repliche secondarie. Viene eseguito il provisioning delle repliche secondarie con le stesse dimensioni di calcolo della replica primaria. La funzionalità **di scalabilità** orizzontale in lettura consente di bilanciare il carico dei carichi di lavoro di sola lettura del database SQL utilizzando la capacità di una delle repliche di sola lettura anziché condividere la replica di lettura/scrittura. In questo modo i carichi di lavoro di sola lettura verranno isolati dal carico di lavoro principale di lettura/scrittura e non ne comprometteranno le prestazioni. La funzionalità è destinata alle applicazioni che includono carichi di lavoro di sola lettura separati logicamente, ad esempio l'analisi. Nei livelli di servizio Premium e Business Critical, le applicazioni potrebbero ottenere vantaggi in termini di prestazioni utilizzando questa capacità aggiuntiva senza costi aggiuntivi.

La funzionalità **di scalabilità** orizzontale in lettura è disponibile anche nel livello di servizio Hyperscale quando viene creata almeno una replica secondaria. È possibile usare più repliche secondarie se i carichi di lavoro di sola lettura richiedono più risorse di quelle disponibili in una replica secondaria. L'architettura a disponibilità elevata dei livelli di servizio Basic, Standard e General Purpose non include repliche. La funzionalità **di scalabilità** orizzontale in lettura non è disponibile in questi livelli di servizio.

Il diagramma seguente illustra l'utilizzo di un database Business Critical.

![Repliche di sola lettura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

La funzionalità di scalabilità orizzontale in lettura è abilitata per impostazione predefinita nei nuovi database Premium, Business Critical e Hyperscale. Per Hyperscale, una replica secondaria viene creata per impostazione predefinita per i nuovi database. Se la stringa di `ApplicationIntent=ReadOnly`connessione SQL è configurata con , l'applicazione verrà reindirizzata dal gateway a una replica di sola lettura del database. Per informazioni su come `ApplicationIntent` utilizzare la proprietà, vedere [Specifica dell'intento dell'applicazione](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se si desidera assicurarsi che l'applicazione si connetta alla replica primaria indipendentemente dall'impostazione nella stringa di connessione SQL, è necessario disabilitare in modo esplicito la scalabilità orizzontale in lettura durante la creazione del database o quando si modifica la `ApplicationIntent` configurazione. Ad esempio, se si aggiorna il database dal livello Standard o Uso generale al livello Premium, Business Critical o Hyperscale e si desidera assicurarsi che tutte le connessioni continuino a passare alla replica primaria, disabilitare la scalabilità orizzontale in lettura. Per informazioni dettagliate su come disabilitarla, vedere [Abilitare e disabilitare la scalabilità in lettura.](#enable-and-disable-read-scale-out)

> [!NOTE]
> Le funzionalità Archivio dati query, Eventi estesi e Profiler SQL non sono supportate nelle repliche di sola lettura.

## <a name="data-consistency"></a>Coerenza dei dati

Uno dei vantaggi delle repliche è che sono sempre in uno stato coerente a livello di transazione. Per temporizzazioni diverse potrebbe però verificarsi una breve latenza tra le diverse repliche. La scalabilità in lettura supporta la coerenza a livello di sessione. Ciò significa che, se la sessione di sola lettura si riconnette dopo un errore di connessione causato dall'indisponibilità della replica, può essere reindirizzata a una replica che non è aggiornata al 100% con la replica di lettura/scrittura. Analogamente, se un'applicazione scrive i dati utilizzando una sessione di lettura/scrittura e li legge immediatamente utilizzando una sessione di sola lettura, è possibile che gli aggiornamenti più recenti non siano immediatamente visibili nella replica. La latenza è causata da un'operazione di ripetizione del log delle transazioni asincrona.

> [!NOTE]
> Le latenze di replica nell'area sono basse e questa situazione è rara.

## <a name="connect-to-a-read-only-replica"></a>Connettersi a una replica di sola lettura

Quando si abilita la scalabilità in lettura per un database, l'opzione `ApplicationIntent` nella stringa di connessione fornita dal client indica se la connessione viene instradata alla replica in scrittura o a una replica di sola lettura. In particolare, se il valore di `ApplicationIntent` è `ReadWrite` (valore predefinito), la connessione verrà indirizzata alla replica di lettura/scrittura del database. È un comportamento identico a quello esistente. Se il valore di `ApplicationIntent` è `ReadOnly`, la connessione viene instradata a una replica di sola lettura.

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

È possibile verificare se si è connessi a una replica di sola lettura eseguendo la query seguente. Verrà restituito READ_ONLY quando si è connessi a una replica di sola lettura.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> In un determinato momento solo una delle repliche AlwaysOn è accessibile per le sessioni ReadOnly.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitoraggio e risoluzione dei problemi relativi alla replica di sola letturaMonitoring and troubleshooting read-only replica

Quando si è connessi a una replica di sola `sys.dm_db_resource_stats` lettura, è possibile accedere alle metriche delle prestazioni utilizzando la DMV. Per accedere alle statistiche `sys.dm_exec_query_stats`del `sys.dm_exec_query_plan` `sys.dm_exec_sql_text` piano di query, utilizzare i controlli e le DMV.

> [!NOTE]
> La DMV `sys.resource_stats` nel database master logico restituisce l'utilizzo della CPU e i dati di archiviazione della replica primaria.

## <a name="enable-and-disable-read-scale-out"></a>Abilitare e disabilitare la scalabilità in lettura

La scalabilità orizzontale in lettura è abilitata per impostazione predefinita nei livelli di servizio Premium, Business Critical e Hyperscale.Read Scale-Out is enabled by default on Premium, Business Critical and Hyperscale service tiers. Impossibile abilitare la scalabilità orizzontale in lettura nei livelli di servizio Basic, Standard o General Purpose.Read Scale-Out cannot be enabled in Basic, Standard, or General Purpose service tiers. La scalabilità in lettura viene disabilitata automaticamente nei database con scalabilità hyperscale configurati con 0 repliche.

È possibile disabilitare e riabilitare la scalabilità orizzontale in lettura in singoli database e database di pool elastici nel livello di servizio Premium o Business Critical utilizzando i metodi seguenti.

> [!NOTE]
> La possibilità di disabilitare la scalabilità orizzontale in lettura viene fornita per garantire la compatibilità con le versioni precedenti.

### <a name="azure-portal"></a>Portale di Azure

È possibile gestire l'impostazione Scalabilità orizzontale in lettura nel pannello **Configura** database.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Il modulo Di PowerShell Azure Resource Manager (RM) è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.  Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione al nuovo modulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Per gestire la scalabilità in lettura in Azure PowerShell, è necessaria la versione di Azure PowerShell di dicembre 2016 o una successiva. Per la versione più recente di PowerShell, vedere [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

È possibile disabilitare o riabilitare la scalabilità orizzontale in lettura in Azure PowerShell richiamando il `-ReadScale` cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) e passando il valore desiderato, `Enabled` oppure `Disabled` , per il parametro.

Per disabilitare la scalabilità orizzontale in lettura in un database esistente (sostituendo gli elementi tra parentesi angolari con i valori corretti per l'ambiente ed eliminando le parentesi angolari):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Per disabilitare la scalabilità orizzontale in lettura in un nuovo database (sostituendo gli elementi tra parentesi angolari con i valori corretti per l'ambiente e eliminando le parentesi angolari):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Per riattivare la scalabilità orizzontale in lettura in un database esistente (sostituendo gli elementi tra parentesi angolari con i valori corretti per l'ambiente ed eliminando le parentesi angolari):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Per creare un database con la scalabilità in lettura disabilitata o per modificare `readScale` l'impostazione di un database esistente, utilizzare il metodo seguente con la proprietà impostata su `Enabled` o `Disabled` come nella richiesta di esempio seguente.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Per altre informazioni, vedere [Databases - Create or Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) (Database - Creare o aggiornare).

## <a name="using-tempdb-on-read-only-replica"></a>Utilizzo di TempDB nella replica di sola letturaUsing TempDB on read-only replica

Il database TempDB non viene replicato nelle repliche di sola lettura. Ogni replica dispone di una propria versione del database TempDB che viene creata quando viene creata la replica. Garantisce che TempDB sia aggiornabile e possa essere modificato durante l'esecuzione della query. Se il carico di lavoro di sola lettura dipende dall'utilizzo di oggetti TempDB, è necessario creare questi oggetti come parte dello script di query.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Uso della scalabilità in lettura con database con replica geografica

Se si usa la scalabilità in lettura per bilanciare il carico dei carichi di lavoro di sola lettura in un database replicato geograficamente (ad esempio, come membro di un gruppo di failover), assicurarsi che la scalabilità orizzontale in lettura sia abilitata nei database secondari primari e in quelli replicati geograficamente. Questa configurazione garantirà che la stessa esperienza di bilanciamento del carico continui quando l'applicazione si connette al nuovo database primario dopo il failover. Se ci si connette al database secondario con replica geografica con scalabilità in lettura abilitata, le sessioni con `ApplicationIntent=ReadOnly` saranno indirizzate a una delle repliche nello stesso modo in cui le connessioni sono state indirizzate al database primario.  Le sessioni senza `ApplicationIntent=ReadOnly` verranno indirizzate alla replica primaria del database secondario con replica geografica, anche questa di sola lettura. Poiché il database secondario con replica geografica ha un endpoint diverso rispetto al database `ApplicationIntent=ReadOnly`primario, in una cronologia dell'accesso al database secondario non era necessario impostare . Per garantire la compatibilità con le versioni precedenti, DMV `sys.geo_replication_links` mostra `secondary_allow_connections=2`, che indica che qualsiasi connessione client è consentita.

> [!NOTE]
> Round robin o qualsiasi altro routing con carico bilanciato tra le repliche locali del database secondario non è supportato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'offerta Hyperscale del database SQL, vedere [Livello di servizio Hyperscale](./sql-database-service-tier-hyperscale.md).
