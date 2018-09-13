---
title: Scalabilità orizzontale dei ruoli di lavoro in servizi App - Azure Stack | Microsoft Docs
description: Indicazioni dettagliate per la scalabilità dei servizi App di Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: abfc75e40e146b1cf7cb237f18a1ff08626e5be1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35998491"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Servizio App in Azure Stack: aggiungere altri ruoli di lavoro o dell'infrastruttura

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*  

Questo documento vengono fornite istruzioni su come ridimensionare il servizio App in ruoli di lavoro e infrastruttura di Azure Stack. Contiene i passaggi per la creazione di ruoli di lavoro aggiuntiva per supportare le applicazioni di qualsiasi dimensione.

> [!NOTE]
> Se l'ambiente dello Stack di Azure non ha più di 96 GB di RAM, si potrebbero incontrare difficoltà aggiunta di ulteriore capacità.

Servizio App in Azure Stack, per impostazione predefinita, supporta i livelli gratuito e condiviso ruolo di lavoro. Per aggiungere altri piani di lavoro, è necessario aggiungere altri ruoli di lavoro.

Se non si conosce ciò che è stato distribuito con il servizio App predefinito nell'installazione di Azure Stack, è possibile rivedere informazioni aggiuntive sotto la [servizio App in panoramica di Azure Stack](azure-stack-app-service-overview.md).

Servizio App di Azure in Azure Stack consente di distribuire tutti i ruoli usando il set di scalabilità di macchine virtuali e di conseguenza si avvale delle funzionalità di scalabilità di questo carico di lavoro. Pertanto, tutti la scalabilità dei livelli di lavoro viene eseguita tramite l'amministratore del servizio App.

> [!IMPORTANT]
> Attualmente non è possibile ridimensionare i set di scalabilità di macchine virtuali nel portale, come identificato le note sulla versione di Azure Stack, pertanto usare l'esempio di PowerShell per la scalabilità orizzontale.
>
>

## <a name="add-additional-workers-with-powershell"></a>Aggiungere ruoli di lavoro aggiuntivi con PowerShell

1. [Configurare l'ambiente di amministrazione di Azure Stack in PowerShell](azure-stack-powershell-configure-admin.md)

2. Usare questo esempio per scalare orizzontalmente il set di scalabilità:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Questo passaggio può richiedere un numero di ore a seconda del tipo di ruolo e il numero di istanze.
   >
   >

3. Monitorare lo stato delle nuove istanze del ruolo nell'amministrazione del servizio App, per controllare lo stato di una singola istanza del ruolo, fare clic sul tipo di ruolo nell'elenco.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Aggiungere altri ruoli di lavoro direttamente all'interno di App Service Resource Provider amministratore.

1. Accedere al portale di amministrazione di Azure Stack come amministratore del servizio.

2. Passare a **servizi App**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Fare clic su **Ruoli**. Qui è visualizzare la suddivisione di tutti i ruoli del servizio App distribuiti.

4. Fare clic con il pulsante destro sulla riga del tipo di cui si desidera scalare e quindi fare clic su **set di scalabilità**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Fare clic su **Scaling**, selezionare il numero di istanze da ridimensionare a e quindi fare clic su **salvare**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Servizio App in Azure Stack verrà a questo punto aggiungere le VM aggiuntive configurarli, installare tutto il software necessario e contrassegnarli come pronto quando questo processo è stato completato. Questo processo può richiedere circa 80 minuti.

7. È possibile monitorare lo stato di avanzamento della conformità dei nuovi ruoli visualizzando i ruoli di lavoro nel **ruoli** pannello.

## <a name="result"></a>Risultato

Dopo che sono completamente distribuito ed è pronto, i ruoli di lavoro diventano disponibili per gli utenti distribuire il carico di lavoro su di essi. Di seguito viene riportato un esempio di vari piani tariffari disponibili per impostazione predefinita. Se non sono presenti alcun thread di lavoro disponibili per un livello di computer di lavoro specifico, non è disponibile l'opzione per scegliere il piano tariffario corrispondente.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Per scalare orizzontalmente Management, i ruoli Front-End o Publisher aggiungere che è necessario scalare orizzontalmente il tipo di ruolo corrispondente. 
>
>

Per scalare orizzontalmente gestione, Front-End o ruoli del server di pubblicazione, seguire gli stessi passaggi selezionando il tipo di ruolo appropriate. I controller non vengono distribuiti come set di scalabilità e pertanto due devono essere distribuiti al momento dell'installazione per tutte le distribuzioni di produzione.

### <a name="next-steps"></a>Passaggi successivi

[Configurare le origini di distribuzione](azure-stack-app-service-configure-deployment-sources.md)
