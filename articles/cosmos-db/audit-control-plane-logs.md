---
title: Come controllare le operazioni del piano di controllo Azure Cosmos DB
description: Informazioni su come controllare le operazioni del piano di controllo, ad esempio aggiungere un'area, aggiornare la velocità effettiva, il failover dell'area, aggiungere un VNet e così via in Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 08cc3b08611947ac32973b2dfb01060140dc0798
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743897"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Come controllare le operazioni del piano di controllo Azure Cosmos DB

Il piano di controllo in Azure Cosmos DB è un servizio RESTful che consente di eseguire un set diversificato di operazioni nell'account Azure Cosmos. Espone un modello di risorse pubblico, ad esempio database, account, e varie operazioni agli utenti finali per eseguire azioni sul modello di risorse. Le operazioni del piano di controllo includono le modifiche apportate all'account o al contenitore Azure Cosmos. Ad esempio, operazioni come la creazione di un account Azure Cosmos, l'aggiunta di un'area, la velocità effettiva degli aggiornamenti, il failover dell'area, l'aggiunta di un VNet e così via sono alcune delle operazioni del piano di controllo. Questo articolo illustra come controllare le operazioni del piano di controllo in Azure Cosmos DB. È possibile eseguire le operazioni del piano di controllo sugli account Azure Cosmos usando l'interfaccia della riga di comando di Azure, PowerShell o portale di Azure, mentre per i contenitori usare l'interfaccia della riga di comando di Azure o PowerShell.

Di seguito sono riportati alcuni scenari di esempio in cui sono utili le operazioni del piano di controllo di controllo:

* Si desidera ricevere un avviso quando vengono modificate le regole del firewall per l'account Azure Cosmos. L'avviso è necessario per individuare le modifiche non autorizzate alle regole che regolano la sicurezza di rete dell'account Azure Cosmos e intraprendere un'azione rapida.

* Si vuole ricevere un avviso se viene aggiunta o rimossa una nuova area dall'account Azure Cosmos. L'aggiunta o la rimozione di aree implica la fatturazione e i requisiti di sovranità dei dati. Questo avviso consente di rilevare un'aggiunta o una rimozione accidentale dell'area nell'account.

* Si desidera ottenere altri dettagli dai log di diagnostica su ciò che è stato modificato. Ad esempio, un VNet è stato modificato.

## <a name="disable-key-based-metadata-write-access"></a>Disabilitare l'accesso in scrittura ai metadati basati su chiave

