---
title: 'Servizio App in Azure Stack: aggiornamento del dominio di errore | Microsoft Docs'
description: Come ridistribuire il servizio App di Azure in Azure Stack nei domini di errore
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: 53766099f283f802482fe8e84144502d386b1d69
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440152"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Come ridistribuire il servizio App di Azure in Azure Stack nei domini di errore

*Si applica a: i sistemi integrati di Azure Stack*

Con l'aggiornamento 1802, Azure Stack supporta ora la distribuzione dei carichi di lavoro nei domini di errore, una funzionalità che è fondamentale per la disponibilità elevata.

>[!IMPORTANT]
>Per sfruttare i vantaggi del supporto di domini di errore, è necessario aggiornare il sistema integrato Azure Stack alla versione 1802. Questo documento si applica solo alle distribuzioni di provider risorse servizio App sono state completate prima dell'aggiornamento 1802. Se è stato distribuito il servizio App in Azure Stack dopo che è stato applicato l'aggiornamento 1802 per Azure Stack, il provider di risorse è già distribuito nei domini di errore.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Ribilanciare un provider di risorse del servizio App in domini di errore

Per ridistribuire i set di scalabilità distribuiti per il provider di risorse del servizio App, è necessario eseguire i passaggi descritti in questo articolo per ogni set di scalabilità. Per impostazione predefinita, i nomi di set di scalabilità sono:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Se non si dispone di istanze distribuite in alcuni set di scalabilità di livello di ruolo di lavoro, non è necessario ribilanciare i set di scalabilità. I set di scalabilità verranno bilanciati correttamente quando si ridimensionali in futuro.

Per scalare orizzontalmente i set di scalabilità, seguire questa procedura:

1. Accedere al portale di Azure Stack di amministratore.
1. Selezionare **Altri servizi**.
1. Nel gruppo di risorse di calcolo, selezionare **set di scalabilità di macchine virtuali**. Verranno elencati set di scalabilità esistente, distribuito come parte della distribuzione del servizio App con le informazioni sul conteggio di istanza. La schermata seguente mostra un esempio di set di scalabilità.

      ![Set di scalabilità di servizio di Azure App elencati in Virtual Machine Scale set UX][1]

1. La scalabilità orizzontale ogni set. Ad esempio, se sono presenti tre istanze esistenti nel set di scalabilità è necessario scalare orizzontalmente a 6 in modo che le tre nuove istanze vengono distribuite tra domini di errore. Nell'esempio di PowerShell seguente mostra per scalare orizzontalmente il set di scalabilità.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Questo passaggio può richiedere diverse ore, a seconda del numero di istanze e il tipo di ruolo.

1. Nelle **ruoli di amministrazione del servizio App**, monitorare lo stato delle nuove istanze del ruolo. Per controllare lo stato di un'istanza del ruolo, selezionare il tipo di ruolo nell'elenco

    ![Servizio App di Azure su ruoli di Azure Stack][2]

1. Quando lo stato delle nuove istanze del ruolo è **pronti**, tornare alla **Set di scalabilità di macchine virtuali** e **Elimina** le istanze del ruolo precedente.

1. Ripetere questi passaggi per **ogni** set di scalabilità di macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile provare altri [piattaforma distribuita come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md).

* [Provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
