---
title: Spostare un'area di lavoro di Log Analytics in Monitoraggio di Azure . Documenti Microsoft
description: Informazioni su come spostare l'area di lavoro di Log Analytics in un'altra sottoscrizione o gruppo di risorse.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659493"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Spostare un'area di lavoro di Log Analytics in una sottoscrizione o in un gruppo di risorse diversoMove a Log Analytics workspace to different subscription or resource group

In questo articolo verranno illustrati i passaggi per spostare l'area di lavoro di Log Analytics in un altro gruppo di risorse o sottoscrizione nella stessa area. Altre informazioni sullo spostamento delle risorse di Azure tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. in [Sposta le risorse in un nuovo gruppo](../../azure-resource-manager/management/move-resource-group-and-subscription.md)di risorse o in una nuova sottoscrizione . 

> [!IMPORTANT]
> Non è possibile spostare un'area di lavoro in un'area geografica diversa.

## <a name="verify-active-directory-tenant"></a>Verificare il tenant di Active Directory
Le sottoscrizioni di origine e di destinazione dell'area di lavoro devono esistere all'interno dello stesso tenant di Azure Active Directory.The workspace source and destination subscriptions must exist within the same Azure Active Directory tenant. Usare Azure PowerShell per verificare che entrambe le sottoscrizioni abbiano lo stesso ID tenant.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Considerazioni sullo spostamento dell'area di lavoro
Le soluzioni gestite installate nell'area di lavoro verranno spostate con l'operazione di spostamento dell'area di lavoro di Log Analytics.Managed solutions that are installed in the workspace will be moved with the Log Analytics workspace move operation. Gli agenti connessi rimarranno connessi e manterranno i dati di invio all'area di lavoro dopo lo spostamento. Poiché l'operazione di spostamento richiede che non vi sia alcun collegamento dall'area di lavoro a qualsiasi account di automazione, le soluzioni che si basano su tale collegamento devono essere rimosse.

Soluzioni che devono essere rimosse prima di poter scollegare l'account di automazione:

- Gestione degli aggiornamenti
- Rilevamento modifiche
- Avviare/arrestare VM durante gli orari di minore attività


### <a name="delete-in-azure-portal"></a>Eseguire l'eliminazione nel portale di Azure
Usare la procedura seguente per rimuovere le soluzioni usando il portale di Azure:Use the following procedure to remove the solutions using the Azure portal:

1. Aprire il menu per il gruppo di risorse in cui sono installate tutte le soluzioni.
2. Selezionare le soluzioni da rimuovere.
3. Fare clic su **Elimina risorse** e quindi confermare la rimozione delle risorse facendo clic su **Elimina**.

![Eliminare soluzioni](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Eliminare tramite PowerShellDelete using PowerShell

Per rimuovere le soluzioni tramite PowerShell, utilizzare il cmdlet Remove-AzResource come illustrato nell'esempio seguente:To remove the solutions using PowerShell, use the [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) cmdlet as shown in the following example:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Rimuovere le regole di avviso
Per la soluzione **Avvia/Arresta macchine virtuali,** è inoltre necessario rimuovere le regole di avviso create dalla soluzione. Usare la procedura seguente nel portale di Azure per rimuovere queste regole.

1. Aprire il menu **Monitoraggio** , quindi selezionare **Avvisi**.
2. Fare clic su **Gestisci regole di avviso.**
3. Selezionare le tre regole di avviso seguenti e quindi fare clic su **Elimina**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Eliminare regole](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Scollega account di automazione
Usare la procedura seguente per scollegare l'account di automazione dall'area di lavoro usando il portale di Azure:Use the following procedure to unlink the Automation account from the workspace using the Azure portal:

1. Apri il menu **Account di automazione** e quindi seleziona l'account da rimuovere.
2. Nella sezione **Risorse correlate** del menu selezionare Area di **lavoro collegata**. 
3. Fare clic su **Scollega area di lavoro** per scollegare l'area di lavoro dall'account di Automazione.

    ![Unlink workspace (Scollega area di lavoro)](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Spostare l'area di lavoro

### <a name="azure-portal"></a>Portale di Azure
Usare la procedura seguente per spostare l'area di lavoro usando il portale di Azure:Use the following procedure to move your workspace using the Azure portal:

1. Aprire il menu **Aree** di lavoro di Log Analytics e quindi selezionare l'area di lavoro.
2. Nella pagina **Panoramica** fare clic su **Cambia** accanto a **Gruppo** di risorse o **Sottoscrizione**.
3. Viene visualizzata una nuova pagina con un elenco di risorse correlate all'area di lavoro. Selezionare le risorse da spostare nella stessa sottoscrizione di destinazione e nello stesso gruppo di risorse dell'area di lavoro. 
4. Selezionare una **sottoscrizione** di destinazione e un **gruppo di risorse**. Se si sposta l'area di lavoro in un altro gruppo di risorse nella stessa sottoscrizione, l'opzione **Sottoscrizione** non verrà visualizzata.
5. Fare clic **su OK** per spostare l'area di lavoro e le risorse selezionate.

    ![Portale](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Per spostare l'area di lavoro tramite PowerShell, usare Move-AzResource come nell'esempio seguente:To move your workspace using PowerShell, use the [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) as in the following example:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Dopo l'operazione di spostamento, le soluzioni rimosse e il collegamento dell'account di automazione devono essere riconfigurati per riportare l'area di lavoro allo stato precedente.


## <a name="next-steps"></a>Passaggi successivi
- Per un elenco delle risorse che supportano lo spostamento, vedere [Move operation support for resources](../../azure-resource-manager/management/move-support-resources.md).
