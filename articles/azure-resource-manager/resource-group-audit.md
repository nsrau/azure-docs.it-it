---
title: Visualizzare i log attività di Azure per monitorare le risorse | Documentazione Microsoft
description: Usare i log attività per esaminare le azioni degli utenti e gli errori. Mostra portale di Azure PowerShell, CLI di Azure e REST.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 7ff45be4eea5c6e8ab83093847164ede0e94579a
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606622"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Visualizzare i log attività per monitorare le azioni sulle risorse

Con i log attività è possibile determinare:

* le operazioni eseguite sulle risorse nella sottoscrizione
* chi ha avviato l'operazione
* quando si è verificata l'operazione;
* lo stato dell'operazione;
* i valori delle altre proprietà che potrebbero essere utili per esaminare l'operazione.

Il log attività contiene tutte le operazioni di scrittura (PUT, POST, DELETE) per le risorse. Non include le operazioni di lettura (GET). Per un elenco azioni risorsa, vedere [Operazioni di provider di risorse con Azure Resource Manager](../role-based-access-control/resource-provider-operations.md). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

I log attività vengono conservati per 90 giorni. È possibile eseguire query per qualsiasi intervallo di date, purché la data di inizio non risalga a più di 90 giorni prima.

È possibile recuperare le informazioni dai log attività tramite il portale, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Insights o la [libreria .NET di Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Portale di Azure

1. Per visualizzare i log attività dal portale, selezionare **Monitoraggio**.

    ![Selezionare monitoraggio](./media/resource-group-audit/select-monitor.png)

1. Selezionare **Log attività**.

    ![Selezionare il log attività](./media/resource-group-audit/select-activity-log.png)

1. Viene visualizzato un riepilogo delle operazioni recenti. Un set predefinito di filtri viene applicato alle operazioni. Si noti che le informazioni del riepilogo include chi ha avviato l'azione e quando è stato eseguito.

    ![Visualizzare riepilogo delle operazioni recenti](./media/resource-group-audit/audit-summary.png)

1. Per eseguire rapidamente un set predefinito di filtri, selezionare **Quick Insights**.

    ![Seleziona informazioni rapide](./media/resource-group-audit/select-quick-insights.png)

1. Selezionare una delle opzioni disponibili. Ad esempio, selezionare **distribuzioni non riuscite** per visualizzare gli errori da distribuzioni.

    ![Selezionare distribuzioni non riuscite](./media/resource-group-audit/select-failed-deployments.png)

1. Si noti che i filtri sono stati modificati per concentrarsi sugli errori di distribuzione nelle ultime 24 ore. Vengono visualizzate solo le operazioni che corrispondono ai filtri.

    ![Visualizza filtri](./media/resource-group-audit/view-filters.png)

1. Per concentrare l'attenzione su operazioni specifiche, modificare i filtri o applicarne di nuovi. Ad esempio, l'immagine seguente mostra un nuovo valore per l'**intervallo di tempo** e il **tipo di risorsa** è impostato su account di archiviazione. 

    ![Impostare le opzioni di filtro](./media/resource-group-audit/set-filter.png)

1. Per eseguire nuovamente la query in un secondo momento, selezionare **Aggiungi filtri correnti**.

    ![Aggiungere filtri](./media/resource-group-audit/pin-filters.png)

1. Assegnare un nome al filtro.

    ![Assegnare un nome ai filtri](./media/resource-group-audit/name-filters.png)

1. Il filtro è disponibile nel dashboard.

    ![Mostrare il filtro nel dashboard](./media/resource-group-audit/show-dashboard.png)

1. Dal portale, è possibile visualizzare le modifiche apportate a una risorsa. Passare il valore predefinito consente di visualizzare in Monitoraggio e selezionare un'operazione che per la modifica di una risorsa.

    ![Seleziona operazione](./media/resource-group-audit/select-operation.png)

1. Selezionare **cronologia revisioni (anteprima)** e selezionare una delle operazioni disponibili.

    ![Selezionare la cronologia delle modifiche](./media/resource-group-audit/select-change-history.png)

1. Vengono visualizzate le modifiche nella risorsa.

    ![Visualizzare le modifiche](./media/resource-group-audit/show-changes.png)

Per altre informazioni sulla cronologia delle modifiche, vedere [ottenere le modifiche alle risorse](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per recuperare le voci di log, eseguire il comando **Get-AzLog**. Offrire parametri aggiuntivi per filtrare l'elenco di voci. Se non si specifica un'ora di inizio e fine, vengono restituite le voci per gli ultimi 7 giorni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

L'esempio seguente illustra come usare il log attività per cercare le operazioni eseguite durante un intervallo di tempo specificato. Le date di inizio e fine vengono specificate in un formato Data.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

In alternativa, è possibile usare funzioni data per specificare l'intervallo di date, ad esempio gli ultimi 14 giorni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

È possibile cercare le azioni eseguite da un utente specifico.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

È possibile filtrare le operazioni non riuscite.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

È possibile concentrarsi su un errore esaminando il messaggio di stato per tale voce.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

È possibile selezionare valori specifici per limitare i dati restituiti.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

A seconda dell'ora di inizio specificata, il comando precedente può restituire un lungo elenco di operazioni per il gruppo di risorse. I risultati possono essere filtrati in base all'elemento da cercare specificando i criteri di ricerca. Ad esempio, è possibile filtrare per tipo di operazione.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

È possibile usare risorse grafico per visualizzare la cronologia delle modifiche per una risorsa. Per altre informazioni, vedere [ottenere le modifiche alle risorse](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per recuperare le voci di log, eseguire il comando [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) con una differenza per indicare l'intervallo di tempo.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

L'esempio seguente illustra come usare il log attività per cercare le operazioni eseguite durante un intervallo di tempo specificato. Le date di inizio e fine vengono specificate in un formato Data.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

È possibile cercare le azioni eseguite da un utente specifico, anche per un gruppo di risorse che non esiste più.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

È possibile filtrare le operazioni non riuscite.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

È possibile concentrarsi su un errore esaminando il messaggio di stato per tale voce.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

È possibile selezionare valori specifici per limitare i dati restituiti.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

A seconda dell'ora di inizio specificata, il comando precedente può restituire un lungo elenco di operazioni per il gruppo di risorse. I risultati possono essere filtrati in base all'elemento da cercare specificando i criteri di ricerca. Ad esempio, è possibile filtrare per tipo di operazione.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

È possibile usare risorse grafico per visualizzare la cronologia delle modifiche per una risorsa. Per altre informazioni, vedere [ottenere le modifiche alle risorse](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>API REST

Le operazioni REST per l'uso del log attività fanno parte delle [Informazioni di riferimento sulle API REST di Azure Insights](/rest/api/monitor/). Per recuperare gli eventi del log attività, vedere [Elencare gli eventi di gestione in una sottoscrizione](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Passaggi successivi

* I log attività di Azure possono essere usati con Power BI per ottenere altre informazioni sulle azioni nella sottoscrizione. Vedere [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)(Visualizzare e analizzare i log attività di Azure in Power BI e altri strumenti).
* Per informazioni su come impostare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo in Azure](../role-based-access-control/role-assignments-portal.md).
* Per informazioni sui comandi per visualizzare le operazioni di distribuzione, vedere [Visualizzare le operazioni di distribuzione](resource-manager-deployment-operations.md).
* Per informazioni su come impedire operazioni di eliminazione su una risorsa per tutti gli utenti, vedere [Bloccare le risorse con Azure Resource Manager](resource-group-lock-resources.md).
* Per visualizzare l'elenco delle operazioni disponibili per ogni provider Microsoft Azure Resource Manager, vedere [Operazioni di provider di risorse con Azure Resource Manager](../role-based-access-control/resource-provider-operations.md)
