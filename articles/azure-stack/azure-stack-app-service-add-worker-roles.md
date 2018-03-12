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
ms.openlocfilehash: d6471796863a80e69fdaf740b68fb27d59503453
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
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
    ## e.g. if you VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
  
    '''

> [!NOTE]
> This step can take a number of hours to complete depending on the type of role and the number of instances.
>
>

3. Monitor the status of the new role instances in the App Service Administration, to check the status of an individual role instance click the role type in the list.

## Add additional workers directly within the App Service Resource Provider Admin.

1. Log in to the Azure Stack administration portal as the service administrator.

2. Browse to **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Click **Roles**. Here you see the breakdown of all App Service roles deployed.

4. Right click on the row of the type you want to scale and then click **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Click **Scaling**, select the number of instances you want to scale to, and then click **Save**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service on Azure Stack will now add the additional VMs, configure them, install all the required software, and mark them as ready when this process is complete. This process can take approximately 80 minutes.

7. You can monitor the progress of the readiness of the new roles by viewing the workers in the **Roles** blade.

## Result

After they are fully deployed and ready, the workers become available for users to deploy their workload onto them. The following shows an example of the multiple pricing tiers available by default. If there are no available workers for a particular worker tier, the option to choose the corresponding pricing tier is unavailable.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> To scale out Management, Front End or Publisher roles add you must scale out the corresponding role type. 
>
>

To scale out Management, Front End, or Publisher roles, follow the same steps selecting the appropriate role type. Controllers are not deployed as Scale Sets and therefore two should be deployed at Installation time for all production deployments.

### Next steps

[Configure deployment sources](azure-stack-app-service-configure-deployment-sources.md)
