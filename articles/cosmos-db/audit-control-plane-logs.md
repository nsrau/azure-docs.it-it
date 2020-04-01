---
title: Come controllare le operazioni del piano di controllo di Azure Cosmos DBHow to audit Azure Cosmos DB control plane operations
description: Informazioni su come controllare le operazioni del piano di controllo, ad esempio aggiungere un'area, aggiornare la velocità effettiva, il failover dell'area, aggiungere una rete virtuale e così via in Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420264"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Come controllare le operazioni del piano di controllo di Azure Cosmos DBHow to audit Azure Cosmos DB control plane operations

Le operazioni del piano di controllo includono modifiche all'account o al contenitore Cosmos di Azure.Control plane operations include changes to the Azure Cosmos account or container. Ad esempio, creare un account Azure Cosmos, aggiungere un'area, aggiornare la velocità effettiva, il failover dell'area, aggiungere una rete virtuale e così via sono alcune delle operazioni del piano di controllo. Questo articolo illustra come controllare le operazioni del piano di controllo in Azure Cosmos DB.

## <a name="disable-key-based-metadata-write-access"></a>Disabilitare l'accesso in scrittura ai metadati basati su chiaveDisable key based metadata write
 
Prima di controllare le operazioni del piano di controllo in Azure Cosmos DB, disabilitare l'accesso in scrittura dei metadati basato su chiave nell'account. Quando l'accesso in scrittura ai metadati basati su chiave è disabilitato, ai client che si connettono all'account Cosmos di Azure tramite le chiavi dell'account viene impedito l'accesso all'account. È possibile disabilitare l'accesso in scrittura impostando la `disableKeyBasedMetadataWriteAccess` proprietà su true. Dopo aver impostato questa proprietà, le modifiche a qualsiasi risorsa possono verificarsi da un utente con il ruolo di controllo di accesso basato sui ruoli (RBAC) appropriato solo le credenziali. Per altre informazioni su come impostare questa proprietà, vedere l'articolo [Impedire le modifiche da SDK.](role-based-access-control.md#preventing-changes-from-cosmos-sdk)

 Quando si disattiva l'accesso in scrittura ai metadati, considerare i punti seguenti:Consider the following points when off the metadata write access:

* Valutare e assicurarsi che le applicazioni non esecano chiamate ai metadati che modificano le risorse precedenti (ad esempio, creare una raccolta, aggiornare la velocità effettiva, ...) usando le chiavi SDK o account.

* Attualmente, il portale di Azure usa le chiavi dell'account per le operazioni sui metadati e pertanto queste operazioni verranno bloccate. In alternativa, usare l'interfaccia della riga di comando di Azure, gli SDK o le distribuzioni dei modelli di Resource Manager per eseguire tali operazioni.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Abilitare i registri diagnostici per le operazioni del piano di controlloEnable diagnostic logs for control plane operations

È possibile abilitare i log di diagnostica per le operazioni del piano di controllo usando il portale di Azure.You can enable diagnostic logs for control plane operations by using the Azure portal. Utilizzare la procedura seguente per abilitare la registrazione sulle operazioni del piano di controllo:

1. Accedere al portale di Azure e passare all'account Cosmos di Azure.Sign into [Azure portal](https://portal.azure.com) and navigate to your Azure Cosmos account.

1. Aprire il riquadro **Impostazioni di diagnostica,** specificare un **nome** per i log da creare.

1. Selezionare ControlPlaneRequests per il tipo di registro e selezionare l'opzione **Invia a Log Analytics.Select** **ControlPlaneRequests** for log type and select the Send to Log Analytics option.

È anche possibile archiviare i log in un account di archiviazione o in un flusso in un hub eventi. In questo articolo viene illustrato come inviare i log per registrare l'analisi e quindi eseguire query su di essi. Dopo l'abilitazione, sono necessari alcuni minuti per attivare i log di diagnostica. Tutte le operazioni del piano di controllo eseguite dopo tale punto possono essere monitorate. Nella schermata seguente viene illustrato come abilitare i registri piano di controllo:The following screenshot shows how to enable control plane logs:

![Abilitare la registrazione delle richieste del piano di controlloEnable control plane requests logging](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Visualizzare le operazioni del piano di controllo

Dopo aver attivato la registrazione, utilizzare la procedura seguente per tenere traccia delle operazioni per un account specifico:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Aprire la scheda **Monitoraggio** nel riquadro di spostamento a sinistra, quindi selezionare il riquadro **Registri.** Si apre un'interfaccia utente in cui è possibile eseguire facilmente query con tale account specifico nell'ambito. Eseguire la query seguente per visualizzare i registri piano di controllo:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Le schermate seguenti acquisiscono i log quando una rete virtuale viene aggiunta a un account Azure Cosmos:The following screenshots capture logs when a VNET is added to an Azure Cosmos account:

![Controllare i registri aerei quando viene aggiunta una rete virtualeControl plane logs when a VNet is added](./media/audit-control-plane-logs/add-ip-filter-logs.png)

Le schermate seguenti acquisiscono i log quando la velocità effettiva di una tabella di Cassandra viene aggiornata:The following screenshots capture logs when throughput of a Cassandra table is updated:

![Controllare i registri piano quando viene aggiornata la velocità effettivaControl plane logs when throughput is updated](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identificare l'identità associata a un'operazione specifica

Se si desidera eseguire ulteriormente il debug, è possibile identificare un'operazione specifica nel **log attività** utilizzando l'ID attività o il timestamp dell'operazione. Timestamp viene utilizzato per alcuni client di Resource Manager in cui l'ID attività non viene passato in modo esplicito. Il log attività fornisce dettagli sull'identità con cui è stata avviata l'operazione. La schermata seguente mostra come usare l'ID attività e trovare le operazioni ad esso associate nel log attività:

![Utilizzare l'ID attività e trovare le operazioni](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>Passaggi successivi

* [Esplora Monitoraggio di Azure per il database Cosmos di AzureExplore Azure Monitor for Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md)