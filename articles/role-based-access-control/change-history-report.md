---
title: Visualizzare i log attività per le modifiche RBAC di Azure
description: Visualizzare i log attività per le modifiche al controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per le risorse di Azure negli ultimi 90 giorni.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78161780"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Visualizzare i log attività per le modifiche RBAC di Azure

A volte sono necessarie informazioni sulle modifiche del controllo degli accessi in base al ruolo di Azure (RBAC di Azure), ad esempio per il controllo o la risoluzione dei problemi. Ogni volta che un utente apporta modifiche alle assegnazioni di ruolo o alle definizioni dei ruoli all'interno delle sottoscrizioni, le modifiche vengono registrate nel [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md). È possibile visualizzare i log attività per visualizzare tutte le modifiche apportate a RBAC di Azure negli ultimi 90 giorni.

## <a name="operations-that-are-logged"></a>Operazioni registrate

Di seguito sono riportate le operazioni correlate a RBAC di Azure registrate nel log attività:

- Crea assegnazione ruolo
- Elimina assegnazione ruolo
- Crea o aggiorna la definizione del ruolo personalizzata
- Elimina la definizione del ruolo personalizzata

## <a name="azure-portal"></a>Portale di Azure

Il modo più semplice per iniziare è visualizzare i log attività con il portale di Azure. Lo screenshot seguente mostra un esempio di operazioni di assegnazione di ruolo nel log attività. Include anche un'opzione per scaricare i log come file CSV.

![Log attività tramite il portale - screenshot](./media/change-history-report/activity-log-portal.png)

Il log attività nel portale include diversi filtri. Ecco i filtri correlati a RBAC di Azure:

| Filtro | valore |
| --------- | --------- |
| Categoria evento | <ul><li>Administrative</li></ul> |
| Operazione | <ul><li>Crea assegnazione ruolo</li><li>Elimina assegnazione ruolo</li><li>Crea o aggiorna la definizione del ruolo personalizzata</li><li>Elimina la definizione del ruolo personalizzata</li></ul> |

Per altre informazioni sui log attività, vedere [visualizzare i log attività per monitorare le azioni sulle risorse](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Per visualizzare i log attività con Azure PowerShell, usare il comando [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog).

Questo comando elenca tutte le modifiche relative alle assegnazioni di ruolo in una sottoscrizione per gli ultimi 7 giorni:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Questo comando elenca tutte le modifiche relative alle definizioni di ruolo in un gruppo di risorse per gli ultimi 7 giorni:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Questo comando elenca tutte le modifiche alle assegnazioni di ruolo e alle definizioni di ruolo in una sottoscrizione per gli ultimi 7 giorni e visualizza i risultati in un elenco:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per visualizzare i log attività usando l'interfaccia della riga di comando di Azure, usare il comando [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

Questo comando elenca i log attività in un gruppo di risorse del 27 febbraio, cercando in futuro sette giorni:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Questo comando elenca i log attività per il provider di risorse di autorizzazione dal 27 febbraio, cercando in futuro sette giorni:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

[Log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) è un altro strumento che è possibile usare per raccogliere e analizzare le modifiche RBAC di Azure per tutte le risorse di Azure. I log di monitoraggio di Azure offrono i vantaggi seguenti:

- Possibilità di scrivere query e logica complesse
- Integrazione con avvisi, Power BI e altri strumenti
- Salvataggio dei dati per periodi di conservazione più estesi
- Riferimenti incrociati con gli altri log, ad esempio relativi a sicurezza e macchine virtuali o personalizzati

Ecco i passaggi di base per iniziare:

1. [Creare un'area di lavoro log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Configurare la soluzione Analisi log attività](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) per la propria area di lavoro.

1. [Visualizzare i log attività](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Un modo rapido per accedere alla pagina Panoramica della soluzione Analisi log attività consiste nel fare clic sull'opzione **logs** .

   ![Opzione log di monitoraggio di Azure nel portale](./media/change-history-report/azure-log-analytics-option.png)

1. Facoltativamente, usare il [log Analytics di monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md) per eseguire una query e visualizzare i log. Per altre informazioni, vedere [Introduzione alle query di log di monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md).

Ecco una query che restituisce le nuove assegnazioni di ruolo organizzate in base al provider di risorse di destinazione:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Ecco una query che restituisce le modifiche alle assegnazioni di ruolo visualizzate in un grafico:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Log attività tramite il portale Advanced Analytics - screenshot](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare eventi nel log attività](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorare l'attività di sottoscrizione con il log attività di Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
