<properties
   pageTitle="Eseguire la migrazione delle risorse e dell’account di Automazione | Microsoft Azure"
   description="L’articolo illustra come spostare un account di Automazione in Automazione di Azure e le risorse associate da una sottoscrizione all’altra."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="magoedte" />

# Eseguire la migrazione delle risorse e dell’account di Automazione

Per migrare facilmente gli account di Automazione e le risorse associate (ad esempio asset, runbook, moduli, ecc.) creati nel Portale di Azure da un gruppo di risorse a un altro o da una sottoscrizione a un’altra, è possibile utilizzare la funzione[Sposta risorse](../resource-group-move-resources.md) disponibile nel Portale di Azure. Tuttavia, prima di procedere, è necessario rivedere il [Controllo prima di spostare le risorse](../resource-group-move-resources.md#Checklist-before-moving-resources) e, in più, l'elenco seguente specifico per Automazione.

1.  Il gruppo di risorse/sottoscrizioni di destinazione deve essere nella stessa area di origine. Pertanto, gli account di Automazione non possono essere spostati tra le aree.
2.  Durante lo spostamento di risorse (ad es. runbook, processi, ecc.), sia il gruppo di origine che il gruppo di destinazione sono bloccati per la durata dell'operazione. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi fino al completamento dello spostamento.
3.  I runbook o le variabili che fanno riferimento a un ID sottoscrizione o risorsa dalla sottoscrizione esistente devono essere aggiornati al termine della migrazione.


>[AZURE.NOTE] Questa funzionalità non supporta lo spostamento delle risorse di automazione classica.

## Per spostare l'account di automazione tramite il portale

1. Dall'account di Automazione fare clic su **Sposta** nella parte superiore del pannello.<br> ![Opzione Sposta](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Nel pannello **Sposta risorse**, sono presenti le risorse relative all'account di Automazione e ai gruppi di risorse. Selezionare la **sottoscrizione** e il **gruppo di risorse** dagli elenchi a discesa, oppure selezionare l'opzione **Creare un nuovo gruppo di risorse** e immettere il nome del nuovo gruppo di risorse nell’apposito campo.
3. Esaminare e selezionare la casella di controllo per confermare di *comprendere che gli strumenti e gli script dovranno essere aggiornati per poter utilizzare nuovi ID risorsa dopo lo spostamento delle risorse*, quindi fare clic su **OK**.<br> ![Pannello Sposta risorse](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>

Il completamento di questa operazione può richiedere alcuni minuti. In **Notifiche** verrà visualizzato lo stato di ciascuna operazione eseguita: convalida, migrazione e completa.

## Per spostare l'account di Automazione tramite PowerShell

Per spostare le risorse di Automazione esistenti in un altro gruppo di risorse o in una sottoscrizione, utilizzare il cmdlet **Get-AzureRmResource** per ottenere l'account di Automazione specifico e il cmdlet **Move-AzureRmResource** per eseguire lo spostamento.

Nel primo esempio viene illustrato come spostare un account di Automazione in un nuovo gruppo di risorse.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ``` 

Dopo aver eseguito l'esempio di codice riportato sopra, viene chiesto di confermare l’operazione. Una volta fatto clic su **Sì** e dopo aver consentito allo script di continuare, l’utente non riceverà altre notifiche mentre è in corso la migrazione.

Per eseguire lo spostamento in una nuova sottoscrizione, includere un valore per il parametro *DestinationSubscriptionId*.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ``` 

Come per l’esempio precedente, viene chiesto di confermare l’operazione di spostamento.

## Passaggi successivi

- Per ulteriori informazioni sullo spostamento delle risorse in un nuovo gruppo di risorse o sottoscrizione, vedere [Spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../resource-group-move-resources.md)
- Per altre informazioni sul controllo degli accessi in base al ruolo in Automazione di Azure, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](../automation/automation-role-based-access-control.md).
- Per informazioni sui cmdlet di PowerShell per la gestione della sottoscrizione, vedere [Uso di Azure PowerShell con Resource Manager](../powershell-azure-resource-manager.md)
- Per informazioni sulle funzionalità del portale per la gestione della sottoscrizione, vedere [Gestire le risorse di Azure mediante il portale](../azure-portal/resource-group-portal.md).

<!---HONumber=AcomDC_0713_2016-->