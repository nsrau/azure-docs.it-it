---
title: Spostare un'area di lavoro Log Analytics in monitoraggio di Azure | Microsoft Docs
description: Informazioni su come spostare l'area di lavoro di Log Analytics in un'altra sottoscrizione o in un altro gruppo di risorse.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: f6e1af2fdf43eb4351e996297f7dba775b7ffcef
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278801"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Spostare un'area di lavoro Log Analytics in una sottoscrizione o in un gruppo di risorse diverso

Questo articolo illustra i passaggi per spostare Log Analytics area di lavoro in un altro gruppo di risorse o sottoscrizione nella stessa area. Per altre informazioni sullo scorrimento delle risorse di Azure, è possibile usare la portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. in [spostare le risorse in un nuovo gruppo di risorse o una nuova sottoscrizione](../../azure-resource-manager/resource-group-move-resources.md). 

> [!IMPORTANT]
> Non è possibile spostare un'area di lavoro in un'area diversa.

## <a name="verify-active-directory-tenant"></a>Verificare Active Directory tenant
Le sottoscrizioni di origine e di destinazione dell'area di lavoro devono esistere nello stesso tenant Azure Active Directory. Usare Azure PowerShell per verificare che entrambe le sottoscrizioni abbiano lo stesso ID tenant.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Considerazioni sullo spostamento dell'area di lavoro
Le soluzioni gestite installate nell'area di lavoro verranno spostate con l'operazione di spostamento dell'area di lavoro Log Analytics. Gli agenti connessi rimarranno connessi e continueranno a inviare i dati all'area di lavoro dopo lo spostamento. Poiché l'operazione di spostamento richiede che non vi sia alcun collegamento dall'area di lavoro a un account di automazione, è necessario rimuovere le soluzioni che si basano su tale collegamento.

Soluzioni che devono essere rimosse prima di poter scollegare l'account di automazione:

- Gestione degli aggiornamenti
- Change Tracking
- Avviare/arrestare VM durante gli orari di minore attività


### <a name="delete-in-azure-portal"></a>Eseguire l'eliminazione nel portale di Azure
Usare la procedura seguente per rimuovere le soluzioni usando il portale di Azure:

1. Aprire il menu per il gruppo di risorse in cui sono installate le soluzioni.
2. Selezionare le soluzioni da rimuovere.
3. Fare clic su **Elimina risorse** e confermare le risorse da rimuovere facendo clic su **Elimina**.

![Elimina soluzioni](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Eliminare con PowerShell

Per rimuovere le soluzioni usando PowerShell, usare il cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) , come illustrato nell'esempio seguente:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Rimuovi regole di avviso
Per la soluzione di **avvio/arresto di macchine virtuali** , è necessario rimuovere anche le regole di avviso create dalla soluzione. Utilizzare la procedura seguente nel portale di Azure per rimuovere queste regole.

1. Aprire il menu **monitoraggio** , quindi selezionare **avvisi**.
2. Fare clic su **Gestisci regole di avviso**.
3. Selezionare le seguenti tre regole di avviso e quindi fare clic su **Elimina**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Eliminare regole](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Scollega account di automazione
Usare la procedura seguente per scollegare l'account di automazione dall'area di lavoro usando il portale di Azure:

1. Aprire il menu **account di automazione** e quindi selezionare l'account da rimuovere.
2. Nella sezione **risorse correlate** del menu selezionare **area di lavoro collegata**. 
3. Fare clic su **Scollega area di lavoro** per scollegare l'area di lavoro dall'account di automazione.

    ![Unlink workspace (Scollega area di lavoro)](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Spostare l'area di lavoro

### <a name="azure-portal"></a>portale di Azure
Usare la procedura seguente per spostare l'area di lavoro usando il portale di Azure:

1. Aprire il menu **aree di lavoro log Analytics** , quindi selezionare l'area di lavoro.
2. Nella pagina **Panoramica** fare clic su **Cambia** accanto a **gruppo di risorse** o **sottoscrizione**.
3. Verrà visualizzata una nuova pagina con un elenco di risorse correlate all'area di lavoro. Selezionare le risorse da spostare nella stessa sottoscrizione di destinazione e nel gruppo di risorse dell'area di lavoro. 
4. Selezionare una **sottoscrizione** di destinazione e un **gruppo di risorse**. Se si sta migrando l'area di lavoro a un altro gruppo di risorse nella stessa sottoscrizione, non verrà visualizzata l'opzione di **sottoscrizione** .
5. Fare clic su **OK** per spostare l'area di lavoro e le risorse selezionate.

    ![di Microsoft Azure](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Per spostare l'area di lavoro usando PowerShell, usare il comando [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) come nell'esempio seguente:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Al termine dell'operazione di spostamento, è necessario riconfigurare le soluzioni rimosse e il collegamento dell'account di automazione per ripristinare lo stato precedente dell'area di lavoro.


## <a name="next-steps"></a>Passaggi successivi
- Per un elenco delle risorse che supportano lo spostamento, vedere [spostare il supporto delle operazioni per le risorse](../../azure-resource-manager/move-support-resources.md).
