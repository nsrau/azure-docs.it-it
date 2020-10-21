---
title: Spostare un'area di lavoro Log Analytics in monitoraggio di Azure | Microsoft Docs
description: Informazioni su come spostare l'area di lavoro di Log Analytics in un'altra sottoscrizione o in un altro gruppo di risorse.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: d59fb0dc39103119edbc4096b506c588c38cece4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282861"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Spostare un'area di lavoro Log Analytics in una sottoscrizione o in un gruppo di risorse diverso

Questo articolo illustra i passaggi per spostare Log Analytics area di lavoro in un altro gruppo di risorse o sottoscrizione nella stessa area. Per altre informazioni sullo scorrimento delle risorse di Azure, è possibile usare la portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. in [spostare le risorse in un nuovo gruppo di risorse o una nuova sottoscrizione](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Non è possibile spostare un'area di lavoro in un'area diversa.

## <a name="verify-active-directory-tenant"></a>Verificare Active Directory tenant
Le sottoscrizioni di origine e di destinazione dell'area di lavoro devono esistere nello stesso tenant Azure Active Directory. Usare Azure PowerShell per verificare che entrambe le sottoscrizioni abbiano lo stesso ID tenant.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Considerazioni sullo spostamento dell'area di lavoro
Le soluzioni gestite installate nell'area di lavoro verranno spostate con l'operazione di spostamento dell'area di lavoro Log Analytics. Gli agenti connessi rimarranno connessi e continueranno a inviare i dati all'area di lavoro dopo lo spostamento. Poiché l'operazione di spostamento richiede che non siano presenti servizi collegati dall'area di lavoro, è necessario rimuovere le soluzioni che si basano su tale collegamento per consentire lo spostamento dell'area di lavoro.

Soluzioni che devono essere rimosse prima di poter scollegare l'account di automazione:

- Gestione degli aggiornamenti
- Rilevamento modifiche
- Avviare/arrestare VM durante gli orari di minore attività
- Centro sicurezza di Azure

>[!IMPORTANT]
> **Clienti di Azure Sentinel**
> - Una volta distribuito in un'area di lavoro, Azure Sentinel **non supporta al momento** il trasferimento di tale area di lavoro in altri gruppi di risorse o sottoscrizioni. 
> - Se l'area di lavoro è già stata spostata, disabilitare tutte le regole attive in **Analytics** e abilitarle di nuovo dopo cinque minuti. Questa operazione dovrebbe essere efficace nella maggior parte dei casi, tuttavia, come già detto, non è supportata e viene eseguita a proprio rischio.
> 
> **Avvisi**
> - Tutti gli avvisi devono essere ricreati dopo lo spostamento, perché le autorizzazioni sono basate sull'ID risorsa di Azure dell'area di lavoro e vengono modificate con lo spostamento dell'area di lavoro. 

### <a name="delete-solutions-in-azure-portal"></a>Eliminare soluzioni in portale di Azure
Usare la procedura seguente per rimuovere le soluzioni usando il portale di Azure:

1. Aprire il menu per il gruppo di risorse in cui sono installate le soluzioni.
2. Selezionare le soluzioni da rimuovere.
3. Fare clic su **Elimina risorse** e confermare le risorse da rimuovere facendo clic su **Elimina**.

![Eliminare soluzioni](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Eliminare con PowerShell

Per rimuovere le soluzioni usando PowerShell, usare il cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) , come illustrato nell'esempio seguente:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>Rimuovere le regole di avviso per la soluzione di avvio/arresto di macchine virtuali
Per rimuovere la soluzione di **avvio/arresto di macchine virtuali** , è necessario rimuovere anche le regole di avviso create dalla soluzione. Utilizzare la procedura seguente nel portale di Azure per rimuovere queste regole.

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

### <a name="azure-portal"></a>Portale di Azure
Usare la procedura seguente per spostare l'area di lavoro usando il portale di Azure:

1. Aprire il menu **aree di lavoro log Analytics** , quindi selezionare l'area di lavoro.
2. Nella pagina **Panoramica** fare clic su **Cambia** accanto a **gruppo di risorse** o **sottoscrizione**.
3. Verrà visualizzata una nuova pagina con un elenco di risorse correlate all'area di lavoro. Selezionare le risorse da spostare nella stessa sottoscrizione di destinazione e nel gruppo di risorse dell'area di lavoro. 
4. Selezionare una **sottoscrizione** di destinazione e un **gruppo di risorse**. Se si sta migrando l'area di lavoro a un altro gruppo di risorse nella stessa sottoscrizione, non verrà visualizzata l'opzione di **sottoscrizione** .
5. Fare clic su **OK** per spostare l'area di lavoro e le risorse selezionate.

    ![Screenshot mostra il riquadro Panoramica nell'area di lavoro Log Analytics con le opzioni per modificare il gruppo di risorse e il nome della sottoscrizione.](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Per spostare l'area di lavoro usando PowerShell, usare il comando [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) come nell'esempio seguente:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> Al termine dell'operazione di spostamento, è necessario riconfigurare le soluzioni rimosse e il collegamento dell'account di automazione per ripristinare lo stato precedente dell'area di lavoro.


## <a name="next-steps"></a>Passaggi successivi
- Per un elenco delle risorse che supportano lo spostamento, vedere [spostare il supporto delle operazioni per le risorse](../../azure-resource-manager/management/move-support-resources.md).
