---
title: Raccogliere il log attività di Azure con le impostazioni di diagnostica - Monitoraggio di Azure Documenti Microsoft
description: Usare le impostazioni di diagnostica per inoltrare i log attività di Azure ai log di monitoraggio di Azure, all'archiviazione di Azure o agli hub eventi di Azure.Use diagnostic settings to forward Azure Activity logs to Azure Monitor Logs, Azure storage, or Azure Event Hubs.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096902"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Aggiornamento alla raccolta e all'esportazione del log attività di AzureUpdate to Azure Activity log collection and export
Il log attività di Azure è un log della piattaforma che fornisce informazioni dettagliate sugli eventi a livello di sottoscrizione che si sono verificati in Azure.The [Azure Activity log](platform-logs-overview.md) is a [platform log](platform-logs-overview.md) that provides insight into subscription-level events that have occurred in Azure. Il metodo per inviare le voci del log attività a [un hub eventi o](activity-log-export.md) a un account di archiviazione oppure a [un'area](activity-log-collect.md) di lavoro di Log Analytics è stato modificato per usare [le impostazioni di diagnostica.](diagnostic-settings.md) In questo articolo viene descritta la differenza tra i metodi e come cancellare le impostazioni legacy in preparazione per passare alle impostazioni di diagnostica.


## <a name="differences-between-methods"></a>Differenze tra i metodi

### <a name="advantages"></a>Vantaggi
L'utilizzo delle impostazioni di diagnostica presenta i vantaggi seguenti rispetto ai metodi correnti:Using diagnostic settings has the following advantages over the current methods:

- Metodo coerente per la raccolta di tutti i log della piattaforma.
- Raccogliere il log attività tra più sottoscrizioni e tenant.
- Raccolta filtri per raccogliere i log solo per determinate categorie.
- Raccogliere tutte le categorie del log attività. Alcune categorie non vengono raccolte utilizzando il metodo legacy.
- Latenza più rapida per l'inserimento del log. Il metodo precedente ha una latenza di circa 15 minuti, mentre le impostazioni di diagnostica aggiungono solo circa 1 minuto.

### <a name="considerations"></a>Considerazioni
Considerare i dettagli seguenti della raccolta di log attività usando le impostazioni di diagnostica prima di abilitare questa funzionalità.

- L'impostazione di conservazione per la raccolta del log attività nell'archiviazione di Azure è stata rimossa, il che significa che i dati verranno archiviati per un tempo indefinito fino a quando non vengono rimossi.
- Attualmente, è possibile creare solo un'impostazione di diagnostica a livello di sottoscrizione usando il portale di Azure.Currently, you can only create a subscription level diagnostic setting using the Azure portal. Per usare altri metodi, ad esempio PowerShell o l'interfaccia della riga di comando, è possibile creare un modello di Resource Manager.To use other methods such as PowerShell or CLI, you can create a Resource Manager template.


### <a name="differences-in-data"></a>Differenze nei dati
Le impostazioni di diagnostica raccolgono gli stessi dati dei metodi precedenti utilizzati per raccogliere il log attività con le differenze correnti seguenti:

Le colonne seguenti sono state rimosse. La sostituzione di queste colonne è in un formato diverso, pertanto potrebbe essere necessario modificare le query di log che le utilizzano. È possibile che le colonne rimosse siano ancora visualizzate nello schema, ma non verranno popolate con i dati.

| Colonna rimossa | Colonna di sostituzione |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus (Substato attività) | ActivitySubstatusValue |
| OperationName     | Valore NomeOperazione     |
| ResourceProvider  | ResourceProviderValue  |

È stata aggiunta la seguente colonna:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> In alcuni casi, i valori in queste colonne possono essere tutti in maiuscolo. Se si dispone di una query che include queste colonne, è necessario utilizzare [l'operatore di tipo ,](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) per eseguire un confronto senza distinzione tra maiuscole e minuscole.

## <a name="work-with-legacy-settings"></a>Utilizzare le impostazioni legacy
Le impostazioni legacy per la raccolta del log attività continueranno a funzionare se non si sceglie di sostituire con un'impostazione di diagnostica. Utilizzare il metodo seguente per gestire il profilo di log per una sottoscrizione.

1. Dal menu **Monitoraggio di Azure** nel portale di Azure selezionare Log **attività**.
3. Fare clic su **Impostazioni di diagnostica**.

   ![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Fai clic sul banner viola per l'esperienza legacy.

    ![Esperienza legacy](media/diagnostic-settings-subscription/legacy-experience.png)


Vedere gli articoli seguenti per informazioni dettagliate sull'utilizzo dei metodi di raccolta legacy.

- [Raccogliere e analizzare i log delle attività di Azure nell'area di lavoro Log Analytics in Monitoraggio di AzureCollect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor](activity-log-collect.md)
- [Raccogliere i log delle attività di Azure in Monitoraggio di Azure tra tenant di Azure Active DirectoryCollect Azure Activity logs into Azure Monitor across Azure Active Directory tenants](activity-log-collect-tenants.md)
- [Esportare il log attività di Azure nell'archiviazione o nelle hub eventi di AzureExport Azure Activity log to storage or Azure Event Hubs](activity-log-export.md)

## <a name="disable-existing-settings"></a>Disattivare le impostazioni esistenti
È necessario disabilitare la raccolta esistente dell'attività prima di abilitarla usando le impostazioni di diagnostica. Avendo entrambi abilitati può provocare dati duplicati.

### <a name="disable-collection-into-log-analytics-workspace"></a>Disabilitare la raccolta nell'area di lavoro di Log AnalyticsDisable collection into Log Analytics workspace

1. Aprire il menu **Delle aree di lavoro** di Log Analytics nel portale di Azure e selezionare l'area di lavoro per raccogliere il log attività.
2. Nella sezione **Origini dati area di lavoro** del menu dell'area di lavoro selezionare Log attività di **Azure.**
3. Fare clic sull'abbonamento da disconnettere.
4. Fare clic su **Disconnetti** e quindi **su Sì** quando viene richiesto di confermare la scelta.

### <a name="disable-log-profile"></a>Disabilitare il profilo di registro

1. Utilizzare la procedura descritta in [Utilizzare le impostazioni legacy](#work-with-legacy-settings) per aprire le impostazioni legacy.
2. Disabilitare qualsiasi raccolta corrente in hub di archiviazione o eventi.



## <a name="activity-log-monitoring-solution"></a>Soluzione di monitoraggio del log attività
La soluzione di monitoraggio di Azure Log Analytics include più query di log e visualizzazioni per l'analisi dei record del log attività nell'area di lavoro di Log Analytics.The Azure Log Analytics monitoring solution includes multiple log queries and views for analyzing the Activity Log records in your Log Analytics workspace. Questa soluzione usa i dati di log raccolti in un'area di lavoro di Log Analytics e continuerà a funzionare senza modifiche se raccogli il log attività usando le impostazioni di diagnostica. Per informazioni dettagliate su questa soluzione, vedere Soluzione di [monitoraggio di Activity Logs Analytics.See Activity Logs Analytics monitoring solution](activity-log-collect.md#activity-logs-analytics-monitoring-solution) for details on this solution.

## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sul Log attività](../../azure-resource-manager/management/view-activity-logs.md)
* [Ulteriori informazioni sulle impostazioni di diagnostica](diagnostic-settings.md)
