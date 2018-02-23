---
title: Eseguire la migrazione delle risorse e dell'account di Automazione | Microsoft Docs
description: "L’articolo illustra come spostare un account di Automazione in Automazione di Azure e le risorse associate da una sottoscrizione all’altra."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 9c2db4a2-f324-48dc-8ce7-3343bf7230d5
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 40567d1b1f450c499715682cf8c22f0c79c7d0c7
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="migrate-automation-account-and-resources"></a>Eseguire la migrazione delle risorse e dell’account di Automazione
Per gli account di Automazione e le risorse associate (ad esempio asset, runbook, moduli, e così via) creati nel portale di Azure per i quali si vuole eseguire la migrazione da un gruppo di risorse a un altro o da una sottoscrizione a un'altra, è possibile farlo facilmente grazie alla funzionalità per [spostare le risorse](../azure-resource-manager/resource-group-move-resources.md) disponibile nel portale di Azure. Prima di procedere tuttavia è necessario rivedere l'[elenco si controllo prima di spostare le risorse](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources) e, in più, l'elenco seguente specifico per Automazione.  

1. Il gruppo di risorse/sottoscrizioni di destinazione deve essere nella stessa area di origine. Pertanto, gli account di Automazione non possono essere spostati tra le aree.
2. Durante lo spostamento di risorse, ad esempio runbook, processi e così via, sia il gruppo di origine che il gruppo di destinazione sono bloccati per la durata dell'operazione. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi fino al completamento dello spostamento. 
3. I runbook o le variabili che fanno riferimento a una risorsa o a un ID sottoscrizione dalla sottoscrizione esistente devono essere aggiornati al termine della migrazione.  

> [!NOTE]
> Questa funzionalità non supporta lo spostamento delle risorse di automazione classica.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>Per spostare l'account di automazione tramite il portale
1. Dall'account di Automazione fare clic su **Sposta** nella parte superiore della pagina.<br> ![Opzione Sposta](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Spostare l'account di automazione in un altro gruppo di risorse o un'altra sottoscrizione.
3. Nel riquadro **Sposta risorse** sono presenti le risorse relative all'account di Automazione e ai gruppi di risorse. Selezionare la **sottoscrizione** e il **gruppo di risorse** dagli elenchi a discesa, oppure selezionare l'opzione **creare un nuovo gruppo di risorse** e immettere il nome di un nuovo gruppo di risorse nel campo corrispondente. 
4. Esaminare e selezionare la casella di controllo per confermare di *comprendere che gli strumenti e gli script dovranno essere aggiornati per poter utilizzare nuovi ID risorsa dopo lo spostamento delle risorse*, quindi fare clic su **OK**.<br> ![Riquadro Sposta risorse](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Il completamento di questa operazione può richiedere alcuni minuti. In **Notifiche** viene visualizzato lo stato di ciascuna operazione eseguita: convalida, migrazione e infine completamento.    

## <a name="to-move-the-automation-account-using-powershell"></a>Per spostare l'account di Automazione tramite PowerShell
Per spostare le risorse di Automazione esistenti in un altro gruppo di risorse o sottoscrizione, usare prima il cmdlet **Get-AzureRmResource** per ottenere l'account di Automazione specifico, poi usare il cmdlet **Move-AzureRmResource** per eseguire lo spostamento.

Nel primo esempio viene illustrato come spostare un account di Automazione in un nuovo gruppo di risorse.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

Dopo aver eseguito l'esempio di codice riportato sopra, viene chiesto di confermare l'operazione. Dopo aver fatto clic su **Sì** e dopo aver consentito allo script di continuare, l'utente non riceverà altre notifiche mentre è in corso la migrazione. 

Per eseguire lo spostamento in una nuova sottoscrizione, includere un valore per il parametro *DestinationSubscriptionId* .

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Come per l'esempio precedente, viene chiesto di confermare l'operazione di spostamento. 

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sullo spostamento delle risorse in un nuovo gruppo di risorse o sottoscrizione, vedere [Spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../azure-resource-manager/resource-group-move-resources.md)
* Per altre informazioni sul controllo degli accessi in base al ruolo in Automazione di Azure, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
* Per informazioni sui cmdlet di PowerShell per la gestione della sottoscrizione, vedere [Uso di Azure PowerShell con Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* Per informazioni sulle funzionalità del portale per la gestione della sottoscrizione, vedere [Gestire le risorse di Azure mediante il portale](../azure-resource-manager/resource-group-portal.md).
