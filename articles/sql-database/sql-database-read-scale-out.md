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
ms.date: 01/25/2019
ms.openlocfilehash: cf32f3998e254e8f4a9c347980718dbc8d0b13c4
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461645"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Usare le repliche di sola lettura per bilanciare il carico dei carichi di lavoro di query di sola lettura (anteprima)

La **scalabilità in lettura** consente di bilanciare il carico dei carichi di lavoro di sola lettura del database SQL di Azure usando la funzionalità di un'unica replica di sola lettura.

Di ogni database nel livello Premium ([modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md)) o nel livello Business Critical ([modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md)) viene effettuato il provisioning automatico con diverse repliche AlwaysOn per supportare il contratto di servizio per la disponibilità.

![Repliche di sola lettura](media/sql-database-managed-instance/business-critical-service-tier.png)

Il provisioning di queste repliche viene effettuato con la stessa dimensione di calcolo della replica di lettura/scrittura usata dalle normali connessioni di database. La funzionalità di **scalabilità in lettura** consente di bilanciare il carico dei carichi di lavoro di sola lettura del database SQL usando la capacità di una delle repliche di sola lettura invece di condividere la replica di lettura/scrittura. In questo modo i carichi di lavoro di sola lettura verranno isolati dal carico di lavoro principale di lettura/scrittura e non ne comprometteranno le prestazioni. La funzionalità è destinata alle applicazioni che includono carichi di lavoro di sola lettura separati in modo logico, ad esempio dati di analisi, che quindi potrebbero ottenere vantaggi di prestazioni usando tale capacità senza costi aggiuntivi.

Per usare la funzionalità di scalabilità in lettura con un determinato database, è necessario abilitarla in modo esplicito quando si crea il database oppure in seguito modificandone la configurazione con PowerShell richiamando i cmdlet [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) o [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) oppure tramite l'API REST di Azure Resource Manager con il metodo [Create o Update per i database](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

Dopo che la scalabilità in lettura è stata abilitata per un database, le applicazioni che si connettono a tale database verranno indirizzate alla replica di lettura/scrittura o a una replica di sola lettura di tale database a seconda della proprietà `ApplicationIntent` configurata nella stringa di connessione dell'applicazione. Per informazioni sulla proprietà `ApplicationIntent`, vedere [Specifying Application Intent](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent) (Specifica della finalità dell'applicazione).

Se la scalabilità in lettura è disabilitata o è stata impostata la proprietà ReadScale in un livello di servizio non supportato, tutte le connessioni vengono indirizzate alla replica in lettura-scrittura, indipendentemente dalla proprietà `ApplicationIntent`.

> [!NOTE]
> Durante l'anteprima, Query Data Store ed Eventi estesi non sono supportati nelle repliche di sola lettura.

## <a name="data-consistency"></a>Coerenza dei dati

Uno dei vantaggi delle repliche è che sono sempre in uno stato coerente a livello di transazione. Per temporizzazioni diverse potrebbe però verificarsi una breve latenza tra le diverse repliche. La scalabilità in lettura supporta la coerenza a livello di sessione. Se quindi la sessione di sola lettura si riconnette dopo un errore di connessione causato dalla mancata disponibilità della replica, può essere reindirizzata a una replica non aggiornata al 100% con la replica di lettura/scrittura. Analogamente, se un'applicazione scrive i dati usando una sessione di lettura/scrittura e li legge immediatamente usando una sessione di sola lettura, gli aggiornamenti più recenti potrebbero non essere immediatamente visibili. La fase di rollforward del log delle transazioni per le repliche è infatti asincrona.

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

## <a name="enable-and-disable-read-scale-out"></a>Abilitare e disabilitare la scalabilità in lettura

La scalabilità in lettura è abilitata per impostazione predefinita nel livello Business Critical di [Istanza gestita](sql-database-managed-instance.md). Deve essere abilitata in modo esplicito nei livelli Premium e Business Critical del [database disponibile nel server di database SQL](sql-database-servers.md). I metodi per l'abilitazione e la disabilitazione della scalabilità in lettura sono illustrati di seguito.

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell: Abilitare e disabilitare la scalabilità in lettura

Per gestire la scalabilità in lettura in Azure PowerShell, è necessaria la versione di Azure PowerShell di dicembre 2016 o una successiva. Per la versione più recente di PowerShell, vedere [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Abilitare o disabilitare la scalabilità in lettura in Azure PowerShell richiamando il cmdlet [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) e passando il valore desiderato (`Enabled` o `Disabled`) per il parametro `-ReadScale`. In alternativa, è possibile usare il cmdlet [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) per creare un nuovo database con la scalabilità in lettura abilitata.

Ad esempio, per abilitare la scalabilità in lettura per un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Per disabilitare la scalabilità in lettura per un database esistente (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Per creare un nuovo database con la scalabilità in lettura abilitata (sostituendo gli elementi nelle parentesi acute con i valori corretti per l'ambiente ed eliminando le parentesi acute):

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
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

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Uso della scalabilità in lettura con database con replica geografica

Se si usa la scalabilità in lettura per bilanciare il carico dei carichi di lavoro di sola lettura in un database con replica geografica, ad esempio come un membro di un gruppo di failover, assicurarsi che la scalabilità in lettura sia abilitata per i database primari e secondari con replica geografica. In questo modo si garantisce lo stesso effetto di bilanciamento del carico quando l'applicazione si connette al nuovo database primario dopo il failover. Se ci si connette al database secondario con replica geografica con scalabilità in lettura abilitata, le sessioni con `ApplicationIntent=ReadOnly` saranno indirizzate a una delle repliche nello stesso modo in cui le connessioni sono state indirizzate al database primario.  Le sessioni senza `ApplicationIntent=ReadOnly` verranno indirizzate alla replica primaria del database secondario con replica geografica, anche questa di sola lettura. Poiché il database secondario con replica geografica dispone di un endpoint diverso da quello del database primario, in passato per accedere al database secondario non era necessario impostare `ApplicationIntent=ReadOnly`. Per garantire la compatibilità con le versioni precedenti, DMV `sys.geo_replication_links` mostra `secondary_allow_connections=2`, che indica che qualsiasi connessione client è consentita.

> [!NOTE]
> Durante l'anteprima, round robin o altri routing con bilanciamento del carico tra le repliche locali del database secondario non sono supportati.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'uso di PowerShell per impostare la scalabilità in lettura, vedere i cmdlet [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) o [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).
- Per informazioni sull'uso dell'API REST per impostare la scalabilità in lettura, vedere [Databases - Create or Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) (Database - Creare o aggiornare).
