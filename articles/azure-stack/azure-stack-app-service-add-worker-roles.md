---
title: "Scalabilità dei ruoli di lavoro in servizi di App - Stack di Azure | Documenti Microsoft"
description: "Linee guida dettagliate per la scalabilità in servizi di App di Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 680cb70777574d0ed88c5f83fb0a6fa20263b951
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Servizio App nello Stack di Azure: aggiungere più ruoli di infrastruttura o di lavoro

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*  

Questo documento vengono fornite istruzioni su come ridimensionare il servizio App nei ruoli di lavoro e infrastruttura di Azure Stack. Contiene i passaggi per la creazione di ruoli di lavoro aggiuntivi per supportare le applicazioni di qualsiasi dimensione.

> [!NOTE]
> Se l'ambiente dello Stack di Azure non dispone di più di 96 GB di RAM, è possibile aggiunta di ulteriore capacità difficoltà.

Servizio App nello Stack di Azure, per impostazione predefinita, supporta i piani di lavoro gratuito e condiviso. Per aggiungere altri piani di lavoro, è necessario aggiungere più ruoli di lavoro.

Se non si conoscono ciò che è stato distribuito con il valore predefinito di servizio App in Installazione dello Stack di Azure, è possibile esaminare informazioni aggiuntive nel [servizio App nella panoramica di Azure Stack](azure-stack-app-service-overview.md).

Servizio App di Azure nello Stack di Azure distribuisce tutti i ruoli utilizzando il set di scalabilità di macchine virtuali e di conseguenza sfrutta le funzionalità di scalabilità di questo carico di lavoro. Pertanto, tutti scalabilità dei piani di lavoro viene eseguita tramite l'amministratore di servizio App.

> [!IMPORTANT]
> Non è attualmente possibile ridimensionare il set di scalabilità di macchine virtuali nel portale, come indicato nelle note sulla versione di Azure Stack, pertanto utilizzare l'esempio di PowerShell per scalabilità orizzontale.
>
>

## <a name="add-additional-workers-with-powershell"></a>Aggiungere ulteriori processi di lavoro con PowerShell

1. [Configurare l'ambiente Azure Stack Admin in PowerShell](azure-stack-powershell-configure-admin.md)

2. Usare questo esempio per scalare orizzontalmente il set di scalabilità:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRMAccount -EnvironmentName AzureStackAdmin
                                                 
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
   > Questo passaggio può richiedere un numero di ore in base al tipo di ruolo e il numero di istanze.
   >
   >

3. Monitorare lo stato delle nuove istanze del ruolo nell'amministrazione del servizio App, per controllare lo stato di una singola istanza del ruolo, fare clic sul tipo di ruolo nell'elenco.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Aggiungere ulteriori processi di lavoro direttamente all'interno di amministratore. Provider di risorse del servizio App

1. Accedere al portale di amministrazione di Stack di Azure come amministratore del servizio.

2. Passare a **servizi App**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Fare clic su **Ruoli**. Qui è visualizzare la suddivisione di tutti i ruoli del servizio App distribuita.

4. Fare clic con il pulsante destro sulla riga del tipo di cui si desidera applicare la scalabilità e quindi fare clic su **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Fare clic su **Scaling**, selezionare il numero di istanze che si desidera scalare di e quindi fare clic su **salvare**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Servizio app di Azure stack verrà ora aggiungere le altre macchine virtuali, configurarli, installare tutti i software necessari e contrassegnarli come pronto quando questo processo è stato completato. Questo processo può richiedere circa 80 minuti.

7. È possibile monitorare lo stato di conformità dei nuovi ruoli visualizzando i lavoratori il **ruoli** blade.

## <a name="result"></a>Risultato

Dopo che sono completamente distribuito e pronto, i processi di lavoro diventi disponibile per gli utenti di distribuire il carico di lavoro su di essi. Di seguito viene riportato un esempio di più livelli di prezzo disponibili per impostazione predefinita. Se sono non presenti processi di lavoro disponibili per un livello di lavoro specifico, non è disponibile l'opzione per scegliere il piano tariffario corrispondente.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Per ampliare gestione, i ruoli Front-End o Publisher aggiungere che è necessario scalare orizzontalmente il tipo di ruolo corrispondente. 
>
>

Per ampliare gestione, Front-End o ruoli del server di pubblicazione, la stessa procedura selezionando il tipo di ruolo appropriate. Controller non vengono distribuiti come set di scalabilità e pertanto due devono essere distribuiti in fase di installazione per tutte le distribuzioni di produzione.

### <a name="next-steps"></a>Passaggi successivi

[Configurare le origini di distribuzione](azure-stack-app-service-configure-deployment-sources.md)