Prima di controllare le operazioni del piano di controllo in Azure Cosmos DB, disabilitare l'accesso in scrittura ai metadati basati su chiave per l'account. Quando l'accesso in scrittura ai metadati basati su chiave è disabilitato, non è consentito l'accesso ai client che si connettono all'account Azure Cosmos tramite chiavi dell'account. È possibile disabilitare l'accesso in scrittura impostando la `disableKeyBasedMetadataWriteAccess` proprietà su true. Dopo aver impostato questa proprietà, le modifiche apportate a qualsiasi risorsa possono verificarsi da un utente con il ruolo di controllo degli accessi in base al ruolo (RBAC) e le credenziali appropriate. Per altre informazioni su come impostare questa proprietà, vedere l'articolo [Impedisci modifiche da SDK](role-based-access-control.md#prevent-sdk-changes) . 

Dopo l' `disableKeyBasedMetadataWriteAccess` Abilitazione di, se i client basati su SDK eseguono operazioni di creazione o aggiornamento, l'errore *"operazione" post "sulla risorsa" ContainerNameorDatabaseName "non è consentito tramite Azure Cosmos DB endpoint* viene restituito. È necessario attivare l'accesso a tali operazioni per l'account o eseguire le operazioni di creazione/aggiornamento tramite Azure Resource Manager, l'interfaccia della riga di comando di Azure o Azure PowerShell. Per tornare indietro, impostare disableKeyBasedMetadataWriteAccess su **false** usando l'interfaccia della riga di comando di Azure, come descritto nell'articolo [Impedisci modifiche da Cosmos SDK](role-based-access-control.md#prevent-sdk-changes) . Assicurarsi di modificare il valore di `disableKeyBasedMetadataWriteAccess` in false anziché true.

Quando si disattiva l'accesso in scrittura ai metadati, tenere presente quanto segue:

* Valutare e assicurarsi che le applicazioni non effettuino chiamate di metadati che modifichino le risorse precedenti (ad esempio, creare una raccolta, aggiornare la velocità effettiva,...) usando l'SDK o le chiavi dell'account.

* Attualmente, il portale di Azure usa le chiavi dell'account per le operazioni sui metadati e pertanto queste operazioni verranno bloccate. In alternativa, usare l'interfaccia della riga di comando di Azure, gli SDK o le distribuzioni del modello di Gestione risorse per eseguire tali operazioni.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Abilitare i log di diagnostica per le operazioni del piano di controllo

È possibile abilitare i log di diagnostica per le operazioni del piano di controllo usando il portale di Azure. Dopo l'abilitazione, i log di diagnostica registreranno l'operazione come coppia di eventi di avvio e di completamento con i dettagli pertinenti. Ad esempio, *RegionFailoverStart* e *RegionFailoverComplete* completano l'evento di failover dell'area.

Per abilitare la registrazione sulle operazioni del piano di controllo, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'account Azure Cosmos.

1. Aprire il riquadro **impostazioni di diagnostica** e specificare un **nome** per i log da creare.

1. Selezionare **ControlPlaneRequests** per tipo di log e selezionare l'opzione **Invia a log Analytics** .

È anche possibile archiviare i log in un account di archiviazione o in un flusso in un hub eventi. Questo articolo illustra come inviare i log a log Analytics ed eseguire query su di essi. Dopo l'abilitazione, per rendere effettive le registrazioni di diagnostica sono necessari alcuni minuti. È possibile tenere traccia di tutte le operazioni del piano di controllo eseguite dopo tale punto. Lo screenshot seguente mostra come abilitare i log del piano di controllo:

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Abilitare la registrazione delle richieste del piano di controllo":::

## <a name="view-the-control-plane-operations"></a>Visualizzare le operazioni del piano di controllo

Dopo l'abilitazione della registrazione, attenersi alla procedura seguente per tenere traccia delle operazioni per un account specifico:

1. Accedere [portale di Azure](https://portal.azure.com).

1. Aprire la scheda **monitoraggio** dal riquadro di spostamento a sinistra e quindi selezionare il riquadro **log** . Viene aperta un'interfaccia utente in cui è possibile eseguire facilmente query con tale account specifico nell'ambito. Eseguire la query seguente per visualizzare i log del piano di controllo:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Gli screenshot seguenti acquisiscono i log quando viene modificato un livello di coerenza per un account Azure Cosmos:

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="Abilitare la registrazione delle richieste del piano di controllo":::

Le schermate seguenti acquisiscono i log quando viene creato lo spazio di Portatasto o una tabella di un account Cassandra e quando viene aggiornata la velocità effettiva. I registri del piano di controllo per le operazioni di creazione e aggiornamento nel database e il contenitore vengono registrati separatamente, come illustrato nello screenshot seguente:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Abilitare la registrazione delle richieste del piano di controllo":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identificare l'identità associata a un'operazione specifica

Se si desidera eseguire ulteriormente il debug, è possibile identificare un'operazione specifica nel **log attività** utilizzando l'ID attività o il timestamp dell'operazione. Timestamp viene utilizzato per alcuni Gestione risorse client in cui l'ID attività non viene passato in modo esplicito. Il log attività fornisce informazioni dettagliate sull'identità con la quale è stata avviata l'operazione. Lo screenshot seguente mostra come usare l'ID attività e individuare le operazioni associate nel log attività:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="Abilitare la registrazione delle richieste del piano di controllo":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Operazioni del piano di controllo per l'account Azure Cosmos

Di seguito sono riportate le operazioni del piano di controllo disponibili a livello di account. La maggior parte delle operazioni viene rilevata a livello di account. Queste operazioni sono disponibili come metriche in monitoraggio di Azure:

* Area aggiunta
* Area rimossa
* Account eliminato
* Area sottoposta a failover
* Account creato
* Rete virtuale eliminata
* Impostazioni di rete account aggiornate
* Impostazioni di replica dell'account aggiornate
* Chiavi dell'account aggiornate
* Impostazioni di backup dell'account aggiornate
* Impostazioni di diagnostica dell'account aggiornate

## <a name="control-plane-operations-for-database-or-containers"></a>Operazioni del piano di controllo per database o contenitori

Di seguito sono riportate le operazioni del piano di controllo disponibili a livello di database e di contenitore. Queste operazioni sono disponibili come metriche in monitoraggio di Azure:

* Database SQL creato
* Database SQL aggiornato
* Velocità effettiva del database SQL aggiornata
* Database SQL eliminato
* Contenitore SQL creato
* Contenitore SQL aggiornato
* Velocità effettiva del contenitore SQL aggiornata
* Contenitore SQL eliminato
* Spazio di portadi Cassandra creato
* Spazio del tasto Cassandra aggiornato
* Velocità effettiva del tasto di Cassandra aggiornata
* Spazio di portadi Cassandra eliminato
* Tabella Cassandra creata
* Tabella Cassandra aggiornata
* Velocità effettiva della tabella Cassandra aggiornata
* Tabella Cassandra eliminata
* Database Gremlin creato
* Database Gremlin aggiornato
* Velocità effettiva del database Gremlin aggiornata
* Database Gremlin eliminato
* Grafo Gremlin creato
* Grafico Gremlin aggiornato
* Velocità effettiva del grafo Gremlin aggiornata
* Grafo Gremlin eliminato
* Database Mongo creato
* Database Mongo aggiornato
* Velocità effettiva del database Mongo aggiornata
* Database Mongo eliminato
* Raccolta Mongo creata
* Raccolta Mongo aggiornata
* Velocità effettiva raccolta Mongo aggiornata
* Raccolta Mongo eliminata
* Tabella AzureTable creata
* Tabella AzureTable aggiornata
* Velocità effettiva della tabella AzureTable aggiornata
* Tabella AzureTable eliminata

## <a name="diagnostic-log-operations"></a>Operazioni del log di diagnostica

Di seguito sono riportati i nomi di operazione nei log di diagnostica per operazioni diverse:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Per le operazioni specifiche dell'API, l'operazione viene denominata con il formato seguente:

* Tipologia API + ApiKindResourceType + OperationType
* Tipologia API + ApiKindResourceType + "velocità effettiva" + operationType

**Esempio** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

La proprietà *ResourceDetails* contiene l'intero corpo della risorsa come payload della richiesta e contiene tutte le proprietà richieste per l'aggiornamento

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Query del log di diagnostica per le operazioni del piano di controllo

Di seguito sono riportati alcuni esempi per ottenere i log di diagnostica per le operazioni del piano di controllo:

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersThroughputUpdate"
```

Eseguire una query per ottenere l'activityId e il chiamante che ha avviato l'operazione di eliminazione del contenitore:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

Eseguire una query per ottenere gli aggiornamenti di indice o TTL. È quindi possibile confrontare l'output di questa query con un aggiornamento precedente per visualizzare la modifica nell'indice o nella durata (TTL).

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**output**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare Monitoraggio di Azure per Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md)
